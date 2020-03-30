---
title: Konfigurowanie usług kontenera sieci szkieletowej usług Azure dla usługi GMSA
description: Dowiedz się teraz, aby skonfigurować grupę kont usług zarządzanych (gMSA) dla kontenera działającego w sieci szkieletowej usług Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639211"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Konfigurowanie kontenerów gMSA dla systemu Windows działających w sieci szkieletowej usług

Aby skonfigurować gMSA (grupy kont usług zarządzanych), plik specyfikacji poświadczeń (`credspec`) jest umieszczany na wszystkich węzłach w klastrze. Plik można skopiować na wszystkich węzłach przy użyciu rozszerzenia maszyny Wirtualnej.  Plik `credspec` musi zawierać informacje o koncie gMSA. Aby uzyskać więcej `credspec` informacji na temat pliku, zobacz [Tworzenie specyfikacji poświadczeń](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Specyfikacja poświadczeń i `Hostname` tag są określone w manifeście aplikacji. Tag `Hostname` musi być zgodny z nazwą konta gMSA, pod którą działa kontener.  Tag `Hostname` umożliwia kontenerowi uwierzytelnianie się w innych usługach w domenie przy użyciu uwierzytelniania Kerberos.  Przykład do określania `Hostname` i `credspec` w manifeście aplikacji jest wyświetlany w następującym urywek:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
W następnym kroku przeczytaj następujące artykuły:

* [Wdrażanie kontenera systemu Windows w sieci szkieletowej usług w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w sieci szkieletowej usług w systemie Linux](service-fabric-get-started-containers-linux.md)
