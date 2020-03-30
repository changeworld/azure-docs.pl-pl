---
title: Używanie importu/eksportowania platformy Azure do przesyłania danych do obiektów blob platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć zadania importowania i eksportowania w witrynie Azure portal, aby przesyłać dane do i z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282498"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Importowanie danych do usługi Azure Blob Storage za pomocą usługi Azure Import/Export service

Ten artykuł zawiera instrukcje krok po kroku dotyczące używania usługi Azure Import/Export do bezpiecznego importowania dużych ilości danych do magazynu obiektów Blob platformy Azure. Aby zaimportować dane do obiektów blob platformy Azure, usługa wymaga wysłania zaszyfrowanych dysków twardych zawierających dane do centrum danych platformy Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importu w celu przeniesienia danych do usługi Azure Blob Storage należy dokładnie przejrzeć i wypełnić następującą listę wymagań wstępnych dla tej usługi.
Musisz:

* Mieć aktywną subskrypcję platformy Azure, która może służyć do importu/eksportu usługi.
* Mieć co najmniej jedno konto usługi Azure Storage z kontenerem magazynu. Zobacz listę [obsługiwanych kont magazynu i typów magazynowania dla usługi Import/Eksport .](storage-import-export-requirements.md)
  * Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [Jak utworzyć konto magazynu](storage-account-create.md).
  * Aby uzyskać informacje na temat kontenera magazynu, przejdź do [temat Tworzenie kontenera magazynu](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Posiadać odpowiednią liczbę dysków [obsługiwanych typów](storage-import-export-requirements.md#supported-disks).
* Mieć system Windows z uruchomiona [w wersji obsługiwanego systemu operacyjnego](storage-import-export-requirements.md#supported-operating-systems).
* Włącz funkcję BitLocker w systemie Windows. Zobacz [Jak włączyć funkcję BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Pobierz najnowszą wersję WAImportExport w wersji 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows. Najnowsza wersja narzędzia zawiera aktualizacje zabezpieczeń umożliwiające zewnętrzną ochronę klucza Funkcje BitLocker i funkcję zaktualizowanego trybu odblokowywania.

  * Rozpaj do folderu `waimportexportv1`domyślnego . Na przykład `C:\WaImportExportV1`.
* Posiadaj konto FedEx/DHL. Jeśli chcesz użyć przewoźnika innego niż FedEx/DHL, skontaktuj `adbops@microsoft.com`się z zespołem operacji usługi Azure Data Box pod adresem .  
  * Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki zwrotnej.
  * Wygeneruj numer śledzenia dla zadania eksportu.
  * Każde zadanie powinno mieć osobny numer śledzenia. Wiele zadań o tym samym numerze śledzenia nie są obsługiwane.
  * Jeśli nie masz konta operatora, przejdź do:
    * [Utwórz konto FedEX](https://www.fedex.com/en-us/create-account.html), lub
    * [Utwórz konto DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Krok 1: Przygotowanie napędów

Ten krok generuje plik dziennika. Plik dziennika przechowuje podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegóły konta magazynu.

Wykonaj następujące kroki, aby przygotować dyski.

1. Podłącz dyski do systemu Windows za pomocą złączy SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie używaj punktów montażowych.
3. Włącz szyfrowanie funkcją BitLocker na woluminie NTFS. Jeśli korzystasz z systemu Windows Server, użyj instrukcji w [jak włączyć funkcję BitLocker w systemie Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Kopiowanie danych do zaszyfrowanego woluminu. Użyj przeciągania i upuszczania lub Robocopy lub takiego narzędzia do kopiowania. Plik dziennika (*jrn*) jest tworzony w tym samym folderze, w którym uruchomi się narzędzie.

   Jeśli dysk jest zablokowany i musisz odblokować dysk, kroki odblokowania mogą się różnić w zależności od przypadku użycia.

   * Jeśli dodano dane do wstępnie zaszyfrowanego dysku (narzędzie WAImportExport nie było używane do szyfrowania), użyj klawisza BitLocker (hasło numeryczne, które określisz) w wyskakującym okienku, aby odblokować dysk.

   * Jeśli dodano dane do dysku zaszyfrowanego przez narzędzie WAImportExport, użyj następującego polecenia, aby odblokować dysk:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Otwórz okno programu PowerShell lub wiersz polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na rozpakowany folder, uruchom następujące polecenie:

    `cd C:\WaImportExportV1`
6. Aby uzyskać klucz funkcji BitLocker dysku, uruchom następujące polecenie:

    `manage-bde -protectors -get <DriveLetter>:`
7. Aby przygotować dysk, uruchom następujące polecenie. **W zależności od rozmiaru danych może to potrwać od kilku godzin do dni.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Plik dziennika jest tworzony w tym samym folderze, w którym uruchomiono narzędzie. Two other files są również tworzone - plik *xml* (folder, w którym uruchomisz narzędzie) i plik *drive-manifest.xml* (folder, w którym znajdują się dane).

    Zastosowane parametry są opisane w poniższej tabeli:

    |Opcja  |Opis  |
    |---------|---------|
    |/j:     |Nazwa pliku dziennika z rozszerzeniem jrn. Plik dziennika jest generowany na dysk. Zaleca się użycie numeru seryjnego dysku jako nazwy pliku dziennika.         |
    |/ Identyfikator:     |Identyfikator sesji. Użyj unikatowego numeru sesji dla każdego wystąpienia polecenia.      |
    |/t:     |Litera dysku, który ma zostać wysłany. Na przykład `D`dysk .         |
    |/bk:     |Klawisz BitLocker dla dysku. Jego numeryczne hasło z wyjścia`manage-bde -protectors -get D:`      |
    |/srcdir:     |Po literze dysku, który ma `:\`zostać wysłany, następuje . Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage.         |
    |/blobtype:     |Ta opcja określa typ obiektów blob, do których mają zostać zaimportować dane. W przypadku bloków obiektów `BlockBlob` blob jest to `PageBlob`i dla obiektów blob strony, jest .         |
    |/skipwrite:     |Opcja określająca, że nie ma żadnych nowych danych wymaganych do skopiowania i należy przygotować istniejące dane na dysku.          |
    |/enablecontentmd5:     |Opcja po włączeniu, zapewnia, że MD5 jest `Content-md5` obliczany i ustawiony jako właściwość na każdym obiekcie blob. Tej opcji należy używać tylko `Content-md5` wtedy, gdy chcesz użyć tego pola po przekazaniu danych na platformę Azure. <br> Ta opcja nie ma wpływu na sprawdzanie integralności danych (które występuje domyślnie). To ustawienie wydłuża czas przekazywania danych do chmury.          |
8. Powtórz poprzedni krok dla każdego dysku, który musi zostać wysłany. Plik dziennika o podanej nazwie jest tworzony dla każdego uruchomienia wiersza polecenia.

    > [!IMPORTANT]
    > * Wraz z plikiem `<Journal file name>_DriveInfo_<Drive serial ID>.xml` dziennika plik jest również tworzony w tym samym folderze, w którym znajduje się narzędzie. Plik xml jest używany zamiast pliku dziennika podczas tworzenia zadania, jeśli plik dziennika jest zbyt duży.

## <a name="step-2-create-an-import-job"></a>Krok 2: Tworzenie zadania importu

Wykonaj następujące kroki, aby utworzyć zadanie importu w witrynie Azure portal.

1. Zaloguj się https://portal.azure.com/do pliku .
2. Przejdź do **pozycję Wszystkie usługi > zadaniami magazynowania > importu/eksportu**.

    ![Przejdź do zadania importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij **pozycję Utwórz zadanie importu/eksportu**.

    ![Kliknij pozycję Utwórz zadanie importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. W **podstawach**:

   * Wybierz **pozycję Importuj na platformę Azure**.
   * Wprowadź opisową nazwę zadania importu. Użyj nazwy, aby śledzić postęp zadań.
       * Nazwa może zawierać tylko małe litery, cyfry i łączniki.
       * Nazwa musi zaczynać się od litery i nie może zawierać spacji.
   * Wybierz subskrypcję.
   * Umożliwia wprowadzenie lub wybranie grupy zasobów.  

     ![Tworzenie zadania importu — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. W **szczegóły zadania**:

   * Przekaż pliki dziennika dysku uzyskane podczas etapu przygotowania dysku. Jeśli `waimportexport.exe version1` użyto, prześlij jeden plik dla każdego przygotowanego dysku. Jeśli rozmiar pliku dziennika przekracza 2 MB, `<Journal file name>_DriveInfo_<Drive serial ID>.xml` można użyć również utworzonego z plikiem dziennika.
   * Wybierz docelowe konto magazynu, na którym będą przebywać dane.
   * Lokalizacja nadajnicza jest wypełniana automatycznie na podstawie regionu wybranego konta magazynu.

   ![Tworzenie zadania importu — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. W **zamian informacje o wysyłce:**

   * Wybierz przewoźnika z listy rozwijanej. Jeśli chcesz użyć przewoźnika innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się `adbops@microsoft.com` z zespołem operacji platformy Azure Data Box, aby uzyskać informacje dotyczące operatora, którego zamierzasz użyć.
   * Wprowadź prawidłowy numer konta przewoźnika utworzony z tym operatorem. Firma Microsoft używa tego konta do wysłania dysków z powrotem do Użytkownika po zakończeniu zadania importowania. Jeśli nie masz numeru konta, utwórz konto [fedex](https://www.fedex.com/us/oadr/) lub [dhl](https://www.dhl.com/) przewoźnika.
   * Podaj pełną i prawidłową nazwę kontaktu, telefon, adres e-mail, adres pocztowy, miasto, zamek błyskawiczny, województwo i kraj/region.

       > [!TIP]
       > Zamiast określać adres e-mail dla pojedynczego użytkownika, podaj grupową wiadomość e-mail. Dzięki temu otrzymasz powiadomienia, nawet jeśli administrator opuści.

     ![Tworzenie zadania importu — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. W **podsumowaniu:**

   * Przejrzyj informacje o stanowisku pracy podane w podsumowaniu. Zanotuj nazwę zadania i adres wysyłki centrum danych platformy Azure, aby wysłać dyski z powrotem na platformę Azure. Te informacje są używane później na etykiecie wysyłkowej.
   * Kliknij **przycisk OK,** aby utworzyć zadanie importu.

     ![Tworzenie zadania importu — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Krok 3 (opcjonalnie): Konfigurowanie klucza zarządzanego przez klienta

Pomiń ten krok i przejdź do następnego kroku, jeśli chcesz użyć klucza zarządzanego firmy Microsoft do ochrony kluczy funkcji BitLocker dla dysków. Aby skonfigurować własny klucz w celu ochrony klucza funkcji BitLocker, postępuj zgodnie z instrukcjami w [temacie Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault for Azure Import/Export w witrynie Azure portal](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Krok 4: Wyślij napędy

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Krok 5: Aktualizowanie zadania informacjami o śledzeniu

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Krok 6: Weryfikowanie przekazywania danych na platformę Azure

Śledzenie zadania do zakończenia. Po zakończeniu zadania sprawdź, czy dane zostały przekazane na platformę Azure. Usuń dane lokalne dopiero po zweryfikowaniu, że przekazywanie zakończyło się pomyślnie.

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie stanowiska i stanu dysku](storage-import-export-view-drive-status.md)
* [Przejrzyj wymagania dotyczące importu/eksportu](storage-import-export-requirements.md)
