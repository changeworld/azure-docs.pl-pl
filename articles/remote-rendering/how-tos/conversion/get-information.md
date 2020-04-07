---
title: Uzyskaj informacje o przekonwertowanym modelu
description: Opis wszystkich parametrów konwersji modelu
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681521"
---
# <a name="get-information-about-a-converted-model"></a>Uzyskaj informacje o przekonwertowanym modelu

Plik arrAsset wyprodukowany przez usługę konwersji jest przeznaczony wyłącznie do użytku przez usługę utylizacją. Może jednak wystąpić czasy, gdy chcesz uzyskać dostęp do informacji o modelu bez uruchamiania sesji renderowania. W związku z tym usługa konwersji umieszcza plik JSON obok pliku arrAsset w kontenerze wyjściowym. Na przykład, jeśli `buggy.gltf` plik jest konwertowany, kontener `buggy.info.json` wyjściowy będzie `buggy.arrAsset`zawierał plik o nazwie obok przekonwertowanego zasobu . Zawiera informacje o modelu źródłowym, przekonwertowanym modelu i o samej konwersji.

## <a name="example-info-file"></a>Przykładowy plik *informacyjny*

Oto przykładowy plik *informacji* wyprodukowany przez `buggy.gltf`konwersję pliku o nazwie:

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informacje w pliku informacyjnym

### <a name="the-files-section"></a>Sekcja *pliki*

Ta sekcja zawiera podane nazwy plików.

* `input`: Nazwa pliku źródłowego.
* `output`: Nazwa pliku wyjściowego, gdy użytkownik określił nazwę nieniekońcową.

### <a name="the-conversionsettings-section"></a>Sekcja *ConversionSettings*

W tej sekcji znajduje się kopia [ConversionSettings](configure-model-conversion.md#settings-file) określone podczas konwersji modelu.

### <a name="the-inputinfo-section"></a>Sekcja *inputInfo*

W tej sekcji są rejestrowane informacje o formacie pliku źródłowego.

* `sourceAssetExtension`: Rozszerzenie pliku źródłowego.
* `sourceAssetFormat`: Opis formatu pliku źródłowego.
* `sourceAssetFormatVersion`: Wersja formatu pliku źródłowego.
* `sourceAssetGenerator`: Nazwa narzędzia, które wygenerowało plik źródłowy, jeśli jest dostępna.

### <a name="the-inputstatistics-section"></a>Sekcja *InputStatistics*

Ta sekcja zawiera informacje o scenie źródłowej. Często występują rozbieżności między wartościami w tej sekcji a równoważnymi wartościami w narzędziu, które utworzyło model źródłowy. Takie różnice są oczekiwane, ponieważ model zostanie zmodyfikowany podczas etapów eksportowania i konwersji.

* `numMeshes`: Liczba części siatki, w których każda część może odwoływać się do pojedynczego materiału.
* `numFaces`: Całkowita liczba _trójkątów_ w całym modelu. Należy zauważyć, że siatka jest triangulated podczas konwersji.
* `numVertices`: Całkowita liczba wierzchołków w całym modelu.
* `numMaterial`: Całkowita liczba materiałów w całym modelu.
* `numFacesSmallestMesh`: Liczba trójkątów w najmniejszej siatce modelu.
* `numFacesBiggestMesh`: Liczba trójkątów w największej siatce modelu.
* `numNodes`: Liczba węzłów na wykresie sceny modelu.
* `numMeshUsagesInScene`: Liczba sekwencji odwołań do siatek. Więcej niż jeden węzeł może odwoływać się do tej samej siatki.
* `maxNodeDepth`: Maksymalna głębokość węzłów na wykresie sceny.

### <a name="the-outputinfo-section"></a>Sekcja *outputInfo*

W tej sekcji zapisuje się ogólne informacje o wygenerowanych danych wyjściowych.

* `conversionToolVersion`: Wersja konwertera modelu.
* `conversionHash`: skrót danych w arrAsset, które mogą przyczynić się do renderowania. Może służyć do zrozumienia, czy usługa konwersji przyniosły inny wynik po ponownym uruchomieniu w tym samym pliku.

### <a name="the-outputstatistics-section"></a>Sekcja *statystyki wyjściowej*

W tej sekcji zapisuje się informacje obliczone na podstawie przekonwertowanego zasobu.

* `numMeshPartsCreated`: Liczba siatek w arrAsset. Może się `numMeshes` różnić `inputStatistics` od w sekcji, ponieważ na instancing ma wpływ proces konwersji.
* `numMeshPartsInstanced`: Liczba siatek, które są ponownie używane w arrAsset.
* `recenteringOffset`: Gdy `recenterToOrigin` opcja w [conversionsettings](configure-model-conversion.md) jest włączona, ta wartość jest tłumaczeniem, które przesunie przekonwertowany model z powrotem do jego pierwotnej pozycji.
* `boundingBox`: Granice modelu.

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](model-conversion.md)
* [Konfigurowanie konwersji modelu](configure-model-conversion.md)
