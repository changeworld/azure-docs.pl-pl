---
title: Microsoft Azure Data Box Disk — często zadawane pytania | Microsoft Docs — dane
description: Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Data Box Disk, rozwiązania w chmurze umożliwiającego przenoszenie dużych ilości danych na platformę Azure, oraz odpowiedzi na te pytania
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 7ba6ea8606fc354527ff4114bc45a0904941ba93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918941"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Dysk pola danych platformy Azure: często zadawane pytania

Rozwiązanie w chmurze Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób. Często zadawane pytania zawierają pytania, które mogą się pojawić podczas korzystania z usługi Data Box Disk w witrynie Azure Portal, oraz odpowiedzi na te pytania. 

Pytania i odpowiedzi są podzielone na następujące kategorie:

- Informacje o usłudze
- Konfigurowanie i łączenie 
- Śledzenie stanu
- Migrowanie danych 
- Weryfikowanie i przekazywanie danych 


## <a name="about-the-service"></a>Informacje o usłudze

### <a name="q-what-is-azure-data-box-service"></a>PYTANIE: Co to jest usługa Azure Data Box? 
A.  Usługa Azure Data Box jest przeznaczona do pozyskiwania danych w trybie offline. Ta usługa zarządza szeregiem produktów dostosowanych do transportu danych dla różnych pojemności magazynu. 

### <a name="q-what-are-azure-data-box-disks"></a>PYTANIE: Co to są urządzenia Azure Data Box Disk?
A. Urządzenia Azure Data Box Disk umożliwiają szybki, niedrogi i bezpieczny transfer terabajtów danych do i z platformy Azure. Firma Microsoft dostarcza od 1 do 5 dysków o maksymalnej pojemności magazynu wynoszącej 35 TB. Za pomocą usługi Data Box w witrynie Azure Portal możesz łatwo konfigurować, łączyć i odblokowywać te dyski.  

Dyski są szyfrowane za pomocą funkcji szyfrowania dysków Microsoft BitLocker, a klucze szyfrowania są zarządzane w witrynie Azure Portal. Następnie kopiujesz dane z serwerów klientów. W centrum danych firma Microsoft migruje dane z dysku do chmury przy użyciu szybkiego połączenia przekazywania w sieci prywatnej i przekazuje je na platformę Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>PYTANIE: Kiedy używać urządzeń Data Box Disk?
A. Jeśli masz nie więcej niż 40 TB danych, które chcesz przenieść na platformę Azure, korzystne będzie zastosowanie urządzeń Data Box Disk.

### <a name="q-what-is-the-price-of-data-box-disks"></a>PYTANIE: Jaki jest koszt urządzeń Data Box Disk?
A. Aby uzyskać informacje o cenach urządzeń Data Box Disk, przejdź do [strony cennika](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>PYTANIE: Jak mogę otrzymać urządzenia Data Box Disk? 
A.  Aby otrzymać urządzenia Azure Data Box Disk, zaloguj się do witryny Azure Portal i utwórz zamówienie dysków w usłudze Data Box. Podaj informacje kontaktowe i szczegóły dotyczące powiadomień. Po złożeniu zamówienia, w zależności od dostępności, dyski zostaną dostarczone do Ciebie w ciągu 10 dni.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>PYTANIE: Jaka jest maksymalna ilość danych, które mogę jednorazowo przenieść za pomocą urządzeń Data Box Disk?
A. W przypadku 5 dysków — każdy o rozmiarze 8 TB (7 TB pojemności do wykorzystania) — maksymalna pojemność do wykorzystania to 35 TB. W związku z tym jednorazowo możesz przenieść 35 TB danych. Aby przenieść większą ilość danych, musisz zamówić więcej dysków.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>PYTANIE: Jak sprawdzić, czy urządzenia Data Box Disk są dostępne w moim regionie? 
A.  Aby zobaczyć, gdzie dyski skrzynek danych są obecnie dostępne, przejdź do [regionu dostępności](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>PYTANIE: W jakich regionach mogę zapisać dane, korzystając z usługi Data Box Disk?
A. Dysk data box jest obsługiwany dla wszystkich regionów w USA, Kanadzie, Australii, Europie Zachodniej i Europie Północnej, Korei i Japonii. Obsługiwane są tylko regiony chmury publicznej Azure. Usługa Azure Government ani inne suwerenne chmury nie są obsługiwane.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>PYTANIE: Czy mój dysk skrzynki danych przekroczy granice kraju podczas wysyłki?
A. Dysk skrzynki danych są wysyłane z tego samego kraju co ich miejsce docelowe i nie przekraczają żadnych granic międzynarodowych. Jedynym wyjątkiem są zamówienia w Unii Europejskiej (UE), gdzie dyski mogą wysyłać do i z dowolnego kraju UE.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>PYTANIE: Z kim należy się skontaktować, gdy pojawią się jakiekolwiek problemy z urządzeniami Data Box Disk?
A. Jeśli napotkasz jakiekolwiek problemy z usługą Data Box Disk, [skontaktuj się z działem pomocy technicznej firmy Microsoft](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Konfigurowanie i łączenie
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>PYTANIE: Czy mogę określić liczbę urządzeń Data Box Disk w zamówieniu?
A.  Nie. Otrzymasz maksymalnie 5 dysków o rozmiarze 8 TB każdy, w zależności od rozmiaru danych i dostępności dysków.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>PYTANIE: Jak mogę odblokować urządzenia Data Box Disk? 
A.  W witrynie Azure Portal przejdź do zamówienia urządzenia Data Box Disk, a następnie wybierz pozycję **Szczegóły urządzenia**. Skopiuj klucz dostępu. W witrynie Azure Portal pobierz i wyodrębnij narzędzie do odblokowywania dysków Data Box Disk dla Twojego systemu operacyjnego. Uruchom narzędzie na komputerze zawierającym dane, które chcesz skopiować na dyski. Podaj klucz dostępu, aby odblokować dyski. Ten sam klucz dostępu odblokowuje wszystkie dyski. 

Aby uzyskać instrukcje krok po kroku, zobacz [Odblokowywanie dysków na komputerze klienckim z systemem Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) lub [Odblokowywanie dysków na komputerze klienckim z systemem Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>PYTANIE: Czy mogę użyć komputera-hosta z systemem Linux, aby nawiązać połączenie i skopiować dane na urządzenia Data Box Disk?
A.  Tak. Zarówno komputer kliencki z systemem Linux, jak i z systemem Windows może zostać użyty do nawiązania połączenia i skopiowania danych na urządzenia Data Box Disk. Aby uzyskać więcej informacji, przejdź do listy [Obsługiwane systemy operacyjne](data-box-disk-system-requirements.md) komputera-hosta.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>PYTANIE: Moje dyski zostały wysłane, ale teraz chcę anulować to zamówienie. Dlaczego przycisk Anuluj nie jest dostępny?
A.  Zamówienie można anulować tylko po zamówieniu dysków, ale przed ich wysyłką. Po wysłaniu dysków nie można już anulować zamówienia. Można jednak zwrócić dyski za odpowiednią opłatą. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>PYTANIE: Czy mogę podłączyć wiele urządzeń Data Box Disk do tego samego komputera-hosta w celu transferu danych?
A. Tak. Do jednego komputera-hosta można podłączyć wiele urządzeń Data Box Disk na potrzeby transferu danych i można równolegle wykonywać wiele zadań kopiowania.

## <a name="track-status"></a>Śledzenie stanu

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>PYTANIE: Jak mogę śledzić stan dysków od momentu złożenia zamówienia do ich odesłania? 
A.  Stan zamówienia urządzenia Data Box Disk możesz śledzić w witrynie Azure Portal. Podczas tworzenia zamówienia jest również wyświetlany monit o podanie adresu e-mail do powiadomień. Jeśli go podasz, będziesz otrzymywać powiadomienia e-mail o każdej zmianie stanu zamówienia. Więcej informacji na temat [konfigurowania wiadomości e-mail z powiadomieniami](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>PYTANIE: Jak mogę zwrócić dyski? 
A.  Firma Microsoft dołącza etykietę wysyłkową do przesyłki z urządzeniami Data Box Disk. Przyklej etykietę do opakowania wysyłkowego i dostarcz zapieczętowaną paczkę do firmy przewozowej. Jeśli etykieta została uszkodzona lub zgubiona, wybierz pozycję **Przegląd > Pobierz etykietę wysyłkową** i pobierz nową zwrotną etykietę wysyłkową.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Czy mogę samodzielnie odebrać zamówienie na dysk z polem danych? Czy mogę zwrócić dyski za pośrednictwem wybranego przeze mnie operatora?
A. Tak. Firma Microsoft oferuje również samodzielną wysyłkę tylko w regionie gov w USA. Podczas składania zamówienia na dysku pola danych można wybrać opcję wysyłki zarządzanej samodzielnie. Aby odebrać zamówienie dysku skrzynki danych, należy wykonać następujące czynności:
    
1. Po złożeniu zamówienia zamówienie jest przetwarzane i przygotowywane dyski. Zostaniesz powiadomiony za pośrednictwem wiadomości e-mail, że twoje zamówienie jest gotowe do odbioru. 
2. Gdy zamówienie jest gotowe do odbioru, przejdź do zamówienia w witrynie Azure portal i przejdź do **bloku Przegląd.** 
3. Zostanie wyświetlone powiadomienie z kodem w witrynie Azure portal. Wyślij wiadomość e-mail do [zespołu operacji usługi Azure Data Box](mailto:adbops@microsoft.com) i podaj im kod. Zespół poda lokalizację i zaplanuje datę i godzinę odbioru. Musisz zadzwonić do zespołu w ciągu 5 dni roboczych od otrzymania powiadomienia e-mail.

Po zakończeniu kopiowania i sprawdzania poprawności danych wykonaj następujące kroki, aby zwrócić dysk:

1. Po zakończeniu sprawdzania poprawności danych odłącz dyski. Odłącz kable połączeniowe.
2. Zapakuj wszystkie dyski i kable połączeniowe w folię bąbelkową, a następnie umieść w opakowaniu wysyłkowym. W przypadku braku akcesoriów mogą zostać naliczone opłaty.

    - Ponownie użyj opakowania z pierwotnej wysyłki. Zalecamy zabezpieczenie dysków przy użyciu folii bąbelkowej.
    - Upewnij się, że wszystko jest dopasowane i żaden element nie przemieszcza się w opakowaniu.
3. Przejdź do **bloku Przegląd** dla zamówienia w witrynie Azure portal. Powinno zostać wyświetlone powiadomienie z kodem.
4. Użyj tego kodu i wyślij wiadomość e-mail [do zespołu operacji usługi Azure Data Box](mailto:adbops@microsoft.com) i podaj im kod. Dostarczają one informacji o tym, gdzie i kiedy zasnąć dyski.


## <a name="migrate-data"></a>Migrowanie danych

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>PYTANIE: Jaki jest maksymalny rozmiar danych w przypadku urządzeń Data Box Disk?  
A.  Rozwiązanie Data Box Disk może zapewnić do 5 dysków o maksymalnej pojemności do wykorzystania wynoszącej 35 TB. Same dyski mają rozmiar 8 TB (przy czym rozmiar do wykorzystania to 7 TB).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>PYTANIE: Jakie są maksymalne rozmiary blokowych obiektów blob i stronicowych obiektów blob obsługiwane przez urządzenia Data Box Disk ? 
A.  Maksymalne rozmiary podlegają limitom usługi Azure Storage. Maksymalny rozmiar blokowego obiektu blob to około 4,768 TiB, a maksymalny rozmiar stronicowego obiektu blob to 8 TiB. Aby uzyskać więcej informacji, zobacz [Skalowalność i cele wydajności dla magazynu obiektów Blob](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>PYTANIE: Jaka jest szybkość transferu danych w przypadku urządzeń Data Box Disk?
A. Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>PYTANIE: Skąd mam wiedzieć, że moje dane są bezpieczne podczas transportu? 
A.  Urządzenia Data Box Disk są zaszyfrowane za pomocą funkcji BitLocker z użyciem 128-bitowego algorytmu AES, a klucz dostępu jest dostępny tylko w witrynie Azure Portal. Zaloguj się do witryny Azure Portal przy użyciu poświadczeń konta, aby uzyskać klucz dostępu. Podaj ten klucz dostępu podczas uruchamiania narzędzia do odblokowywania urządzenia Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>PYTANIE: Jak mogę skopiować dane na urządzenia Data Box Disk? 
A.  Dane możesz skopiować na dyski za pomocą narzędzia kopiowania SMB, takiego jak Robocopy, Diskboss lub nawet funkcji przeciągnij i upuść Eksploratora plików systemu Windows.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>PYTANIE: Czy istnieją jakieś porady dotyczące przyspieszania kopiowania danych?
A.  Aby przyspieszyć proces kopiowania:

- Użyj wielu strumieni kopiowania danych. Na przykład w narzędziu Robocopy włącz opcję kopiowania wielowątkowego. Aby uzyskać więcej informacji na temat konkretnego polecenia, zobacz [Tutorial: Copy data to Azure Data Box Disk and verify (Samouczek: kopiowanie danych na urządzenie Data Box Disk i ich weryfikacja)](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Użyj wielu sesji.
- Zamiast kopiować za pośrednictwem udziału sieciowego (gdzie szybkość sieci może powodować ograniczenia), upewnij się, że dane znajdują się lokalnie na komputerze, do którego podłączono dyski.
- Przez cały proces kopiowania korzystaj z interfejsu USB 3.0 lub nowszego. Pobierz [narzędzie USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) i za jego pomocą zidentyfikuj kontrolery USB oraz urządzenia USB podłączone do komputera.
- Wykonaj test porównawczy wydajności komputera używanego do kopiowania danych. Pobierz [narzędzie Bluestop FIO](https://ci.appveyor.com/project/axboe/fio) i za jego pomocą przeprowadź test porównawczy wydajności sprzętu serwera. Wybierz najnowszą kompilację x86 lub x64, wybierz kartę **Artefakty** i pobierz aplikację MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>PYTANIE: Jak przyspieszyć kopiowanie danych, jeśli dane źródłowe składają się z małych plików (o rozmiarze wyrażonym w kilobajtach lub równym kilka megabajtów)?
A.  Aby przyspieszyć proces kopiowania:

- Utwórz lokalny dysk VHDX w szybkim magazynie lub utwórz pusty dysk VHD na dysku twardym lub SSD (jest to wolniejsze).
- Zainstaluj go na maszynie wirtualnej.
- Skopiuj pliki na dysk maszyny wirtualnej.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>PYTANIE: Czy mogę używać wielu kont magazynu z urządzeniami Data Box Disk?
A.  Nie. Obecnie na potrzeby urządzeń Data Box Disk jest obsługiwane tylko jedno konto magazynu, ogólne lub klasyczne. Obsługiwane są obiekty blob zarówno w warstwie Gorąca, jak i Chłodna. Obecnie są obsługiwane tylko konta magazynu w regionach Stanów Zjednoczonych oraz regionach Europa Zachodnia i Europa Północna chmury publicznej platformy Azure.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>PYTANIE: Jaki zestaw narzędzi jest dostępny dla moich danych na urządzeniach Data Box Disk?
A. Zestaw narzędzi dostępny na urządzeniu Data Box Disk zawiera trzy narzędzia:
 - **Narzędzie do odblokowywania dysków w polu danych:** Użyj tego narzędzia, aby odblokować zaszyfrowane dyski, które są wysyłane z firmy Microsoft. W przypadku odblokowywania dysków za pomocą narzędzia musisz podać klucz dostępu udostępniony w zamówieniu urządzenia Data Box Disk w witrynie Azure Portal. 
 - **Narzędzie sprawdzania poprawności dysku pola danych:** Użyj tego narzędzia do sprawdzania poprawności nazw rozmiarów, formatów i obiektów blob zgodnie z konwencjami nazewnictwa platformy Azure. Generuje ono sumy kontrolne skopiowanych danych, które następnie są używane do weryfikowania danych przekazywanych na platformie Azure.
 - **Narzędzie do kopiowania podzielonego dysku pole danych:** Użyj tego narzędzia, gdy używasz wielu dysków i masz duży zestaw danych, który musi zostać podzielony i skopiowany na wszystkie dyski. To narzędzie jest obecnie dostępne dla systemu Windows. To narzędzie nie jest obsługiwane z dyskami zarządzanymi. To narzędzie sprawdza również, jak kopiuje dane, w związku z tym można pominąć krok sprawdzania poprawności podczas korzystania z tego narzędzia.

Zestaw narzędzi jest dostępny dla systemów Windows i Linux. Zestaw narzędzi można pobrać w tym miejscu:
- [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Windows](https://aka.ms/databoxdisktoolswin) 
- [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>PYTANIE: Czy można używać urządzenia Data Box Disk do przesyłania danych do usługi Azure Files, a następnie używać danych w usłudze Azure File Sync? 
A. Usługa Azure Files są obsługiwane za pomocą dysku pola danych, ale nie będą działać dobrze z synchronizacją plików platformy Azure. Metadane nie są zachowywane, jeśli dane pliku są używane z synchronizacją plików azure.


## <a name="verify-and-upload"></a>Weryfikowanie i przekazywanie

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>PYTANIE: Jak szybko uzyskam dostęp do moich danych na platformie Azure po odesłaniu dysków? 
A.  Dostęp do danych powinien być możliwy natychmiast po zmianie stanu zamówienia dla pozycji Kopiowanie danych na Ukończono.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>PYTANIE: Gdzie znajdują się moje dane na platformie Azure po przekazaniu?
A.  Jeśli skopiowano dane do folderów *BlockBlob* i *PageBlob* na dysku, w ramach konta usługi Azure Storage jest tworzony kontener dla każdego podfolderu w folderze *BlockBlob* i *PageBlob*. Jeśli pliki zostały skopiowane bezpośrednio do folderów *BlockBlob* i *PageBlob*, zostaną one umieszczone w domyślnym kontenerze *$root* na koncie usługi Azure Storage. Podczas kopiowania danych do folderu w folderze *AzureFile* tworzony jest udział plików.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>PYTANIE: Nazwy moich kontenerów nie są zgodne z wymaganiami dotyczącymi nazewnictwa platformy Azure. Czy przekazywanie moich danych na platformę Azure nie powiedzie się?
A. Jeśli nazwy kontenerów zawierają wielkie litery, zostaną one automatycznie przekonwertowane w małe litery. Jeśli nazwy nie są zgodne w inny sposób (znaki specjalne, inne języki itd.), przekazywanie zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Konwencje nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>PYTANIE: Jak mogę zweryfikować dane skopiowane na wiele urządzeń Data Box Disk?
A.  Po zakończeniu kopiowania danych możesz uruchomić plik `DataBoxDiskValidation.cmd` znajdujący się w folderze *DataBoxDiskImport* w celu wygenerowania sum kontrolnych na potrzeby walidacji. W przypadku wielu dysków musisz otworzyć okno polecenia i uruchomić to polecenie dla każdego z nich. Pamiętaj, że ta operacja może zająć dużo czasu (kilka godzin) w zależności od rozmiaru danych.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>PYTANIE: Co się stanie z moimi danymi po zwróceniu dysków?
A.  Po zakończeniu kopiowania danych na platformę Azure dane z dysków są bezpiecznie wymazywane zgodnie z wytycznymi NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>PYTANIE: Jak moje dane są chronione w transporcie? 
A.  Urządzenia Data Box Disk są zaszyfrowane za pomocą funkcji Microsoft BitLocker z użyciem algorytmu AES-128. Do odblokowania dysków i uzyskania dostępu do danych jest wymagany jeden klucz dostępu.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>PYTANIE: Czy muszę ponownie uruchomić walidację sumy kontrolnej, jeśli dodam więcej danych do urządzenia Data Box Disk?
A. Tak. Jeśli zdecydujesz się na walidację danych (zdecydowanie to zalecamy!), musisz ponownie uruchomić walidację po dodaniu danych do dysków.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>PYTANIE: Transferowane dane zajęły miejsce na wszystkich dyskach i muszę zamówić więcej dysków. Czy można szybko złożyć zamówienie?
A. W takim przypadku możesz sklonować poprzednie zamówienie. Klonowanie tworzy takie samo zamówienie i możliwe jest edytowanie jego szczegółów bez konieczności wpisywania adresu, danych kontaktowych i danych dotyczących powiadomień.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>PYTANIE: Skopiowałem dane do folderu ManagedDisk. Nie widzę żadnych dysków zarządzanych z grupą zasobów określoną dla dysków zarządzanych. Czy moje dane zostały przesłane na platformę Azure i jak je zlokalizować?
A. Tak. Dane zostały przekazane na platformę Azure, ale jeśli nie widzisz żadnych dysków zarządzanych z określonymi grupami zasobów, prawdopodobnie, ponieważ dane nie były prawidłowe. Jeśli obiekty blob strony, blok obiektów blob, usługi Azure Files i dysków zarządzanych były nieprawidłowe, zostaną one przejść do następujących folderów:
 - Obiekty BLOB strony przechodziłyby do kontenera bloku obiektów blob, zaczynając od *databoxdisk-invalid-pb-*.
 - Usługa Azure Files przejdzie do kontenera bloku obiektów blob, zaczynając od *databoxdisk-invalid-af-*.
 - Dyski zarządzane przechodziłyby do kontenera bloku obiektów blob, zaczynając od *databoxdisk-invalid-md-*.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami systemowymi dysku pola danych](data-box-disk-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Disk](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.
