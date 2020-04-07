---
title: Odbicia nieba
description: Opisuje sposób konfigurowania map środowiskowych dla odbić nieba
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680611"
---
# <a name="sky-reflections"></a>Odbicia nieba

W usłudze Azure Remote Rendering tekstura nieba jest używana do realistycznego oświetlenia obiektów. W przypadku aplikacji rozszerzonej rzeczywistości tekstura ta powinna przypominać rzeczywiste otoczenie, aby obiekty wydawały się przekonujące. W tym artykule opisano, jak zmienić teksturę nieba.

> [!NOTE]
> Tekstura nieba jest również określana jako *mapa środowiska.* Terminy te są używane zamiennie.

## <a name="object-lighting"></a>Oświetlenie obiektów

Renderowanie zdalne platformy Azure wykorzystuje *renderowanie oparte fizycznie* (PBR) do realistycznych obliczeń oświetlenia. Chociaż możesz dodać [źródła światła](lights.md) do sceny, używanie dobrej tekstury nieba ma największy wpływ.

Poniższe obrazy przedstawiają wyniki oświetlenia różnych powierzchni tylko z teksturą nieba:

| Chropowatości  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Niemetalowe  | ![Dielektryk0](media/dielectric-0.png)   | ![Park GreenPointPark](media/dielectric-0.25.png)  | ![Park GreenPointPark](media/dielectric-0.5.png)  | ![Park GreenPointPark](media/dielectric-0.75.png)  | ![Park GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![Park GreenPointPark](media/metallic-0.png)  | ![Park GreenPointPark](media/metallic-0.25.png)    | ![Park GreenPointPark](media/metallic-0.5.png)    | ![Park GreenPointPark](media/metallic-0.75.png)    | ![Park GreenPointPark](media/metallic-1.png)    |

Aby uzyskać więcej informacji na temat modelu oświetlenia, zobacz rozdział [materiałów.](../../concepts/materials.md)

> [!IMPORTANT]
> Renderowanie zdalne platformy Azure używa tekstury nieba tylko dla modeli oświetlenia. Nie czyni nieba tłem, ponieważ aplikacje Augmented Reality mają już odpowiednie tło - prawdziwy świat.

## <a name="changing-the-sky-texture"></a>Zmiana tekstury nieba

Aby zmienić mapę środowiska, wystarczy [załadować teksturę](../../concepts/textures.md) i `SkyReflectionSettings`zmienić sesję:

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Należy zauważyć, że `LoadTextureFromSASAsync` wariant jest używany powyżej, ponieważ wbudowana tekstura jest ładowana. W przypadku ładowania z [połączonych magazynów obiektów blob](../../how-tos/create-an-account.md#link-storage-accounts)użyj wariantu. `LoadTextureAsync`

## <a name="sky-texture-types"></a>Typy tekstur nieba

Mapy środowiska można używać zarówno *[map modułowych,](https://en.wikipedia.org/wiki/Cube_mapping)* jak i *tekstur 2D.*

Wszystkie tekstury muszą być w [obsługiwanym formacie tekstury.](../../concepts/textures.md#supported-texture-formats) Nie musisz dostarczać mipmaps dla tekstur nieba.

### <a name="cube-environment-maps"></a>Mapy środowiska sześcianu

Dla odniesienia, oto nieopakowane cubemap:

![Nieopakowana mapa modułu](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Użyj `LoadTextureFromSASAsync` `TextureType.CubeMap` z załadować cubemap tekstury.

### <a name="sphere-environment-maps"></a>Mapy środowiska sfery

W przypadku korzystania z tekstury 2D jako mapy środowiska obraz musi znajdować się w [sferycznej przestrzeni współrzędnych.](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

![Obraz nieba we współrzędnych sferycznych](media/spheremap-example.png)

Użyj `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` z do ładowania map sferycznych środowiska.

## <a name="built-in-environment-maps"></a>Wbudowane mapy środowiska

Renderowanie zdalne platformy Azure udostępnia kilka wbudowanych map środowiska, które są zawsze dostępne. Wszystkie wbudowane mapy środowiska są cubemaps.

|Identyfikator                         | Opis                                              | Ilustracji                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Różnorodność świateł paskowych, jasne oświetlenie podstawy wewnętrznej    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Jasne oświetlenie wewnętrzne, wiele świateł okiennych      | ![Pokój kotłownia](media/boiler-room.png)
|builtin://ColorfulStudio           | Światła w różnobarze w średnim świetle w pomieszczeniach  | ![KoloroweStudio](media/colorful-studio.png)
|builtin://Hangar                   | Umiarkowanie jasne oświetlenie hali otoczenia                     | ![MałyGar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Słabe ustawienie wewnętrzne z kontrastem jasnej ciemności              | ![PrzemysłowaPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Światło otoczenia w ciągu dnia, jasne światło w oknie     | ![Okręg wyborczy Lebombo](media/lebombo.png)
|builtin://SataraNight              | Ciemne nocne niebo i ziemia z wieloma otaczającymi światłami   | ![SataraNocny](media/satara-night.png)
|builtin://SunnyVondelpark          | Jasne światło słoneczne i kontrast cienia                      | ![Park SłonecznyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Jasne światło nieba z umiarkowanym oświetleniem gruntu            | ![Syferfontein ( Syferfontein )](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Umiarkowanie zmienne słońce i cień                         | ![TearsOfSteelBridge (TearsOfSteelBridge)](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Wieczorne światło zachodu słońca zbliżające się do zmierzchu                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Jasne, bujnie zielone i białe odcienie światła, przyciemnione | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | W ciągu dnia z jasnym światłem terenu otoczenia                 | ![ZimowaRiver](media/winter-river.png)
|builtin://DefaultSky               | Tak samo jak TearsOfSteelBridge                               | ![DefaultSky (Domyślnysky)](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Następne kroki

* [Lampki](../../overview/features/lights.md)
* [Materiały](../../concepts/materials.md)
* [Tekstury](../../concepts/textures.md)
* [Narzędzie wiersza polecenia TexConv](../../resources/tools/tex-conv.md)
