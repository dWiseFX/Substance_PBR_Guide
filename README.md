THE PBR GUIDE - PART 1
===

THE PBR GUIDE BY ALLEGORITHMIC - PART 1
---

# Light and Matter : The theory of Physically-Based Rendering and Shading
## TABLE OF CONTENTS
* 광선(Light Rays)
* 흡수(Absorption)와 산란(Scattering)[^scattering_diffuse_difference] - Transparency and Translucency
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

[^homogeneous_medium]: homogeneous medium : 균질 매질. 설탕물, 표준대기상태의 공기로 채워진 용기와 같이 혼합물 전체가 같은 조성을 가지는 매질.(장소와 관계가 없는 매질.)

[^매질]: 매질(medium) : 힘이나 파동 등의 물리현상을 전달하는 작용을 하는 물질 또는 공간. 장(선형/비선형), 공간(균질/비균질), 방향성(등방성/<이방성, 비등방성>), 탄성(탄성/비탄성) 의존성에 따라 성질이 구분된다. 매질의 물리적 성질에 따라 서로 다른 매질의 경계면에서는 반사(Reflection), 굴절(Refraction), 산란(Scattering), 투과(Transmission), 흡수(Absorption) 등의 현상이 일어난다. 

[^conversion]: 서페이스에서는 빛의 반사(reflection) 뿐만 아니라 흡수(Absorption, absorb)도 일어난다. 빛 에너지는 몸체의 분자에 흡수되어 운동 에너지로 변환되고, 분자의 움직임이 증가하면 주변으로 복사되는 열이 발생한다.

[^albedo]: 알베도(albedo) : 이러한 흡수 원리는 일상 생활으로 옮겨볼 수 있다. 검은색 티셔츠는 흰색 티셔츠보다 햇빛을 더 많이 흡수한다. 이것이 우리가 여름에 검은색 셔츠를 입고 땀을 더 많이 흘리는 이유이다. 몸체의 알베도는 흡수된 햇빛의 비율에 영향을주어 다양한 스펙트럼 범위에서 재질의 반사 정도를 측정한다 . 100%의 알베도는 흡수가 일어나지 않음을 나타낸다. 따라서 0%의 알베도는 반사가 없음을 나타낸다. 테이블 참조.

[^scattering_diffuse_difference]: 산란(Scattering)과 확산(Diffuse)의 차이 : 산란은 "어떤 매질에 빛이 입사되어 흡수되고 흡수된 매질의 원자를 파원으로 하여 사방으로 빛이 재방출되는 현상"을 의미한다.
확산은 "어떤 매질에 일정 방향에서 입사한 빛이 반사 또는 투과된 후, 모든 방향으로 퍼지면서 진행하는 현상"이다. 확산은 난반사(Diffuse Reflection)를 일으킨다.

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


























test line-----------------

    code block

test line-----------------

## reference
* <https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-1>
* <https://www.fis.uni-bonn.de/en/recherchetools/infobox/professionals/what-remote-sensing/reflection-and-absorption>
* <https://knowledge.autodesk.com/support/maya-lt/learn-explore/caas/CloudHelp/cloudhelp/2020/ENU/MayaLT-LightingShading/files/GUID-9158B659-5CA0-413B-8AD6-194E35A10FD8-htm.html>
* <https://www.scienceall.com/%EB%A7%A4%EC%A7%88medium-%E5%AA%92%E8%B3%AA/>
* <http://www.ktword.co.kr/test/view/view.php?m_temp1=6336>
* <https://ko.strephonsays.com/dispersion-and-vs-diffusion-5788>
* <https://optical-engineering.tistory.com/entry/%EB%B9%9B%EC%9D%98-%EC%82%B0%EB%9E%80-Scattering-%EA%B3%BC-%ED%99%95%EC%82%B0-Diffusing%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90>
* <https://www.scienceall.com/%ED%88%AC%EA%B3%BCtransmission/>
