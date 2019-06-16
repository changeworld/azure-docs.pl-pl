---
title: Jak za pomocą danych profilowania źródeł danych w usłudze Azure Data Catalog
description: Artykuł porad wyróżnianie, jak dołączyć poziomie tabeli i kolumn danych profilów rejestrowania źródła danych w usłudze Azure Data Catalog i sposobu używania profilów danych, aby zrozumieć użycie źródeł danych.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 64185a951b25b4e04ea5fc65aeede9b0e617d0c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61001723"
---
# <a name="data-profile-data-sources"></a>Źródła danych profilu danych
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to usługa w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i system odnajdowania firmowych źródeł danych. Innymi słowy **usługi Azure Data Catalog** to przede wszystkim pomagając ludziom, odnajdywanie, zrozumienie i używanie źródeł danych oraz pomagając organizacjom pozwala zwiększyć wartość uzyskiwaną z ich istniejących danych. Jeśli źródło danych jest zarejestrowany w **usługi Azure Data Catalog**, jego metadanych jest kopiowany i indeksowane przez tę usługę, ale wątek nie kończy się tam.

**Danych profilowania** funkcji **usługi Azure Data Catalog** sprawdza dane z obsługiwanych źródeł danych w wykazie i zbiera dane statystyczne i informacje na temat tych danych. To proste dołączyć profil zasobów danych. Po zarejestrowaniu się zasobu danych, wybierz **Dołącz profil danych** w narzędzia rejestracji źródła danych.

## <a name="what-is-data-profiling"></a>Co to jest danych profilowania
Dane profilowania analizuje dane w źródle danych jest zarejestrowany i zbiera dane statystyczne i informacje na temat tych danych. Podczas odnajdowania źródeł danych te statystyki może pomóc w określeniu przydatność danych, aby rozwiązać problemy biznesowe.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Następujące źródła danych obsługują dane profilowania:

* Program SQL Server (w tym usługi Azure SQL DB i Azure SQL Data Warehouse), tabele i widoki
* Oracle tabel i widoków
* Teradata tabel i widoków
* Tabele programu hive

Takie jak profile danych podczas rejestrowania zasobów danych ułatwia użytkownikom odpowiedzi na pytania dotyczące źródeł danych, w tym:

* Może on służyć do rozwiązywania problemów z firmą?
* Czy danych jest zgodny z określonego standardów lub wzorców?
* Jakie są anomalii w źródle danych?
* Co to są potencjalnych problemów integracji tych danych do mojej aplikacji?

> [!NOTE]
> Dokumentację można także dodać do elementu zawartości do opisywania, jak dane mogą być zintegrowane z aplikacji. Zobacz [jak dokumentować źródła danych](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Jak dołączyć profil danych podczas rejestrowania źródła danych
To proste dołączyć profil źródła danych. Po zarejestrowaniu źródła danych w **obiekty do zarejestrowania** panelu Narzędzia rejestracji źródła danych, wybierz **Dołącz profil danych**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Aby dowiedzieć się więcej o tym, jak rejestrować źródła danych, zobacz [jak rejestrować źródła danych](data-catalog-how-to-register.md) i [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrowanie zasobów danych, które dotyczą profili danych
Aby odnaleźć zasoby danych, które Dołącz profil danych, można dołączyć `has:tableDataProfiles` lub `has:columnsDataProfiles` jako jeden z wyszukiwane terminy.

> [!NOTE]
> Wybieranie **Dołącz profil danych** w źródle danych, narzędzia do rejestracji zawiera zarówno w tabeli, jak i informacje o profilu na poziomie kolumny. Jednak API usługi Data Catalog umożliwia zasoby danych mają być zarejestrowany tylko jeden zestaw uwzględnione informacje o profilu.
>
>

## <a name="viewing-data-profile-information"></a>Wyświetlanie informacji o profilu danych
Po znalezieniu źródłem odpowiednie dane za pomocą profilu, możesz wyświetlić szczegóły profilu danych. Aby wyświetlić profil danych, wybierz zasobu danych i wybierz **profil danych** w oknie portalu wykazu danych.

![](media/data-catalog-data-profile/data-catalog-view.png)

Profil danych w **usługi Azure Data Catalog** pokazuje tabela i kolumna profilu informacje, w tym:

### <a name="object-data-profile"></a>Profil danych obiektu
* Liczba wierszy
* Rozmiar tabeli
* Gdy obiekt ostatniej aktualizacji

### <a name="column-data-profile"></a>Profil danych kolumny
* Typ danych kolumny
* Liczba unikatowych wartości
* Liczba wierszy zawierających wartości NULL
* Minimalna, maksymalna, średnia i odchylenia standardowego dla wartości w kolumnie

## <a name="summary"></a>Podsumowanie
Dane profilowania zawiera dane statystyczne i informacje o zarejestrowanych zasobów danych w celu określenia przydatność danych do rozwiązywania problemów biznesowych. Oprócz dodawania adnotacji i dokumentowanie źródeł danych, dane profilów można udostępnić użytkownikom lepiej zrozumieć dane.

## <a name="see-also"></a>Zobacz też
* [Jak rejestrować źródła danych](data-catalog-how-to-register.md)
* [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md)
