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
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: af959507fc2e0d1b68f547d2856eb7020d3ed5c6
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247580"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Dokumentacja poleceń cmdlet Start-AzsReadinessChecker

Moduł: Microsoft.AzureStack.ReadinessChecker

Ten moduł zawiera tylko jednego polecenia cmdlet.  To polecenie cmdlet wykonuje jedną lub więcej funkcji przed wdrożeniem lub wstępnie obsługi dla usługi Azure Stack.

## <a name="syntax"></a>Składnia

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
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

**Start AzsReadinessChecker** polecenia cmdlet sprawdza poprawność certyfikatów, kont platformy Azure, subskrypcje platformy Azure i katalogi Active Azure. Uruchom sprawdzanie poprawności przed wdrożeniem usługi Azure Stack lub przed obsługi akcji, takich jak obrotu klucz tajny w usłudze Azure Stack. Polecenia cmdlet można również wygenerować żądania podpisania certyfikatu dla certyfikatów infrastruktury i opcjonalnie certyfikaty PaaS.  Na koniec polecenia cmdlet można Przeprowadź ponowne pakowanie certyfikaty PFX do rozwiązywania typowych problemów z pakietu.

## <a name="examples"></a>Przykłady

### <a name="example-generate-certificate-signing-request"></a>Przykład: Generowanie żądanie podpisania certyfikatu

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

W tym przykładzie Start AzsReadinessChecker generuje wiele certyfikatów podpisywania żądań obsługi dla certyfikatów odpowiednich dla wdrożenia usług AD FS usługi Azure Stack przy użyciu nazwy regionu "Wschód" i nazwę FQDN zewnętrznej "azurestack.contoso.com"

### <a name="example-validate-certificates"></a>Przykład: Sprawdzanie poprawności certyfikatów

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

W tym przykładzie hasło PFX jest monitowany o podanie bezpiecznego i rozpoczęcia AzsReadinessChecker sprawdza folderu względnego "Certyfikaty" w przypadku certyfikatów jest prawidłowy na potrzeby wdrażania usługi AAD, o nazwie regionu "Wschód" i nazwę FQDN zewnętrznej "azurestack.contoso.com"

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Przykład: Sprawdzanie poprawności certyfikatów przy użyciu danych z wdrożenia (wdrażania i obsługi)

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

W tym przykładzie wdrażania i obsługi hasła PFX jest monitowany o podanie bezpiecznego i rozpoczęcia AzsReadinessChecker sprawdza folderu względnego "Certyfikaty" prawidłowy na potrzeby wdrożenia, gdy tożsamość, region i nazwy FQDN zewnętrznej są odczytywane z certyfikatami Plik JSON danych wdrożenia wygenerowany dla wdrożenia. 

### <a name="example-validate-paas-certificates"></a>Przykład: Sprawdzanie poprawności certyfikatów PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

W tym przykładzie tablica skrótów jest konstruowany przy użyciu ścieżek i hasła do każdego certyfikatu PaaS. Certyfikaty można pominąć. Start AzsReadinessChecker sprawdza, czy każda ścieżka PFX istnieje i weryfikuje je przy użyciu region "Wschód" i FQDN zewnętrznej "azurestack.contoso.com".

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Przykład: Sprawdzanie poprawności certyfikatów PaaS przy użyciu danych wdrożenia

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

W tym przykładzie tablica skrótów jest konstruowany przy użyciu ścieżek i hasła do każdego certyfikatu PaaS. Certyfikaty można pominąć. Start AzsReadinessChecker sprawdza, czy każda ścieżka PFX istnieje i sprawdza poprawność ich za pomocą region i FQDN zewnętrznej odczytu z pliku JSON danych wdrożenia wygenerowany dla wdrożenia. 

### <a name="example-validate-azure-identity"></a>Przykład: Weryfikowanie tożsamości platformy Azure

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

W tym przykładzie poświadczenia konta administratora usługi są monitowani o podanie bezpiecznego i rozpoczęcia AzsReadinessChecker sprawdza, czy konto platformy Azure i usługi Azure Active Directory są prawidłowe dla wdrożenia usługi AAD o nazwie katalogu dzierżawy "azurestack.contoso.com"

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Przykład: Weryfikowanie usługi Azure identity danymi wdrażania (Obsługa wdrażania)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

W tym przykładzie poświadczenia konta administratora usługi są monitowani o podanie bezpiecznego i Start AzsReadinessChecker sprawdza, czy konto platformy Azure i usługi Azure Active Directory są prawidłowe dla wdrożenia usługi AAD gdzie AzureCloud i TenantName są odczytywane z danych wdrożenia Plik JSON, generowany dla wdrożenia.

### <a name="example-validate-azure-registration"></a>Przykład: Sprawdź poprawność rejestracji platformy Azure

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

W tym przykładzie poświadczenia właściciela subskrypcji są monitowani o podanie bezpiecznego Start AzsReadinessChecker wykonuje następnie weryfikacji względem podane konto i subskrypcję, aby upewnić się, że może służyć do rejestracji w usłudze Azure Stack. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Przykład: Sprawdź poprawność rejestracja w usłudze Azure z danymi wdrażania (zespół wdrażania)

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

W tym przykładzie poświadczenia właściciela subskrypcji są monitowani o podanie bezpiecznego Start AzsReadinessChecker następnie wykonuje sprawdzanie poprawności względem danego konta i subskrypcji, aby upewnić się, że może służyć do rejestracji w usłudze Azure Stack gdzie znajdują się dodatkowe szczegóły odczytać z pliku JSON danych wdrożenia wygenerowany dla wdrożenia.

### <a name="example-importexport-pfx-package"></a>Przykład: Pakiet PFX importu/eksportu

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

W tym przykładzie hasło PFX jest monitowany o podanie bezpiecznego. Plik ssl.pfx zostaną zaimportowane do magazynu certyfikatów komputera lokalnego i ponownie eksportowana przy użyciu tego samego hasła i zapisywane jako ssl_new.pfx.  Ta procedura jest do użytku podczas sprawdzania poprawności certyfikatu oflagowane, czy klucz prywatny nie mają ustawionego atrybutu komputera lokalnego, łańcuch certyfikatów jest uszkodzony, nie ma znaczenia certyfikaty znajdują się w pliku PDF lub łańcuch certyfikatów jest w złej kolejności.

### <a name="example-view-validation-report-deployment-support"></a>Przykład: Wyświetl raport weryfikacji (Obsługa wdrażania)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

W tym przykładzie zespołu pomocy technicznej lub wdrożenia otrzymywać raport gotowości klienta (Contoso) i użyj Start AzsReadinessChecker, aby wyświetlić stan wykonania sprawdzania poprawności, wykonania firmy Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Przykład: Wyświetl raport weryfikacji Podsumowanie certyfikatu weryfikacji tylko (wdrażania i obsługi)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

W tym przykładzie zespołu pomocy technicznej lub wdrożenia otrzymywać raport gotowości odbiorca Contoso i umożliwia wyświetlanie podsumowania stanu wykonań sprawdzania poprawności certyfikatów, wykonać Contoso AzsReadinessChecker rozpoczęcia.

## <a name="required-parameters"></a>Wymagane parametry

> -RegionName

Określa nazwę region wdrożenia usługi Azure Stack.
|  |  |
|----------------------------|--------------|
|Wpisz:                       |Ciąg        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |Brak          |
|Akceptować wejście potokowe:      |False         |
|Akceptować symbole wieloznaczne: |False         |

> — NAZWA FQDN

Określa FQDN zewnętrznej wdrożenia usługi Azure Stack, również aliasowana jako ExternalFQDN i ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Wpisz:                       |Ciąg        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |ExternalFQDN, ExternalDomainName |
|Akceptować wejście potokowe:      |False         |
|Akceptować symbole wieloznaczne: |False         |

> -IdentitySystem

Określa odpowiednio wdrożenia usługi Azure Stack System obsługi tożsamości prawidłowe wartości, AAD lub ADFS, dla usługi Azure Active Directory i Active Directory Federation Services.
|  |  |
|----------------------------|--------------|
|Wpisz:                       |Ciąg        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |Brak          |
|Prawidłowe wartości:               |"AAD", "ADFS"  |
|Akceptować wejście potokowe:      |False         |
|Akceptować symbole wieloznaczne: |False         |

> -PfxPassword

Określa hasło skojarzone z plików certyfikatów PFX.
|  |  |
|----------------------------|---------|
|Wpisz:                       |SecureString |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -PaaSCertificates

Określa hashtable zawierające ścieżki i hasła, certyfikaty PaaS.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Tablica skrótów |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -DeploymentDataJSONPath

Określa plik konfiguracji JSON danych wdrożenia usługi Azure Stack. Ten plik został wygenerowany dla wdrożenia.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -PfxPath

Określa ścieżkę do problematycznych certyfikatu, który wymaga importu/eksportu procedury, aby rozwiązać problem, wskazane przez weryfikację certyfikatu w tym narzędziu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -ExportPFXPath  

Określa ścieżkę docelową dla wynikowego pliku PFX z procedury importowania/eksportowania.  
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -Podmiotu

Określa słownika uporządkowane podmiotu do generowania żądania certyfikatu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Element OrderedDictionary   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -RequestType

Określa typ sieci SAN w żądaniu certyfikatu. Prawidłowe wartości MultipleCSR, SingleCSR.

- *MultipleCSR* generuje wiele żądań certyfikatów, jeden dla każdej usługi.
- *SingleCSR* generuje jedno żądanie certyfikatu dla wszystkich usług.

|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Prawidłowe wartości:               |"MultipleCSR", "SingleCSR" |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -OutputRequestPath

Określa ścieżkę docelową dla plików żądania certyfikatu, katalog musi istnieć.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -AADServiceAdministrator

Określa administratora usługi usługi Azure Active Directory służący do wdrażania usługi Azure Stack.
|  |  |
|----------------------------|---------|
|Wpisz:                       |PSCredential   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -AADDirectoryTenantName

Określa nazwę usługi Azure Active Directory, można użyć do wdrożenia usługi Azure Stack.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -AzureEnvironment

Określa wystąpienie usług platformy Azure, zawierającą konta, katalogów i subskrypcji służący do wdrażania usługi Azure Stack i rejestrowania.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Prawidłowe wartości:               |"AzureCloud", "AzureChinaCloud", "AzureUSGovernment" |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -RegistrationAccount

Określa konto rejestracji, który ma być używany do rejestracji w usłudze Azure Stack.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -RegistrationSubscriptionID

Określa identyfikator subskrypcji rejestracji, który ma być używany do rejestracji w usłudze Azure Stack.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Identyfikator GUID     |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -ReportPath

Określa ścieżkę do raportu gotowości, wartością domyślną jest bieżący katalog i domyślne nazwę raportu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Wszyscy      |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

## <a name="optional-parameters"></a>Parametry opcjonalne

> -CertificatePath

Określa ścieżkę, pod którym wymagane foldery certyfikatu znajdują się tylko certyfikat.

Dostępne są następujące foldery wymagane do wdrożenia usługi Azure Stack z systemem obsługi tożsamości usługi Azure Active Directory:

ACSBlob ACSQueue, ACSTable, Portal administratora, administrator usługi ARM ARM Public, magazynu kluczy, KeyVaultInternal, publiczny

Wymagane folderu dla usługi Azure Stack, które są wdrażanie za pomocą usług federacyjnych Active Directory systemu tożsamości:

ACSBlob, ACSQueue, ACSTable, usług AD FS, portalu administracyjnym, ARM administratora, publiczny ARM, programu Graph, magazynu kluczy, KeyVaultInternal, publiczny

|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |. \Certificates |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> -IncludePaaS  

Określa, jeśli usługi PaaS jako nazw hostów powinny zostać dodane do żądania certyfikatu.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

> -ReportSections

Określa, czy tylko w celu wyświetlenia raportu podsumowania, pomija szczegółów.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Wszyscy      |
|Prawidłowe wartości:               |"Certyfikat", "AzureRegistration", "AzureIdentity", "Zadania", "All" |
|Akceptować wejście potokowe:      |False    |
|Akceptować symbole wieloznaczne: |False    |

> — Podsumowanie

Określa, czy tylko w celu wyświetlenia raportu podsumowania, pomija szczegółów.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

> -CleanReport

Usuwa historię wykonywania i sprawdzania poprawności i zapisuje operacji sprawdzania poprawności nowego raportu.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliasy:                    |usługi CF                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

> -OutputPath

Określa ścieżkę niestandardową, aby zapisać raport gotowości JSON i pełny plik dziennika.  Jeśli ścieżka nie istnieje, narzędzie spróbuje utworzyć katalog.

|  |  |
|----------------------------|------------------|
|Wpisz:                       |Ciąg            |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |$ENV: TEMP\AzsReadinessChecker  |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

> -Upewnij się

Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliasy:                    |usługi CF                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |

> -WhatIf

Pokazuje, co się stanie po uruchomieniu polecenia cmdlet. Polecenie cmdlet nie jest uruchomione.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliasy:                    |Wi                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptować wejście potokowe:      |False             |
|Akceptować symbole wieloznaczne: |False             |
