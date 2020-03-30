---
title: Migracja serii StorSimple 8000 do usługi Azure File Sync
description: Dowiedz się, jak przeprowadzić migrację urządzenia StorSimple 8100 lub 8600 do usługi Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7e5f70d0323aa5c502491ab99db303fde31ade83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528629"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migracja storsimple 8100 i 8600 do synchronizacji plików platformy Azure

Seria StorSimple 8000 jest reprezentowana przez urządzenia fizyczne 8100 lub 8600 i ich składniki usługi w chmurze. Istnieje możliwość migracji danych z jednego z tych urządzeń do środowiska synchronizacji plików platformy Azure. Usługa Azure File Sync to domyślna i strategiczna długoterminowa usługa platformy Azure, do którą można migrować urządzenia StorSimple.

Seria StorSimple 8000 dobiegnie [końca](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) w grudniu 2022 roku. Ważne jest, aby rozpocząć planowanie migracji tak szybko, jak to możliwe. Ten artykuł zawiera niezbędną wiedzę w tle i kroki migracji dla pomyślnej migracji do usługi Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Firma Microsoft dokłada wszelkich starań, aby pomóc klientom w ich migracji. Wyślij AzureFilesMigration@microsoft wiadomość e-mail do .com w celu uzyskania dostosowanego planu migracji oraz pomocy podczas migracji.

Usługa Azure File Sync to usługa w chmurze firmy Microsoft oparta na dwóch głównych składnikach:

* Synchronizacja plików i warstwowanie w chmurze.
* Udziały plików jako magazynu macierzystego na platformie Azure, które mogą być dostępne za pośrednictwem wielu protokołów, takich jak SMB i rest pliku. Udział plików platformy Azure jest porównywalny z udziałem plików w systemie Windows Server, który można natywnie zainstalować jako dysk sieciowy. Obsługuje ważne aspekty wierności plików, takie jak atrybuty, uprawnienia i sygnatury czasowe. W przypadku udziałów plików platformy Azure nie ma już potrzeby interpretowania plików i folderów przechowywanych w chmurze przez aplikację lub usługę. Można uzyskać do nich dostęp natywnie za korzystać ze znanych protokołów i klientów, takich jak Eksplorator plików Windows. To sprawia, że udziały plików platformy Azure są idealnym i najbardziej elastycznym podejściem do przechowywania danych serwera plików ogólnego przeznaczenia, a także niektórych danych aplikacji w chmurze.

W tym artykule skupiono się na krokach migracji. Jeśli przed migracją chcesz dowiedzieć się więcej o usłudze Azure File Sync, zalecamy następujące artykuły:

* [Usługa Azure File Sync — omówienie](https://aka.ms/AFS "Omówienie")
* [Usługa Azure File Sync — przewodnik po wdrażaniu](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cele migracji

Celem jest zagwarantowanie integralności danych produkcyjnych, a także zagwarantowanie dostępności. Ten ostatni wymaga utrzymania przestojów do minimum, tak aby mógł zmieścić się w lub tylko nieznacznie przekroczyć regularne okna konserwacji.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Ścieżka migracji storsimple serii 8000 do synchronizacji plików platformy Azure

Do uruchomienia agenta usługi Azure File Sync wymagany jest lokalny serwer Windows Server. System Windows Server może być co najmniej serwerem 2012R2, ale najlepiej jest z systemem Windows Server 2019.

Istnieje wiele alternatywnych ścieżek migracji i stworzyłoby to zbyt długi artykuł, aby udokumentować wszystkie z nich i zilustrować, dlaczego ponoszą ryzyko lub wady trasy, którą zalecamy jako najlepszą praktykę w tym artykule.

![Omówienie faz migracji serii StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 serii migracji przeglądu trasy fazy poniżej w tym artykule.")

Poprzedni obraz przedstawia fazy, które odpowiadają sekcjom w tym artykule.
Używamy migracji po stronie chmury, aby uniknąć niepotrzebnego wycofywania plików do lokalnego urządzenia StorSimple. Takie podejście pozwala uniknąć wpływu na zachowanie lokalnego buforowania lub wykorzystanie przepustowości sieci, z których każda może mieć wpływ na obciążenia produkcyjne.
Migracja po stronie chmury działa na migawkę (klon woluminu) danych. Więc dane produkcyjne są odizolowane od tego procesu - do cut-over na koniec migracji. Praca z tego, co jest w istocie kopii zapasowej, sprawia, że migracja bezpieczne i łatwe do powtórzenia, należy napotkać na jakiekolwiek trudności.

## <a name="considerations-around-existing-storsimple-backups"></a>Zagadnienia dotyczące istniejących kopii zapasowych StorSimple

StorSimple umożliwia wykonywanie kopii zapasowych w postaci klonów woluminów. W tym artykule użyto nowego klonu woluminu do migracji plików na żywo.
Jeśli musisz przeprowadzić migrację kopii zapasowych oprócz danych na żywo, wszystkie wskazówki w tym artykule nadal mają zastosowanie. Jedyną różnicą jest to, że zamiast zaczynać od nowego klonu woluminu, zaczniesz od najstarszego klonu woluminu kopii zapasowej, który musisz przeprowadzić migrację.

Kolejność jest następująca:

* Określ minimalny zestaw klonów woluminów, które należy przeprowadzić migrację. Zalecamy zachowanie tej listy do minimum, jeśli to możliwe, ponieważ im więcej kopii zapasowych zostanie migrowane, tym dłużej trwa ogólny proces migracji.
* Podczas przechodzenia przez proces migracji, należy rozpocząć od najstarszego klonu woluminu, który zamierzasz przeprowadzić migrację, a przy każdej kolejnej migracji użyć następnego najstarszego.
* Po zakończeniu każdej migracji klonowania woluminów należy wykonać migawkę udziału plików platformy Azure. [Migawki udziału plików platformy Azure](storage-snapshots-files.md) to sposób przechowywania kopii zapasowych plików i struktury folderów w czasie dla udziałów plików platformy Azure. Te migawki będą potrzebne po zakończeniu migracji, aby upewnić się, że zachowane wersje każdego klonów woluminu w miarę postępów w migracji.
* Upewnij się, że należy wziąć migawki udziału plików platformy Azure dla wszystkich udziałów plików platformy Azure, które są obsługiwane przez ten sam wolumin StorSimple. Klony woluminów są na poziomie woluminu, migawki udziału plików platformy Azure są na poziomie udziału. Po zakończeniu migracji klonu woluminu należy zrobić migawkę udziału (w każdym udziale plików platformy Azure).
* Powtórz proces migracji dla klonu woluminu i robienia migawek udziału po każdym klonu woluminu, aż zostaniesz złapany do migawki danych na żywo. Proces migracji klonu woluminu jest opisany w poniższych fazach. 

Jeśli nie trzeba w ogóle przenosić kopii zapasowych i można uruchomić nowy łańcuch kopii zapasowych po stronie udziału plików platformy Azure po migracji tylko dane na żywo jest wykonywana, a następnie jest to korzystne, aby zmniejszyć złożoność migracji i ilość czasu migracji zajmie. Można podjąć decyzję, czy przenieść kopie zapasowe i ile dla każdego woluminu (nie każdego udziału) masz w StorSimple.

## <a name="phase-1-get-ready"></a>Faza 1: Przygotuj się

:::row:::
    :::column:::
        ![Obraz przedstawiający część wcześniejszego obrazu przeglądu, który pomaga skupić się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Podstawą migracji jest klon woluminu i wirtualne urządzenie w chmurze, o nazwie StorSimple 8020.
Ta faza koncentruje się na wdrażaniu tych zasobów na platformie Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Wdrażanie urządzenia wirtualnego StorSimple 8020

Wdrażanie urządzenia w chmurze jest procesem, który wymaga zabezpieczeń, sieci i kilku innych zagadnień.

> [!IMPORTANT]
> Poniższy przewodnik zawiera kilka niepotrzebnych sekcji. Przeczytaj i postępuj zgodnie z artykułem od początku do "Kroku 3". Następnie wróć do tego artykułu. Nie musisz w tej chwili wypełniać "Kroku 3" ani niczego poza nim w tym przewodniku.

[Wdrożenie urządzenia wirtualnego StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Określanie klonu woluminu, który ma być używany

Gdy będziesz gotowy do rozpoczęcia migracji, pierwszym krokiem jest podjęcie nowego klonu woluminu — tak samo jak w przypadku kopii zapasowej — który przechwytuje bieżący stan magazynu w chmurze StorSimple. Weź klon dla każdego woluminu StorSimple, które masz.
Jeśli potrzebujesz przenoszenia kopii zapasowych, pierwszy klon woluminu, którego używasz, nie jest nowo utworzonym klonem, ale najstarszy klon woluminu (najstarsza kopia zapasowa), który należy przeprowadzić migrację.
Szczegółowe wskazówki można znaleźć w sekcji ["Zagadnienia dotyczące istniejących kopii zapasowych StorSimple".](#considerations-around-existing-storsimple-backups)

> [!IMPORTANT]
> Poniższy przewodnik zawiera kilka niepotrzebnych sekcji. Przeczytaj i wykonaj tylko kroki opisane w sekcji połączonej. Następnie wróć do tego artykułu. Nie musisz postępować zgodnie z sekcją "Następne kroki".

[Tworzenie klonu woluminu](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Użyj klonu woluminu

Ostatnia faza fazy 1 polega na udostępnieniu wybranego klonu woluminu na urządzeniu wirtualnym 8020 na platformie Azure.

> [!IMPORTANT]
> Poniższy przewodnik zawiera niezbędne kroki, ale także - na końcu - instrukcję formatowania woluminu. **NIE FORMATUJ WOLUMINU** Przeczytaj i postępuj zgodnie z powiązaną "sekcją 7" od początku do instrukcji: "10. Aby sformatować wolumin prosty, ..."  Zatrzymaj się przed dokonaniem tego kroku i wróć do tego artykułu.

[Instalowanie klonowania woluminów na urządzeniu wirtualnym 8020 na platformie Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Podsumowanie fazy 1

Po zakończeniu fazy 1 wykonasz następujące czynności:

* Wdrożono urządzenie wirtualne StorSimple 8020 na platformie Azure.
* Określa, który klon woluminu rozpocznie migrację.
* Zainstalowano klony woluminów (po jednym dla każdego woluminu na żywo) na urządzeniu wirtualnym StorSimple na platformie Azure, z jego danymi dostępnymi do dalszego użycia.

## <a name="phase-2-cloud-vm"></a>Faza 2: Maszyna wirtualna w chmurze

:::row:::
    :::column:::
        ![Obraz przedstawiający część wcześniejszego obrazu przeglądu, który pomaga skupić się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Po początkowym klon jest dostępny na StorSimple 8020 urządzenia wirtualnego na platformie Azure, nadszedł czas, aby aprowizować maszynę wirtualną i udostępnić klon woluminu (lub wiele) do tej maszyny wirtualnej za pośrednictwem iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Wdrażanie maszyny Wirtualnej platformy Azure

Maszyna wirtualna systemu Windows Server na platformie Azure jest podobnie jak StorSimple 8020, tymczasowy element infrastruktury, który jest niezbędny tylko podczas migracji.
Konfiguracja maszyny Wirtualnej, którą wdrażasz, zależy głównie od liczby elementów (plików i folderów), które będą synchronizowane. Jeśli masz jakiekolwiek wątpliwości, zalecamy przejście z konfiguracją o wyższej wydajności.

Pojedynczy system Windows Server może synchronizować maksymalnie 30 udziałów plików platformy Azure.
Specyfikacje, które zdecydujesz się na potrzebę obejmują każdy udział / ścieżka lub katalog główny woluminu StorSimple i zliczanie elementów (pliki i foldery).

Ogólny rozmiar danych jest mniejszy wąskim gardłem - jest to liczba elementów, do których musisz dostosować specyfikację maszyny.

* [Dowiedz się, jak rozmiar systemu Windows Server na podstawie liczby elementów (plików i folderów) potrzebnych do synchronizacji.](storage-sync-files-planning.md#recommended-system-resources)

    **Uwaga:** Wcześniej połączony artykuł przedstawia tabelę z zakresem pamięci serwera (RAM). Orientuj się w kierunku dużej liczby maszyny Wirtualnej platformy Azure. Można zorientować się w kierunku mniejszej liczby dla komputera lokalnego.

* [Dowiedz się, jak wdrożyć maszynę wirtualną systemu Windows Sever.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Upewnij się, że maszyna wirtualna jest wdrażana w tym samym regionie platformy Azure co urządzenie wirtualne StorSimple 8020. Jeśli w ramach tej migracji, należy również zmienić region danych w chmurze z regionu, w którym są przechowywane w dniu dzisiejszym, można to zrobić w późniejszym kroku, podczas aprowizowania udziałów plików platformy Azure.

> [!IMPORTANT]
> Aby zoptymalizować pod kątem wydajności, należy wdrożyć **bardzo szybki dysk systemu operacyjnego** dla maszyny Wirtualnej w chmurze. Baza danych synchronizacji zostanie przechowywana na dysku systemu operacyjnego dla wszystkich woluminów danych. Ponadto upewnij się, że utworzono **duży dysk systemu operacyjnego**. W zależności od liczby elementów (plików i folderów) na woluminach StorSimple dysk systemu operacyjnego może potrzebować **kilkuset gib** miejsca, aby pomieścić bazę danych synchronizacji.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Udostępnianie woluminów StorSimple 8020 na maszynie wirtualnej platformy Azure

W tej fazie łączysz jeden lub kilka woluminów StorSimple z urządzenia wirtualnego 8020 za łącze iSCSI z aprowizowanym maszyną wirtualną systemu Windows Server.

> [!IMPORTANT]
> W przypadku następujących artykułów uzupełnij tylko sekcje **Pobierz prywatny adres IP dla urządzenia w chmurze** i **Połącz za pomocą interfejsu iSCSI** i wróć do tego artykułu.

1. [Uzyskiwanie prywatnego adresu IP urządzenia w chmurze](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Łączenie się za pomocą interfejsu iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Podsumowanie fazy 2

Po zakończeniu fazy 2: 

* Aprowizowana maszyna wirtualna systemu Windows Server w tym samym regionie co urządzenie 8020 virtual StorSimple
* Ujawniono wszystkie odpowiednie woluminy z 8020 na maszynę wirtualną systemu Windows Server za cieki iSCSI.
* Zawartość plików i folderów powinna być teraz widoczna podczas używania Eksploratora plików na maszynie Wirtualnej serwera na zainstalowanych woluminach.

Przejdź do fazy 3 tylko po wykonaniu tych kroków dla wszystkich woluminów, które wymagają migracji.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Faza 3: Konfigurowanie udziałów plików platformy Azure i przygotowanie do synchronizacji plików platformy Azure

:::row:::
    :::column:::
        ![Obraz przedstawiający część wcześniejszego obrazu przeglądu, który pomaga skupić się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        W tej fazie będzie określanie i inicjowanie obsługi administracyjnej szereg udziałów plików platformy Azure, tworzenie lokalnego systemu Windows Server jako zamiennik urządzenia StorSimple i konfigurowanie tego serwera dla synchronizacji plików platformy Azure. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapowanie istniejących obszarów nazw na udziały plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Wdrażanie udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Jeśli chcesz zmienić region platformy Azure z bieżącego regionu, w którego znajdują się dane StorSimple, a następnie aprowizować udziały plików platformy Azure w nowym regionie, którego chcesz użyć. Region można określić, wybierając go podczas aprowizowania kont magazynu, które przechowują udziały plików platformy Azure. Upewnij się, że również zasób usługi Azure File Sync, który będzie aprowizować poniżej, znajduje się w tym samym, nowym regionie.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Wdrażanie zasobu chmury usługi Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Jeśli chcesz zmienić region platformy Azure z bieżącego regionu, w którego znajdują się dane StorSimple, a następnie aprowizowana konta magazynu dla udziałów plików platformy Azure w nowym regionie. Upewnij się, że wybrano ten sam region podczas wdrażania tej usługi synchronizacji magazynu.

### <a name="deploy-an-on-premises-windows-server"></a>Wdrażanie lokalnego systemu Windows Server

* Utwórz system Windows Server 2019 — co najmniej 2012R2 — jako maszynę wirtualną lub serwer fizyczny. Obsługiwany jest również klaster trybu failover systemu Windows Server. Nie należy ponownie używać serwera, na który masz fronting StorSimple 8100 lub 8600.
* Aprowizuj lub dodaj bezpośrednio podłączoną pamięć masową (DAS w porównaniu z serwerem NAS, który nie jest obsługiwany).

Najlepszym rozwiązaniem jest zapewnienie nowemu systemowi Windows Server równej lub większej ilości miejsca niż urządzenie StorSimple 8100 lub 8600 jest lokalnie dostępne do buforowania. System Windows Server będzie używany w taki sam sposób, w jaki używane urządzenie StorSimple, jeśli ma taką samą ilość miejsca do magazynowania jak urządzenie, środowisko buforowania powinno być podobne, jeśli nie takie samo.
Magazyn można dodawać lub usuwać z systemu Windows Server do woli. Dzięki temu można skalować rozmiar woluminu lokalnego i ilość magazynu lokalnego dostępnego do buforowania.

### <a name="prepare-the-windows-server-for-file-sync"></a>Przygotowywanie systemu Windows Server do synchronizacji plików

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurowanie synchronizacji plików platformy Azure w systemie Windows Server

Zarejestrowany lokalny system Windows Server musi być gotowy i połączony z Internetem w tym procesie.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Pamiętaj, aby włączyć warstwy w chmurze!** Warstwa chmury to funkcja AFS, która umożliwia serwerowi lokalnemu mniejszą pojemność niż jest przechowywana w chmurze, ale ma dostęp do pełnego obszaru nazw. Lokalnie interesujące dane są również buforowane lokalnie w celu uzyskania szybkiej wydajności dostępu lokalnego. Innym powodem, aby włączyć warstwy w chmurze w tym kroku jest to, że nie chcemy synchronizować zawartości pliku na tym etapie, tylko obszar nazw powinien być w tej chwili w ruchu.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Faza 4: Konfigurowanie maszyny Wirtualnej platformy Azure do synchronizacji

:::row:::
    :::column:::
        ![Obraz przedstawiający część wcześniejszego obrazu przeglądu, który pomaga skupić się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Ta faza dotyczy maszyny Wirtualnej platformy Azure z zainstalowanym iSCSI, klonami pierwszego woluminu. Podczas tej fazy otrzymasz maszynę wirtualną połączoną za pośrednictwem usługi Azure File Sync i rozpoczniesz pierwszą rundę przenoszenia plików z klonów woluminów StorSimple.
        
    :::column-end:::
:::row-end:::

Skonfigurowano już serwer lokalny, który zastąpi urządzenie StorSimple 8100 lub 8600 dla usługi Azure File Sync. 

Konfigurowanie maszyny Wirtualnej platformy Azure jest prawie identyczny proces, z jednym dodatkowym krokiem. Poniższe kroki poprowadzą Cię przez proces.

> [!IMPORTANT]
> Ważne jest, aby maszyna wirtualna platformy Azure nie była **skonfigurowana z włączoną warstwą w chmurze!** Wolumin tego serwera zostanie wymieniony z nowszymi klonami woluminów podczas migracji. Warstwowa chmura nie ma żadnych korzyści i obciążenie użycia procesora CPU, którego należy unikać.

1. [Wdrażanie agenta AFS. (patrz poprzednia sekcja)](#prepare-the-windows-server-for-file-sync)
2. [Przygotowanie maszyny Wirtualnej do synchronizacji plików platformy Azure.](#get-the-vm-ready-for-azure-file-sync)
3. [Konfigurowanie synchronizacji](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Przygotowanie maszyny Wirtualnej do synchronizacji plików platformy Azure

Usługa Azure File Sync służy do przenoszenia plików z zainstalowanych woluminów iSCSI StorSimple do docelowych udziałów plików platformy Azure.
Podczas tego procesu migracji zostanie zamontowanych kilka klonów woluminów na maszynie wirtualnej, pod tą samą literą dysku. Usługa Azure File Sync musi być skonfigurowana tak, aby była instalowana jako nowsza wersja plików i folderów oraz aktualizowana zawartość plików platformy Azure połączona za pośrednictwem usługi Azure File Sync. 

> [!IMPORTANT]
> Aby to działało, klucz rejestru musi być ustawiony na serwerze przed skonfigurowaniem usługi Azure File Sync.

1. Utwórz nowy katalog na dysku systemowym maszyny Wirtualnej. Informacje o synchronizacji plików platformy Azure będą musiały być tam utrwalane, a nie na zainstalowanych klonach woluminu. Na przykład: `"C:\syncmetadata"`
2. Otwórz regedit i znajdź następujący gałąź rejestru:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Utwórz nowy klucz typu Ciąg o nazwie: ***MetadataRootPath***
4. Ustaw pełną ścieżkę do katalogu utworzonego na woluminie systemowym, na przykład:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Konfigurowanie synchronizacji plików platformy Azure na maszynie Wirtualnej platformy Azure

Ten krok jest podobny do poprzedniej sekcji, w której omówiono sposób konfigurowania systemu AFS na serwerze lokalnym.

Różnica polega na tym, że nie należy włączać warstw w chmurze na tym serwerze i że należy upewnić się, że odpowiednie foldery są połączone z odpowiednimi udziałami plików platformy Azure. W przeciwnym razie nazewnictwo udziałów plików platformy Azure i zawartości danych nie będzie zgodne i nie ma możliwości zmiany nazwy zasobów w chmurze lub folderów lokalnych bez ponownej konfiguracji synchronizacji.

Zobacz [poprzednią sekcję dotyczącą konfigurowania synchronizacji plików platformy Azure na serwerze Windows Server](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Podsumowanie kroku 4

W tym momencie pomyślnie skonfigurowano usługę Azure File Sync na maszynie Wirtualnej platformy Azure, która została zainstalowana przez klony woluminów StorSimple za pośrednictwem interfejsu iSCSI.
Dane są teraz przepływa z maszyny Wirtualnej platformy Azure do różnych udziałów plików platformy Azure, a stamtąd w pełni zmęczony obszar nazw pojawia się na lokalnym systemie Windows Server.

> [!IMPORTANT]
> Upewnij się, że obecnie nie wprowadzono żadnych zmian ani dostępu użytkownika do systemu Windows Server.

Początkowe dane klonowania woluminu przechodzące za pośrednictwem maszyny Wirtualnej platformy Azure do udziałów plików platformy Azure może zająć dużo czasu, potencjalnie tygodnie. Oszacowanie czasu, który to zajmie, jest trudne i zależy od wielu czynników. Przede wszystkim szybkość, z jaką maszyna wirtualna platformy Azure może uzyskiwać dostęp do plików na woluminach StorSimple i jak szybko usługa Azure File Sync może przetwarzać pliki i foldery, które wymagają synchronizacji. 

Z doświadczenia możemy założyć, że przepustowość - w związku z tym rzeczywisty rozmiar danych - odgrywa podrzędną rolę. Czas, który zajmie to lub dowolne kolejne rundy migracji, zależy głównie od liczby elementów, które mogą być przetwarzane na sekundę. Na przykład 1 TiB ze 100 000 plików i folderów najprawdopodobniej zakończy się wolniej niż 1 TiB z zaledwie 50 000 plików i folderów.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Faza 5: Iteracji przez klony wielu objętości

:::row:::
    :::column:::
        ![Obraz przedstawiający część wcześniejszego obrazu przeglądu, który pomaga skupić się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Jak wspomniano w poprzedniej fazie, synchronizacja początkowa może zająć dużo czasu. Użytkownicy i aplikacje nadal uzyskują dostęp do lokalnego urządzenia StorSimple 8100 lub 8600. Oznacza to, że zmiany są kumulujące się i każdego dnia większy delta między danymi na żywo i klon woluminu początkowego, jesteś obecnie migracji, formularze. W tej sekcji dowiesz się, jak zminimalizować przestoje przy użyciu wielu klonów woluminów i informując, kiedy synchronizacja jest wykonywana.
    :::column-end:::
:::row-end:::

Niestety proces migracji nie jest natychmiastowy. Oznacza to, że wspomniana delta danych na żywo jest nieuniknioną konsekwencją. Dobrą wiadomością jest to, że można powtórzyć proces montażu nowych klonów objętości. Delta każdego klona woluminu będzie stopniowo mniejsza. Ostatecznie synchronizacja zakończy się w czasie, który można uznać za dopuszczalne dla użytkowników i aplikacji w trybie offline, aby wyciąć na lokalnym serwerze Windows.

Powtarzaj następujące czynności, aż synchronizacja zakończy się w czasie wystarczająco szybkim, aby czuć się komfortowo, przełącz użytkowników i aplikacje do trybu offline:

1. [Określenie, że synchronizacja jest zakończona dla danego klonu woluminu.](#determine-when-sync-is-done)
2. [Weź nowe klony woluminów i zamontuj go na urządzeniu wirtualnym 8020.](#the-next-volume-clones)
3. [Określ, kiedy synchronizacja jest wykonywana.](#determine-when-sync-is-done)
4. [Strategia cięcia](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Klony następnego woluminu

Omówiliśmy przy klon (s) tom wcześniej w tym artykule.
Ta faza ma dwie akcje:

1. [Klonowanie woluminu](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Zamontuj klon woluminu (patrz wyżej)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Określanie, kiedy synchronizacja jest wykonywana

Po zakończeniu synchronizacji można zatrzymać pomiar czasu i określić, czy trzeba powtórzyć proces robienia klonowania woluminu i montowania go, czy synchronizacja czasu z ostatnim klonem woluminu była wystarczająco mała.

W celu określenia, że synchronizacja jest zakończona:

1. Otwórz Podgląd zdarzeń i przejdź do **aplikacji i usług**
2. Nawigowanie i otwieranie programu **Microsoft\FileSync\Agent\Telemetria**
3. Poszukaj najnowszego **zdarzenia 9102**, które odpowiada zakończonej sesji synchronizacji
4. Wybierz **pozycję Szczegóły** i upewnij się, że wartość **SyncDirection** to **Przekazywanie**
5. Sprawdź **HResult** i potwierdzić, że pokazuje **0**. Oznacza to, że sesja synchronizacji zakończyła się pomyślnie. Jeśli HResult jest wartością niezerową, wystąpił błąd podczas synchronizacji. Jeśli **PerItemErrorCount** jest większa niż 0, niektóre pliki lub foldery nie zostały poprawnie zsynchronizowane. Jest możliwe, aby HResult 0, ale PerItemErrorCount, który jest większy niż 0. W tym momencie nie musisz się martwić o PerItemErrorCount. Złapiemy te pliki później. Jeśli ta liczba błędów jest znacząca, tysiące elementów, skontaktuj się z obsługą klienta i poproś o połączenie z grupą produktów usługi Azure File Sync, aby uzyskać bezpośrednie wskazówki dotyczące najlepszych, następnych faz.
6. Sprawdź, aby wyświetlić wiele zdarzeń 9102 z HResult 0 z rzędu. Oznacza to, że synchronizacja została zakończona dla tego klonu woluminu.

### <a name="cut-over-strategy"></a>Strategia cięcia

1. Określ, czy synchronizacja z klonem woluminu jest teraz wystarczająco szybka. (Delta wystarczająco mała.)
2. Przełącz urządzenie StorSimple do trybu offline.
3. Ostateczna RoboCopy.

Zmierz czas i określ, czy synchronizacja z klonem woluminu może zakończyć się w ciągu przedziału czasu na tyle małego, że możesz sobie pozwolić na przestoje w systemie.

Nadszedł czas, aby wyłączyć dostęp użytkownika do urządzenia StorSimple. Koniec z zmianami. Rozpoczął się przestój.
Musisz pozostawić urządzenie w trybie online i podłączony, ale teraz musi zapobiec zmianom na nim.

W fazie 6 można dogonić wszelkie delta w danych na żywo od ostatniego klonu woluminu.

## <a name="phase-6-a-final-robocopy"></a>Faza 6: Ostateczna RoboCopy

W tym momencie istnieją dwie różnice między lokalnym systemem Windows Server a urządzeniem StorSimple 8100 lub 8600:

1. Mogą istnieć pliki, które nie zostały zsynchronizowane (zobacz **PerItemErrors** z dziennika zdarzeń powyżej)
2. Urządzenie StorSimple ma wypełnioną pamięć podręczną w porównaniu z systemem Windows Server tylko obszar nazw bez zawartości pliku przechowywane lokalnie w tej chwili.

![Obraz przedstawiający część wcześniejszego obrazu przeglądu, który pomaga skupić się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Możemy doprowadzić pamięć podręczną systemu Windows Server do stanu urządzenia i upewnić się, że żaden plik nie zostanie pozostawiony z ostatecznym RoboCopy.

> [!CAUTION]
> Konieczne jest, aby polecenie RoboCopy, które obserwujesz, było dokładnie takie, jak opisano poniżej. Chcemy tylko skopiować pliki, które są lokalne i pliki, które nie zostały przeniesione przez klon woluminu + sync podejście przed. Możemy rozwiązać problemy, dlaczego nie zostały zsynchronizowane później, po zakończeniu migracji. (Zobacz [Rozwiązywanie problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Najprawdopodobniej nie można drukować znaków w nazwach plików, których nie przegapisz po ich usunięciu).

RoboCopy, polecenie:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Tle:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Pozwala roboCopy do uruchamiania wielowątkowych. Wartość domyślna to 8, max to 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Dane wyjściowe do pliku LOG jako UNICODE (zastępuje istniejący dziennik).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Wyjścia do okna konsoli. Używany w połączeniu z wyjściem do pliku dziennika.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Uruchamia RoboCopy w tym samym trybie, którego użyłaby aplikacja do tworzenia kopii zapasowych. Umożliwia RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      RoboCopy może uwzględniać tylko różnice między źródłem (urządzenie StorSimple) a obiektem docelowym (katalog windows server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      wierność kopii pliku (domyślnie jest to /COPY:DAT), flagi kopiowania: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACL, O=Informacje o właścicielu, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      KOPIUJ WSZYSTKIE informacje o pliku (odpowiednik /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      wierność kopii katalogów (domyślnie jest to /DCOPY:DA), flagi kopiowania: D=Data, A=Attributes, T=Sygnatury czasowe
   :::column-end:::
:::row-end:::

Należy uruchomić to polecenie RoboCopy dla każdego z katalogów w systemie Windows Server jako obiektu docelowego skonfigurowanego z synchronizacją plików z plikiem platformy Azure.

Można uruchomić wiele z tych poleceń równolegle.
Po zakończeniu tego kroku RoboCopy można zezwolić użytkownikom i aplikacjom na dostęp do systemu Windows Server, tak jak wcześniej, gdy urządzenie StorSimple.

Skonsultuj się z plikami dziennika robocopy, aby sprawdzić, czy pliki zostały pozostawione. Jeśli problemy powinny istnieć, w większości przypadków można je rozwiązać po zakończeniu migracji, a użytkownicy i aplikacje zostały ponownie zagosione na serwerze Windows Server. Jeśli chcesz rozwiązać jakiekolwiek problemy, zrób to przed fazą 7.

Prawdopodobnie jest potrzebne do utworzenia udziałów SMB w systemie Windows Server, który miał na StorSimple danych przed. Możesz załadować ten krok z przodu i zrobić to wcześniej, aby nie stracić czasu tutaj, ale musisz upewnić się, że przed tym punktem nie nastąpią żadne zmiany w plikach na serwerze Windows.

Jeśli masz wdrożenie DFS-N, możesz skierować obszary nazw DFN na nowe lokalizacje folderów serwera. Jeśli nie masz wdrożenia DFS-N i urządzenie 8100 8600 jest lokalnie połączone z systemem Windows Server, możesz wyłączyć ten serwer z domeny i dołączyć do nowej domeny systemu Windows Server z usługą AFS do domeny, nadaj mu taką samą nazwę serwera jak stary serwer i te same nazwy udziałów, a następnie wycięcie na nowym serwerze pozostaje przezroczyste dla użytkowników, zasad grupy lub skryptów.

## <a name="phase-7-deprovision"></a>Faza 7: Deprovision

Podczas ostatniej fazy masz iterowane przez klony wielu woluminów, a ostatecznie były w stanie wyciąć dostęp użytkownika do nowego systemu Windows Server po przekręceniu urządzenia StorSimple w trybie offline.

Teraz można rozpocząć deprovision niepotrzebnych zasobów.
Przed rozpoczęciem jest najlepszym rozwiązaniem, aby obserwować nowe wdrożenie usługi Azure File Sync w produkcji, na chwilę. To daje opcje, aby rozwiązać wszelkie problemy, które mogą wystąpić.

Po spełnieniu i obserwowaniu wdrożenia AFS przez co najmniej kilka dni, można rozpocząć deprovision zasobów w tej kolejności:

1. Wyłącz maszynę wirtualną platformy Azure, która została użyta do przenoszenia danych z klonów woluminów do udziałów plików platformy Azure za pośrednictwem synchronizacji plików.
2. Przejdź do zasobu usługi synchronizacji magazynu na platformie Azure i wyrejestruj maszynę wirtualną platformy Azure. Spowoduje to usunięcie go ze wszystkich grup synchronizacji.

    > [!WARNING]
    > **UPEWNIJ SIĘ, ŻE wybierzesz odpowiednią maszynę.** Wyłączyłeś maszynę wirtualną w chmurze, co oznacza, że powinna ona być pokazywalna jako jedyny serwer w trybie offline na liście zarejestrowanych serwerów. Nie wolno wybierać lokalnego systemu Windows Server w tym kroku, w ten sposób spowoduje wyrejestrowania go.

3. Usuń maszynę wirtualną platformy Azure i jej zasoby.
4. Wyłącz wirtualne urządzenie StorSimple 8020.
5. Deprovision wszystkie zasoby StorSimple na platformie Azure.
6. Odłącz urządzenie fizyczne StorSimple od centrum danych.

Migracja została zakończona.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z usługą Azure File Sync. Zwłaszcza dzięki elastyczności zasad warstwowych chmury.

Jeśli w witrynie Azure portal lub z wcześniejszych zdarzeń zostanie wyświetlenie, że niektóre pliki nie są trwale synchronizowane, zapoznaj się z przewodnikiem rozwiązywania problemów, aby uzyskać instrukcje rozwiązywania tych problemów.

* [Omówienie usługi Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Warstwy w chmurze](storage-sync-cloud-tiering.md) 
* [Przewodnik po rozwiązywaniu problemów z synchronizacją plików platformy Azure](storage-sync-files-troubleshoot.md)
