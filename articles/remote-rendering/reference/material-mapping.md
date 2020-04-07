---
title: Mapowanie materiałów dla formatów modeli
description: Zawiera opis domyślnej konwersji z formatów źródła modelu na materiał PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680390"
---
# <a name="material-mapping-for-model-formats"></a>Mapowanie materiałów dla formatów modeli

Gdy zasób źródłowy jest [konwertowany jako model,](../how-tos/conversion/model-conversion.md)konwerter tworzy [materiały](../concepts/materials.md) dla każdej [siatki](../concepts/meshes.md). Sposób tworzenia materiałów można [zastąpić](../how-tos/conversion/override-materials.md). Jednak domyślnie konwersja utworzy [materiały PBR](../overview/features/pbr-materials.md). Ponieważ każdy format pliku źródłowego, takich jak FBX, używa własnych konwencji do definiowania materiałów, konwencje te muszą być mapowane do parametrów materiału PBR renderowania zdalnego platformy Azure. 

W tym artykule wymieniono dokładne mapowania używane do konwersji materiałów z zasobów źródłowych na materiały środowiska wykonawczego.

## <a name="gltf"></a>gltf

Prawie wszystko ze specyfikacji glTF 2.0 jest obsługiwane w usłudze Azure Remote Rendering, z wyjątkiem *EmissiveFactor* i *EmissiveTexture*.

W poniższej tabeli przedstawiono mapowanie:

| gltf | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture (Lakier bazowy)  |   albedoMap (mapa albedo)                |
|   metallicFactor (MetallicFactor)    |   metalizm                |
|   metallicTexture (MetalicznyTektura)   |   metalnessMap             |
|   chropowatośćZeczystko   |   Chropowatości                |
|   chropowatośćTexura  |   chropowatośćMapa             |
|   niedrożnośćFactor   |   Okluzji                |
|   okluzjaTextura  |   okluzjaMapa             |
|   normalTexture (normalTexture)     |   normalnyMapa                |
|   alfaCutoff (alfaCutoff)       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode.MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode.BLEND   |   isTransparent = prawda     |
|   doubleSided (dwustronny)       |   isDoubleSided ( IsDoubleSided )            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Każda tekstura w glTF może mieć `texCoord` wartość, która jest również obsługiwana w materiałach renderowania zdalnego platformy Azure.

### <a name="embedded-textures"></a>Osadzone tekstury

Obsługiwane są tekstury osadzone w * \** plikach .bin lub * \*.glb.*

### <a name="supported-gltf-extension"></a>Obsługiwane rozszerzenie glTF

Ponadto do podstawowego zestawu funkcji zdalne renderowanie platformy Azure obsługuje następujące rozszerzenia glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Odpowiada [materiałom kolorowym](../overview/features/color-materials.md). W przypadku materiałów *emysyjnych* zaleca się użycie tego rozszerzenia.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Zamiast tekstur metalicznych chropowatości, można zapewnić rozproszone-specular-błyszczące tekstury. Implementacja renderowania zdalnego platformy Azure bezpośrednio następuje formuły konwersji z rozszerzenia.

## <a name="fbx"></a>Fbx

Format FBX jest zamknięty, a materiały FBX nie są ogólnie kompatybilne z materiałami PBR. FBX używa złożonego opisu powierzchni z wieloma unikatowymi parametrami i właściwościami, a **nie wszystkie z nich są używane przez potok zdalnego renderowania platformy Azure.**

> [!IMPORTANT]
> Potok konwersji modelu zdalnego renderowania platformy Azure obsługuje tylko **FBX 2011 i nowsze**.

Format FBX definiuje konserwatywne podejście do materiałów, istnieją tylko dwa typy w oficjalnej specyfikacji FBX:

* *Lambert* - Nie powszechnie używane od dłuższego czasu już, ale nadal jest obsługiwany przez konwersję do Phong w czasie konwersji.
* *Phong* - Prawie wszystkie materiały i większość narzędzi zawartości używać tego typu.

Model Phong jest dokładniejszy i jest używany jako *jedyny* model dla materiałów FBX. Poniżej będzie on określany jako *MATERIAŁ FBX*.

> Maya używa dwóch niestandardowych rozszerzeń dla FBX, definiując niestandardowe właściwości dla typów PBR i Stingray materiału. Te szczegóły nie są zawarte w specyfikacji FBX, więc nie jest obsługiwany przez usługi Azure Remote Rendering obecnie.

FBX Materiały używają koncepcji Diffuse-Specular-SpecularLevel, więc aby przekonwertować z rozproszonej tekstury na mapę albedo, musimy obliczyć inne parametry, aby odjąć je od rozproszonego.

> Wszystkie kolory i tekstury w FBX znajdują się w przestrzeni sRGB (znanej również jako przestrzeń Gamma), ale zdalne renderowanie platformy Azure działa z przestrzenią liniową podczas wizualizacji, a na końcu ramki konwertuje wszystko z powrotem na przestrzeń sRGB. Potok zasobów renderowania zdalnego platformy Azure konwertuje wszystko na przestrzeń liniową, aby wysłać go jako przygotowane dane do modułu renderowania.

W tej tabeli pokazano, jak tekstury są mapowane z materiałów FBX do materiałów zdalnego renderowania platformy Azure. Niektóre z nich nie są bezpośrednio używane, ale w połączeniu z innymi teksturami uczestniczącymi w formułach (na przykład rozproszona tekstura):

| Fbx | Azure Remote Rendering |
|:-----|:----|
| AmbientColor (Kolor otoczenia) | Mapa okluzji   |
| DiffuseColor | *używane do Albedo, Metalness* |
| TransparentColor (Kolor przezroczysty) | *używany do kanału alfa Albedo* |
| PrzezroczystośćFactor | *używany do kanału alfa Albedo* |
| Krycie | *używany do kanału alfa Albedo* |
| SpecularColor (SpecularColor) | *używane do Albedo, Metalność, Chropowatość* |
| SpecularFactor (SpecularFactor)| *używane do Albedo, Metalność, Chropowatość* |
| ShininessExponent | *używane do Albedo, Metalność, Chropowatość* |
| Normalna mapa | Normalna mapa |
| Bump | *przekonwertowane na NormalMap* |
| EmissiveColor ( EmissiveColor ) | - |
| EmissiveFactor (EmissiveFactor) | - |
| Kolor odbicia | - |
| PrzemieszczenieKolor | - |

Powyższe mapowanie jest najbardziej złożoną częścią konwersji materiału, ze względu na wiele założeń, które muszą być wykonane. Omówimy te założenia poniżej.

Niektóre definicje użyte poniżej:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Czerwony ♠ 0.2125 + `Specular`. Zielony ♠ 0,7154 + `Specular`. Niebieski ♠ 0,0721
* `DiffuseBrightness`= 0,299 `Diffuse`* . Czerwony<sup>2</sup> + 0,587 * `Diffuse`. Zielony<sup>2</sup> + 0.114 * `Diffuse`. Niebieski<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Czerwony<sup>2</sup> + 0,587 * `Specular`. Zielony<sup>2</sup> + 0.114 * `Specular`. Niebieski<sup>2</sup>
* `SpecularStrength`=`Specular`maks. Czerwony, `Specular`. Zielony, `Specular`. niebieski)

Formuła SpecularIntensity jest uzyskiwana [z tego miejsca](https://en.wikipedia.org/wiki/Luma_(video)).
Formuła jasności jest opisana w tej [specyfikacji](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Chropowatości

`Roughness`jest obliczana na podstawie `Specular` tej formuły i `ShininessExponent` przy użyciu [niego](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). Wzór jest przybliżeniem chropowatości od wykładnika spekularnego Phong:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalowość

`Metalness`jest obliczana na podstawie `Diffuse` tej formuły i `Specular` przy użyciu tej formuły ze [specyfikacji glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Chodzi o to, że rozwiązujemy równanie: Ax<sup>2</sup> + Bx + C = 0.
Zasadniczo powierzchnie dielektryczne odbijają około 4% światła w sposób odblaskowy, a reszta jest rozproszona. Powierzchnie metalowe nie odbijają światła w sposób rozproszony, ale wszystko w sposób odblaskowy.
Ta formuła ma kilka wad, ponieważ nie ma sposobu, aby odróżnić błyszczące powierzchnie z tworzywa sztucznego i błyszczące metalowe. Zakładamy, że przez większość czasu powierzchnia ma właściwości metaliczne, a w konsekwencji błyszczące powierzchnie z tworzyw sztucznych / gumy mogą nie wyglądać zgodnie z oczekiwaniami.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`jest obliczany `Diffuse` `Specular`z `Metalness`, i .

Jak opisano w sekcji Metalność, powierzchnie dielektryczne odbijają się wokół 4% światła.  
Chodzi o to, aby liniowo `Dielectric` `Metal` interpolować `Metalness` między kolorami i przy użyciu wartości jako czynnika. Jeśli metalowość jest `0.0`, to w zależności od od odbarwnego będzie to ciemny kolor (jeśli specular jest wysoki) lub rozproszone nie zmieni (jeśli nie ma specular). Jeśli metalowość jest dużą wartością, to rozproszony kolor zniknie na rzecz koloru lustrzanego.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`została obliczona przez powyższą formułę, ale kanał alfa wymaga dodatkowych obliczeń. Format FBX jest niejasny co do przejrzystości i ma wiele sposobów, aby go zdefiniować. Narzędzia do różnych treści używają różnych metod. Chodzi o to, aby zjednoczyć je w jedną formułę. To sprawia, że niektóre zasoby niepoprawnie wyświetlane jako przezroczyste, jeśli nie są tworzone w sposób wspólny.

Jest to obliczane `TransparencyFactor` `Opacity`na podstawie `TransparentColor`, , :

jeśli `Opacity` jest zdefiniowany, użyj `AlbedoAlpha`  =  `Opacity` go bezpośrednio: else  
jeśli `TransparencyColor` jest zdefiniowany, to `AlbedoAlpha` = 1,0 - ((`TransparentColor`. Czerwony `TransparentColor`+ . Zielony `TransparentColor`+ . Niebieski) / 3.0)  
jeśli `TransparencyFactor`, `AlbedoAlpha` a następnie = 1,0 -`TransparencyFactor`

Końcowy `Albedo` kolor ma cztery kanały, łączące `AlbedoRGB` z `AlbedoAlpha`.

### <a name="summary"></a>Podsumowanie

Podsumowując tutaj, `Albedo` będzie bardzo blisko oryginału `Diffuse` `Specular` , jeśli jest blisko zera. W przeciwnym razie powierzchnia będzie wyglądać jak metalowa powierzchnia i traci rozproszony kolor. Powierzchnia będzie wyglądać bardziej wypolerowane i odblaskowe, jeśli `ShininessExponent` jest wystarczająco duży i `Specular` jest jasny. W przeciwnym razie powierzchnia będzie wyglądać szorstko i ledwo odzwierciedlać środowisko.

### <a name="known-issues"></a>Znane problemy

* Bieżąca formuła nie działa dobrze w przypadku geometrii w kolorze prostym. Jeśli `Specular` jest wystarczająco jasny, wszystkie geometrie stają się odblaskowymi powierzchniami metalowymi bez żadnego koloru. Obejście tutaj jest obniżenie `Specular` do 30% od oryginału lub użycie ustawienia konwersji [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Materiały PBR zostały `Maya` `3DS Max` niedawno dodane do narzędzi do tworzenia treści i ich tworzenia. Używają niestandardowych właściwości black-box zdefiniowane przez użytkownika, aby przekazać go do FBX. Renderowanie zdalne platformy Azure nie odczytuje tych dodatkowych właściwości, ponieważ nie są one udokumentowane, a format jest źródłem zamkniętym.

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](../how-tos/conversion/model-conversion.md)
* [Zastępowanie materiałów podczas konwersji modelu](../how-tos/conversion/override-materials.md)
