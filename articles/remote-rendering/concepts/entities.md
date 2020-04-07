---
title: Jednostki
description: Definicja jednostek w zakresie interfejsu API zdalnego renderowania platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681950"
---
# <a name="entities"></a>Jednostki

*Jednostka* reprezentuje ruchomy obiekt w przestrzeni i jest podstawowym budulcem zdalnie renderowanych treści.

## <a name="entity-properties"></a>Właściwości encji

Elementy mają transformację zdefiniowaną przez położenie, obrót i skalę. Same jednostki nie mają żadnych zauważalnych funkcji. Zamiast tego zachowanie jest dodawane za pośrednictwem składników, które są dołączone do jednostek. Na przykład dołączenie [CutPlaneComponent](../overview/features/cut-planes.md) spowoduje utworzenie płaszczyzny cięcia w pozycji elementu.

Najważniejszym aspektem samej jednostki jest hierarchia i wynikowa transformacja hierarchiczna. Na przykład gdy wiele jednostek są dołączone jako elementy podrzędne do jednostki nadrzędnej udostępnionej, wszystkie te jednostki mogą być przenoszone, obracane i skalowane w zgodzie, zmieniając przekształcenie jednostki nadrzędnej.

Jednostka jest unikatowo własnością jej jednostki nadrzędnej, co `Entity.Destroy()`oznacza, że gdy jednostka nadrzędna jest niszczona za pomocą , tak są jego elementy podrzędne i wszystkie połączone [składniki.](components.md) W związku z tym usunięcie modelu ze `Destroy` sceny odbywa się przez wywołanie `AzureSession.Actions.LoadModelAsync()` węzła `AzureSession.Actions.LoadModelFromSASAsync()`głównego modelu, zwróconego przez lub jego wariant sygnatury dostępu Współdzielonego.

Jednostki są tworzone, gdy serwer ładuje zawartość lub gdy użytkownik chce dodać obiekt do sceny. Na przykład, jeśli użytkownik chce dodać płaszczyznę cięcia do wizualizacji wnętrza siatki, użytkownik może utworzyć element, w którym płaszczyzna powinna istnieć, a następnie dodać do niej komponent płaszczyzny cięcia.

## <a name="query-functions"></a>Funkcje kwerendy

Istnieją dwa typy funkcji kwerendy w jednostkach: wywołania synchroniczne i asynchroniczne. Kwerendy synchroniczne mogą być używane tylko dla danych, które są obecne na kliencie i nie wymaga wiele obliczeń. Przykłady są kwerendy dla składników, względne przekształcenia obiektów lub relacji nadrzędny/podrzędny. Kwerendy asynchroniczne są używane dla danych, które znajdują się tylko na serwerze lub obejmuje dodatkowe obliczenia, które byłyby zbyt kosztowne do uruchomienia na kliencie. Przykładami są zapytania o granice przestrzenne lub zapytania dotyczące metadanych.

### <a name="querying-components"></a>Wykonywanie zapytań o składniki

Aby znaleźć składnik określonego typu, `FindComponentOfType`należy użyć:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Wykonywanie zapytań o przekształcenia

Kwerendy przekształcania są synchroniczne wywołania obiektu. Należy pamiętać, że przekształcenia poszukiwane za pośrednictwem interfejsu API są transformacje przestrzeni lokalnej, względem obiektu nadrzędnego. Wyjątki to obiekty główne, dla których przestrzeń lokalna i przestrzeń świata są identyczne.

> [!NOTE]
> Nie ma dedykowanego interfejsu API do wykonywania zapytań o transformację przestrzeni świata dowolnych obiektów.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Wykonywanie zapytań o granice przestrzenne

Zapytania granice są wywołania asynchroniczne, które działają w hierarchii pełnego obiektu, przy użyciu jednej jednostki jako katalogu głównego. Zobacz dedykowany rozdział o [granicach obiektu](object-bounds.md).

### <a name="querying-metadata"></a>Wykonywanie zapytań o metadane

Metadane to dodatkowe dane przechowywane w obiektach, które są ignorowane przez serwer. Metadane obiektu to zasadniczo zestaw par (nazwa, wartość), gdzie _wartość_ może być typu liczbowego, logicznego lub ciągu. Metadane mogą być eksportowane za pomocą modelu.

Zapytania metadanych są wywołania asynchroniczne dla określonej jednostki. Kwerenda zwraca tylko metadane pojedynczej jednostki, a nie scalone informacje wykresu podrzędnego.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

Kwerenda zakończy się pomyślnie, nawet jeśli obiekt nie zawiera żadnych metadanych.

## <a name="next-steps"></a>Następne kroki

* [Składniki](components.md)
* [Granice obiektu](object-bounds.md)
