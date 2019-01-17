---
title: Stan opcji magazynu na platformie Azure Usługa Service Fabric siatki | Dokumentacja firmy Microsoft
description: Więcej informacji na temat niezawodne przechowywanie stanu w usługi Service Fabric siatki działających na platformie Azure Usługa Service Fabric siatki.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 81d9fc7babef7c1d14a0acc420d0c16c05b83215
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354335"
---
# <a name="state-management-with-service-fabric"></a>Zarządzanie stanem przy użyciu usługi Service Fabric

Usługa Service Fabric obsługuje wiele różnych opcji do przechowywania stanów. Aby uzyskać omówienie wzorców zarządzania stanu i Service Fabric, zobacz [pojęcia dotyczące usługi Service Fabric: Stan](/azure/service-fabric/service-fabric-concepts-state). Te te same pojęcia mają zastosowanie tego, czy usługi działają wewnątrz lub na zewnątrz usługi Service Fabric siatki. 

Za pomocą usługi Service Fabric siatki można łatwo wdrożyć nową aplikację i połączyć ją z istniejącym magazynem danych hostowanych na platformie Azure. Poza używaniem dowolną zdalną bazą danych, istnieje kilka opcji do przechowywania danych, w zależności od tego, czy usługa życzy magazynu lokalnego lub zdalnego. 

## <a name="volumes"></a>Woluminy

Kontenery często należy użyć dysków tymczasowych. Dyski tymczasowe są jednak tymczasowych, więc uzyskiwanie nowego dysku tymczasowego i utracić informacji w przypadku awarii kontenera. Jest również był trudny do udostępniania informacji na temat dyski tymczasowe z innych kontenerów. Woluminy są katalogów, która ma zostać zainstalowany wewnątrz wystąpienia kontenera, które umożliwia utrwalanie stanu. Woluminy pozwalają magazynu plików ogólnego przeznaczenia i pozwala na odczyt/zapis plików za pomocą dysku normalnych operacji We/Wy plikowych interfejsów API. Zasobu woluminu w tym artykule opisano sposób instalacji katalogu i magazynu zapasowego. Można wybrać usługę Azure File storage lub Service Fabric woluminu dysku do przechowywania danych.

![Woluminy][image3]

### <a name="service-fabric-reliable-volume"></a>Usługa Service Fabric Reliable woluminu

Usługa Service Fabric Reliable woluminu jest używany do zainstalowania woluminu lokalnego do kontenera sterownik woluminu platformy Docker. Odczyty i zapisy są operacje lokalne i fast. Dane są replikowane do węzłów pomocniczych, dzięki czemu o wysokiej dostępności. Tryb failover jest również szybkie. Kontener ulegnie awarii, go nie powiedzie się za pośrednictwem do węzła, który ma już kopię danych. Aby uzyskać przykład, zobacz [sposób wdrażania aplikacji za pomocą usługi Service Fabric Reliable woluminu](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Usługa Azure Files woluminu

Azure woluminu plików jest używane do instalowania udziału plików platformy Azure do kontenera sterownik woluminu platformy Docker. Usługa Azure Files storage korzysta z magazynu w sieci, więc odczytuje i zapisuje odbywać się za pośrednictwem sieci. W porównaniu do usługi Service Fabric Reliable woluminu magazynu usługi Azure Files jest mniej wydajna, ale udostępnia opcję tańsze i w pełni wiarygodnych danych. Aby uzyskać przykład, zobacz [sposób wdrażania aplikacji za pomocą woluminu plików platformy Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacji na temat modelu aplikacji, zobacz [zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
