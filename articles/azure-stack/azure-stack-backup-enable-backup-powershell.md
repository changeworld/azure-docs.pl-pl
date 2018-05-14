---
title: Włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Włącz usługę kopia zapasowa infrastruktury przy użyciu programu Windows PowerShell, tak, aby w przypadku awarii można przywrócić stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4faa6930c37f9d491a3efa4b34519dbb13761a9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Włącz usługę kopia zapasowa infrastruktury przy użyciu programu Windows PowerShell tak wykonać okresowe kopie zapasowe:
 - Wewnętrzny tożsamości usługi i certyfikatu głównego
 - Plany użytkownika, oferty, subskrypcje
 - Klucze tajne Keyvault
 - Role RBAC użytkownika i zasady

Można uzyskać dostępu do poleceń cmdlet programu PowerShell do włączenia kopii zapasowej, rozpocząć tworzenie kopii zapasowej i uzyskać informacje o kopii zapasowej za pośrednictwem punktu końcowego zarządzania operatora.

## <a name="prepare-powershell-environment"></a>Przygotowanie środowiska PowerShell

Aby uzyskać instrukcje na temat konfigurowania środowiska PowerShell, zobacz [Zainstaluj program PowerShell Azure stosu ](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Generowanie klucza szyfrowania

Instalowanie i PowerShell skonfigurowanych dla stosu Azure i narzędzi Azure stosu.
 - Zobacz [rozpocząć pracę przy użyciu programu PowerShell w stosie Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Zobacz [narzędzia stosu Azure pobrać z witryny GitHub](azure-stack-powershell-download.md)

Otwórz program Windows PowerShell z podniesionego wiersza, a następnie uruchom następujące polecenia:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
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
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
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