---
title: Dokumentacja poleceń cmdlet Start-AzsReadinessChecker | Dokumentacja firmy Microsoft
description: Pomocy dotyczącej poleceń cmdlet programu PowerShell dla modułu narzędzie do sprawdzania gotowości usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757782"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Dokumentacja poleceń cmdlet Start-AzsReadinessChecker

Moduł: **Microsoft.AzureStack.ReadinessChecker**

Ten moduł zawiera tylko jednego polecenia cmdlet. Polecenia cmdlet wykonuje jedną lub więcej funkcji przed wdrożeniem lub wstępnie obsługi dla usługi Azure Stack.

## <a name="syntax"></a>Składnia

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Opis

**Start AzsReadinessChecker** polecenia cmdlet sprawdza poprawność certyfikatów, kont platformy Azure, subskrypcje platformy Azure i katalogi Active Azure. Uruchom sprawdzanie poprawności przed wdrożeniem usługi Azure Stack lub przed obsługi akcje, takie jak obrotu tajny w usłudze Azure Stack. Polecenia cmdlet można również wygenerować certyfikat podpisywania żądania certyfikatów infrastruktury i, opcjonalnie, certyfikaty PaaS. Na koniec polecenia cmdlet można Przeprowadź ponowne pakowanie certyfikaty PFX do rozwiązywania typowych problemów z pakietu.

## <a name="examples"></a>Przykłady

### <a name="example-generate-certificate-signing-request"></a>Przykład: generowanie żądanie podpisania certyfikatu

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

W tym przykładzie `Start-AzsReadinessChecker` generuje wiele żądania (CSR) podpisywania certyfikatu dla certyfikatów odpowiednich dla wdrożenia usługi AD FS usługi Azure Stack przy użyciu nazwy regionu **wschód** i zewnętrznych nazwę FQDN  **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Przykład: Sprawdzanie poprawności certyfikatów

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

W tym przykładzie hasło PFX jest wymagany dla bezpieczeństwa i `Start-AzsReadinessChecker` sprawdza, czy folder względny **certyfikaty** certyfikatów jest prawidłowy na potrzeby wdrażania usługi AAD o nazwie regionu **wschód** i zewnętrzne nazwę FQDN **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Przykład: Sprawdzanie poprawności certyfikatów przy użyciu danych z wdrożenia (wdrażania i obsługi)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

W tym przykładzie wdrażania i obsługi hasła PFX jest wymagany dla bezpieczeństwa i `Start-AzsReadinessChecker` sprawdza, czy folder względny **certyfikaty** certyfikatów jest prawidłowy na potrzeby wdrożenia, których tożsamość, region i nazwy FQDN zewnętrznej odczytać z pliku JSON danych wdrożenia wygenerowany dla wdrożenia.

### <a name="example-validate-paas-certificates"></a>Przykład: Sprawdzanie poprawności certyfikatów PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

W tym przykładzie tablica skrótów jest konstruowany przy użyciu ścieżek i hasła do każdego certyfikatu PaaS. Certyfikaty można pominąć. `Start-AzsReadinessChecker` sprawdza, czy każda ścieżka PFX istnieje i sprawdza poprawność ich za pomocą region **wschód** i nazwy FQDN zewnętrznej **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Przykład: Sprawdzanie poprawności certyfikatów PaaS przy użyciu danych wdrożenia

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

W tym przykładzie tablica skrótów jest konstruowany przy użyciu ścieżek i hasła do każdego certyfikatu PaaS. Certyfikaty można pominąć. `Start-AzsReadinessChecker` sprawdza, czy każda ścieżka PFX istnieje i sprawdza poprawność ich za pomocą region, a tylko FQDN zewnętrznej odczytu z pliku JSON danych wdrożenia wygenerowany dla wdrożenia.

### <a name="example-validate-azure-identity"></a>Przykład: weryfikowania tożsamości platformy Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

W tym przykładzie są wymagane dla bezpieczeństwa poświadczenia konta administratora usługi i `Start-AzsReadinessChecker` sprawdza, czy konto platformy Azure i usługi Azure Active Directory są prawidłowe dla wdrożenia usługi AAD o nazwie katalogu dzierżawy  **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Przykład: Sprawdzanie poprawności tożsamości platformy Azure z danymi wdrażania (Obsługa wdrażania)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

W tym przykładzie są wymagane dla bezpieczeństwa poświadczenia konta administratora usługi i `Start-AzsReadinessChecker` sprawdza, czy konto platformy Azure i usługi Azure Active Directory są prawidłowe dla wdrożenia usługi AAD, gdzie **AzureCloud** i **TenantName** są odczytywane z pliku JSON wdrożenia dane generowane dla wdrożenia.

### <a name="example-validate-azure-registration"></a>Przykład: Sprawdzanie poprawności rejestracja w usłudze Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

W tym przykładzie są wymagane dla bezpieczeństwa poświadczenia właściciela subskrypcji i `Start-AzsReadinessChecker` następnie wykonuje sprawdzanie poprawności względem danego konta i subskrypcji, aby upewnić się, może służyć do rejestracji w usłudze Azure Stack.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Przykład: Sprawdzanie poprawności rejestracja w usłudze Azure z danymi wdrażania (zespół wdrażania)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

W tym przykładzie są wymagane dla bezpieczeństwa poświadczenia właściciela subskrypcji i `Start-AzsReadinessChecker` następnie wykonuje sprawdzanie poprawności względem danego konta i subskrypcji, aby upewnić się, może służyć do rejestracji usługi Azure Stack, w którym dodatkowe szczegóły są odczytywane z Plik JSON danych wdrożenia wygenerowany dla wdrożenia.

### <a name="example-importexport-pfx-package"></a>Przykład: importu/eksportu pakietu PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

W tym przykładzie hasło PFX jest wymagany dla zabezpieczeń. Plik Ssl.pfx jest zaimportowany do magazynu certyfikatów komputera lokalnego, ponownie eksportowana przy użyciu tego samego hasła i zapisywane jako Ssl_new.pfx. Ta procedura jest używany podczas weryfikacji certyfikatu oflagowane, że nie ma klucza prywatnego **komputera lokalnego** zestaw atrybutów, łańcuch certyfikatów został przerwany, nie ma znaczenia certyfikaty znajdują się w pliku PDF lub łańcuch certyfikatów jest w nieprawidłowej kolejności.

### <a name="example-view-validation-report-deployment-support"></a>Przykład: Wyświetl raport weryfikacji (Obsługa wdrażania)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

W tym przykładzie zespołu pomocy technicznej lub wdrożenia otrzymuje raport gotowości od klienta (Contoso) i używa `Start-AzsReadinessChecker` Aby wyświetlić stan sprawdzania poprawności wykonaną w Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Przykład: Wyświetl raport weryfikacji Podsumowanie certyfikatu weryfikacji tylko (wdrażania i obsługi)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

W tym przykładzie zespołu pomocy technicznej lub wdrożenia otrzymuje raport gotowości od klienta (Contoso) i używa `Start-AzsReadinessChecker` do wyświetlania podsumowania stanu certyfikatu weryfikacji wykonaną w Contoso.

## <a name="required-parameters"></a>Wymagane parametry

### <a name="-regionname"></a>-RegionName

Określa nazwę region wdrożenia usługi Azure Stack.

|  |  |
|----------------------------|--------------|
|Wpisz:                       |String        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |Brak          |
|Akceptować wejście potokowe:      |False         |
|Akceptować symbole wieloznaczne: |False         |

### <a name="-fqdn"></a>-FQDN

Określa wdrożenia usługi Azure Stack FQDN zewnętrznej, również aliasowana jako **ExternalFQDN** i **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Wpisz:                       |String        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |ExternalFQDN, ExternalDomainName |
|Akceptować wejście potokowe:      |False         |
|Akceptować symbole wieloznaczne: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Określa usługi Azure Stack wdrożenia tożsamości systemu prawidłowe wartości, AAD lub ADFS, Azure Active Directory i Active Directory Federation Services, odpowiednio.

|  |  |
|----------------------------|--------------|
|Wpisz:                       |String        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |Brak          |
|Prawidłowe wartości:               |"AAD", "ADFS"  |
|Akceptować wejście potokowe:      |False         |
|Akceptować symbole wieloznaczne: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Określa hasło skojarzone z plików certyfikatów PFX.

|  |  |
|----------------------------|---------|
|Wpisz:                       |SecureString |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Określa hashtable zawierające ścieżki i hasła, certyfikaty PaaS.

|  |  |
|----------------------------|---------|
|Wpisz:                       |Tablica skrótów |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Określa plik konfiguracji JSON danych wdrożenia usługi Azure Stack. Ten plik został wygenerowany dla wdrożenia.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-pfxpath"></a>-PfxPath

Określa ścieżkę do problematycznych certyfikatu, który wymaga procedury importu/eksportu, aby rozwiązać problem, wskazane przez weryfikację certyfikatu w tym narzędziu.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Określa ścieżkę docelową dla wynikowego pliku PFX z procedury importowania/eksportowania.  

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-subject"></a>-Podmiotu

Określa słownika uporządkowane podmiotu do generowania żądania certyfikatu.

|  |  |
|----------------------------|---------|
|Wpisz:                       |Element OrderedDictionary   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-requesttype"></a>-RequestType

Określa typ sieci SAN w żądaniu certyfikatu. Prawidłowe wartości to **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** generuje wiele żądań certyfikatów, jeden dla każdej usługi.
- **SingleCSR** generuje jedno żądanie certyfikatu dla wszystkich usług.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Prawidłowe wartości:               |'MultipleCSR','SingleCSR' |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Określa ścieżkę docelową dla plików żądania certyfikatu. Katalog musi już istnieć.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Określa, administrator usługi Azure Active Directory służący do wdrażania usługi Azure Stack.

|  |  |
|----------------------------|---------|
|Wpisz:                       |PSCredential   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Określa nazwę usługi Azure Active Directory, można użyć do wdrożenia usługi Azure Stack.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Określa wystąpienie usług platformy Azure, zawierającą konta, katalogów i subskrypcji służący do wdrażania usługi Azure Stack i rejestrowania.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Prawidłowe wartości:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Określa konto rejestracji, który ma być używany do rejestracji w usłudze Azure Stack.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Określa identyfikator subskrypcji rejestracji, który ma być używany do rejestracji w usłudze Azure Stack.

|  |  |
|----------------------------|---------|
|Wpisz:                       |Guid     |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-reportpath"></a>-ReportPath

Określa ścieżkę do raportu gotowość, wartością domyślną jest bieżący katalog i domyślne nazwę raportu.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Wszyscy      |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

## <a name="optional-parameters"></a>Parametry opcjonalne

### <a name="-certificatepath"></a>-CertificatePath

Określa ścieżkę, pod którym wymagane foldery certyfikatu znajdują się tylko certyfikat.

Dostępne są następujące foldery wymagane do wdrożenia usługi Azure Stack z systemem obsługi tożsamości usługi Azure Active Directory:

ACSBlob ACSQueue, ACSTable, Portal administratora, administrator usługi ARM ARM Public, magazynu kluczy, KeyVaultInternal, publiczny

Wymagane folderu dla usługi Azure Stack, które są wdrażanie za pomocą usług federacyjnych Active Directory systemu tożsamości:

ACSBlob, ACSQueue, ACSTable, usług AD FS, portalu administracyjnym, ARM administratora, publiczny ARM, programu Graph, magazynu kluczy, KeyVaultInternal, publiczny

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |. \Certificates |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Określa, czy nazwy hosta/usług PaaS, powinny zostać dodane do żądania certyfikatu.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

### <a name="-reportsections"></a>-ReportSections

Określa, czy tylko w celu wyświetlenia raportu podsumowania, pomija szczegółów.

|  |  |
|----------------------------|---------|
|Wpisz:                       |String   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Wszyscy      |
|Prawidłowe wartości:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

### <a name="-summary"></a>— Podsumowanie

Określa, czy tylko w celu wyświetlenia raportu podsumowania, pomija szczegółów.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

### <a name="-cleanreport"></a>-CleanReport

Usuwa historię wykonywania i sprawdzania poprawności i zapisuje operacji sprawdzania poprawności nowego raportu.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

### <a name="-outputpath"></a>-OutputPath

Określa niestandardową ścieżkę, aby zapisać raport w formacie JSON gotowości i pełny plik dziennika. Jeśli ścieżka nie istnieje, polecenie próbuje utworzyć katalogu.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |String            |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |$ENV:TEMP\AzsReadinessChecker  |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

### <a name="-confirm"></a>-Upewnij się

Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

### <a name="-whatif"></a>-WhatIf

Pokazuje, co się stanie po uruchomieniu polecenia cmdlet. Polecenie cmdlet nie jest uruchomione.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliases:                    |wi                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |
