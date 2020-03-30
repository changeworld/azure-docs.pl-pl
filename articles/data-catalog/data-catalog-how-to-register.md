---
title: Rejestrowanie źródeł danych w wykazie danych platformy Azure
description: W tym artykule przedstawiono sposób rejestrowania źródeł danych w usłudze Azure Data Catalog, w tym pól metadanych wyodrębnionych podczas rejestracji.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736320"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Rejestrowanie źródeł danych w wykazie danych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Usługa Azure Data Catalog to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdowania dla źródeł danych przedsiębiorstwa. Innymi słowy, usługa Data Catalog pomaga użytkownikom wykrywać, rozumieć i używać źródeł danych, a także pomaga organizacjom uzyskać większą wartość z istniejących danych. Pierwszym krokiem do tworzenia źródła danych wykrywalne za pośrednictwem wykazu danych jest zarejestrowanie tego źródła danych.

## <a name="register-data-sources"></a>Rejestrowanie źródeł danych (Rejestrowanie źródeł danych)
Rejestracja to proces wyodrębniania metadanych ze źródła danych i kopiowania tych danych do usługi wykaz danych. Dane pozostają w miejscu, w którym aktualnie się znajdują, i podlegają kontroli administratorów i zasadom obowiązującym w danym systemie.

Aby zarejestrować źródło danych, wykonaj następujące czynności:
1. W portalu usługi Azure Data Catalog uruchom narzędzie do rejestracji źródła danych wykazu danych. 
2. Zaloguj się przy użyciu konta służbowego przy użyciu tych samych poświadczeń usługi Azure Active Directory, których używasz do logowania się do portalu.
3. Wybierz źródło danych, które chcesz zarejestrować.

Aby uzyskać więcej szczegółów krok po kroku, zobacz wprowadzenie [do usługi Azure Data Catalog](data-catalog-get-started.md) samouczek.

Po zarejestrowaniu źródła danych katalog śledzi jego lokalizację i indeksuje jego metadane. Użytkownicy mogą wyszukiwać, przeglądać i odnajdywać źródło danych, a następnie używać jego lokalizacji do łączenia się z nim za pomocą wybranej aplikacji lub narzędzia.

## <a name="supported-data-sources"></a>Obsługiwane źródła danych
Aby uzyskać listę aktualnie obsługiwanych źródeł danych, zobacz [DsR wykazu danych](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadane strukturalne
Podczas rejestrowania źródła danych narzędzie rejestracji wyodrębnia informacje o strukturze wybranych obiektów. Te informacje są określane jako metadane strukturalne.

Dla wszystkich obiektów te metadane strukturalne zawiera lokalizację obiektu, dzięki czemu użytkownicy, którzy odnajdują dane, mogą używać tych informacji do łączenia się z obiektem w wybranych przez siebie narzędziach klienta. Inne metadane strukturalne obejmują nazwę i typ obiektu oraz nazwę atrybutu/kolumny i typ danych.

## <a name="descriptive-metadata"></a>Metadane opisowe
Oprócz podstawowych metadanych strukturalnych, które są wyodrębniane ze źródła danych, narzędzie do rejestracji źródła danych wyodrębnia metadane opisowe. W przypadku usług SQL Server Analysis Services i usług SQL Server Reporting Services metadane są pobierane z właściwości Opisu udostępniane przez te usługi. W przypadku programu SQL Server\_wyodrębniane są wartości podane przy użyciu właściwości rozszerzonej opisu ms. W przypadku bazy danych Oracle narzędzie do rejestracji źródła\_danych\_wyodrębnia kolumnę COMMENTS z widoku WSZYSTKIE KOMENTARZE TAB.

Oprócz opisowych metadanych wyodrębnionych ze źródła danych użytkownicy mogą wprowadzać opisowe metadane przy użyciu narzędzia do rejestracji źródła danych. Użytkownicy mogą dodawać tagi i identyfikować ekspertów dla zarejestrowanych obiektów. Wszystkie te opisowe metadane są kopiowane do usługi wykaz danych wraz z metadanymi strukturalnymi.

## <a name="include-previews"></a>Dołączanie podglądów
Domyślnie tylko metadane są wyodrębniane ze źródeł danych i kopiowane do usługi wykaz danych, ale zrozumienie źródła danych jest często łatwiejsze, gdy można wyświetlić próbkę danych, które zawiera.

Za pomocą narzędzia do rejestracji źródła danych wykazu danych można dołączyć podgląd migawki danych w każdej tabeli i widoku, który jest zarejestrowany. Jeśli zdecydujesz się uwzględnić podglądy podczas rejestracji, narzędzie rejestracji zawiera maksymalnie 20 rekordów z każdej tabeli i widoku. Ta migawka jest następnie kopiowana do katalogu wraz z metadanymi strukturalnymi i opisowymi.

> [!NOTE]
> Szerokie tabele z dużą liczbą kolumn mogą mieć mniej niż 20 rekordów uwzględnionych w wersji zapoznawczej.
>
>

## <a name="include-data-profiles"></a>Uwzględnij profile danych
Podobnie jak w tym podglądu może zapewnić cenny kontekst dla użytkowników, którzy szukają źródeł danych w wykazie danych, w tym profil danych może ułatwić zrozumienie odnalezionych źródeł danych.

Za pomocą narzędzia do rejestracji źródła danych wykazu danych można dołączyć profil danych dla każdej tabeli i widoku, który jest zarejestrowany. Jeśli zdecydujesz się dołączyć profil danych podczas rejestracji, narzędzie do rejestracji zawiera zbiorcze statystyki dotyczące danych w każdej tabeli i widoku, w tym:

* Liczba wierszy i rozmiar danych w obiekcie.
* Data ostatniej aktualizacji danych i schematu obiektu.
* Liczba rekordów null i odrębnych wartości dla kolumn.
* Wartości minimalne, maksymalne, średnie i odchylenia standardowego dla kolumn.

Statystyki te są następnie kopiowane do katalogu wraz z metadanymi strukturalnymi i opisowymi.

> [!NOTE]
> Kolumny tekstowe i datowe nie zawierają statystyk dotyczących średnich ani odchyleń standardowych w profilu danych.
>
>

## <a name="update-registrations"></a>Aktualizowanie rejestracji
Rejestrowanie źródła danych sprawia, że wykrywalne w wykazie danych podczas korzystania z metadanych i opcjonalnej wersji zapoznawczej wyodrębnione podczas rejestracji. Jeśli źródło danych musi zostać zaktualizowane w katalogu (na przykład, jeśli schemat obiektu uległ zmianie, tabele pierwotnie wykluczone powinny zostać uwzględnione lub chcesz zaktualizować dane, które są zawarte w podglądach), narzędzie do rejestracji źródła danych można ponownie uruchomić.

Ponowne zarejestrowanie już zarejestrowanego źródła danych wykonuje operację scalania "upsert": istniejące obiekty są aktualizowane i tworzone są nowe obiekty. Wszelkie metadane dostarczone przez użytkowników za pośrednictwem portalu wykazu danych są zachowywane.

## <a name="summary"></a>Podsumowanie
Ponieważ kopiuje metadane strukturalne i opisowe ze źródła danych do usługi katalogu, rejestrowanie źródła danych w wykazie danych ułatwia odnajdywanie i zrozumienie danych. Po zarejestrowaniu źródła danych można dodawać adnotacje, zarządzać nimi i wykrywać je za pomocą portalu wykazu danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rejestrowania źródeł danych, zobacz samouczek [Wprowadzenie do usługi Azure Data Catalog.](data-catalog-get-started.md)
