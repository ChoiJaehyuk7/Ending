# TERRAFORMING

## YOUTUBE Link
---
https://youtu.be/Hhana_6slpE
## 게임 소개
---

**1. 타이틀**

  * TerraForming
  
**2. 컨셉**

  * 함선을 강화하여 외계 함선을 무찌르고 외계행성을 테라포밍(지구화)하기.
  
**3. 플랫폼**

  * PC
  
**4. 장르**

  * 비행 FPS
  
**5. 타겟 플레이어**

  * 비행기, 전투기 조종을 즐겨하는 유저.
 
## 게임 특징
---
### 기획적 특징
* 플레이어는 게임 시작 시 함선 조종사 역할을 맡게 되며 스콜스 대령으로부터 튜토리얼을 진행하며 기본 조작키와 기본 공격, 스킬 사용 방법을 차례대로 배우게 된다. 튜토리얼을 완료하면 곧바로 스테이지 단계로 넘어가 진행하지 않고 지구 내부에서 함선 강화를 진행한 뒤 외계 행성에 존재하는 적들을 제거하라는 임무를 전달받는다. 임무를 전달받은 플레이어는 즉시 함선에 탑승하여 이륙해서 우주정거장으로 향한다. 우주정거장에 도착한 플레이어는 서브 퀘스트를 전달받은 후 워프시스템을 이용하여 스테이지1 '아인스행성'으로 향하고, 포탈을 타게되면 스테이지1을 진행하게 된다. 이와 같이 튜토리얼과 본 스테이지 사이에 강화, 워프, 퀘스트 등 부가적인 요소를 추가하여 단조롭게 느낄수 있는 시퀀스를 흥미롭게 기획해보았다.

* 우주라는 광활한 공간적 배경 때문에 플레이어는 어디로 이동해서 무얼 진행해야 하는지 헷갈려 할 수 있다. 그래서 기획 단계에서 조작튜토리얼, 우주공간, 지구내부, 우주정거장, 스테이지1 까지 BOOL값 조절을 통한 단계적 시퀀스를 설정하였다. 각 시퀀스에 입장하면 메시지 UI를 이용하여 플레이어로 하여금 무엇을 해야하는지 알려준다. 메시지 UI는 무전 통신으로 설계하여 음성도 들어가있으면 좋을 것 같아 각각의 메시지를 사이트에서 녹음하여 설정하였다.

### 기술적 특징
**1. DontDestroyOnLoad() 활용**
 * 각 Scene 별로 해당 시퀀스 단계에 맞는 메시지가 차례대로 출력되어야해서 첫 튜토리얼 Scene부터 bool값으로 현재 진행 단계를 저장하여 활성화된 bool값일 때 Text가 출력되고 필요에 따라서 진행방향을 표시한 화살표 UI 등이 표시된다. 이는 튜토리얼부터 테라포밍하는 엔딩 단계까지 모두 Bool type으로 설정되어있으며, Scene이 바뀌어도 DontDestroyOnLoad()로 인해 값이 초기화 되지 않는다.
 * 'TerraForming'에는 강화 시스템이 존재한다. 스테이지에 사용되는 플레이어의 함선을 공격력, 방어력, 기술력, 관통력 4가지로 분류하여 각각의 요소를 업그레이드할 수 있는 시스템이다. 강화 value가 높아질수록 함선의 parameter 값 또한 증가해야하고, 이 변경된 value는 다른 Scene에서도 저장되어야 하기 때문에 DontDestroyOnLoad()를 사용하여 현재 강화 수치를 저장하였다.
 * 강화를 하려면 재화를 사용해야한다. 'TerraForming'에는 미네랄 재화, 전기 재화 총 2가지를 사용하여 함선을 강화해야하는데, 이 재화들은 매 초마다 특정한 수치만큼 상승한다. Scene이 변경되어도 재화는 계속해서 저장되어야 하고, 늘어나야하기에 OnDestroyOnLoad()를 사용하였다.
 * 우주정거장 Scene에서 워프 시스템을 이용하여 우주 Scene으로 넘어갈 때, 워프 시스템에서 어느 행성으로 이동할 것인지 선택 여부에 따라 그 bool 값이 true로 저장되고, 이 bool 값을 DontDestroyOnLoad()로 저장하여 우주 Scene에서 해당 bool 값에 설정한 좌표로 이동하게 된다.
 * 우주정거장 Scene에는 워프 시스템 말고 퀘스트 시스템도 있다. 퀘스트는 공격력, 방어력, 기술력, 관통력 4가지 강화 parameter를 특정한 수치까지 도달할 시 재화를 보상으로 주는 시스템이다. 강화가 진행되는 지구 내부 Scene에서 저장된 강화 수치를 퀘스트 시스템이 진행되는 우주정거장 Scene 에서 사용해야하기 때문에 OnDestroyOnLoad()를 이용하였다.

**2. Enemy AI 시스템**
* 가장 구현하기 어려웠던 부분으로, 기존에 배웠던 AI는 Enemy가 돌아다니는 땅이 있고, 이를 Bake하고 Nav mesh Agent 사용하는 것이었다. 하지만 우주라는 공간적 배경 상 Enemy인 적 외계함선이 딛는 땅이 없기에, Bake할수도 없고 Nav mesh Agent를 사용할 수도 없었다. 그래서 사용한 방법으로는 Player와 Enemy 사이의 거리를 .magnitude로 구별 후, 둘 사이의 거리가 400 이상일 때와 그 이하일 때로 나누었다. 거리가 400이상일 때에는 Quaternion.LookLotation()과 Quaternion.Slerp()를 이용하여 Enemy를 Player방향으로 회전시키고 다가가게 만들었다. 400 이하일 때에는 여전히 Quaternion.LookLotation()과 Quaternion.Slerp()를 이용하여 Player방향을 바라보지만 다가가지 않고 Instatiate()를 이용하여 플레이어를 공격하도록 설정하였다. 완성된 EnemyAI 코드 길이는 그리 길지 않지만 생각했던 의도에 맞게 적용시키기까지 수많은 시행착오가 있었다.

**3. Player Skill 시스템**
* Player는 미사일, 스텔스, 회복, 궁극기 스킬을 사용할 수 있다. 미사일은 Enemy에게 더 강력한 데미지를 입힐 수 있고, 스텔스 스킬을 사용하면 적에게 감지되지 않아 Enemy가 일정 시간동안 공격을 하지 않는다. 스텔스 기능은 스킬 사용시 bool값을 true로 설정하고, Enemy의 공격을 다룬 스크립트에서는 이 bool값이 true면 공격을 하지않고, false면 공격을 할 수 있도록 하였다. 일정 시간동안 이 bool 값이 true로 변경되며, 이 스킬 지속 시간은 HP UI 상단에 표시되어 남은 지속 시간을 확인할 수 있다. 회복 시스템은 HP를 20% 상승시키는 것으로 설계하였다. 궁극기 시스템은 적에게 기본 공격과 미사일 공격을 행할 시 조금씩 게이지가 차게 되는데, 이 게이지가 100%에 도달하게 되면 궁극기를 사용할 수 있다. 궁극기는 사용 시 0%에 도달할 때까지 매우 많은 bullet을 발사하여 스테이지를 클리어하는 데에 있어 핵심적인 역할을 한다. 

**4. Player 함선의 탄창 현황**
* Player가 기본 공격을 할 때마다 탄창의 탄이 하나씩 줄어들게 되고, 탄을 모두 소진하면 장전하는 동안은 기본 공격을 할 수 없다. 탄창 시스템은 Horizontal Layout Group을 이용하여 구현하였다.

**5. Combo 시스템**
* Timer를 2개 사용하여 하나는 콤보 유지 시간, 다른 하나는 화면상에 나타나는 콤보 Text 유지 시간이다. 콤보 유지 시간은 0초에서 시작하여 3초가 되면 콤보가 초기화되는 방식이다. 3초가 되기 전에 Enemy에게 공격을 성공하면 콤보 value가 증가하며 콤보 유지 시간은 다시 0초로 초기화된다. 콤보 value는 콤보 유지 시간이 3초에 도달하지 않는 한 계속 증가한다. 콤보 Text 유지 시간은 플레이 화면 상에 나타나는 Combo 1, Combo 2 ... 등 해당 콤보 Text가 나타나는 시간이다. 이는 0.35초로 설정하여 화면상에 너무 오래 떠있지도 않고, 너무 빨리 사라지지도 않도록 설계하였다. 콤보 Text는 Animation으로 크기를 조절하여 빠르게 Text의 size가 커진 후 사라지게끔 하였다. 또한 콤보 Text는 value가 증가할수록 Text의 색상이 변하게 된다.

**6. 강화 시스템**
* 강화 시스템은 메이플 스토리의 장비 아이템 강화를 레퍼런스로 하여 기획했던 시스템이다. 이에 따라 확률성으로 설계하여 보유하고 있는 재화를 소모하여 성공 시 강화 수치가 올라가고 실패 시 강화 수치가 내려가게 된다. 강화 수치가 5 이하일 때에는 실패해도 보유 재화만 소모되고 강화 수치는 하락하지 않는다. 강화 수치가 6 이상일 때에는 강화 실패 시 강화 수치가 1씩 내려가게 된다. 강화 성공 확률은 100%부터 시작하여 강화 수치가 증가할수록 5%씩 낮아지게 된다. 시스템 구현 난이도는 낮았지만, 강화 시스템 UI의 지속적인 개선 작업을 꾸준히 해야했고, 공격력, 방어력, 기술력, 관통력 4가지 파라미터를 각각 최대 20강까지 설계하고, 강화 수치당 소모되는 재화도 각 파라미터당 다르게 설정하여 코드 길이는 가장 길었다.

**7. 메시지 UI 타이핑 시스템**
* IEnumerator와 for 문 내에서 Text의 .substring()을 이용하여 0.05초마다 문자가 타이핑되게 구현하였다. 

**8. 테라포밍 시스템**
* 최종 엔딩 Scene으로 향하기 전 마지막 시퀀스로, 모든 스테이지를 클리어하고 보유중인 재화로 외계행성의 데이터를 지구에 맞게 설정하여 테라포밍을 시행하는 과정이다. 재화를 소모하여 6가지 테라포밍 데이터를 지구에 맞게 설정할 수 있으며, 플레이 화면의 왼쪽에 나타난 육각형 UI를 클릭 시 조금씩 Rotate하여 다른 테라포밍 데이터 현황을 보여준다. 모든 데이터를 지구에 맞게 설정하게 되면 육각형 UI가 중심으로 이동하게 되고 사이즈가 증가하게 되면서 엔딩 Scene으로 넘어갈 수 있다. 

**9.Stage Boss**
1. Stage1 Boss
* 먼저 Stage1의 Boss로는 외계행성을 공전하는 큰 모선이다. 행성을 공전하는 것은 .RotateAround()로 구현하였고, 모선의 후방에 외계 함선 Enemy가 지속적으로 소환된다. 소환된 Enemy는 Enemy AI 시스템을 통해 Player에게로 향하고, 일정 거리 도달 시 공격을 하게 된다. 이 밖에 모선 자체에서도 플레이어 방향을 향해 공격을 한다. 
2. Stage2 Boss
* Stage2의 Boss는 외계행성의 내부에 존재하는 커맨드센터이다. 이 Boss는 계속 회전하면서 Bullet을 발사하고, 일정 시간마다 모든 방향으로 불을 내뿜는 패턴이 존재한다. 이 패턴이 등장하기 전에 특정 위치에서 곧 발사된다는 것을 알리는 가이드가 나타나게 되는데, 이는 Animation으로 구현하여 점차 Size가 커지게 된 후 사라진다. 그리고 커맨드 센터의 위 아래로 Enemy가 지속적으로 등장하게 되고 육지에 터렛도 존재하여 Stage1에 비해 난이도가 비약적으로 상승한 것을 체감할 수 있다.


---
사운드 출처 : https://freesound.org/
