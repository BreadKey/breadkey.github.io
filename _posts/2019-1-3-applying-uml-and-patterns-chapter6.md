---
layout: post
title: Applying UML and Patterns Chapter.6
author: breadkey
categories: study
tags: [object-oriented, applying-uml-and-patterns]
---
## Goals and Stories
> 고객과 사용자들은 다양한 목표(UP에서는 *needs*)를 가지고 있으며 컴퓨터 시스템이 이들이 목표를 이룰 수 있게 도와주길 원한다. 다양한 목표들은 여러가지 방법으로 분석될 수 있는데, 그 중 유스케이스는 고객들도 한눈에 볼 수 있을 정도로 간단하고 친숙한 좋은 방법이다.

> 유스케이스는 모든 관계자들에게 목표를 이해할 수 있게 도와준다. 유스케이스는 대게 목표를 이루기 위해 시스템을 사용하는 과정을 담은 이야기이다.

> 유스케이스는 특히 기능적 요구사항을 분석하는데 특화되어 있다.

* * *
## Use Cases and Adding Value
> **actor**는 사람 뿐만 아니라 컴퓨터 시스템, 기관 등 어떠한 행동을 가진 모든것이 될 수 있다.

> **Scenario**는 행동, actor와 system사이의 상호작용을 나열한 것이다. 이는 **use cas instance**라고도 불린다.

> 유스케이스는 actor가 목표를 이루기 위해 시스템을 사용할 때 발생할 수 있는 **성공**한 시나리오와 **실패**한 시나리오의 모임이다.

> 유스케이스는 질문에 초점을 맞추는 것이 중요하다. "세탁물 목록"등 단순한 요구사항에 대한 생각이 아닌 "시스템을 어떻게 사용해야 사용자의 목표를 모두 이루어주거나 사용자에게 적절한 가치를 보여줄 수 있을까?"라고 질문하는 것에 초점을 맞추어야 한다.

> 유스케이스는 특징이나 기능들을 목표 지향적으로 써내려 가기 떄문에 사람들이 정말로 무엇을 원하는지 알 수 있다.

> 이러한 유스케이스는 요구사항이 **어떻게** 시스템이 가치를 더하고 목표를 만족시켜주는지에 대해 집중될 수 있게 한다.

* * *
## Use Cases and Functional Requirements
> 유스케이스는 시스템이 무엇을 해야하는지에 대한 기능적인 요구사항이다.

> 유스케이스는 다이어그램이 아닌 텍스트 문서이다.

* * *
## Use Case Types and Formats
> ### Black-Box Use Cases and Ssystem Responsibilities
>> **Black-Box** 유스케이스는 가장 통상적이며 추천되는 유스케이스다. 이는 시스템 내부가 어떻게 작동하는지에 대해 기술하지 않고 시스템이 **책임**을 가진 것 처럼 기술한다. 책임은 객체지향적인 사고에 있어 매우 중요하다.

>> 블랙박스 유스케이스를 통해 시스템이 어떻게 돌아가는지가 아닌 **무엇을 할지**에 초점이 맞출 수 있다. 이는 분석에 적합하며 시스템이 어떻게 돌아가는지는 설계부분에 들어가야 한다.

<table>
	<tbody>
		<tr>
			<th style="width: 50%">Black-box-style</th>
			<th>Not</th>
		</tr>
		<tr style="text-align: center;">
			<td>시스템은 판매를 기록한다.</td>
			<td>시스템은 판매를 데이터베이스에 저장한다...(더 나아가) 시스템은 SQL INSERT문을 날려 판매를 기록한다.</td>
		</tr>
	</tbody>
</table>

> ### Formality Types
- **brief**: 대부분 주요 성공 시나리오를 가지고 한 단락에 요약한다.
- **casual**: 형식이 정해져있지 않다. 다양인 시나리오를 여러개의 단락에 걸쳐서 쓴다.
- **fully dressed**: 가장 정교하다. 모든 절차와 변화가 상세하기 써진다. precondition이나 success guarantess와 같은 부분도 존재한다.

> ### Two-Column Variation
>> actor의 행동과 시스템의 책임으로 나누어서 작성한다. 매우 직관적이다.

<table align="center" style="width: 80%; text-align: left;">
	<tbody>
		<tr>
			<th>Primary Actor</th>
		</tr>
		<tr>
			<td>...</td>
		</tr>
		<tr>
			<th>Main Success Scenario</th>
		</tr>
		<tr>
			<td style="width: 50%">Actor Action</td>
			<td>System Responsibilitiy</td>
		</tr>
		<tr>
			<td>1. 고객은 POS에 상품과 함께 도착한다.</td>
		</tr>
		<tr>
			<td>2. 판매원은 새로운 판매를 시작한다.</td>
		</tr>
		<tr>
			<td>3. 판매원은 상품번호를 입력한다.</td>
			<td>4. 각 상품을 기록하고 상품 설명과 총액을 보여준다.</td>
		</tr>
		<tr>
			<td>...</td>
			<td>...</td>
		</tr>
	</tbody>
</table>

> **가장 좋은 형식은 없다.**

* * *
## Explaining the Sections
> ### Preface Elements
>> 일종의 "header"로서 주요 성공시나리오 이전에 반드시 읽어야 하는 것들이다.
#### Stakeholders and Interests List
>>> header중 가장 중요하다. 이부분은 시스템이 무엇을 해야하는지 정의할 수 있게해준다.  
또한 "유스케이스에 무엇이 들어가야할까?"에 대한 대답이 되어준다. 이 부분을 모두 만족시키는 것이 들어가야한다!

>> #### Preconditions and Success Guarantess(Postconditions)
- **Preconditions**
유스케이스의 시나리오를 시작하기 전에 어떤 것이 항상 **참**이여야 하는지 쓰는 부분이다. "컴퓨터 전원이 켜져있어야 한다."등 너무 당연한 것은 쓸 필요가 없고 읽는 사람에게 경고가 될 만한 것들만 쓴다.
- **Postconditions**
성공 시나리오나 대체 시나리오든 간에 시나리오가 성공적으로 끝났을 때 무엇이 **참**이 되어야 하는지 쓴다. 모든 관계자들이 필요한 것들을 보증해야한다.

> ### Main Success Scenario and Steps(Basic Flow)
>> "happy path" 시나리오라고도 불린다. 이는 관계자들의 요구를 만족시켜주는 성공적인 길을 서술한다. 어떠한 조건이나 가지들을 포함하지 않는다. **조건이나 가지들은 Extension section에서 쓰자.**  
이 시나리오는 세 종류로 분류되는 단계들을 기록한다.
1. actor들 간의 상호작용
2. (대부분 시스템에 의한) 검증
3. 시스템에의한 상태변화

>> 유스케이스의 첫 번째 단계는 항상 이 세단계에 속하진 않지만 시나리오의 시작을 알리는 트리거 이벤트를 포함한다.(ex. 1. 고객은 상품을 구매하기 위해 상품을 들고 POS 판매대로 온다.)  
actor의 이름은 첫글자가 항상 대문자여야한다.

> ### Extensions(or Alternate Flows)
>> 확장은 매우 중요하다. 모든 성공과 실패 시나리오나 가지들을 쓴다. 주요 성공 시나리오보다 훨씬 길고 복잡하다. 이부분 또한 관계자의 요구를 만족시키는 것에 중점을 두어야한다. 확장 시나리오들은 주요 성공 시나리오의 가지이므로 그것에 관하여 쓰일 수 있다. 예를 들어, 주요 성공 시나리오의 3단계에 관련된 시나리오라면 "3a. 유효하지 않는 품번"과 같이 쓰일 수 있다.  

>> 확장 시나리오는 **조건**(condition)과 **제어**(handling) 두 부분으로 나뉜다. 조건은 시스템이나 actor에 의해 발견될 수 있도록 쓰여야 한다. 만약 외부 세금 계산 시스템이 정상적으로 동작하지 않는 경우 다음처럼 쓸 수 있을 것이다.
```
5a. 시스템은 외부 세금계신시스템 서비스와 통신에 실패했음을 발견한다.
5a. 외부 세금 계산 시스템이 작동하지 않는다.
```
후자가 추론에 불과한 것에 비해 전자는 시스템이 발견 가능한 것이기 때문에 전자처럼 작성하는 것이 더 선호된다.  

>> 제어는 한 단계로 요약되거나 여러 단계로 나타낼 수 있다. 제어가 끝난 후 메인 시나리오로 다시 병합된다.  
>> 때떄로 "신용카드를 사용한 계산"과 같은 어떤 확장점은 다소 복잡할 수 있다. 이 확장점은 메인 시나리오와 마찬가지로 분기점이 생성될 수 있다.
```
7b. 신용카드로 결제하기
	1. ...
	2. ...
		2a. 조건발생
			1. ...
	...
```

> ### Special Requirements
>> 유스케이스와 관련된 품질 요구사항이나 제약사항같은 비기능적인 요구사항이 존재하면 이곳에 작성한다.
```
Special Requirements:
- 큰 평평한 모니터에 터치 스크린 UI가 있다. 문자는 1미터 안에서 볼 수 있어야한다.
- 신용카드 인증은 30초 이내에 이루어져야 한다.
...
```

> ### Technology and Data Variations List
>> 유스케이스와 관련된 **무엇**이 아닌 **어떻게** 처리가 되야 하는지에 대한 기술적인 변화를 이곳에 작성한다.
```
Technology and Data Variations List:
3a. 품번은 레이저 스캐너나 키보드에 의해 입력된다.
3b. 품번은 UPC, EAN, JAN, 또는 SKU 코딩 방식으로 되어있다.
...
```
*이 부분엔 다른 케이스에대한 다양한 행동을 여러단계에 걸쳐 기술하면 안된다. 그 행동을 반드시 기술해야한다면 Extension section에 적어라.*

* * *
## Goals and Scope of a Use Case
> 시스템 영역, 액터, 목표에 따라 다양한 레벨의 유스케이스가 존재할 수 있다. 그런데 낮은 레벨의 유스케이스는 분명 그보다 더 높은 레벨의 유스케이스 안에 포함될 수 있다. 요구사항을 분석하는 데 있어 어떠한 레벨의 유스케이스가 적정한지 판단할 떄 **EBP**(Elementary Business Processes)테스트를 통해 판단해볼 수 있다.
>> EBP: 한 사람이 한 장소에서 한 번 행하는 일
>>> *"상품 삭제하기"와 같은 매우 작은 일이 아니다.*

> EBP 유스케이스는 대략 5~10단계 정도의 주요 성공 시나리오가 나온다. 몇날 며칠이 아닌 대략 몇분에서 한시간 정도 안에 끝나는 일이다.  
너무 낮은 레벨의 너무 많은 유스케이스를 정의하는 실수를 많이 하는데, 이들은 분명 EBP 유스케이스안에 포함될 수 있다.

> ### Reasonable Violations of the EBP Guideline 
>> EBP 테스트를 통과하지 못한다고 유스케이스가 될 수 없는건 아니다. EBP 테스트는 하위 유스케이스를 판별하는데 도움이 된다.

>> #### Use Cases and Goals
>>> EBP 레벨의 유스케이스는 **사용자** 목표 레벨의 유스케이스이다. 이를 위해 다음과 같은 절차를 진해할 수 있다.
1. 사용자의 목표를 찾는다.
2. 각각의 목표에 대해 유스케이스를 정의한다.

>>> 사용자의 목표는 유스케이스의 이름이 된다.

>>> EBP 테스트를 거치면서 사용자의 목표와 유스케이스가 적절한 레벨인지 판단할 수 있다.  
어떠한 목표를 들었을 때, 목표의 목표가 무엇인지에 대해 질문하면서 더 높은 레벨의 목표를 찾을 수 있다.

>> #### Example: Applying the EBP Guideline
>>> 다음은 상위 레벨의 목표를 찾아가는 대화이다.
```
분석가: POS시스템을 어떤 목적을 가지고 사용하고 싶으신가요?
판매원: 로그인을 빨리하고 판매를 하고싶어요.
분석가: 로그인을 빨리하고 싶으신데, 어떤 목적 때문에 그러신가요?
판매원: 판매를 기록하거나 다른 일을 하는 등 시스템을 사용하기 위해 저의 신원을 밝히고싶어요.
분석가: 무엇때문에 그러시죠?
판매원: 도둑이나 데이터 손실, 회사 개인 정보가 나타나는 것을 방지하고 싶거든요.
```
목표의 목표를 물어보면서 더 높은 레벨의 목표를 찾아나갈 수 있다.  

>>> "도둑...방지" 목표는 사용자의 목적보다 높은 레벨이다. 이는 기업의 목적이지 EBP가 아니다.   

>>> 한 단계 낮춘 "저의 신원을 밝히고 싶어요."는 관찰할 수 있거나 측정할 수 있는 비즈니스 가치를 만들지 않기 때문에 EBP가 아니다.  

>>> 그러므로 "판매를 하고싶어요."가 가장 적절한 EBP라고 볼 수 있다.

>> #### Subfunction Goals and Use Cases
>>> 이전에 "로그인"이 사용자 목표에서 제외되었지만, 이것은 사용자 목적을 도와주는 낮은 레벨의 목표이며 **subfunction goal**이라고 불린다.  

>>> 비록 이 subfunction goal이 모두가 인정하는 문제일지라도 무조건적으로 유스케이스에 쓰여서는 안된다. 한 시스템에 수백개의 subfunction goal들이 있을 수 있는데 모두 유스케이스에 포함시킨다면 이는 유스케이스의 복잡도만 올릴 뿐이다.  

>>> subfunction이 반복되거나 다양한 사용자 목표 레벨의 유스케이스의 precondition일 때 작성하는 것이 좋다.

>> #### Goals and Use Cases Can be Composite
>>> 기업 수준의 레벨로인해 목표는 합쳐질 수 있다. 마찬가지고 유스케이스는 이러한 목표들을 만족시키기 위해 다른 레벨로 쓰일 수 있으며, 낮은 레벨의 유스케이스들이 합쳐진 것일 수도 있다.

* * *
## Finding Primary Actors, Goals, and Use Cases
> 유스케이스는 **Primary actor**의 목표를 만족시키는 것이므로 이프라이머리 액터를 찾아야하는데, 과정은 다음과 같다.
### 1. 시스템의 영역을 결정한다.
>> 단순한 소프트웨어 어플리케이션인지, 하나의 유닛으로서 하드웨어와 함께하는 어플리케이션인지, 한사람이 사용하는건지, 아니면 전체 기관이 사용하는지 결정해야한다. 애매하다면 시스템 밖에 뭐가 있는지 식별함으로서 결정할 수 있다.

> ### 2. 프라이머리 액터를 식별한다.
>> 이 시스템이 제공하는 서비스를 사용하면서 모든 목표가 만족되는 사용자가 프라이머리 액터다.
>> #### 프라이머리 액터와 목표를 찾는데 도움이 되는 질문
- 누가 이 시스템을 시작/종료하는가?
- 누가 사용자, 보안 관리를 하는가?
- 시스템이 고장났을 때 재시작하는 걸 지켜보는 과정이 있는가?
- 소프트웨어 업데이트가 어떻게 이루어지는가? push나 pull 업데이트인가?
- 누가 시스템의 관리자인가?
- 이벤트가 발생할 "시간"이 액터인가?
- 누가 시스템 성능을 평가하는가?
- 누가 기록들을 평가하는가?

> ### 3. EBP 가이드에 따라 각각의 사용자 목표를 찾는다.

> ### 4. 사용자 목적을 만족시키는 유스케이스를 목적에 따라 이름을 짓는다.