---
title: Łączenie usługi w chmurze z niestandardowym kontrolerem domeny | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć role sieć web/proces roboczy na domenę niestandardową AD przy użyciu programu PowerShell i rozszerzenie domeny usługi AD
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 8bee2e2038ee39c777e1ca09994ad21872d2029a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337344"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Role usługi w chmurze Azure nawiązywania połączenia z niestandardowego kontrolera domeny usługi AD hostowanych na platformie Azure
Skonfiguruje możemy najpierw Virtual Network (VNet) na platformie Azure. Następnie dodamy kontrolera domeny Active Directory (obsługiwanych na maszynie wirtualnej platformy Azure) do sieci wirtualnej. Następnie firma Microsoft będzie Dodawanie istniejących ról usługi w chmurze do wstępnie utworzonej sieci wirtualnej, a następnie łączyć je z kontrolera domeny.

Zanim zaczniemy, kilka rzeczy, które należy uwzględnić:

1. W tym samouczku jest używany program PowerShell, dlatego upewnij się, że masz programu Azure PowerShell zainstalowana i gotowa do użytku. Aby uzyskać pomoc przy konfigurowaniu programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).
2. Kontroler domeny usługi AD i roli sieć Web/proces roboczy wystąpień muszą znajdować się w sieci wirtualnej.

Postępuj zgodnie z tym przewodniku krok po kroku, a Jeśli napotkasz problemy, pozostaw komentarz na końcu tego artykułu. Ktoś będzie Postaramy się (tak, mamy odczyt komentarzy).

Sieć, która odwołuje się do usługi w chmurze musi znajdować się **klasycznej sieci wirtualnej**.

## <a name="create-a-virtual-network"></a>Create a Virtual Network
Utworzysz sieć wirtualną na platformie Azure przy użyciu witryny Azure portal lub programu PowerShell. W tym samouczku jest używany program PowerShell. Aby utworzyć sieć wirtualną przy użyciu witryny Azure portal, zobacz [tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md). Artykuł obejmuje tworzenie sieci wirtualnej (Resource Manager), ale należy utworzyć sieć wirtualna (klasyczna) dla usług w chmurze. Aby to zrobić, w portalu wybierz **Utwórz zasób**, typ *sieci wirtualnej* w **wyszukiwania** polu, a następnie naciśnij klawisz **Enter**. W wynikach wyszukiwania w obszarze **wszystko**, wybierz opcję **sieć wirtualna**. W obszarze **wybierz model wdrożenia**, wybierz opcję **klasycznego**, a następnie wybierz **Utwórz**. Można następnie postępuj zgodnie z instrukcjami w artykule.

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
Po zakończeniu konfigurowania sieci wirtualnej, należy utworzyć kontroler domeny usługi AD. Na potrzeby tego samouczka firma Microsoft będzie ustawienie konfiguracji kontrolera domeny usługi AD na maszynie wirtualnej platformy Azure.

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Podwyższanie poziomu maszyny wirtualnej do poziomu kontrolera domeny
Aby skonfigurować maszynę wirtualną jako kontroler domeny usługi AD, należy zalogować się do maszyny Wirtualnej i skonfigurować go.

Aby zalogować się do maszyny Wirtualnej, można pobrać pliku RDP przy użyciu programu PowerShell, użyj następujących poleceń:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Po zalogowaniu do maszyny Wirtualnej skonfigurować maszynę wirtualną jako kontroler domeny usługi AD, po przewodnik krok po kroku na [sposób konfigurowania klienta kontrolerem domeny usługi AD](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Dodaj usługi w chmurze do sieci wirtualnej
Następnie należy dodać wdrażania usługi w chmurze do nowej sieci wirtualnej. Aby to zrobić, należy zmodyfikować pliku cscfg usługi cloud service, dodając istotne sekcje do Twojego pliku cscfg przy użyciu programu Visual Studio lub dowolnego edytora.

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

Następnie tworzyć projekt usług w chmurze i wdrożyć ją na platformie Azure. Aby uzyskać pomoc dotyczącą wdrażania pakietu cloud services na platformie Azure, zobacz [jak utworzyć i wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Role sieć web/proces roboczy połączyć się z domeną
Wdrożenie projektu usługi w chmurze na platformie Azure, nawiązać połączenia wystąpień roli niestandardowej domeny AD przy użyciu rozszerzenia domeny usługi AD. Aby dodać rozszerzenie domenowych AD do istniejącego wdrożenia usługi w chmurze i Dołącz do domeny niestandardowej, wykonaj następujące polecenia w programie PowerShell:

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

I to wszystko.

Usługi w chmurze powinien być połączony z kontrolerem domeny niestandardowej. Jeśli chcesz dowiedzieć się więcej o różne opcje dotyczące sposobu konfigurowania rozszerzenia domeny usługi AD, skorzystaj z pomocy programu PowerShell. Kilka przykładów wykonaj:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
