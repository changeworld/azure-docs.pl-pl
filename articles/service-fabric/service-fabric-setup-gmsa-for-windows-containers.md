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
ms.author: atsenthi
ms.openlocfilehash: 45fc4c924a8fb794ad81529de74b98ee812f46c5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170438"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Skonfiguruj gMSA dla kontenerów systemu Windows uruchomionych w systemie Service Fabric

Aby skonfigurować gMSA (konta usług zarządzane przez grupę), plik specyfikacji poświadczeń (`credspec`) jest umieszczany na wszystkich węzłach w klastrze. Plik można skopiować na wszystkie węzły przy użyciu rozszerzenia maszyny wirtualnej.  Plik `credspec` musi zawierać informacje o koncie gMSA. Aby uzyskać więcej informacji na temat pliku `credspec`, zobacz [Tworzenie specyfikacji poświadczeń](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specyfikacja poświadczeń i tag `Hostname` są określone w manifeście aplikacji. Tag `Hostname` musi być zgodny z nazwą konta gMSA, w którym działa kontener.  Znacznik `Hostname` umożliwia kontenerowi uwierzytelnienie się w innych usługach w domenie przy użyciu uwierzytelniania Kerberos.  Przykład służący do określania `Hostname` i `credspec` w manifeście aplikacji jest przedstawiony w następującym fragmencie kodu:

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
