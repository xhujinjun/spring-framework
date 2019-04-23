# bean 完整的生命周期


Bean的前世今生
出生: <bean>...</bean>
幼年: BeanDefinition bean的接口定义
小学: GenericBeanDefinition 基础的Bean映射
中学: RootBeanDefinition 拥有依赖关系等附加信息的bean
大学: BeanWrapper 内部用
社会: Object 你的bean

bean源 -> bean定义 -> bean对象
## bean源
xml
java代码(@Bean @Componert @)
# bean 定义过程

# bean创建过程
AbstractAutowireCapableBeanFactory.doCreateBean
1. 实例化bean(Instantiate the bean)
真实创建一个bean

实例化bean createBeanInstance
2.  MergedBeanDefinitionPostProcessors
3. 初始化beaninit bean instance()
    3.1 属性注入populateBean
    3.2 Aware注入
    3.3 #applyBeanPostProcessorsBeforeInitialization
    3.4 #invokeInitMethods
    3.5 #applyBeanPostProcessorsAfterInitialization)