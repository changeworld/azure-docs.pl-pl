---
title: Model danych, poziom zgodności w usłudze Azure Analysis Services | Dokumentacja firmy Microsoft
description: Opis poziomu zgodności modelu tabelarycznego.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d68d544a66448fbbf193ff53fa43e179b1edb706
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602071"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Poziom zgodności dla modele tabelaryczne usług Analysis Services

*Poziom zgodności* odwołuje się do zachowania specyficzne dla wersji aparatu usług Analysis Services. Zmiany poziomu zgodności pokrywają się zwykle z główne wersje programu SQL Server. Te zmiany są również implementowana w usług Azure Analysis Services w celu zachowania zgodności między obu platform. Poziom zgodności zmienia również wpływ funkcji dostępnych w modeli tabelarycznych. Na przykład zapytania bezpośredniego i metadanych tabelarycznych obiektów mają implementacje różne w zależności od poziomu zgodności. Poziom zgodności jest określony w projekcie modelu tabelarycznego w Visual Studio (SSDT). Modele tabelaryczne tworzone w i importowane z programu Power BI Desktop są tylko na poziomie 1400 zgodności.

Azure Analysis Services obsługuje modele tabelaryczne poziomie zgodności 1200 i 1400. 

Najnowsze poziom zgodności jest 1400. Ten poziom pokrywa się z usługami analizy serwera SQL w 2017 r. Główne funkcje w 1400 poziom zgodności:

*  Nowe funkcje usługi łączności danych, a następnie zaimportuj z obsługą TOMASZ interfejsów API i skryptów TMSL. 
*  Przekształcenia danych i możliwości mashup danych za pomocą wyrażenia pobieranie danych i M.
*  Środki obsługuje właściwości wiersze szczegółów za pomocą wyrażenia języka DAX. Ta właściwość umożliwia klienta narzędzi, takich jak Microsoft Excel, aby następnie przejść do szczegółowych danych z zagregowane raportu. Na przykład przy przeglądaniu łączna sprzedaż dla regionu i miesiąc, można wyświetlić szczegółów skojarzone zamówienia. 
*  Zabezpieczenia na poziomie obiektu dla nazwy tabel i kolumn, oprócz danych w tych.
*  Rozszerzona obsługa niewyrównane hierarchie.
*  Wydajność i udoskonalenia funkcji monitorowania.
  
## <a name="set-compatibility-level"></a>Ustaw poziom zgodności 
 Podczas tworzenia nowego projektu modelu tabelarycznego programu SSDT, możesz określić poziom zgodności na **Projektant modelu tabelarycznego** okna dialogowego. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 W przypadku wybrania **nie pokazuj ponownie tego komunikatu** , wszystkie kolejne projekty Użyj określony jako domyślny poziom zgodności. Można zmienić domyślny poziom zgodności programu SSDT w **narzędzia** > **opcje**.  
  
 Aby uaktualnić istniejący projekt modelu tabelarycznego programu SSDT, ustaw **poziom zgodności** właściwości w modelu **właściwości** okna. Zachowaj w uwadze, poziom zgodności uaktualnienia jest nieodwracalna.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Sprawdź poziom zgodności bazy danych modelu tabelarycznego w programie SQL Server Management Studio 
 W programie SSMS, kliknij prawym przyciskiem myszy nazwę bazy danych > **właściwości** > **poziom zgodności**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Sprawdź obsługiwany poziom zgodności dla serwera w programie SSMS  
 W programie SSMS, kliknij prawym przyciskiem myszy nazwę serwera > **właściwości** > **obsługiwany poziom zgodności**.  
  
 Ta właściwość określa najwyższy poziom zgodności bazy danych, które zostanie uruchomione na serwerze (z wyjątkiem wersji zapoznawczej). Nie można zmienić poziomu zgodności obsługiwanych.  

## <a name="next-steps"></a>Kolejne kroki
  [Tworzenie modelu w portalu Azure](analysis-services-create-model-portal.md)   
  [Zarządzanie usług Analysis Services](analysis-services-manage.md)  
