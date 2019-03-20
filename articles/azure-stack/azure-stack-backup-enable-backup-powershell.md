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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 773e600577b35019b8a3619c7eec3e93b77a4382
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085800"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Włącz usługę Backup infrastruktury za pomocą programu Windows PowerShell więc warto poświęcić okresowe kopie zapasowe:
 - Wewnętrzny tożsamości usługi i certyfikatu głównego
 - Plany, oferty i subskrypcje
 - Wystąpienia obliczeniowe, Magazyn i limitów przydziałów sieci użytkownika
 - Wpisy tajne z magazynu klucz użytkownika
 - Role RBAC użytkownika i zasady
 - Konta magazynu użytkownika

Można uzyskać dostęp do poleceń cmdlet programu PowerShell do włączenia kopii zapasowej, rozpocząć tworzenie kopii zapasowej i pobieranie informacji o kopii zapasowej za pośrednictwem punktu końcowego zarządzania operatora.

## <a name="prepare-powershell-environment"></a>Przygotuj środowisko programu PowerShell

Aby uzyskać instrukcje dotyczące konfigurowania środowiska PowerShell, zobacz [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md). Aby zalogować się do usługi Azure Stack, zobacz [skonfigurować środowisko operatora i zaloguj się do usługi Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Podaj klucz udziału, poświadczenia i szyfrowania kopii zapasowych włączyć tworzenie kopii zapasowych

W tej samej sesji programu PowerShell należy edytować poniższy skrypt programu PowerShell, dodając zmienne dla danego środowiska. Uruchom skrypt zaktualizowane zapewnienie infrastruktury usługi kopii zapasowej klucza kopii zapasowej, udział, poświadczenia i szyfrowania.

| Zmienna        | Opis   |
|---              |---                                        |
| $username       | Typ **Username** użycie domena i nazwa użytkownika dla lokalizacji udostępnionego dysku przy użyciu wystarczające uprawnienia dostępu, aby odczytywać i zapisywać pliki. Na przykład `Contoso\backupshareuser`. |
| $password       | Typ **hasło** dla użytkownika. |
| $sharepath      | Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Należy użyć ciągu Universal Naming Convention (UNC) dla ścieżki do udziału plików hostowane na oddzielnym urządzeniu. Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. Aby zapewnić dostępność danych kopii zapasowej, urządzenie powinno być w innej lokalizacji. |
| $frequencyInHours | Określa częstotliwość w godzinach, jak często kopie zapasowe są tworzone. Wartością domyślną jest 12. Usługa Scheduler obsługuje maksymalnie 12 i co najmniej 4.|
| $retentionPeriodInDays | Czas przechowywania w dniach Określa, ile dni kopii zapasowych zostaną zachowane w lokalizacji zewnętrznej. Wartość domyślna to 7. Usługa Scheduler obsługuje maksymalnie 14 i co najmniej 2. Starszy niż okres przechowywania kopii zapasowych Pobierz automatycznie usuwane z lokalizacji zewnętrznej.|
| $encryptioncertpath | Ma zastosowanie do 1901 i nie tylko.  Parametr jest dostępny moduł usługi Azure Stack w wersji 1.7 i późniejsze. Ścieżka certyfikatu szyfrowania określa ścieżkę pliku. Plik CER przy użyciu klucza publicznego używany do szyfrowania danych. |
| $encryptionkey | Stosowane w przypadku tworzenia 1811 lub starszym. Parametr jest dostępny w moduł usługi Azure Stack w wersji 1.6 lub starszej. Klucz szyfrowania używany do szyfrowania danych. Użyj [New AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) polecenia cmdlet, aby wygenerować nowy klucz. |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>Włącz wykonywanie kopii zapasowej na 1901 i nowszych przy użyciu certyfikatu
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Włącz wykonywanie kopii zapasowej na 1811 lub wcześniej przy użyciu certyfikatu
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Potwierdź ustawienia kopii zapasowej

W tej samej sesji programu PowerShell uruchom następujące polecenia:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
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

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Wynik powinien wyglądać następujące przykładowe dane wyjściowe:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
Polecenia cmdlet programu PowerShell w celu skonfigurowania kopii zapasowej infrastruktury jest AzsBackupConfiguration zestawu. W poprzednich wersjach polecenia cmdlet została AzsBackupShare zestawu. To polecenie cmdlet wymaga zapewnienia certyfikatu. Jeśli tworzenie kopii zapasowych jest skonfigurowany przy użyciu klucza szyfrowania, nie można zaktualizować klucza szyfrowania lub wyświetlić właściwości. Należy użyć programu PowerShell w wersji 1.6. 

Jeśli tworzenie kopii zapasowych została skonfigurowana przed zaktualizowaniem do 1901, można użyć programu PowerShell w wersji 1.6, i wyświetlić klucz szyfrowania. W wersji 1.6 uniemożliwi Aktualizuj na podstawie klucza szyfrowania do pliku certyfikatu.
Zapoznaj się [Instalowanie programu Azure Stack PowerShell](azure-stack-powershell-install.md) Aby uzyskać więcej informacji na temat instalowania odpowiedniej wersji modułu. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak uruchomić tworzenia kopii zapasowych, zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Dowiedz się zweryfikować kopii zapasowej, zobacz [Potwierdź kopia zapasowa została wykonana w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md)
