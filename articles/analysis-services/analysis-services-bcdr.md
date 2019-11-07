---
title: Azure Analysis Services wysoka dostępność | Microsoft Docs
description: W tym artykule opisano, jak Azure Analysis Services zapewnia wysoką dostępność podczas przerw w działaniu usługi.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e750dce804ea93f3d3068ffd36bc7a73a50906a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573360"
---
# <a name="analysis-services-high-availability"></a>Analysis Services wysoka dostępność

W tym artykule opisano sposób zapewnienia wysokiej dostępności dla serwerów Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zapewnienie wysokiej dostępności podczas przerw w działaniu usługi

Czasami centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje to zakłócenia działania firmy, które może trwać kilka minut, lub może trwać w godzinach. Wysoka dostępność jest najczęściej osiągana z nadmiarowością serwera. Za pomocą Azure Analysis Services można uzyskać nadmiarowość, tworząc dodatkowe, pomocnicze serwery w jednym lub kilku regionach. Podczas tworzenia nadmiarowych serwerów, aby zapewnić, że dane i metadane na tych serwerach są zsynchronizowane z serwerem w regionie, który został przełączony w tryb offline, można:

* Wdrażaj modele w nadmiarowych serwerach w innych regionach. Ta metoda wymaga przetwarzania danych zarówno na serwerze podstawowym, jak i w sposób nadmiarowy, co zapewnia, że wszystkie serwery są zsynchronizowane.

* [Utwórz kopię zapasową](analysis-services-backup.md) baz danych z serwera podstawowego i przywróć je na serwerach nadmiarowych. Na przykład można zautomatyzować kopie zapasowe nocne do usługi Azure Storage, a następnie przywrócić inne nadmiarowe serwery w innych regionach. 

W obu przypadkach, jeśli serwer podstawowy ulegnie awarii, należy zmienić parametry połączenia w klientach raportowania, aby połączyć się z serwerem w innym regionalnym centrum danych. Ta zmiana powinna być traktowana jako Ostatnia i tylko wtedy, gdy wystąpi awaria regionalnego centrum danych. Prawdopodobnie awaria centrum danych, w którym serwer podstawowy zostałby powracał do trybu online, zanim będzie można zaktualizować połączenia na wszystkich klientach. 

Aby uniknąć konieczności zmiany parametrów połączenia na klientach raportowania, można utworzyć [alias](analysis-services-server-alias.md) serwera dla serwera podstawowego. Jeśli serwer podstawowy ulegnie awarii, można zmienić alias tak, aby wskazywał serwer nadmiarowy w innym regionie. Można zautomatyzować alias do nazwy serwera przez kodowanie kontroli kondycji punktu końcowego na serwerze podstawowym. Jeśli sprawdzanie kondycji nie powiedzie się, ten sam punkt końcowy może kierować do nadmiarowego serwera w innym regionie. 

## <a name="related-information"></a>Informacje pokrewne

  [kopii zapasowych i przywracania](analysis-services-backup.md)  
[Zarządzanie  Azure Analysis Services](analysis-services-manage.md)  
[Nazwy serwerów aliasów](analysis-services-server-alias.md) 

