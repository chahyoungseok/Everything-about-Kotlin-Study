# Everything-about-kotlin-Study

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#viewbinding-databinding">ViewBinding DataBinding</a></li>
  </ol>
</details>
<br>

## ViewBinding DataBinding
안드로이드 스튜디오를 사용하게 되면 가장 처음겪는 문제이다.<br>
kotlin-android-extensions 지원이 중단되어 build.gradle 파일에 다음과 같이 추가해야합니다.<br>

![캡처](https://user-images.githubusercontent.com/29851990/153743967-8fa272d2-b01d-492c-a4b1-a44e55aa114c.PNG)

하지만 위의 과정은 findViewById 를 사용될 모든 View에서 가져와야하므로 코드가 지저분해지고, 중복된 id에 한하여 참조시 실수확률이 증가한다.<br>
따라서 ViewBinding 및 DataBinding을 공부하고자 합니다.

<br>

### ViewBinding
마찬가지로 build.gradle 파일에 다음과 같이 추가합니다.<br>

![2](https://user-images.githubusercontent.com/29851990/153744107-2e9baf5b-d2b6-40bf-bc97-f096d4445515.PNG)

<br>

여기서 inflate : xml에 있는 레이아웃들을 메모리에 객체화시키는 행위 이므로<br>
ActivityMainBinding.inflate(layoutInflater)이 그런과정이라고 보면 되겠습니다.<br>
 
 ``` viewBinding
 import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Toast
import com.example.practiceapplication.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var binding : ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater) 
        setContentView(R.layout.activity_main)

        binding.viewBindingEx.setOnClickListener {
            Toast.makeText(applicationContext,"message",Toast.LENGTH_LONG)
        }
    }
}
```

<br>

### DataBinding
build.gradle 파일에 다음과 같이 추가합니다.<br>

![3](https://user-images.githubusercontent.com/29851990/153744553-9601672c-f0d8-4b8b-b619-73d90d727f8a.PNG)

<br>

DataBinding은 view와 데이터를 직접적으로 연결할 수 있고, 양방향 바인딩을 할 수 있습니다.<br>
레이아웃에서 데이터 모델을 연결해서 이름을 붙여주고, 사용할 view에 @{이름.변수이름}을 넣어줍니다.

![5](https://user-images.githubusercontent.com/29851990/153744877-a93d4531-417d-4159-8677-03e75388ba8a.PNG)

<br>

아래와 같이 data 모델을 만들고, 변수도 설정합니다.<br>
DataBindingUtil을 통해 view를 binding하고, binding한 변수를 이용해 data 모델을 전달합니다.

``` dataBinding
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Toast
import androidx.databinding.DataBindingUtil
import com.example.practiceapplication.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var binding : ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        binding.viewBindingEx.setOnClickListener {
            Toast.makeText(applicationContext,"message",Toast.LENGTH_LONG)
            var model = dataBindingEx("BindingMes")
            binding.tes = model
        }
    }
}

data class dataBindingEx(
    var test1 : String
)
```

<br>

### ViewBinding vs DataBinding
 - 가장 중요한점은 특징이라고 생각합니다.

||ViewBinding|DataBinding|
|:----:|:---:|:---:|
|특징|속도가 빠르다|양방향 바인딩을 지원한다|

<br>

### two way binding
 - 뷰와 컴포넌트 클래스의 상태변화를 상호반영하는것.
 - 1-way-binding은 적당한 event가 발생하지 않으면 데이터에 적용시킬 수 없습니다.
 - 2-way-binding은 watcher를 통해 계속 적용이 됩니다.
 - 다음 그림으로보면 이해가 쉬울것입니다.

2-way-binding

![2wa](https://user-images.githubusercontent.com/29851990/153745463-25fc2470-4282-4fa2-9daf-2f6731643dfa.PNG)

1-way-binding

![1w](https://user-images.githubusercontent.com/29851990/153745531-3b65540f-3f14-47b0-958a-2f1e702999f6.PNG)

<br>

그럼 2-way-binding을 만들어보겠습니다.

![6](https://user-images.githubusercontent.com/29851990/153745972-e08233cc-6ba7-453a-bf62-3ffe509e190a.PNG)

![7](https://user-images.githubusercontent.com/29851990/153745974-25dcf2ee-0135-4b00-9c26-24506ee1de3b.PNG)


``` 2-way-binding
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.databinding.DataBindingUtil
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider
import com.example.practiceapplication.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    private lateinit var binding : ActivityMainBinding
    private lateinit var viewModel : MainActivityViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        viewModel = ViewModelProvider(this).get(MainActivityViewModel::class.java)

        binding.lifecycleOwner = this
        binding.viewModel = viewModel
    }
}

class MainActivityViewModel : ViewModel(){
    val userName = MutableLiveData<String>()

    init {
        userName.value = "Frank"
    }
}
```

다음 프로그램은 editText에 입력하면 바로바로 TextView에 적용이 됩니다.

## 
