---
title: Problemy z odblokowaniem dysku na dysku w usłudze Azure Data Box Disk | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemy z usługą Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148285"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów z odblokowywaniem dysku na dysku usługi Azure Data Box

Ten artykuł dotyczy dysku microsoft azure data box i opisuje przepływy pracy używane do rozwiązywania problemów podczas korzystania z narzędzia odblokowywania. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Błędy narzędzia do odblokowywania dysków Data Box Disk


| Komunikat o błędzie/działanie narzędzia      | Zalecenia                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| The current .NET Framework is not supported. The supported versions are 4.5 and later.<br><br>Narzędzie kończy działanie, wyświetlając komunikat.  | Program .NET 4.5 nie jest zainstalowany. Zainstaluj program .NET 4.5 lub nowszą wersję na komputerze hosta, na którym uruchomiono narzędzie do odblokowywania dysków Data Box.                                                                            |
| Could not unlock or verify any volumes. Contact Microsoft Support.  <br><br>Za pomocą narzędzia nie można odblokować lub sprawdzić żadnego zablokowanego dysku. | Narzędzie nie mogło odblokować żadnego z zablokowanych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach.                                                |
| Following volumes are unlocked and verified. <br>Volume drive letters: E:<br>Could not unlock any volumes with the following passkeys: werwerqomnf, qwerwerqwdfda <br><br>Narzędzie odblokowało niektóre dyski i wyświetla listę liter dysków, które udało się odblokować, i tych, których nie udało się odblokować.| Częściowe powodzenie. Nie udało się odblokować niektórych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Could not find locked volumes. Verify disk received from Microsoft is connected properly and is in locked state.          | Narzędzie nie odnajduje żadnych zablokowanych dysków. Dyski są już odblokowane lub nie zostały wykryte. Upewnij się, że dyski są podłączone i zablokowane.                                                           |
| Fatal error: Invalid parameter<br>Parameter name: invalid_arg<br>USAGE:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Example: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Example: DataBoxDiskUnlock /SystemCheck<br>Example: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.<br>/Help:           This option provides help on cmdlet usage and examples.<br>/SystemCheck:    This option checks if your system meets the requirements to run the tool.<br><br>Press any key to exit. | Wprowadzono nieprawidłowy parametr. Jedynymi dozwolonymi parametrami są /SystemCheck, /PassKey i /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Odblokowywanie problemów z dyskami podczas korzystania z klienta systemu Windows

W tej sekcji opisano niektóre z najważniejszych problemów napotykanych podczas wdrażania dysku data box podczas korzystania z klienta systemu Windows do kopiowania danych.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problem: Nie można odblokować dysku z funkcji BitLocker
 
**Spowodować** 

Użyto hasła w oknie dialogowym Funkcji BitLocker i próbie odblokowania dysku za pomocą okna dialogowego odblokowywanie dysków funkcji BitLocker. To by nie zadziałało.

**Rozdzielczość**

Aby odblokować dyski pól danych, należy użyć narzędzia Odblokowywanie dysków pola danych i podać hasło z witryny Azure portal. Aby uzyskać więcej informacji, przejdź do [samouczka: Rozpakuj, połącz się i odblokuj dysk azure data box](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problem: nie można odblokować ani zweryfikować niektórych woluminów. Contact Microsoft Support.
 
**Spowodować**

W dzienniku błędów może zostać wyświetlony następujący błąd i nie można odblokować ani zweryfikować niektórych woluminów.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Oznacza to, że prawdopodobnie brakuje odpowiedniej wersji programu Windows PowerShell na kliencie systemu Windows.

**Rozdzielczość**

Można zainstalować [program Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) i ponowić próbę wykonania operacji.
 
Jeśli nadal nie możesz odblokować woluminów, skopiuj dzienniki z folderu, w którego znajduje się narzędzie Odblokowywanie dysków pola danych, i [skontaktuj się z pomocą techniczną firmy Microsoft](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z sprawdzaniem poprawności](data-box-disk-troubleshoot.md).
