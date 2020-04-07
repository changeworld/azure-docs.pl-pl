---
title: Zastępowanie materiałów podczas konwersji modelu
description: W tym artykule wyjaśniono przepływ pracy zastępujący materiał w czasie konwersji
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681482"
---
# <a name="override-materials-during-model-conversion"></a>Zastępowanie materiałów podczas konwersji modelu

Podczas konwersji ustawienia materiału w modelu źródłowym są używane do definiowania [materiałów PBR używanych](../../overview/features/pbr-materials.md) przez moduł renderowania.
Czasami [konwersja domyślna](../../reference/material-mapping.md) nie daje pożądanych rezultatów i trzeba wprowadzić zmiany.
Gdy model jest konwertowany do użytku w renderowaniu zdalnym platformy Azure, można podać plik zastępowania materiałów, aby dostosować sposób konwersji materiałów odbywa się na podstawie materiału.
Sekcja dotycząca [konfigurowania konwersji modelu](configure-model-conversion.md) zawiera instrukcje dotyczące deklarowania nazwy pliku zastępowania materiału.

## <a name="the-override-file-used-during-conversion"></a>Plik zastępowania używany podczas konwersji

Na prostym przykładzie załóżmy, że model box ma jeden materiał o nazwie "Default". Kolor albedo musi być dostosowany do stosowania w ARR.
W takim przypadku `box_materials_override.json` plik można utworzyć w następujący sposób:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Plik `box_materials_override.json` jest umieszczany w kontenerze `ConversionSettings.json` wejściowym, a obok `box.fbx`niego jest dodawany , który informuje o tym, gdzie można znaleźć plik zastępowania (patrz [Konfigurowanie konwersji modelu):](configure-model-conversion.md)

```json
{
    "material-override" : "box_materials_override.json"
}
```

Po przekonwertowaniu modelu zostaną zastosowane nowe ustawienia.

### <a name="color-materials"></a>Materiały kolorowe

Model [materiału kolorów](../../overview/features/color-materials.md) opisuje stale zacienioną powierzchnię, która jest niezależna od oświetlenia.
Jest to przydatne dla zasobów wykonanych przez algorytmy fotogrametrii, na przykład.
W plikach zastępowania materiału materiał można zadeklarować jako `unlit` materiał `true`kolorowy, ustawiając na .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignoruj określone mapy tekstur

Czasami proces konwersji może ignorować określone mapy tekstur. Może to być przypadek, gdy model został wygenerowany przez narzędzie, które generuje specjalne mapy nie jest poprawnie zrozumiany przez moduł renderowania. Na przykład "OpacityMap", który jest używany do definiowania czegoś innego niż krycie, lub modelu, w którym "NormalMap" jest przechowywany jako "BumpMap". (W tym ostatnim przypadku chcesz zignorować "NormalMap", co spowoduje, że konwerter użyje "BumpMap" jako "NormalMap".)

Zasada jest prosta. Wystarczy dodać właściwość o nazwie `ignoreTextureMaps` i dodać dowolną mapę tekstury, którą chcesz zignorować:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Aby uzyskać pełną listę map tekstur, które można zignorować, zobacz schemat JSON poniżej.

## <a name="json-schema"></a>Schemat JSON

Pełny schemat JSON dla plików materiałów znajduje się tutaj. Z wyjątkiem `unlit` i `ignoreTextureMaps`, dostępne właściwości są podzbiorem właściwości opisanych w sekcjach na [materiał koloru](../../overview/features/color-materials.md) i PBR modeli [materiałów.](../../overview/features/pbr-materials.md)

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Następne kroki

* [Materiały kolorowe](../../overview/features/color-materials.md)
* [Materiały PBR](../../overview/features/pbr-materials.md)