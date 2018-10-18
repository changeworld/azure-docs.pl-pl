---
title: Rozwiązywanie problemów z usługą Azure Data Box Disk | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania problemy z usługą Azure Data Box Disk.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: 9b2c03c13cf687af7cdebc9c4d2624a6a7c5907f
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069203"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk-preview"></a>Rozwiązywanie problemów z usługą Azure Data Box Disk (wersja zapoznawcza)

Ten artykuł dotyczy usługi Microsoft Azure Data Box w wersji zapoznawczej. W artykule opisano niektóre złożone przepływy pracy i zadania związane z zarządzaniem, które można wykonywać w usługach Data Box i Data Box Disk. 

Można zarządzać usługą Data Box Disk w witrynie Azure Portal. Ten artykuł dotyczy zadań, które można wykonywać w witrynie Azure Portal. W witrynie Azure Portal można zarządzać zamówieniami, zarządzać urządzeniami oraz śledzić stan zamówienia na kolejnych etapach aż do ukończenia.

W tym artykule zawarto następujące samouczki:

- Pobieranie dzienników diagnostycznych
- Wykonywanie zapytań dotyczących dzienników aktywności


> [!IMPORTANT]
> Rozwiązanie Data Box jest dostępne w wersji zapoznawczej. Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="download-diagnostic-logs"></a>Pobieranie dzienników diagnostycznych

Jeśli podczas kopiowania danych wystąpi błąd, w portalu zostanie wyświetlona ścieżka do folderu zawierającego dzienniki diagnostyczne. 

Dostępne są następujące rodzaje dzienników diagnostycznych:
- Dzienniki błędów
- Dzienniki szczegółowe  

Aby przejść do ścieżki dziennika kopiowania, przejdź do konta magazynu skojarzonego z zamówieniem urządzenia Data Box. 

1.  Przejdź do pozycji **Ogólne > Szczegóły zamówienia** i zapisz konto magazynu skojarzone z zamówieniem.
 

2.  W obszarze **Wszystkie zasoby** wyszukaj konto magazynu określone w poprzednim kroku. Wybierz i kliknij konto magazynu.

    ![Dzienniki kopiowania 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Przejdź do pozycji **Blob Service > Przeglądaj obiekty blob** i wyszukaj obiekt blob odpowiadający kontu magazynu. Przejdź do pozycji **diagnosticslogcontainer > waies**. 

    ![Dzienniki kopiowania 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Powinny zostać wyświetlone zarówno dzienniki błędów, jak i dzienniki szczegółowe dotyczące kopiowania danych. Wybierz i kliknij każdy z plików, a następnie pobierz kopię lokalną.

## <a name="query-activity-logs"></a>Wykonywanie zapytań dotyczących dzienników aktywności

Możesz użyć dzienników aktywności do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji. Za pomocą dzienników aktywności można określić:

- Jakie operacje zostały wykonane na zasobach w subskrypcji.
- Użytkownik, który zainicjował operację. 
- Kiedy została wykonana operacja.
- Stan operacji.
- Wartości innych właściwości, które mogą ułatwić zbadanie operacji.

Dziennik aktywności zawiera informacje o wszystkich operacjach zapisu (takich jak PUT, POST, DELETE) wykonywanych względem zasobów, ale nie zawiera informacji dotyczących operacji odczytu (na przykład GET). 

Dzienniki aktywności są przechowywane przez 90 dni. Możesz wykonać zapytanie dotyczące dowolnego zakresu dat, jednak data początkowa nie może być wcześniejsza od daty bieżącej o więcej niż 90 dni. Możesz również filtrować dane, używając jednej z wbudowanych opcji zapytań w sekcji Szczegółowe informacje. Możesz na przykład kliknąć błąd, a następnie wybrać i kliknąć konkretne rodzaje błędów, aby określić główną przyczynę.

## <a name="data-box-disk-unlock-tool-errors"></a>Błędy narzędzia do odblokowywania dysków Data Box Disk


| Komunikat o błędzie/działanie narzędzia      | Zalecenia                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Brak<br><br>Narzędzie do odblokowywania dysków Data Box ulega awarii.                                                                            | Funkcja BitLocker nie jest zainstalowana. Upewnij się, że na komputerze hosta, na którym uruchomiono narzędzie do odblokowywania dysków Data Box, zainstalowano funkcję BitLocker.                                                                            |
| The current .NET Framework is not supported. The supported versions are 4.5 and later.<br><br>Narzędzie kończy działanie, wyświetlając komunikat.  | Program .NET 4.5 nie jest zainstalowany. Zainstaluj program .NET 4.5 lub nowszą wersję na komputerze hosta, na którym uruchomiono narzędzie do odblokowywania dysków Data Box.                                                                            |
| Could not unlock or verify any volumes. Contact Microsoft Support.  <br><br>Za pomocą narzędzia nie można odblokować lub sprawdzić żadnego zablokowanego dysku. | Narzędzie nie mogło odblokować żadnego z zablokowanych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach.                                                |
| Following volumes are unlocked and verified. <br>Volume drive letters: E:<br>Could not unlock any volumes with the following passkeys: werwerqomnf, qwerwerqwdfda <br><br>Narzędzie odblokowało niektóre dyski i wyświetla listę liter dysków, które udało się odblokować, i tych, których nie udało się odblokować.| Częściowe powodzenie. Nie udało się odblokować niektórych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Could not find locked volumes. Verify disk received from Microsoft is connected properly and is in locked state.          | Narzędzie nie odnajduje żadnych zablokowanych dysków. Dyski są już odblokowane lub nie zostały wykryte. Upewnij się, że dyski są podłączone i zablokowane.                                                           |
| Fatal error: Invalid parameter<br>Parameter name: invalid_arg<br>USAGE:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Example: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Example: DataBoxDiskUnlock /SystemCheck<br>Example: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.<br>/Help:           This option provides help on cmdlet usage and examples.<br>/SystemCheck:    This option checks if your system meets the requirements to run the tool.<br><br>Press any key to exit. | Wprowadzono nieprawidłowy parametr. Dozwolone parametry to: /SystemCheck, /PassKey oraz /Help.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>Błędy narzędzia do dzielenia skopiowanych dysków Data Box Disk

|Komunikat o błędzie/ostrzeżenia  |Zalecenia |
|---------|---------|
|[Informacje] Pobieranie hasła funkcji BitLocker dla woluminu: m <br>[Błąd] Przechwycono wyjątek podczas pobierania klucza funkcji BitLocker dla woluminu m:<br> Sekwencja nie zawiera elementów.|Ten błąd jest zwracany, jeśli docelowe dyski Data Box Disk są w trybie offline. <br> W przypadku dysków w trybie online należy użyć narzędzia `diskmgmt.msc`.|
|[Błąd] Zgłoszono wyjątek: operacja usługi WMI zakończyła się niepowodzeniem:<br> Method = UnlockWithNumericalPassword, ReturnValue = 2150694965, <br>Win32Message = format podanego hasła odzyskiwania jest nieprawidłowy. <br>Hasła odzyskiwania funkcji BitLocker są 48-cyfrowe. <br>Sprawdź, czy hasło odzyskiwania ma poprawny format, a następnie spróbuj ponownie.|Użyj narzędzia do odblokowywania dysków Data Box Disk, aby odblokować dyski i ponowić próbę wykonania polecenia. Aby uzyskać więcej informacji, przejdź na stronę <li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Błąd] Zgłoszono wyjątek: plik DriveManifest.xml znajduje się na dysku docelowym. <br> Oznacza to, że dysk docelowy został prawdopodobnie przygotowany przy użyciu innego pliku dziennika. <br>Aby dodać więcej danych do tego samego dysku, użyj poprzedniego pliku dziennika. Aby usunąć istniejące dane i ponownie użyć dysku docelowego dla nowego zadania importu, usuń plik DriveManifest.xml z dysku. Ponownie uruchom to polecenie, używając nowego pliku dziennika.| Ten błąd zostanie wyświetlony podczas próby użycia tego samego zestawu dysków w wielu sesjach importowania. <br> Z jedną sesją dzielenia i kopiowania można użyć tylko jednego zestawu dysków.|
|[Błąd] Zgłoszono wyjątek: CopySessionId importdata-sept-test-1 odwołuje się do poprzedniej sesji kopiowania i nie może zostać użyty ponownie w nowej sesji kopiowania.|Ten błąd jest zgłaszany w przypadku próby użycia tej samej nazwy zadania dla nowego zadania, które zostało poprzednio pomyślnie ukończone.<br> Należy przypisać unikatową nazwę dla nowego zadania.|
|[Informacje] Nazwa pliku lub katalogu docelowego przekracza limit długości dla systemu plików NTFS. |Ten komunikat jest zgłaszany, gdy nazwa pliku docelowego została zmieniona z powodu długiej ścieżki pliku.<br> Aby sterować tym zachowaniem, należy zmodyfikować opcję dyspozycji w pliku `config.json`.|
|[Błąd] Zgłoszono wyjątek: nieprawidłowa sekwencja ucieczki pliku JSON. |Ten komunikat jest zgłaszany, gdy format pliku config.json jest nieprawidłowy. <br> Przed zapisaniem pliku `config.json` zweryfikuj go przy użyciu narzędzia [JSONlint](https://jsonlint.com/).|



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób [zarządzać usługą Data Box Disk w witrynie Azure Portal](data-box-portal-ui-admin.md).
