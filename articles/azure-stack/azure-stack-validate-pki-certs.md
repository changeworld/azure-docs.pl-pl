---
title: Sprawdzanie poprawności certyfikatów infrastruktury kluczy publicznych usługi Azure Stack dla usługi Azure Stack zintegrowane systemy wdrażania | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób sprawdzania poprawności certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack w systemach zintegrowanych w usłudze Azure Stack. Dotyczy korzystania z narzędzia Azure Stack certyfikat kontroler.
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
ms.date: 1/08/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 6b2b72403365dc0f6cc61232e0b70801b57a29e2
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095548"
---
# <a name="validate-azure-stack-pki-certificates"></a>Sprawdzanie poprawności certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack

Dostępne jest narzędzie narzędzie do sprawdzania gotowości usługi Azure Stack, opisane w tym artykule [z galerii programu PowerShell](https://aka.ms/AzsReadinessChecker). Można użyć narzędzia, aby sprawdzić, czy [generowanych certyfikaty infrastruktury kluczy publicznych](azure-stack-get-pki-certs.md) są odpowiednie na potrzeby przed wdrożeniem. Weryfikowanie certyfikatów, pozostawiając wystarczająco dużo czasu na test i ponownego wystawienia certyfikatów, jeśli to konieczne.

Narzędzie sprawdzania gotowości wykonuje następujące operacje sprawdzania poprawności certyfikatu:

- **Przeczytaj PFX**  
    Sprawdza, czy prawidłowy plik PFX, prawidłowe hasło i tego, czy informacje o publicznym nie jest chroniony hasłem. 
- **Algorytm podpisu**  
    Sprawdza, że algorytm podpisu jest algorytm SHA1.
- **Klucz prywatny**  
    Sprawdza, czy klucz prywatny jest obecny i są eksportowane z atrybutem komputera lokalnego. 
- **Łańcuch certyfikatów**  
    Sprawdza łańcuch certyfikatów jest nienaruszony, włącznie z Sprawdzaj certyfikaty z podpisem własnym.
- **Nazwy DNS**  
    Sprawdza, czy sieć SAN zawiera odpowiednie nazwy DNS dla każdego punktu końcowego lub w przypadku obsługi symboli wieloznacznych jest obecny.
- **Użycie klucza**  
    Sprawdza, czy użycie klucza zawiera podpis cyfrowy i szyfrowanie klucza, i rozszerzone użycie klucza zawiera uwierzytelnianie serwera i uwierzytelnianie klienta.
- **Rozmiar klucza**  
    Sprawdza, czy rozmiar klucza 2048 lub większą.
- **Kolejnością łańcucha**  
    Sprawdza, czy kolejność certyfikatów, weryfikowanie, czy kolejność jest poprawna.
- **Inne certyfikaty**  
    Upewnij się, że nie inne certyfikaty zostały spakowane do PFX innego niż certyfikat liścia odpowiednie i jego łańcuch.
- **Brak profilu**  
    Sprawdza, czy nowy użytkownik można załadować danych PFX bez profilu użytkownika załadowane, naśladując zachowania kont gMSA podczas obsługi certyfikatu.

> [!IMPORTANT]  
> Certyfikat PKI jest plik PFX i hasła powinny być traktowane jako poufne informacje.

## <a name="prerequisites"></a>Wymagania wstępne

System powinien spełniać następujące wymagania wstępne, przed sprawdzaniem poprawności certyfikaty PKI dla wdrożenia usługi Azure Stack:

- Narzędzie do sprawdzania gotowości platformy Microsoft Azure Stack
- Certyfikaty SSL wyeksportowane następujące [instrukcje przygotowań](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 lub Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Sprawdzania poprawności certyfikatu usług podstawowych

Wykonaj następujące kroki, aby przygotować się, jak i do sprawdzania poprawności certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack dla wdrożenia i wymiany klucza tajnego:

1. Zainstaluj **AzsReadinessChecker** w wierszu polecenia programu PowerShell (5.1 lub nowszej), uruchamiając następujące polecenie cmdlet:

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Tworzenie struktury katalogów certyfikatu. W poniższym przykładzie można zmienić `<c:\certificates>` nową ścieżkę katalogu wybranych przez użytkownika.
    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal','Admin Extension Host','Public Extension Host'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Wykres i usług AD FS są wymagane, jeśli używasz usług AD FS w systemie tożsamości.
    
     - Umieść swoje certyfikaty w katalogach odpowiedni, utworzony w poprzednim kroku. Na przykład:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. W oknie programu PowerShell, należy zmienić wartości **RegionName** i **FQDN** odpowiednie do środowiska usługi Azure Stack i uruchom następujące polecenie:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Sprawdź dane wyjściowe i wszystkich certyfikatów pomyślnie przejść wszystkie testy. Na przykład:

```PowerShell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
```

### <a name="known-issues"></a>Znane problemy

**Objaw**: Testy zostały pominięte.

**Przyczyna**: AzsReadinessChecker pomija pewne testy, jeśli zależność nie jest spełniony:

 - Inne certyfikaty są pomijane, jeśli łańcuch certyfikatów nie powiedzie się.

    ```PowerShell  
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

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Rozwiązanie**: Postępuj zgodnie z tego narzędzia ze wskazówkami zawartymi w sekcji szczegółów w ramach każdego zestawu testów dla każdego certyfikatu.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Platformy, należy wykonać jako Walidacja certyfikatów usług

Powyższe kroki należy wykonać do przygotowania i zweryfikuj certyfikaty infrastruktury kluczy publicznych do usługi Azure Stack dla platformy jako usługi (PaaS) certyfikaty, jeśli planowane jest ich wprowadzenie wdrożenia SQL/MySQL lub App Services.

1.  Zainstaluj **AzsReadinessChecker** w wierszu polecenia programu PowerShell (5.1 lub nowszej), uruchamiając następujące polecenie cmdlet:

    ```PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Tworzenie zagnieżdżonych hashtable, zawierające ścieżki i hasło, aby każdy z certyfikatów PaaS wymagające weryfikacji. W oknie programu PowerShell, uruchom:

    ```PowerShell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Zmienianie wartości **RegionName** i **FQDN** pod kątem danego środowiska usługi Azure Stack, aby rozpocząć sprawdzanie poprawności. Następnie uruchom polecenie:

    ```PowerShell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Sprawdź, czy dane wyjściowe i że wszystkie certyfikaty pomyślnie przejść wszystkie testy.

    ```PowerShell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>Certyfikaty

| Katalog | Certyfikat |
| ---    | ----        |
| acsBlob | wildcard_blob_\< region >\< externalFQDN > |
| ACSQueue  |  wildcard_queue\< region >\< externalFQDN > |
| ACSTable  |  wildcard_table\< region >\< externalFQDN > |
| Host rozszerzenia administratora  |  wildcard_adminhosting\< region >\< externalFQDN > |
| Portal administracyjny  |  adminportal\< region >\< externalFQDN > |
| Administrator ARM  |  adminmanagement\< region >\< externalFQDN > |
| Publiczne ARM  |  Zarządzanie\< region >\< externalFQDN > |
| KeyVault  |  wildcard_vault\< region >\< externalFQDN > |
| KeyVaultInternal  |  wildcard_adminvault\< region >\< externalFQDN > |
| Host rozszerzenia publiczne  |  wildcard_hosting\< region >\< externalFQDN > |
| Publiczny  |  Portal\< region > _\< externalFQDN > |

## <a name="using-validated-certificates"></a>Przy użyciu zweryfikowanych certyfikatów

Po sprawdzeniu poprawności certyfikatów przez AzsReadinessChecker można przystąpić do ich używać w ramach wdrożenia usługi Azure Stack lub obrotu wpisu tajnego z usługi Azure Stack. 

 - W przypadku wdrożenia bezpiecznego transferu certyfikatów do specjalistą wdrożenia, aby mógł on skopiować je na hoście wdrażania, jak to określono w [dokumentacji wymagań usługi Azure Stack infrastruktury kluczy publicznych](azure-stack-pki-certs.md).
 - Dla obrotu wpisu tajnego, można użyć certyfikatów do zaktualizowania stare certyfikaty dla punktów końcowych infrastruktury publicznego środowiska Azure Stack, postępując zgodnie z [dokumentacji usługi Azure Stack klucz tajny obrotu](azure-stack-rotate-secrets.md).
 - Dla usług PaaS, można użyć certyfikatów do zainstalowania SQL, MySQL i dostawcy zasobów usługi aplikacji w usłudze Azure Stack, postępując zgodnie z [Przegląd oferty usług w dokumentacji usługi Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Kolejne kroki

[Integracja tożsamości w centrum danych](azure-stack-integrate-identity.md)
