```aidl
@Aspect
public class NotVeryUsefulAspect {
    @Pointcut("execution(public * *(..))")
    private void anyPublicOperation() {}
    
       @Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
        public void doAccessCheck() {
            // ...
        }
}
```

```aidl
<aop:config>
    <aop:aspect id="myAspect" ref="aBean">
        <aop:pointcut id="businessService"
                    expression="execution(* com.xyz.myapp.service.*.*(..))"/>
        <aop:before pointcut-ref="businessService" method="monitor"/>
    </aop:aspect>
    
</aop:config>

<bean id="aBean" class="...">
    ...
</bean>
```