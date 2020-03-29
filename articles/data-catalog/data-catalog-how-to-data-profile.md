---
title: Jak korzystać ze źródeł danych profilowania danych w usłudze Azure Data Catalog
description: Artykuł in jakywanie przedstawiający sposób dołączania profilów danych na poziomie tabeli i kolumn podczas rejestrowania źródeł danych w usłudze Azure Data Catalog oraz sposobu używania profilów danych do zrozumienia źródeł danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 04ac6c2bf0137289221a4ae6ed58d5a71ad21739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950223"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Jak źródła danych profilu danych w wykazie danych platformy Azure

## <a name="introduction"></a>Wprowadzenie

**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i systemu odnajdowania dla źródeł danych przedsiębiorstwa. Innymi słowy **usługa Azure Data Catalog** ułatwia ludziom odkrywanie, zrozumienie i używanie źródeł danych oraz pomaga organizacjom w pozyskiwaniu większej wartości z istniejących danych. Gdy źródło danych jest zarejestrowane w **usłudze Azure Data Catalog,** jego metadane są kopiowane i indeksowane przez usługę, ale historia nie kończy się na tym.

Funkcja **profilowania danych** **usługi Azure Data Catalog** sprawdza dane z obsługiwanych źródeł danych w katalogu i zbiera statystyki i informacje o tych danych. Łatwo jest dołączyć profil zasobów danych. Podczas rejestrowania zasobu danych wybierz pozycję **Uwzględnij profil danych** w narzędziu rejestracji źródła danych.

## <a name="what-is-data-profiling"></a>Co to jest profilowanie danych

Profilowanie danych sprawdza dane w zarejestrowanym źródle danych i zbiera statystyki i informacje o tych danych. Podczas odnajdowania źródła danych te statystyki mogą pomóc w określeniu przydatności danych do rozwiązania ich problemu biznesowego.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Następujące źródła danych obsługują profilowanie danych:

* Tabele i widoki programu SQL Server (w tym usługi Azure SQL DB i Azure SQL Data Warehouse)
* Tabele i widoki Oracle
* Teradata tabele i widoki
* Tabele Hive

Uwzględnienie profilów danych podczas rejestrowania zasobów danych pomaga użytkownikom odpowiadać na pytania dotyczące źródeł danych, w tym:

* Czy można go wykorzystać do rozwiązania problemu biznesowego?
* Czy dane są zgodne z określonymi normami lub wzorcami?
* Jakie są niektóre anomalie źródła danych?
* Jakie są możliwe wyzwania związane z integracją tych danych z moją aplikacją?

> [!NOTE]
> Można również dodać dokumentację do zasobu, aby opisać, jak dane mogą być zintegrowane z aplikacją. Zobacz [Jak dokumentować źródła danych](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Jak dołączyć profil danych podczas rejestrowania źródła danych

Łatwo jest dołączyć profil źródła danych. Podczas rejestrowania źródła danych w panelu **Obiekty, które mają być zarejestrowane,** wybierz pozycję **Uwzględnij profil danych**.

![Pole wyboru Dołącz profil danych](media/data-catalog-data-profile/data-catalog-register-profile.png)

Aby dowiedzieć się więcej o rejestrowaniu źródeł danych, zobacz [Jak zarejestrować źródła danych](data-catalog-how-to-register.md) i Wprowadzenie do usługi Azure Data [Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrowanie zasobów danych, które zawierają profile danych

Aby odkryć zasoby danych, które zawierają profil `has:tableDataProfiles` `has:columnsDataProfiles` danych, możesz dołączyć lub jako jeden z wyszukiwanych haseł.

> [!NOTE]
> Wybranie **opcji Uwzględnij profil danych** w narzędziu rejestracji źródła danych zawiera informacje o profilu na poziomie tabeli i kolumny. Jednak interfejs API wykazu danych umożliwia zarejestrowanie zasobów danych tylko z jednym zestawem informacji o profilu.
>

## <a name="viewing-data-profile-information"></a>Wyświetlanie informacji o profilu danych

Po znalezieniu odpowiedniego źródła danych z profilem można wyświetlić szczegóły profilu danych. Aby wyświetlić profil danych, wybierz zasób danych i wybierz pozycję **Profil danych** w oknie Portal wykazu danych.

![Karta Profil danych](media/data-catalog-data-profile/data-catalog-view.png)

Profil danych w **usłudze Azure Data Catalog** zawiera informacje o profilu tabeli i kolumn, w tym:

### <a name="object-data-profile"></a>Profil danych obiektu

* Liczba wierszy
* Rozmiar tabeli
* Kiedy obiekt został ostatnio zaktualizowany

### <a name="column-data-profile"></a>Profil danych kolumny

* Typ danych kolumny
* Liczba odrębnych wartości
* Liczba wierszy z wartościami NULL
* Odchylenie minimalne, maksymalne, średnie i standardowe dla wartości kolumn

## <a name="summary"></a>Podsumowanie

Profilowanie danych udostępnia statystyki i informacje o zarejestrowanych zasobach danych, aby pomóc w określeniu przydatności danych do rozwiązywania problemów biznesowych. Wraz z dodawaniem adnotacji i dokumentowanie źródeł danych profile danych mogą dać użytkownikom głębsze zrozumienie danych.

## <a name="see-also"></a>Zobacz też

* [Jak rejestrować źródła danych](data-catalog-how-to-register.md)
* [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md)
