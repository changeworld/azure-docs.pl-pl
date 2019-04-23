---
title: Rejestracja źródeł danych w usłudze Azure Data Catalog
description: W tym artykule opisano sposób rejestrowania źródła danych w usłudze Azure Data Catalog, w tym pola metadanych wyodrębnionych podczas rejestracji.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 57b9a040b875c584b126e2062e4938b37875a31b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002990"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Rejestracja źródeł danych w usłudze Azure Data Catalog
## <a name="introduction"></a>Wprowadzenie
Azure Data Catalog to w pełni zarządzana usługa w chmurze służąca jako system rejestracji i odnajdywania firmowych źródeł danych. Innymi słowy Data Catalog ułatwia odnajdywanie, zrozumienie i używanie źródeł danych, co zapewnia organizacjom osiąganie większych zysków z ich istniejących danych. Pierwszym krokiem do wprowadzania odnaleźć za pomocą wykazu danych źródła danych jest można zarejestrować tego źródła danych.

## <a name="register-data-sources"></a>Rejestrowanie źródeł danych (Rejestrowanie źródeł danych)
Rejestracja to proces wyodrębniania metadanych ze źródła danych oraz kopiowania tych danych do usługi Data Catalog. Dane pozostają w miejscu, w którym aktualnie się znajdują, i podlegają kontroli administratorów i zasadom obowiązującym w danym systemie.

Aby zarejestrować źródła danych, wykonaj następujące czynności:
1. W portalu usługi Azure Data Catalog uruchomić narzędzia rejestracji źródła danych wykazu danych. 
2. Zaloguj się przy użyciu konta służbowego przy użyciu tych samych poświadczeń usługi Azure Active Directory, których używasz do logowania do portalu.
3. Wybierz źródło danych, które chcesz zarejestrować.

Aby uzyskać więcej szczegółowych informacji krok po kroku, zobacz [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md) samouczka.

Po zarejestrowaniu źródła danych wykazu śledzi lokalizacji i indeksuje jego metadanych. Użytkownicy mogą wyszukiwanie, przeglądanie i odnajdywać źródła danych i następnie nawiązać z nim za pomocą aplikacji lub narzędzia wybranych przez nich za pomocą jego lokalizacji.

## <a name="supported-data-sources"></a>Obsługiwane źródła danych
Aby uzyskać listę aktualnie obsługiwanych źródeł danych, zobacz [DSR wykazu danych](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadane strukturalne
Po zarejestrowaniu źródła danych, narzędzie do rejestracji wyodrębnia informacje o strukturze wybranych obiektów. Te informacje nazywa się strukturalną metadanych.

Dla wszystkich obiektów te metadane strukturalne obejmuje lokalizacji obiektu, tak aby użytkownicy, którzy wykrywają dane można połączyć się z obiektem w narzędziach klienckich wybranych przez nich za pomocą tych informacji. Inne metadane strukturalne obejmują nazwę obiektu i typu, a następnie wpisz nazwy atrybutu/kolumny i danych.

## <a name="descriptive-metadata"></a>Metadane opisowe
Oprócz metadane strukturalne podstawowe, które zostały wyodrębnione ze źródła danych narzędzia rejestracji źródła danych wyodrębnia metadane opisowe. SQL Server Analysis Services i SQL Server Reporting Services metadanych jest pobierana z właściwości opisu udostępniane przez te usługi. Dla programu SQL Server, wartości za pomocą ms\_jest wyodrębniany rozszerzona właściwość opis. Narzędzia rejestracji źródła danych dla bazy danych Oracle, wyodrębnia kolumny COMMENTS ze wszystkimi\_kartę\_wyświetlanie KOMENTARZY.

Oprócz metadanych opisowych, które zostały wyodrębnione ze źródła danych użytkownicy mogą wprowadzać metadanych opisowych, za pomocą narzędzia rejestracji źródła danych. Użytkownicy mogą dodawać tagi i identyfikują one ekspertów dla obiektów, jest zarejestrowany. Te metadane opisowe są kopiowane do usługi Data Catalog wraz z metadane strukturalne.

## <a name="include-previews"></a>Obejmują wersje zapoznawcze
Domyślnie tylko metadanych jest wyodrębnione ze źródła danych i kopiowane do usługi Data Catalog, ale zrozumienie, które źródła danych jest często łatwiejsze gdy można wyświetlić przykładowe dane, które zawiera.

Za pomocą narzędzia rejestracji źródła danych wykazu danych, możesz dołączyć podgląd migawki danych w każdej tabeli i widoku, który jest zarejestrowany. Jeśli zdecydujesz się obejmują wersje zapoznawcze podczas rejestracji, narzędzie do rejestracji zawiera maksymalnie 20 rekordów z każdej tabeli, widoku. Ta migawka zostaną skopiowane do katalogu wraz z metadanymi strukturalnych i opisowe.

> [!NOTE]
> Szerokich tabel z dużą liczbą kolumn może być mniej niż 20 rekordy w ich w wersji zapoznawczej.
>
>

## <a name="include-data-profiles"></a>Dołącz profile danych
Zgodnie z tym Podgląd zapewniają kontekstowe przydatne dla użytkowników, którzy wyszukiwania źródeł danych w usłudze Data Catalog, w tym profilu danych może ułatwić zrozumienie źródła odnalezionych danych.

Za pomocą narzędzia rejestracji źródła danych wykazu danych, można dołączyć profil danych dla każdej tabeli i widoku, który jest zarejestrowany. Jeśli chcesz dołączyć profil danych podczas rejestracji, narzędzie do rejestracji zawiera zagregowanych danych statystycznych dotyczących danych w każdej tabeli i widok, w tym:

* Liczba wierszy i rozmiar danych w obiekcie.
* Data ostatniej aktualizacji danych i schematu obiektu.
* Liczba rekordów o wartości null i różne wartości w kolumnach.
* Minimalna, maksymalna, średnia i odchylenie standardowe wartości dla kolumn.

Te statystyki są kopiowane do katalogu, wraz z metadanymi strukturalnych i opisowe.

> [!NOTE]
> Tekst i Data kolumny nie dołączaj statystyki średnia lub odchylenie standardowe w swoim profilu danych.
>
>

## <a name="update-registrations"></a>Aktualizowanie rejestracji
Rejestrowanie źródła danych sprawia, że wykrywalne w usłudze Data Catalog podczas korzystania z metadanych i opcjonalnie wyodrębnione podczas rejestracji w wersji zapoznawczej. Jeśli źródło danych musi zostać zaktualizowana w katalogu (na przykład, jeśli schemat obiektu został zmieniony, początkowo wyłączone tabele mają zostać uwzględnione lub zaktualizować danych, który znajduje się w wersji zapoznawczych), można ponownie uruchomić narzędzia rejestracji źródła danych.

Ponowne zarejestrowanie źródła danych już zarejestrowany wykonuje operację "upsert" scalania: istniejących obiektów są aktualizowane, a nowe obiekty są tworzone. Wszystkie metadane, dostarczone przez użytkowników za pośrednictwem portalu usługi Data Catalog zostaną zachowane.

## <a name="summary"></a>Podsumowanie
Ponieważ metadane strukturalne i opisowe kopiowane ze źródła danych do usługi katalogu, rejestrowanie źródła danych w usłudze Data Catalog ułatwia odnajdywanie i zrozumienie. Po zarejestrowaniu źródła danych można dodawać adnotacje, zarządzanie i odnajdywanie przy użyciu portalu usługi Data Catalog.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rejestrowania źródeł danych, zobacz [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md) samouczka.
