---
title: Włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Włącz usługę kopia zapasowa infrastruktury przy użyciu programu Windows PowerShell, tak, aby w przypadku awarii można przywrócić stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 6fbd82c3d49a4d64523bf0e10b67ce3aabe96de2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Włącz usługę kopia zapasowa infrastruktury przy użyciu programu Windows PowerShell, tak, aby w przypadku awarii można przywrócić stosu Azure. Można uzyskać dostępu do poleceń cmdlet programu PowerShell do włączenia kopii zapasowej, rozpocząć tworzenie kopii zapasowej i uzyskać informacje o kopii zapasowej za pośrednictwem punktu końcowego zarządzania operatora.

## <a name="download-azure-stack-tools"></a>Pobieranie narzędzia Azure stosu

Instalowanie i PowerShell skonfigurowanych dla stosu Azure i narzędzi Azure stosu. Zobacz [rozpocząć pracę przy użyciu programu PowerShell w stosie Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Ładowanie modułów Connect i infrastruktury

Otwórz program Windows PowerShell z podniesionego wiersza, a następnie uruchom następujące polecenia:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Instalator Rm środowiska i dziennika do punktu końcowego zarządzania — operator

W tej samej sesji programu PowerShell należy edytować następujący skrypt programu PowerShell, dodając zmienne dla danego środowiska. Uruchom skrypt zaktualizowane, aby skonfigurować środowisko RM i zaloguj się do punktu końcowego zarządzania operatora.

| Zmienna    | Opis |
|---          |---          |
| $TenantName | Nazwa dzierżawy Azure Active Directory. |
| Nazwa konta — operator        | Nazwa konta operator stosu Azure. |
| Punkt końcowy Menedżera zasobów Azure | Adres URL do usługi Azure Resource Manager. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Generowanie klucza szyfrowania

W tej samej sesji programu PowerShell uruchom następujące polecenia:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Należy użyć narzędzia AzureStack do wygenerowania klucza.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Podaj klucz udziału, poświadczeń i szyfrowanie kopii zapasowej do włączenia kopii zapasowej

W tej samej sesji programu PowerShell należy edytować następujący skrypt programu PowerShell, dodając zmienne dla danego środowiska. Uruchom skrypt zaktualizowane zapewnienie kopii zapasowej klucza udziału, poświadczeń i szyfrowanie usługi tworzenia kopii zapasowej infrastruktury.

| Zmienna        | Opis   |
|---              |---                                        |
| $username       | Typ **Username** przy użyciu domena i nazwa użytkownika dla lokalizacji udostępnionego dysku. Na przykład `Contoso\administrator`. |
| $password       | Typ **hasło** dla użytkownika. |
| $sharepath      | Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Ścieżka do udziału plików hostowane na osobnych urządzenie, musisz użyć ciągu Universal Naming Convention (UNC). Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. W celu zapewnienia dostępności danych kopii zapasowej, urządzenia powinny być w innej lokalizacji. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Potwierdź ustawienia kopii zapasowej

W tej samej sesji programu PowerShell uruchom następujące polecenia:

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

Wynik powinien wyglądać następujące dane wyjściowe JSON:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Kolejne kroki

 - Dowiedz się, jak uruchomić tworzenia kopii zapasowych, zobacz [kopia zapasowa Azure stosu](azure-stack-backup-back-up-azure-stack.md ).  
- Dowiedz się zweryfikować kopii zapasowej, zobacz [Potwierdź kopii zapasowej zostało ukończone w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md ).
