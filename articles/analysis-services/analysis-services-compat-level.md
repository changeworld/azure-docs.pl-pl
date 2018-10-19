---
title: Poziom zgodności w usłudze Azure Analysis Services modelu danych | Dokumentacja firmy Microsoft
description: Opis poziomu zgodności modelu tabelarycznego.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 87e6c19268ad8aeb486ce32cf8bfb668e4538a2a
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428219"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Poziom zgodności dla modeli tabelarycznych usług Analysis Services

*Poziom zgodności* odwołuje się do zachowania charakterystyczne dla wersji w aparat usług Analysis Services. Zmiany poziomu zgodności zazwyczaj pokrywa się z głównymi wersjami programu SQL Server. Tych zmian, także są implementowane w usług Azure Analysis Services w celu zachowania zgodności między obu platform. Poziom zgodności zmienia także funkcji efekt, które są dostępne w modelach tabelarycznych. Na przykład zapytanie bezpośrednie i metadane tabelaryczne obiektu mają różne implementacje w zależności od poziomu zgodności. Poziom zgodności jest określony w projekcie modelu tabelarycznego w programie Visual Studio (SSDT). Modele tabelaryczne utworzone w i zaimportowane z programu Power BI Desktop są na poziomie zgodności 1400 tylko.

Usługa Azure Analysis Services obsługują modele tabelaryczne na poziomie zgodności 1200 i 1400. 

Najnowsze poziom zgodności jest 1400. Ten poziom pokrywa się z programu SQL Server 2017 Analysis Services. Główne funkcje na poziomie zgodności 1400:

*  Nowe funkcje usługi łączności danych i importowania z obsługą interfejsów API TOM i skryptów TMSL. 
*  Przekształcanie danych i możliwości programu mashup danych za pomocą wyrażeń w obszarach pobieranie danych i M.
*  Miary obsługuje właściwości wierszy szczegółów przy użyciu wyrażenia języka DAX. Ta właściwość umożliwia narzędzi klienckich, takich jak Microsoft Excel, aby przejść do szczegółowych danych z raportu zagregowane. Na przykład użytkowników podczas wyświetlania całkowitą sprzedaż w regionie i miesiąc, mogą wyświetlać szczegóły zamówienia skojarzone. 
*  Zabezpieczenia na poziomie obiektu nazwy tabel i kolumn, oprócz zawartych w nich danych.
*  Rozszerzona obsługa niewyrównanych hierarchii.
*  Monitorowanie wydajności i ulepszeń.
  
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

## <a name="next-steps"></a>Kolejne kroki
  [Tworzenie modelu w portalu Azure](analysis-services-create-model-portal.md)   
  [Zarządzanie usług Analysis Services](analysis-services-manage.md)  
