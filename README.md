THE PBR GUIDE - PART 1
===

THE PBR GUIDE BY ALLEGORITHMIC - PART 1
---


# Light and Matter : The theory of Physically-Based Rendering and Shading
## TABLE OF CONTENTS
* 광선(Light Rays)
* 흡수(Absorption)와 산란(Scattering)[^scattering_diffuse_difference] - 투명도(Transparency)와 반투명(Translucency)
* 확산(Diffuse)과 Specular Reflection - Microfacet Theory
* Color
* BRDF
* Energy Conservation
* Fresnel Effect - F0 (Fresnel Reflectance at 0 Degrees)
* Conductors and Insulators - Metals and Non Metal
* Linear Space Rendering
* Key characteristics of PBR


# THE THEORY OF PHYSICALLY-BASED RENDERING AND SHADING
 빛은 파동과 입자의 속성을 모두 나타낼 수 있기 때문에 복잡한 현상이다. 결과적으로 그런 현상을 설명하기 위해 다양한 모델이 만들어졌다.

 텍스처 아티스트로서 우리는 빛과 물질의 상호 작용을 설명하는 빛의 광선 모델(ray model of light)에 관심이 있다. 우리의 임무는 서페이스를 설명하는 텍스쳐를 만드는 것이기 때문에 광선이 서페이스 물질과 상호 작용하는 방식을 이해하는 것이 중요하다. 우리가 만든 텍스쳐와 재질은(textures and materials) 가상 세계에서 빛과 상호 작용한다. 빛이 어떻게 작용하는지 더 많이 이해할수록 텍스처가 더 좋아 보일 것 이다.

 이 가이드에서는 물리 기반 렌더링(PBR) 모델의 물리학에 대한 이론을 논의한다. 우리는 광선의 행동양식을 조사하는 것으로 시작하여 PBR의 주요 특성을 정의하는 작업을 할 것이다.


## Light Rays
 빛의 광선 모델은 광선이 공기와 같은 균질한 투명 매질(homogeneous transparent medium)[^homogeneous_medium]에서 직선의 궤적을 갖는다는 것을 나타낸다. 광선 모델은 또한 광선이 불투명한 물체(opaque objects)와 같은 서페이스를 만날 때 또는 공기에서 물을 지나는 것과 같이 다른 매질[^매질]을 통과할 때 예측 가능한 방식으로 행동한다고 말한다.

 이를 통해 광선이 시작지점에서 열과 같은 다른 형태의 에너지로 변하는[^conversion] [^albedo] 다른 지점으로 이동할 때 광선의 경로를 시각화할 수 있다.

 |albedo table||
 |----|----|
 |Material|Albedo|
 |snow|80-90%|
 |cloud|60-90%|
 |desert|30%|
 |meadow|20%|

 서페이스에 닿는 광선을 입사광선이라고 하고 그것이 닿는 각도를 입사각이라고 한다(Figure01).

<!--
이미지 기본형
![figure01](/img/figure01.png)

가운데 정렬하기(캡션 포함)
<img> 태그로 이미지를 첨부하고, 이미지 태그를 p태그로 감싼다.
-->
<p align="center">
  <img src="/img/figure01.png" alt="figure01" width="75%" height="75%" /> 
  <p align="center">
  Figure 01: 입사각(Angle of incidence), 입사광선과 반사광선(incident and reflected rays)
  </p>
</p>

 광선은 두 매질 사이의 평면 경계면에 입사한다. 광선이 서페이스에 닿으면 다음 이벤트 중 하나 또는 모두가 발생할 수 있다.

 1. 광선은 서페이스에서 반사되어 다른 방향으로 이동한다. 이때 광선은 "반사각은 입사각(반사광)과 같다"는 반사의 법칙을 따른다.

 2. 광선은 직선(굴절광)의 궤적으로 한 매질에서 다른 매질로 통과한다.

 이 지점에서 광선은 반사와 굴절(reflection and refraction)의 두 방향으로 나뉜다. 서페이스에서 광선은 반사되거나 굴절되며 결국 두 매질에 흡수(Absorption)될 수 있다. 그러나 물질의 표면에서는 흡수가 일어나지 않는다.


## Absorption and Scattering (Transparency and Translucency)
 불균일한 매질(inhomogeneous medium)이나 반투명 물질로 이동할 때 빛은 다음과 같이 흡수되거나 산란될 수 있다.

 빛이 흡수되면(absorbed) 다른 형태의 에너지(보통 열)로 바뀌면서 빛의 강도가 감소한다. 파장을 근거로하여 흡수되는 빛의 양에 따라 색이 변하지만 광선의 방향은 변하지 않는다.

 빛이 산란되면(scattered) 광선 방향이 무작위로 바뀌고 편차의 정도는 재질에 따라 다르다. 산란은 빛의 방향을 무작위로 지정하지만 강도는 변화시키지 않는다. 신체부위중 귀는 이 현상의 좋은 예이다. 귀가 얇아서(흡수율이 낮음) Figure 02와 같이 귀 뒤쪽에서 산란광이 방출되는 것을 볼 수 있다.

 산란이 없고 흡수가 낮으면 광선이 표면을 직접 통과할 수 있다. 이것은 유리의 경우이다. 예를 들어 깨끗한 수영장에서 수영하고 있다고 할때, 사람은 눈을 뜨고 맑은 물을 통해 먼 거리를 볼 수 있다. 그러나 같은 수영장이 상대적으로 더러우면 먼지 입자가 빛을 산란시켜 물의 투명도를 낮추고 결과적으로 볼 수 있는 거리가 줄어든다.

 그러한 매질/물질(medium/material)에서 더 많은 빛이 이동할수록 더 많이 흡수 및/또는 산란된다. 따라서 물체의 두께는 빛이 얼마나 흡수되거나 산란되는지에 큰 역할을 한다. 두께 맵(thickness map)은 Figure 03과 같이 셰이더에 오브젝트의 두께를 설명하는 데 사용할 수 있다.

<p align="center">
  <img src="/img/figure02.jpg" alt="figure02" width="75%" height="75%" /> 
  <p align="center">
  Figure 02: 귀 뒤쪽으로부터 방사되는 산란광
  </p>
</p>

<p align="center">
  <img src="/img/figure03.png" alt="figure03" width="75%" height="75%" /> 
  <p align="center">
  Figure 03: 섭스턴스 페인터 내에서 서페이스 아래 산란과 함께 사용되는 두께 맵
  </p>
</p>

[^homogeneous_medium]: homogeneous medium : 균질 매질. 설탕물, 표준대기상태의 공기로 채워진 용기와 같이 혼합물 전체가 같은 조성을 가지는 매질.(장소와 관계가 없는 매질.)

[^매질]: 매질(medium) : 힘이나 파동 등의 물리현상을 전달하는 작용을 하는 물질 또는 공간. 장(선형/비선형), 공간(균질/비균질), 방향성(등방성/<이방성, 비등방성>), 탄성(탄성/비탄성) 의존성에 따라 성질이 구분된다. 매질의 물리적 성질에 따라 서로 다른 매질의 경계면에서는 반사(Reflection), 굴절(Refraction), 산란(Scattering), 투과(Transmission), 흡수(Absorption) 등의 현상이 일어난다. 

[^conversion]: 서페이스에서는 빛의 반사(reflection) 뿐만 아니라 흡수(Absorption, absorb)도 일어난다. 빛 에너지는 몸체의 분자에 흡수되어 운동 에너지로 변환되고, 분자의 움직임이 증가하면 주변으로 복사되는 열이 발생한다.

[^albedo]: 알베도(albedo) : 이러한 흡수 원리는 일상 생활으로 옮겨볼 수 있다. 검은색 티셔츠는 흰색 티셔츠보다 햇빛을 더 많이 흡수한다. 이것이 우리가 여름에 검은색 셔츠를 입고 땀을 더 많이 흘리는 이유이다. 몸체의 알베도는 흡수된 햇빛의 비율에 영향을주어 다양한 스펙트럼 범위에서 재질의 반사 정도를 측정한다 . 100%의 알베도는 흡수가 일어나지 않음을 나타낸다. 따라서 0%의 알베도는 반사가 없음을 나타낸다. 테이블 참조.

[^scattering_diffuse_difference]: 산란(Scattering)과 확산(Diffuse)의 차이 : 산란은 "어떤 매질에 빛이 입사되어 흡수되고 흡수된 매질의 원자를 파원으로 하여 사방으로 빛이 재방출되는 현상"을 의미한다.
확산은 "어떤 매질에 일정 방향에서 입사한 빛이 반사 또는 투과된 후, 모든 방향으로 퍼지면서 진행하는 현상"이다. 확산은 난반사(Diffuse Reflection)를 일으킨다.












































test line-----------------

    code block

test line-----------------

## reference
* <https://www.fis.uni-bonn.de/en/recherchetools/infobox/professionals/what-remote-sensing/reflection-and-absorption>
* <https://knowledge.autodesk.com/support/maya-lt/learn-explore/caas/CloudHelp/cloudhelp/2020/ENU/MayaLT-LightingShading/files/GUID-9158B659-5CA0-413B-8AD6-194E35A10FD8-htm.html>
* <https://www.scienceall.com/%EB%A7%A4%EC%A7%88medium-%E5%AA%92%E8%B3%AA/>
* <http://www.ktword.co.kr/test/view/view.php?m_temp1=6336>
* <https://ko.strephonsays.com/dispersion-and-vs-diffusion-5788>
* <https://optical-engineering.tistory.com/entry/%EB%B9%9B%EC%9D%98-%EC%82%B0%EB%9E%80-Scattering-%EA%B3%BC-%ED%99%95%EC%82%B0-Diffusing%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90>
* <https://www.scienceall.com/%ED%88%AC%EA%B3%BCtransmission/>
