---
title: Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Włącz usługę Backup infrastruktury za pomocą programu Windows PowerShell usługi Azure Stack mogą zostać przywrócone, jeśli wystąpi awaria.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055421"
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
| $password       | Typ **hasło** dla użytkownika. |
| $sharepath      | Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Należy użyć ciągu Universal Naming Convention (UNC) dla ścieżki do udziału plików hostowane na oddzielnym urządzeniu. Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. Aby zapewnić dostępność danych kopii zapasowej, urządzenie powinno być w innej lokalizacji. |
| $frequencyInHours | Określa częstotliwość w godzinach, jak często kopie zapasowe są tworzone. Wartością domyślną jest 12. Usługa Scheduler obsługuje maksymalnie 12 i co najmniej 4.|
| $retentionPeriodInDays | Czas przechowywania w dniach Określa, ile dni kopii zapasowych zostaną zachowane w lokalizacji zewnętrznej. Wartość domyślna to 7. Usługa Scheduler obsługuje maksymalnie 14 i co najmniej 2. Starszy niż okres przechowywania kopii zapasowych Pobierz automatycznie usuwane z lokalizacji zewnętrznej.|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Potwierdź ustawienia kopii zapasowej

W tej samej sesji programu PowerShell uruchom następujące polecenia:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

Wynik powinien wyglądać następujące przykładowe dane wyjściowe:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Aktualizowanie ustawień kopii zapasowej
W tej samej sesji programu PowerShell można zaktualizować wartości domyślne dla okresu przechowywania i częstotliwość tworzenia kopii zapasowych. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Wynik powinien wyglądać następujące przykładowe dane wyjściowe:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>Kolejne kroki

 - Dowiedz się, jak uruchomić tworzenia kopii zapasowych, zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Dowiedz się zweryfikować kopii zapasowej, zobacz [Potwierdź kopia zapasowa została wykonana w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md ).
