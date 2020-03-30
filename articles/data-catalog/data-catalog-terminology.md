---
title: Terminologia usługi Azure Data Catalog
description: Ten artykuł zawiera wprowadzenie do pojęć i terminów używanych w dokumentacji usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736295"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia usługi Azure Data Catalog

Ten artykuł zawiera wprowadzenie do pojęć i terminów używanych w dokumentacji usługi Azure Data Catalog.

## <a name="catalog"></a>Wykaz

Wykaz danych platformy Azure to oparte na chmurze repozytorium metadanych, w którym można zarejestrować źródła danych i zasoby danych. Katalog służy jako centralna lokalizacja magazynu dla metadanych strukturalnych wyodrębnionych ze źródeł danych i opisowych metadanych dodawanych przez użytkowników.

## <a name="data-source"></a>Źródło danych

Źródło danych to system lub kontener, który zarządza zasobami danych. Przykłady obejmują bazy danych PROGRAMU SQL Server, bazy danych Oracle, bazy danych SQL Server Analysis Services (tabelaryczne lub wielowymiarowe) i serwery SQL Server Reporting Services.

## <a name="data-asset"></a>Zasób danych

Zasoby danych są obiektami zawartymi w źródłach danych, które mogą być zarejestrowane w katalogu. Przykłady obejmują tabele i widoki programu SQL Server, tabele i widoki Oracle, miary usług SQL Server Analysis Services, wymiary i kluczowe wskaźniki wydajności oraz raporty SQL Server Reporting Services.

## <a name="data-asset-location"></a>Lokalizacja zasobu danych

Katalog przechowuje lokalizację źródła danych lub zasobu danych, który może służyć do łączenia się ze źródłem przy użyciu aplikacji klienckiej. Format i szczegóły lokalizacji różnią się w zależności od typu źródła danych. Na przykład tabela programu SQL Server może być identyfikowana za pomocą jego czteroczęściowej nazwy — nazwy serwera, nazwy bazy danych, nazwy schematu, nazwy obiektu — podczas gdy raport usług raportowania programu SQL Server można zidentyfikować za pomocą adresu URL.

## <a name="structural-metadata"></a>Metadane strukturalne

Metadane strukturalne to metadane wyodrębnione ze źródła danych opisującego strukturę zasobu danych. Obejmuje to lokalizację zasobów, jego nazwę i typ obiektu oraz dodatkowe cechy specyficzne dla typu. Na przykład metadane strukturalne dla tabel i widoków zawiera nazwy i typy danych dla kolumn obiektu.

## <a name="descriptive-metadata"></a>Metadane opisowe

Opisowe metadane to metadane opisujące cel lub zamiar zasobu danych. Zazwyczaj opisowe metadane są dodawane przez użytkowników katalogu za pomocą portalu usługi Azure Data Catalog, ale można je również wyodrębnić ze źródła danych podczas rejestracji. Na przykład narzędzie rejestracji usługi Azure Data Catalog wyodrębni opisy z właściwości Description w usługach SQL Server Analysis Services i SQL Server Reporting Services oraz z [właściwości rozszerzonej ms_description](https://technet.microsoft.com/library/ms190243.aspx) w bazach danych programu SQL Server, jeśli te właściwości zostały wypełnione wartościami.

## <a name="request-access"></a>Żądanie dostępu

Opisowe metadane zasobu danych mogą zawierać informacje o sposobie żądania dostępu do zasobu danych lub źródła danych. Te informacje są prezentowane z lokalizacją zasobu danych i mogą zawierać jedną lub więcej z następujących opcji:

* Adres e-mail użytkownika lub zespołu odpowiedzialnego za udzielenie dostępu do źródła danych.
* Adres URL udokumentowanego procesu, który użytkownicy muszą wykonać, aby uzyskać dostęp do źródła danych.
* Adres URL narzędzia do zarządzania tożsamościami i dostępem (takiego jak Microsoft Identity Manager), którego można użyć w celu uzyskania dostępu do źródła danych.
* Wpis tekstu wolnego, który opisuje, jak użytkownicy mogą uzyskać dostęp do źródła danych.

## <a name="preview"></a>Wersja zapoznawcza

Wersja zapoznawcza w usłudze Azure Data Catalog to migawka maksymalnie 20 rekordów, które można wyodrębnić ze źródła danych podczas rejestracji i przechowywać w katalogu z metadanymi zasobu danych. Wersja zapoznawcza może pomóc użytkownikom, którzy odnajdują zasób danych, lepiej zrozumieć jego funkcję i cel. Innymi słowy, wyświetlanie przykładowych danych może być bardziej wartościowe niż wyświetlanie tylko nazw kolumn i typów danych.
Podglądy są obsługiwane tylko dla tabel i widoków i muszą być jawnie wybrane przez użytkownika podczas rejestracji.

## <a name="data-profile"></a>Profil danych

Profil danych w usłudze Azure Data Catalog to migawka metadanych na poziomie tabeli i kolumny dotyczących zarejestrowanego zasobu danych, który można wyodrębnić ze źródła danych podczas rejestracji i przechowywać w katalogu z metadanymi zasobu danych. Profil danych może pomóc użytkownikom, którzy odnajdują zasób danych, lepiej zrozumieć jego funkcję i cel. Podobnie jak w wersji zapoznawczej, profile danych muszą być jawnie wybrane przez użytkownika podczas rejestracji.

> [!NOTE]
> Wyodrębnianie profilu danych może być kosztowną operacją dla dużych tabel i widoków i może znacznie wydłużyć czas wymagany do zarejestrowania źródła danych.


## <a name="user-perspective"></a>Perspektywa użytkownika

W usłudze Azure Data Catalog każdy użytkownik może podać opisowe metadane zarejestrowanego zasobu danych. Każdy użytkownik ma odrębne spojrzenie na dane i ich wykorzystanie. Na przykład administrator odpowiedzialny za serwer może podać szczegóły swojej umowy dotyczącej poziomu usług (SLA) lub okien kopii zapasowej; zarządca danych może dostarczyć linki do dokumentacji dla procesów biznesowych, które obsługuje dane; a analityk może podać opis w terminach, które są najbardziej istotne dla innych analityków i które mogą być najbardziej wartościowe dla tych użytkowników, którzy muszą odkryć i zrozumieć dane.

Każda z tych perspektyw jest z natury cenne i z usługi Azure Data Catalog każdy użytkownik może dostarczyć informacji, które mają znaczenie dla nich, podczas gdy wszyscy użytkownicy mogą używać tych informacji do zrozumienia danych i ich przeznaczenia.

## <a name="expert"></a>Ekspert

Ekspert jest użytkownikiem, który został zidentyfikowany jako posiadający świadomą perspektywę "eksperta" dla zasobu danych. Każdy użytkownik może dodać siebie lub innego użytkownika jako eksperta dla zasobu. Jest wymieniony jako ekspert nie przekazuje żadnych dodatkowych uprawnień w usłudze Azure Data Catalog; pozwala użytkownikom łatwo zlokalizować te perspektywy, które są najbardziej przydatne podczas przeglądania metadanych opisowych zasobu.

## <a name="owner"></a>Właściciel

Właściciel jest użytkownikiem, który ma dodatkowe uprawnienia do zarządzania zasobem danych w usłudze Azure Data Catalog. Użytkownicy mogą przejąć na własność zarejestrowane zasoby danych, a właściciele mogą dodawać innych użytkowników jako współwłaścicieli. Aby uzyskać więcej informacji, zobacz [Jak zarządzać zasobami danych](data-catalog-how-to-manage.md)  

> [!NOTE]
> Własność i zarządzanie są dostępne tylko w wersji standardowej usługi Azure Data Catalog.

## <a name="registration"></a>Rejestracja

Rejestracja jest aktem wyodrębniania metadanych zasobów danych ze źródła danych i kopiowania ich do usługi Azure Data Catalog. Zasoby danych, które zostały zarejestrowane, mogą być następnie opisywane i odnajdowane.

## <a name="next-steps"></a>Następne kroki

[Szybki start: tworzenie wykazu danych platformy Azure](data-catalog-get-started.md) 
