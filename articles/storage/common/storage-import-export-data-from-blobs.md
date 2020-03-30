---
title: Używanie narzędzia Azure Import/Export do eksportowania danych z obiektów blob platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć zadania eksportu w witrynie Azure portal, aby przesyłać dane z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a5afa6439caa6b7c1572447e3b212f3357bf296a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282515"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Eksportowanie danych z usługi Azure Blob Storage za pomocą usługi Azure Import/Export

Ten artykuł zawiera instrukcje krok po kroku dotyczące używania usługi Azure Import/Export do bezpiecznego eksportowania dużych ilości danych z magazynu obiektów Blob platformy Azure. Usługa wymaga wysłania pustych dysków do centrum danych platformy Azure. Usługa eksportuje dane z konta magazynu do dysków, a następnie wysyła dyski z powrotem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania eksportu w celu przeniesienia danych z usługi Azure Blob Storage należy dokładnie przejrzeć i wypełnić następującą listę wymagań wstępnych dla tej usługi.
Musisz:

- Mieć aktywną subskrypcję platformy Azure, która może służyć do importu/eksportu usługi.
- Mieć co najmniej jedno konto usługi Azure Storage. Zobacz listę [obsługiwanych kont magazynu i typów magazynowania dla usługi Import/Eksport .](storage-import-export-requirements.md) Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [Jak utworzyć konto magazynu](storage-account-create.md).
- Posiadać odpowiednią liczbę dysków [obsługiwanych typów](storage-import-export-requirements.md#supported-disks).
- Posiadaj konto FedEx/DHL. Jeśli chcesz użyć przewoźnika innego niż FedEx/DHL, skontaktuj `adbops@microsoft.com`się z zespołem operacji usługi Azure Data Box pod adresem .
  - Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki zwrotnej.
  - Wygeneruj numer śledzenia dla zadania eksportu.
  - Każde zadanie powinno mieć osobny numer śledzenia. Wiele zadań o tym samym numerze śledzenia nie są obsługiwane.
  - Jeśli nie masz konta operatora, przejdź do:
    - [Utwórz konto FedEX](https://www.fedex.com/en-us/create-account.html), lub
    - [Utwórz konto DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Krok 1: Tworzenie zadania eksportu

Wykonaj następujące kroki, aby utworzyć zadanie eksportu w witrynie Azure portal.

1. Zaloguj się https://portal.azure.com/do pliku .
2. Przejdź do **pozycję Wszystkie usługi > zadaniami magazynowania > importu/eksportu**.

    ![Przejdź do zadania importu/eksportu](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Kliknij **pozycję Utwórz zadanie importu/eksportu**.

    ![Kliknij pozycję Importuj/eksportuj zadanie](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. W **podstawach**:

    - Wybierz **pozycję Eksportuj z platformy Azure**.
    - Wprowadź opisową nazwę zadania eksportu. Użyj nazwy, którą wybierzesz, aby śledzić postęp zadań.
        - Nazwa może zawierać tylko małe litery, cyfry, łączniki i podkreślenia.
        - Nazwa musi zaczynać się od litery i nie może zawierać spacji.
    - Wybierz subskrypcję.
    - Umożliwia wprowadzenie lub wybranie grupy zasobów.

        ![Podstawy](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. W **szczegóły zadania**:

    - Wybierz konto magazynu, na którym znajdują się dane, które mają być eksportowane. Użyj konta magazynu w pobliżu miejsca, w którym się znajdujesz.
    - Lokalizacja nadajnicza jest wypełniana automatycznie na podstawie regionu wybranego konta magazynu.
    - Określ dane obiektu blob, które mają być eksportowane z konta magazynu na pusty dysk lub dyski.
    - Wybierz opcję **Eksportuj wszystkie** dane obiektów blob na koncie magazynu.

         ![Wyeksportuj wszystkie](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - Można określić, które kontenery i obiekty blob mają być eksportowane.
        - **Aby określić obiekt blob do wyeksportowania:** Użyj selektora **Równe do.** Określ ścieżkę względną do obiektu blob, począwszy od nazwy kontenera. Użyj *$root,* aby określić kontener główny.
        - **Aby określić wszystkie obiekty blob zaczynające się od prefiksu:** Użyj **selektora Rozpoczyna się** od. Określ prefiks, zaczynając od ukośnika do przodu '/'. Prefiks może być prefiksem nazwy kontenera, pełnej nazwy kontenera lub pełnej nazwy kontenera, po której następuje prefiks nazwy obiektu blob. Należy podać ścieżki obiektów blob w prawidłowym formacie, aby uniknąć błędów podczas przetwarzania, jak pokazano na tym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [Przykłady prawidłowych ścieżek obiektów blob](#examples-of-valid-blob-paths).

           ![Eksportowanie wybranych kontenerów i obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - Można wyeksportować z pliku listy obiektów blob.

        ![Eksportowanie z pliku listy obiektów blob](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Jeśli obiekt blob do wyeksportowania jest używany podczas kopiowania danych, usługa Azure Import/Export pobiera migawkę obiektu blob i kopiuje migawkę.

6. W **zamian informacje o wysyłce:**

    - Wybierz przewoźnika z listy rozwijanej. Jeśli chcesz użyć przewoźnika innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się `adbops@microsoft.com` z zespołem operacji platformy Azure Data Box, aby uzyskać informacje dotyczące operatora, którego zamierzasz użyć.
    - Wprowadź prawidłowy numer konta przewoźnika utworzony z tym operatorem. Firma Microsoft używa tego konta do wysłania dysków z powrotem do Użytkownika po zakończeniu zadania eksportu.
    - Podaj pełną i prawidłową nazwę kontaktu, telefon, adres e-mail, adres pocztowy, miasto, zamek błyskawiczny, województwo i kraj/region.

        > [!TIP]
        > Zamiast określać adres e-mail dla pojedynczego użytkownika, podaj grupową wiadomość e-mail. Dzięki temu otrzymasz powiadomienia, nawet jeśli administrator opuści.

7. W **podsumowaniu**:

    - Przejrzyj szczegóły zadania.
    - Zanotuj nazwę zadania i podaj adres wysyłki centrum danych platformy Azure dla dysków wysyłkowych na platformę Azure.

        > [!NOTE]
        > Zawsze wysyłaj dyski do centrum danych odnotowanego w witrynie Azure portal. Jeśli dyski są wysyłane do niewłaściwego centrum danych, zadanie nie zostanie przetworzone.

    - Kliknij **przycisk OK,** aby zakończyć tworzenie miejsc pracy eksportu.

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Krok 2: Wyślij napędy

Jeśli nie znasz potrzebnej liczby dysków, przejdź do strony [Sprawdź liczbę dysków](#check-the-number-of-drives). Jeśli znasz liczbę dysków, przystępuj do wysyłki dysków.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Krok 3: Aktualizowanie zadania za pomocą informacji o śledzeniu

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Krok 4: Odbieranie dysków

Gdy pulpit nawigacyjny zgłasza, że zadanie zostało ukończone, dyski są wysyłane do Ciebie, a numer śledzenia przesyłki jest dostępny w portalu.

1. Po otrzymaniu dysków z wyeksportowanymi danymi musisz uzyskać klucze funkcji BitLocker, aby odblokować dyski. Przejdź do zadania eksportu w witrynie Azure portal. Kliknij kartę **Importuj/Eksportuj.**
2. Wybierz i kliknij zadanie eksportu z listy. Przejdź do **funkcji Szyfrowanie** i skopiuj klucze.

   ![Wyświetlanie klawiszy funkcji BitLocker do zadania eksportu](./media/storage-import-export-service/export-job-bitlocker-keys-02.png)

3. Użyj klawiszy Funkcji BitLocker, aby odblokować dyski.

Eksport został zakończony.

## <a name="step-5-unlock-the-disks"></a>Krok 5: Odblokuj dyski

Jeśli używasz wersji 1.4.0.300 narzędzia WAImportExport, użyj następującego polecenia, aby odblokować dysk:

    `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`  

Jeśli używasz wcześniejszych wersji narzędzia, użyj okna dialogowego BitLocker, aby odblokować dysk.

W tej chwili możesz usunąć zadanie lub je opuścić. Zadania są automatycznie usuwane po 90 dniach.

## <a name="check-the-number-of-drives"></a>Sprawdź liczbę dysków

Ten *opcjonalny* krok pomaga określić liczbę dysków wymaganych dla zadania eksportu. Wykonaj ten krok w systemie Windows z [uruchomiona w wersji obsługiwanego systemu operacyjnego](storage-import-export-requirements.md#supported-operating-systems).

1. [Pobierz WAImportExport w wersji 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows.
2. Rozpaj do folderu `waimportexportv1`domyślnego . Na przykład `C:\WaImportExportV1`.
3. Otwórz okno programu PowerShell lub wiersz polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na rozpakowany folder, uruchom następujące polecenie:

    `cd C:\WaImportExportV1`

4. Aby sprawdzić liczbę dysków wymaganych dla wybranych obiektów blob, uruchom następujące polecenie:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametry są opisane w poniższej tabeli:

    |Parametr wiersza polecenia|Opis|  
    |--------------------------|-----------------|  
    |**/logdir:**|Element opcjonalny. Katalog dziennika. Pełne pliki dziennika są zapisywane w tym katalogu. Jeśli nie zostanie określony, bieżący katalog jest używany jako katalog dziennika.|  
    |**/sn:**|Wymagany. Nazwa konta magazynu dla zadania eksportu.|  
    |**/sk:**|Wymagane tylko wtedy, gdy nie określono sygnatury dostępu Współdzielonego kontenera. Klucz konta magazynu dla zadania eksportu.|  
    |**/csas:**|Wymagane tylko wtedy, gdy nie określono klucza konta magazynu. Sygnatura dostępu Współdzielonego kontenera do wyświetlania listy obiektów blob, które mają zostać wyeksportowane w zadaniu eksportu.|  
    |**/ExportBlobListFile:**|Wymagany. Ścieżka do pliku XML zawierającego listę ścieżek obiektów blob lub prefiksów ścieżki obiektów blob dla obiektów blob, które mają zostać wyeksportowane. Format pliku używany `BlobListBlobPath` w elemencie w operacji [Umieść zadanie](/rest/api/storageimportexport/jobs) interfejsu API REST usługi importu/eksportu.|  
    |**/DriveSize:**|Wymagany. Rozmiar dysków do użycia do zadania eksportu, *np.*|  

    Zobacz [przykład polecenia PreviewExport](#example-of-previewexport-command).

5. Sprawdź, czy można odczytać/zapisywać na dyskach, które zostaną wysłane do zadania eksportu.

### <a name="example-of-previewexport-command"></a>Przykład polecenia PreviewExport

Poniższy przykład pokazuje `PreviewExport` polecenie:  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

Plik listy obiektów blob eksportu może zawierać nazwy obiektów blob i prefiksy obiektów blob, jak pokazano poniżej:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Narzędzie importu/eksportu platformy Azure wyświetla listę wszystkich obiektów blob do wyeksportowania i oblicza sposób pakowania ich na dyski o określonym rozmiarze, biorąc pod uwagę wszelkie niezbędne obciążenie, a następnie szacuje liczbę dysków potrzebnych do przechowywania obiektów blob i informacji o użyciu dysku.  

Oto przykład danych wyjściowych, z dzienników informacyjnych pominięte:  

```powershell
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

## <a name="examples-of-valid-blob-paths"></a>Przykłady prawidłowych ścieżek obiektów blob

W poniższej tabeli przedstawiono przykłady prawidłowych ścieżek obiektów blob:

   | Selektor | Ścieżka obiektu blob | Opis |
   | --- | --- | --- |
   | Zaczyna się od |/ |Eksportuje wszystkie obiekty blob na koncie magazynu |
   | Zaczyna się od |/$root/ |Eksportuje wszystkie obiekty BLOB w kontenerze głównym |
   | Zaczyna się od |/książka |Eksportuje wszystkie obiekty blob w dowolnym kontenerze, który rozpoczyna się od **książki** prefiksów |
   | Zaczyna się od |/muzyka/ |Eksportuje wszystkie obiekty blob w **muzyce kontenera** |
   | Zaczyna się od |/muzyka/miłość |Eksportuje wszystkie obiekty blob w **muzyce** kontenera, które zaczynają się od prefiksu **miłości** |
   | Równe |$root/logo.bmp |Eksportuje **logo obiektu blob.bmp** w kontenerze głównym |
   | Równe |filmy/story.mp4 |Eksportuje plik **blob story.mp4** w **filmach kontenerowych** |

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie stanowiska i stanu dysku](storage-import-export-view-drive-status.md)
- [Przejrzyj wymagania dotyczące importu/eksportu](storage-import-export-requirements.md)
