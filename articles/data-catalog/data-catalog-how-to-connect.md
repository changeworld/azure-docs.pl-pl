---
title: Sposób nawiązywania połączenia ze źródłami danych | Dokumentacja firmy Microsoft
description: Artykule wyróżnianie sposób nawiązywania połączenia ze źródłami danych odnalezionymi w wykazie danych Azure.
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: b4dfc7a900e42b348c416d2d8c05b73fb15847ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-connect-to-data-sources"></a>Jak nawiązywać połączenie ze źródłami danych
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to usługa w chmurze pełni zarządzana, która służy jako system rejestracji i system odnajdowania źródeł danych przedsiębiorstwa. Innymi słowy **Azure Data Catalog** wszystkim chodzi o pomaga osób odnajdywania, zrozumienia i używania źródeł danych oraz organizacjom osiąganie większych zysków z ich istniejących danych. Kluczowym aspektem ten scenariusz jest przy użyciu danych — gdy użytkownik odnajduje źródła danych i zrozumienie ich przeznaczenia, następnym krokiem jest połączyć się ze źródłem danych umieszczanie jego danych do użycia.

## <a name="data-source-locations"></a>Lokalizacje źródeł danych
Podczas rejestrowania źródła danych **Azure Data Catalog** otrzymuje metadane dotyczące źródła danych. Te metadane zawiera szczegółowe informacje o lokalizacji źródła danych. Szczegółowe informacje o lokalizacji są różne źródła danych do źródła danych, ale zawsze będzie zawierać informacje wymagane do połączenia. Na przykład lokalizacji dla tabeli programu SQL Server zawiera nazwę serwera, nazwa bazy danych, nazwy schematu i nazwy tabeli, natomiast lokalizacji raportu usług SQL Server Reporting Services zawiera nazwę serwera i ścieżkę do raportu. Inne typy źródeł danych będą mieć lokalizacje, które odzwierciedlać struktury i możliwości w systemie źródłowym.

## <a name="integrated-client-tools"></a>Narzędzia zintegrowanego klienta
Najprostszym sposobem, aby połączyć się ze źródłem danych jest użycie "Otwórz w..." w menu **Azure Data Catalog** portalu. Rozwijalna lista opcji w celu nawiązania z wybranego zasobu danych.
Korzystając z widoku tile domyślne, to menu jest dostępne na każdego kafelka.

 ![Otwarcie tabeli programu SQL Server w programie Excel z kafelka zasobów danych](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Korzystając z widoku listy, jest dostępna na pasku wyszukiwania w górnej części okna portalu.

 ![Otwarcie raportu usług SQL Server Reporting Services w Menedżerze raportów z pasek wyszukiwania](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Obsługiwany klient aplikacji
Korzystając z "Otwórz w..." menu dla źródeł danych w portalu Azure Data Catalog, aplikacja kliencka poprawne musi być zainstalowany na komputerze klienckim.

| Otwórz w aplikacji | Rozszerzenie pliku / protokołu | Obsługiwana aplikacja wersji |
| --- | --- | --- |
| Excel |odc |Excel 2010 lub nowszej. |
| Program Excel (pierwszych 1000) |odc |Excel 2010 lub nowszej. |
| Power Query |.xlsx |Zainstalowany program Excel 2016 lub Excel 2010 lub Excel 2013 z dodatku Power Query dla dodatek programu Excel |
| Power BI Desktop |.pbix |Power BI Desktop lipca 2016 lub nowszy |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 lub nowszym z narzędzi programu SQL Server zainstalowana |
| Menedżer raportów |http:// |Zobacz [wymagania przeglądarki do programu SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Dane, narzędziami
Opcje dostępne w menu uzależniony od typu wybranego zasobu danych. Oczywiście nie wszystkie możliwe narzędzia mają być uwzględnieni w "Otwórz w..." menu, ale jest nadal łatwo połączyć się ze źródłem danych przy użyciu dowolnego narzędzia klienta. Po wybraniu zasobu danych w **Azure Data Catalog** portalu, pełną lokalizacji jest wyświetlana w okienku właściwości.

 ![Informacje o połączeniu dla tabeli programu SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Szczegółowe informacje dotyczące połączenia będą się różnić od typu źródła danych na typ źródła danych, ale informacje zawarte w portalu zostaną wyświetlone wszystkie elementy potrzebne do nawiązania połączenia źródła danych w narzędziu dowolnego klienta. Użytkownicy mogą kopiować szczegóły połączenia dla źródła danych, które zostały odnalezione, za pomocą **Azure Data Catalog**, umożliwiając im do pracy z danymi w wybranym narzędziu.

## <a name="connecting-and-data-source-permissions"></a>Uprawnienia źródeł danych i łączenie
Mimo że **Azure Data Catalog** sprawia, że źródła danych wykrywalny, dostęp do danych pozostaje się pod kontrolą właściciela źródła danych lub administratora. Odnajdywanie źródła danych w **Azure Data Catalog** nie daje użytkownikowi żadnych uprawnień dostępu do źródła danych sam.

W celu ułatwienia dla użytkowników, którzy odnajdywanie źródła danych, ale nie masz uprawnienia dostępu do danych, użytkownicy mogą podać informacje we właściwości żądania dostępu, gdy dodawanie adnotacji do źródła danych. Podane tu — łącznie z łącza do procesu lub punkt kontaktu dla uzyskania dostępu do źródła danych — informacje są prezentowane obok informacji o lokalizacji źródła danych w portalu.

 ![Informacje o połączeniu z żądania dostępu do instrukcji](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Podsumowanie
Rejestrowanie źródła danych z **Azure Data Catalog** sprawia, że dane wykrywalny przez skopiowanie metadanych strukturalnych i opisowy ze źródła danych do usługi wykazu. Po źródło danych zostało zarejestrowane i odnalezione, użytkownicy mogą łączyć się ze źródłem danych z **Azure Data Catalog** portal "Otwórz w..." " menu lub przy użyciu ich narzędzi danych wyboru.

## <a name="see-also"></a>Zobacz także
* [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md) samouczek krok po kroku szczegółowe informacje na temat nawiązywania połączenia ze źródłami danych.
