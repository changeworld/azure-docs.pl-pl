---
title: Konfigurowanie tunelu zawsze włączone VPN dla VPN Gateway
description: Kroki konfigurowania tunelu VPN o zawsze włączonym użytkowniku dla VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846474"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurowanie tunelu użytkownika o zawsze włączonym sieci VPN

Jedną z nowych funkcji klienta wirtualnej sieci prywatnej (VPN) systemu Windows 10 jest możliwość utrzymania połączenia sieci VPN. Funkcja Always On jest funkcją systemu Windows 10, która umożliwia automatyczne łączenie się z aktywnym profilem sieci VPN i pozostanie połączona na podstawie wyzwalaczy — w tym, logowania użytkownika, zmiany stanu sieci lub aktywności ekranu urządzenia.

Bramy sieci wirtualnej platformy Azure mogą być używane z systemem Windows 10, które są zawsze włączone, aby nawiązywać trwałe tunele użytkowników oraz tunele urządzeń na platformie Azure. Ten artykuł pomoże Ci skonfigurować tunel użytkownika o zawsze włączonym sieci VPN.

Zawsze włączone połączenia sieci VPN obejmują dwa typy tuneli:

* **Tunel urządzenia** łączy się z określonymi serwerami sieci VPN przed zalogowaniem się użytkownika na urządzeniu. Scenariusze łączności przed zalogowaniem i cele zarządzania urządzeniami używają tunelu urządzenia.

* **Tunel użytkownika** łączy się tylko po zalogowaniu się użytkownika na urządzeniu. Tunel użytkownika umożliwia użytkownikom dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunel urządzenia i tunel użytkownika działają niezależnie od profilów sieci VPN. Mogą być połączone w tym samym czasie i mogą korzystać z innych metod uwierzytelniania i innych ustawień konfiguracji sieci VPN zgodnie z potrzebami.

## <a name="1-configure-the-gateway"></a>1. Skonfiguruj bramę

Skonfiguruj bramę sieci VPN do korzystania z protokołu IKEv2 i uwierzytelniania opartego na certyfikatach przy użyciu tego [artykułu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Skonfiguruj tunel użytkownika

1. Zainstaluj certyfikaty klienta na kliencie systemu Windows 10, jak pokazano w tym artykule dotyczącym [klienta sieci VPN typu punkt-lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md). Certyfikat musi znajdować się w bieżącym magazynie użytkownika
2. Użyj [tych instrukcji](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections), aby skonfigurować klienta usługi Always On VPN za pomocą programu PowerShell, oprogramowania SCCM lub usługi Intune.

### <a name="configuration-example-for-user-tunnel"></a>Przykład konfiguracji dla tunelu użytkownika

Po skonfigurowaniu bramy sieci wirtualnej i zainstalowaniu certyfikatu klienta w magazynie komputera lokalnego na kliencie systemu Windows 10 Użyj następujących przykładów, aby skonfigurować tunel urządzenia klienckiego.

1. Skopiuj poniższy tekst i Zapisz go jako ***Usercert. ps1***.

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
1. Skopiuj poniższy tekst i Zapisz go jako ***VPNProfile. XML*** w tym samym folderze, co **Usercert. ps1**. Edytuj następujący tekst, aby dopasować go do środowiska.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Uruchom program PowerShell jako administrator.

1. W programie PowerShell przejdź do folderu, w którym znajdują się **Usercert. ps1** i **VPNProfile. XML** , a następnie uruchom następujące polecenie:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Poszukaj w obszarze Ustawienia sieci VPN.

1. Wyszukaj wpis **UserTest** , a następnie kliknij przycisk **Połącz**.

1. W przypadku pomyślnego nawiązania połączenia pomyślnie skonfigurowano tunel zawsze włączony.

## <a name="cleanup"></a>Oczyszczanie

Aby usunąć profil, uruchom następujące polecenie:

1. Rozłącz połączenie i usuń zaznaczenie pozycji "Połącz automatycznie"

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Oczyszczanie](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [problemy z połączeniem punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
