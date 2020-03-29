---
title: Łączenie usługi w chmurze z niestandardowym kontrolerem domeny | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć role sieci Web/procesu roboczego z niestandardową domeną usługi AD przy użyciu programu PowerShell i rozszerzenia domeny usługi AD
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d40e392984d2675c748bda00c61cdaeb1c0932da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387024"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Łączenie ról usług w chmurze platformy Azure z niestandardowym kontrolerem domeny usługi AD hostowanym na platformie Azure
Najpierw skonfigurujemy sieć wirtualną (VNet) na platformie Azure. Następnie dodamy kontroler domeny usługi Active Directory (hostowany na maszynie wirtualnej platformy Azure) do sieci wirtualnej. Następnie dodamy istniejące role usługi w chmurze do wstępnie utworzonej sieci wirtualnej, a następnie połączymy je z kontrolerem domeny.

Zanim zaczniemy, kilka rzeczy, o których należy pamiętać:

1. W tym samouczku używa programu PowerShell, więc upewnij się, że masz zainstalowany program Azure PowerShell i gotowe do pracy. Aby uzyskać pomoc dotyczącą konfigurowania programu Azure PowerShell, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).
2. Kontroler domeny usługi AD i wystąpienia roli sieci Web/procesu roboczego muszą znajdować się w sieci wirtualnej.

Postępuj zgodnie z tym przewodnikiem krok po kroku, a jeśli napotkasz jakiekolwiek problemy, zostaw nam komentarz na końcu artykułu. Ktoś do Ciebie wróci (tak, czytamy komentarze).

Sieć, do którą odwołuje się usługa w chmurze, musi być **klasyczną siecią wirtualną.**

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Sieć wirtualną można utworzyć na platformie Azure przy użyciu portalu Azure lub programu PowerShell. W tym samouczku jest używany program PowerShell. Aby utworzyć sieć wirtualną przy użyciu portalu Azure portal, zobacz [Tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md). W tym artykule opisano tworzenie sieci wirtualnej (Menedżer zasobów), ale należy utworzyć sieć wirtualną (Classic) dla usług w chmurze. Aby to zrobić, w portalu wybierz pozycję **Utwórz zasób**, wpisz *sieć wirtualną* w polu **Wyszukiwania,** a następnie naciśnij **klawisz Enter**. W wynikach wyszukiwania w obszarze **Wszystko**wybierz pozycję **Sieć wirtualna**. W obszarze **Wybierz model wdrażania**wybierz pozycję **Klasyczny**, a następnie wybierz pozycję **Utwórz**. Następnie można wykonać kroki opisane w artykule.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Po zakończeniu konfigurowania sieci wirtualnej należy utworzyć kontroler domeny usługi AD. W tym samouczku będziemy konfigurować kontroler domeny usługi AD na maszynie wirtualnej platformy Azure.

Aby to zrobić, należy utworzyć maszynę wirtualną za pośrednictwem programu PowerShell przy użyciu następujących poleceń:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Podwyższyjenie poziomu maszyny wirtualnej na kontroler domeny
Aby skonfigurować maszynę wirtualną jako kontroler domeny usługi AD, należy zalogować się do maszyny wirtualnej i skonfigurować ją.

Aby zalogować się do maszyny Wirtualnej, można uzyskać plik RDP za pośrednictwem programu PowerShell, użyj następujących poleceń:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Po zalogowaniu się do maszyny wirtualnej skonfiguruj maszynę wirtualną jako kontroler domeny usługi AD, postępując zgodnie z przewodnikiem krok po kroku dotyczącymi [konfigurowania kontrolera domeny usługi AD klienta.](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Dodawanie usługi w chmurze do sieci wirtualnej
Następnie należy dodać wdrożenie usługi w chmurze do nowej sieci wirtualnej. Aby to zrobić, zmodyfikuj cscfg usługi w chmurze, dodając odpowiednie sekcje do cscfg za pomocą programu Visual Studio lub wybranego edytora.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Następnie skompiluj swój projekt usług w chmurze i wdrożyć go na platformie Azure. Aby uzyskać pomoc dotyczącą wdrażania pakietu usług w chmurze na platformie Azure, zobacz [Jak utworzyć i wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Łączenie ról sieci Web/procesu roboczego z domeną
Po wdrożeniu projektu usługi w chmurze na platformie Azure połącz wystąpienia roli z niestandardową domeną usługi AD przy użyciu rozszerzenia domeny usługi AD. Aby dodać rozszerzenie domeny usługi AD do istniejącego wdrożenia usług w chmurze i dołączyć do domeny niestandardowej, wykonaj następujące polecenia w programie PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

To wszystko.

Usługi w chmurze powinny być przyłączone do niestandardowego kontrolera domeny. Jeśli chcesz dowiedzieć się więcej o różnych dostępnych opcjach konfigurowania rozszerzenia domeny usługi AD, skorzystaj z pomocy programu PowerShell. Kilka przykładów następują:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



