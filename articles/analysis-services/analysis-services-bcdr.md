---
title: Wysoka dostępność usług Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak usługi Azure Analysis Services zapewnia wysoką dostępność podczas przerw w świadczeniu usług.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e750dce804ea93f3d3068ffd36bc7a73a50906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573360"
---
# <a name="analysis-services-high-availability"></a>Wysoka dostępność usług Analysis Services

W tym artykule opisano zapewnienie wysokiej dostępności dla serwerów usług Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zapewnienie wysokiej dostępności podczas przerwy w świadczeniu usług

Chociaż rzadko centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje zakłócenia biznesowe, które mogą trwać kilka minut lub może trwać wiele godzin. Wysoka dostępność jest najczęściej osiągana dzięki nadmiarowości serwerów. Dzięki usłudze Azure Analysis Services można uzyskać nadmiarowość, tworząc dodatkowe serwery pomocnicze w co najmniej jednym regionie. Podczas tworzenia serwerów nadmiarowych, aby zapewnić, że dane i metadane na tych serwerach są synchronizowane z serwerem w regionie, który przeszedł w tryb offline, można:

* Wdrażanie modeli na serwerach nadmiarowych w innych regionach. Ta metoda wymaga przetwarzania danych zarówno na serwerze podstawowym, jak i serwerach nadmiarowych równolegle, zapewniając, że wszystkie serwery są zsynchronizowane.

* [Tworzenie kopii zapasowych](analysis-services-backup.md) baz danych z serwera podstawowego i przywracanie na serwerach nadmiarowych. Na przykład można zautomatyzować nocne kopie zapasowe do magazynu platformy Azure i przywrócić do innych serwerów nadmiarowych w innych regionach. 

W obu przypadkach, jeśli serwer podstawowy wystąpi awaria, należy zmienić parametry połączenia w raportowaniu klientów, aby połączyć się z serwerem w innym regionalnym centrum danych. Tę zmianę należy uznać za ostateczność i tylko wtedy, gdy wystąpi katastrofalna awaria regionalnego centrum danych. Jest bardziej prawdopodobne, że awaria centrum danych obsługującego serwer podstawowy powróci do trybu online, zanim będzie można zaktualizować połączenia na wszystkich klientach. 

Aby uniknąć konieczności zmiany ciągów połączeń w przypadku klientów raportowania, można utworzyć [alias](analysis-services-server-alias.md) serwera dla serwera podstawowego. Jeśli serwer podstawowy ulegnie upadkowi, można zmienić alias wskazujący serwer nadmiarowy w innym regionie. Alias do nazwy serwera można zautomatyzować, kodując sprawdzanie kondycji punktu końcowego na serwerze podstawowym. Jeśli sprawdzanie kondycji nie powiedzie się, ten sam punkt końcowy może kierować do serwera nadmiarowego w innym regionie. 

## <a name="related-information"></a>Informacje pokrewne

[Tworzenie kopii zapasowych i przywracanie](analysis-services-backup.md)   
[Zarządzanie usługami analizy platformy Azure](analysis-services-manage.md)   
[Nazwy serwerów aliasów](analysis-services-server-alias.md) 

