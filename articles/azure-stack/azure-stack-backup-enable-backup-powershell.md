---
title: Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Włącz usługę Backup infrastruktury za pomocą programu Windows PowerShell usługi Azure Stack mogą zostać przywrócone, jeśli wystąpi awaria.
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
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242958"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Włącz usługę Backup infrastruktury za pomocą programu Windows PowerShell więc warto poświęcić okresowe kopie zapasowe:
 - Wewnętrzny tożsamości usługi i certyfikatu głównego
 - Plany, oferty i subskrypcje
 - Wpisów tajnych usługi Keyvault
 - Role RBAC użytkownika i zasady

Można uzyskać dostęp do poleceń cmdlet programu PowerShell do włączenia kopii zapasowej, rozpocząć tworzenie kopii zapasowej i pobieranie informacji o kopii zapasowej za pośrednictwem punktu końcowego zarządzania operatora.

## <a name="prepare-powershell-environment"></a>Przygotuj środowisko programu PowerShell

Aby uzyskać instrukcje dotyczące konfigurowania środowiska PowerShell, zobacz [Instalowanie programu PowerShell dla usługi Azure Stack ](azure-stack-powershell-install.md). Aby zalogować się do usługi Azure Stack, zobacz [skonfigurować środowisko operatora i zaloguj się do usługi Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Podaj klucz udziału, poświadczenia i szyfrowania kopii zapasowych włączyć tworzenie kopii zapasowych

W tej samej sesji programu PowerShell należy edytować poniższy skrypt programu PowerShell, dodając zmienne dla danego środowiska. Uruchom skrypt zaktualizowane zapewnienie infrastruktury usługi kopii zapasowej klucza kopii zapasowej, udział, poświadczenia i szyfrowania.

| Zmienna        | Opis   |
|---              |---                                        |
| $username       | Typ **Username** użycie domena i nazwa użytkownika dla lokalizacji udostępnionego dysku przy użyciu wystarczające uprawnienia dostępu, aby odczytywać i zapisywać pliki. Na przykład `Contoso\backupshareuser`. |
| $key            | Typ **klucza szyfrowania** używany do szyfrowania każdej kopii zapasowej. |
| $password       | Typ **hasło** dla użytkownika. |
| $sharepath      | Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Należy użyć ciągu Universal Naming Convention (UNC) dla ścieżki do udziału plików hostowane na oddzielnym urządzeniu. Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. Aby zapewnić dostępność danych kopii zapasowej, urządzenie powinno być w innej lokalizacji. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Potwierdź ustawienia kopii zapasowej

W tej samej sesji programu PowerShell uruchom następujące polecenia:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

Wynik powinien wyglądać następujące dane wyjściowe:

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>Kolejne kroki

 - Dowiedz się, jak uruchomić tworzenia kopii zapasowych, zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Dowiedz się zweryfikować kopii zapasowej, zobacz [Potwierdź kopia zapasowa została wykonana w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md ).