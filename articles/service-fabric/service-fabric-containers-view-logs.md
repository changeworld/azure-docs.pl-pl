---
title: Wyświetl dzienniki kontenery w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania kontenera dzienniki uruchomione usługi kontenera sieci szkieletowej usług za pomocą Eksploratora usługi sieć szkieletowa.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Wyświetl dzienniki dla usługi kontenera sieci szkieletowej usług
Azure Service Fabric jest orchestrator kontenera i obsługuje zarówno [kontenery systemu Linux i Windows](service-fabric-containers-overview.md).  W tym artykule opisano sposób wyświetlania dzienników kontenera uruchomionej usługi kontenera, aby zdiagnozować i rozwiązać problemy.

## <a name="access-container-logs"></a>Dostęp do kontenera dzienników
Kontener Dzienniki mogą uzyskać dostęp za pomocą [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  W przeglądarce sieci web Otwórz Eksploratora usługi sieć szkieletowa z klastra zarządzania końcowego przechodząc do [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Kontener Dzienniki znajdują się w węźle klastra, który wystąpienie usługi kontenera. Na przykład Pobierz dzienniki sieci web frontonu kontenera [Linux głosowanie w przykładowej aplikacji](service-fabric-quickstart-containers-linux.md). W widoku drzewa, rozwiń węzeł **klastra**>**aplikacji**>**VotingType**>**fabric: / głosowania / azurevotefront**.  Następnie rozwiń partycji (d1aa737e-f22a-e347-be16-eec90be24bc1, w tym przykładzie) i sprawdzić, czy kontenera jest uruchomiona w węźle klastra *_lnxvm_0*.

W widoku drzewa, Znajdź pakiet kodu na *_lnxvm_0* węzła rozwijając **węzłów**>**_lnxvm_0**>**fabric: / głosu**  > **azurevotfrontPkg**>**pakiety kodu**>**kod**.  Następnie wybierz **dzienniki kontenera** opcję, aby wyświetlić dzienniki kontenera.

![Platforma Service Fabric][Image1]


## <a name="next-steps"></a>Kolejne kroki
- Pracy za pośrednictwem [utworzyć samouczek aplikacji kontenera Linux](service-fabric-tutorial-create-container-images.md).
- Dowiedz się więcej o [sieci szkieletowej usług i kontenerów](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
