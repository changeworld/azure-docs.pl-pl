---
title: Konfigurowanie tunelu zawsze włączona sieć VPN dla bramy sieci VPN
description: Kroki, aby skonfigurować tunel zawsze włączona sieć VPN dla bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695790"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurowanie tunelu zawsze włączonego urządzenia sieci VPN

Jedną z nowych funkcji klienta systemu Windows 10 wirtualnej sieci prywatnej (VPN) jest możliwość utrzymywania połączenia sieci VPN. Jest zawsze włączona funkcja systemu Windows 10, która umożliwia aktywne profilu sieci VPN, Połącz automatycznie, a pozostały połączone w oparciu o wyzwalaczach — to znaczy, logowania użytkownika, zmiany stanu sieci lub aktywny ekran urządzenia.

Bramy sieci wirtualnej platformy Azure może służyć za pomocą systemu Windows 10 zawsze włączone można ustanowić tuneli trwałego użytkownika, a także tuneli urządzenie na platformie Azure. Ten artykuł pomaga skonfigurować tunelu zawsze sieci VPN na urządzeniu.

Zawsze włączona sieć VPN połączenia obejmują dwa typy tuneli:

* **Tunel urządzenia** łączy do określonych serwerów sieci VPN, zanim użytkownicy logują się urządzenia. Scenariusze łączności przed logowaniem i celów zarządzania urządzeniem używają urządzeń tunelu.

* **Użytkownika tunelu** łączy dopiero po logowania użytkownika na urządzeniu. Tunel użytkownika umożliwia użytkownikom dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunel urządzenia i użytkownika tunelu działać niezależnie i z ich profilów sieci VPN. Mogą być połączone w tym samym czasie i można użyć różnych metod uwierzytelniania i inne ustawienia konfiguracji sieci VPN zgodnie z potrzebami.

## <a name="1-configure-the-gateway"></a>1. Konfigurowanie bramy

Konfigurowanie bramy sieci VPN IKEv2 i uwierzytelniania opartego na certyfikatach, za pomocą tego [artykułu point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Konfigurowanie tunelu użytkownika

1. Instalowanie certyfikatów klienta na kliencie systemu Windows 10, jak pokazano w tym [artykułu klienta sieci VPN typu punkt lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md). Ten certyfikat musi znajdować się w bieżącej Store użytkownika
2. Konfigurowanie klienta zawsze włączona sieć VPN za pomocą programu PowerShell, programu SCCM lub usługi Intune przy użyciu [w instrukcjach](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Konfigurowanie tunelu urządzenia

W celu pomyślnego ustanowienia tuneli urządzenia, muszą być spełnione następujące wymagania:

* Urządzenie musi być komputerem przyłączonym do domeny systemem Windows 10 Enterprise lub Education wersja 1709 lub nowszej.
* Tunel tylko można skonfigurować dla wbudowanego rozwiązania Windows w sieci VPN, zostanie nawiązane za pomocą protokołu IKEv2 przy użyciu uwierzytelniania certyfikatów komputerów. 
* Tylko jedno urządzenie tunelu można skonfigurować na każdym urządzeniu.

1. Instalowanie certyfikatów klienta na kliencie systemu Windows 10, jak pokazano w tym [artykułu klienta sieci VPN typu punkt lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md). Ten certyfikat musi znajdować się w magazynie komputera lokalnego.
1. Użyj [w instrukcjach](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) tworzenia profilu sieci VPN i skonfigurować urządzenie tunelu w kontekście lokalnego konta SYSTEMOWEGO.

### <a name="configuration-example-for-device-tunnel"></a>Przykład konfiguracji dla urządzeń tunelu

Po skonfigurowaniu bramy sieci wirtualnej i zainstalowany certyfikat klienta w magazynie komputera lokalnego na komputerze klienckim systemu Windows 10 umożliwiają konfigurowanie tunelu urządzenia klienta poniższych przykładach.

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
1. Skopiuj poniższy tekst i zapisz go jako ***VPNProfile.xml*** w tym samym folderze co **devicecert.ps1**. Edytuj następujący tekst pod kątem danego środowiska.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

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
1. Pobierz **PsExec** z [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) i Wyodrębnij pliki do **C:\PSTools**.
1. W wierszu polecenia administratora uruchom program PowerShell przez uruchomienie:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Program PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. W programie PowerShell przejdź do folderu, gdzie **devicecert.ps1** i **VPNProfile.xml** znajdują, i uruchom następujące polecenie:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Uruchom **rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Wyszukaj **MachineCertTest** wpis, a następnie kliknij przycisk **Connect**.

   ![Połączenie](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Połączenie powiedzie się, należy ponownie uruchomić komputer. Tunel połączy się automatycznie.

## <a name="cleanup"></a>Czyszczenie

Aby usunąć profil, uruchom następujące polecenie:

![Czyszczenie](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Kolejne kroki

Do rozwiązywania problemów, zobacz [problemy z połączeniem usługi Azure point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
