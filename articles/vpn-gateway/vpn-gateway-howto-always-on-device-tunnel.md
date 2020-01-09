---
title: Konfigurowanie tunelu VPN Always-On
titleSuffix: Azure VPN Gateway
description: Procedura konfigurowania zawsze włączone tunel VPN dla VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: cherylmc
ms.openlocfilehash: 6f0c33ee7fd5790a060574230f1156c569a63936
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425666"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurowanie tunelu zawsze włączonego urządzenia sieci VPN

Jedną z nowych funkcji klienta wirtualnej sieci prywatnej (VPN) systemu Windows 10 jest możliwość utrzymania połączenia sieci VPN. Funkcja Always On jest funkcją systemu Windows 10, która umożliwia automatyczne łączenie się z aktywnym profilem sieci VPN i pozostanie połączona na podstawie wyzwalaczy — w tym, logowania użytkownika, zmiany stanu sieci lub aktywności ekranu urządzenia.

Bramy sieci wirtualnej platformy Azure mogą być używane z systemem Windows 10, które są zawsze włączone, aby nawiązywać trwałe tunele użytkowników oraz tunele urządzeń na platformie Azure. Ten artykuł pomoże Ci skonfigurować tunel urządzenia sieci VPN zawsze włączony.

Zawsze włączone połączenia sieci VPN obejmują dwa typy tuneli:

* **Tunel urządzenia** łączy się z określonymi serwerami sieci VPN przed zalogowaniem się użytkownika na urządzeniu. Scenariusze łączności przed zalogowaniem i cele zarządzania urządzeniami używają tunelu urządzenia.

* **Tunel użytkownika** łączy się tylko po zalogowaniu się użytkownika na urządzeniu. Tunel użytkownika umożliwia użytkownikom dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunel urządzenia i tunel użytkownika działają niezależnie od profilów sieci VPN. Mogą być połączone w tym samym czasie i mogą korzystać z innych metod uwierzytelniania i innych ustawień konfiguracji sieci VPN zgodnie z potrzebami.

## <a name="1-configure-the-gateway"></a>1. Skonfiguruj bramę

Skonfiguruj bramę sieci VPN do korzystania z protokołu IKEv2 i uwierzytelniania opartego na certyfikatach przy użyciu tego [artykułu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-device-tunnel"></a>2. Konfigurowanie tunelu urządzenia

Aby można było pomyślnie ustanowić tunel urządzenia, muszą zostać spełnione następujące wymagania:

* Urządzenie musi być komputerem przyłączonym do domeny z systemem Windows 10 Enterprise lub Education w wersji 1809 lub nowszej.
* Tunel można skonfigurować tylko dla wbudowanego rozwiązania sieci VPN systemu Windows i jest ustanawiany przy użyciu protokołu IKEv2 z uwierzytelnianiem certyfikatu komputera. 
* Na urządzenie można skonfigurować tylko jeden tunel urządzenia.

1. Zainstaluj certyfikaty klienta na kliencie systemu Windows 10, jak pokazano w tym artykule dotyczącym [klienta sieci VPN typu punkt-lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md). Certyfikat musi znajdować się w magazynie komputera lokalnego.
1. Użyj [tych instrukcji](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) , aby utworzyć profil sieci VPN i skonfigurować tunel urządzenia w kontekście lokalnego konta systemowego.

### <a name="configuration-example-for-device-tunnel"></a>Przykład konfiguracji dla tunelu urządzenia

Po skonfigurowaniu bramy sieci wirtualnej i zainstalowaniu certyfikatu klienta w magazynie komputera lokalnego na kliencie systemu Windows 10 Użyj następujących przykładów, aby skonfigurować tunel urządzenia klienckiego.

1. Skopiuj poniższy tekst i Zapisz go jako ***devicecert. ps1***.

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
1. Skopiuj poniższy tekst i Zapisz go jako ***VPNProfile. XML*** w tym samym folderze, co **devicecert. ps1**. Edytuj następujący tekst, aby dopasować go do środowiska.

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
1. Pobierz **PsExec** z programu [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) i Wyodrębnij pliki do **C:\PSTools**.
1. W wierszu polecenia administratora uruchom program PowerShell, uruchamiając polecenie:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. W programie PowerShell przejdź do folderu, w którym znajdują się **devicecert. ps1** i **VPNProfile. XML** , a następnie uruchom następujące polecenie:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Uruchom **Rasphone**.

   ![pliku](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Wyszukaj wpis **MachineCertTest** , a następnie kliknij przycisk **Połącz**.

   ![Łączenie](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Jeśli połączenie zakończy się pomyślnie, należy ponownie uruchomić komputer. Tunel zostanie automatycznie nawiązane.

## <a name="cleanup"></a>Czyszczenie

Aby usunąć profil, uruchom następujące polecenie:

![Czyszczenie](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [problemy z połączeniem punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
