---
title: Weryfikowanie kopii zapasowej usługi Azure Stack przy użyciu ASDK | Dokumentacja firmy Microsoft
description: Jak zweryfikować backup systemów integerated dla usługi Azure Stack przy użyciu ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 181f37fb72584e18cc963ba1ffde070379a1b0c6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961466"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Weryfikacja kopii zapasowej usługi Azure Stack za pomocą ASDK
Po wdrażanie usługi Azure Stack i Inicjowanie obsługi administracyjnej zasobów użytkownika, takich jak oferty, plany, limity przydziału i subskrypcji, wykonaj następujące czynności [włączenia kopii zapasowej infrastruktury usługi Azure Stack](../azure-stack-backup-enable-backup-console.md). Planowanie i uruchamianie infrastruktury regularnego tworzenia kopii zapasowych zapewni, że danych związanych z zarządzaniem infrastrukturą nie zostaną utracone w przypadku poważnej sprzętu lub awaria usługi.

> [!TIP]
> Zaleca się, że możesz [zapisywać kopie zapasowe na żądanie](../azure-stack-backup-back-up-azure-stack.md) przed rozpoczęciem tej procedury, aby zapewnić kopię infrastrcuture udostępnianie najnowszych danych. Upewnij się przechwycić identyfikator kopii zapasowej, po pomyślnym wykonaniu kopii zapasowej. Ten identyfikator będzie wymagane podczas odzyskiwania w chmurze. 

Tworzenie kopii zapasowych usługi Azure Stack infrastruktury zawierają ważne dane dotyczące chmury można przywrócić podczas ponownego wdrażania usługi Azure Stack. ASDK służy do sprawdzania poprawności te kopie zapasowe bez wywierania wpływu na produkcyjne chmury. 

Weryfikowanie kopii zapasowych na ASDK jest obsługiwane w następujących scenariuszach:

|Scenariusz|Przeznaczenie|
|-----|-----|
|Sprawdzanie poprawności infrastruktury kopii zapasowych w ramach rozwiązania zintegrowanego.|Krótki czas życia weryfikacji, czy dane w kopii zapasowej są prawidłowe.|
|Dowiedz się, przepływu pracy odzyskiwania end-to-end.|Użyj ASDK do sprawdzania poprawności całej kopii zapasowej i przywracania środowiska.|
|     |     |

Poniższy scenariusz **nie** obsługiwana podczas tworzenia kopii zapasowej na ASDK weryfikacji:

|Scenariusz|Przeznaczenie|
|-----|-----|
|Kompilacja ASDK do tworzenia kopii zapasowej i przywracania.|Przywracanie danych kopii zapasowej z poprzedniej wersji ASDK do nowszej wersji.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Wdrażanie odzyskiwania w chmurze
Wykonując wdrożenie w chmurze odzyskiwania ASDK można zweryfikować infrastrukturę tworzenia kopii zapasowych z wdrożenia systemów zintegrowanych. W tym typie wdrożenia określonej usługi danych został przywrócony z kopii zapasowej po ASDK została zainstalowana na komputerze-hoście.



### <a name="cloud-recovery-prerequisites"></a>Wymagania wstępne dotyczące chmury odzyskiwania
Przed rozpoczęciem wdrożenie w chmurze odzyskiwania ASDK, upewnij się, że masz następujące informacje:

|Wymagania wstępne|Opis|
|-----|-----|
|Ścieżka udziału kopii zapasowej.|Ścieżka udziału plików UNC najnowszej kopii zapasowej usługi Azure Stack, która będzie służyć do odzyskiwania informacji o infrastrukturze Azure Stack. Tego udziału lokalnego zostaną utworzone w procesie wdrażania odzyskiwania chmury.|
|Kopię zapasową klucza szyfrowania.|Klucz szyfrowania, który został użyty do zaplanować tworzenie kopii zapasowych do uruchamiania przy użyciu portalu administracyjnego usługi Azure Stack.|
|Identyfikator kopii zapasowej do przywrócenia.|Identyfikator kopii zapasowych w formie alfanumeryczne "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", który identyfikuje kopii zapasowej można przywrócić podczas odzyskiwania w chmurze.|
|Adres IP serwera czasu.|Adres IP do serwera z prawidłową godzinę, takich jak 132.163.97.2, jest wymagane do wdrożenia usługi Azure Stack.|
|Hasło certyfikatu zewnętrznego.|Hasło dla certyfikatu zewnętrznego, używane przez usługę Azure Stack. Kopii zapasowej urzędu certyfikacji zawiera certyfikaty zewnętrzne, które mają być przywracane przy użyciu tego hasła.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Przygotuj komputer-host 
Tak jak w przypadku wdrożenia w normalnym ASDK ASDK środowiska systemu hosta muszą być przygotowane do instalacji. Gdy na komputerze deweloperskim zestaw hostów zostały przygotowane, uruchomi się z dysku twardego maszyny wirtualnej CloudBuilder.vhdx, aby rozpocząć wdrażanie ASDK.

Na komputerze-hoście ASDK Pobierz nowe cloudbuilder.vhdx, odpowiadający tej samej wersji usługi Azure Stack, która została utworzona kopia zapasowa i postępuj zgodnie z instrukcjami dotyczącymi [Przygotowywanie komputera hosta ASDK](asdk-prepare-host.md).

Po ponownym uruchomieniu serwera hosta z cloudbuilder.vhdx należy utworzyć udział plików i skopiować dane kopii zapasowej do. Udział plików powinny być dostępne dla konta instalujący; Administrator w przykładzie polecenia programu PowerShell: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Następnie skopiuj najnowsze pliki kopii zapasowej usługi Azure Stack do nowo utworzonego udziału. Powinien być strukturę folderów w ramach udziału: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Wdrażanie ASDK w trybie odzyskiwania w chmurze
**InstallAzureStackPOC.ps1** skryptu jest używany do inicjowania odzyskiwania w chmurze. 

> [!IMPORTANT]
> Instalacja ASDK obsługuje dokładnie jeden karta interfejsu sieciowego (NIC) dla sieci. Jeśli masz wiele kart sieciowych, upewnij się, że włączone jest tylko jeden (i wszystkie inne są wyłączone) przed uruchomieniem skryptu wdrażania.

Zmodyfikuj następujące polecenia programu PowerShell dla danego środowiska, a następnie uruchom je wdrożyć ASDK w trybie odzyskiwania w chmurze:

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Przywracanie infrastruktury danych z kopii zapasowej
Po wdrożeniu pomyślne chmury odzyskiwania, należy wykonać przy użyciu przywracania **AzureStack przywracania** polecenia cmdlet. 

Po zalogowaniu się jako operatora infrastruktury Azure Stack [instalacji programu Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) i następnie, podstawiając identyfikator kopii zapasowej dla `Name` parametru, uruchom następujące polecenie:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Oczekiwania 60 minut po wywoływania tego polecenia cmdlet można uruchomić weryfikacji kopii zapasowej danych w chmurze odzyskaniu ASDK.

## <a name="next-steps"></a>Kolejne kroki
[Rejestrowanie usługi Azure Stack](asdk-register.md)

