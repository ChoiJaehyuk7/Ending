# TERRAFORMING

## YOUTUBE Link
---
lnk-
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
* 플레이어는 게임 시작 시 함선 조종사 역할을 맡게 되며 스미스 대령으로부터 튜토리얼을 진행하며 기본 조작키와 기본 공격, 스킬 사용 방법을 차례대로 배우게 된다. 튜토리얼을 완료하면 곧바로 스테이지 단계로 넘어가 진행하지 않고 지구 내부에서 함선 강화를 진행한 뒤 외계 행성에 존재하는 적들을 제거하라는 임무를 전달받는다. 임무를 전달받은 플레이어는 즉시 함선에 탑승하여 이륙해서 우주정거장으로 향한다. 우주정거장에 도착한 플레이어는 서브 퀘스트를 전달받은 후 워프시스템을 이용하여 스테이지1 '아인스행성'으로 향하고, 포탈을 타게되면 스테이지1을 진행하게 된다. 이와 같이 튜토리얼과 본 스테이지 사이에 강화, 워프, 퀘스트 등 부가적인 요소를 추가하여 단조롭게 느낄수 있는 시퀀스를 흥미롭게 기획해보았다.

* 우주라는 광활한 공간적 배경 때문에 플레이어는 어디로 이동해서 무얼 진행해야 하는지 헷갈려 할 수 있다. 그래서 기획 단계에서 조작튜토리얼, 우주공간, 지구내부, 우주정거장, 스테이지1 까지 BOOL값 조절을 통한 단계적 시퀀스를 설정하였다. 각 시퀀스에 입장하면 메시지 UI를 이용하여 플레이어로 하여금 무엇을 해야하는지 알려준다. 메시지 UI는 무전 통신으로 설계하여 음성도 들어가있으면 좋을 것 같아 각각의 메시지를 사이트에서 녹음하여 설정하였다.

### 기술적 특징
1. DontDestroyOnLoad() 활용
 * 각 Scene 별로 해당 시퀀스 단계에 맞는 메시지가 차례대로 출력되어야해서 첫 튜토리얼 Scene부터 bool값으로 현재 진행 단계를 저장하여 활성화된 bool값일 때 Text가 출력되고 필요에 따라서 진행방향을 표시한 화살표 UI 등이 표시된다. 이는 튜토리얼부터 테라포밍하는 엔딩 단계까지 모두 Bool type으로 설정되어있으며, Scene이 바뀌어도 DontDestroyOnLoad()로 인해 값이 초기화 되지 않는다.
 * 'TerraForming'에는 강화 시스템이 존재한다. 스테이지에 사용되는 플레이어의 함선을 공격력, 방어력, 기술력, 관통력 4가지로 분류하여 각각의 요소를 업그레이드할 수 있는 시스템이다. 강화 value가 높아질수록 함선의 parameter 값 또한 증가해야하고, 이 변경된 value는 다른 Scene에서도 저장되어야 하기 때문에 DontDestroyOnLoad()를 사용하여 현재 강화 수치를 저장하였다.
 * 강화를 하려면 재화를 사용해야한다. 'TerraForming'에는 미네랄 재화, 전기 재화 총 2가지를 사용하여 함선을 강화해야하는데, 이 재화들은 매 초마다 특정한 수치만큼 상승한다. Scene이 변경되어도 재화는 계속해서 저장되어야 하고, 늘어나야하기에 OnDestroyOnLoad()를 사용하였다.

2. Enemy AI 시스템
* 가장 구현하기 어려웠던 부분으로, 기존에 배웠던 AI는 Enemy가 돌아다니는 땅이 있고, 이를 Bake하고 Nav mesh Agent 사용하는 것이었다. 하지만 우주라는 공간적 배경 상 Enemy인 적 외계함선이 딛는 땅이 없기에, Bake할수도 없고 Nav mesh Agent를 사용할 수도 없었다. 그래서 사용한 방법으로는 Player와 Enemy 사이의 거리를 .magnitude로 구별 후, 둘 사이의 거리가 400 이상일 때와 그 이하일 때로 나누었다. 거리가 400이상일 때에는 Quaternion.LookLotation()과 Quaternion.Slerp()를 이용하여 Enemy를 Player방향으로 회전시키고 다가가게 만들었다. 400 이하일 때에는 여전히 Quaternion.LookLotation()과 Quaternion.Slerp()를 이용하여 Player방향을 바라보지만 다가가지 않고 Instatiate()를 이용하여 플레이어를 공격하도록 설정하였다. 완성된 EnemyAI 코드 길이는 그리 길지 않지만 생각했던 의도에 맞게 적용시키기까지 수많은 시행착오가 있었다.

3. Player Skill 시스템
* Player는 미사일, 스텔스, 회복, 궁극기 스킬을 사용할 수 있다. 미사일은 Enemy에게 더 강력한 데미지를 입힐 수 있고, 스텔스 스킬을 사용하면 적에게 감지되지 않아 Enemy가 일정 시간동안 공격을 하지 않는다. 스텔스 기능은 스킬 사용시 bool값을 true로 설정하고, Enemy의 공격을 다룬 스크립트에서는 이 bool값이 true면 공격을 하지않고, false면 공격을 할 수 있도록 하였다. 일정 시간동안 이 bool 값이 true로 변경되며, 이 스킬 지속 시간은 HP UI 상단에 표시되어 남은 지속 시간을 확인할 수 있다. 회복 시스템은 HP를 20% 상승시키는 것으로 설계하였다. 궁극기 시스템은 적에게 기본 공격과 미사일 공격을 행할 시 조금씩 게이지가 차게 되는데, 이 게이지가 100%에 도달하게 되면 궁극기를 사용할 수 있다. 궁극기는 사용 시 0%에 도달할 때까지 매우 많은 bullet을 발사하여 스테이지를 클리어하는 데에 있어 핵심적인 역할을 한다. 
