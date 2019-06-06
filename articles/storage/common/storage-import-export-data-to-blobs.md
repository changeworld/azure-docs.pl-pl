---
title: Przesyłanie danych do obiektów blob platformy Azure przy użyciu usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Importuj i Eksportuj zadania w witrynie Azure portal na przesyłanie danych do i z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/31/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 68f62a6945f3b651781414e3194104b6d2e6295c
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455814"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usługa Azure Import/Export umożliwia importowanie danych do usługi Azure Blob Storage

Ten artykuł zawiera instrukcje krok po kroku dotyczące sposobu bezpiecznego importowania dużych ilości danych do usługi Azure Blob storage przy użyciu usługi Azure Import/Export. Aby zaimportować dane do obiektów blob platformy Azure, usługa wymaga dostarczanie zaszyfrowanych dysków zawierający dane do centrum danych platformy Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do tworzenia zadania importu do przenoszenia danych do usługi Azure Blob Storage, należy dokładnie przejrzyj i ukończ Poniższa lista wymagań wstępnych dla tej usługi. Musisz mieć:

- Mieć aktywną subskrypcją platformy Azure, który może służyć do usługi Import/Export.
- Ma co najmniej jedno konto usługi Azure Storage z kontenera magazynu. Przejrzyj listę rzeczy, [obsługiwanych kont magazynu i typów magazynu dla usługi Import/Export](storage-import-export-requirements.md). 
    - Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [sposób tworzenia konta magazynu](storage-quickstart-create-account.md). 
    - Aby uzyskać informacji na temat kontenera magazynu, przejdź do [utworzyć kontenera magazynu](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Mieć odpowiednią liczbę dysków [obsługiwane typy](storage-import-export-requirements.md#supported-disks). 
- System Windows z systemem [obsługiwany system operacyjny w wersji](storage-import-export-requirements.md#supported-operating-systems). 
- Włącz funkcję BitLocker w systemie Windows. Zobacz [jak włączyć funkcję BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Pobierz WAImportExport wersji 1](https://aka.ms/waiev1) w systemie Windows. Rozpakuj go do domyślnego folderu `waimportexportv1`. Na przykład `C:\WaImportExportV1`.
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

1.  Do systemu Windows za pomocą łączników SATA, należy połączyć z stacje dysków.
1.  Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie należy używać punkty instalacji.
2.  Włącz szyfrowanie funkcją BitLocker na woluminie NTFS. Jeśli system Windows Server, postępuj zgodnie z instrukcjami w [jak włączyć funkcję BitLocker w systemie Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopiuj dane do woluminu wykorzystującego. Użyj przeciągania i upuszczania lub Robocopy lub dowolnego narzędzia kopiowania.
4.  Otwórz okno programu PowerShell lub wierszu polecenia z uprawnieniami administracyjnymi. Zmień katalog na folder rozpakowany, uruchom następujące polecenie:
    
    `cd C:\WaImportExportV1`
5.  Aby uzyskać klucz funkcji BitLocker na dysku, uruchom następujące polecenie:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Aby przygotować się na dysku, uruchom następujące polecenie. **W zależności od rozmiaru danych to może potrwać kilka godzin, dni.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite /enablecontentmd5 
    ```
    Plik dziennika jest tworzony w tym samym folderze, na którym uruchomiono narzędzie. Dwa inne pliki są również tworzone - *.xml* pliku (folder, w którym uruchamiasz narzędzie) i *manifest.xml dysku* pliku (gdzie znajdują się dane folder).
    
    Parametry używane są opisane w poniższej tabeli:

    |Opcja  |Opis  |
    |---------|---------|
    |/j:     |Nazwa pliku dziennika z rozszerzeniem jrn. Generowany jest plik dziennika na dysku. Zalecamy użycie numer seryjny dysku jako nazwa pliku dziennika.         |
    |/id:     |Identyfikator sesji. Użyj numeru sesji unikatowy dla poszczególnych wystąpień tego polecenia.      |
    |/t:     |Literę dysku, który ma zostać wysłane. Na przykład dysk `D`.         |
    |/bk:     |Klucz funkcji BitLocker dla dysku. Jego hasło numeryczne z danych wyjściowych `manage-bde -protectors -get D:`      |
    |/srcdir:     |Następuje literę dysku, który ma zostać wysłane `:\`. Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage.         |
    |/blobtype:     |Ta opcja określa typ obiektów blob, który chcesz zaimportować dane. W przypadku blokowych obiektów blob jest `BlockBlob` i stronicowe obiekty BLOB, jest `PagaBlob`.         |
    |/skipwrite:     |Opcja, która określa, że nie istnieje żadne nowe dane wymagane do skopiowania i istniejące dane na dysku jest przygotowany.          |
    |/enablecontentmd5:     |Po włączeniu opcji gwarantuje, że MD5 jest obliczany w trakcie przekazywania blokowych obiektów blob na platformie Azure.          |
7. Powtórz poprzedni krok dla każdego dysku, który ma zostać wysłane. Plik dziennika o podanej nazwie jest tworzony dla każdego uruchomienia wiersza polecenia.
    
    > [!IMPORTANT]
    > - Wraz z pliku dziennika `<Journal file name>_DriveInfo_<Drive serial ID>.xml` plik również jest tworzony w tym samym folderze, w którym znajduje się narzędzie. Plik XML jest używany zamiast pliku dziennika, podczas tworzenia zadania, jeśli plik dziennika jest zbyt duży. 

## <a name="step-2-create-an-import-job"></a>Krok 2: Tworzenie zadania importu

Wykonaj poniższe kroki, aby utworzyć zadanie importu w witrynie Azure portal.

1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do **wszystkie usługi > Magazyn > zadania importu/eksportu**. 
    
    ![Przejdź do zadania importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij przycisk **Tworzenie zadania importu/eksportu**.

    ![Kliknij zadanie Utwórz importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. W **podstawy**:

   - Wybierz **Importuj na platformę Azure**.
   - Wprowadź opisową nazwę zadania importowania. Użyj nazwy w celu śledzenia postępu zadań.
       - Nazwa może zawierać tylko małe litery, cyfry i łączniki.
       - Nazwa musi zaczynać się literą i nie może zawierać spacji.
   - Wybierz subskrypcję.
   - Wprowadź lub wybierz grupę zasobów.  

     ![Tworzenie zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. W **szczegóły zadania**:

    - Przekazywanie plików dziennika dysków, które uzyskany w kroku przygotowania dysku. Jeśli `waimportexport.exe version1` był używany, przekazać jeden plik dla każdego dysku, który został przygotowany. Jeśli rozmiar pliku dziennika przekracza 2 MB, a następnie można użyć `<Journal file name>_DriveInfo_<Drive serial ID>.xml` również tworzone przy użyciu pliku dziennika. 
    - Wybierz docelowe konto magazynu, w którym dane zostaną umieszczone. 
    - Lokalizacja dropoff jest automatycznie wypełniane na podstawie w regionie wybranym koncie magazynu.
   
   ![Tworzenie zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. W **zwracają informacje o wysyłce**:

   - Wybierz nośnik, z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL w sprawie, wybierz opcję istniejący, z listy rozwijanej. Skontaktuj się z pomocą operacje pole danych platformy Azure zespołu w `adbops@microsoft.com` informacji na temat operatora planujesz używać.
   - Wprowadź numer konta operatora prawidłowe, utworzony za pomocą tego operatora. Firma Microsoft używa tego konta do wysłania dysków do Ciebie, po zakończeniu zadania importu. Jeśli nie masz numeru konta, Utwórz [FedEx](https://www.fedex.com/us/oadr/) lub [przez firmę DHL](https://www.dhl.com/) konto przewoźnika.
   - Podaj kompletne i prawidłowe nazwisko osoby kontaktowej, telefonicznej, wiadomości e-mail, adres, Miasto, zip, stan/prowincję/Województwo i kraj/region. 
        
       > [!TIP] 
       > Zamiast określania adresu e-mail dla pojedynczego użytkownika, należy podać adres e-mail grupy. Dzięki temu otrzymywać powiadomienia, nawet jeśli opuści administrator.

     ![Tworzenie zadania importu — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. W **Podsumowanie**:

   - Przejrzyj informacje o zadaniu, podane w informacjach. Zanotuj nazwę zadania i centrum danych platformy Azure, wysyłania adres odeślij dyski do platformy Azure. Te informacje są używane w dalszej części na etykietę wysyłkową.
   - Kliknij przycisk **OK** do utworzenia zadania importu.

     ![Tworzenie zadania importu — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Krok 3: Dostarczaj dyski 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizacja zadania przy użyciu informacji o śledzeniu

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5. Weryfikowanie przekazania danych na platformę Azure

Śledź zadania do zakończenia. Po zakończeniu zadania Sprawdź, czy danych został przekazany na platformę Azure. Usuń lokalne dane tylko w przypadku, gdy będziesz mieć pewność, że przekazywanie powiodło się.

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań importu/eksportu](storage-import-export-requirements.md)


