# SubComponent 예제

서브컴포넌트의 이해를 위해 서브 컴포넌트를 활용한 카페 예제를 만들어봅니다. 

카페에는 에스프레소 머신이 있고 이 머신에 커피콩과 물을 더해 커피를 만들 수 있습니다.

카페쪽이 컴포넌트, 에스프레소 머신 쪽이 서브 컴포넌트가 될 것입니다. 먼저 서브 컴포넌트를 정의합니다.

```
@Subcomponent(modules = [MachineModule::class])
interface MachineComponent {
    fun getCoffee() : Coffee
    fun inject(machine : Machine)
    @Subcomponent.Builder
    interface Builder{
        fun setMachineModule(coffeMachineModule : MachineModule) : Builder
        fun build() : MachineComponent
    }
}
```

```
class Machine(builder : MachineComponent.Builder) {
    private var component : MachineComponent
    init{
        component = builder.setMachineModule(MachineModule())
            .build()

        component.inject(this)
    }
    fun extract() : Coffee = component.getCoffee()
}
```

```
@Module
class MachineModule {
    @Provides
    fun provideCoffeeBean() : CoffeeBean = CoffeeBean()
}

class CoffeeBean{}

class Water{}
```

```
class Coffee @Inject constructor(var coffeeBean : CoffeeBean, var water : Water){
}
```

서브컴포넌트를 정의했다면 컴포넌트에 서브컴포넌트로 추가해야 합니다.

컴포넌트를 추가하려면 컴포넌트가 가진 @Module의 멤버인 subcomponents에 서브 컴포넌트 클래스를 추가합니다.

서브컴포넌트가 연결되었다면, 연결된 컴포넌트 모듈로부터 서브 컴포넌트의 빌더를 요청할 수 있습니다.

```
@Component(modules = [CafeModule::class])
interface CafeComponent {
    fun inject(cafe : Cafe)
}
```

```
class Cafe {
    @Inject
    lateinit var coffeeMachine : Machine
    init{
        DaggerCafeComponent.create().inject(this)
    }
    fun orderCoffee() = coffeeMachine.extract()
}
```

```
@Module(subcomponents = [MachineComponent::class])
class CafeModule {

    @Provides
    fun provideWater() : Water = Water()

    @Provides
    fun provideMachine(builder : MachineComponent.Builder) = Machine(builder)
}
```

컴포넌트에 연결된 CafeModule로부터 Machine을 제공받을 수 있게 되었고 Machine의 생성자 매개변수로 서브 컴포넌트의 빌더를 제공받아 객체를 생성할 수 있는 예제입니다.

MachineCoponent는 CafeComponent의 일부분으로 CafeComponent가 가진 CafeModule로부터 모듈이 가진 의존성을 제공받을 수 있지만,

CafeComponent는 MachineComponent.Builder를 제외하고는 서브컴포넌트가 가진 모듈의 의존성을 제공받을 수 없습니다.

## 컴포넌트의 상속

서브컴포넌트의 대안으로 컴포넌트를 상속하는 방법이 있습니다. 컴포넌트를 상속하는 방법은 @Component 어노테이션 dependencies속성에 상속하려는 컴포넌트의 클래스를 추가하면 됩니다.

상속한 컴포넌트의 의존성 사용을 위해서는 컴포넌트가 해당 의존성을 프로비전메소드로 반드시 제공해야 합니다.
