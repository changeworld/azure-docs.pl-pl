---
title: Konfigurowanie tunelu użytkownika z zawsze włączonymi sieciami VPN
titleSuffix: Azure VPN Gateway
description: W tym artykule opisano sposób konfigurowania tunelu użytkownika zawsze włączone sieci VPN dla bramy sieci VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: a22ba5d8b33dd41fcc76c65fcddaf60c1c0ed5e3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514752"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurowanie zawsze włączonego tunelu VPN użytkownika

Nową funkcją klienta sieci VPN systemu Windows 10, która jest zawsze włączona, jest możliwość utrzymania połączenia sieci VPN. Zawsze, gdy aktywny profil sieci VPN może nawiązywać połączenie automatycznie i pozostaje połączony na podstawie wyzwalaczy, takich jak logowanie użytkownika, zmiana stanu sieci lub aktywny ekran urządzenia.

Za pomocą bram sieci wirtualnej platformy Azure z systemem Windows 10, które są zawsze włączone, można nawiązywać stałe tunele użytkowników i tunele urządzeń na platformie Azure. W tym artykule opisano sposób konfigurowania tunelu użytkownika o zawsze włączonym sieci VPN.

Zawsze włączone połączenia sieci VPN obejmują jeden z dwóch typów tuneli:

* **Tunel urządzenia**: nawiązuje połączenie z określonymi serwerami sieci VPN przed zalogowaniem się użytkowników na urządzeniu. Scenariusze łączności przed logowaniem i zarządzaniem urządzeniami używają tunelu urządzenia.

* **Tunel użytkownika**: nawiązuje połączenie tylko po zalogowaniu się użytkowników na urządzeniu. Korzystając z tuneli użytkowników, można uzyskać dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunele urządzeń i tunele użytkownika działają niezależnie od profilów sieci VPN. Mogą być połączone w tym samym czasie i mogą korzystać z innych metod uwierzytelniania i innych ustawień konfiguracji sieci VPN, zgodnie z potrzebami.

W poniższych sekcjach opisano konfigurowanie bramy sieci VPN i tunelu użytkownika.

## <a name="step-1-configure-a-vpn-gateway"></a>Krok 1. Konfigurowanie bramy sieci VPN

Bramę sieci VPN można skonfigurować tak, aby korzystała z protokołu IKEv2 i uwierzytelniania opartego na certyfikatach, postępując zgodnie z instrukcjami w tym artykule [punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="step-2-configure-a-user-tunnel"></a>Krok 2. Konfigurowanie tunelu użytkownika

1. Zainstaluj certyfikaty klienta na kliencie systemu Windows 10, jak pokazano w tym artykule dotyczącym [klienta sieci VPN typu punkt-lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md) . Certyfikat musi znajdować się w magazynie bieżący użytkownik.

1. Skonfiguruj opcję zawsze włączone klienta VPN za pomocą programu PowerShell, Configuration Manager lub Intune, postępując zgodnie z instrukcjami w temacie [Konfigurowanie klienta systemu Windows 10 zawsze włączone połączenia sieci VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Przykładowa konfiguracja dla tunelu użytkownika

Po skonfigurowaniu bramy sieci wirtualnej i zainstalowaniu certyfikatu klienta w magazynie komputera lokalnego na kliencie z systemem Windows 10 należy skonfigurować tunel urządzenia klienckiego przy użyciu następujących przykładów:

1. Skopiuj poniższy tekst i Zapisz go jako *Usercert. ps1*:

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
1. Skopiuj poniższy tekst i Zapisz go jako *VPNProfile. XML* w tym samym folderze, co *Usercert. ps1*. Edytuj następujący tekst, aby dopasować go do środowiska:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

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

1. W programie PowerShell przejdź do folderu, w którym znajdują się *Usercert. ps1* i *VPNProfile. XML* , a następnie uruchom następujące polecenie:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. W obszarze **Ustawienia sieci VPN**Znajdź wpis **UserTest** , a następnie wybierz pozycję **Połącz**.

1. W przypadku pomyślnego nawiązania połączenia pomyślnie skonfigurowano tunel zawsze włączony.

## <a name="clean-up-your-resources"></a>Czyszczenie zasobów

Aby usunąć profil, wykonaj następujące czynności:

1. Uruchom następujące polecenie:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Odłącz połączenie i wyczyść pole wyboru **Połącz automatycznie** .

![Czyszczenie](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problemy z połączeniem, które mogą wystąpić, zobacz [problemy z połączeniem punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
