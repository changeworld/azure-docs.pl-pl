---
title: Eksportowanie danych z obiektów blob Azure za pomocą Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć zadania eksportu w portalu Azure na przesyłanie danych z obiektów blob Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: eb41708c7446b3139758678c9247ffbb11da8b40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660872"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Eksportowanie danych z magazynu obiektów Blob platformy Azure za pomocą usługi Import/Eksport Azure
Ten artykuł zawiera instrukcje krok po kroku dotyczące sposobu używania usługi Import/Eksport Azure bezpiecznie eksportowania dużych ilości danych z magazynu obiektów Blob platformy Azure. Usługa wymaga do wysłania pustych dysków do centrum danych Azure. Usługa eksportuje dane z konta magazynu na dyski i następnie dostarczany ponownie dyski.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania eksportu na przesyłanie danych z magazynu obiektów Blob Azure uważnie Przejrzyj i ukończ poniższą listę wymagań wstępnych dla tej usługi. Należy:

- Mieć aktywną subskrypcją platformy Azure, który może służyć do usługi Import/Eksport.
- Ma co najmniej jedno konto magazynu Azure. Lista [obsługiwany konta magazynu i typów magazynu dla usługi Import/Eksport](storage-import-export-requirements.md). Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-create-storage-account.md#create-a-storage-account).
- Ma odpowiedniej liczby dysków [obsługiwane typy](storage-import-export-requirements.md#supported-disks).

## <a name="step-1-create-an-export-job"></a>Krok 1: Tworzenie zadania eksportu

Wykonaj poniższe kroki, aby utworzyć zadanie eksportu w portalu Azure.

1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do **wszystkie usługi > magazynu > zadania importu/eksportu**. 

    ![Przejdź do zadania importu/eksportu](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kliknij przycisk **zadania importu/eksportu Utwórz**.

    ![Kliknij pozycję zadania importu/eksportu](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. W **podstawy**:
    
    - Wybierz **wyeksportować z usługi Azure**. 
    - Wprowadź nazwę opisową dla zadania eksportu. Użyj nazwy, aby śledzić postęp zadań. 
        - Nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        - Nazwa musi zaczynać się literą i nie może zawierać spacji. 
    - Wybierz subskrypcję.
    - Wprowadź lub wybierz grupę zasobów.

        ![Podstawy](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. W **szczegóły zadania**:

    - Wybierz konto magazynu, w którym znajdują się dane do wyeksportowania. 
    - Lokalizacja przyjmowania jest wypełniane automatycznie na podstawie regionu wybrane konta magazynu. 
    - Określ dane obiektów blob, który chcesz wyeksportować z konta magazynu do pustego dysku lub dysków. 
    - Wybierz **wyeksportować wszystkie** obiektu blob danych na koncie magazynu.
    
         ![Eksportuj całą](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - Można określić, które kontenery i obiekty BLOB do wyeksportowania.
        - **Aby określić obiekt blob, aby wyeksportować**: Użyj **równe** selektora. Określ ścieżkę względną do obiektu blob, począwszy od nazwy kontenera. Użyj *$root* Aby określić kontener główny.
        - **Aby określić wszystkie obiekty BLOB, począwszy od prefiksu**: Użyj **rozpoczyna się od** selektora. Określ prefiks, rozpoczynający się od ukośnika "/". Prefiks może być prefiksem nazwy kontenera, nazwa kontenera pełną lub nazwę kontenera ukończone, a następnie prefiks nazwy obiektu blob. Należy podać ścieżki obiektu blob w nieprawidłowym formacie, aby uniknąć błędów podczas przetwarzania, jak pokazano na tym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [przykłady ścieżek nieprawidłowy obiekt blob](#examples-of-valid-blob-paths). 
   
           ![Eksportuj wybrane kontenery i obiekty BLOB](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Możesz wyeksportować plik do listy obiektów blob.

        ![Eksportowanie z pliku listy obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Jeśli do wyeksportowania obiektu blob jest używany podczas kopiowania danych, usługi Import/Eksport Azure tworzy migawkę obiektu blob i kopiuje migawki.
 

4. W **zwraca informacje o wysyłki**:

    - Wybierz nośnik, z listy rozwijanej.
    - Wprowadź numer konta nieprawidłowy operator, które zostały utworzone z tego nośnika. Firma Microsoft używa tego konta na potrzeby wysłania dysków powrotem po zakończeniu zadania importu. 
    - Podaj nazwisko osoby kontaktowej w pełne i prawidłowe, telefonu, poczty e-mail, adres, Miasto, zip, Województwo i kraj/region.
   
5. W **Podsumowanie**:

    - Przejrzyj szczegóły zadania.
    - Należy pamiętać, adres wysyłkowy nazwa pod warunkiem centrum danych Azure zadania dla wysyłania dysków na platformie Azure. 
    - Kliknij przycisk **OK** aby zakończyć tworzenie zadania eksportu.

## <a name="step-2-ship-the-drives"></a>Krok 2: Wysłać dysków

Jeśli nie znasz liczba dysków, należy przejść do [Sprawdź liczbę dysków](#check-the-number-of-drives). Jeśli znasz liczba dysków, przejdź do wysyłki na dyskach.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: Zaktualizuj zadania ze śledzeniem informacji

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Krok 4: Odbieranie dysków
Po zakończeniu zadania raporty pulpitu nawigacyjnego, dyski są wysyłane do Ciebie i numer wysyłki jest dostępna w portalu.

1. Po otrzymaniu stacje z wyeksportowane dane, musisz pobrać klucze funkcji BitLocker do odblokowywania dysków. Przejdź do zadania eksportu w portalu Azure. Kliknij przycisk **importu/eksportu** kartę. 
2. Wybierz i kliknij eksportu zadań z listy. Przejdź do **klucze funkcji BitLocker** i kopiowanie kluczy.
   
   ![Wyświetl klucze funkcji BitLocker dla zadania eksportu](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Klucze funkcji BitLocker umożliwia odblokowanie dysków.

Eksport jest ukończona. W tej chwili można usunąć zadania lub automatycznie pobiera ona usunięte po 90 dniach.


## <a name="check-the-number-of-drives"></a>Sprawdź liczbę dysków

To *opcjonalne* krok ułatwia określa liczbę dysków wymaganych dla zadania eksportu. Wykonaj ten krok w systemie Windows uruchomiony [obsługiwany system operacyjny w wersji](storage-import-export-requirements.md#supported-operating-systems).

1. [Pobierz WAImportExport wersji 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) w systemie Windows. 
2. Rozpakuj w domyślnym folderze `waimportexportv1`. Na przykład `C:\WaImportExportV1`.
3. Otwórz okno programu PowerShell lub wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog, aby rozpakować folderu, uruchom następujące polecenie:
    
    `cd C:\WaImportExportV1`

4. Aby sprawdzić liczbę dysków wymaganych dla wybranych obiektów blob, uruchom następujące polecenie:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    W poniższej tabeli opisano parametry:
    
    |Parametr wiersza polecenia|Opis|  
    |--------------------------|-----------------|  
    |**/ logdir:**|Opcjonalny. Katalog dziennika. Plików pełnego dziennika są zapisywane do tego katalogu. Jeśli nie zostanie określony, bieżący katalog jest używany jako katalog dziennika.|  
    |**/SN:**|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
    |**/SK:**|Wymagany tylko wtedy, jeśli nie określono kontenera sygnatury dostępu Współdzielonego. Klucz konta dla konta magazynu dla zadania eksportu.|  
    |**/csas:**|Wymagany tylko wtedy, jeśli nie określono klucza konta magazynu. Kontener sygnatury dostępu Współdzielonego dla listę obiektów blob do wyeksportowania zadania eksportu.|  
    |**/ ExportBlobListFile:**|Wymagany. Ścieżka do pliku XML pliku zawierającego listę obiektów blob ścieżek lub obiektu blob prefiksy ścieżki dla obiektów blob do wyeksportowania. Format pliku używany w `BlobListBlobPath` element [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operacji interfejsu API REST usługi Import/Eksport.|  
    |**/ DriveSize:**|Wymagany. Rozmiar należy użyć dla zadania eksportu *np.*, 500 GB, 1,5 TB.|  

    Zobacz [przykład polecenia PreviewExport](#example-of-previewexport-command).
 
5. Sprawdź, czy użytkownik może odczytu/zapisu na dyskach, które zostaną dostarczone dla zadania eksportu.

### <a name="example-of-previewexport-command"></a>Przykład polecenia PreviewExport

W poniższym przykładzie pokazano `PreviewExport` polecenia:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Wyeksportowany plik listy obiektów blob może zawierać nazwy obiektów blob i obiektu blob prefiksy, jak pokazano poniżej:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Narzędzie importu/eksportu Azure zawiera listę wszystkich obiektów blob do wyeksportowania oblicza porady pakietu ich na dyski o określonym rozmiarze, biorąc pod uwagę wszystkie niezbędne czynności, a następnie Szacuje liczbę dysków potrzebne do przechowywania obiektów blob i informacje na temat wykorzystania dysku.  
  
Oto przykład danych wyjściowych z dziennikami informacyjną pominięte:  
  
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

## <a name="examples-of-valid-blob-paths"></a>Przykłady ścieżek nieprawidłowy obiekt blob

W poniższej tabeli przedstawiono przykłady ścieżek nieprawidłowy obiekt blob:
   
   | Selektor | Ścieżka obiektu blob | Opis |
   | --- | --- | --- |
   | Rozpoczyna się od |/ |Eksportuje wszystkie obiekty BLOB na koncie magazynu |
   | Rozpoczyna się od |/$root / |Eksportuje wszystkie obiekty BLOB w kontenerze głównego |
   | Rozpoczyna się od |/Book |Eksportuje wszystkie obiekty BLOB w dowolnym kontenerze, który rozpoczyna się od prefiksu **książki** |
   | Rozpoczyna się od |/Music/ |Eksportuje wszystkie obiekty BLOB w kontenerze **utworów muzycznych** |
   | Rozpoczyna się od |/ music/love |Eksportuje wszystkie obiekty BLOB w kontenerze **utworów muzycznych** się od prefiksu **lubisz** |
   | Równa się |$root/logo.bmp |Eksportowanie obiektów blob **logo.bmp** w kontenerze głównego |
   | Równa się |videos/Story.mp4 |Eksportowanie obiektów blob **story.mp4** w kontenerze **wideo** |

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań importu/eksportu](storage-import-export-requirements.md)


