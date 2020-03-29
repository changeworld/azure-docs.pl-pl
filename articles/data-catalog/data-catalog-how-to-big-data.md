---
title: Jak katalogować duże zbiory danych w usłudze Azure Data Catalog
description: Artykuł przedstawiający wzorce przedstawiające wzorce dotyczące korzystania z usługi Azure Data Catalog z źródłami danych "big data", w tym usługi Azure Blob Storage, Usługa Azure Data Lake i Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300586"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Jak katalogować duże zbiory danych w usłudze Azure Data Catalog

## <a name="introduction"></a>Wprowadzenie

**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i systemu odnajdowania dla źródeł danych przedsiębiorstwa. Chodzi o pomoc w odkrywaniu, zrozumieniu i korzystaniu ze źródeł danych oraz pomaganiu organizacjom w pozyskiwaniu większej wartości z istniejących źródeł danych, w tym dużych zbiorów danych.

**Usługa Azure Data Catalog** obsługuje rejestrację obiektów blob i katalogów usługi Azure Blog Storage, a także plików i katalogów usługi Hadoop HDFS. Półstrukturalny charakter tych źródeł danych zapewnia dużą elastyczność. Jednak aby uzyskać jak najwięcej wartości z rejestrowania ich w **usłudze Azure Data Catalog,** użytkownicy muszą rozważyć sposób organizowania źródeł danych.

## <a name="directories-as-logical-data-sets"></a>Katalogi jako logiczne zestawy danych

Typowym wzorcem do organizowania źródeł dużych zbiorów danych jest traktowanie katalogów jako logicznych zestawów danych. Katalogi najwyższego poziomu są używane do definiowania zestawu danych, podczas gdy podfoldery definiują partycje, a pliki, które zawierają, przechowują same dane.

Przykładem tego wzorca może być:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

W tym przykładzie vehicle_maintenance_events i location_tracking_events reprezentują logiczne zestawy danych. Każdy z tych folderów zawiera pliki danych, które są zorganizowane według roku i miesiąca w podfoldery. Każdy z tych folderów może potencjalnie zawierać setki lub tysiące plików.

W tym wzorzec rejestrowanie poszczególnych plików w **usłudze Azure Data Catalog** prawdopodobnie nie ma sensu. Zamiast tego należy zarejestrować katalogi, które reprezentują zestawy danych, które mają znaczenie dla użytkowników pracujących z danymi.

## <a name="reference-data-files"></a>Pliki danych referencyjnych

Wzorzec uzupełniający jest do przechowywania zestawów danych referencyjnych jako pojedyncze pliki. Te zestawy danych mogą być traktowane jako "mała" strona dużych zbiorów danych i często są podobne do wymiarów w modelu danych analitycznych. Referencyjne pliki danych zawierają rekordy, które są używane do zapewnienia kontekstu dla większości plików danych przechowywanych w innym miejscu w magazynie dużych zbiorów danych.

Przykładem tego wzorca może być:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Gdy analityk lub analityk danych pracuje z danymi zawartymi w większych strukturach katalogów, dane w tych plikach referencyjnych mogą służyć do dostarczania bardziej szczegółowych informacji dla jednostek, które są określane tylko przez nazwę lub identyfikator w większym zestawie danych.

W tym wzorcu warto zarejestrować poszczególne pliki danych referencyjnych w **usłudze Azure Data Catalog**. Każdy plik reprezentuje zestaw danych, a każdy z nich może być opisywany i odnajdowany indywidualnie.

## <a name="alternate-patterns"></a>Alternatywne wzory

Wzorce opisane w poprzedniej sekcji to tylko dwa możliwe sposoby organizowania magazynu dużych zbiorów danych, ale każda implementacja jest inna. Niezależnie od struktury źródeł danych podczas rejestrowania źródeł dużych zbiorów danych w **usłudze Azure Data Catalog**należy skupić się na rejestrowaniu plików i katalogów, które reprezentują zestawy danych, które mają wartość dla innych osób w organizacji. Rejestrowanie wszystkich plików i katalogów może zaśmiecać katalog, utrudniając użytkownikom znalezienie tego, czego potrzebują.

## <a name="summary"></a>Podsumowanie

Rejestrowanie źródeł danych w **usłudze Azure Data Catalog** ułatwia ich odnajdowanie i zrozumienie. Rejestrując i donośnąc pliki i katalogi dużych zbiorów danych, które reprezentują logiczne zestawy danych, można pomóc użytkownikom w znalezieniu i korzystaniu ze potrzebnych im źródeł danych.
