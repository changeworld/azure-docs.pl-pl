---
title: Sposób pracy ze źródłami danych dane big data w usłudze Azure Data Catalog
description: Artykuł porad wyróżnianie wzorce ze źródłami danych dane big data, w tym usługi Azure Blob Storage, Azure Data Lake i HDFS, Hadoop przy użyciu usługi Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b6b419d575e2164fc683b8e6b5020572db74d1b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001745"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Jak pracować z dużymi źródłami danych w usłudze Azure Data Catalog
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to usługa w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i system odnajdowania firmowych źródeł danych. Jest ona wszystkie o osobach, pomagając odnaleźć, zrozumieć i używać źródła danych i pomagając organizacjom pozwala zwiększyć wartość uzyskiwaną z ich istniejących źródeł danych, włącznie z danymi big Data.

**Usługa Azure Data Catalog** obsługuje rejestrację obiektów blob Azure Blog Storage i katalogów, a także HDFS, Hadoop, plików i katalogów. Lub częściową strukturą charakter tych źródeł danych zapewnia dużą elastyczność. Jednak aby uzyskać największe korzyści z ich za pomocą rejestrowania **usługi Azure Data Catalog**, użytkowników należy wziąć pod uwagę organizowania źródeł danych.

## <a name="directories-as-logical-data-sets"></a>Katalogi jako logicznych zestawów danych
Typowy wzorzec służący do organizowania źródeł danych big data jest traktować katalogów jako zestawy danych logicznych. Katalogi najwyższego poziomu są używane do definiowania zestawu danych, podczas podfoldery Definiuj partycje, a pliki, które zawierają przechowywania danych.

Przykładem tego wzorca mogą być:

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

W tym przykładzie vehicle_maintenance_events i location_tracking_events reprezentują logiczne zestawów danych. Każda z tych folderów zawiera pliki danych, które są zorganizowane według roku i miesiąca w jego podfolderach. Każda z tych folderów potencjalnie mogą zawierać setek lub tysięcy plików.

W tym wzorcu rejestrowania poszczególnych plików za pomocą **usługi Azure Data Catalog** prawdopodobnie nie ma sensu. Zamiast tego Zarejestruj katalogi, które reprezentują zbiory danych, które można wiele wywnioskować użytkownikom pracy z danymi.

## <a name="reference-data-files"></a>Odwołanie do plików danych
Uzupełniające wzorzec jest do przechowywania zestawów danych referencyjnych jako pojedyncze pliki. Te zestawy danych mogą być uważane za "małe" po stronie danych big Data i często są podobne do wymiarów w modelu danych analitycznych. Pliki danych odwołania zawiera rekordy, które są używane do zapewniania kontekstu zbiorczego pliki danych przechowywanych w innym miejscu w magazynie danych big Data.

Przykładem tego wzorca mogą być:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Badacz danych lub analityka pracując z danymi zawartymi w większych struktur katalogów danych w tych plikach odwołania może służyć do zapewnienia bardziej szczegółowe informacje dotyczące jednostek, które są określane tylko według nazwy lub Identyfikatora większy zestaw danych.

W tym wzorcu warto zarejestrować plików danych indywidualne odwołanie z **usługi Azure Data Catalog**. Każdy plik reprezentuje zestaw danych i każdej z nich można dodawać adnotacje i odnalezione indywidualnie.

## <a name="alternate-patterns"></a>Alternatywne wzorce
Wzorce opisane w poprzedniej sekcji są tylko dwa możliwe sposoby, które mogą być uporządkowane magazynie danych big Data, ale każda implementacja jest inny. Niezależnie od tego, jak strukturalnych źródeł danych, podczas rejestrowania źródeł danych big data za pomocą **usługi Azure Data Catalog**, skupić się na rejestrowanie, pliki i katalogi, które reprezentują zbiory danych, które mają wartość innym użytkownikom w ramach usługi organizacja. Rejestrowanie wszystkich plików i katalogów można zaśmiecać wykazu, co utrudnia użytkownikom znajdowanie potrzebnych informacji.

## <a name="summary"></a>Podsumowanie
Rejestrowanie źródeł danych za pomocą **usługi Azure Data Catalog** łatwiej je do odnalezienia i zrozumienia. Rejestrowanie i dodawanie adnotacji do obsługi dużych ilości danych, pliki i katalogi, które reprezentują logiczne zestawów danych, możesz pomóc użytkownikom znajdowanie i używanie źródeł danych big data, które są im potrzebne.
