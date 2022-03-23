THE PBR GUIDE - PART 1
===

THE PBR GUIDE BY ALLEGORITHMIC - PART 1
---

# Light and Matter : The theory of Physically-Based Rendering and Shading
## TABLE OF CONTENTS
* Light Rays
* Absorption and Scattering - Transparency and Translucency
> 반사와 흡수(Reflection and Absorption)의 관계에서
반사는 무언가를 되돌려놓는 것을 의미하고, 흡수는 빛에너지를..
* Diffuse and Specular Reflection - Microfacet Theory
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

















test line-----------------

    code block

test line-----------------

## reference
* <https://www.fis.uni-bonn.de/en/recherchetools/infobox/professionals/what-remote-sensing/reflection-and-absorption>
