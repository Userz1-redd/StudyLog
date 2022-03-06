
구성원 일정뷰는 좌우, 상하 모두 스크롤 되어야 합니다. 

저는 구성원 일정뷰를 Vertical한 LinearRecyclerView로 두고 

RecyclerView 전체를 HorizontalScrollview로 감싸서 상하 좌우 스크롤을 가능하게 한 뒤,

좌측 구성원 목록 헤더와 상단 시간헤더를 고정시키면서 스크롤 방향에 맞게 함께 움직이도록 구현하는 것을 고민해보았습니다.

우선 간단하게 예시코드를 작성하여 HorizontalScrollView + RecyclerView를 구성하였습니다.

MainActivity
```
class MainActivity : AppCompatActivity() {
   lateinit var mBinding: ActivityMainBinding
   lateinit var adapter: MainViewAdapter
   companion object {
       var TAG = "TAG"
   }
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       mBinding = ActivityMainBinding.inflate(layoutInflater)
       setContentView(mBinding.root)

       adapter = MainViewAdapter()
       initView()
 
   }
   private fun initView() {
       mBinding.mainRv.apply {
           itemAnimator = null
           layoutManager = LinearLayoutManager(context)
           var list = ArrayList<String>()
           for(i in 1..100){
               list.add(i.toString())
           }
           this@MainActivity.adapter.submitList(list)
           adapter = this@MainActivity.adapter
       }
   }

}
```

Adapter
```
class MainViewAdapter : RecyclerView.Adapter<MainViewHolder>() {
   private lateinit var list : ArrayList<String>
   override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MainViewHolder {
       Log.d("TAG", "onCreateViewHolder:called ")
       val binding = ItemContentBinding.inflate(LayoutInflater.from(parent.context), parent, false)
       return MainViewHolder(binding)
   }

   override fun onBindViewHolder(holder: MainViewHolder, position: Int) {
       Log.d("TAG", "onBindViewHolder:called ")
       holder.bindWithView(this.list[position])
   }

   override fun getItemCount(): Int {
       return list.size
   }
   fun submitList(list : ArrayList<String>){
       this.list = list
   }
}
```
테스트 삼아 작성한 코드이기 때문에 뷰홀더에 따로 데이터를 넣지는 않았습니다.
```
class MainViewHolder(private val binding : ItemContentBinding) : RecyclerView.ViewHolder(binding.root){

   fun bindWithView(string : String){

   }
}
```

레이아웃은 스크롤 테스트를 위해 아래와 같이 여러 뷰들을 추가해놓았습니다.
activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

   android:layout_width="match_parent"
   android:layout_height="match_parent"
   android:orientation="vertical"
   >
   <LinearLayout
       android:layout_width="match_parent"
       android:layout_height="50dp"
       android:layout_marginLeft="100dp"
       android:paddingTop="30dp"
       >
   <HorizontalScrollView
       android:id="@+id/time_header_sc"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:scrollbars="none"
       >
       <LinearLayout
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           android:orientation="horizontal"
           >

           <TextView
               style="@style/ListItemHeaderTextView"
               android:text=""
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="1시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="2시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="3시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="4시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="5시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="오전6시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="7시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="8시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="9시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="10시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="11시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="오후12시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="1시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="2시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="3시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="4시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="5시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="오후6시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="7시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="8시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="9시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="10시"
               />
           <TextView
               style="@style/ListItemHeaderTextView"
               android:text="11시"
               />
       </LinearLayout>
   </HorizontalScrollView>
   </LinearLayout>
   <HorizontalScrollView
       android:id="@+id/schedule_horizontal_view"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
   <androidx.recyclerview.widget.RecyclerView
       android:id="@+id/main_rv"
       android:layout_width="match_parent"
       android:layout_height="match_parent" />
</HorizontalScrollView>

</LinearLayout>
```

item_content.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
   xmlns:android="http://schemas.android.com/apk/res/android"

   style="@style/Item_Parent_Style">

   <LinearLayout
       android:layout_width="wrap_content"
       android:layout_height="match_parent"
       android:orientation="horizontal"
       android:descendantFocusability="blocksDescendants">

       <LinearLayout
           android:layout_width="100dp"
           android:layout_height="match_parent"
           android:background="@color/white"
           android:orientation="vertical">

           <LinearLayout
               android:layout_width="match_parent"
               android:layout_height="match_parent"
               android:orientation="vertical"
               android:gravity="center_vertical">

               <TextView
                   android:id="@+id/item_content_tv_title"
                   android:layout_height="wrap_content"
                   android:layout_width="match_parent"
                   android:textSize="13dp"
                   android:text="성지훈"
                   android:gravity="center"/>

               <TextView
                   android:id="@+id/item_content_tv_sub_title"
                   android:layout_height="wrap_content"
                   android:layout_width="match_parent"
                   android:text="일정있음"
                   android:textSize="11dp"
                   android:gravity="center"/>

           </LinearLayout>

       </LinearLayout>

       <HorizontalScrollView
           android:id="@+id/item_horizontal_scroll"
           android:layout_width="match_parent"
           android:layout_height="40.7dp"
           android:background="@color/white"
           android:overScrollMode="never"
           android:scrollbars="none">

           <LinearLayout
               android:layout_width="match_parent"
               android:layout_height="match_parent"
               android:orientation="horizontal"
               android:gravity="center_vertical"
               android:descendantFocusability="blocksDescendants">

               <TextView
                   android:id="@+id/item_content_tv_1"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="1"/>

               <TextView
                   android:id="@+id/item_content_tv_2"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="2"/>

               <TextView
                   android:id="@+id/item_content_tv_3"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="3"/>

               <TextView
                   android:id="@+id/item_content_tv_4"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="4"/>

               <TextView
                   android:id="@+id/item_content_tv_5"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="5"/>

               <TextView
                   android:id="@+id/item_content_tv_6"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="6"/>
               <TextView
                   android:id="@+id/item_content_tv_7"
                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>

               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>
               <TextView

                   style="@style/ListItemContentTextView"
                   android:layout_width="50dp"
                   android:text="7"/>


           </LinearLayout>

       </HorizontalScrollView>

   </LinearLayout>

   <View
       android:id="@+id/item_playerstat_line"
       android:layout_width="match_parent"
       android:layout_height="1dp"
       android:background="#40000000"
       android:visibility="visible" />

</LinearLayout>
```
실행화면
![image](https://user-images.githubusercontent.com/58676668/149308471-3a903663-b599-4b55-b720-3970ae041f5d.png)

상하 좌우 모두 스크롤이 정상적으로 되지만 좌우 스크롤시 좌측 멤버 이름을 고정시켜야 하고 상단 시간 헤더는 좌우스크롤시 함께 움직여야 합니다.

상단 헤더 스크롤은 아래와 같은 방법으로 해결하였습니다.
```
private fun observeScroll(){
    mBinding.scheduleHorizontalView.setOnScrollChangeListener{
        _,x,_,_,_ -> mBinding.timeHeaderSc.scrollX = x
   }
}
  ```
리사이클러뷰를 감싸고 있는 스크롤이 움직일 때, 수평 스크롤 값을 감지하여 함께 움직이도록 설정하였습니다.

스크롤 시 시간도 함께 움직이도록 되었습니다.

좌측 헤더에 고정시키는 방법을 좀더 공부해보고 다양한 방법으로 구현해볼예정입니다.

