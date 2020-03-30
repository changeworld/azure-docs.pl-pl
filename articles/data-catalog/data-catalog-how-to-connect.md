---
title: Jak połączyć się ze źródłami danych w usłudze Azure Data Catalog
description: Artykuł in jakywanie przedstawiający sposób łączenia się ze źródłami danych wykrytymi za pomocą usługi Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976782"
---
# <a name="how-to-connect-to-data-sources"></a>Jak nawiązywać połączenie ze źródłami danych
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i systemu odnajdowania dla źródeł danych przedsiębiorstwa. Innymi słowy **usługa Azure Data Catalog** ułatwia ludziom odkrywanie, zrozumienie i używanie źródeł danych oraz pomaga organizacjom w pozyskiwaniu większej wartości z istniejących danych. Kluczowym aspektem tego scenariusza jest przy użyciu danych — gdy użytkownik odnajduje źródło danych i rozumie jego przeznaczenie, następnym krokiem jest połączenie się ze źródłem danych, aby umieścić swoje dane do użycia.

## <a name="data-source-locations"></a>Lokalizacje źródeł danych
Podczas rejestracji źródła danych **usługa Azure Data Catalog** odbiera metadane dotyczące źródła danych. Te metadane zawierają szczegóły lokalizacji źródła danych. Szczegóły lokalizacji będą się różnić w zależności od źródła danych, ale zawsze będą zawierać informacje potrzebne do nawiązania połączenia. Na przykład lokalizacja tabeli programu SQL Server zawiera nazwę serwera, nazwę bazy danych, nazwę schematu i nazwę tabeli, podczas gdy lokalizacja raportu programu SQL Server Reporting Services zawiera nazwę serwera i ścieżkę do raportu. Inne typy źródeł danych będą miały lokalizacje, które odzwierciedlają strukturę i możliwości systemu źródłowego.

## <a name="integrated-client-tools"></a>Zintegrowane narzędzia klienckie
Najprostszym sposobem na połączenie się ze źródłem danych jest użycie "Otwórz w..." w portalu **usługi Azure Data Catalog.** W tym menu wyświetlana jest lista opcji łączenia się z wybranym zasobem danych.
W przypadku korzystania z domyślnego widoku kafelków to menu jest dostępne na każdym kafelku.

 ![Otwieranie tabeli programu SQL Server w programie Excel na kafelku zasobu danych](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

W przypadku korzystania z widoku listy menu jest dostępne na pasku wyszukiwania w górnej części okna portalu.

 ![Otwieranie raportu usług SQL Server Reporting Services w Menedżerze raportów](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Obsługiwane aplikacje klienckie
Podczas korzystania z "Otwórz w..." dla źródeł danych w portalu usługi Azure Data Catalog, na komputerze klienckim musi być zainstalowana poprawna aplikacja kliencka.

| Otwórz w aplikacji | Rozszerzenie pliku / protokół | Obsługiwane wersje aplikacji |
| --- | --- | --- |
| Excel |Odc |Program Excel 2010 lub nowszy |
| Excel (top 1000) |Odc |Program Excel 2010 lub nowszy |
| Power Query |xlsx |Excel 2016 lub Excel 2010 lub Excel 2013 z zainstalowanym dodatkiem Dodatku Power Query dla programu Excel |
| Power BI Desktop |.pbix |Usługa Power BI Desktop w lipcu 2016 r. lub nowsza |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 lub nowsze z zainstalowanymi narzędziami programu SQL Server |
| Menedżer raportów |http:// |Zobacz [wymagania dotyczące przeglądarki dla usług SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Twoje dane, twoje narzędzia
Opcje dostępne w menu zależą od typu aktualnie wybranego zasobu danych. Oczywiście nie wszystkie możliwe narzędzia zostaną uwzględnione w "Otwórz w..." menu, ale nadal można łatwo połączyć się ze źródłem danych za pomocą dowolnego narzędzia klienckiego. Gdy zasób danych jest zaznaczony w portalu **usługi Azure Data Catalog,** pełna lokalizacja jest wyświetlana w okienku właściwości.

 ![Informacje o połączeniu dla tabeli programu SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Szczegóły informacji o połączeniu będą się różnić od typu źródła danych do typu źródła danych, ale informacje zawarte w portalu daje wszystko, czego potrzebujesz, aby połączyć się ze źródłem danych w dowolnym narzędziu klienta. Użytkownicy mogą kopiować szczegóły połączenia dla źródeł danych, które odkryli przy użyciu **usługi Azure Data Catalog,** umożliwiając im pracę z danymi w wybranym narzędziu.

## <a name="connecting-and-data-source-permissions"></a>Uprawnienia do łączenia i źródła danych
Mimo że **usługa Azure Data Catalog** sprawia, że źródła danych są wykrywalne, dostęp do samych danych pozostaje pod kontrolą właściciela lub administratora źródła danych. Odnajdowanie źródła danych w **usłudze Azure Data Catalog** nie daje użytkownikowi żadnych uprawnień dostępu do samego źródła danych.

Aby ułatwić użytkownikom, którzy odnajdują źródło danych, ale nie mają uprawnień dostępu do jego danych, użytkownicy mogą podać informacje we właściwości Dostęp do żądania podczas dodawania adnotacji do źródła danych. Informacje podane tutaj – w tym linki do procesu lub punktu kontaktowego w celu uzyskania dostępu do źródła danych – są prezentowane wraz z informacjami o lokalizacji źródła danych w portalu.

 ![Informacje o połączeniu z instrukcjami dostępu do żądania pod warunkiem](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Podsumowanie
Rejestrowanie źródła danych w **usłudze Azure Data Catalog** sprawia, że dane można odnajdyć, kopiując metadane strukturalne i opisowe ze źródła danych do usługi Catalog. Po zarejestrowaniu i wykryciu źródła danych użytkownicy mogą łączyć się ze źródłem danych z portalu **usługi Azure Data Catalog** "Otwórz w..."" menu lub za pomocą wybranych narzędzi danych.

## <a name="see-also"></a>Zobacz też
* [Wprowadzenie do samouczka usługi Azure Data Catalog, aby](data-catalog-get-started.md) uzyskać szczegółowe informacje o tym, jak połączyć się ze źródłami danych.
