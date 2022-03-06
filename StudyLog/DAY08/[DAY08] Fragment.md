# Fragment

ktx기준으로 아래와 같이 프래그먼트를 작성할 수 있습니다.

```
class HomeFragment : Fragment(R.layout.home_fragment)
```

그리고 이전과 달리 onCreateView를 오버라이딩 하지 않습니다.

이전코드

```
class HomeFragment : Fragment() {
  override fun onCreateView(inflater : LayoutInflater, container : ViewGroup?, savedInstanceState: Bundle?): View{
      return inflater.inflate(R.layout.home_fragment, container, false)
  }
}
```

최근스타일은 아래와 같이 작성할 수 있습니다.

```
class HomeFragment : Fragment(R.layout.home_fragment){
  private lateinit var binding : HomeFragmentBinding // View Binding
  
  override fun onViewCreated(view : View, savedInstanceState: Bundle?){
    binding = HomeFragmentBinding.bind(view)
  }
}
```

액티비티에 Fragment를 붙이려면 xml에 FragmentContainerView를 선언하고, 

android:name에 전체 패키지를 포함한 프래그먼트를 입력하면 선언할 수 있습니다.

ex) android : name = "com.example.fragmentpractice.ui.home.HomeFragment"

코드를 통해 추가하는 방법도 가능합니다

fragment-ktx를 기준으로 아래와 같이 onCreate내에 작성하면 됩니다.
```
supportFragmentManager.commit{
  add<HomeFragment>(R.id.fragment_container_view)
}
```

프래그먼트를 액티비티에 추가하면서 데이터를 넘기려면 bundle형태로 인자를 넘길 수 있습니다.
```
add<HomeFragment>(R.id.fragment_container_view, bundle)
```

만약 프래그먼트에서 이 값을 받으려면 아래와 같이 사용할 수 있습니다.
```
override fun onViewCreated(view : View, savedInstanceState: Bundle?){
    val somInt = arguments.getInt("some_int") // getInt(Key)
  }
```

## FragmentManager

- 프래그먼트 추가, 삭제, 스택관리 등 프래그먼트에 관리에 대한 모든것을 담당합니다.

- getFragmentManager(X) getSupportFragmentManager(O)

- 프래그먼트 내부에서 Fragmentmanager를 가져올 때, getParentFragmentManager와 getChildFragmentManager로나뉩니다.

- 프래그먼트의 인스턴스를 가져올 때, supportFragmentManager.findFragmentById(R.id.fragment_container_view)와 같이 사용할 수 있습니다.

- 또는 xml 내에 tag를선언하고 findFragmentByTag도 사용 가능합니다.


## FragmenTransaction

- 데이터베이스의 트랜잭션과 비슷한 의미입니다. 작업 시 beginTransaction ~ commit 을 호출해야 합니다.

- setReorderingAllowed(true)을 통해 애니메이션과 트랜잭션이 올바르게 작동하도록 트랜잭션과 관련된 프래그먼트의 상태 변경을 최적화합니다. 항상 사용을 권장하고 있습니다.

- add / remove / replace(기존에 있는 뷰 제거후 add) / attach() / detach(remove와는 달리 뷰계층에서 분리하는 의미) / show / hide

- 프래그먼트 트랜잭션은 commit()을 수행해야 비로소 동작하게 됩니다. 

- commit()은 비동기적으로 처리됩니다. 그 이유는 inflate는 비용이 많이 들기 때문에, 비용에 따라 적절한 시점에 동작하도록 설계되어 있습니다.

- commitNow()를 통해 동기적으로 처리할 수 있습니다.

- executePendingTransactions()는 보류중인 트랜잭션을 처리하라고 명령하라는 의미이고, 성공/실패에 따라 boolean을 리턴합니다.

- 프래그먼트 내에 onSaveInstanceState()호출 후, commit을 호출하면 런타임 익셉션이 발생하도록 되어있습니다.

- 이를 막기 위해 commit(allowStateLoss = true)를 넣어주어야 합니다. 실무에서도 주로 이렇게 사용합니다.

## Back Stack

- addToBackStack()을 통해 Fragment transaction단위로 스택에 push할 수있습니다.

- Fragment transaction의 히스토리가 쌓인다고 볼 수 있습니다.

- addToBackStack(entryname) 을 통해 넣을 수 있으며 entry는 백스택 앤트리를 탐색할 때 사용합니다.

- popBackstack()을 사용하여 Fragment transaction단위로 스택에서 pop할 수 있습니다.

- 기본적으로 비동기처리로 되어있습니다. 동기처리를 위해서는 popBackStackImmediate()를 사용할 수 있습니다.

- 안드로이드 onBackPressed에 popBackStackImmediate()가 존재하므로, single backstack은 별도의  처리가 필요 없습니다.

- androidx에서는 back key에 대한 처리가 onBackPressedDispatcher로 위임되었습니다.

## MultipleBackStack

- saveBackStack() 과 restoreBackStack()을 이용하면 직접 구현하지 않고도 쉽게 사용할 수 있습니다.  

## 동일한 FragmentManager를 사용하는 프래그먼트간 데이터전달

- setFragmentResult("requestKey, bundleOf("bundleKey" to result))의 형태로 데이터를 저장할 수 있습니다.

- 데이터를 전달받을 때는 아래와 같이 사용할 수 있습니다.
 
 ```
setFragmentResultListener("requestKey") { requestKey, bundle ->
    val result = bundle.getString("bundleKey")
}
```

## Parent, Child Fragment간 데이터 전달

- 동일하지만, 리스너는 childFrgmentManger.setFragmentResultListener를 사용해야 합니다.

# ViewModel

- 가장 좋은 방법은 뷰모델을 사용하여 데이터를 공유하는 방법입니다. 안드로이드에서도 권장하는 방법입니다.

- 아래는 예시 코드입니다.


```
class ListFragment : Fragment() {
    private val viewModel : ListViewModel by activityViewModels()
      ...
      override fun onViewCreated(view : View, savedInstanceState: Bundle?){
        viewModel.filteredList.observe(viewLifecycleOwner, Observer{
          list -> //Update list UI
        }
      }
}

// Child Fragment에서 사용하는 경우

class ChildFragment : Fragment() {

  private val viewModel : ListViewModel by viewModels({requireParentFragment()})
}
```                        
