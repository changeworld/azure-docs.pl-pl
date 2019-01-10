---
title: Przygotowanie do hosta rozszerzenia dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się przygotować się do hosta rozszerzenia, który jest włączany automatycznie za pomocą przyszłych pakiet aktualizacji usługi Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 11/27/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: fcd5137792e573c3077a4b9d5e815b9bf20774f6
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155074"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Przygotowanie do hosta rozszerzenia dla usługi Azure Stack

Host rozszerzenia zabezpiecza usługi Azure Stack, zmniejszając liczbę wymaganych portów TCP/IP. W tym artykule patrzy na hosta rozszerzenia, który jest włączany automatycznie za pomocą pakietu aktualizacji usługi Azure Stack po aktualizacji 1808 przygotowywanie usługi Azure Stack.

## <a name="certificate-requirements"></a>Wymagania dotyczące certyfikatów

Host rozszerzenia implementuje dwie nowe domeny przestrzeni nazw w celu zagwarantowania, wpisy host unikatowy dla każdego rozszerzenia portalu. Nowe przestrzenie nazw domen wymaga dwóch dodatkowych certyfikatów symbolami wieloznacznymi w celu zapewnienia bezpiecznej komunikacji.

W tabeli przedstawiono nowe przestrzenie nazw i skojarzone certyfikatów:

| Folder wdrożenia | Temat wymaganego certyfikatu i alternatywnej nazwy podmiotu (SAN) | Zakres (na region) | Przestrzeń nazw poddomeny |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Host rozszerzenia administratora | *.adminhosting. \<region >. \<fqdn > (wieloznaczne certyfikaty SSL) | Host rozszerzenia administratora | adminhosting. \<region >. \<fqdn > |
| Host rozszerzenia publiczne | * .hosting. \<region >. \<fqdn > (wieloznaczne certyfikaty SSL) | Host rozszerzenia publiczne | hosting. \<region >. \<fqdn > |

Wymagania dotyczące certyfikatów szczegółowe znajdują się w [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych w usłudze Azure Stack](azure-stack-pki-certs.md) artykułu.

## <a name="create-certificate-signing-request"></a>Tworzenie żądania podpisania certyfikatu

Narzędzie sprawdzania gotowości stosu Azure zapewnia możliwość tworzenia certyfikatu podpisywania żądania dla dwóch nowych, wymagane certyfikaty SSL. Postępuj zgodnie z instrukcjami w artykule [usługi Azure Stack certyfikaty podpisywania generowania żądania](azure-stack-get-pki-certs.md).

> [!Note]  
> Możesz pominąć ten krok w zależności od tego, jak żądanie certyfikatów SSL.

## <a name="validate-new-certificates"></a>Zweryfikuj nowe certyfikaty

1. Otwórz program PowerShell z podwyższonym poziomem uprawnień na hoście cyklu życia sprzętu lub stacji roboczej zarządzania usługi Azure Stack.
2. Uruchom następujące polecenie cmdlet, aby zainstalować narzędzie narzędzie do sprawdzania gotowości usługi Azure Stack.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Uruchom następujący skrypt, aby utworzyć strukturę wymaganego folderu:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > W przypadku wdrożenia przy użyciu usługi Azure Active Directory Sfederowana Services (AD FS) następujące katalogi muszą zostać dodane do **$directories** w skrypcie: `ADFS`, `Graph`.

4. Uruchom następujące polecenia cmdlet, aby rozpocząć sprawdzanie certyfikatu:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

5. Umieść swoje certyfikaty w odpowiednich katalogów.

6. Sprawdź dane wyjściowe i wszystkich certyfikatów pomyślnie przejść wszystkie testy.


## <a name="import-extension-host-certificates"></a>Zaimportuj certyfikaty hosta rozszerzenia

Użyj komputera, który może nawiązać połączenia z punktem końcowym usługi Azure Stack uprzywilejowany dalsze czynności. Upewnij się, że masz dostęp do nowych plików certyfikatów z tego komputera.

1. Użyj komputera, który może nawiązać połączenia z punktem końcowym usługi Azure Stack uprzywilejowany dalsze czynności. Upewnij się, że dostęp do nowych plików certyfikatów z tego komputera.
2. Otwórz program PowerShell ISE do wykonania następnego blokach skryptu
3. Zaimportuj certyfikat dla administratora hostingu punktu końcowego.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Zaimportuj certyfikat dla hostingu punktu końcowego.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Aktualizowanie konfiguracji serwera DNS

> [!Note]  
> Ten krok nie jest wymagane, jeśli delegowanie strefy DNS jest używana do integracji usługi DNS.
Publikowanie punktów końcowych usługi Azure Stack zostały skonfigurowane rekordów A poszczególnych hostów, należy utworzyć dwa rekordy A dodatkowego hosta:

| Adres IP | Nazwa hosta | Typ |
|----|------------------------------|------|
| \<IP &GT; | *. Adminhosting. \<Region >. \<FQDN > | A |
| \<IP &GT; | *. Hosting. \<Region >. \<FQDN > | A |

Przydzielone adresy IP mogą być pobierane przy użyciu punktu końcowego uprzywilejowane, uruchamiając polecenie cmdlet **Get AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Porty i protokoły

Artykuł [Integracja z centrum danych usługi Azure Stack — publikowanie punktów końcowych](azure-stack-integrate-endpoints.md), omówiono porty i protokoły, które wymagają komunikacji przychodzącej, aby opublikować usługę Azure Stack przed wdrożeniem hosta rozszerzenia.

### <a name="publish-new-endpoints"></a>Publikowanie nowych punktów końcowych

Istnieją dwa nowe punkty końcowe wymagane do opublikowania przez zaporę. Przydzielone adresy IP z puli publicznych adresów VIP można pobrać przy użyciu następującego kodu, które muszą być uruchamiane za pomocą usługi Azure Stack [środowiska użytkownika uprzywilejowanego punktu końcowego](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-privileged-endpoint).

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Przykładowe dane wyjściowe

```PowerShell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Należy wprowadzić tę zmianę, przed włączeniem host rozszerzenia. Dzięki temu portali usługi Azure Stack, stale dostępne.

| Punkt końcowy (VIP) | Protokół | Porty |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Aktualizowanie istniejących reguł publikowania (włączenie wpis hosta rozszerzenia)

> [!Note]  
> Jest pakiet aktualizacji usługi Azure Stack 1808 **nie** jeszcze Włącz host rozszerzenia. Umożliwia, aby przygotować się do hosta rozszerzenia przez zaimportowanie wymaganych certyfikatów. Nie zamykaj żadnych portów, zanim host rozszerzenia jest włączany automatycznie za pomocą pakietu aktualizacji usługi Azure Stack po 1808 aktualizacji.

Następujące porty istniejącego punktu końcowego musi być zamknięty istniejące reguły zapory.

> [!Note]  
> Zalecane jest, aby zamknąć porty po pomyślnej weryfikacji.

| Punkt końcowy (VIP) | Protokół | Porty |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (administrator) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| Portal (użytkownik) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Usługa Azure Resource Manager (administrator) | HTTPS | 30024 |
| Usługa Azure Resource Manager (użytkownik) | HTTPS | 30024 |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zapory integracji](azure-stack-firewall.md).
- Dowiedz się więcej o [usługi Azure Stack certyfikaty podpisywania generowania żądania](azure-stack-get-pki-certs.md)
