---
title: Terminologia usługi Azure Data Catalog
description: Ten artykuł zawiera wprowadzenie do pojęć i terminów używanych w dokumentacji usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: a6f2cf1dcee6a85376c8d767e57c504b6b246e5d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011388"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia usługi Azure Data Catalog

Ten artykuł zawiera wprowadzenie do pojęć i terminów używanych w dokumentacji usługi Azure Data Catalog.

## <a name="catalog"></a>Wykaz

Usługa Azure Data Catalog to repozytorium metadanych opartych na chmurze, w danych, które można zarejestrować źródła i dane zasobów. Katalog służy jako lokalizację magazynu centralnego metadane strukturalne wyodrębnione ze źródła danych i metadanych opisowych, dodane przez użytkowników.

## <a name="data-source"></a>Źródło danych

Źródło danych jest system lub kontener, który zarządza zasobami danych. Przykłady obejmują baz danych programu SQL Server, baz danych Oracle, SQL Server Analysis Services bazy danych (tabelarycznymi lub wielowymiarowymi) i serwerów SQL Server Reporting Services.

## <a name="data-asset"></a>Zasób danych

Zasoby danych są obiektów zawartych w źródeł danych, które mogą być zarejestrowane w wykazie. Przykłady obejmują tabel programu SQL Server i widoków, Oracle tabele i widoki, SQL Server Analysis Services miary, wymiary i kluczowe wskaźniki wydajności i raporty usług SQL Server Reporting Services.

## <a name="data-asset-location"></a>Lokalizacja zasobów danych

Katalog przechowywana lokalizacja źródła danych lub zasobów danych, która może służyć do połączenia ze źródłem za pomocą aplikacji klienckiej. Format i szczegółowe informacje o lokalizacji różnią się zależnie od typu źródła danych. Na przykład tabeli programu SQL Server można zidentyfikować przez nazwę czteroczęściową — nazwa serwera, nazwa bazy danych, nazwę schematu, nazwa obiektu — a raportem usługi raportowania serwera SQL można zidentyfikować przez jej adresu URL.

## <a name="structural-metadata"></a>Metadane strukturalne

Metadane strukturalne jest metadanych wyodrębnionych ze źródła danych, które opisuje strukturę zasobu danych. W tym lokalizacji zasobów, jego nazwa obiektu i typu oraz dodatkowe właściwości specyficzne dla danego typu. Na przykład metadane strukturalne dla tabel i widoków zawiera nazwy i typy danych dla kolumny obiektu.

## <a name="descriptive-metadata"></a>Metadane opisowe

Metadane opisowe są metadane opisujące przeznaczenie lub celem zasobu danych. Zazwyczaj opisowymi metadanymi jest dodawany przez użytkowników wykazu za pomocą portalu usługi Azure Data Catalog, ale ona również można wyodrębnić ze źródła danych podczas rejestracji. Na przykład narzędzie do rejestracji usługi Azure Data Catalog będzie prowadzenie opisy właściwości Opis w SQL Server Analysis Services i SQL Server Reporting Services i z [ms_description rozszerzona właściwość](https://technet.microsoft.com/library/ms190243.aspx) w języku SQL Baz danych serwera, jeśli te właściwości są wypełnione wartościami.

## <a name="request-access"></a>Żądaj dostępu

Metadane opisowe do zasobu danych może zawierać informacje dotyczące żądania dostępu do zasobu danych i źródła danych. Te informacje są prezentowane z lokalizacją zasobów danych i może zawierać co najmniej jeden z następujących opcji:

* Adres e-mail użytkownika lub zespołu odpowiedzialnego za udzielanie dostępu do źródła danych.
* Adres URL udokumentowanego procesu, który użytkownicy muszą wykonać w celu uzyskania dostępu do źródła danych.
* Adres URL tożsamość i dostęp do narzędzia do zarządzania (takich jak Microsoft Identity Manager), który może służyć do uzyskiwania dostępu do źródła danych.
* Wpis dowolny tekst, który opisuje, w jaki sposób użytkownicy będą mogli uzyskać dostępu do źródła danych.

## <a name="preview"></a>Wersja zapoznawcza

(Wersja zapoznawcza) w usłudze Azure Data Catalog jest migawką z maksymalnie 20 rekordów, które mogą być wyodrębnione ze źródła danych podczas rejestracji i przechowywane w katalogu z metadanymi zasobów danych. Może pomóc w wersji zapoznawczej użytkownicy, którzy odnajdywanie zasobów danych, lepiej zrozumieć jego funkcji i celów. Innymi słowy wyświetlanie przykładowych danych może być bardziej wartościowa niż tylko nazwy kolumn i typy danych.
Wersje zapoznawcze są obsługiwane tylko dla tabel i widoków i należy jawnie wybrać przez użytkownika podczas rejestracji.

## <a name="data-profile"></a>Profil danych

Profil danych w usłudze Azure Data Catalog jest migawką z tabeli i na poziomie kolumny metadanych dotyczących zasobów danych zarejestrowanych, które mogą być wyodrębnione ze źródła danych podczas rejestracji i przechowywane w katalogu z metadanymi zasobów danych. Profil danych może pomóc użytkownikom, którzy odnajdywanie zasobów danych lepiej zrozumieć jego funkcji i celów. Podobnie jak w wersjach zapoznawczych, profile danych musi być jawnie wybierane przez użytkownika podczas rejestracji.

> [!NOTE]
> Trwa wyodrębnianie profil danych może być kosztowna operacja dla dużych tabel i widoków i może znacznie zwiększyć czas wymagany do rejestrowania źródła danych.


## <a name="user-perspective"></a>Perspektywy użytkownika.

W usłudze Azure Data Catalog każdy użytkownik, można zapewnić opisowymi metadanymi zarejestrowanego zasobu danych. Każdy użytkownik ma różne perspektywą danych i jego użycia. Na przykład administrator odpowiedzialny za serwerem może dostarczyć szczegóły umowy dotyczącej poziomu usług (SLA) lub kopii zapasowej systemu windows; Zarządca danych może zapewnić linki do dokumentacji firmy przetwarza obsługuje danych; a analityk może dostarczyć opis w zasadach, które są najbardziej odpowiednie do innych analityków i który może być najbardziej przydatna dla tych użytkowników, którzy potrzebują, aby odnaleźć i zrozumieć dane.

Każdy z tych perspektyw jest natury cenne i za pomocą usługi Azure Data Catalog każdy użytkownik może zapewnić informacje, które są zrozumiałe dla nich, podczas gdy wszyscy użytkownicy mogą używać tych informacji, aby zrozumieć dane i jej przeznaczenie.

## <a name="expert"></a>Ekspert

Ekspert jest użytkownik, który został zidentyfikowany jako posiadające świadome perspektywy "ekspertów" dla zasobu danych. Każdy użytkownik, można dodać samodzielnie lub innemu użytkownikowi, jako ekspertów dla zasobu. Są wyświetlane jako ekspertem nie obejmują żadnych dodatkowych uprawnień w usłudze Azure Data Catalog; Umożliwia użytkownikom łatwy dostęp do tych perspektyw, które z największym prawdopodobieństwem mogą być przydatne podczas przeglądania opisowymi metadanymi elementu zawartości.

## <a name="owner"></a>Właściciel

Właścicielem jest użytkownik, który ma dodatkowe uprawnienia do zarządzania zasobu danych w usłudze Azure Data Catalog. Użytkownicy mogą przejąć prawo własności zasobów danych zarejestrowanych i właścicieli można dodać innym użytkownikom jako współwłaścicieli. Aby uzyskać więcej informacji, zobacz [jak zarządzać zasobami danych](data-catalog-how-to-manage.md)  

> [!NOTE]
> Prawo własności i zarządzania są dostępne tylko w Standard Edition usługi Azure Data Catalog.

## <a name="registration"></a>Rejestracja

Czynność wyodrębniania metadanych zasobu danych ze źródła danych i skopiować go do usługi Azure Data Catalog jest rejestracja. Można następnie adnotacji zasobów danych, które zostały zarejestrowane i odnalezione.

## <a name="next-steps"></a>Kolejne kroki

[Szybki start: Tworzenie usługi Azure Data Catalog](data-catalog-get-started.md) 
