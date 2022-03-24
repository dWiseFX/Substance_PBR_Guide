THE PBR GUIDE - PART 1
===

THE PBR GUIDE BY ALLEGORITHMIC - PART 1
---

# Light and Matter : The theory of Physically-Based Rendering and Shading
## TABLE OF CONTENTS
* 광선(Light Rays)
* 흡수(Absorption)와 산란(Scattering) - Transparency and Translucency
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
 빛의 광선 모델은 광선이 공기와 같은 균질한 투명 매질(homogeneous transparent medium)[^homogeneous_medium]에서 직선의 궤적을 갖는다는 것을 나타낸다. 광선 모델은 또한 광선이 불투명한 물체(opaque objects)와 같은 서페이스를 만날 때 또는 공기에서 물을 지나는 것과 같이 다른 매질을 통과할 때 예측 가능한 방식으로 행동한다고 말한다.

 이를 통해 광선이 시작지점에서 열과 같은 다른 형태의 에너지로 변하는 다른 지점으로 이동할 때 광선의 경로를 시각화할 수 있다.

 서페이스에 닿는 광선을 입사광선이라고 하고 그것이 닿는 각도를 입사각이라고 한다.

[^homogeneous_medium]: homogeneous medium


<!--
![figure01](/img/figure01.png)
-->
<img src="/img/figure01.png" width="450px" height="300px" title="10px" alt="figure01"></img><br/>
-->







































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
