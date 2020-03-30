---
title: Używanie narzędzia Azure Import/Export do przesyłania danych do plików platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć zadania importowania w witrynie Azure portal, aby przenieść dane do usługi Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268303"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Use Azure Import/Export service to import data to Azure Files (Używanie usługi Azure Import/Export do importowania danych do usługi Azure Files)

Ten artykuł zawiera instrukcje krok po kroku dotyczące używania usługi Azure Import/Export do bezpiecznego importowania dużych ilości danych do usługi Azure Files. Aby zaimportować dane, usługa wymaga wysłania obsługiwanych dysków zawierających dane do centrum danych platformy Azure.  

Usługa Importuj/eksportuj obsługuje tylko importowanie plików platformy Azure do usługi Azure Storage. Eksportowanie plików platformy Azure nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importu do przesyłania danych do usługi Azure Files należy dokładnie przejrzeć i wypełnić następującą listę wymagań wstępnych. Musisz:

- Mieć aktywną subskrypcję platformy Azure do użycia z usługą Importuj/Eksportuj.
- Mieć co najmniej jedno konto usługi Azure Storage. Zobacz listę [obsługiwanych kont magazynu i typów magazynowania dla usługi Import/Eksport .](storage-import-export-requirements.md) Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [Jak utworzyć konto magazynu](storage-account-create.md).
- Posiadać odpowiednią liczbę dysków [obsługiwanych typów](storage-import-export-requirements.md#supported-disks).
- Mieć system Windows z uruchomiona [w wersji obsługiwanego systemu operacyjnego](storage-import-export-requirements.md#supported-operating-systems).
- [Pobierz WAImportExport w wersji 2](https://aka.ms/waiev2) w systemie Windows. Rozpaj do folderu `waimportexport`domyślnego . Na przykład `C:\WaImportExport`.
- Posiadaj konto FedEx/DHL. Jeśli chcesz użyć przewoźnika innego niż FedEx/DHL, skontaktuj `adbops@microsoft.com`się z zespołem operacji usługi Azure Data Box pod adresem .  
    - Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki zwrotnej.
    - Wygeneruj numer śledzenia dla zadania eksportu.
    - Każde zadanie powinno mieć osobny numer śledzenia. Wiele zadań o tym samym numerze śledzenia nie są obsługiwane.
    - Jeśli nie masz konta operatora, przejdź do:
        - [Utwórz konto FedEX](https://www.fedex.com/en-us/create-account.html), lub
        - [Utwórz konto DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Krok 1: Przygotowanie napędów

Ten krok generuje plik dziennika. Plik dziennika przechowuje podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegóły konta magazynu.

Wykonaj następujące kroki, aby przygotować dyski.

1. Podłącz nasze dyski do systemu Windows za pomocą złączy SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie używaj punktów montażowych.
3. Zmodyfikuj plik *dataset.csv* w folderze głównym, w którym znajduje się narzędzie. W zależności od tego, czy chcesz zaimportować plik lub folder, czy oba, dodaj wpisy w pliku *dataset.csv* podobne do poniższych przykładów.  

   - **Aby zaimportować plik**: W poniższym przykładzie dane do skopiowania znajdują się na dysku F:. Plik *MyFile1.txt* jest kopiowany do katalogu głównego *folderu MyAzureFileshare1*. Jeśli *MyAzureFileshare1* nie istnieje, jest tworzony na koncie usługi Azure Storage. Struktura folderów jest utrzymywana.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Aby zaimportować folder:** Wszystkie pliki i foldery w obszarze *Folder MyFolder2* są rekursywnie kopiowane do udziału plików. Struktura folderów jest utrzymywana.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     W tym samym pliku można dokonać wielu wpisów odpowiadających importowanym folderom lub plikom.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Dowiedz się więcej o [przygotowywaniu pliku CSV zestawu danych](storage-import-export-tool-preparing-hard-drives-import.md).


4. Zmodyfikuj plik *driveset.csv* w folderze głównym, w którym znajduje się narzędzie. Dodaj wpisy w pliku *driveset.csv* podobne do poniższych przykładów. Plik zestawu dysków zawiera listę dysków i odpowiadające im litery dysków, dzięki czemu narzędzie może poprawnie wybrać listę dysków, które mają być przygotowane.

    W tym przykładzie założono, że dwa dyski są dołączone i podstawowe woluminy NTFS G:\ i H:\ są tworzone. H:\nie jest szyfrowany, gdy G: jest już zaszyfrowany. Narzędzie formatuje i szyfruje dysk, na którym znajduje się dysk H:\ tylko (a nie\)G: .

   - **Dla dysku, który nie jest zaszyfrowany:** Określ *szyfrowanie,* aby włączyć szyfrowanie funkcją BitLocker na dysku.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Dla dysku, który jest już zaszyfrowany:** Określ *juższyfrowany* i podać klucz Funkcji BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     W tym samym pliku można dokonać wielu wpisów odpowiadających wielu dyskom. Dowiedz się więcej o [przygotowywaniu pliku CSV zestawu dysków](storage-import-export-tool-preparing-hard-drives-import.md).

5. Użyj `PrepImport` tej opcji, aby skopiować i przygotować dane na dysk. W przypadku pierwszej sesji kopiowania w celu skopiowania katalogów i/lub plików przy nowej sesji kopiowania uruchom następujące polecenie:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Przykład importu jest pokazany poniżej.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. Plik dziennika o nazwie `/j:` podanej z parametrem jest tworzony dla każdego uruchomienia wiersza polecenia. Każdy przygotowywany dysk ma plik dziennika, który musi zostać przekazany podczas tworzenia zadania importu. Dyski bez plików dziennika nie są przetwarzane.

    > [!IMPORTANT]
    > - Nie należy modyfikować danych na dyskach twardych ani w pliku dziennika po zakończeniu przygotowania dysku.

Aby uzyskać dodatkowe próbki, przejdź do [przykładów dla plików dziennika](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: Tworzenie zadania importu

Wykonaj następujące kroki, aby utworzyć zadanie importu w witrynie Azure portal.
1. Zaloguj się https://portal.azure.com/do pliku .
2. Przejdź do **pozycję Wszystkie usługi > zadaniami magazynowania > importu/eksportu**.

    ![Przejdź do importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij **pozycję Utwórz zadanie importu/eksportu**.

    ![Kliknij pozycję Importuj/eksportuj zadanie](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. W **podstawach**:

    - Wybierz **pozycję Importuj na platformę Azure**.
    - Wprowadź opisową nazwę zadania importu. Ta nazwa służy do śledzenia zadań w trakcie ich realizacji i po ich zakończeniu.
        -  Ta nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        -  Nazwa musi zaczynać się od litery i nie może zawierać spacji.
    - Wybierz subskrypcję.
    - Wybierz grupę zasobów.

        ![Tworzenie zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. W **szczegóły zadania**:

    - Przekaż pliki dziennika utworzone podczas poprzedniego [kroku 1: Przygotowanie dysków](#step-1-prepare-the-drives).
    - Wybierz konto magazynu, do które będą importowane dane.
    - Lokalizacja nadajnicza jest wypełniana automatycznie na podstawie regionu wybranego konta magazynu.

       ![Tworzenie zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. W **zamian informacje o wysyłce:**

    - Wybierz przewoźnika z listy rozwijanej. Jeśli chcesz użyć przewoźnika innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się `adbops@microsoft.com` z zespołem operacji platformy Azure Data Box, aby uzyskać informacje dotyczące operatora, którego zamierzasz użyć.
    - Wprowadź prawidłowy numer konta przewoźnika utworzony z tym operatorem. Firma Microsoft używa tego konta do wysłania dysków z powrotem do Użytkownika po zakończeniu zadania importowania.
    - Podaj pełną i prawidłową nazwę kontaktu, telefon, adres e-mail, adres pocztowy, miasto, zamek błyskawiczny, województwo i kraj/region.

        > [!TIP]
        > Zamiast określać adres e-mail dla pojedynczego użytkownika, podaj grupową wiadomość e-mail. Dzięki temu otrzymasz powiadomienia, nawet jeśli administrator opuści.

       ![Tworzenie zadania importu — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. W **podsumowaniu:**

    - Podaj adres wysyłki centrum danych platformy Azure dla dysków wysyłkowych z powrotem na platformę Azure. Upewnij się, że nazwa zadania i pełny adres są wymienione na etykiecie wysyłkowej.
    - Kliknij **przycisk OK,** aby zakończyć tworzenie zadań importowych.

        ![Tworzenie zadania importu — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: Wyślij dyski do centrum danych platformy Azure

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizowanie zadania za pomocą informacji o śledzeniu

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5: Weryfikowanie przekazywania danych na platformę Azure

Śledzenie zadania do zakończenia. Po zakończeniu zadania sprawdź, czy dane zostały przekazane na platformę Azure. Usuń dane lokalne dopiero po zweryfikowaniu, że przekazywanie zakończyło się pomyślnie.

## <a name="samples-for-journal-files"></a>Przykłady dla plików dziennika

Aby **dodać więcej dysków,** utwórz nowy plik zestawu dysków i uruchom polecenie jak poniżej.

W przypadku kolejnych sesji kopiowania na inne dyski niż określone w pliku *csv initialDriveset* należy określić `AdditionalDriveSet`nowy plik *csv* zestawu dysków i podać go jako wartość parametru . Użyj **tej samej** nazwy pliku dziennika i podaj **nowy identyfikator sesji**. Format pliku CSV additionaldriveset jest taki sam jak format InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Przykład importu jest pokazany poniżej.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Aby dodać dodatkowe dane do tego samego zestawu dysków, użyj polecenia PrepImport dla kolejnych sesji kopiowania, aby skopiować dodatkowe pliki/katalog.

W przypadku kolejnych sesji kopiowania na te same dyski twarde, które zostały określone w pliku *InitialDriveset.csv,* określ **tę samą** nazwę pliku dziennika i podaj **nowy identyfikator sesji;** nie ma potrzeby podawania klucza konta magazynu.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Przykład importu jest pokazany poniżej.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie stanowiska i stanu dysku](storage-import-export-view-drive-status.md)
* [Przejrzyj wymagania dotyczące importu/eksportu](storage-import-export-requirements.md)
