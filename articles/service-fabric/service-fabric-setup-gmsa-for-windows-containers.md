---
title: GMSA Instalatora usługi Azure Service Fabric Container Services | Microsoft Docs
description: Dowiedz się teraz, jak skonfigurować gMSA dla kontenera działającego na platformie Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: 09994c7676de8470efff1707598ddf32a48e41a0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599184"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Skonfiguruj gMSA dla kontenerów systemu Windows uruchomionych w systemie Service Fabric

Aby skonfigurować gMSA (konta usług zarządzane przez grupę), plik specyfikacji poświadczeń (`credspec`) jest umieszczany na wszystkich węzłach w klastrze. Plik można skopiować na wszystkie węzły przy użyciu rozszerzenia maszyny wirtualnej.  `credspec` Plik musi zawierać informacje o koncie gMSA. Aby uzyskać więcej informacji na `credspec` temat pliku, zobacz [Tworzenie specyfikacji poświadczeń](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specyfikacja poświadczeń i `Hostname` znacznik są określone w manifeście aplikacji. `Hostname` Tag musi być zgodny z nazwą konta gMSA, w którym działa kontener.  `Hostname` Znacznik umożliwia kontener do samodzielnego uwierzytelnienia w innych usługach w domenie przy użyciu uwierzytelniania Kerberos.  Przykład do określenia `Hostname` `credspec` i w manifeście aplikacji jest przedstawiony w poniższym fragmencie kodu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Następnym krokiem jest zapoznanie się z następującymi artykułami:

* [Wdrażanie kontenera systemu Windows w celu Service Fabric w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w celu Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)
