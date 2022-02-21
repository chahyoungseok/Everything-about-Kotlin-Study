# Everything-about-kotlin-Study

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#viewbinding-databinding">ViewBinding DataBinding</a></li>
    <li><a href="#basic-layout">Basic Layout</a></li>
    <li><a href="#fragment">Fragment</a></li>
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
        setContentView(binding.root)

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

<br>

## Basic-Layout

target : Layout이 많아지면 하드웨어가 버거워하기 때문에 각각의 Layout을 잘 활용하여 간소하게 만드는게 목적이다.

<br>

#### 1. RelativeLayout

Component 들을 상대적으로 묶어주는 Layout (Parent 와도 묶임)<br>
먼저쓰는 Component일수록 뒤로간다.<br>


``` relative layout
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <View
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:background="#FFC107"
        />
    <View
        android:id="@+id/red_view"
        android:layout_alignParentLeft="true"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#F44336"
        />
    <View
        android:id="@+id/green_view"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#4CAF50"
        android:layout_toRightOf="@+id/red_view"
        />

</RelativeLayout>
```

![re](https://user-images.githubusercontent.com/29851990/154704353-86abbdbd-91b5-4566-b7db-7404dd2547d7.PNG)

<br>

#### 2. Linear Layout

Horizontal, Vertical의 종류가 있고, 그의 기준으로 정렬되게 배치해주는 Layout<br>
weight를 주어 배치할 수 있고, weight가 없다면 적용된 길이만큼 가져감

``` linear layout
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:orientation="horizontal"
    android:weightSum="1">

    <View
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#F44336"/>

    <View
        android:layout_weight="1"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:background="#FFEB3B"/>

    <View
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#3F51B5"/>

</LinearLayout>
```

![Lineaar](https://user-images.githubusercontent.com/29851990/154705772-a806348f-8280-47a4-935c-5029648c9c7b.PNG)

#### 3. Constraint Layout

서로 앵커로 연결되어있고, 계층이 하나인 Layout<br>
화면이 Portrait과 Landscape를 왔다 갔다해도 다른 Layout처럼 완전히 메모리에 Delete 되었다가 다시 생성하지않고 비율만 바꿈

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:orientation="horizontal"
    android:weightSum="1">

    <View
        android:id="@+id/red_view"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#F44336"
        />
    <View
        android:id="@+id/blue_view"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#3F51B5"
        app:layout_constraintTop_toBottomOf="@+id/red_view"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        />
    <View
        android:id="@+id/pink_view"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#E91E63"
        app:layout_constraintTop_toBottomOf="@+id/blue_view"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        />

</androidx.constraintlayout.widget.ConstraintLayout>
```

![Const](https://user-images.githubusercontent.com/29851990/154708509-608943de-6ba1-4984-8bc8-220e5555e31a.PNG)

<br>

## Fragment

화면의 크기가 다양해짐에 따라 여러개의 화면요소를 원하는 수요가 증가<br>
여러개의 Fragment를 조합하여 Activity가 출력하는 한 화면의 UI를 표햔할 수 있다.<br>
하나의 Fragment를 다른 Activity에 재사용할 수 있다<br>

![다운로드](https://user-images.githubusercontent.com/29851990/154827068-e469ca92-0e6b-4450-96e7-ac6c1fd25a2d.png)

다음과 같이 화면이 큰 테블릿에서는 하나의 Activity에 2개의 Fragment를 넣었고,<br>
화면이 작은 휴대폰에는 하나의 Activity마다 하나의 Fragment를 넣으며 구현합니다.

<br>

### FragmentManager

Fragment를 사용한다면 필수적으로 들어간 FragmentManager를 알아보려한다.<br>
정의로는 "앱 Fragment에서 작업을 추가, 삭제 또는 교체하고 백 스택에 추가하는 등의 작업을 실행하는 클래스" 라고 표현되어있다.<br>
간단하게 Activity와 Fragment 사이에서 서로를 이어주는 역할이다.

역할
 - Fragment를 추가, 삭제, 교체등의 작업 및 Fragment transaction을 Fragment 백스택에 저장
 - Activity와의 통신 (Fragment 내의 구성요소들 하나하나에 접근할 수 있도록 해줌)

![image](https://user-images.githubusercontent.com/29851990/154977586-8fc64526-be65-4763-a162-7875bfad9a77.png)

 - 위의 그림처럼 각 호스트는 FragmentManager를 가지고 있다. 여기에 접근해서 유저에게 보여지는 Fragment를 조작한다.
 - 그리고 가장 바깥의 host activity는 child fragment들을 관리하기위해서 supportFragmentManager로 fragmentManager에 접근한다.
 - host fragment는 자식 fragment를 관리하기 위해 childfragmentManager를 사용하고, 자기를 관리하는 FragmentManager에 접근하기 위해 parentFragmentManager를 사용한다.

<br>

FragmentManager의 또다른 개념
 - fragmentManager는 fragment 백스택을 관리한다. 이 작업은 FragmentTransaction을 commit하며 수행된다.
 - 유저가 백버튼을 누르거나, FragmentManager.popBackStack() 을 호출하면 가장위에있는 FragmentTransaction이 스택에서 pop된다.
 - 예를 들어 Bottom Navigation A에서 B로 이동한 다음 백버튼을 눌렀을 때, A -> B transaction이 스택에서 pop 되면서 B -> A 로 작동
 - 만일 더이상 pop할 transaction이 없고, childfragment가 없다면 activity로 거슬러 올라간다.
 - popBackStack을 하기 직전에 수행한 transaction을 addToBackStack 하지않았다면 transaction이 반대로 처리되지 않는다.
 - 따라서 transaction을 pop하고 싶다면 add를 먼저 수행해야한다. 

<br>

#### Fragment Transaction
 
 Fragment 추가, 삭제, 교체 뿐아니라 Fragment 백스택 관리, Fragment 전환, 애니매이션 설정 등
 
``` fragment access
class MainActivity : AppCompatActivity() {
    private lateinit var main_binding : ActivityMainBinding

    private lateinit var fragmentManager : FragmentManager
    private lateinit var transaction : FragmentTransaction

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        main_binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(main_binding.root)
        
        // fragmentManager 할당
        fragmentManager = supportFragmentManager
        
        // fragmentTransaction 시작
        transaction = fragmentManager.beginTransaction()

        // fragment transaction 마무리 
        transaction.commit()
        
        // fragment 추가
        transaction.add(R.id.fragment_container, new fragment 이름)
        
        // fragment 교체
        transaction.replace(R.id.fragment_container, new fragment 이름)
        
        // fragment 제거
        transaction.remove(fragment 이름)
        
        // 백스택에 저장
        transaction.addToBackStack(null)
        
        // transaction 마무리
        transaction.commit()
    }
}
```
 
#### Fragment Lifecycle
