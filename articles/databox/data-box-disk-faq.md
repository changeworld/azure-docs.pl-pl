---
title: Microsoft Azure Data Box Disk — często zadawane pytania | Microsoft Docs — dane
description: Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Data Box Disk, rozwiązania chmurowego umożliwiającego przenoszenie dużych ilości danych na platformę Azure, oraz odpowiedzi na te pytania
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/17/2018
ms.author: alkohli
ms.openlocfilehash: 5288e9900c75eae7601b84f7366edf9ac739d5da
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125807"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Co to jest usługa Azure Data Box Disk? (Wersja zapoznawcza)

Rozwiązanie w chmurze Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób. Często zadawane pytania zawierają pytania, które mogą się pojawić podczas korzystania z usługi Data Box Disk w witrynie Azure Portal, oraz odpowiedzi na te pytania. 

Pytania i odpowiedzi są podzielone na następujące kategorie:

- Informacje o usłudze
- Konfigurowanie i łączenie 
- Śledzenie stanu
- Migrowanie danych 
- Weryfikowanie i przekazywanie danych 

> [!IMPORTANT]
> Usługa Data Box Disk jest dostępna w wersji zapoznawczej. Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-the-service"></a>Informacje o usłudze

### <a name="q-what-is-azure-data-box-service"></a>PYTANIE: Co to jest usługa Azure Data Box? 
ODPOWIEDŹ:  Usługa Azure Data Box jest przeznaczona do pozyskiwania danych w trybie offline. Ta usługa zarządza szeregiem produktów dostosowanych do transportu danych dla różnych pojemności magazynu. 

### <a name="q-what-are-azure-data-box-disks"></a>PYTANIE: Co to są urządzenia Azure Data Box Disk?
ODPOWIEDŹ: Urządzenia Azure Data Box Disk umożliwiają szybki, niedrogi i bezpieczny transfer terabajtów danych do i z platformy Azure. Firma Microsoft dostarcza od 1 do 5 dysków o maksymalnej pojemności magazynu wynoszącej 35 TB. Za pomocą usługi Data Box w witrynie Azure Portal możesz łatwo konfigurować, łączyć i odblokowywać te dyski.  

Dyski są szyfrowane za pomocą funkcji szyfrowania dysków Microsoft BitLocker, a klucze szyfrowania są zarządzane w witrynie Azure Portal. Następnie kopiujesz dane z serwerów klientów. W centrum danych firma Microsoft migruje dane z dysku do chmury przy użyciu szybkiego połączenia przekazywania w sieci prywatnej i przekazuje je na platformę Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>PYTANIE: Kiedy używać urządzeń Data Box Disk?
ODPOWIEDŹ: Jeśli masz 35 TB (lub mniej) danych, które chcesz przenieść na platformę Azure, zastosowanie urządzeń Data Box Disk będzie korzystne.

### <a name="q-what-is-the-price-of-data-box-disks"></a>PYTANIE: Jaki jest koszt urządzeń Data Box Disk?
ODPOWIEDŹ: W okresie obowiązywania wersji zapoznawczej urządzenia Data Box Disk są dostępne bezpłatnie. Bezpłatna jest także przesyłka, natomiast zostaną naliczone opłaty za magazyn platformy Azure.

### <a name="q-how-do-i-get-data-box-disks"></a>PYTANIE: Jak mogę otrzymać urządzenia Data Box Disk? 
ODPOWIEDŹ:  Aby otrzymać urządzenia Azure Data Box Disk, najpierw zarejestruj się w celu skorzystania z [wersji zapoznawczej usługi Data Box Disk](http://aka.ms/AzureDataBox). Następnie zaloguj się do witryny Azure Portal i utwórz zamówienie dysków w usłudze Data Box. Podaj informacje kontaktowe i szczegóły dotyczące powiadomień. Po złożeniu zamówienia, w zależności od dostępności, dyski zostaną dostarczone do Ciebie w ciągu 10 dni.   

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>PYTANIE: Jaka jest maksymalna ilość danych, które mogę jednorazowo przenieść za pomocą urządzeń Data Box Disk?
ODPOWIEDŹ: W przypadku 5 dysków — każdy o rozmiarze 8 TB (7 TB pojemności do wykorzystania) — maksymalna pojemność do wykorzystania to 35 TB. W związku z tym jednorazowo możesz przenieść 35 TB danych.  Aby przenieść większą ilość danych, musisz zamówić więcej dysków.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>PYTANIE: Jak sprawdzić, czy urządzenia Data Box Disk są dostępne w moim regionie? 
ODPOWIEDŹ:  W okresie obowiązywania wersji zapoznawczej urządzenia Data Box Disk są dostępne w Stanach Zjednoczonych i we wszystkich krajach Unii Europejskiej.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>PYTANIE: W jakich regionach mogę zapisać dane, korzystając z usługi Data Box Disk?
ODPOWIEDŹ: Wersja zapoznawcza usługi Data Box Disk jest obsługiwana we wszystkich regionach Stanów Zjednoczonych oraz w regionach Europa Zachodnia i Europa Północna. Obsługiwane są tylko regiony chmury publicznej Azure. Usługa Azure Government ani inne suwerenne chmury nie są obsługiwane.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>PYTANIE: Z kim należy się skontaktować, gdy pojawią się jakiekolwiek problemy z urządzeniami Data Box Disk?
ODPOWIEDŹ: Jeśli wystąpią problemy z urządzeniami Data Box Disk, skontaktuj się z [pomocą techniczną usługi Data Box Disk](mailto:expresspodsupport@microsoft.com).

## <a name="configure-and-connect"></a>Konfigurowanie i łączenie
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>PYTANIE: Czy mogę określić liczbę urządzeń Data Box Disk w zamówieniu?
ODPOWIEDŹ:  Nie. Otrzymasz maksymalnie 5 dysków o rozmiarze 8 TB każdy, w zależności od rozmiaru danych i dostępności dysków.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>PYTANIE: Jak mogę odblokować urządzenia Data Box Disk? 
ODPOWIEDŹ:  W witrynie Azure Portal przejdź do zamówienia urządzenia Data Box Disk, a następnie wybierz pozycję **Szczegóły urządzenia**. Skopiuj klucz dostępu. Pobierz i wyodrębnij narzędzie do odblokowywania urządzeń Data Box Disk z witryny Azure Portal i uruchom program *DataBoxDiskUnlock.exe* na komputerze zawierającym dane, które chcesz skopiować na dyski. Podaj klucz dostępu, aby odblokować dyski. Ten sam klucz dostępu odblokowuje wszystkie dyski.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>PYTANIE: Czy mogę użyć komputera-hosta z systemem Linux, aby nawiązać połączenie i skopiować dane na urządzenia Data Box Disk?
ODPOWIEDŹ:  Nie. Obsługiwane są wyłącznie komputery z systemem Windows. Aby uzyskać więcej informacji, przejdź do listy [Obsługiwane systemy operacyjne](data-box-disk-system-requirements.md) komputera-hosta.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>PYTANIE: Moje dyski zostały wysłane, ale teraz chcę anulować to zamówienie. Dlaczego przycisk Anuluj nie jest dostępny?
ODPOWIEDŹ:  Zamówienie można anulować tylko po zamówieniu dysków, ale przed ich wysyłką. Po wysłaniu dysków nie można już anulować zamówienia. W okresie obowiązywania wersji zapoznawczej dyski można zwrócić bez opłat, jednak najprawdopodobniej ulegnie to zmianie, gdy rozwiązanie będzie ogólnie dostępne. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>PYTANIE: Czy mogę podłączyć wiele urządzeń Data Box Disk do tego samego komputera-hosta w celu transferu danych?
ODPOWIEDŹ: Tak. Do jednego komputera-hosta można podłączyć wiele urządzeń Data Box Disk na potrzeby transferu danych i można równolegle wykonywać wiele zadań kopiowania.

## <a name="track-status"></a>Śledzenie stanu

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>PYTANIE: Jak mogę śledzić stan dysków od momentu złożenia zamówienia do ich odesłania? 
ODPOWIEDŹ:  Stan zamówienia urządzenia Data Box Disk możesz śledzić w witrynie Azure Portal. Podczas tworzenia zamówienia jest również wyświetlany monit o podanie adresu e-mail do powiadomień. Jeśli go podasz, będziesz otrzymywać powiadomienia e-mail o każdej zmianie stanu zamówienia. Więcej informacji na temat [konfigurowania wiadomości e-mail z powiadomieniami](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>PYTANIE: Jak mogę zwrócić dyski? 
ODPOWIEDŹ:  Firma Microsoft dołącza etykietę wysyłkową do przesyłki z urządzeniami Data Box Disk. Przyklej etykietę do opakowania wysyłkowego i dostarcz zapieczętowaną paczkę do firmy przewozowej. Jeśli etykieta została uszkodzona lub zgubiona, wybierz pozycję **Przegląd > Pobierz etykietę wysyłkową** i pobierz nową zwrotną etykietę wysyłkową.

## <a name="migrate-data"></a>Migrowanie danych

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>PYTANIE: Jaki jest maksymalny rozmiar danych w przypadku urządzeń Data Box Disk?  
ODPOWIEDŹ:  Rozwiązanie Data Box Disk może zapewnić do 5 dysków o maksymalnej pojemności do wykorzystania wynoszącej 35 TB. Same dyski mają rozmiar 8 TB (przy czym rozmiar do wykorzystania to 7 TB). 

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>PYTANIE: Jakie są maksymalne rozmiary blokowych obiektów blob i stronicowych obiektów blob obsługiwane przez urządzenia Data Box Disk ? 
ODPOWIEDŹ:  Maksymalne rozmiary podlegają limitom usługi Azure Storage. Maksymalny rozmiar blokowego obiektu blob to około 4,768 TiB, a maksymalny rozmiar stronicowego obiektu blob to 8 TiB. Aby uzyskać więcej informacji, zobacz [Cele usługi Azure Storage dotyczące skalowalności i wydajności](../storage/common/storage-scalability-targets.md). 

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>PYTANIE: Jaka jest szybkość transferu danych w przypadku urządzeń Data Box Disk?
ODPOWIEDŹ: Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>PYTANIE: Skąd mam wiedzieć, że moje dane są bezpieczne podczas transportu? 
ODPOWIEDŹ:  Urządzenia Data Box Disk są zaszyfrowane za pomocą funkcji BitLocker z użyciem 128-bitowego algorytmu AES, a klucz dostępu jest dostępny tylko w witrynie Azure Portal. Zaloguj się do witryny Azure Portal przy użyciu poświadczeń konta, aby uzyskać klucz dostępu. Podaj ten klucz dostępu podczas uruchamiania narzędzia do odblokowywania urządzenia Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>PYTANIE: Jak mogę skopiować dane na urządzenia Data Box Disk? 
ODPOWIEDŹ:  Dane możesz skopiować na dyski za pomocą narzędzia kopiowania SMB, takiego jak Robocopy, Diskboss lub nawet funkcji przeciągnij i upuść Eksploratora plików systemu Windows. 

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>PYTANIE: Czy istnieją jakieś porady dotyczące przyspieszania kopiowania danych?
ODPOWIEDŹ:  Aby przyspieszyć proces kopiowania:

- Użyj wielu strumieni kopiowania danych. Na przykład w narzędziu Robocopy włącz opcję kopiowania wielowątkowego. Aby uzyskać więcej informacji na temat konkretnego polecenia, zobacz [Tutorial: Copy data to Azure Data Box Disk and verify (Samouczek: kopiowanie danych na urządzenie Data Box Disk i ich weryfikacja)](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Użyj wielu sesji.
- Zamiast kopiować za pośrednictwem udziału sieciowego (gdzie szybkość sieci może powodować ograniczenia), upewnij się, że dane znajdują się lokalnie na komputerze, do którego podłączono dyski.
- Przez cały proces kopiowania korzystaj z interfejsu USB 3.0 lub nowszego. Pobierz [narzędzie USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) i za jego pomocą zidentyfikuj kontrolery USB oraz urządzenia USB podłączone do komputera.
- Wykonaj test porównawczy wydajności komputera używanego do kopiowania danych. Pobierz [narzędzie Bluestop FIO](https://bluestop.org/fio/) i za jego pomocą przeprowadź test porównawczy wydajności sprzętu serwera.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>PYTANIE: Jak przyspieszyć kopiowanie danych, jeśli dane źródłowe składają się z małych plików (o rozmiarze wyrażonym w kilobajtach lub równym kilka megabajtów)?
ODPOWIEDŹ:  Aby przyspieszyć proces kopiowania:

- Utwórz lokalny dysk VHDX w szybkim magazynie lub utwórz pusty dysk VHD na dysku twardym lub SSD (jest to wolniejsze).
- Zainstaluj go na maszynie wirtualnej.
- Skopiuj pliki na dysk maszyny wirtualnej.


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>PYTANIE: Czy mogę używać wielu kont magazynu z urządzeniami Data Box Disk?
ODPOWIEDŹ:  Nie. Obecnie na potrzeby urządzeń Data Box Disk jest obsługiwane tylko jedno konto magazynu, ogólne lub klasyczne. Obsługiwane są obiekty blob zarówno w warstwie Gorąca, jak i Chłodna. W okresie obowiązywania wersji zapoznawczej są obsługiwane tylko konta magazynu w regionach Stanów Zjednoczonych oraz regionach Europa Zachodnia i Europa Północna chmury publicznej platformy Azure.

## <a name="verify-and-upload"></a>Weryfikowanie i przekazywanie

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>PYTANIE: Jak szybko uzyskam dostęp do moich danych na platformie Azure po odesłaniu dysków? 
ODPOWIEDŹ:  Dostęp do danych powinien być możliwy natychmiast po zmianie stanu zamówienia dla pozycji Kopiowanie danych na Ukończono.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>PYTANIE: Gdzie znajdują się moje dane na platformie Azure po przekazaniu?
ODPOWIEDŹ:  Jeśli skopiowano dane do folderów *BlockBlob* i *PageBlob* na dysku, w ramach konta usługi Azure Storage jest tworzony kontener dla każdego podfolderu w folderze *BlockBlob* i *PageBlob*. Jeśli pliki zostały skopiowane bezpośrednio do folderów *BlockBlob* i *PageBlob*, zostaną one umieszczone w domyślnym kontenerze *$root* na koncie usługi Azure Storage. 

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>PYTANIE: Nazwy moich kontenerów nie są zgodne z wymaganiami dotyczącymi nazewnictwa platformy Azure. Czy przekazywanie moich danych na platformę Azure nie powiedzie się?
ODPOWIEDŹ: Jeśli nazwy kontenerów zawierają wielkie litery, zostaną one automatycznie przekonwertowane w małe litery. Jeśli nazwy nie są zgodne w inny sposób (znaki specjalne, inne języki itd.), przekazywanie zakończy się niepowodzeniem.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>PYTANIE: Jak mogę zweryfikować dane skopiowane na wiele urządzeń Data Box Disk?
ODPOWIEDŹ:  Po zakończeniu kopiowania danych możesz uruchomić program `AzureExpressDiskService.cmd` znajdujący się w folderze *AzureImportExport* w celu wygenerowania sum kontrolnych na potrzeby walidacji. W przypadku wielu dysków musisz otworzyć okno polecenia i uruchomić to polecenie dla każdego z nich. Pamiętaj, że ta operacja może zająć dużo czasu (kilka godzin) w zależności od rozmiaru danych.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>PYTANIE: Co się stanie z moimi danymi po zwróceniu dysków?
ODPOWIEDŹ:  Po zakończeniu kopiowania danych na platformę Azure dane z dysków są bezpiecznie wymazywane zgodnie z wytycznymi NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>PYTANIE: Jak moje dane są chronione w transporcie? 
ODPOWIEDŹ:  Urządzenia Data Box Disk są zaszyfrowane za pomocą funkcji Microsoft BitLocker z użyciem algorytmu AES-128. Do odblokowania dysków i uzyskania dostępu do danych jest wymagany jeden klucz dostępu.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>PYTANIE: Czy muszę ponownie uruchomić walidację sumy kontrolnej, jeśli dodam więcej danych do urządzenia Data Box Disk?
ODPOWIEDŹ: Tak. Jeśli zdecydujesz się na walidację danych (zdecydowanie to zalecamy!), musisz ponownie uruchomić walidację po dodaniu danych do dysków.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>PYTANIE: Transferowane dane zajęły miejsce na wszystkich dyskach i muszę zamówić więcej dysków. Czy można szybko złożyć zamówienie?
ODPOWIEDŹ: W takim przypadku możesz sklonować poprzednie zamówienie. Klonowanie tworzy takie samo zamówienie i możliwe jest edytowanie jego szczegółów bez konieczności wpisywania adresu, danych kontaktowych i danych dotyczących powiadomień. 



## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe usługi Data Box](data-box-disk-system-requirements.md).
- Poznaj [ograniczenia usługi Data Box](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.
