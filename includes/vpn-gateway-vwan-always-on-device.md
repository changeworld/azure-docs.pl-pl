---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371766"
---
Aby pomyślnie ustanowić tunel urządzenia, muszą być spełnione następujące wymagania:

* Urządzenie musi być komputerem przyłączanym do domeny z systemem Windows 10 Enterprise lub Education w wersji 1809 lub nowszej.
* Tunel jest konfigurowany tylko dla wbudowanego rozwiązania sieci VPN systemu Windows i jest ustanawiany przy użyciu protokołu IKEv2 z uwierzytelnianiem certyfikatów komputera.
* Na jedno urządzenie można skonfigurować tylko jeden tunel urządzenia.

1. Instalowanie certyfikatów klientów na kliencie systemu Windows 10 przy użyciu artykułu [klienta sieci VPN typu punkt-lokacja.](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) Certyfikat musi znajdować się w magazynie komputera lokalnego.
1. Utwórz profil sieci VPN i skonfiguruj tunel urządzenia w kontekście konta LOCAL SYSTEM, korzystając z [tych instrukcji](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Przykład konfiguracji tunelu urządzenia

Po skonfigurowaniu bramy sieci wirtualnej i zainstalowaniu certyfikatu klienta w magazynie komputera lokalnego na kliencie systemu Windows 10 należy użyć następujących przykładów, aby skonfigurować tunel urządzenia klienckiego:

1. Skopiuj poniższy tekst i zapisz go jako ***devicecert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Skopiuj następujący tekst i zapisz go jako ***VPNProfile.xml*** w tym samym folderze co **devicecert.ps1**. Edytuj następujący tekst, aby dopasować go do środowiska.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Pobierz **PsExec** z [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) i wyodrębnij pliki do **C:\PSTools**.
1. Z monitu cmd administratora uruchom program PowerShell, uruchamiając:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. W programie PowerShell przełącz się do folderu, w którym znajdują się **pliki devicecert.ps1** i **VPNProfile.xml,** i uruchom następujące polecenie:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Test machinecert](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Uruchom **rasphone**.

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Poszukaj wpisu **MachineCertTest** i kliknij przycisk **Połącz**.

   ![Połącz](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Jeśli połączenie zakończy się pomyślnie, uruchom ponownie komputer. Tunel połączy się automatycznie.
