---
title: Dokumentacja poleceń cmdlet Start-AzsReadinessChecker | Dokumentacja firmy Microsoft
description: Pomoc polecenia cmdlet programu PowerShell dla modułu sprawdzania gotowości stosu Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Dokumentacja poleceń cmdlet Start-AzsReadinessChecker

Moduł: Microsoft.AzureStack.ReadinessChecker

Ten moduł zawiera tylko jednego polecenia cmdlet.  To polecenie cmdlet przeprowadza co najmniej jedną funkcję przed wdrożeniem lub wstępnie obsługi stosu Azure.

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
**Start AzsReadinessChecker** polecenia cmdlet weryfikuje certyfikaty, Azure konta subskrypcji platformy Azure i usług Azure Active Directory. Przeprowadzenie weryfikacji przed wdrożeniem stosu Azure lub przed stosu Azure obsługi akcji, takich jak obrót klucz tajny. Polecenia cmdlet można także wygenerować żądania podpisania certyfikatu dla infrastruktury i opcjonalnie PaaS certyfikatami.  Polecenia cmdlet można ponadto Spakuj ponownie certyfikatów PFX w celu rozwiązania typowych problemów z pakietów.

## <a name="examples"></a>Przykłady
**Przykład: Generowanie żądania podpisania certyfikatu**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

W tym przykładzie Start AzsReadinessChecker generuje wiele żądań (Podpisania certyfikatu) dla certyfikatów umożliwiających wdrożenie usług AD FS Azure stosu z nazwy regionu "Wschód" i nazwy FQDN zewnętrznej "azurestack.contoso.com"

**Przykład: Sprawdzanie poprawności certyfikatów**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

W tym przykładzie hasła do pliku PFX jest monitowany o podanie bezpiecznego i Start AzsReadinessChecker sprawdza folder względny "Certyfikaty" certyfikaty dla wdrożenia usługi AAD, o nazwie region "Wschód" i nazwy FQDN zewnętrznej "azurestack.contoso.com" 

**Przykład: Sprawdzanie poprawności certyfikatów z danymi wdrożenia (wdrożenia i pomocy technicznej)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
W tym przykładzie wdrażania i obsługi hasła do pliku PFX jest monitowany o podanie bezpiecznego i Start AzsReadinessChecker sprawdza folder względny "Certyfikaty" prawidłowy na potrzeby wdrożenia, w którym tożsamości, regionu i nazwy FQDN zewnętrznej są odczytywane z certyfikatów Plik JSON danych wdrożenia wygenerowany dla wdrożenia. 

**Przykład: Sprawdzanie poprawności PaaS certyfikatów**
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

W tym przykładzie obiektu hashtable jest tworzony przy użyciu ścieżek i hasła do każdego certyfikatu PaaS. Certyfikaty można pominąć. Początek AzsReadinessChecker sprawdza każda ścieżka PFX istnieje i weryfikuje je za pomocą obszaru "Wschód" i nazwy FQDN zewnętrznej "azurestack.contoso.com".

**Przykład: Zweryfikować PaaS certyfikaty z danymi wdrożenia**
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

W tym przykładzie obiektu hashtable jest tworzony przy użyciu ścieżek i hasła do każdego certyfikatu PaaS. Certyfikaty można pominąć. Start AzsReadinessChecker sprawdza, każda ścieżka PFX istnieje i weryfikuje je za pomocą obszaru i nazwy FQDN zewnętrznej odczytu z pliku JSON danych wdrożenia wygenerowany dla wdrożenia. 

**Przykład: Zweryfikować tożsamość platformy Azure**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

W tym przykładzie poświadczeń konta administratora usługi są monitowani o podanie bezpiecznego i rozpoczęcia AzsReadinessChecker sprawdza, czy konto platformy Azure i usługi Azure Active Directory do wdrożenia usługi AAD z nazwą katalogu dzierżawy "azurestack.contoso.com"


**Przykład: Zweryfikować tożsamość platformy Azure z danymi wdrożenia (Obsługa wdrażania)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

W tym przykładzie poświadczeń konta administratora usługi są monitowani o podanie bezpiecznego i rozpoczęcia AzsReadinessChecker sprawdza, czy konto platformy Azure i usługi Azure Active Directory są prawidłowe dla wdrożenia usługi AAD gdzie AzureCloud i TenantName są odczytywane z danych wdrożenia Plik JSON wygenerowany dla wdrożenia.


**Przykład: Sprawdzanie poprawności rejestracja w usłudze Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

W tym przykładzie poświadczenia właściciela subskrypcji są monitowani o podanie bezpiecznego Start AzsReadinessChecker wykonuje następnie sprawdzenia poprawności względem podane konto i subskrypcję, aby upewnić się, że może służyć do rejestracji stosu Azure. 


**Przykład: Sprawdzanie poprawności rejestracja w usłudze Azure z danymi wdrożenia (zespół wdrażania)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

W tym przykładzie poświadczenia właściciela subskrypcji są monitowani o podanie bezpiecznego i Start AzsReadinessChecker wykonuje następnie sprawdzenia poprawności względem podane konto i subskrypcję, aby upewnić się, że może służyć do rejestracji stosu Azure gdzie znajdują się dodatkowe szczegóły odczytany z pliku JSON danych wdrożenia wygenerowany dla wdrożenia.

**Przykład: PFX importu/eksportu pakietu**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

W tym przykładzie hasła do pliku PFX jest monitowany o podanie bezpiecznego. Plik ssl.pfx zostaną zaimportowane do magazynu certyfikatów komputera lokalnego i ponownie wyeksportowany z tego samego hasła i zapisane jako ssl_new.pfx.  Ta procedura jest przeznaczona do użytku podczas weryfikacji certyfikatu oflagowane, że klucz prywatny nie ma ustawiony atrybut komputera lokalnego, łańcuch certyfikatów jest uszkodzony, nie znaczenia certyfikatów znajdują się w pliku PDF lub łańcuch certyfikatów jest w niewłaściwej kolejności.


**Przykład: Wyświetl raport weryfikacji (Obsługa wdrażania)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

W tym przykładzie zespołu wdrażania lub pomocy technicznej otrzymywać raport gotowości klienta (Contoso) i umożliwia wyświetlanie stanu wykonania sprawdzania poprawności, wykonywane Contoso Start AzsReadinessChecker.

**Przykład: Wyświetl raport weryfikacji Podsumowanie certyfikatu weryfikacji tylko (wdrożenia i pomocy technicznej)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

W tym przykładzie zespołu wdrażania lub pomocy technicznej otrzymywać raport gotowości klienta Contoso i umożliwia przeglądanie podsumowań stanu wykonaniami weryfikacji certyfikatu wykonywane Contoso Start AzsReadinessChecker.



## <a name="required-parameters"></a>Wymagane parametry
> -RegionName

Określa nazwę regionu wdrożenia stosu Azure.
|  |  |
|----------------------------|--------------|
|Wpisz:                       |Ciąg        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |Brak          |
|Akceptowanie danych wejściowych potoku:      |False         |
|Akceptowanie symboli wieloznacznych: |False         |

> -FQDN    

Określa zewnętrzne nazwy FQDN wdrożenia stosu Azure, również używane z aliasem jako ExternalFQDN i ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Wpisz:                       |Ciąg        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |ExternalFQDN, ExternalDomainName |
|Akceptowanie danych wejściowych potoku:      |False         |
|Akceptowanie symboli wieloznacznych: |False         |

 

> -IdentitySystem    

Określa wdrożenia stosu Azure systemu tożsamości prawidłowe wartości, AAD lub ADFS, dla usługi Azure Active Directory i Active Directory Federation Services odpowiednio.
|  |  |
|----------------------------|--------------|
|Wpisz:                       |Ciąg        |
|Położenie:                   |o nazwie         |
|Wartość domyślna:              |Brak          |
|Prawidłowe wartości:               |"AAD", "USŁUG AD FS"  |
|Akceptowanie danych wejściowych potoku:      |False         |
|Akceptowanie symboli wieloznacznych: |False         |

> -PfxPassword    

Określa hasło skojarzone z pliki certyfikatów PFX.
|  |  |
|----------------------------|---------|
|Wpisz:                       |SecureString |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -PaaSCertificates

Określa hashtable zawierający ścieżek i hasła do PaaS certyfikatów.
|  |  |
|----------------------------|---------|
|Wpisz:                       |HashTable |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -DeploymentDataJSONPath

Określa plik konfiguracji stosu Azure wdrożenia danych JSON. Ten plik został wygenerowany dla wdrożenia.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -PfxPath

Określa ścieżkę do problematyczne certyfikatu, który wymaga importu/eksportu procedury, aby rozwiązać problem, wskazywany przez weryfikacji certyfikatu w tym narzędziu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -ExportPFXPath  

Określa ścieżkę docelową dla wynikowego pliku PFX z procedury importowania/eksportowania.  
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -Podmiotu

Określa uporządkowanej słownika podmiot generowania żądania certyfikatu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Element OrderedDictionary   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -RequestType

Określa typ sieci SAN żądania certyfikatu. Prawidłowe wartości MultipleCSR, SingleCSR.
- *MultipleCSR* generuje wiele żądań certyfikatów, po jednej dla każdej usługi.
- *SingleCSR* generuje jedno żądanie certyfikatu dla wszystkich usług.   

|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Prawidłowe wartości:               |"MultipleCSR", "SingleCSR" |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -OutputRequestPath

Określa ścieżkę docelową dla plików żądania certyfikatu, katalog musi już istnieć.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -AADServiceAdministrator

Określa administratora usługi Azure Active Directory służący do wdrożenia usługi Azure stosu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |PSCredential   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -AADDirectoryTenantName

Określa nazwę usługi Azure Active Directory służący do wdrożenia usługi Azure stosu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -AzureEnvironment

Określa wystąpienie usług Azure, zawierającą konta, katalogów i subskrypcji do zastosowania w przypadku wdrożenia stosu Azure i rejestracji.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Prawidłowe wartości:               |"AzureCloud", "AzureChinaCloud", "AzureGermanCloud" |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -RegistrationAccount

Określa konto rejestracji do używanych w stosie Azure rejestracji.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -RegistrationSubscriptionID

Określa identyfikator rejestracji subskrypcji do używanych w stosie Azure rejestracji.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Identyfikator GUID     |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Brak     |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |

> -ReportPath

Określa ścieżkę do Raport gotowości, domyślnie nazwa bieżącego katalogu i domyślne raportu.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Wszyscy      |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |



## <a name="optional-parameters"></a>Parametry opcjonalne
> -CertificatePath     

Określa ścieżkę, w którym wymagane foldery certyfikatu znajdują się tylko certyfikat.

Foldery wymagane do wdrożenia stosu Azure z systemu tożsamości w usłudze Azure Active Directory są:

ACSBlob, ACSQueue, ACSTable, portalu administracyjnego, administrator ARM ARM publicznego, KeyVault, KeyVaultInternal, publiczny

Wymagany folder stosu Azure są wdrażanie za pomocą usług federacyjnych Active Directory systemu tożsamości:

ACSBlob, ACSQueue, ACSTable, usługi AD FS, portalu administratora, administratora ARM, publicznego ARM, wykres, KeyVault, KeyVaultInternal, publiczny


|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |. \Certificates |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |


> -IncludePaaS  

Określa, czy usługi PaaS jako nazw hostów powinny zostać dodane do żądania certyfikatu.


|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptowanie danych wejściowych potoku:      |False             |
|Akceptowanie symboli wieloznacznych: |False             |


> -ReportSections        

Określa, czy tylko w celu wyświetlenia raportu podsumowania, pomija szczegółów.
|  |  |
|----------------------------|---------|
|Wpisz:                       |Ciąg   |
|Położenie:                   |o nazwie    |
|Wartość domyślna:              |Wszyscy      |
|Prawidłowe wartości:               |"Certyfikat", "AzureRegistration", "AzureIdentity", "Zadania", "All" |
|Akceptowanie danych wejściowych potoku:      |False    |
|Akceptowanie symboli wieloznacznych: |False    |


> — Podsumowanie 

Określa, czy tylko w celu wyświetlenia raportu podsumowania, pomija szczegółów.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptowanie danych wejściowych potoku:      |False             |
|Akceptowanie symboli wieloznacznych: |False             |


> -CleanReport  

Usuwa historię wykonywania i sprawdzania poprawności i zapisuje operacji sprawdzania poprawności nowego raportu.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliasy:                    |CF                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptowanie danych wejściowych potoku:      |False             |
|Akceptowanie symboli wieloznacznych: |False             |


> -OutputPath    

Określa niestandardową ścieżkę, aby zapisać raport gotowości JSON i pełny plik dziennika.  Jeśli ścieżka nie istnieje, narzędzie będzie podejmować próby utworzenia katalogu.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |Ciąg            |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |$ENV: TEMP\AzsReadinessChecker  |
|Akceptowanie danych wejściowych potoku:      |False             |
|Akceptowanie symboli wieloznacznych: |False             |


> -Confirm  

Wyświetla monit o potwierdzenie przed uruchomieniem polecenia cmdlet.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliasy:                    |CF                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptowanie danych wejściowych potoku:      |False             |
|Akceptowanie symboli wieloznacznych: |False             |


> -WhatIf  

Pokazuje, co się stanie po uruchomieniu polecenia cmdlet. Polecenie cmdlet nie zostało uruchomione.
|  |  |
|----------------------------|------------------|
|Wpisz:                       |SwitchParameter   |
|Aliasy:                    |Wi                |
|Położenie:                   |o nazwie             |
|Wartość domyślna:              |False             |
|Akceptowanie danych wejściowych potoku:      |False             |
|Akceptowanie symboli wieloznacznych: |False             |

 
