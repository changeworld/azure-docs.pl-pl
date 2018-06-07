---
title: Przy użyciu Import/Eksport Azure na przesyłanie danych do obiektów blob Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia importowania i eksportowania zadania w portalu Azure na przesyłanie danych do i z obiektów blob Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: fe9292459134972b44037a58235cdd817030a956
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660873"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Importuj dane do magazynu obiektów Blob Azure przy użyciu usługi Import/Eksport Azure

Ten artykuł zawiera instrukcje krok po kroku dotyczące sposobu używania usługi Import/Eksport Azure można bezpiecznie zaimportować dużych ilości danych do magazynu obiektów Blob platformy Azure. Do importowania danych do obiektów blob Azure, usługa wymaga do wysłania zaszyfrowane dyski zawierające dane do centrum danych Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importu na przesyłanie danych do magazynu obiektów Blob Azure uważnie Przejrzyj i ukończ poniższą listę wymagań wstępnych dla tej usługi. Należy:

- Mieć aktywną subskrypcją platformy Azure, który może służyć do usługi Import/Eksport.
- Ma co najmniej jedno konto magazynu Azure z kontenera magazynu. Lista [obsługiwany konta magazynu i typów magazynu dla usługi Import/Eksport](storage-import-export-requirements.md). Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-create-storage-account.md#create-a-storage-account). Aby uzyskać informacji na temat kontenera magazynu, przejdź do [utworzyć kontenera magazynu](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Ma odpowiedniej liczby dysków [obsługiwane typy](storage-import-export-requirements.md#supported-disks). 
- System Windows z systemem [obsługiwany system operacyjny w wersji](storage-import-export-requirements.md#supported-operating-systems). 
- Włącz funkcję BitLocker w systemie Windows. Zobacz [jak włączyć funkcję BitLocker](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Pobierz WAImportExport wersji 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) w systemie Windows. Rozpakuj w domyślnym folderze `waimportexportv1`. Na przykład `C:\WaImportExportV1`.


## <a name="step-1-prepare-the-drives"></a>Krok 1: Przygotowanie dysków

Ten krok generuje plik dziennika. Plik dziennika zawiera podstawowe informacje, takie jak dysk numer seryjny, klucz szyfrowania i szczegóły konta magazynu. 

Wykonaj poniższe kroki, aby przygotować dyski.

1.  Połącz dysków twardych w systemie Windows za pomocą łączników SATA.
1.  Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie należy używać punkty instalacji.
2.  Włącz szyfrowanie funkcją BitLocker w woluminie NTFS. Jeśli przy użyciu systemu Windows Server, skorzystaj z instrukcji w [Włączanie funkcji BitLocker w systemie Windows Server 2012 R2](http://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopiowanie danych do woluminu zaszyfrowane. Użyj przeciągania i upuszczania lub Robocopy lub dowolnego narzędzia kopiowania.
4.  Otwórz okno programu PowerShell lub wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog, aby rozpakować folderu, uruchom następujące polecenie:
    
    `cd C:\WaImportExportV1`
5.  Aby uzyskać klucza funkcji BitLocker na dysku, uruchom następujące polecenie:
    
    ` manage-bde -protectors -get <DriveLetter>: `
6.  Aby przygotować się na dysku, uruchom następujące polecenie. **W zależności od rozmiaru danych może to potrwać kilka godzin do dni.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    Plik dziennika jest tworzony w tym samym folderze, w którym są uruchomione narzędzie. Dwa inne pliki są również tworzone - *.xml* pliku (gdzie należy uruchomić narzędzie folder) i *manifest.xml dysku* pliku (gdzie znajdują się dane folderze).
    
    Parametry używane są opisane w poniższej tabeli:

    |Opcja  |Opis  |
    |---------|---------|
    |/j:     |Nazwa pliku dziennika z rozszerzeniem .jrn. Plik dziennika jest generowany na dysk. Zalecane jest użycie numeru seryjnego dysku jako nazwa pliku dziennika.         |
    |/ Identyfikator:     |Identyfikator sesji. Używa wielu sesji unikatowy dla każdego wystąpienia polecenia.      |
    |/SK:     |Klucz konta magazynu Azure.         |
    |/ t:     |Litera dysku do wysłania. Na przykład dysk `D`.         |
    |/bk:     |Klucza funkcji BitLocker dla dysku. Jego numerycznym hasłem z danych wyjściowych ` manage-bde -protectors -get D: `      |
    |/srcdir:     |Litera dysku do wysłania następuje `:\`. Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage.         |
    |/skipwrite:     |Opcję określającą, że nie istnieje żadne nowe dane wymagane do skopiowania i istniejące dane na dysku jest przygotowany.          |
7. Powtórz poprzedni krok dla każdego dysku, który ma zostać wysłane. W pliku dziennika o podanej nazwie jest tworzony dla każdego uruchomienia wiersza polecenia.
    
    > [!IMPORTANT]
    > - Wraz z pliku dziennika `<Journal file name>_DriveInfo_<Drive serial ID>.xml` plików tworzona jest również w tym samym folderze, w którym znajduje się narzędzie. Plik XML jest używany zamiast pliku dziennika, podczas tworzenia zadania, jeśli plik dziennika jest za duży. 

## <a name="step-2-create-an-import-job"></a>Krok 2: Tworzenie zadania importu

Wykonaj poniższe kroki, aby utworzyć zadanie importu w portalu Azure.

1. Zaloguj się do https://portal.azure.com/.
2. Przejdź do **wszystkie usługi > magazynu > zadania importu/eksportu**. 
    
    ![Przejdź do zadania importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij przycisk **zadania importu/eksportu Utwórz**.

    ![Kliknij zadanie Utwórz Import/Eksport](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. W **podstawy**:

    - Wybierz **importu na platformie Azure**.
    - Wprowadź nazwę opisową dla zadania importu. Użyj nazwy, aby śledzić postęp zadań.
        - Nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        - Nazwa musi zaczynać się literą i nie może zawierać spacji.
    - Wybierz subskrypcję.
    - Wprowadź lub wybierz grupę zasobów.  

    ![Utwórz zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. W **szczegóły zadania**:

    - Przekazywanie plików dziennika dysków, które uzyskane w kroku przygotowania dysku. Jeśli `waimportexport.exe version1` został użyty, Przekaż jeden plik dla każdego dysku, które zostały przygotowane. Jeśli rozmiar pliku dziennika przekracza 2 MB, a następnie można użyć `<Journal file name>_DriveInfo_<Drive serial ID>.xml` również utworzony za pomocą pliku dziennika. 
    - Wybierz konto magazynu docelowego, na którym znajdują się dane. 
    - Lokalizacja przyjmowania jest wypełniane automatycznie na podstawie regionu wybrane konta magazynu.
   
   ![Utwórz zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. W **zwraca informacje o wysyłki**:

    - Wybierz nośnik, z listy rozwijanej.
    - Wprowadź numer konta nieprawidłowy operator, które zostały utworzone z tego nośnika. Firma Microsoft używa tego konta na potrzeby wysłania dysków powrotem po zakończeniu zadania importu. Jeśli nie masz numeru konta, Utwórz [FedEx](http://www.fedex.com/us/oadr/) lub [przez firmę DHL](http://www.dhl.com/) operator konta.
    - Podaj nazwisko osoby kontaktowej w pełne i prawidłowe, telefonu, poczty e-mail, adres, Miasto, zip, Województwo i kraj/region.

    ![Utwórz zadania importu — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. W **Podsumowanie**:

    - Przejrzyj informacje o zadaniu w podsumowaniu. Zanotuj nazwę zadania i centrum danych Azure adresu na potrzeby wysłania Azure dysków wysyłki. Te informacje są używane później na etykiecie wysyłki.
    - Kliknij przycisk **OK** można utworzyć zadania importu.

    ![Utwórz zadania importu — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

## <a name="step-3-ship-the-drives"></a>Krok 3: Wyślij dysków 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: Aktualizowanie zadania ze śledzeniem informacji

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="next-steps"></a>Kolejne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przegląd wymagań importu/eksportu](storage-import-export-requirements.md)


