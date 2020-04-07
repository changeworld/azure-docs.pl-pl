---
title: Tekstury
description: Przepływ pracy zasobów tekstury
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681664"
---
# <a name="textures"></a>Tekstury

Tekstury są niezmiennym [zasobem udostępnionym](../concepts/lifetime.md). Tekstury można ładować z [magazynu obiektów blob](../how-tos/conversion/blob-storage.md) i stosować bezpośrednio do modeli, jak pokazano w [samouczku: Zmiana środowiska i materiałów](../tutorials/unity/changing-environment-and-materials.md). Najczęściej jednak tekstury będą częścią [przekonwertowanego modelu,](../how-tos/conversion/model-conversion.md)do którego odwołują się jego [materiały.](materials.md)

## <a name="texture-types"></a>Typy tekstur

Różne typy tekstur mają różne przypadki użycia:

* **Tekstury 2D** są stosowane głównie w [materiałach.](materials.md)
* **Cubemaps** mogą być używane do [nieba](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Obsługiwane formaty tekstur

Wszystkie tekstury podane do ARR muszą być w [formacie DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Najlepiej z mipmaps i kompresji tekstury. Jeśli chcesz zautomatyzować proces konwersji, zobacz [narzędzie wiersza polecenia TexConv.](../resources/tools/tex-conv.md)

## <a name="loading-textures"></a>Ładowanie tekstur

Podczas ładowania tekstury należy określić jej oczekiwany typ. Jeśli typ jest niezgodny, obciążenie tekstury kończy się niepowodzeniem.
Dwukrotne załadowanie tekstury z tym samym identyfikatorem URI spowoduje zwrócenie tego samego obiektu tekstury, ponieważ jest to [zasób udostępniony.](../concepts/lifetime.md)

Podobnie jak w modelach ładowania, istnieją dwa warianty adresowania zasobu tekstury w źródłowym magazynie obiektów blob:

* Zasób tekstury można rozwiązać za pomocą jego identyfikatora URI SAS. Odpowiednia funkcja `LoadTextureFromSASAsync` ładowania `LoadTextureFromSASParams`jest z parametrem . Użyj tego wariantu również podczas ładowania [wbudowanych tekstur](../overview/features/sky.md#built-in-environment-maps).
* Teksturę można rozwiązać bezpośrednio za pomocą parametrów magazynu obiektów blob, w przypadku gdy [magazyn obiektów blob jest połączony z kontem](../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania `LoadTextureAsync` w `LoadTextureParams`tym przypadku jest z parametrem .

Poniższy przykładowy kod pokazuje, jak załadować teksturę za pośrednictwem jego identyfikatora URI sygnatury dostępu Współdzielonego (lub wbudowanej tekstury) — należy pamiętać, że tylko funkcja ładowania/parametr różni się w przypadku innego przypadku:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

W zależności od tego, do czego ma być używana tekstura, mogą istnieć ograniczenia dotyczące typu tekstury i zawartości. Na przykład mapa chropowatości [materiału PBR](../overview/features/pbr-materials.md) musi być w skali szarości.

> [!CAUTION]
> Wszystkie *funkcje asynchroniczne* w ARR zwracają obiekty operacji asynchronialnych. Należy przechowywać odwołanie do tych obiektów, dopóki operacja nie zostanie zakończona. W przeciwnym razie moduł odśmiecania elementów bezużytecznych języka C# może usunąć operację wcześniej i nigdy nie może zakończyć. W przykładowym kodzie powyżej zmiennej członkowskiej "_textureLoad" jest używany do przechowywania odwołania, dopóki nie nadejdzie *zdarzenie Completed.*

## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
* [Niebo](../overview/features/sky.md)
