---
title: Połączenie usługi w chmurze z kontrolerem domeny niestandardowe | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć role sieć web/proces roboczy na domenę niestandardową AD przy użyciu programu PowerShell i rozszerzenia domeny usługi AD
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 4a50ae5e19ff9bf79b7f5361e5a274a2aba350f5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29845659"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Łączenie role usługi w chmurze Azure z niestandardowego kontrolera domeny AD hostowana na platformie Azure
Sieć wirtualną (VNet) zostanie najpierw skonfigurowanie na platformie Azure. Następnie dodamy kontroler domeny usługi Active Directory (obsługiwanych na maszynie wirtualnej platformy Azure) do sieci wirtualnej. Następnie firma Microsoft będzie Dodaj istniejące role usługi w chmurze do wstępnie utworzone sieci wirtualnej, a następnie podłącz je do kontrolera domeny.

Początek, kilka rzeczy, które należy wziąć pod uwagę:

1. W tym samouczku korzysta z programu PowerShell, upewnij się, że masz programu Azure PowerShell zainstalowana i rozpocząć pracę. Aby uzyskać pomoc dotyczącą konfigurowania programu Azure PowerShell, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
2. Swoich wystąpień kontroler domeny usługi AD i roli sieć Web/proces roboczy muszą znajdować się w sieci wirtualnej.

Wykonaj ten krok po kroku, a jeśli wystąpiły problemy, pozostaw komentarz na końcu tego artykułu. Ktoś będzie zająć się (tak, odczytano komentarzy).

Sieć, do którego odwołuje się przez usługę w chmurze musi być **klasycznej sieci wirtualnej**.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Można utworzyć sieć wirtualną na platformie Azure przy użyciu portalu Azure lub programu PowerShell. W tym samouczku jest używany programu PowerShell. Aby utworzyć sieć wirtualną przy użyciu portalu Azure, zobacz [utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md). Artykuł obejmuje tworzenie sieci wirtualnej (Resource Manager), ale należy utworzyć sieć wirtualną (klasyczne) dla usług w chmurze. Aby to zrobić w portalu, wybierz **Utwórz zasób**, typ *sieci wirtualnej* w **wyszukiwania** polu, a następnie naciśnij klawisz **Enter**. W wynikach wyszukiwania w obszarze **wszystko**, wybierz pozycję **sieci wirtualnej**. W obszarze **wybierz model wdrożenia**, wybierz pozycję **klasycznego**, a następnie wybierz pozycję **Utwórz**. Można następnie wykonaj kroki opisane w artykule.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

## <a name="create-a-virtual-machine"></a>Utworzenie maszyny wirtualnej
Po zakończeniu konfigurowania sieci wirtualnej, należy utworzyć kontroler domeny usługi AD. W tym samouczku firma Microsoft będzie Trwa konfigurowanie kontrolera domeny AD na maszynie wirtualnej platformy Azure.

Aby to zrobić, utwórz maszynę wirtualną za pomocą programu PowerShell przy użyciu następujących poleceń:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Podwyższ poziom maszyny wirtualnej do poziomu kontrolera domeny
Aby skonfigurować maszynę wirtualną jako kontroler domeny usługi AD, należy zalogować się do maszyny Wirtualnej i skonfigurować go.

Aby zalogować się do maszyny Wirtualnej, można pobrać pliku RDP za pomocą programu PowerShell, użyj następujących poleceń:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Gdy użytkownik jest zalogowany do maszyny Wirtualnej, konfigurowanie maszyny wirtualnej jako kontrolera domeny usługi AD wykonując przewodnik krok po kroku na [sposobu konfigurowania klienta kontroler domeny usługi AD](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Dodaj do sieci wirtualnej usługi w chmurze
Następnie należy dodać do nowej sieci wirtualnej wdrożenia usługi chmury. Aby to zrobić, należy zmodyfikować z cscfg usługi chmury przez dodanie odpowiednich sekcji do Twojej cscfg przy użyciu programu Visual Studio lub dowolnego edytora.

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

Następnie skompilowanie projektu usługi w chmurze i wdrożyć go na platformie Azure. Aby uzyskać pomoc dotyczącą wdrażania pakietu usługi w chmurze na platformie Azure, zobacz [sposobu tworzenia i wdrażania usługi w chmurze](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Połącz się z domeną role sieć web/proces roboczy.
Po wdrożeniu projekt usługi w chmurze na platformie Azure nawiązać niestandardowej domeny AD za pomocą rozszerzenia domeny AD wystąpienia roli. Dodaj rozszerzenie AD domeny do istniejącego wdrożenia usługi w chmurze i przyłączania do domeny niestandardowe, wykonaj następujące polecenia w programie PowerShell:

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

A to wszystko.

Usługi w chmurze powinny należeć do kontrolera domeny niestandardowej. Jeśli chcesz dowiedzieć się więcej o różnych opcjach konfigurowania rozszerzenia domeny AD, korzystanie z pomocy programu PowerShell. Kilka przykładów wykonaj:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
