---
title: Przy użyciu Import/Eksport Azure na przesyłanie danych do usługi pliki Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zadania importu w portalu Azure na przesyłanie danych do usługi pliki Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 4349b471f960e7844511c473bffcd2177a34e055
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660884"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usługa Import/Eksport Azure umożliwia importowanie danych do usługi pliki Azure

Ten artykuł zawiera instrukcje krok po kroku dotyczące sposobu używania usługi Import/Eksport Azure można bezpiecznie zaimportować dużych ilości danych do usługi pliki Azure. Aby zaimportować dane, usługa wymaga do wysłania obsługiwanych stacje dysków z danymi do centrum danych Azure.  

Do usługi Azure Storage obsługuje usługi Import/Eksport importować plików Azure. Eksportowanie plików Azure nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importu na przesyłanie danych do usługi pliki Azure uważnie Przejrzyj i ukończ poniższą listę wymagań wstępnych. Należy:

- Mieć aktywną subskrypcją platformy Azure do użycia z usługą importu i eksportu.
- Ma co najmniej jedno konto magazynu Azure. Lista [obsługiwany konta magazynu i typów magazynu dla usługi Import/Eksport](storage-import-export-requirements.md). Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-create-storage-account.md#create-a-storage-account).
- Ma odpowiedniej liczby dysków [obsługiwane typy](storage-import-export-requirements.md#supported-disks). 
- System Windows z systemem [obsługiwany system operacyjny w wersji](storage-import-export-requirements.md#supported-operating-systems).
- [Pobierz WAImportExport w wersji 2](https://www.microsoft.com/download/details.aspx?id=55280) w systemie Windows. Rozpakuj w domyślnym folderze `waimportexport`. Na przykład `C:\WaImportExport`.


## <a name="step-1-prepare-the-drives"></a>Krok 1: Przygotowanie dysków

Ten krok generuje plik dziennika. Plik dziennika zawiera podstawowe informacje, takie jak dysk numer seryjny, klucz szyfrowania i szczegóły konta magazynu.

Wykonaj poniższe kroki, aby przygotować dyski.

1. Połącz naszych dysków do systemu Windows za pomocą łączników SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie należy używać punkty instalacji.
3. Modyfikowanie *dataset.csv* pliku w folderze głównym, na którym znajduje się narzędzie. W zależności od tego, czy chcesz importować pliku lub folderu, należy dodać wpisy w *dataset.csv* pliku podobne do poniższych przykładach.  

    - **Aby zaimportować plik**: W poniższym przykładzie dane do skopiowania znajduje się na dysku C:. Plik *MyFile1.txt* jest kopiowany do katalogu głównego *MyAzureFileshare1*. Jeśli *MyAzureFileshare1* nie istnieje, jest tworzony na koncie magazynu Azure. Struktura folderów jest obsługiwana.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **Aby zaimportować folderu**: wszystkie pliki i foldery w obszarze *MyFolder2* są rekursywnie skopiowane do udziału plików. Struktura folderów jest obsługiwana.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Wiele wpisów można podjąć w tym samym pliku odpowiadający foldery lub pliki, które są importowane. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            "F:\MyFolder3\MyFile3.txt","MyAzureFileshare2/",file,rename,"None",None 
            
        ```
    Dowiedz się więcej o [przygotowania pliku CSV zestawu danych](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Modyfikowanie *driveset.csv* pliku w folderze głównym, na którym znajduje się narzędzie. Dodawanie wpisów w *driveset.csv* pliku podobne do poniższych przykładach. Plik driveset ma listę dysków i odpowiednich liter dysku, aby narzędzie poprawnie można pobrać listy dysków, aby przygotować.

    W tym przykładzie przyjęto założenie, że są dołączone dwa dyski i woluminy NTFS podstawowe G:\ i H:\ są tworzone. H:\is nie szyfrowane, gdy G: jest już zaszyfrowany. Narzędzie formatuje i szyfrowanie dysku, który obsługuje tylko H:\ (a nie G:\).

    - **Dla dysku, który nie jest zaszyfrowany**: Określ *Szyfruj* Aby włączyć szyfrowanie funkcji BitLocker na dysku.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **Dla dysku, który jest już zaszyfrowane**: Określ *AlreadyEncrypted* i podaj klucza funkcji BitLocker.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Wiele wpisów może się w tym samym pliku odpowiadający wiele dysków. Dowiedz się więcej o [przygotowania pliku CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Użyj `PrepImport` opcję, aby skopiować i przygotowania danych na dysku. Dla sesji kopiowania do skopiowania katalogów i/lub pliki w nowej sesji kopiowania uruchom następujące polecenie:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Poniżej przedstawiono przykład importu.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. Plik dziennika o nazwie dostarczona z `/j:` parametru jest tworzony dla każdego uruchomienia wiersza polecenia. Każdy dysk, który należy przygotować ma w pliku dziennika, który należy przekazać podczas tworzenia zadania importu. Dyski bez dziennika, które nie są przetwarzane pliki.

    > [!IMPORTANT]
    > - Po zakończeniu przygotowywania dysków nie należy modyfikować dane na dyski lub pliku dziennika.

Aby uzyskać dodatkowe przykłady [przykłady dla plików dziennika](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: Tworzenie zadania importu 

Wykonaj poniższe kroki, aby utworzyć zadanie importu w portalu Azure.
1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do **wszystkie usługi > magazynu > zadania importu/eksportu**. 

    ![Przejdź do importowania i eksportowania](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij przycisk **zadania importu/eksportu Utwórz**.

    ![Kliknij pozycję zadania importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. W **podstawy**:

    - Wybierz **importu na platformie Azure**.
    - Wprowadź nazwę opisową dla zadania importu. Ta nazwa jest używana do śledzenia zadań, gdy są one w toku, a po zakończeniu.
        -  Ta nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        -  Nazwa musi zaczynać się literą i nie może zawierać spacji. 
    - Wybierz subskrypcję.
    - Wybierz grupę zasobów. 

        ![Utwórz zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. W **szczegóły zadania**:
    
    - Przekazywanie plików dziennika, które zostały utworzone w ciągu poprzednich [krok 1: przygotowanie dyski](#step-1-prepare-the-drives). 
    - Wybierz konto magazynu, którego dane mają zostać zaimportowane. 
    - Lokalizacja przyjmowania jest wypełniane automatycznie na podstawie regionu wybrane konta magazynu.
   
       ![Utwórz zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. W **zwraca informacje o wysyłki**:

    - Wybierz nośnik, z listy rozwijanej.
    - Wprowadź numer konta nieprawidłowy operator, które zostały utworzone z tego nośnika. Firma Microsoft używa tego konta na potrzeby wysłania dysków powrotem po zakończeniu zadania importu. 
    - Podaj nazwisko osoby kontaktowej w pełne i prawidłowe, telefonu, poczty e-mail, adres, Miasto, zip, Województwo i kraj/region.

       ![Utwórz zadania importu — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. W **Podsumowanie**:

    - Podaj centrum danych Azure adresu do wysyłania Azure dysków wysyłki. Upewnij się, są wymienione nazwy zadania i pełny adres na etykiecie wysyłki.
    - Kliknij przycisk **OK** aby zakończyć tworzenie zadania importu.

        ![Utwórz zadania importu — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: Wyślij dysków do centrum danych Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizowanie zadania ze śledzeniem informacji

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="samples-for-journal-files"></a>Przykłady dla plików dziennika

Aby **dodawanie kolejnych dysków**, Utwórz nowy plik driveset i uruchom polecenie zgodnie z poniższymi instrukcjami. 

Sesje kolejnych kopii na różnych dyskach niż określona w *CSV InitialDriveset* pliku, określ nowe driveset *CSV* plik, a następnie podaj go jako wartość parametru `AdditionalDriveSet`. Użyj **tego samego pliku dziennika** nazwy i podaj **nowy identyfikator sesji**. Format pliku AdditionalDriveset CSV jest taki sam jak InitialDriveSet format.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Poniżej przedstawiono przykład importu.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Aby dodać dodatkowe dane do tej samej driveset, użyj polecenia PrepImport sesji kolejnych kopii można skopiować dodatkowe pliki lub katalogu.

Sesje kolejnych kopii do tej samej stacje dysków twardych, określona w *InitialDriveset.csv* pliku, określ **tego samego pliku dziennika** nazwy i podaj **nowy identyfikator sesji**; nie istnieje potrzeba zapewnienie klucz konta magazynu.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Poniżej przedstawiono przykład importu.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań importu/eksportu](storage-import-export-requirements.md)


