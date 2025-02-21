你好，我是徐昊。今天我们继续使用TDD的方式实现注入依赖容器。

## 回顾代码与任务列表

到目前为止，我们的代码是这样的：

```
ContextConfig.java:
 
package geektime.tdd.di;
    
import java.util.*;
import static java.util.List.of;
    
public class ContextConfig {
    private Map<Class<?>, ComponentProvider<?>> providers = new HashMap<>();
    
    public <Type> void bind(Class<Type> type, Type instance) {
        providers.put(type, new ComponentProvider<Type>() {
            @Override
            public Type get(Context context) {
                return instance;
            }
            @Override
            public List<Class<?>> getDependencies() {
                return of();
            }
        });
    }
    
    public <Type, Implementation extends Type>
    void bind(Class<Type> type, Class<Implementation> implementation) {
        providers.put(type, new ConstructorInjectionProvider<>(implementation));
    }
    
    public Context getContext() {
        providers.keySet().forEach(component -> checkDependencies(component, new Stack<>()));
        return new Context() {
            @Override
            public <Type> Optional<Type> get(Class<Type> type) {
                return Optional.ofNullable(providers.get(type)).map(provider -> (Type) provider.get(this));
            }
        };
    }
    
    private void checkDependencies(Class<?> component, Stack<Class<?>> visiting) {
        for (Class<?> dependency: providers.get(component).getDependencies()) {
            if (!providers.containsKey(dependency)) throw new DependencyNotFoundException(component, dependency);
            if (visiting.contains(dependency)) throw new CyclicDependenciesFoundException(visiting);
            visiting.push(dependency);
            checkDependencies(dependency, visiting);
            visiting.pop();
        }
    }
    
    interface ComponentProvider<T> {
        T get(Context context);
        List<Class<?>> getDependencies();
    }
}
    
ConstructorInjectionProvider.java:
    
package geektime.tdd.di;
    
import jakarta.inject.Inject;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Parameter;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;
import static java.util.Arrays.stream;
    
class ConstructorInjectionProvider<T> implements ContextConfig.ComponentProvider<T> {
    private Constructor<T> injectConstructor;
    private List<Field> injectFields;
    
    public ConstructorInjectionProvider(Class<T> component) {
        this.injectConstructor = getInjectConstructor(component);
        this.injectFields = getInjectFields(component);
    }
    
    @Override
    public T get(Context context) {
        try {
            Object[] dependencies = stream(injectConstructor.getParameters())
                    .map(p -> context.get(p.getType()).get())
                    .toArray(Object[]::new);
            T instance = injectConstructor.newInstance(dependencies);
            for (Field field : injectFields)
                field.set(instance, context.get(field.getType()).get());
            return instance;
        } catch (InvocationTargetException | InstantiationException | IllegalAccessException e) {
            throw new RuntimeException(e);
        }
    }
    
    @Override
    public List<Class<?>> getDependencies() {
        return Stream.concat(stream(injectConstructor.getParameters()).map(Parameter::getType),
                injectFields.stream().map(Field::getType)).toList();
    }
    
    private static <T> List<Field> getInjectFields(Class<T> component) {
        List<Field> injectFields = new ArrayList<>();
        Class<?> current = component;
        while (current != Object.class) {
            injectFields.addAll(stream(current.getDeclaredFields()).filter(f -> f.isAnnotationPresent(Inject.class))
                    .toList());
            current = current.getSuperclass();
        }
        return injectFields;
    }
    
    private static <Type> Constructor<Type> getInjectConstructor(Class<Type> implementation) {
        List<Constructor<?>> injectConstructors = stream(implementation.getConstructors())
                .filter(c -> c.isAnnotationPresent(Inject.class)).collect(Collectors.toList());
        if (injectConstructors.size() > 1) throw new IllegalComponentException();
        return (Constructor<Type>) injectConstructors.stream().findFirst().orElseGet(() -> {
            try {
                return implementation.getDeclaredConstructor();
            } catch (NoSuchMethodException e) {
                throw new IllegalComponentException();
            }
        });
    }
}
    
Context.java:
package geektime.tdd.di;

import java.util.Optional;

public interface Context {
    <Type> Optional<Type> get(Class<Type> type);
}
```

任务列表状态为：

- 无需构造的组件——组件实例
- 如果注册的组件不可实例化，则抛出异常
  
  - 抽象类
  - 接口
- 构造函数注入
  
  - 无依赖的组件应该通过默认构造函数生成组件实例
  - 有依赖的组件，通过Inject标注的构造函数生成组件实例
  - 如果所依赖的组件也存在依赖，那么需要对所依赖的组件也完成依赖注入
  - 如果组件有多于一个Inject标注的构造函数，则抛出异常
  - 如果组件没有Inject标注的构造函数，也没有默认构造函数（新增任务）
  - 如果组件需要的依赖不存在，则抛出异常
  - 如果组件间存在循环依赖，则抛出异常
- 字段注入
  
  - 通过Inject标注将字段声明为依赖组件
  - 如果字段为final，则抛出异常
  - 依赖中应包含Inject Field声明的依赖
- 方法注入
  
  - 通过Inject标注的方法，其参数为依赖组件
  - 通过Inject标注的无参数方法，会被调用
  - 按照子类中的规则，覆盖父类中的Inject方法
  - 如果方法定义类型参数，则抛出异常
  - 依赖中应包含Inject Method声明的依赖
- 对Provider类型的依赖
  
  - 注入构造函数中可以声明对于Provider的依赖
  - 注入字段中可以声明对于Provider的依赖
  - 注入方法中可以声明对于Provider的依赖
- 自定义Qualifier的依赖
  
  - 注册组件时，可额外指定Qualifier
  - 注册组件时，可从类对象上提取Qualifier
  - 寻找依赖时，需同时满足类型与自定义Qualifier标注
  - 支持默认Qualifier——Named
- Singleton生命周期
  
  - 注册组件时，可额外指定是否为Singleton
  - 注册组件时，可从类对象上提取Singleton标注
  - 对于包含Singleton标注的组件，在容器范围内提供唯一实例
  - 容器组件默认不是Single生命周期
- 自定义Scope标注
  
  - 可向容器注册自定义Scope标注的回调
<div><strong>精选留言（6）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/10/e9/22/7606c6ba.jpg" width="30px"><span>张铁林</span> 👍（0） 💬（1）<div>编辑，字段注入那里，被删除掉了一项，之前还有4项的。</div>2022-04-27</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/1d/de/62bfa83f.jpg" width="30px"><span>aoe</span> 👍（2） 💬（0）<div>收获

1. 掌握了从子类递归寻找到父类的方法
while (current != Object.class) {
  current = current.getSuperclass();
}

2. 利用 Collections.reverse() 方法可以轻松反转集合，不用之前的逻辑反过来实现一遍
- 遇事不要冲动，直接想到的不一定是最好的
- 多思考，尽量使用工具方法简化问题

3. 渐渐的比之前更理解代码了</div>2022-04-29</li><br/><li><img src="http://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTJUCiacuh59wMbq1icuB8U1T7Vpic8FjKFdanvdt9bzClBmYqFUXmtKmh2Zibn9Dic6A8pjdoBiaia1LCrnA/132" width="30px"><span>tdd学徒</span> 👍（1） 💬（0）<div>这段父类先调，子类后调的构造真巧妙  
   static class SuperClassWithInjectMethod {
            int superCalled = 0;

            @Inject
            void install() {
                superCalled++;
            }
        }

        static class SubClassWithInjectMethod extends SuperClassWithInjectMethod {
            int subCalled = 0;

            @Inject
            void installAnother() {
                subCalled = superCalled + 1;
            }
        }</div>2022-05-02</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/22/db/ae/0a11bd56.jpg" width="30px"><span>烧灯续昼</span> 👍（0） 💬（0）<div>新增的两个filter，判断method的方法名和参数类型是否完全相同的地方，未做到TDD。
遗漏了一种测试验证：方法名相同，参数列表不同。

由子类方法标记@Inject 会覆盖且不执行父类@Inject方法的测试驱动了代码：.filter(m -&gt; injectMethods.stream().noneMatch(o -&gt; o.getName().equals(m.getName()) &amp;&amp; Arrays.equals(m.getParameterTypes(), o.getParameterTypes()))) 没有什么问题，
but 这段代码的作用是判断是否是重写方法。这里应该立即补充上判断是否是重写方法的测试</div>2023-02-25</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/12/34/cf/0a316b48.jpg" width="30px"><span>蝴蝶</span> 👍（0） 💬（0）<div>2. 最有感触的几点有：1.需求能变成测试用例，再根据测试用例需要的效果调整代码，减少了关注点。2.关于 Method Constructor Field Stream 和常见 Api 的用法也挺重要的。</div>2022-08-28</li><br/><li><img src="https://static001.geekbang.org/account/avatar/00/11/de/cf/ef2e0501.jpg" width="30px"><span>奇小易</span> 👍（0） 💬（0）<div>1、下一步行动，核心流程
先快速解决遗留下的sad path。
重构生产代码之前，先重构测试代码。

2、测试代码的重构思路，
当前测试代码中有部分的测试是在专门测ConstructionInjectionProvider的功能。
故可以将相关的测试提取到该测试类中。
</div>2022-05-28</li><br/>
</ul>