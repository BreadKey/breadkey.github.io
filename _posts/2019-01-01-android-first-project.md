---
layout: post
author: breadkey
title: 안드로이드 첫번째 앱 만들기
category: study
tag: android
---
## 1. 화면 구성하기


{% include figure.html src="/assets/img/activity-main.PNG" %}
> 왼쪽은 **Design**화면으로 실제 기기에서 보일 화면을 보여준다. 오른쪽은 **Blueprint**화면으로 화면의 레이아웃을 편집할 수 있다.

## 2. 에뮬레이터 만들기
{% include figure.html src="/assets/img/avd-manager-icon.png" %}
> 우측 상단에 존재하는 **AVD Manager** 아이콘을 클릭하면 애뮬레이터를 플랫폼으로 만들고 실행할 수 있는 창을 띄울 수 있다.
{% include figure.html src="/assets/img/create-virtual-device.PNG" %}
{% include figure.html src="/assets/img/device-definition-choice.PNG" %}
> **Create Virtual Device**를 클릭하여 새로운 에뮬레이터를 만든다.
{% include figure.html src="/assets/img/system-image.PNG" %}
> 원하는 기기 환경을 가진 이미지를 클릭하여 설치한 후 이름, 방향 등을 설정한다.
{% include figure.html src="/assets/img/action-play.PNG" %}
> 생성이 완료되면 Actions 탭의 화살표(Lunch this AVD in the emulator)버튼을 클릭해 구동한다.
>> <span style="color: red">Emulator: glClear4?? GL err 0x502</span>오류가 났을 때 나의 경우 AVD 설정에서 EmulatedPerformance의 Graphics를 **Software - GLES 2.0**, RAM을 2024MB, VM heap을 512MB로 설정하니 해결되었다.(Nexus 5에서는 Graphics설정을 바꿀 수 없었음)

## 3. 코드 뜯어보기
```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```
> ### onCreate
>> 안드로이드에서 main과 같이 시작점이 되는 함수

> ### setContentView
>> 파라미터로 넘겨진 View를 화면에 보여준다. R.layout.activity_main은 /res/layout에 있는 **activity_main.xml**을 가져온다.

## 4. 하나식 바꿔보기
> ### TextView를 Button으로 바꿔보기
```
	<TextView
		...
		android:text="Hello World!"
		...
	/>
```
activity_main.xml에 보면 다음과 같은 부분이 있다. 이 TextView를 Button(대소문자 주의)으로 바꿔본다.
{% include figure.html src="/assets/img/textview-to-button.PNG" figcaption="Preview에서 Button으로 바뀌는게 보인다." %}
> ### Button을 눌렀을 때 메세지 보내기
>> #### 1. XML 레이아웃 파일의 버튼에 onClick 속성 값 넣기
```
	<button
		...
		android:onClick="onButtonClicked"
		...
	/>
```
>> #### 2. 자바 파일에 이벤트 처리 메소드 추가하기
```
public class MainActivity extends AppCompatActivirt {
    public void onButtonClicked(View view) {
        Toast.makeText(getApplicationContext(), "버튼이 눌렸습니다.", Toast.LENGTH_LONG).show();
    }
}
```
{% include figure.html src="/assets/img/click-button.PNG" figcaption="버튼을 클릭하면 토스트 메세지가 나오는 것을 확인할 수 있다" %}