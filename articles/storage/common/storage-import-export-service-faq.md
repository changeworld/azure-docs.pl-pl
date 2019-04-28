---
title: Często zadawane pytania dotyczące usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Odczyt odpowiedzi na często zadawane pytania dotyczące usługi Azure Import/Eksport.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ee2917c64843c8ab137e0122d63a328d6c19fedb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478576"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Usługa Azure Import/Export: często zadawane pytania 
Poniżej przedstawiono pytania i odpowiedzi, które mogą wystąpić podczas przesyłanie danych do magazynu platformy Azure za pomocą usługi Azure Import/Export. Pytania i odpowiedzi są podzielone na następujące kategorie:

- O usłudze Import/Export
- Przygotowywanie dysków dla usługi import/export
- Zadania importu/eksportu
- Wysyłanie dysków
- Różne postanowienia 

## <a name="about-importexport-service"></a>O usłudze Import/Export

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Czy mogę skopiować usługi Azure File storage przy użyciu usługi Azure Import/Export

Tak. Obsługa usługi Azure Import/Export zaimportować do usługi Azure File Storage. Nie obsługuje eksportowanie plików platformy Azure w tej chwili.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Usługa Azure Import/Export jest dostępna dla subskrypcji programu CSP?

Tak. Usługa Azure Import/Export obsługuje subskrypcje dostawcy rozwiązań w chmurze (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Skopiuj PST skrzynek pocztowych i dane programu SharePoint do usługi Office 365 mogą używać usługi Azure Import/Export?

Tak. Aby uzyskać więcej informacji, przejdź do [pliki PST importu lub dane programu SharePoint do usługi Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Aby skopiować Moje kopie zapasowe w trybie offline w usłudze Kopia zapasowa Azure można używać usługi Azure Import/Export?

Tak. Aby uzyskać więcej informacji, przejdź do [workflow kopii zapasowej Offline w usłudze Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Od firmy Microsoft można kupić dysków dla zadania importu/eksportu?

Nie. Musisz wysłać dysków do importowania i eksportowania zadania.


## <a name="preparing-disks-for-importexport"></a>Przygotowywanie dysków dla usługi import/export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Czy można pominąć krok przygotowania dysku do zadania importu? Bez kopiowania można przygotować dysku?

Nie. Każdy dysk służą do importowania danych muszą być przygotowane za pomocą narzędzia Azure WAImportExport. To narzędzie umożliwia także skopiować dane na dysku.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Czy muszę wykonać wszelkie przygotowanie dysku podczas tworzenia zadania eksportu?

Nie. Niektóre prechecks są zalecane. Aby sprawdzić, liczba dysków wymagana, należy użyć narzędzia WAImportExport PreviewExport polecenia. Aby uzyskać więcej informacji, zobacz [wyświetlanie podglądu użycia dysków przez zadanie eksportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Polecenie ułatwia wyświetlanie podglądu użycia dysków dla wybranych obiektów blob, w oparciu o rozmiar dysków, które zamierzasz używać. Sprawdź także, czy może odczytywać i zapisu na dysku twardym, który jest dostarczany zadanie eksportu.

## <a name="importexport-jobs"></a>Zadania importu/eksportu

### <a name="can-i-cancel-my-job"></a>Czy można anulować Moje zadanie?
Tak. Można anulować zadania, gdy jego stan to **tworzenie** lub **wysyłania**. Poza te etapy nie można anulować zadania, i jest kontynuowany aż do końcowego etapu.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak długo można wyświetlić stan ukończonych zadań w witrynie Azure portal?
Możesz wyświetlić stan ukończonych zadań przez 90 dni. Ukończone zadania są usuwane po 90 dniach.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Jeśli chcę zaimportować lub wyeksportować więcej niż 10 dysków, co należy zrobić?
Jeden import lub zadanie eksportu może odwoływać się tylko 10 dysków w ramach jednego zadania. Aby wysłać więcej niż 10 dysków, należy utworzyć wiele zadań. Dyski skojarzone z tym samym zadaniu powinny być wysyłane ze sobą w tym samym pakiecie. Dla zadania importu więcej informacji i wskazówek, gdy pojemność danych obejmuje wiele dysków, skontaktuj się z pomocą techniczną firmy Microsoft w bulkimport@microsoft.com. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Przekazany obiekt blob wskazuje stan jako "Dzierżawa wygasła". Co mam zrobić?
Pole "Dzierżawa wygasła" można zignorować. Import/Export uzyskuje dzierżawę w obiekcie blob podczas przekazywania, aby upewnić się, że żaden inny proces może zaktualizować obiektów blob w sposób równoległy. Wygasłe dzierżawy oznacza, że Import/export nie jest już przekazywania do niej, a obiekt blob jest dostępny do użycia. 

## <a name="shipping-disks"></a>Wysyłanie dysków

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Co to jest maksymalna liczba dysk twardy dla jednego wydania?
Nie ma żadnego limitu liczby dysków twardych w jednym wydaniu. Jeśli dyski należą do wielu zadań, zalecamy możesz: 
- etykiety dysków o odpowiedniej nazwy zadania.
- aktualizowanie zadania za pomocą numeru śledzenia sufiks wartość -1,-2 itd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Należy umieszczać coś innego niż dysk twardy w moim pakiecie?
Tylko dyski twarde są dostarczane w pakiecie wysyłki. Nie należy dołączać elementy, takie jak kable dostaw energii lub kable USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Czy muszę dostarczaj Moje dyski za pomocą FedEx lub przez firmę DHL?
Możesz przesłać dysków do centrum danych platformy Azure przy użyciu dowolnej znanych operatora, takich jak FedEx, przez firmę DHL, UPS lub poczty amerykańskiej. Jednak dla wysyłki zwrotnej dysków dla użytkownika z centrum danych, musisz podać:

- Liczba kont FedEx w USA i UE, lub
- Numer konta przez firmę DHL w regionach Azja Wschodnia i Australia.

> [!NOTE]
> Centra danych w Indiach wymaga litery deklaracji nagłówek listowy (dostarczanie challan) do zwrócenia z stacje. Aby rozmieścić wymaganego wpisu — dostęp próbny, możesz również zarezerwować pobranie za pomocą operatora wybranego i udostępniać szczegółowe informacje z centrum danych.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Czy istnieją jakieś ograniczenia przy dostarczaniu stacja w wielu krajach?
Należy pamiętać, nośnik fizyczny, który jest dostarczany może być konieczne między granicami. Ponosisz odpowiedzialność za zapewnienie, że Twoje dane i nośnik fizyczny są importowane i/lub wyeksportowane zgodnie z obowiązującymi przepisami. Przed wysyłką nośnik fizyczny z Twojej doradców, aby sprawdzić, czy multimediów i danych prawnie mogą być wysyłane do centrum danych zidentyfikowane. Ułatwi to zapewnienie osiągnie firmy Microsoft, w odpowiednim czasie.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Czy istnieją jakiekolwiek specjalne wymagania, dostarczania dysków do centrum danych?

Wymagania zależą od ograniczeń określonych centrów danych platformy Azure.
- Istnieje kilka witryn, które wymagają centrum danych platformy Microsoft numer identyfikacyjny dla ruchu przychodzącego, które ma zostać zapisany na działkę ze względów bezpieczeństwa. Przed dostarczeniem aplikacji z dysków lub dysków do centrum danych, skontaktuj się z działem Azure DataBox operacji (adbops@microsoft.com) w celu rozwiązania tego numeru. Bez tego numeru pakiet zostanie odrzucone.
- Centra danych w Indiach wymagają informacje osobowe sterownika, takie jak karty identyfikacyjnej dla instytucji rządowych lub nie dowodu. (na przykład, PRZESUWANIE, AADHAR, listy Dystrybucyjnej) nazwę, skontaktuj się z pomocą i samochodu płytką liczbę, aby otrzymać przebiegu wpis bramy. Aby uniknąć opóźnień w dostawie, informuje operatora o tych wymagań.


### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Podczas tworzenia zadania, adresu wysyłkowego jest lokalizacji, która różni się od Moje Lokalizacja konta magazynu. Co mam zrobić?

Niektóre lokalizacji konta magazynu są mapowane do lokalizacji alternatywnej wysyłki. Wcześniej dostępnych lokalizacji wysyłki można również tymczasowo mapować do alternatywnej lokalizacji. Zawsze sprawdzaj adres wysyłkowy podane podczas tworzenia zadania przed dostarczeniem dysków.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Podczas wysyłania dysk, operatora poprosi o podanie danych Centrum skontaktuj się z pomocą adres i numer telefonu. Co należy zapewnić?

Numer telefonu i kontroler domeny adresów jest dostępna w ramach zadania tworzenia.


## <a name="miscellaneous"></a>Różne postanowienia

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co się stanie, jeśli wymagania obsługiwanej przypadkowo wysyła dysk twardy, który nie jest zgodny?

Centrum danych platformy Azure zwróci dysku, który nie jest zgodny obsługiwane wymagania dla Ciebie. Jeśli tylko niektóre z stacje w pakiecie spełnia wymagania w zakresie obsługi, te dyski zostaną przetworzone i dyski, które nie spełniają wymagań, zostanie zwrócona do Ciebie.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Usługa i sformatować dyski przed zwróceniem?

Nie. Wszystkie dyski są szyfrowane za pomocą funkcji BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak mają dostęp do danych, który jest importowany przez tę usługę?

Za pomocą witryny Azure Portal lub [Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) dostępu do danych w ramach konta usługi Azure storage.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Po ukończeniu importowania Moje dane jak wygląda w ramach konta magazynu? Moje hierarchii katalogów jest zachowywana?

Podczas przygotowywania dysków twardych do zadania importu, miejsce docelowe jest określony przez pole DstBlobPathOrPrefix w zestawie danych CSV. Jest to kontener docelowy w ramach konta magazynu, do którego są kopiowane dane z dysku twardego. W tym kontenerze docelowym katalogi wirtualne są tworzone foldery na dysku twardym, a obiekty BLOB są tworzone dla plików. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Jeśli dysk ma pliki, które już istnieją w moim koncie usługi storage, powoduje usługi zastąpienia istniejących obiektów blob lub pliki?

Jest zależna. Podczas przygotowywania dysku, możesz określić, czy pliki docelowe powinny być zastąpione, czy ignorowane za pomocą pola w pliku CSV zestaw danych o nazwie dyspozycji: < Zmień nazwę | zastąpić nie | zastąpić >. Domyślnie usługa zmienia nazwę nowych plików zamiast zastępowania istniejących obiektów blob lub plików.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Narzędzie WAImportExport jest zgodny z 32-bitowych systemach operacyjnych?
Nie. Narzędzie WAImportExport jest zgodna tylko z 64-bitowych systemach operacyjnych Windows. Aby uzyskać pełną listę obsługiwanych systemów operacyjnych, przejdź do [obsługiwane systemy operacyjne](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Jaka jest maksymalna blokowych obiektów Blob i rozmiar stronicowego obiektu Blob, obsługiwane przez usługi Azure Import/Export?

Rozmiar maksymalny blokowych obiektów Blob jest około 4.768TB lub 5 000 000 MB.
Rozmiar maksymalny stronicowych obiektów Blob jest 8TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Usługa Azure Import/Export obsługuje szyfrowanie AES-256?
Domyślnie usługa Azure Import/Export używa algorytmu AES-128 szyfrowanie funkcją bitlocker. Możesz zmienić to AES-256, szyfrując ręcznie za pomocą funkcji bitlocker przed skopiowaniem danych. 

- Jeśli przy użyciu [WAImportExport V1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), poniżej przedstawiono przykład polecenia
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Jeśli przy użyciu [WAImportExport V2](https://www.microsoft.com/download/details.aspx?id=55280) określ "AlreadyEncrypted", a następnie podać klucz w driveset CSV.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest Azure Import/Export?](storage-import-export-service.md)


