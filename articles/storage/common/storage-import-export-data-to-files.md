---
title: Przesyłanie danych do usługi Azure Files za pomocą usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć zadania importu w witrynie Azure portal na przesyłanie danych do usługi Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 28026a429643c62434ddfd7591126169857a7371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479072"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usługa Azure Import/Export umożliwia importowanie danych do usługi Azure Files

Ten artykuł zawiera instrukcje krok po kroku dotyczące sposobu bezpiecznego importowania dużych ilości danych do usługi Azure Files przy użyciu usługi Azure Import/Export. Aby zaimportować dane, usługa wymaga wysłania obsługiwane dyski zawierające dane do centrum danych platformy Azure.  

Obsługuje usługi Import/Export importować tylko z usługą Azure Files do usługi Azure Storage. Eksportowanie do usługi Azure Files nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do tworzenia zadania importu do przenoszenia danych do usługi Azure Files, starannie Przejrzyj i ukończ poniższą listę wymagań wstępnych. Musisz mieć:

- Mieć aktywną subskrypcję platformy Azure za pomocą usługi Import/Export.
- Ma co najmniej jedno konto usługi Azure Storage. Przejrzyj listę rzeczy, [obsługiwanych kont magazynu i typów magazynu dla usługi Import/Export](storage-import-export-requirements.md). Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [sposób tworzenia konta magazynu](storage-quickstart-create-account.md).
- Mieć odpowiednią liczbę dysków [obsługiwane typy](storage-import-export-requirements.md#supported-disks). 
- System Windows z systemem [obsługiwany system operacyjny w wersji](storage-import-export-requirements.md#supported-operating-systems).
- [Pobierz WAImportExport w wersji 2](https://aka.ms/waiev2) w systemie Windows. Rozpakuj go do domyślnego folderu `waimportexport`. Na przykład `C:\WaImportExport`.
- Mieć konto FedEx/DHL w sprawie. Jeśli chcesz użyć operatora innego niż FedEx/DHL w sprawie, skontaktuj się z zespołem operacje pole danych platformy Azure w `adbops@microsoft.com`.  
    - Konto musi być prawidłowy, powinny mieć salda i musi mieć możliwości wysyłki zwrotnej.
    - Generowanie numer śledzenia, zadanie eksportu.
    - Każde zadanie powinno mieć numer oddzielne śledzenia. Wiele zadań przy użyciu tego samego numeru śledzenia nie są obsługiwane.
    - Jeśli nie masz konto przewoźnika, przejdź do strony:
        - [Utwórz konto FedEX](https://www.fedex.com/en-us/create-account.html), lub 
        - [Tworzenie konta przez firmę DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Krok 1: Przygotowywanie dysków

W tym kroku generuje plik dziennika. Plik dziennika zawiera podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegółów konta magazynu.

Wykonaj poniższe kroki, aby przygotować dyski.

1. Połączyć z naszym z stacje dysków do systemu Windows za pomocą łączników SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie należy używać punkty instalacji.
3. Modyfikowanie *dataset.csv* pliku w folderze głównym, w którym znajduje się narzędzie. W zależności od tego, czy chcesz importować pliku lub folderu, należy dodać wpisy w *dataset.csv* pliku podobne do poniższych przykładach.  

   - **Aby zaimportować plik**: W poniższym przykładzie dane do skopiowania znajduje się na dysku C:. Plik *MyFile1.txt* jest kopiowany do katalogu głównego *MyAzureFileshare1*. Jeśli *MyAzureFileshare1* nie istnieje, jest tworzony w ramach konta usługi Azure Storage. Struktura folderów jest zachowywana.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **Aby zaimportować folder**: Wszystkie pliki i foldery w obszarze *MyFolder2* są rekursywnie skopiowane do udziału plików. Struktura folderów jest zachowywana.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     Wiele wpisów mogą być wprowadzane w tym samym pliku odpowiadający foldery lub pliki, które są importowane. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     Dowiedz się więcej o [przygotowania pliku CSV zestawu danych](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Modyfikowanie *driveset.csv* pliku w folderze głównym, w którym znajduje się narzędzie. Dodawanie wpisów w *driveset.csv* pliku podobne do poniższych przykładach. Plik driveset ma listę dysków i odpowiednie litery dysku, więc narzędzie poprawnie można pobrać listy dysków, które mają zostać przygotowane.

    W tym przykładzie założono, że są dołączane dwa dyski i woluminy NTFS podstawowe G:\ i H:\ są tworzone. H:\is nie szyfrowane, gdy G: jest już zaszyfrowany. Narzędzie formatuje i szyfruje dysku, który obsługuje tylko H:\ (a nie G:\).

   - **Na dysku, które nie są szyfrowane**: Określ *Szyfruj* włączyć szyfrowanie funkcji BitLocker na dysku.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **Dla dysku, który jest już zaszyfrowany**: Określ *AlreadyEncrypted* i podaj klucz funkcji BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Wiele wpisów, może się w tym samym pliku, odpowiadające na wielu dyskach. Dowiedz się więcej o [przygotowania pliku CSV driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5. Użyj `PrepImport` opcję Kopiuj i przygotowywuj danych na dysku. Dla sesji kopiowania do skopiowania katalogów i/lub plików w nowej sesji kopiowania uruchom następujące polecenie:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Poniżej przedstawiono przykład importu.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. Plik dziennika o nazwie dostarczona z `/j:` parametru jest tworzony dla każdego uruchomienia wiersza polecenia. Każdy dysk, który należy przygotować ma w pliku dziennika, które muszą być przesłane, podczas tworzenia zadania importu. Dyski bez arkusza, w których pliki nie zostały przetworzone.

    > [!IMPORTANT]
    > - Po zakończeniu przygotowania dysku nie należy modyfikować dane w pliku dziennika lub dysków.

Aby uzyskać więcej przykładów, przejdź do [przykłady dla plików dziennika](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Krok 2: Tworzenie zadania importu 

Wykonaj poniższe kroki, aby utworzyć zadanie importu w witrynie Azure portal.
1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do **wszystkie usługi > Magazyn > zadania importu/eksportu**. 

    ![Przejdź do importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij przycisk **Tworzenie zadania importu/eksportu**.

    ![Kliknij zadanie importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. W **podstawy**:

    - Wybierz **Importuj na platformę Azure**.
    - Wprowadź opisową nazwę zadania importowania. Ta nazwa jest używana do śledzenia zadań, gdy są one w trakcie wykonywania i po zakończeniu.
        -  Ta nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        -  Nazwa musi zaczynać się literą i nie może zawierać spacji. 
    - Wybierz subskrypcję.
    - Wybierz grupę zasobów. 

        ![Tworzenie zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. W **szczegóły zadania**:
    
    - Przekazywanie plików dziennika, utworzone w ciągu poprzednich [krok 1: Przygotowywanie dysków](#step-1-prepare-the-drives). 
    - Wybierz konto magazynu, które mają zostać zaimportowane dane. 
    - Lokalizacja dropoff jest automatycznie wypełniane na podstawie w regionie wybranym koncie magazynu.
   
       ![Tworzenie zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. W **zwracają informacje o wysyłce**:

    - Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL w sprawie, wybierz opcję istniejący, z listy rozwijanej. Skontaktuj się z pomocą operacje pole danych platformy Azure zespołu w `adbops@microsoft.com` informacji na temat operatora planujesz używać.
    - Wprowadź numer konta operatora prawidłowe, utworzony za pomocą tego operatora. Firma Microsoft używa tego konta do wysłania dysków do Ciebie, po zakończeniu zadania importu. 
    - Podaj kompletne i prawidłowe nazwisko osoby kontaktowej, telefonicznej, wiadomości e-mail, adres, Miasto, zip, stan/prowincję/Województwo i kraj/region.

        > [!TIP] 
        > Zamiast określania adresu e-mail dla pojedynczego użytkownika, należy podać adres e-mail grupy. Dzięki temu otrzymywać powiadomienia, nawet jeśli opuści administrator.

       ![Tworzenie zadania importu — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. W **Podsumowanie**:

    - Podaj centrum danych platformy Azure adresu do wysyłki Azure dysków wysyłki. Upewnij się, czy nazwa zadania i pełnego adresu są wymienione na etykietę wysyłkową.
    - Kliknij przycisk **OK** aby zakończyć tworzenie zadania importu.

        ![Tworzenie zadania importu — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Krok 3: Dostarczaj dyski w centrach danych platformy Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizacja zadania przy użyciu informacji o śledzeniu

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5. Weryfikowanie przekazania danych na platformę Azure

Śledź zadania do zakończenia. Po zakończeniu zadania Sprawdź, czy danych został przekazany na platformę Azure. Usuń lokalne dane tylko w przypadku, gdy będziesz mieć pewność, że przekazywanie powiodło się.

## <a name="samples-for-journal-files"></a>Przykłady dla plików dziennika

Aby **dodawanie kolejnych dysków**, Utwórz nowy plik driveset i uruchom polecenie zgodnie z poniższymi instrukcjami. 

Sesje kolejnych kopii na różne dyski, niż określona w *CSV InitialDriveset* plikiem, należy określić nowy driveset *CSV* pliku, a następnie podać go jako wartość parametru `AdditionalDriveSet`. Użyj **tego samego pliku dziennika** nazwę, a następnie podaj **nowy identyfikator sesji**. Format pliku AdditionalDriveset CSV jest taki sam jak InitialDriveSet format.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Poniżej przedstawiono przykład importu.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Aby dodać dodatkowe dane do tej samej driveset, polecenie PrepImport sesji kolejnych kopiowania do skopiowania dodatkowe pliki/katalog.

Dla sesji kolejnych kopii tych samych dysków twardych określonych w *InitialDriveset.csv* plikiem, należy określić **tego samego pliku dziennika** nazwę, a następnie podaj **nowy identyfikator sesji**; istnieje nie trzeba podawać klucza konta magazynu.

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


