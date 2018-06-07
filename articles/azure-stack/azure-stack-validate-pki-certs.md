---
title: Sprawdź poprawność certyfikatów infrastruktury kluczy publicznych stosu Azure stosu Azure zintegrowanych systemów wdrożenia | Dokumentacja firmy Microsoft
description: Opisuje sposób sprawdzania poprawności certyfikatów PKI stosu Azure stosu Azure zintegrowanych systemów. Obejmuje za pomocą narzędzia sprawdzania certyfikatów stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605614"
---
# <a name="validate-azure-stack-pki-certificates"></a>Sprawdź poprawność certyfikatów PKI stosu Azure

Narzędzie sprawdzania gotowości stosu Azure opisano w tym artykule jest dostępny [z galerii programu PowerShell](https://aka.ms/AzsReadinessChecker). Aby sprawdzić, czy można użyć narzędzia [wygenerowanych certyfikatów PKI](azure-stack-get-pki-certs.md) nadają się do przed wdrożeniem. Certyfikaty należy sprawdzić, czy pozostawić wystarczająco dużo czasu na test i ponowne wystawienie certyfikatów, jeśli to konieczne.

Narzędzie sprawdzania gotowości wykonuje następujące operacje sprawdzania poprawności certyfikatu:

- **Przeczytaj PFX**  
    Sprawdza, czy prawidłowy plik PFX, prawidłowe hasło i wyświetli ostrzeżenie, jeśli informacje o publicznym nie jest chroniony przez hasło. 
- **Algorytm podpisu**  
    Sprawdza, czy algorytm podpisu nie jest SHA1.
- **Klucz prywatny**  
    Sprawdza, czy klucz prywatny jest obecny i został wyeksportowany z atrybutem komputera lokalnego. 
- **Łańcuch certyfikatów**  
    Sprawdza łańcuch certyfikatów jest prawidłowa, w tym sprawdzenia certyfikaty z podpisem własnym.
- **Nazwy DNS**  
    Sprawdza, czy odpowiednie nazwy DNS dla każdego punktu końcowego zawiera sieci SAN lub w przypadku obsługi symboli wieloznacznych jest obecny.
- **Użycie klucza**  
    Sprawdza, czy użycie klucza zawiera podpis cyfrowy i szyfrowanie klucza i zawiera ulepszonego użycia klucza uwierzytelniania serwera i uwierzytelnianie klienta.
- **Rozmiar klucza**  
    Sprawdza, czy rozmiar klucza to 2048 lub większy.
- **Kolejnością łańcucha**  
    Sprawdza, czy kolejność innych certyfikatów weryfikacji, że kolejność jest poprawna.
- **Innych certyfikatów**  
    Upewnij się, że żadne inne certyfikaty nie zostały opakowane w formacie PFX inne niż liścia odpowiedniego certyfikatu i jego łańcucha.
- **Brak profilu**  
    Sprawdza, czy nowy użytkownik mogą ładować dane PFX bez załadowane, profil użytkownika mimicking zachowania kont gMSA podczas obsługi certyfikatu.

> [!IMPORTANT]  
> Plik PFX jest certyfikat PKI i hasła powinny być traktowane jako poufne informacje.

## <a name="prerequisites"></a>Wymagania wstępne

System powinna spełniać następujące wymagania wstępne, przed sprawdzaniem poprawności certyfikatów PKI dla wdrożenia usługi Azure stosu:

- Narzędzie sprawdzania gotowości stosu platformy Microsoft Azure
- Certyfikaty SSL wyeksportowane po [instrukcje dotyczące przygotowań](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 lub Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Sprawdzania poprawności certyfikatu core services

W celu przygotowania i aby zweryfikować certyfikaty PKI stosu Azure wdrożenia i obrotu tajne, wykonaj następujące kroki:

1. Zainstaluj **AzsReadinessChecker** z programu PowerShell wiersza (5.1 lub nowszej), uruchamiając następujące polecenie cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. Utwórz strukturę katalogów certyfikatu. W poniższym przykładzie, można zmienić `<c:\certificates>` nową ścieżkę katalogu wybranych przez użytkownika.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > Wykres i usług AD FS są wymagane, jeśli używasz usług AD FS w systemie tożsamości.
    
     - Umieść Twoje certyfikaty w katalogach odpowiednie utworzony w poprzednim kroku. Na przykład:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. W oknie programu PowerShell, zmień wartości **RegionName** i **FQDN** odpowiednie do środowiska Azure stosu i uruchom następujące polecenie:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Sprawdź dane wyjściowe i wszystkie certyfikaty przejść wszystkie testy. Na przykład:

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Znane problemy

**Objaw**: testy są pomijane.

**Przyczyna**: AzsReadinessChecker pomija niektórych testów, jeśli zależność nie zostały spełnione:

 - Inne certyfikaty są pomijane, jeśli łańcuch certyfikatów nie powiodło się.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Rozdzielczość**: Wykonaj wskazówki dotyczące narzędzia w sekcji szczegółów w obszarze każdego zestawu testów dla każdego certyfikatu.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Przeprowadź platformy Weryfikacja certyfikatów usług

Te kroki umożliwiają przygotowanie i sprawdzania poprawności certyfikatów PKI stosu Azure dla platformy jako usługa (PaaS) certyfikatów, jeśli planuje się wdrożeń SQL/MySQL lub usługi aplikacji.

1.  Zainstaluj **AzsReadinessChecker** z programu PowerShell wiersza (5.1 lub nowszej), uruchamiając następujące polecenie cmdlet:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Utworzyć obiektu hashtable zagnieżdżonych zawierający ścieżek i hasło, aby każdy z certyfikatów PaaS wymagające weryfikacji. W oknie programu PowerShell, uruchom:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Zmiana wartości **RegionName** i **FQDN** do danego środowiska Azure stos, aby rozpocząć sprawdzanie poprawności. Następnie uruchom polecenie:

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Sprawdź, czy dane wyjściowe i że wszystkie certyfikaty przejść wszystkie testy.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Przy użyciu zatwierdzonych certyfikatów

Po sprawdzeniu poprawności certyfikatów przez AzsReadinessChecker można przystąpić do ich używać w danym wdrożeniu stosu Azure lub obrotu tajny stosu Azure. 

 - W przypadku wdrożenia bezpiecznego transferu certyfikaty do specjalistą wdrożenia, aby można je skopiować na host wdrażania, jak określono w [dokumentacji wymagań dotyczących infrastruktury kluczy publicznych Azure stosu](azure-stack-pki-certs.md).
 - Dla obrotu tajne, można użyć certyfikatów zaktualizować stare certyfikaty dla punktów końcowych infrastruktury publicznych środowiska Azure stosu, postępując [dokumentacji Azure stosu klucz tajny obrotu](azure-stack-rotate-secrets.md).
 - W przypadku usług PaaS umożliwia certyfikaty Zainstaluj SQL, MySQL i dostawców zasobów usługi aplikacji Azure stosu, wykonując [Przegląd oferty usług w dokumentacji usługi Azure stosu](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Kolejne kroki

[Integracja tożsamości centrum danych](azure-stack-integrate-identity.md)