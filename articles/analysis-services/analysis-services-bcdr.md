---
title: Azure wysoką dostępność usług Analysis Services | Dokumentacja firmy Microsoft
description: Zapewnienie wysokiej dostępności usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db8f8b9b5af1583662418f774b2eb141bea53ffa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596740"
---
# <a name="analysis-services-high-availability"></a>Wysoka dostępność usługi analizy
W tym artykule opisano, zapewniając wysoką dostępność dla serwerów usług Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Zapewnienie wysokiej dostępności podczas przerw w działaniu usługi
Gdy przypadkach centrum danych platformy Azure mogą mieć awarii. W przypadku wystąpienia awarii powoduje zakłócenia biznesowych, może trwać kilka minut lub może trwać godzin. Wysoka dostępność najczęściej jest to osiągane z nadmiarowością serwera. Z usług Azure Analysis Services można osiągnąć nadmiarowość przez utworzenie dodatkowych, pomocnicze serwery w regionach co najmniej jeden. Podczas tworzenia nadmiarowych serwerów, aby upewnić się, że dane i metadane na tych serwerach jest zsynchronizowana z serwerem w regionie, który przeszedł do trybu offline, można wykonywać następujące czynności:

* Wdrażanie modeli do nadmiarowego serwerów w różnych regionach. Ta metoda wymaga przetwarzania danych na serwerze podstawowym i nadmiarowego serwerów w równolegle, zapewniając wszystkie serwery są w synchronizacji.

* [Kopia zapasowa](analysis-services-backup.md) baz danych z serwera podstawowego i przywracania na serwerach nadmiarowe. Na przykład można zautomatyzować co noc kopii zapasowych do magazynu Azure i przywrócić na inne serwery nadmiarowego w różnych regionach. 

W obu przypadkach Jeśli serwer podstawowy ulegnie awarii, należy zmienić parametry połączenia w raportowania klientom na łączenie się z serwerem w różnych regionalnych centrach danych. Tej zmiany należy traktować jako ostateczność i występuje tylko w przypadku awarii centrum danych w wyniku katastrofy regionalne. Jest bardziej prawdopodobne, awarii centrum danych podstawowego serwera hostingu przybyły trybu online przed można zaktualizować połączenia na wszystkich klientach. 

Aby uniknąć konieczności zmiany parametrów połączenia na klientach raportowania, należy utworzyć serwer [alias](analysis-services-server-alias.md) serwera podstawowego. Jeśli serwer podstawowy ulegnie awarii, można zmienić aliasu, aby wskazywały serwer nadmiarowego w innym regionie. Można zautomatyzować alias do nazwy serwera, kodowania kontroli kondycji punktu końcowego na serwerze podstawowym. W przypadku niepowodzenia sprawdzania kondycji tego samego punktu końcowego można nakazać serwerowi nadmiarowego w innym regionie. 

## <a name="related-information"></a>Informacje pokrewne
[Kopia zapasowa i przywracanie](analysis-services-backup.md)   
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md)   
[Nazwy serwerów aliasu](analysis-services-server-alias.md) 

