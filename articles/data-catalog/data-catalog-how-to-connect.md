---
title: Sposób nawiązywania połączeń ze źródłami danych w usłudze Azure Data Catalog
description: Artykuł porad wyróżnianie sposób nawiązywania połączeń ze źródłami danych odnalezionymi za pomocą usługi Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: c64340491dba11870364610a6c2ff62e25c1328a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61001835"
---
# <a name="how-to-connect-to-data-sources"></a>Jak nawiązywać połączenie ze źródłami danych
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to usługa w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i system odnajdowania firmowych źródeł danych. Innymi słowy **usługi Azure Data Catalog** to przede wszystkim pomagając ludziom, odnajdywanie, zrozumienie i używanie źródeł danych oraz pomagając organizacjom pozwala zwiększyć wartość uzyskiwaną z ich istniejących danych. Kluczowe aspekty tego scenariusza jest korzystanie z danych — po użytkownik umożliwia odnalezienie źródła danych i zrozumienie jego przeznaczenie, następnym krokiem jest nawiązanie połączenia ze źródłem danych, aby umieścić swoje dane, które mają być używane.

## <a name="data-source-locations"></a>Lokalizacje źródeł danych
Podczas rejestracji źródła danych **usługi Azure Data Catalog** otrzymuje metadane dotyczące źródła danych. Te metadane zawiera szczegółowe informacje o lokalizacji źródła danych. Szczegółowe informacje o lokalizacji będą się różnić od źródła danych do źródła danych, ale zawsze będzie zawierać informacji potrzebnych do połączenia. Na przykład lokalizację dla tabeli programu SQL Server zawiera nazwę serwera, nazwę bazy danych, nazwy schematu i nazwę tabeli, natomiast lokalizację dla raportów programu SQL Server Reporting Services zawiera nazwę serwera i ścieżkę do raportu. Innych typów źródeł danych będzie mieć lokalizacje, które odzwierciedlają strukturę i funkcje w systemie źródłowym.

## <a name="integrated-client-tools"></a>Zintegrowanych narzędzi klienckich
Najprostszym sposobem, aby połączyć się ze źródłem danych jest użycie "Otwórz w..." w menu **usługi Azure Data Catalog** portalu. To menu wyświetla listę opcji do łączenia się z wybranego zasobu danych.
Korzystając z widoku tile domyślne, to menu jest dostępne na każdym kafelku.

 ![Otwieranie tabeli programu SQL Server w programie Excel za pomocą kafelka zasobów danych](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Korzystając z widoku listy, jest dostępna na pasku wyszukiwania w górnej części okna portalu.

 ![Otwieranie raportu usług SQL Server Reporting Services w programie Report Manager z paska wyszukiwania](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Obsługiwany klient aplikacji
Korzystając z "Otwórz w..." menu dla źródeł danych w portalu usługi Azure Data Catalog, aplikacja kliencka poprawne musi być zainstalowany na komputerze klienckim.

| Otwórz w aplikacji | Rozszerzenie pliku / protokołu | Wersje obsługiwanych aplikacji. |
| --- | --- | --- |
| Excel |odc |Program Excel 2010 lub nowszy |
| Excel (pierwsze 1000) |odc |Program Excel 2010 lub nowszy |
| Dodatek Power Query |.xlsx |Zainstalowany program Excel 2016 i Excel 2010 lub Excel 2013 za pomocą dodatku Power Query dla dodatku programu Excel |
| Power BI Desktop |.pbix |Usługa Power BI Desktop lipca 2016 lub nowszy |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 lub nowszym za pomocą narzędzi SQL Server zainstalowana |
| Menedżer raportów |http:// |Zobacz [wymagania dotyczące przeglądarki dla programu SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Dane z narzędzi
Opcje dostępne w menu będzie zależeć od typu zasobu danych, które aktualnie wybrany. Oczywiście nie wszystkie narzędzia możliwe będzie dostępna w "Otwórz w..." menu, ale jest nadal można łatwo połączyć się ze źródłem danych przy użyciu dowolnego narzędzia klienta. Po wybraniu zasobu danych w **usługi Azure Data Catalog** portalu, pełną lokalizacja będzie wyświetlana w okienku właściwości.

 ![Informacje o połączeniu dla tabeli programu SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Informacje dotyczące połączenia będą się różnić od typu źródła danych typu źródła danych, ale informacje zawarte w portalu prześle Ci wszystko, co jest potrzebne do połączenia ze źródłem danych w dowolnym klienckim narzędziu. Użytkownicy mogą kopiować szczegóły połączenia dla źródeł danych, które zostały wykryte, przy użyciu **usługi Azure Data Catalog**, umożliwiając im do pracy z danymi w wybranym narzędziu.

## <a name="connecting-and-data-source-permissions"></a>Łączenie i źródła uprawnień dotyczących danych
Mimo że **usługi Azure Data Catalog** sprawia, że źródła danych stała się wykrywalna, dostęp do danych sam pozostaje pod kontrolą właściciel lub administrator. Odnajdywanie źródła danych w **usługi Azure Data Catalog** nie daje użytkownikowi żadnych uprawnień dostępu do źródła danych, sam.

Aby ułatwić użytkownikom odnalezienie źródła danych, ale nie masz uprawnień dostępu do danych, użytkownicy mogą podać informacje we właściwości żądania dostępu podczas Dodawanie adnotacji do źródła danych. Informacje podane w tym miejscu, w tym łącza do procesu lub punkt kontaktu do uzyskania dostępu do źródła danych — są prezentowane wraz z informacji o lokalizacji źródła danych w portalu.

 ![Informacje o połączeniu podanych instrukcji dostęp na żądanie](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Podsumowanie
Rejestrowanie źródła danych za pomocą **usługi Azure Data Catalog** sprawia, że te dane wykrywalny przez skopiowanie metadane strukturalne i opisowy ze źródła danych do usługi katalogu. Gdy źródło danych zostanie zarejestrowane i odnalezione, użytkownicy mogą łączyć się źródło danych z **usługi Azure Data Catalog** portal "Otwórz w..." " menu lub przy użyciu narzędzi data wyboru.

## <a name="see-also"></a>Zobacz także
* [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md) samouczek krok po kroku szczegółowe informacje na temat sposobu łączenia ze źródłami danych.
