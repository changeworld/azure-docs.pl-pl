---
title: Transferowanie danych do obiektów blob platformy Azure za pomocą usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania importu i eksportu w Azure Portal, aby przesyłać dane do i z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: bd15e406cdbee57112ff8ecba158d503e908b73f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73178017"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Importowanie danych do platformy Azure Blob Storage przy użyciu usługi Azure Import/Export

Ten artykuł zawiera instrukcje krok po kroku dotyczące korzystania z usługi Azure Import/Export do bezpiecznego importowania dużych ilości danych do usługi Azure Blob Storage. Aby zaimportować dane do obiektów blob platformy Azure, usługa wymaga dostarczania szyfrowanych dysków zawierających dane do centrum danych platformy Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importowania w celu przesyłania danych do usługi Azure Blob Storage należy uważnie przejrzeć i wykonać poniższą listę wymagań wstępnych dla tej usługi. Należy:

- Mieć aktywną subskrypcję platformy Azure, która może być używana w usłudze Import/Export.
- Mieć co najmniej jedno konto usługi Azure Storage z kontenerem magazynu. Zapoznaj się z listą [obsługiwanych kont magazynu i typów magazynów dla usługi Import/Export](storage-import-export-requirements.md). 
    - Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-quickstart-create-account.md). 
    - Aby uzyskać informacje na temat kontenera magazynu, przejdź do obszaru [Tworzenie kontenera magazynu](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Ma wystarczającą liczbę dysków [obsługiwanych typów](storage-import-export-requirements.md#supported-disks). 
- System Windows z uruchomioną [obsługiwaną wersją systemu operacyjnego](storage-import-export-requirements.md#supported-operating-systems). 
- Włącz funkcję BitLocker w systemie Windows. Zobacz [jak włączyć funkcję BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Pobierz wersję WAImportExport 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows. Rozpakuj do domyślnego folderu `waimportexportv1`. Na przykład `C:\WaImportExportV1`.
- Mieć konto FedEx/DHL. Jeśli chcesz użyć operatora innego niż FedEx/DHL, skontaktuj się z zespołem operacyjnym Azure Data Box w `adbops@microsoft.com`.  
    - Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki zwrotnej.
    - Generuj numer śledzenia dla zadania eksportu.
    - Każde zadanie powinno mieć oddzielny numer śledzenia. Wiele zadań o tym samym numerze śledzenia nie są obsługiwane.
    - Jeśli nie masz konta nośnego, przejdź do:
        - [Utwórz konto FedEx](https://www.fedex.com/en-us/create-account.html)lub 
        - [Utwórz konto DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Krok 1. Przygotowanie dysków

Ten krok powoduje wygenerowanie pliku dziennika. W pliku dziennika są przechowywane podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegóły konta magazynu. 

Wykonaj poniższe kroki, aby przygotować dyski.

1.  Podłącz stacje dysków do systemu Windows za pomocą łączników SATA.
1.  Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie należy używać mountpoints.
2.  Włącz szyfrowanie funkcją BitLocker na woluminie NTFS. W przypadku korzystania z systemu Windows Server wykonaj instrukcje podane w temacie [jak włączyć funkcję BitLocker w systemie Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopiuj dane do woluminu szyfrowanego. Użyj przeciągania i upuszczania lub Robocopy lub dowolnego takiego narzędzia do kopiowania.
4.  Otwórz okno programu PowerShell lub wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na folder niespakowany, uruchom następujące polecenie:
    
    `cd C:\WaImportExportV1`
5.  Aby uzyskać klucz funkcji BitLocker dysku, uruchom następujące polecenie:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Aby przygotować dysk, uruchom następujące polecenie. **W zależności od rozmiaru danych może to potrwać kilka godzin.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite 
    ```
    Plik dziennika jest tworzony w tym samym folderze, w którym uruchomiono narzędzie. Tworzone są również dwa inne pliki — plik *XML* (folder, w którym jest uruchamiane narzędzie) i plik *Drive-manifest. XML* (folder, w którym znajdują się dane).
    
    Użyte parametry są opisane w poniższej tabeli:

    |Opcja  |Opis  |
    |---------|---------|
    |/j     |Nazwa pliku dziennika z rozszerzeniem JRN. Plik dziennika jest generowany na dysku. Zalecamy użycie numeru seryjnego dysku jako nazwy pliku dziennika.         |
    |/ID     |Identyfikator sesji. Użyj unikatowego numeru sesji dla każdego wystąpienia polecenia.      |
    |/t     |Litera dysku do wysłania. Na przykład dysk `D`.         |
    |/bk:     |Klucz funkcji BitLocker dla dysku. Hasło liczbowe z danych wyjściowych `manage-bde -protectors -get D:`      |
    |/srcdir:     |Litera dysku do wysłania, po której następuje `:\`. Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage.         |
    |/blobtype:     |Ta opcja określa typ obiektów blob, do których mają zostać zaimportowane dane. W przypadku blokowych obiektów BLOB jest to `BlockBlob` i dla stronicowych obiektów BLOB jest `PagaBlob`.         |
    |/skipwrite:     |Opcja, która określa, że nie są wymagane żadne nowe dane do skopiowania, a istniejące dane na dysku muszą zostać przygotowane.          |
    |/enablecontentmd5:     |Opcja po włączeniu zapewnia, że MD5 jest obliczany i ustawiany jako właściwość `Content-md5` dla każdego obiektu BLOB. Użyj tej opcji tylko wtedy, gdy chcesz użyć pola `Content-md5` po przekazaniu danych na platformę Azure. <br> Ta opcja nie ma wpływu na sprawdzanie integralności danych (domyślnie występuje). Ustawienie to zwiększa czas przekazywania danych do chmury.          |
7. Powtórz poprzedni krok dla każdego dysku, który należy dostarczyć. Plik dziennika o podanej nazwie jest tworzony dla każdego przebiegu wiersza polecenia.
    
    > [!IMPORTANT]
    > - Wraz z plikiem dziennika tworzony jest również plik `<Journal file name>_DriveInfo_<Drive serial ID>.xml` w tym samym folderze, w którym znajduje się narzędzie. Plik. XML jest używany zamiast pliku dziennika podczas tworzenia zadania, jeśli plik dziennika jest zbyt duży. 

## <a name="step-2-create-an-import-job"></a>Krok 2. Tworzenie zadania importu

Wykonaj następujące kroki, aby utworzyć zadanie importowania w Azure Portal.

1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do obszaru **wszystkie usługi > magazyn > zadania importowania/eksportowania**. 
    
    ![Przejdź do zadań importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij pozycję **Utwórz zadanie importu/eksportu**.

    ![Kliknij pozycję Utwórz zadanie importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **Podstawowe informacje**:

   - Wybierz pozycję **Importuj na platformie Azure**.
   - Wprowadź opisową nazwę zadania importu. Użyj nazwy, aby śledzić postęp zadań.
       - Nazwa może zawierać tylko małe litery, cyfry i łączniki.
       - Nazwa musi rozpoczynać się od litery i nie może zawierać spacji.
   - Wybierz subskrypcję.
   - Wprowadź lub wybierz grupę zasobów.  

     ![Tworzenie zadania importowania — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. W **szczegółach zadania**:

    - Przekaż pliki dziennika stacji, które zostały uzyskane podczas kroku przygotowywania dysku. Jeśli użyto `waimportexport.exe version1`, Przekaż jeden plik dla każdego przygotowanego dysku. Jeśli rozmiar pliku dziennika przekracza 2 MB, można użyć `<Journal file name>_DriveInfo_<Drive serial ID>.xml` również utworzonego przy użyciu pliku dziennika. 
    - Wybierz docelowe konto magazynu, na którym będą przechowywane dane. 
    - Lokalizacja Dropoff jest automatycznie wypełniana na podstawie regionu wybranego konta magazynu.
   
   ![Tworzenie zadania importowania — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. W oknie **Informacje o wysyłce zwrotu**:

   - Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się z zespołem ds. operacyjnych Azure Data Box w `adbops@microsoft.com` z informacjami dotyczącymi przewoźnika, którego zamierzasz używać.
   - Wprowadź prawidłowy numer konta nośnego, który został utworzony za pomocą tego operatora. Firma Microsoft korzysta z tego konta, aby po zakończeniu zadania importowania dostarczać dyski z powrotem do użytkownika. Jeśli nie masz numeru konta, Utwórz konto z systemem [FedEx](https://www.fedex.com/us/oadr/) lub [DHL](https://www.dhl.com/) .
   - Podaj pełną i poprawną nazwę kontaktu, numer telefonu, adres e-mail, ulica, miasto, kod pocztowy, Województwo i kraj/region. 
        
       > [!TIP] 
       > Zamiast określania adresu e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet w przypadku opuszczenia przez administratora.

     ![Tworzenie zadania importowania — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. **Podsumowanie**:

   - Przejrzyj informacje o zadaniu podane w podsumowaniu. Zanotuj nazwę zadania i adres wysyłkowy centrum danych platformy Azure, aby dostarczać dyski z powrotem do platformy Azure. Te informacje są używane później na etykiecie wysyłkowej.
   - Kliknij przycisk **OK** , aby utworzyć zadanie importowania.

     ![Tworzenie zadania importowania — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Krok 3. dostarczenie dysków 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4. aktualizowanie zadania przy użyciu informacji śledzenia

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5. Weryfikacja przekazywania danych na platformę Azure

Śledź zadanie do ukończenia. Po zakończeniu zadania Sprawdź, czy dane zostały przekazane do platformy Azure. Usuń dane lokalne dopiero po sprawdzeniu, że przekazywanie zakończyło się pomyślnie.

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przejrzyj wymagania dotyczące importu/eksportu](storage-import-export-requirements.md)


