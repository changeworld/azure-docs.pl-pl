---
title: Konfiguracja usługi zarządzane przez grupę dla usługi kontenera platformy Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się teraz skonfigurować grupę dla kontenera, w sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: subramar
ms.openlocfilehash: e4cd0b42e21609f88edc28c8fd7b5c433d56b3c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209097"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Skonfiguruj grupę dla kontenerów systemu Windows uruchomiona na sieci szkieletowej usług

Aby skonfigurować usługi zarządzane przez grupę (grupa zarządzanych kont usług), plik specyfikacji poświadczeń (`credspec`) znajduje się we wszystkich węzłach w klastrze. We wszystkich węzłach za pomocą rozszerzenia maszyny Wirtualnej można skopiować pliku.  `credspec` Plik musi zawierać informacje o koncie gMSA. Aby uzyskać więcej informacji na temat `credspec` plików, zobacz [kont usług](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). Specyfikacja poświadczeń i `Hostname` tag są określone w manifeście aplikacji. `Hostname` Tag musi odpowiadać nazwie konta gMSA działającą w kontenerze.  `Hostname` Tagu umożliwia kontenera do samodzielnego uwierzytelnienia w innych usługach w domenie przy użyciu uwierzytelniania Kerberos.  Przykładowy służący do określania `Hostname` i `credspec` w aplikacji manifestu jest wyświetlany w następujący fragment kodu:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Jako kolejny krok przeczytaj następujące artykuły:

* [Wdrażanie kontenera systemu Windows w sieci szkieletowej usług w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera Docker sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)
