---
title: Eksportowanie danych z obiektów blob platformy Azure przy użyciu usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć zadania eksportu w witrynie Azure portal na przesyłanie danych z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e542ad59f6fd64b52aef9438ed0f646e9e36fc4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209623"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Usługa Azure Import/Export umożliwia eksportowanie danych z usługi Azure Blob storage
Ten artykuł zawiera instrukcje krok po kroku dotyczące sposobu używania usługi Azure Import/Export bezpiecznie eksportowania dużych ilości danych z usługi Azure Blob storage. Usługa wymaga dostarczaj puste dyski w centrach danych platformy Azure. Usługa eksportuje dane z konta magazynu na dyski i następnie jest dostarczany z stacje ponownie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do tworzenia zadania eksportu transferować dane z usługi Azure Blob Storage, należy dokładnie przejrzyj i ukończ Poniższa lista wymagań wstępnych dla tej usługi. Musisz mieć:

- Mieć aktywną subskrypcją platformy Azure, który może służyć do usługi Import/Export.
- Ma co najmniej jedno konto usługi Azure Storage. Przejrzyj listę rzeczy, [obsługiwanych kont magazynu i typów magazynu dla usługi Import/Export](storage-import-export-requirements.md). Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [sposób tworzenia konta magazynu](storage-quickstart-create-account.md).
- Mieć odpowiednią liczbę dysków [obsługiwane typy](storage-import-export-requirements.md#supported-disks).
- Mieć konto FedEx/DHL w sprawie. Jeśli chcesz użyć operatora innego niż FedEx/DHL w sprawie, skontaktuj się z zespołem operacje pole danych platformy Azure w `adbops@microsoft.com`. 
    - Konto musi być prawidłowy, powinny mieć salda i musi mieć możliwości wysyłki zwrotnej.
    - Generowanie numer śledzenia, zadanie eksportu.
    - Każde zadanie powinno mieć numer oddzielne śledzenia. Wiele zadań przy użyciu tego samego numeru śledzenia nie są obsługiwane. 
    - Jeśli nie masz konto przewoźnika, przejdź do strony:
        - [Utwórz konto FedEX](https://www.fedex.com/en-us/create-account.html), lub 
        - [Tworzenie konta przez firmę DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Krok 1: Tworzenie zadania eksportu

Wykonaj poniższe kroki, aby utworzyć zadanie eksportu w witrynie Azure portal.

1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do **wszystkie usługi > Magazyn > zadania importu/eksportu**. 

    ![Przejdź do zadania importu/eksportu](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kliknij przycisk **Tworzenie zadania importu/eksportu**.

    ![Kliknij zadanie importu/eksportu](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. W **podstawy**:
    
    - Wybierz **Eksportuj z platformy Azure**. 
    - Wprowadź opisową nazwę zadania eksportu. Użyj imienia lub pseudonimu wybierz w celu śledzenia postępu zadań. 
        - Nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        - Nazwa musi zaczynać się literą i nie może zawierać spacji. 
    - Wybierz subskrypcję.
    - Wprowadź lub wybierz grupę zasobów.

        ![Podstawy](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. W **szczegóły zadania**:

    - Wybierz konto magazynu, w której znajdują się dane, które mają zostać wyeksportowane. Użyj konta magazynu w pobliżu lokalizacji.
    - Lokalizacja dropoff jest automatycznie wypełniane na podstawie w regionie wybranym koncie magazynu. 
    - Określ dane obiektów blob, który chcesz wyeksportować z konta magazynu do pustego dysku lub dysków. 
    - Możliwość **Wyeksportuj wszystkie** danych obiektu blob na koncie magazynu.
    
         ![Eksportuj wszystko](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Można określić, które kontenery i obiekty BLOB do wyeksportowania.
        - **Aby określić obiekt blob do wyeksportowania**: Użyj **równe** selektora. Określ ścieżkę względną do obiektu blob, począwszy od nazwy kontenera. Użyj *$root* do określenia kontenera głównego.
        - **Aby określić wszystkie obiekty BLOB rozpoczynających się prefiksem**: Użyj **rozpoczyna się od** selektora. Określ prefiks, rozpoczynające się od ukośnika "/". Prefiks może składać się z prefiksu nazwy kontenera, nazwa kontenera pełną lub nazwę kontenera ukończone, a następnie prefiks nazwy obiektu blob. Należy podać ścieżki obiektu blob w prawidłowym formacie, aby uniknąć błędów podczas przetwarzania, jak pokazano w tym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [przykłady ścieżek prawidłowy obiekt blob](#examples-of-valid-blob-paths). 
   
           ![Eksportuj zaznaczone kontenerów i obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Możesz wyeksportować z pliku listy obiektów blob.

        ![Eksportowanie z pliku listy obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Jeśli obiekt blob do wyeksportowania jest używany podczas kopiowania danych, usługa Azure Import/Export tworzy migawkę obiektu blob i kopiuje migawkę.
 

4. W **zwracają informacje o wysyłce**:

    - Wybierz nośnik, z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL w sprawie, wybierz opcję istniejący, z listy rozwijanej. Skontaktuj się z pomocą operacje pole danych platformy Azure zespołu w `adbops@microsoft.com` informacji na temat operatora planujesz używać.
    - Wprowadź numer konta operatora prawidłowe, utworzony za pomocą tego operatora. Firma Microsoft używa tego konta do wysłania dysków do Ciebie, po zakończeniu zadania eksportu. 
    - Podaj kompletne i prawidłowe nazwisko osoby kontaktowej, telefonicznej, wiadomości e-mail, adres, Miasto, zip, stan/prowincję/Województwo i kraj/region.

        > [!TIP] 
        > Zamiast określania adresu e-mail dla pojedynczego użytkownika, należy podać adres e-mail grupy. Dzięki temu otrzymywać powiadomienia, nawet jeśli opuści administrator.
   
5. W **Podsumowanie**:

    - Przejrzyj szczegóły zadania.
    - Zanotuj nazwę zadania i podana centrum danych platformy Azure, wysyłania adres wysyłkowy dysków na platformie Azure. 

        > [!NOTE] 
        > Zawsze wysyłaj dysków do centrum danych w witrynie Azure portal. Jeśli dyski zostaną dostarczone do niewłaściwej centrum danych, zadania nie zostaną przetworzone.

    - Kliknij przycisk **OK** aby zakończyć tworzenie zadania eksportu.

## <a name="step-2-ship-the-drives"></a>Krok 2: Dostarczaj dyski

Jeśli nie znasz liczbę stacji, należy przejść do [Sprawdź liczbę dysków](#check-the-number-of-drives). Jeśli wiesz, liczba dysków, przejdź do wysłania dysków.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: Aktualizacja zadania przy użyciu informacji o śledzeniu

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Krok 4: Odbieranie dysków
Gdy pulpit nawigacyjny zgłasza, że zadanie zostało ukończone, dyski zostaną dostarczone do Ciebie, a numer śledzenia dla danego wydania jest dostępna w portalu.

1. Po otrzymaniu dyski z wyeksportowane dane, musisz Pobierz klucze funkcji BitLocker, aby odblokować dyski. Przejdź do zadania eksportu w witrynie Azure portal. Kliknij przycisk **Import/Export** kartę. 
2. Wybierz i kliknij zadanie eksportu z listy. Przejdź do **klucze funkcji BitLocker** i kopiowanie kluczy.
   
   ![Wyświetl klucze funkcji BitLocker dla zadania eksportu](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Klucze funkcji BitLocker umożliwia odblokowanie dysków.

Eksportowanie zostało ukończone. W tej chwili można usunąć zadania lub automatycznie pobiera usunięty po upływie 90 dni.


## <a name="check-the-number-of-drives"></a>Sprawdź liczbę dysków

To *opcjonalne* krok ułatwia określa liczbę dysków wymaganych do zadania eksportu. Wykonaj tę czynność na działanie systemu Windows [obsługiwany system operacyjny w wersji](storage-import-export-requirements.md#supported-operating-systems).

1. [Pobierz WAImportExport wersji 1](https://aka.ms/waiev1) w systemie Windows. 
2. Rozpakuj go do domyślnego folderu `waimportexportv1`. Na przykład `C:\WaImportExportV1`.
3. Otwórz okno programu PowerShell lub wierszu polecenia z uprawnieniami administracyjnymi. Zmień katalog na folder rozpakowany, uruchom następujące polecenie:
    
    `cd C:\WaImportExportV1`

4. Aby sprawdzić, liczba dysków wymagana dla wybranych obiektów blob, uruchom następujące polecenie:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    W poniższej tabeli opisano parametry:
    
    |Parametr wiersza polecenia|Opis|  
    |--------------------------|-----------------|  
    |**/logdir:**|Opcjonalny. Katalog dziennika. Plików pełnego dziennika są zapisywane do tego katalogu. Jeśli nie zostanie określony, bieżący katalog jest używany jako katalog dziennika.|  
    |**/SN:**|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
    |**/sk:**|Wymagane tylko, jeśli sygnatury dostępu Współdzielonego kontenera nie jest określony. Klucz konta dla konta magazynu dla zadania eksportu.|  
    |**/csas:**|Wymagane tylko, jeśli nie określono klucza konta magazynu. Sygnatury dostępu Współdzielonego kontenera do wyświetlania listy obiektów blob, które mają zostać wyeksportowane przez zadanie eksportu.|  
    |**/ExportBlobListFile:**|Wymagany. Ścieżka do pliku XML plik zawierający listę ścieżek obiektów blob lub obiektu blob prefiksy ścieżki dla obiektów blob do wyeksportowania. Format pliku używany w `BlobListBlobPath` element [umieścić zadania](/rest/api/storageimportexport/jobs) operacji interfejs API REST usługi Import/Export.|  
    |**/DriveSize:**|Wymagany. Rozmiar dysków do użycia przez zadanie eksportu *np.* , 500 GB, 1,5 TB.|  

    Zobacz [przykład polecenia PreviewExport](#example-of-previewexport-command).
 
5. Sprawdź, czy użytkownik może odczytu/zapisu na dyskach, które zostaną dostarczone zadanie eksportu.

### <a name="example-of-previewexport-command"></a>Przykład polecenia PreviewExport

W poniższym przykładzie pokazano `PreviewExport` polecenia:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Pliku listy obiektów blob eksportu może zawierać nazwy obiektów blob i obiektów blob prefiksy, jak pokazano poniżej:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Narzędzie importu/eksportu platformy Azure zawiera listę wszystkich obiektów blob do wyeksportowania oblicza jak umieszczenie ich na dyski o określonym rozmiarze, biorąc pod uwagę wszystkie niezbędne czynności, a następnie Szacuje liczbę dysków wymaganych do przechowywania obiektów blob oraz informacje o użyciu dysku.  
  
Oto przykład danych wyjściowych za pomocą dzienników informacyjny pominięte:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Przykłady ścieżek prawidłowy obiekt blob

W poniższej tabeli przedstawiono przykłady ścieżek prawidłowy obiekt blob:
   
   | Selektor | Ścieżka obiektu blob | Opis |
   | --- | --- | --- |
   | Rozpoczyna się od |/ |Eksportuje wszystkie obiekty BLOB na koncie magazynu |
   | Rozpoczyna się od |/$root / |Eksportuje wszystkie obiekty BLOB w kontenerze głównego |
   | Rozpoczyna się od |/book |Eksportuje wszystkie obiekty BLOB w dowolnym kontenerze, który rozpoczyna się od prefiksu **książki** |
   | Rozpoczyna się od |/Music/ |Eksportuje wszystkie obiekty BLOB w kontenerze **utworów muzycznych** |
   | Rozpoczyna się od |/ music/love |Eksportuje wszystkie obiekty BLOB w kontenerze **utworów muzycznych** , zaczynać się od prefiksu **ulubionych** |
   | Równa się |$root/logo.bmp |Eksportowanie obiektów blob **logo.bmp** w kontenerze głównego |
   | Równa się |videos/Story.mp4 |Eksportowanie obiektów blob **story.mp4** w kontenerze **filmów wideo** |

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań importu/eksportu](storage-import-export-requirements.md)


