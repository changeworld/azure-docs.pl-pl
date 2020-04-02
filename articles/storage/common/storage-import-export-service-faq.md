---
title: Często zadawane pytania dotyczące usługi importowania/eksportowania platformy Azure | Dokumenty firmy Microsoft
description: Przeczytaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Import Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 904f93aa705a4e327f29bbec109bdf3b937f6c70
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519482"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Usługa importu/eksportowania platformy Azure: często zadawane pytania

Poniżej przedstawiono pytania i odpowiedzi, które mogą być używane podczas korzystania z usługi Azure Import/Export do przesyłania danych do magazynu platformy Azure. Pytania i odpowiedzi są podzielone na następujące kategorie:

- Informacje o usłudze importu/eksportu
- Przygotowywanie dysków do importu/eksportu
- Zadania importu/eksportu
- Dyski wysyłkowe
- Różne

## <a name="about-importexport-service"></a>Informacje o usłudze importu/eksportu

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Czy mogę skopiować magazyn plików platformy Azure przy użyciu usługi Azure Import/Export?

Tak. Usługa importu/eksportu platformy Azure obsługuje importowanie do usługi Azure File Storage. Obecnie nie obsługuje eksportu plików platformy Azure.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Czy usługa importu/eksportu platformy Azure jest dostępna dla subskrypcji dostawcy usług kryptograficznych?

Tak. Usługa Azure Import/Export obsługuje subskrypcje dostawców rozwiązań w chmurze (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Czy mogę użyć usługi Azure Import/Export do kopiowania skrzynek pocztowych pst i danych programu SharePoint do usługi O365?

Tak. Aby uzyskać więcej informacji, przejdź do [tematu Importowanie plików PST lub danych programu SharePoint do usługi Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Czy mogę użyć usługi Azure Import/Export, aby skopiować kopie zapasowe w trybie offline do usługi Azure Backup Service?

Tak. Aby uzyskać więcej informacji, przejdź do [przepływu pracy kopia zapasowa w trybie offline w usłudze Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Czy mogę kupić dyski do zadań importowania/eksportowania od firmy Microsoft?

Nie. Musisz wysłać własne dyski do importu i eksportu zadań.

## <a name="preparing-disks-for-importexport"></a>Przygotowywanie dysków do importu/eksportu

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Czy mogę pominąć etap przygotowania dysku do zadania importu? Czy mogę przygotować dysk bez kopiowania?

Nie. Każdy dysk używany do importowania danych musi być przygotowany przy użyciu narzędzia Azure WAImportExport. Użyj narzędzia, aby również skopiować dane na dysk.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Czy podczas tworzenia zadania eksportu muszę wykonać jakiekolwiek przygotowanie dysku?

Nie. Zalecane są niektóre wstępne sprawdzanie. Aby sprawdzić liczbę wymaganych dysków, użyj polecenia WAImportExport tool PreviewExport. Aby uzyskać więcej informacji, zobacz [Podgląd użycia dysku dla zadania eksportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Polecenie ułatwia podgląd użycia dysku dla wybranych obiektów blob na podstawie rozmiaru dysków, których chcesz użyć. Sprawdź również, czy można odczytywać i zapisywać na dysku twardym, który jest dostarczany do zadania eksportu.

## <a name="importexport-jobs"></a>Zadania importu/eksportu

### <a name="can-i-cancel-my-job"></a>Czy mogę anulować pracę?

Tak. Zadanie można anulować, gdy jego stan to **Tworzenie** lub **Wysyłka**. Poza tymi etapami zadanie nie może zostać anulowane i trwa do ostatniego etapu.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak długo mogę wyświetlać stan ukończonych zadań w witrynie Azure portal?

Stan ukończonych zadań można wyświetlić przez maksymalnie 90 dni. Ukończone zadania są usuwane po 90 dniach.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Co zrobić, jeśli chcę importować lub eksportować więcej niż 10 dysków?

Jedno zadanie importu lub eksportu może odwoływać się tylko do 10 dysków w jednym zadaniu. Aby wysłać więcej niż 10 dysków, należy utworzyć wiele zadań. Dyski skojarzone z tym samym zadaniem muszą być dostarczane razem w tym samym pakiecie.
Aby uzyskać więcej informacji i wskazówek, gdy pojemność danych obejmuje wiele zadań importowania dysków, skontaktuj się z pomocą techniczną firmy Microsoft.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Przekazany obiekt blob pokazuje stan jako "Dzierżawa wygasła". Co mam zrobić?

Można zignorować pole "Dzierżawa wygasła". Import/Eksport trwa dzierżawy obiektu blob podczas przekazywania, aby upewnić się, że żaden inny proces można zaktualizować obiekt blob równolegle. Dzierżawa Wygasła oznacza, że import/eksport nie jest już przekazywanie do niego i obiekt blob jest dostępny do użytku.

## <a name="shipping-disks"></a>Dyski wysyłkowe

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Jaka jest maksymalna liczba dysków twardych w jednej przesyłce?

Nie ma ograniczeń co do liczby dysków twardych w jednej przesyłce. Jeśli dyski należą do wielu zadań, zaleca się:

- dysków odpowiednimi nazwami zadań.
- zaktualizować zadania z numerem śledzenia z przyrostkiem -1, -2 itp.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Czy powinienem dołączyć do mojego pakietu coś innego niż dysk twardy?

Wyślij tylko dyski twarde w paczce wysyłkowej. Nie należy dołączać takich elementów, jak kable zasilające lub kable USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Czy muszę wysyłać dyski za pomocą FedEx lub DHL?

Dyski można wysyłać do centrum danych platformy Azure przy użyciu dowolnego znanego operatora, takiego jak FedEx, DHL, UPS lub US Postal Service. Jednak w przypadku wysyłki zwrotnej dysków do Ciebie z centrum danych należy podać:

- Numer konta FedEx w USA i UE, lub
- Numer konta DHL w regionach Azji i Australii.

> [!NOTE]
> Centra danych w Indiach wymagają listu deklaracji na papierze firmowym (challan dostawy) do zwrotu dysków. Aby zorganizować wymagany bilet wstępu, należy również zarezerwować odbiór u wybranego operatora i udostępnić szczegóły w centrum danych.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Czy istnieją jakieś ograniczenia dotyczące wysyłki i zwrotu dysku na arenie międzynarodowej?

Należy pamiętać, że fizyczne nośniki, które wysyłasz, mogą wymagać przekroczenia granic międzynarodowych. Użytkownik jest odpowiedzialny za zapewnienie, że jego fizyczne nośniki i dane są importowane i/lub eksportowane zgodnie z obowiązującymi przepisami prawa. Przed wysłaniem nośnika fizycznego skontaktuj się z doradcami, aby sprawdzić, czy twoje multimedia i dane mogą być legalnie wysyłane do zidentyfikowanego centrum danych. Pomoże to zapewnić, że dotrze do firmy Microsoft w odpowiednim czasie.

Po zakończeniu przesyłania proces zwracania dysków na adres międzynarodowy może trwać dłużej niż typowe 2-3 dni potrzebne do wysyłki lokalnej. Na etapie wymienionym w witrynie Azure portal jako opakowanie zespół data box zapewnia, że zostanie dostarczona poprawna dokumentacja, aby upewnić się, że przesyłka jest zgodna z różnymi wymaganiami dotyczącymi importu i eksportu międzynarodowego.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Czy istnieją specjalne wymagania dotyczące dostarczania dysków do centrum danych?

Wymagania zależą od określonych ograniczeń centrum danych platformy Azure.

- Istnieje kilka witryn, takich jak Australia, Niemcy i Wielka Brytania Południowa, które wymagają numeru przychodzącego identyfikatora centrum danych firmy Microsoft, który ma być zapisany na działce ze względów bezpieczeństwa. Przed wysłaniem dysków lub dysków do centrum danych skontaktujadbops@microsoft.comsię z działem operacji usługi Azure DataBox ( ), aby uzyskać ten numer. Bez tego numeru pakiet zostanie odrzucony.
- Centra danych w Indiach wymagają danych osobowych kierowcy, takich jak rządowy dowód tożsamości lub nr dowodu. (na przykład PAN, AADHAR, DL), imię i nazwisko, kontakt i numer tablicy rejestracyjnej samochodu, aby uzyskać przepustkę wejściową. Aby uniknąć opóźnień w dostawie, poinformuj operatora o tych wymaganiach.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Podczas tworzenia zadania adres wysyłki to lokalizacja, która różni się od lokalizacji konta magazynu. Co mam zrobić?

Niektóre lokalizacje kont magazynu są mapowane na alternatywne lokalizacje wysyłki. Wcześniej dostępne lokalizacje wysyłki można również tymczasowo mapować na alternatywne lokalizacje. Zawsze sprawdzaj adres wysyłki podany podczas tworzenia zadania przed wysyłką dysków.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Podczas wysyłki dysku przewoźnik prosi o podanie adresu kontaktowego centrum danych i numeru telefonu. Co powinienem podać?

Numer telefonu i adres DC są dostarczane w ramach tworzenia miejsc pracy.

## <a name="miscellaneous"></a>Różne

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co się stanie, jeśli przypadkowo wyślę dysk twardy, który nie spełnia obsługiwanych wymagań?

Centrum danych platformy Azure zwróci dysk, który nie jest zgodny z obsługiwanymi wymaganiami. Jeśli tylko niektóre dyski w pakiecie spełniają wymagania pomocy technicznej, te dyski zostaną przetworzone, a dyski, które nie spełniają wymagań, zostaną zwrócone do Ciebie.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Czy usługa formatuje dyski przed ich zwróceniem?

Nie. Wszystkie dyski są szyfrowane za pomocą funkcji BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak uzyskać dostęp do danych importowanych przez tę usługę?

Użyj witryny Azure portal lub [Eksploratora magazynu,](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) aby uzyskać dostęp do danych w ramach konta magazynu platformy Azure.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Jak wyglądają moje dane po zakończeniu importu na koncie magazynu? Czy moja hierarchia katalogów jest zachowywana?

Podczas przygotowywania dysku twardego dla zadania importu miejsce docelowe jest określone przez pole DstBlobPathOrPrefix w pliku CSV zestawu danych. Jest to kontener docelowy na koncie magazynu, do którego są kopiowane dane z dysku twardego. W tym kontenerze docelowym katalogi wirtualne są tworzone dla folderów z dysku twardego, a obiekty blob są tworzone dla plików.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Jeśli na dysku znajdują się pliki, które już istnieją na moim koncie magazynu, czy usługa zastępuje istniejące obiekty blob lub pliki?

Zależy. Podczas przygotowywania dysku można określić, czy pliki docelowe mają zostać zastąpione, czy zignorowane przy użyciu pola w pliku CSV zestawu danych o nazwie Dyspozycja:<rename|no-overwrite|overwrite>. Domyślnie usługa zmienia nazwę nowych plików, zamiast zastępowania istniejących obiektów blob lub plików.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Czy narzędzie WAImportExport jest kompatybilne z 32-bitowymi systemami operacyjnymi?

Nie. Narzędzie WAImportExport jest zgodne tylko z 64-bitowymi systemami operacyjnymi Windows. Aby uzyskać pełną listę obsługiwanych systemów operacyjnych, przejdź do [obsługiwanego systemu operacyjnego](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Co to jest maksymalny rozmiar bloku obiektu blob i bloku obiektu blob obsługiwane przez usługę Azure Import/Export?

- Maksymalny rozmiar bloku obiektu blob wynosi około 4.768TB lub 5,000,000 MB.
- Maksymalny rozmiar obiektu blob strony wynosi 8 TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Czy usługa Azure Import/Export obsługuje szyfrowanie AES-256?

Nie. Usługa Azure Import/Export service używa szyfrowania funkcją BitLocker AES-128.

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Azure Import/Export?](storage-import-export-service.md)
