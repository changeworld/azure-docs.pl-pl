---
title: Problem z kopiowaniem danych na dysku usługi Azure Data Box Disk| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemów widocznych podczas kopiowania danych na dysku usługi Azure Data Box przy użyciu dzienników.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148350"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów z kopiowaniem danych na dysku usługi Azure Data Box

Ten artykuł dotyczy dysku microsoft azure data box i opisano, jak rozwiązać wszelkie problemy widoczne podczas kopiowania danych na dyski. W artykule opisano również problemy podczas korzystania z narzędzia do kopiowania dzielonego.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemy z kopiowaniem danych podczas korzystania z systemu Linux

W tej sekcji opisano niektóre z najważniejszych problemów napotykanych podczas kopiowania danych na dyski przy użyciu klienta systemu Linux.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problem: Napęd jest montowany jako tylko do odczytu
 
**Spowodować** 

Może to być spowodowane nieczystym systemem plików.

Ponowne ponowne zamontowanie dysku jako odczytu i zapisu nie działa z dyskami pola danych. Ten scenariusz nie jest obsługiwany z dysków odszyfrowanych przez dislocker. Być może urządzenie zostało pomyślnie ponownie zamontowane za pomocą następującego polecenia:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Mimo że ponowne zamontowanie zakończyło się pomyślnie, dane nie będą się powtarzać.

**Rozdzielczość**

Aby wykonać następujące czynności w systemie Linux:

1. Zainstaluj `ntfsprogs` pakiet dla narzędzia ntfsfix.
2. Odinstaluj punkty mocowania przewidziane dla napędu za pomocą narzędzia do odblokowywania. Liczba punktów instalacji będzie się różnić w przypadku dysków.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Uruchom `ntfsfix` na odpowiedniej ścieżce. Podświetlona liczba powinna być taka sama jak krok 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Uruchom następujące polecenie, aby usunąć metadane hibernacji, które mogą powodować problem z instalacją.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Czy czyste odmontować.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Wykonaj czyste odblokowanie i zamontować.
7. Przetestuj punkt instalacji, zapisując plik.
8. Odinstaluj i ponownie zamontuj, aby sprawdzić poprawność trwałości pliku.
9. Kontynuuj kopiowanie danych.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problem: Błąd z danymi nie utrwalającymi się po skopiowaniu
 
**Spowodować** 

Jeśli widzisz, że dysk nie ma danych po odinstalowywany (choć dane zostały skopiowane do niego), to jest możliwe, że ponownie zamontowany dysk jako odczyt i zapis po dysku został zainstalowany jako tylko do odczytu.

**Rozdzielczość**
 
W takim przypadku należy zapoznać się z rozdzielczością [dysków montowanych jako tylko do odczytu.](#issue-drive-getting-mounted-as-read-only)

Jeśli tak nie było, skopiuj dzienniki z folderu, w który znajduje się narzędzie Odblokowywanie dysków pola danych, i [skontaktuj się z pomocą techniczną firmy Microsoft](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Błędy narzędzia do dzielenia skopiowanych dysków Data Box Disk

Problemy widoczne podczas używania narzędzia do dzielenia kopii w celu podzielenia danych na wiele dysków są podsumowane w poniższej tabeli.

|Komunikat o błędzie/ostrzeżenia |Zalecenia |
|---------|---------|
|[Informacje] Pobieranie hasła funkcji BitLocker dla woluminu: m <br>[Błąd] Wyjątek przechwycony podczas pobierania klucza funkcji BitLocker dla woluminu m:<br> Sekwencja nie zawiera elementów.|Ten błąd jest zwracany, jeśli docelowe dyski Data Box Disk są w trybie offline. <br> W przypadku dysków w trybie online należy użyć narzędzia `diskmgmt.msc`.|
|[Błąd] Zgłoszono wyjątek: operacja usługi WMI zakończyła się niepowodzeniem:<br> Method = UnlockWithNumericalPassword, ReturnValue = 2150694965, <br>Win32Message = format podanego hasła odzyskiwania jest nieprawidłowy. <br>Hasła odzyskiwania funkcji BitLocker są 48-cyfrowe. <br>Sprawdź, czy hasło odzyskiwania ma poprawny format, a następnie spróbuj ponownie.|Użyj narzędzia do odblokowywania dysków Data Box Disk, aby odblokować dyski i ponowić próbę wykonania polecenia. Aby uzyskać więcej informacji, przejdź na stronę <li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Błąd] Zgłoszono wyjątek: plik DriveManifest.xml znajduje się na dysku docelowym. <br> Oznacza to, że dysk docelowy został prawdopodobnie przygotowany przy użyciu innego pliku dziennika. <br>Aby dodać więcej danych do tego samego dysku, użyj poprzedniego pliku dziennika. Aby usunąć istniejące dane i ponownie użyć dysku docelowego dla nowego zadania importu, usuń *plik DriveManifest.xml* na dysku. Ponownie uruchom to polecenie, używając nowego pliku dziennika.| Ten błąd zostanie wyświetlony podczas próby użycia tego samego zestawu dysków w wielu sesjach importowania. <br> Z jedną sesją dzielenia i kopiowania można użyć tylko jednego zestawu dysków.|
|[Błąd] Zgłoszono wyjątek: CopySessionId importdata-sept-test-1 odwołuje się do poprzedniej sesji kopiowania i nie może zostać użyty ponownie w nowej sesji kopiowania.|Ten błąd jest zgłaszany w przypadku próby użycia tej samej nazwy zadania dla nowego zadania, które zostało poprzednio pomyślnie ukończone.<br> Należy przypisać unikatową nazwę dla nowego zadania.|
|[Informacje] Nazwa pliku lub katalogu docelowego przekracza limit długości dla systemu plików NTFS. |Ten komunikat jest zgłaszany, gdy nazwa pliku docelowego została zmieniona z powodu długiej ścieżki pliku.<br> Aby sterować tym zachowaniem, należy zmodyfikować opcję dyspozycji w pliku `config.json`.|
|[Błąd] Zgłoszono wyjątek: nieprawidłowa sekwencja ucieczki pliku JSON. |Ten komunikat jest zgłaszany, gdy format pliku config.json jest nieprawidłowy. <br> Przed zapisaniem pliku `config.json` zweryfikuj go przy użyciu narzędzia [JSONlint](https://jsonlint.com/).|


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z narzędziami sprawdzania poprawności](data-box-disk-troubleshoot.md).
