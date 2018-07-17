---
title: Stan opcji magazynu na platformie Azure Usługa Service Fabric siatki | Dokumentacja firmy Microsoft
description: Więcej informacji na temat niezawodne przechowywanie stanu w usługi Service Fabric siatki działających na platformie Azure Usługa Service Fabric siatki.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076458"
---
# <a name="state-management-with-service-fabric"></a>Zarządzanie stanem przy użyciu usługi Service Fabric
Usługa Service Fabric obsługuje wiele różnych opcji do przechowywania stanów. Aby uzyskać omówienie wzorców zarządzania stanu i Service Fabric, zobacz [pojęcia dotyczące usługi Service Fabric: stan](/azure/service-fabric/service-fabric-concepts-state). Te te same pojęcia mają zastosowanie tego, czy usługi działają wewnątrz lub na zewnątrz usługi Service Fabric siatki. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Opcji magazynu stanu na platformie Azure Service Fabric siatki
Za pomocą usługi Service Fabric siatki można łatwo wdrożyć nową aplikację i połączyć ją z istniejącym magazynem danych hostowanych na platformie Azure. Poza używaniem dowolną zdalną bazą danych, istnieje kilka opcji do przechowywania danych, w zależności od tego, czy usługa życzy magazynu lokalnego lub zdalnego. 

* Dane przechowywane lokalnie, replikowane
  * Niezawodne kolekcje (niedostępne w wersji zapoznawczej)
    * Biblioteka, która implementuje struktur danych, takich jak kolejki i pary klucz wartość do użycia w usłudze
    * Dzięki temu najłatwiejszych i najszybszych obecnie sposób interakcji z danymi, zapewniając łatwy partycji routingu w połączeniu z inteligentnego routingu w usługi Service Fabric siatki
  * Sterownik woluminu usługi Service Fabric (niedostępne w wersji zapoznawczej)
    * Sterownik woluminu platformy docker do zainstalowania woluminu lokalnego do kontenera
    * Zapewnia to elastyczność ultimate, do przechowywania danych lokalnie, za pośrednictwem dowolnego interfejsu API, który obsługuje usługi file storage.

* Magazyn zdalny
  * Sterownik woluminu plików platformy Azure
    * Sterownik woluminu platformy docker, aby zainstalować udział plików platformy Azure do kontenera
    * Zapewnia mniej wydajne, ale opcja danych również tańsze pełnej elastycznych i niezawodnych, za pośrednictwem dowolnego interfejsu API, który obsługuje usługi file storage.
    * [Przewodnik: Wdrażanie aplikacji za pomocą usługi Azure Files woluminu](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacji na temat modelu aplikacji, zobacz [zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)
