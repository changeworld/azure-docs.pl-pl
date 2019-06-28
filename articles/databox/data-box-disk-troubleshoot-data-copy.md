---
title: Dane dotyczące rozwiązywania problemów z dysku Data Box Azure skopiuj problemów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemów występujących podczas kopiowania danych w przy użyciu dzienników dysku Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148350"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów kopiowania danych z dysku Azure Data Box

Ten artykuł ma zastosowanie do dysku systemu Microsoft Azure Data Box i zawiera opis sposobu rozwiązywania wszelkich problemów, które zobaczysz podczas kopiowania danych na dyskach. Artykuł obejmuje również problemy podczas korzystania z narzędzia do kopiowania podziału.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemy dotyczące kopiowania danych, korzystając z systemu Linux

W tej sekcji przedstawiono niektóre najważniejsze problemy sterowaną za pomocą klienta systemu Linux, aby skopiować dane na dyskach.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problem: Dysk wprowadzenie zainstalowany jako tylko do odczytu
 
**Przyczyna** 

Może to być spowodowane system nieczyste plików.

Woluminom dysk w trybie odczytu i zapisu nie działa z dyskami pola danych. Ten scenariusz nie jest obsługiwany z stacje odszyfrować dislocker. Użytkownik może mieć pomyślnie ponownej instalacji urządzenia przy użyciu następującego polecenia:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Chociaż woluminom zakończyło się pomyślnie, dane nie będą zachowywane.

**Rozdzielczość**

Wykonaj następujące kroki w systemie Linux:

1. Zainstaluj `ntfsprogs` pakietów dla narzędzia ntfsfix.
2. Odinstaluj udostępniane na potrzeby dysku narzędzia unlock punktów instalacji. Liczba punktów instalacji różnią się dla dysków.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Uruchom `ntfsfix` odpowiedniej ścieżki. Wyróżnione liczba może być taki sam jak krok 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Uruchom następujące polecenie, aby usunąć metadane hibernacji, które mogą być przyczyną problemu instalacji.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Czy czyste odinstalowanie.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Wykonaj czystą unlock i instalacji.
7. Testowanie punktu instalacji przez napisanie pliku.
8. Odinstaluj i zainstaluj, aby sprawdzić stan trwały plik.
9. Przejdź do kopiowania danych.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problem: Błąd nie utrzymuje się po kopiowania danych
 
**Przyczyna** 

Jeśli widzisz, że dysk nie znajdują się dane po odinstalować (chociaż dane zostały skopiowane do niej), a następnie można ponownie zainstalowany dysk jako odczytu / zapisu po dysk został zainstalowany jako tylko do odczytu.

**Rozdzielczość**
 
Jeśli tak jest rzeczywiście, zobacz Rozpoznawanie dla [dyskach Rozpoczynanie zainstalowanego jako tylko do odczytu](#issue-drive-getting-mounted-as-read-only).

Jeśli nie było tak, skopiuj dzienniki z folderu, który zawiera dane pole dysku odblokować narzędzie i [skontaktuj się z Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Błędy narzędzia do dzielenia skopiowanych dysków Data Box Disk

Problemy z wyświetlanymi podczas korzystania z narzędzia do kopiowania podziału podzielenie danych na wiele dysków są podsumowane w poniższej tabeli.

|Komunikat o błędzie/ostrzeżenia |Zalecenia |
|---------|---------|
|[Informacje o] Trwa pobieranie hasła funkcji BitLocker dla woluminu: m <br>[Błąd] Wystąpił wyjątek podczas pobierania klucza funkcji BitLocker dla woluminu m:<br> Sekwencja nie zawiera elementów.|Ten błąd jest zwracany, jeśli docelowe dyski Data Box Disk są w trybie offline. <br> W przypadku dysków w trybie online należy użyć narzędzia `diskmgmt.msc`.|
|[Błąd] Zgłoszono wyjątek: Nie można wykonać operacji usługi WMI:<br> Method = UnlockWithNumericalPassword, ReturnValue = 2150694965, <br>Win32Message = format podanego hasła odzyskiwania jest nieprawidłowy. <br>Hasła odzyskiwania funkcji BitLocker są 48-cyfrowe. <br>Sprawdź, czy hasło odzyskiwania ma poprawny format, a następnie spróbuj ponownie.|Użyj narzędzia do odblokowywania dysków Data Box Disk, aby odblokować dyski i ponowić próbę wykonania polecenia. Aby uzyskać więcej informacji, przejdź na stronę <li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odblokowywanie dysków Data Box Disk dla klientów systemu Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Błąd] Zgłoszono wyjątek: Plik DriveManifest.xml istnieje na dysku docelowego. <br> Oznacza to, że dysk docelowy został prawdopodobnie przygotowany przy użyciu innego pliku dziennika. <br>Aby dodać więcej danych do tego samego dysku, użyj poprzedniego pliku dziennika. Aby usunąć istniejące dane i ponownego użycia dysku docelowego dla nowego zadania importu, Usuń *DriveManifest.xml* na dysku. Ponownie uruchom to polecenie, używając nowego pliku dziennika.| Ten błąd zostanie wyświetlony podczas próby użycia tego samego zestawu dysków w wielu sesjach importowania. <br> Z jedną sesją dzielenia i kopiowania można użyć tylko jednego zestawu dysków.|
|[Błąd] Zgłoszono wyjątek: CopySessionId dane importowania września test-1 odwołuje się do poprzedniej sesji kopii i nie można użyć ponownie dla nowej sesji kopiowania.|Ten błąd jest zgłaszany w przypadku próby użycia tej samej nazwy zadania dla nowego zadania, które zostało poprzednio pomyślnie ukończone.<br> Należy przypisać unikatową nazwę dla nowego zadania.|
|[Informacje] Nazwa pliku lub katalogu docelowego przekracza limit długości dla systemu plików NTFS. |Ten komunikat jest zgłaszany, gdy nazwa pliku docelowego została zmieniona z powodu długiej ścieżki pliku.<br> Aby sterować tym zachowaniem, należy zmodyfikować opcję dyspozycji w pliku `config.json`.|
|[Błąd] Zgłoszono wyjątek: Nieprawidłowa sekwencja ucieczki JSON. |Ten komunikat jest zgłaszany, gdy format pliku config.json jest nieprawidłowy. <br> Przed zapisaniem pliku `config.json` zweryfikuj go przy użyciu narzędzia [JSONlint](https://jsonlint.com/).|


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [rozwiązywania problemów narzędzie do sprawdzania poprawności](data-box-disk-troubleshoot.md).
