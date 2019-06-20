---
title: Rozwiązywanie problemów z platformy Azure dysku Data Box z dysku odblokowywanie problemów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemy z usługą Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148285"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów odblokowywanie dysk z dysku Azure Data Box

Ten artykuł ma zastosowanie do dysku systemu Microsoft Azure Data Box i opisano przepływy pracy, używane do Rozwiązywanie problemów podczas korzystania z narzędzia odblokowania. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Błędy narzędzia do odblokowywania dysków Data Box Disk


| Komunikat o błędzie/działanie narzędzia      | Zalecenia                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| The current .NET Framework is not supported. The supported versions are 4.5 and later.<br><br>Narzędzie kończy działanie, wyświetlając komunikat.  | Program .NET 4.5 nie jest zainstalowany. Zainstaluj program .NET 4.5 lub nowszą wersję na komputerze hosta, na którym uruchomiono narzędzie do odblokowywania dysków Data Box.                                                                            |
| Could not unlock or verify any volumes. Contact Microsoft Support.  <br><br>Za pomocą narzędzia nie można odblokować lub sprawdzić żadnego zablokowanego dysku. | Narzędzie nie mogło odblokować żadnego z zablokowanych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach.                                                |
| Following volumes are unlocked and verified. <br>Litery dysku woluminu: E:<br>Could not unlock any volumes with the following passkeys: werwerqomnf, qwerwerqwdfda <br><br>Narzędzie odblokowało niektóre dyski i wyświetla listę liter dysków, które udało się odblokować, i tych, których nie udało się odblokować.| Częściowe powodzenie. Nie udało się odblokować niektórych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Could not find locked volumes. Verify disk received from Microsoft is connected properly and is in locked state.          | Narzędzie nie odnajduje żadnych zablokowanych dysków. Dyski są już odblokowane lub nie zostały wykryte. Upewnij się, że dyski są podłączone i zablokowane.                                                           |
| Błąd krytyczny: Nieprawidłowy parametr<br>Parameter name: invalid_arg<br>USAGE:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Przykład: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Przykład: DataBoxDiskUnlock /SystemCheck<br>Przykład: DataBoxDiskUnlock /Help<br><br>/ Klucze dostępu:       Pobierz ten klucz dostępu z usługi Azure DataBox Disk zamówienia. The passkey unlocks your disks.<br>/ Help:           Ta opcja zapewnia pomoc na użycie polecenia cmdlet i przykładów.<br>/SystemCheck:    Ta opcja sprawdza, czy system spełnia wymagania, aby uruchomić narzędzie.<br><br>Press any key to exit. | Wprowadzono nieprawidłowy parametr. Tylko parametry dozwolone są /SystemCheck /PassKey i/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Odblokuj problemy dotyczące dysków, korzystając z klienta Windows

W tej sekcji przedstawiono niektóre najważniejsze problemy sterowaną podczas wdrażania dysku Data Box przy użyciu klienta Windows do kopiowania danych.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problem: Nie można odblokować dysk z funkcją BitLocker
 
**Przyczyna** 

Wykorzystano hasło w oknie dialogowym funkcji BitLocker i próby odblokowania dysku za pomocą funkcji BitLocker odblokowania stacji okna dialogowego. Nie będzie działać.

**Rozdzielczość**

Aby odblokować dyski Data Box, należy za pomocą narzędzia Data Box dysku odblokowania i podaj hasło w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Samouczek: Rozpakowywanie, łączenie i odblokować dysku Azure Data Box](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problem: Nie można odblokować lub sprawdzić niektóre woluminy. Contact Microsoft Support.
 
**Przyczyna**

Może zostać wyświetlony następujący błąd w dzienniku błędów, a nie są w stanie odblokować lub sprawdzić niektóre woluminy.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Oznacza to, że prawdopodobnie brakuje odpowiednią wersję programu Windows PowerShell na komputerze klienckim Windows.

**Rozdzielczość**

Możesz zainstalować [v programu Windows PowerShell 5.0](https://www.microsoft.com/download/details.aspx?id=54616) i spróbuj ponownie wykonać operację.
 
Jeśli nadal nie można odblokować woluminy, skopiuj dzienniki z folderu, który zawiera dane pole dysku odblokować narzędzie i [skontaktuj się z Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [rozwiązać problemy ze sprawdzaniem poprawności](data-box-disk-troubleshoot.md).
