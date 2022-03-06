# DataStore

DataStore란 SharedPreferences와 같이 key-value 형태로 값을 저장할수 있는 라이브러리 입니다. 안드로이드 자체에서도 SharedPreferences대신 DataStore사용을 적극 권장하고 있습니다.

코루틴 및 Flow를 사용하여 비동기적이고 일관된 트랜잭션 방식으로 데이터를 저장하는 것이 특징입니다.

DataStore는 아래와같은 특징이 있습니다.

-	코루틴과 Flow를 통해 읽고 쓰기에 대한 비동기 API를 제공합니다.
-	DataStore는 UI스레드를 호출해도 안전합니다.( Dispatcher.IO로 작동)
-	runtime exception으로부터 안전합니다.

먼저 gradle에 의존성을 추가합니다.

```
dependencies{

    // Preferences Data Store
    implementation "androidx.datastore:datastore-preferences:1.0.0-alpha07"
    implementation "androidx.datastore:datastore-core:1.0.0-alpha07"
    
}

```
아래는 DataStore 생성 방법입니다.
```

class DataStoreModule(private val context : Context) {
    private val Context.dataStore by preferencesDataStore(name = "dataStore")

    private val stringKey = stringPreferencesKey("key_name") // string 저장 키값
    private val intKey = intPreferencesKey("key_name") // int 저장 키값
}

```

DataStore에 값을 저장하는 방법은 edit()을 이용하며 반드시 비동기로 작업을 처리해야 합니다.

따라서 작성하는 메소드에 suspend를 통해 메소드가 코루틴 영역에서 동작할 수 있도록 처리합니다.

값을 저장하고자 한다면 아래와 같은 메소드를 작성할 수 있습니다.

```
suspend fun setText(text : String){
    context.dataStore.edit{
        it[stringKey] = text
    }
}

```
값을 불러오고자 한다면 전달받은 데이터는 Flow객체로 전달됩니다.
따라서 DataStore에서 값을 읽기 위해서는 아래와 같이 사용합니다.
```
val text : Flow<String> = context.dataStore.data
	.map { preferences ->
		preferences[stringKey] ?: “”
}

```
이 때 DataStore에서 data를 불러오지 못하면 IOException이 발생하기 떄문에 exception 처리를 합니다.

```
val text : Flow<String> = context.dataStore.data
        .catch { exception ->
            if (exception is IOException) {
                emit(emptyPreferences())
            } else {
                throw exception
            }
        }
        .map {preferences ->
            preferences[stringKey] ?: ""
        }

```

실제 사용을 위해 App을 만들고 항상 같은 객체를 반환하도록 만들었습니다.
```
class App : Application() {
companion object{
    lateinit var instance : App
}
    lateinit var dataStore : DataStoreModule
    override fun onCreate() {
        super.onCreate()
        instance = App()
        dataStore = DataStoreModule(this)
    }
}

```

접근시에는 App.instance.dataStore로 접근합니다.

읽기의 경우 Flow객체를 반환받고 collect()를 이용하면 값을 받아올 수 있습니다.
아래는 간단히 작성한 샘플 예제입니다.
```

class DatastoreActivity : AppCompatActivity(), View.OnClickListener{
    lateinit var mBinding : ActivityDatastoreBinding
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        mBinding = ActivityDatastoreBinding.inflate(layoutInflater)
        setContentView(mBinding.root)
        mBinding.getBtn.setOnClickListener(this)
        mBinding.submitBtn.setOnClickListener(this)
    }

    override fun onClick(v: View) {
        when(v){
            mBinding.getBtn -> {
                CoroutineScope(Dispatchers.Main).launch{
                    App.instance.dataStore.text.collect{
                        mBinding.getTextview.text = it
                    }
                }
            }
            mBinding.submitBtn -> {
                CoroutineScope(Dispatchers.Main).launch {
                    App.instance.dataStore.setText(mBinding.editTxt.text.toString())
                }

            }

        }

    }
}
```

실행화면
<img width="287" alt="image" src="https://media.oss.navercorp.com/user/29230/files/6a286500-72fc-11ec-853f-9440deca9cdb">
<img width="269" alt="image" src="https://media.oss.navercorp.com/user/29230/files/77ddea80-72fc-11ec-8c62-6e0b04c4bcf4">
