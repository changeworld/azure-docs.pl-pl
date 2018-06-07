---
title: Często zadawane pytania dotyczące usługi Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Odczyt odpowiedzi na często zadawane pytania dotyczące usługi Azure Importuj Eksportuj.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: alkohli
ms.openlocfilehash: ed928452946b871ee9192bda82fcbf205b96e6e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660885"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Usługa Import/Eksport Azure: często zadawane pytania 
Poniżej przedstawiono pytania i odpowiedzi, które mogą mieć w przypadku transferu danych do magazynu Azure za pomocą usługi Import/Eksport Azure. Pytania i odpowiedzi są rozmieszczone w ramach następujących kategorii:

- O usłudze Import/Eksport
- Przygotowywanie dyski do importowania i eksportowania
- Zadania importu/eksportu
- Wysyłanie dysków
- Różne postanowienia 

## <a name="about-importexport-service"></a>O usłudze Import/Eksport

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Można skopiować magazyn plików Azure za pomocą usługi Import/Eksport Azure?

Tak. Obsługa usługi Import/Eksport Azure importowania do usługi Magazyn plików Azure. Nie obsługuje eksportowanie plików Azure w tej chwili.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>To jest usługa Import/Eksport Azure dostępne dla dostawcy usług Kryptograficznych subskrypcji?

Tak. Usługa Import/Eksport Azure obsługuje subskrypcje dostawców rozwiązań chmury (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Aby skopiować PST skrzynki pocztowe i danych programu SharePoint dla usługi Office 365 można użyć usługi Import/Eksport Azure?

Tak. Aby uzyskać więcej informacji, przejdź do [PST importowanie plików lub danych programu SharePoint do usługi Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Aby skopiować kopiach zapasowych w trybie offline z usługą kopia zapasowa Azure można używać usługi Import/Eksport Azure?

Tak. Aby uzyskać więcej informacji, przejdź do [przepływu pracy w trybie Offline z kopii zapasowej w programie Kopia zapasowa Azure](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Firma Microsoft może zakupić dysków dla zadania importu/eksportu?

Nie. Należy wysłać dysków do importowania i eksportowania zadania.


## <a name="preparing-disks-for-importexport"></a>Przygotowywanie dysków do importowania i eksportowania

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Można pominąć krok przygotowania dysku dla zadania importu? Bez kopiowania można przygotować dysku?

Nie. Każdy dysk służą do importowania danych muszą być przygotowane za pomocą narzędzia Azure WAImportExport. Narzędzie do także skopiować dane na dysku.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Należy wykonać wszelkie przygotowanie dysku podczas tworzenia zadania eksportu?

Nie. Zaleca się niektóre prechecks. Aby sprawdzić liczbę dysków wymaganych, użyj narzędzia WAImportExport PreviewExport polecenia. Aby uzyskać więcej informacji, zobacz [przeglądania użycia dysków dla zadania eksportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Polecenie pomaga Podgląd użycia dysków dla wybranych obiektów blob, zależnie od rozmiaru dysków, które zamierzasz używać. Ponadto sprawdź, czy można odczytywać i zapisywać na dysku twardym, który jest dostarczany dla zadania eksportu.

## <a name="importexport-jobs"></a>Zadania importu/eksportu

### <a name="can-i-cancel-my-job"></a>Czy można anulować Moje zadanie?
Tak. Można anulować zadania, gdy jego stan jest **tworzenie** lub **wysyłania**. Poza etapy te nie można anulować zadania i nadal przed ostatnim etapie.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak długo wyświetlanie stanu zadań zakończonych w portalu Azure
Można wyświetlić stan zakończonych zadań do 90 dni. Zadania ukończone są usuwane po 90 dniach.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Jeśli Chcę importowania lub eksportowania więcej niż 10 dysków, co należy zrobić?
Jeden importu lub eksportu zadanie może odwoływać się tylko 10 dysków w jednym zadaniu. Aby wysłać więcej niż 10 dysków, należy utworzyć wiele zadań. Skojarzone z tym samym zadaniu dyski muszą być wysłane razem w tym samym pakiecie. Dla zadania importu dodatkowe informacje i wskazówki, gdy pojemność danych obejmuje wiele dysków, skontaktuj się z pomocą techniczną firmy Microsoft w bulkimport@microsoft.com.                                                              

## <a name="shipping-disks"></a>Wysyłanie dysków

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Co to jest maksymalna liczba dysków Twardych dla w jednym wydaniu?
Nie ma żadnego limitu liczby dysków twardych w jednym wydaniu. Jeśli dyski należą do wielu zadań, zaleca się możesz: 
- etykiety dysków przy użyciu nazwy zadania.
- Aktualizacja zadania z liczbą śledzenia sufiks na -1,-2 itd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Należy umieszczać innym niż dysk twardy w moim pakiecie?
Wyślij tylko dyski twarde w pakiecie wysyłki. Nie dołączaj elementy, takie jak power przewodów zasilania lub USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Czy muszę wysłać Moje dysków przy użyciu FedEx lub przez firmę DHL?
Mogą być dysków do centrum danych platformy Azure przy użyciu dowolnego znanego operatora jak FedEx, DHL w sprawie, UPS lub poczty amerykańskiej. Jednak w wydaniu zwrotu dysków dla użytkownika z centrum danych, należy określić:

- Numer konta FedEx w Stanach Zjednoczonych i UE, lub
- Numer konta przez firmę DHL w regionach Azja i klientów w Australii.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>Czy istnieją ograniczeń z wysyłki za granicę dysk?
Należy pamiętać, że nośnik fizyczny, który jest dostarczany może być konieczne między granicami. Ponosisz odpowiedzialność za zapewnienie, że nośnik fizyczny i danych użytkownika są importowane i/lub wyeksportować zgodnie z prawem. Przed wysyłką nośnik fizyczny, sprawdź z Twojej doradcy do sprawdzenia, czy multimediów i danych legalnie mogą być wysyłane do centrum danych zidentyfikowane. Pomoże to w celu zapewnienia osiągnie firmy Microsoft, w odpowiednim czasie.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Podczas tworzenia zadania, adres wysyłkowy jest lokalizacji, która różni się od Moje Lokalizacja konta magazynu. Co mam zrobić?

Niektóre lokalizacje konta magazynu są mapowane do lokalizacji alternatywnej wysyłki. Wcześniej dostępne lokalizacje wysyłanie można również tymczasowo mapować do alternatywnej lokalizacji. Zawsze należy sprawdzić adres wysyłkowy podany podczas tworzenia zadania przed dostarczeniem dysków.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Podczas wysyłania dysk, operatora monituje o podanie danych Centrum kontaktu adres i numer telefonu. Co należy zapewnić?

Numer telefonu i kontroler domeny adresów jest dostępna w ramach zadania tworzenia.


## <a name="miscellaneous"></a>Różne postanowienia

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co się stanie, obsługiwane wymagania przypadkowego wysłania dysk twardy, który nie jest zgodna z?

Centrum danych Azure zwróci dysk, który nie jest zgodna z obsługiwanych wymagania dla Ciebie. Jeśli tylko niektóre z dysków w pakiecie wymagań obsługi tych dyskach zostaną przetworzone, a dyski, które nie spełniają wymagań, zostanie zwrócony do Ciebie.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Usługa sformatować dyski przed zwróceniem?

Nie. Wszystkie dyski są szyfrowane za pomocą funkcji BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak mają dostęp do danych, który jest importowany przez tę usługę?

Za pomocą portalu Azure lub [Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) dostępu do danych na koncie magazynu platformy Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Po zakończeniu importowania moich danych jak wygląda na koncie magazynu? Moje hierarchii katalogów jest zachowywana?

Podczas przygotowywania dysk twardy dla zadania importu, miejsce docelowe jest określony przez pole DstBlobPathOrPrefix w zestawie danych CSV. Jest to docelowy kontener na koncie magazynu, do którego jest kopiowany danych z dysku twardego. W tym docelowy kontener katalogi wirtualne są tworzone foldery z dysku twardego i obiekty BLOB są tworzone dla plików. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Jeśli dysk ma plików, które już istnieją w moim koncie magazynu, usługa zastąpić istniejące obiekty BLOB lub pliki?

Zależy od. Podczas przygotowywania dysku, można określić czy pliki docelowy powinien zostać zastąpione lub ignorowane za pomocą pola w pliku CSV zestawu danych o nazwie dyspozycji: < zmienić | zastąpić nie | zastąpić >. Domyślnie usługa zmienia nazwę nowych plików zamiast zastąpić istniejące obiekty BLOB lub plików.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Narzędzie WAImportExport jest zgodny z 32-bitowych systemach operacyjnych?
Nie. Narzędzie WAImportExport jest zgodna tylko z 64-bitowych systemach operacyjnych Windows. Aby uzyskać pełną listę obsługiwanych systemu operacyjnego, przejdź do [systemy operacyjne obsługiwane](). 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Jaka jest maksymalna blokowych obiektów Blob i rozmiar obiektu Blob strony obsługiwane przez Import/Eksport Azure?

Rozmiar maksymalny blokowych obiektów Blob to około 4.768TB lub 5,000,000 MB.
Rozmiar obiektu Blob strony maksymalna liczba wynosi 1TB.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Import/Eksport Azure obsługuje szyfrowanie AES 256?
Domyślnie usługa Import/Eksport Azure używa szyfrowania AES-128 funkcji bitlocker. Można to zmienić na AES 256 szyfrując ręcznie za pomocą funkcji bitlocker przed skopiowaniem danych. 

- Jeśli przy użyciu [WAImportExport V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), poniżej przedstawiono przykład polecenia
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- Jeśli przy użyciu [WAImportExport V2](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) określ "AlreadyEncrypted" i podaj klucz w driveset CSV.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Co to jest Azure importu/eksportu?](storage-import-export-service.md)


