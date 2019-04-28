---
title: Poziom zgodności w usłudze Azure Analysis Services modelu danych | Dokumentacja firmy Microsoft
description: Opis poziomu zgodności modelu tabelarycznego.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032619"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Poziom zgodności dla modeli tabelarycznych usług Analysis Services

*Poziom zgodności* odwołuje się do zachowania charakterystyczne dla wersji w aparat usług Analysis Services. Zmiany poziomu zgodności zazwyczaj pokrywa się z głównymi wersjami programu SQL Server. Tych zmian, także są implementowane w usług Azure Analysis Services w celu zachowania zgodności między obu platform. Poziom zgodności zmienia także funkcji efekt, które są dostępne w modelach tabelarycznych. Na przykład zapytanie bezpośrednie i metadane tabelaryczne obiektu mają różne implementacje w zależności od poziomu zgodności. Poziom zgodności jest określony w projekcie modelu tabelarycznego w programie Visual Studio (SSDT).

Usługa Azure Analysis Services obsługują modele tabelaryczne na poziomie zgodności 1200 i 1400. Najnowsze poziom zgodności jest 1400. Ten poziom pokrywa się z programu SQL Server 2017 Analysis Services. Główne funkcje na poziomie zgodności 1400:

*  Nowe funkcje usługi łączności danych i importowania z obsługą interfejsów API TOM i skryptów TMSL. 
*  Przekształcanie danych i możliwości programu mashup danych za pomocą wyrażeń w obszarach pobieranie danych i M.
*  Miary obsługuje właściwości wierszy szczegółów przy użyciu wyrażenia języka DAX. Ta właściwość umożliwia narzędzi klienckich, takich jak Microsoft Excel, aby przejść do szczegółowych danych z raportu zagregowane. Na przykład użytkowników podczas wyświetlania całkowitą sprzedaż w regionie i miesiąc, mogą wyświetlać szczegóły zamówienia skojarzone. 
*  Zabezpieczenia na poziomie obiektu nazwy tabel i kolumn, oprócz zawartych w nich danych.
*  Rozszerzona obsługa niewyrównanych hierarchii.
*  Monitorowanie wydajności i ulepszeń.

> [!NOTE]
> Usługa Azure Analysis Services obsługuje zaimportowane pliki programu Power BI Desktop na poziomie zgodności 1465. Jednak Importuj z usługi Power BI Desktop funkcję, która zawsze była w wersji zapoznawczej, został wycofany i usunięta z usługi w marca 2019 r. Istniejące modele na poziomie zgodności 1465 w dalszym ciągu obsługiwany.  


## <a name="set-compatibility-level"></a>Ustaw poziom zgodności

 Podczas tworzenia nowego projektu modelu tabelarycznego w programie SSDT, można określić poziom zgodności na **Projektant modeli tabelarycznych** okna dialogowego. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Jeśli wybierzesz **nie pokazuj tego komunikatu ponownie** opcja, wszystkie projekty kolejne użycie określony jako domyślny poziom zgodności. Możesz zmienić domyślny poziom zgodności w programie SSDT w **narzędzia** > **opcje**.  
  
 Aby uaktualnić istniejący projekt modelu tabelarycznego w programie SSDT, należy ustawić **poziom zgodności** właściwości w modelu **właściwości** okna. Należy pamiętać, poziom zgodności uaktualniania jest nieodwracalne.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Sprawdź poziom zgodności bazy danych modelu tabelarycznego w programie SQL Server Management Studio 

 W programie SSMS kliknij prawym przyciskiem myszy nazwę bazy danych > **właściwości** > **poziom zgodności**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Sprawdź obsługiwany poziom zgodności dla serwera w programie SSMS  

 W programie SSMS kliknij prawym przyciskiem myszy nazwę serwera > **właściwości** > **obsługiwany poziom zgodności**.  
  
 Ta właściwość określa najwyższy poziom zgodności bazy danych, które będą uruchamiane na serwerze (z wyjątkiem wersji zapoznawczej). Nie można zmienić poziomu zgodności obsługiwanych.  

> [!NOTE]
> W programie SSMS podczas połączenia z serwerem usług Azure Analysis Services **obsługiwany poziom zgodności** pokaże właściwość **1200**. Jest to znany problem i zostanie rozwiązany w przyszłych SSMS aktualizacji. Po usunięciu, ta właściwość będzie wyświetlana najwyższego poziomu obsługiwany zgodność.

## <a name="next-steps"></a>Kolejne kroki

  [Zarządzanie usług Analysis Services](analysis-services-manage.md)  
