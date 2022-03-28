THE PBR GUIDE - PART 1
===

THE PBR GUIDE BY ALLEGORITHMIC - PART 1
---


# Light and Matter : The theory of Physically-Based Rendering and Shading
## TABLE OF CONTENTS
* 광선(Light Rays)
* 흡수(Absorption)와 산란(Scattering)[^scattering_diffuse_difference] - 투명도(Transparency)와 반투명(Translucency)
* 확산(Diffuse)과 정반사(Specular Reflection) - 미세면 이론(Microfacet Theory)
* 색상(Color)
* 양방향 반사도 분포 함수(BRDF)
* 에너지 보존(Energy Conservation)
* 프레넬 효과(Fresnel Effect) - F0(Fresnel Reflectance at 0 Degrees)
* 도체(Conductors)와 절연체(Insulators) - 금속과 비금속(Metals and Non Metal)
* 선형 공간 렌더링(Linear Space Rendering)
* PBR의 주요 특성(Key characteristics of PBR)


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

[^homogeneous_medium]: homogeneous medium : 균질 매질. 설탕물, 표준대기상태의 공기로 채워진 용기와 같이 혼합물 전체가 같은 조성을 가지는 매질.(장소와 관계가 없는 매질.)

[^매질]: 매질(medium) : 힘이나 파동 등의 물리현상을 전달하는 작용을 하는 물질 또는 공간. 장(선형/비선형), 공간(균질/비균질), 방향성(등방성/<이방성, 비등방성>), 탄성(탄성/비탄성) 의존성에 따라 성질이 구분된다. 매질의 물리적 성질에 따라 서로 다른 매질의 경계면에서는 반사(Reflection), 굴절(Refraction), 산란(Scattering), 투과(Transmission), 흡수(Absorption) 등의 현상이 일어난다. 

[^conversion]: 서페이스에서는 빛의 반사(reflection) 뿐만 아니라 흡수(Absorption, absorb)도 일어난다. 빛 에너지는 몸체의 분자에 흡수되어 운동 에너지로 변환되고, 분자의 움직임이 증가하면 주변으로 복사되는 열이 발생한다.

[^albedo]: 알베도(albedo) : 이러한 흡수 원리는 일상 생활으로 옮겨볼 수 있다. 검은색 티셔츠는 흰색 티셔츠보다 햇빛을 더 많이 흡수한다. 이것이 우리가 여름에 검은색 셔츠를 입고 땀을 더 많이 흘리는 이유이다. 몸체의 알베도는 흡수된 햇빛의 비율에 영향을주어 다양한 스펙트럼 범위에서 재질의 반사 정도를 측정한다 . 100%의 알베도는 흡수가 일어나지 않음을 나타낸다. 따라서 0%의 알베도는 반사가 없음을 나타낸다. 테이블 참조.

[^scattering_diffuse_difference]: 산란(Scattering)과 확산(Diffuse)의 차이 : 산란은 "어떤 매질에 빛이 입사되어 흡수되고 흡수된 매질의 원자를 파원으로 하여 사방으로 빛이 재방출되는 현상"을 의미한다.
확산은 "어떤 매질에 일정 방향에서 입사한 빛이 반사 또는 투과된 후, 모든 방향으로 퍼지면서 진행하는 현상"이다. 확산은 난반사(Diffuse Reflection)를 일으킨다.


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


## Diffuse and Specular Reflection
 정반사(Specular Reflection)란 광선(Light Rays) 섹션에서 논의한 것처럼 서페이스에서 반사된 빛을 말한다. 광선은 서페이스에서 반사되어 다른 방향으로 이동한다. 그것은 반사의 법칙을 따른다. 즉, 완벽하게 평평한 표면에서 반사각은 입사각과 같다. 그러나 대부분의 표면은 불규칙하고 반사 방향은 표면 거칠기(surface roughness)에 따라 무작위로 달라진다. 이 경우 빛의 방향이 바뀌지만 빛의 강도는 일정하게 유지된다.

 더 거친 서페이스는 더 크고 더 흐릿하게 보이는 하이라이트를 가질 것이다. 매끄러운 표면은 정반사의 초점을 유지하고 적절한 각도에서 볼 때 더 밝거나 강렬하게 보인다. 그러나 두 경우 모두 동일한 총 광량이 반사된다(Figure 04).

<p align="center">
  <img src="/img/figure04.jpg" alt="figure04" width="75%" height="75%" /> 
  <p align="center">
  Figure 04: 반사 방향은 표면 거칠기에 따라 무작위로 달라진다.
  </p>
</p>

 굴절(Refraction)은 광선 방향의 변화이다. 빛이 한 매질에서 다른 매질로 이동할 때 속도와 방향이 바뀐다. 굴절률 또는 IOR(Index Of Refraction)은 광선이 진행하는 방향의 변화를 설명하는 광학 측정이다. 기본적으로 IOR 값은 광선이 한 매체를 통해 다른 매체로 통과할 때 구부러지는 정도를 결정하는 데 사용된다. 예를 들어, 물의 IOR은 1.33인 반면 판유리의 IOR은 1.52이다. Figure 05에서 물 한 컵에 담긴 빨대의 렌더링을 볼 수 있다. 빨대는 빛이 다른 매질(공기, 물, 유리)을 통과할 때 굴절로 인해 구부러진 것처럼 보인다.

<p align="center">
  <img src="/img/figure05.jpg" alt="figure05" width="75%" height="75%" /> 
  <p align="center">
  Figure 05: 빨대가 굴절로 인해 구부러진 것처럼 보인다.
  </p>
</p>

 확산 반사(Diffuse Reflection)는 굴절된 빛이다. 광선은 한 매질에서 다른 매질로 전달된다. 광선이 오브젝트에 들어간다고 할 때, 빛은 이 오브젝트 내에서 여러 번 산란된다. 그것은 마침내 물체 밖으로 다시 굴절되어 처음에 들어갔던 거의 같은 지점에서 원래의 매질로 되돌아간다(Figure 06).

 확산 물질은 흡수성이다. 굴절된 빛이 그러한 물질에서 너무 오래 이동하면 완전히 흡수될 수 있다. 빛이 이 물질을 빠져나가면 진입 지점에서 아주 작은 거리만 이동했을 가능성이 크다.

 따라서 들어가는 지점과 나오는 지점간의 거리는 무시할 수 있다. 전통적인 셰이딩에서 확산 반사에 사용되는 램버트(Lambertian) 모델은 서페이스 거칠기를 고려하지 않는다. 그러나 오렌-나야르(Oren-Nayar) 모델과 같은 다른 확산 반사 모델은 이러한 거칠기를 반영한다.

 높은 산란과 낮은 흡수(high scattering and low absorption)를 모두 갖는 재료를 참여 매질(participating media) 또는 반투명 물질(translucent materials)이라고 한다. 예를 들어 연기, 우유, 피부, 옥 및 대리석이 있다. 광선의 들어오고 나가는 지점 사이의 차이가 더 이상 무시할 수 없는 것으로 간주되는 피하산란(subsurface scattering)의 추가 모델링으로 우유, 피부, 옥 및 대리석과 같은 렌더링이 가능할 수 있다. 연기 또는 안개와 같이 매우 다양하고 산란 및 흡수가 매우 낮은 매질을 정확하게 렌더링하려면 몬테카를로 시뮬레이션(Monte Carlo simulations)[^Monte]과 같은 훨씬 더 느린 방법이 요구될 수 있다.

<p align="center">
  <img src="/img/figure06.png" alt="figure06" width="75%" height="75%" /> 
  <p align="center">
  Figure 06: 한 매질에서 다른 매질로 이동하는 광선이 물체 내부에서 산란
  </p>
</p>

[^Monte]: 몬테카를로 시뮬레이션(Monte Carlo simulations) : 반복되는 랜덤 샘플링을 사용하여 발생하는 결과 범위의 가능성을 얻을 수 있는 계산 알고리즘의 한 유형. 몬테카를로 메서드 또는 다중 확률 시뮬레이션이라고도 하는 몬테카를로 시뮬레이션은 불확실한 사건의 가능한 결과를 추정하는 데 사용되는 수학적 기법이다.

#### Microfacet Theory
 이론적으로 확산 반사(diffuse)와 정반사(specular reflection) 모두 광선이 매질과 교차하는 표면의 불규칙성(surface irregularities)에 따라 달라진다. 그러나 실제로는 난반사(diffuse reflection)에 대한 거칠기의 영향은 물질 내부에서 발생하는 산란 때문에 덜 가시적이다. 결과적으로 광선이 반사되어 나가는 방향은 표면 거칠기와 입사 방향에 상당히 독립적이다.[^diffuse_ref] 확산 반사에 대한 가장 일반적인 모델(Lambertian)은 거칠기를 완전히 무시한다.

 이 가이드에서는 이러한 표면 불규칙성을 표면 거칠기(surface roughness)[^roughness]라고 한다. 표면 불규칙성은 사용 중인 PBR 워크플로우에 따라 거칠기, 부드러움, 광택 또는 미세 표면(roughness, smoothness, glossiness or micro-surface)을 비롯한 여러 다른 이름을 가질 수 있다. 이 모든 용어는 서브 텍셀[^texel](sub-texel)의 기하학적 세부사항인 서페이스의 동일한 측면(surface irregularities)을 설명한다.

 이러한 표면 불규칙성은 사용 중인 워크플로에 따라 거칠기(roughness) 또는 광택맵(glossiness map)에서 작성된다. 물리 기반 BRDF(physically-based BRDF)[^BRDF]는 표면이 미세면(microfacet)이라고 하는 다양한 방향의 소규모 평면 세부 표면(small-scaled planar detail surfaces of varying orientation)으로 구성되어 있다고 가정하는 미세면 이론(microfacet theory)을 기반으로 한다. 이 작은 평면 각각은 법선(normal)을 기반으로 한 방향(single direction)을 향해 빛을 반사한다(Figure 07).

 표면 법선(surface normal)이 조명 방향과 보기 방향(light direction and view direction) 사이의 정확히 중간 방향인 미세면은 가시광선을 반사한다. 그러나 미세표면 노말과 하프 노말이 동일한 경우 그림 07에서와 같이 일부 미세면이 그림자(빛 방향, light direction) 또는 마스킹(보기 방향, view direction)에 의해 차단되므로 모든 미세면이 기여하는 것은 아니다.

 미세한 수준(microscopic level)의 표면 불규칙성은 광 확산을 유발한다. 예를 들어, 흐릿한 반사는 산란된 광선으로 인해 발생한다. 광선은 평행하게 반사되지 않으므로 정반사를 흐리게 인식한다(Figure 08).

<p align="center">
  <img src="/img/figure07.png" alt="figure07" width="75%" height="75%" /> 
  <p align="center">
  Figure 07: 미세면 이론에 기반한 Physically-based BRDF
  </p>
</p>
<p align="center">
  <img src="/img/figure08.png" alt="figure08" width="75%" height="75%" /> 
  <p align="center">
  Figure 08: 산란된 광선에 의한 흐릿한 반사
  </p>
</p>

[^diffuse_ref]: 디퓨즈 리플렉션은 물질 내부에서 발생하는 산란으로 인해 표면의 거칠기를 무시 가능한 수준으로 무작위적인 방향으로 반사된다.

[^roughness]: Roughness : 또한 대부분의 대중적인 렌더러에서 표면에 대한 거칠기를 Roughness라는 이름으로 나타낸다. 이때 일반적으로 거칠기가 증가하면 더 많은 확산으로 인해 부드러운 셰입의 반사를 얻을 수 있고, 거칠기가 감소하면 선명하고 날카로운 셰입의 반사를 얻을 수 있다.

[^texel]: Texel : 텍셀, 텍스처 엘리먼트 또는 텍스처 픽셀은 텍스처 맵의 기본 단위이다. 텍스처는 이미지가 픽셀 배열로 표현되는 것처럼 텍스처 공간을 나타내는 텍셀 배열로 표현된다.

[^BRDF]: BRDF : 양방향 반사도 분포 함수(Bidirectional Reflectance Distribution Function)는 빛이 불투명한 표면에서 어떤 방식으로 반사되는지를 정의하는 4차원 함수이다.


## Color

 표면의 가시적인 색상은 광원에서 방출되는 파장에서 비롯된다. 이러한 파장은 물체에 흡수되고, 정반사 및 확산 반사된다. 그 흡수되는 파장 외 나머지 반사 파장을 우리가 색상으로 보는 것이다.

 예를 들어, 사과의 피부는 대부분 붉은 빛을 반사한다. 빨간색 파장만 사과 껍질 외부로 다시 산란되고 나머지 파장은 흡수된다(Figure 09).

 사과는 또한 전기를 전도하지 않는 물질(유전체, dielectrics)[^dielectrics]로서(사과 껍질과 같이) 정반사가 파장에 거의 독립적이기 때문에 광원과 동일한 색상의 밝은 정반사 하이라이트를 갖는다. 이러한 물질의 경우 정반사에 색상이 지정되지 않는다. 이후 섹션에서 다양한 유형의 물질(금속 및 유전체)에 대해 논의할 것이다.

 Substance PBR 셰이더는 GGX 미세면 분포(GGX microfacet distribution)를 사용한다.

<p align="center">
  <img src="/img/figure09.png" alt="figure09" width="75%" height="75%" /> 
  <p align="center">
  Figure 09: 적색 파장이 눈으로 반사된다.
  </p>
</p>

[^dielectrics]: 유전체(dielectric) : 도체와 달리 유전체는 절연체이므로 전하가 통과하지 않는다.

## BRDF

 양방향 반사율 분포 함수(BRDF)는 표면의 반사율 속성을 설명하는 함수이다. 컴퓨터 그래픽에는 다양한 BRDF 모델이 있으며 그 중 일부는 물리적으로 설득력있지 않다. BRDF가 물리적으로 그럴듯해지기 위해서는 에너지를 보존(energy conserving)하고 상호성을 나타내야 한다. 상호성은 BRDF의 결과에 영향을 미치지 않고 들어오는 광선과 나가는 광선이 서로의 반전으로 간주될 수 있다는 헬름홀츠 상호성 원칙(Helmholtz Reciprocity Principle)를 나타낸다.

 섭스턴스의 PBR 쉐이더에서 사용하는 BRDF는 Disney의 principled reflectance model을 기반으로 한다. 이 모델은 GGX 미세면 분포(GGX microfacet distribution)를 기반으로 한다. GGX는 반사 분포 측면에서 더 나은 솔루션 중 하나를 제공한다. 하이라이트에서 더 짧은 피크와 폴오프에서 더 긴 꼬리를 사용하여 더 사실적으로 보인다(Figure 10).

<p align="center">
  <img src="/img/figure10.png" alt="figure10" width="75%" height="75%" /> 
  <p align="center">
  Figure 10: GGX vs Blinn specular distribution - GGX는 specular distributio 측면에서 더 나은 솔루션 중 하나를 제공한다.
  </p>
</p>


## Energy Conservation

 에너지 보존은 물리 기반 렌더링 솔루션에서 중요한 역할을 한다.[^energy_conservation] 이 원리는 표면에서 다시 방출되는 빛(반사 및 산란, reflected and scattered back)의 총량이 수신된 총량보다 적음을 나타낸다. 다시 말해, 표면에서 반사된 빛은 표면에 닿기 전보다 더 강렬하지 않다. 예술가로서 우리는 에너지 보존을 통제하는 것에 대해 걱정할 필요가 없다. 이것은 PBR의 장점 중 하나로, 에너지 보존은 항상 셰이더에 의해 시행된다. 이것은 물리 기반 모델의 일부이며 우리가 물리학보다 예술에 집중할 수 있도록 해준다.

[^energy_conservation]: 에너지 보존 법칙(law of energy conservation) : 물리적 현상에 따라 한 물체에서 다른 물체로 에너지가 옮겨가거나 물체의 에너지가 다른 종류의 에너지로 변환할 때, 항상 자연계 전체의 에너지의 총량은 일정하게 보존된다는 법칙이다. 예를 들어 물체가 일정 위치에서 지상으로 떨어질 경우 위치에너지가 운동에너지로 변환되면서 속도가 증가하지만, 위치에너지와 운동에너지의 총합은 일정하게 보존된다.


## Fresnel Effect

 프레넬 반사 인자(Fresnel reflection factor) 또한 BRDF의 계수로서 물리 기반 셰이딩에서 중요한 역할을 합니다. 프랑스 물리학자 Augustin-Jean Fresnel이 발견한 프레넬 효과(Fresnel Effect)는 표면에서 반사되는 빛의 양이 그것이 인지되는 시야각(viewing angle)에 따라 달라진다고 말한다. 물 웅덩이를 예로든다면, 물 표면에 수직으로하여 똑바로 아래를 볼 때 바닥까지 볼 수 있다. 이때 수면을 보는 것은 0도(zero degrees) 또는 수직 입사(normal incidence)[^normal_incidence]가 될 것이다(노말은 표면 법선). 수면에 더 평행한 스침 입사(grazing incidence)[^grazing_incidence]에서 물 웅덩이를 보면 수면의 정반사가 더 강해지는 것을 볼 수 있으며, 수면 아래는 전혀 볼 수 없을 수도 있다.

 프레넬은 우리가 전통적인 셰이딩에서 했던 것처럼 PBR에서 제어하는 것이 아니다. 다시 말하지만, 이것은 PBR 셰이더에 의해 처리되는 물리학의 또 다른 측면이다. 스침 입사에서 표면을 볼 때 모든 매끄러운 표면은 90도 입사각에서 거의 100%의 반사체가 된다.

 거친 표면의 경우 반사율은 스침 입사에 가까울 수록 점점 더 정반사화되지만 100% 정반사에 도달하지는 않는다. 여기서 가장 중요한 요소는 "macrosurface"의 법선과 빛이 이루는 각도가 아니라 각 미세면(microfacet)의 법선과 빛이 이루는 각도이다. 광선이 다른 방향으로 분산되기 때문에 반사가 더 부드럽거나 흐리게 보인다. 거시적 수준(macroscopic level)에서 발생하는 것은 집합적인 미세면(collective microfacets)에 대해 관찰할 모든 프레넬 효과의 평균과 다소 유사하다.

[^normal_incidence]: 수직 입사(normal incidence) : 파동이 경계면에 충돌할 때 수직인 경우, 즉 입사각이 0도 일때 수직 입사라고 한다.

[^grazing_incidence]: 스침 입사(grazing incidence) : 수직입사와는 다르게 입사각이 경계면의 법선에 대해 90도에 근접할 때 이러한 입사각을 스침각이라 하는데 이 스침각에서의 입사를 스침 입사라고 한다.

#### F0 (Fresnel Reflectance at 0 Degrees)

 빛이 표면에 직각 또는 수직으로(0도 각도로) 닿으면 해당 빛의 일정 비율이 정반사(specular)로 반사된다. 표면에 대한 굴절률(IOR)을 사용하여 반사되는 양을 도출할 수 있다. 이를 F0(Fresnel 0)라고 한다(Figure 11). 표면으로 굴절되는 빛의 양을 1 ~ F0이라고 한다.

<p align="center">
  <img src="/img/figure11.png" alt="figure11" width="75%" height="75%" /> 
  <p align="center">
  Figure 11: 매끄러운 유전체 표면의 경우 F0에서는 빛의 2-5%, 스침각에서는 100%를 반사한다.
  </p>
</p>

 가장 일반적인 유전체(dielectrics)의 F0 범위는 0.02-0.05(선형 값, linear values)이다. 도체(conductors)의 경우 F0 범위는 0.5-1.0이다. 따라서 표면의 반사율은 아래 방정식과 같이 굴절률에 의해 결정된다(Lagarde 2011).

 <p align="center">
  <img src="/img/figure11_f0.png" alt="figure11_f0" width="75%" height="75%" />
  <p align="center">
  Figure 11_2: n = refractive index(IOR)
  </p>
</p>

 텍스처 제작과 관련하여 관심을 갖는 것은 F0 반사율 값이다. 비금속<유전체/절연체, Non-metals(dielectrics/insulators)>은 그레이스케일 값을 가지며 금속(도체)은 RGB 값을 갖는다. PBR과 관련하여 반사율에 대한 예술적 해석을 통해 Figure 11과 같이 매끄러운 유전체 표면의 경우 F0은 빛의 2%에서 5%를 반사하고 스침 각도(grazing angles)에서 100%를 반사한다고 말할 수 있다.

 유전체(비금속) 반사율 값은 실제로 크게 변하지 않는다.(유전체의 반사율 값이 대부분 크게 차이를 보이지 않음.) 사실, 거칠기에 의해 변경될 때 값의 실제 변화는 알아보기 어려울 수 있다. 그러나 값에는 차이가 있다. Figure 12에서 금속 및 비금속 물질 모두에 대한 F0 범위를 보여주는 차트를 볼 수 있다.

 비금속의 범위는 서로 크게 다르지 않다. 보석은 값이 더 높기 때문에 예외이다. F0는 특히 도체(conductors)나 절연체(insulators)에 관한 것이므로 잠시 후에 논의할 것이다.


## Conductors and Insulators(Metals and Non-Metals)

 PBR용 재질을 만들 때는 금속이나 비금속(metal or non-metal)의 관점에서 생각하는 것이 도움이 된다. 표면이 금속인지 아닌지 스스로에게 물어보고, 금속이라면 한 세트의 가이드라인을 따라야 한다. 그렇지 않은 경우 다른 지침을 따라야한다.

 이것은 일부 재질이 준금속(metalloids, 금속과 비금속의 혼합)과 같이 이러한 범주에 속하지 않을 수 있기 때문에 단순한 접근 방식일 수 있지만, 재질을 만드는 전체 과정에서 금속과 비금속을 구별하는 것은 좋은 접근 방식이며 메탈로이드는 예외이다. 재질에 대한 가이드라인을 설정하려면 먼저 우리가 만들고자 하는 것을 이해해야 한다. PBR을 사용하면 금속(도체, Conductors)[^conductor] [^thermal_conductor] [^electrical_conductor]과 비금속(절연체, Insulators)[^insulators]의 특성을 살펴보고 Figure 12와 같이 이러한 가이드라인 세트를 도출할 수 있다.

<p align="center">
  <img src="/img/figure12.png" alt="figure12" width="75%" height="75%" /> 
  <p align="center">
  Figure 12: 금속 및 비금속 재질 모두에 대한 F0 범위
  </p>
</p>

 굴절된 빛은 흡수되고 금속의 색조(color tint)는 반사된 빛에서 나오므로 맵에서 금속에 확산 색상(diffuse color)을 지정하지 않는다(diffuse = 0).

[^conductor]: 도체(Conductor) : '전기 몇 '열'이 잘 통하는 도전성 물질.

[^thermal_conductor]: 열 전도체(Thermal Conductor) : 열이 잘 전달되는 물질.

[^electrical_conductor]: 전기 전도체(Electrical Conductor) : 전도도가 높아서 전기가 통하기 쉬운 물질.

[^insulators]: 절연체(Insulator) : '전기'나 '열'을 전달하기 어려운 성질을 가지는 물질의 총칭.
전기가 통하기 쉬운 '도체(전기 전도체)'에 비교해서 '부도체'라고도 한다. '절연체'는 '유전체(dielectric)'의 성질도 갖는다.

#### Metals

 금속은 열과 전기의 좋은 도체이다. 전도성 금속의 전기장(electric field)은 0이며 전기장과 자기장으로 이루어진 입사 광파가 표면에 부딪힐 때 그 파동은 부분적으로 반사되고 굴절된 빛은 모두 흡수된다. 연마된 금속의 반사율 값은 약 70-100% 반사 범위로 높은 편이다(Figure 13).

<p align="center">
  <img src="/img/figure13.png" alt="figure13" width="75%" height="75%" /> 
  <p align="center">
  Figure 13: 금속의 반사율 값은 약 70-100% 정반사이다.
  </p>
</p>

 일부 금속은 다른 파장의 빛을 흡수한다. 예를 들어, 금은 가시 스펙트럼의 고주파수 끝에서 청색광을 흡수하므로 노란색으로 보이게된다. 그러나 굴절된 빛은 흡수되기 때문에 금속의 색조는 반사된 빛에서 나온다. 따라서 우리 맵에서는 금속에 확산 색상을 지정하지 않는다(diffuse = 0). 예를 들어, 반사광/광택(specular/gloss) 워크플로우에서 원금속(raw metal)은 디퓨즈 맵에서 검은색으로 설정되고 반사율 값은 반사광 맵(specular map)에서 착색된 색상 값이다. 금속의 경우 반사율 값은 RGB가 되며 착색될 수 있다. 물리 기반 모델 내에서 작업하고 있기 때문에 맵에서 금속 반사율에 대해 실제 측정 값을 사용해야 한다.

 텍스쳐링 측면에서 금속의 또 다른 중요한 점은 부식 경향이다. 이것은 풍화 요소(weathering elements)가 금속의 반사 상태에서 큰 역할을 할 수 있음을 의미한다. 금속이 녹슬면 금속의 반사 상태가 변경된다. 그런 다음 부식된 영역은 Figure 14와 같이 금속성 맵(metallic map)에서 검은색 값으로 표시된 유전체로 처리된다. 2부에서 논의할 것처럼 금속성/거칠기 워크플로우(metallic/roughness workflow)의 셰이더는 유전체의 F0 값을 4% 반사 하도록 하드코딩한다. Figure 14는 하드코딩된 F0 값이 4%인 확산 반사 색상(diffuse reflected color)으로 기본 색상 맵(base color map)의 녹슨 영역을 보여준다.

 또한 도색된 금속은 금속이 아닌 유전체로 취급된다. 페인트는 원금속(raw metal) 위에 레이어 역할을 한다. 페인트가 벗겨진 상태에서 노출된 원금속만 금속으로 처리된다. 금속에 묻은 먼지나 원금속을 흐리게하는 물질도 마찬가지이다.

 이 장의 시작 부분에서 언급했듯이 PBR 재질을 만들 때 재질이 금속인지 여부를 묻는 것이 도움이 된다. 더 정확하게 말하면, 질문에는 금속의 상태에 대한 정보도 포함되어야 한다. 금속이 칠해졌는지, 녹슬었는지, 흙이나 기름과 같은 다른 물질로 덮여 있는지 여부이다. 재질은 원금속이 아닌 경우 유전체로 처리된다. 풍화에 따라 풍화 요소가 금속의 반사 상태에서 역할을 하기 때문에 금속과 비금속이 혼합될 수 있다.

<p align="center">
  <img src="/img/figure14.png" alt="figure14" width="75%" height="75%" /> 
  <p align="center">
  Figure 14: 부식 영역(Corrosive areas)은 F0 값이 4% 반사인 유전체로 처리된다.
  </p>
</p>

#### Non-Metals

 비금속(절연체/유전체, insulators/dielectrics)은 전기 전도성이 좋지 않다. 굴절된 빛이 산란 및/또는 흡수(종종 표면에서 다시 나타남)되므로 금속보다 훨씬 적은 양의 빛을 반사하고 알베도 색상을 갖는다.

 우리는 앞에서 일반적인 유전체의 값이 굴절률에 의해 계산된 F0를 기준으로 약 2-5%라고 말했다. 이 값은 Figure 12에서와 같이 0.017-0.067(40-75 sRGB)의 선형 범위 내에 포함된다. 보석과 같은 일부 비금속 재료를 제외하고 대부분의 유전체는 4%보다 큰 F0 값을 갖지 않는다.

 금속과 마찬가지로 실제 측정값을 사용해야 하지만 투명하지 않은 다른 재료의 경우 굴절률(IOR)을 찾기가 어려울 수 있다. 그러나 가장 일반적인 유전체 재료 사이의 값은 크게 변하지 않으므로 반사율 값에 대해 몇 가지 가이드라인을 사용할 수 있다. 이 가이드의 뒷부분에서 다룰 것이다.

 일반적인 유전체의 값은 IOR에 의해 계산된 F0를 기준으로 약 2-5%이다. Figure 15에서 이 범위를 볼 수 있다.

<p align="center">
  <img src="/img/figure15.png" alt="figure15" width="75%" height="75%" /> 
  <p align="center">
  Figure 15: sRGB에서 선형으로의 변환은 감마 2.2 근사치를 사용하여 수행되었다 - 자세한 내용은 선형 공간 렌더링 섹션을 참조
  </p>
</p>


## Linear Space Rendering

 선형 공간 렌더링은 매우 복잡한 주제이다. 이 가이드에서는 선형 공간 렌더링이 조명 계산을 위한 올바른 계산을 제공한다는 단순한 접근 방식을 취할 것이다. 이는 신뢰할만한 실제 방식으로 표현될 수 있는 빛의 상호작용이 가능한 환경을 만들어준다. 선형 공간 렌더링에 대한 논의를 위해서는 감마 보정의 개념을 도입해야 한다. 디스플레이 및 저장 목적으로 이미지를 인코딩할 때 감마 보정은 대역폭과 비트 할당을 줄이는 최적화 프로세스이다. 이 프로세스는 대략적으로 휘도의 세제곱근을 따르는 인간의 눈의 밝기 인식을 활용한다.

 HVS(Human Visual System)는 밝은 톤보다 어두운 톤의 상대적 차이에 더 민감하다. 이 때문에 감마 보정을 사용하지 않는 것은 HVS가 톤을 구별할 수 없는 톤 영역에 너무 많은 비트가 할당되기 때문에 낭비이다. 

 일반적인 디지털 이미지 생성 프로세스에서 이미지는 디스플레이 장치에 표시하기 위해 sRGB OETF 또는 감마 1 / 2.2[^oetf]와 같은 인코딩 감마 함수를 사용하여 인코딩된다. 그런 다음 디스플레이 장치 회로는 자체 디코딩 감마 함수인 EOTF[^eotf]를 사용하여 이미지를 디코딩한다. 컴퓨터 모니터의 감마 설정은 2.2인 경우가 많다.

 선형 색 공간에는 본질적으로 감마 보정이 없다. 이는 1.0의 유효 감마선에 해당하며, 이는 정확한 선형 계산을 산출한다. 그러나 렌더링된 이미지를 뷰어에게 올바르게 표시하려면 감마 공간으로 인코딩해야 한다.[^gamma_encoding]

 색상 값의 계산과 색상에 대한 연산은 선형 공간에서 수행된다. 이 프로세스는 감마 인코딩된 값을 색상 맵과 색상 선택기를 통해 모니터에서 보는 동안 선택한 색상의 선형 값으로 디코딩한다. 색상 관리 워크플로우에서 이 프로세스에는 일반적으로 선형으로 해석되거나 sRGB OETF 또는 감마 1 / 2.2로 인코딩된 것으로 해석되도록 텍스처 맵에 태그를 지정하는 작업이 포함된다. 그런 다음 선형 공간에서 계산이 수행되고 최종 렌더링된 결과는 sRGB OETF 또는 감마 1 / 2.2로 감마 인코딩된다.

 어떤 맵을 디코딩해야 하는지 고려하는 간단한 방법은 Substance Painter 또는 Substance Designer에서 내보낸 맵이 금속의 색조나 잔디의 녹색과 같이 보이는 색상(확산 반사 색상, diffuse reflected color)을 나타내는 경우 셰이더가 맵을 올바르게 해석하도록 감마 인코딩으로 플래그를 지정해야 한다. PBR 워크플로우에서 감마 인코딩으로 플래그가 지정되는 맵은 기본 색상, 확산, 반사 및 방사(base color, diffuse, specular and emissive)이다. 맵이 표면이 얼마나 거친지(거칠기 맵, roughness map) 또는 재질이 금속인지(금속 맵, metallic map) 같은 데이터를 나타내는 경우 선형으로 플래그가 지정되어야 한다. PBR 워크플로우에서 선형으로 플래그 지정되는 맵은 거칠기, 차폐, 법선(노말), 금속성 및 높이(roughness, ambient occlusion, normal, metallic, and height)이다.

 Substance Designer 및 Substance Painter 내에서 셰이더에 대한 입력의 선형 공간과 감마 공간 간의 변환이 자동으로 처리된다. 렌더링된 뷰포트에서 계산된 결과에 대한 감마 보정도 마찬가지이다. 아티스트로서 우리는 일반적으로 소프트웨어가 기본적으로 처리하므로 Substance 소프트웨어 내에서 선형 계산 및 변환에 대해 걱정할 필요가 없다.

 Substance Integration 플러그인을 통해 Substance 재질을 사용할 때 통합 및 호스트 응용 프로그램의 색상 관리를 통해 출력에 자동으로 선형/감마 공간(linear/gamma space) 플래그가 지정된다. 그러나 프로세스를 이해하는 것이 중요하다. Substance 맵이 Substance 재질이 아닌 내보낸 비트맵으로 사용되는 경우 사용 중인 렌더러에 따라 텍스처를 감마 인코딩하거나 선형으로 수동 플래그를 지정해야 할 수도 있다. 일반적으로 .png, .jpg, .tga 또는 .tif 파일은 감마 인코딩되는 반면 sRGB OETF 및 .exr 파일은 선형이다.

 감마 공간에서 선형 공간으로 변환하는 sRGB 디코딩 기능(EOTF)은 Substance Painter 및 Substance Designer*에서 사용되며 IEC 61966-2-1:1999 표준에 의해 다음과 같이 정의된다.

<p align="center">
  <img src="/img/figure_srgb_linear01.png" alt="figure_srgb_linear01" width="75%" height="75%" /> 
</p>

 *이 글의 작성 시점에서 Substance Designer의 선형에서 rgb 및 rgb에서 선형 노드(inear to rgb and rgb to linear nodes)는 최적화를 위해 이 공식을 사용하지 않는다. 이는 향후 릴리스에서 변경될 수 있다.

 단순화를 위해 이 가이드의 모든 변환에 대해 다음과 같은 단순화된(근사적인) 변환 함수를 대신 사용했다.

<p align="center">
  <img src="/img/figure_srgb_linear02.png" alt="figure_srgb_linear02" width="75%" height="75%" /> 
</p>

***
_a. 기본 색도 좌표(chromaticity coordinates)는 주어진 RGB 색상 공간으로 인코딩할 수 있는 색역(색상 삼각형)을 정의한다._

_b. 화이트포인트(white point)는 주어진 RGB 색상 공간에 대한 흰색을 정의한다._

_c. 전달 함수는 선형 광 성분(3자극 값)과 비선형 RGB 비디오 신호(대부분의 경우 코딩 최적화 및 대역폭 성능) 간의 매핑을 수행한다._
***

[^oetf]: OETF : 감마 인코딩 함수의 기술명은 OETF(Opto-Electronic Transfer Function)이다.

[^eotf]: OETF : 감마 디코딩 함수의 기술명은 EOTF(Electro-Optical Transfer Function)이다.

[^gamma_encoding]: sRGB 색상 공간에서 sRGB OETF를 명확하게 하는 것이 매우 중요하다. OETF는 RGB 색상 공간을 만드는 세 가지 구성 요소 중 하나일 뿐이다.


## Key Characteristics of PBR

 이제 물리학의 기본 이론을 살펴보았으므로 PBR의 몇 가지 주요 특성을 도출할 수 있다.

 1. 에너지 보존. 반사된 광선은 처음 표면에 부딪쳤을 때의 값보다 결코 밝지 않다. 에너지 보존은 셰이더에 의해 처리된다.

 2. 프레넬. BRDF는 셰이더에 의해 처리된다. F0 반사율 값은 가장 일반적인 유전체에 대해 최소한의 변화를 가지며 2-5% 범위에 속한다. 금속에 대한 F0는 70-100% 범위의 높은 값이다.

 3. 정반사의 강도(Specular intensity)는 BRDF, 거칠기 또는 광택 맵(glossiness map) 및 F0 반사율 값을 통해 제어된다.

 4. 조명 계산은 선형 공간에서 처리된다. 기본 색상 또는 확산(base color or diffuse)과 같은 감마 인코딩 값이 있는 모든 맵은 일반적으로 셰이더에 의해 선형으로 변환되지만 게임 엔진이나 렌더러에서 이미지를 가져올 때 적절한 옵션을 선택하여 변환이 제대로 처리되는지 확인해야 할 수도 있다. 거칠기, 광택, 금속성 및 높이와 같은 표면 속성을 설명하는 맵은 선형으로 해석되도록 설정해야 한다.


<!--
test line-----------------

    code block

test line-----------------
-->

## reference
* <https://substance3d.adobe.com/tutorials/courses/the-pbr-guide-part-1>
* <https://www.fis.uni-bonn.de/en/recherchetools/infobox/professionals/what-remote-sensing/reflection-and-absorption>
* <https://knowledge.autodesk.com/support/maya-lt/learn-explore/caas/CloudHelp/cloudhelp/2020/ENU/MayaLT-LightingShading/files/GUID-9158B659-5CA0-413B-8AD6-194E35A10FD8-htm.html>
* <https://www.scienceall.com/%EB%A7%A4%EC%A7%88medium-%E5%AA%92%E8%B3%AA/>
* <http://www.ktword.co.kr/test/view/view.php?m_temp1=6336>
* <https://ko.strephonsays.com/dispersion-and-vs-diffusion-5788>
* <https://optical-engineering.tistory.com/entry/%EB%B9%9B%EC%9D%98-%EC%82%B0%EB%9E%80-Scattering-%EA%B3%BC-%ED%99%95%EC%82%B0-Diffusing%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90>
* <https://www.scienceall.com/%ED%88%AC%EA%B3%BCtransmission/>
* <https://www.ibm.com/kr-ko/cloud/learn/monte-carlo-simulation>
* <https://ko.wikipedia.org/wiki/%EB%AA%AC%ED%85%8C%EC%B9%B4%EB%A5%BC%EB%A1%9C_%EB%B0%A9%EB%B2%95>
* <https://en.wikipedia.org/wiki/Texel_(graphics)>
* <https://en.wikipedia.org/wiki/Bidirectional_reflectance_distribution_function#:~:text=The%20bidirectional%20reflectance%20distribution%20function,and%20in%20computer%20vision%20algorithms.>
* <https://www.scienceall.com/%EC%97%90%EB%84%88%EC%A7%80-%EB%B3%B4%EC%A1%B4-%EB%B2%95%EC%B9%99law-of-energy-conservation-2/>
* <https://glossary.oilfield.slb.com/en/terms/n/normal_incidence#:~:text=1.%20n.%20%5BGeophysics%5D,%2C%20two%2Dway%20traveltime%2C%20wave>
* <https://wikipredia.net/ko/Normal_incidence>
* <https://www.kps.or.kr/>
* <http://www.ktword.co.kr/test/view/view.php?m_temp1=4367>
* <https://ko.wikipedia.org/wiki/%EC%A0%84%EA%B8%B0_%EC%A0%84%EB%8F%84%EC%B2%B4>
* <https://ko.wikipedia.org/wiki/%EC%A0%88%EC%97%B0%EC%B2%B4>
* <https://ko.wikipedia.org/wiki/%EC%A0%88%EC%97%B0%EC%B2%B4>
* <https://handlespixels.wordpress.com/tag/f0-reflectance/>
