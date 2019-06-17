---
title: Informacje o wersji StorSimple 8000 Series Update 2 | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i rozwiązania dla usługi StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f23a507ab631be553613e22cafa037291548a8aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717137"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Informacje o wersji StorSimple 8000 Series Update 2

## <a name="overview"></a>Omówienie
Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować krytyczne nierozwiązane problemy dla usługi StorSimple 8000 Series Update 2. Zawierają one również listę oprogramowania StorSimple, sterowników i aktualizacje oprogramowania układowego dysku zawartych w tej wersji. 

Aktualizacja 2 mogą stosowane do każdego urządzenia StorSimple z wersji (GA) lub aktualizacji 0.1 za pośrednictwem aktualizacji 1.2. Wersja urządzenia skojarzone z aktualizacją Update 2 jest 6.3.9600.17673.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Trwa około 4 – 7 godzin, aby zainstalować tę aktualizację (w tym aktualizacje Windows). 
> * Aktualizacja 2 ma oprogramowania, LSI sterowników i aktualizacji oprogramowania układowego stacji dysków SSD.
> * Do nowych wersji, mogą nie być widoczne aktualizacje od razu, ponieważ robimy etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-2"></a>Co nowego w wersji Update 2
Aktualizacja 2 wprowadzono następujące nowe funkcje.

* **Lokalnie przypięte woluminy** — w poprzednich wersjach serii StorSimple 8000, bloki danych zostały warstwowe w chmurze na podstawie użycia. Nie było ma gwarancji, że bloki będzie pozostają w lokalnych. W wersji Update 2 podczas tworzenia woluminu, można wyznaczyć woluminu jako przypiętego lokalnie i podstawowe dane z tego woluminu nie będą umieszczane w chmurze. Migawki woluminów przypiętych lokalnie nadal zostaną skopiowane do chmury na potrzeby kopii zapasowych, tak, aby chmura może służyć do celów odzyskiwania po awarii i przenoszenia danych. Ponadto można zmienić typ woluminu (oznacza to convert warstwowe woluminy do woluminów przypiętych lokalnie i przekonwertować lokalnie przypięte woluminy warstwowe). 
* **Ulepszenia urządzenia wirtualnego StorSimple** — wcześniej serii StorSimple 8000 umieszczona urządzenia wirtualnego jako rozwiązania odzyskiwania lub tworzenia/testowania po awarii. Było tylko jeden model urządzenia wirtualnego (model 1100). Aktualizacja 2 wprowadzono dwa modele urządzenia wirtualnego: 
  
  * 8010 (wcześniej nazywanych 1100) — bez zmian; ma pojemność wynoszącą 30 TB, a następnie używa usługi Azure standard storage.
  * — 8020 ma 64 TB pojemności i korzysta z usługi Azure Premium storage w celu zwiększenia wydajności.
    
    Brak pojedynczego wirtualnego dysku twardego dla obu modeli urządzenia wirtualnego (8010/8020). Podczas pierwszego uruchomienia urządzenia wirtualnego, wykrywa parametry platformy i dotyczy wersji poprawny model.
* **Ulepszenia sieci** — Update 2 zawiera następujące ulepszenia sieciowe:
  
  * Wiele kart sieciowych można włączyć dla chmury, aby tryb failover może wystąpić, jeśli karta sieciowa nie powiedzie się.
  * Ulepszenia routingu, za pomocą stałych metryk dla chmury włączone bloków.
  * Spróbuj wykonać ponownie online zasobów zakończonych niepowodzeniem przed włączeniem trybu failover.
  * Nowe alerty na wypadek awarii usługi.
* **Aktualizowanie ulepszenia** — zaktualizować 1.2 i wcześniej serii StorSimple 8000 został zaktualizowany przy użyciu dwóch kanałów: Aktualizacja Windows klastrowania, iSCSI, a itd i Microsoft Update w danych binarnych i oprogramowanie układowe.
    Dla wszystkich pakietów aktualizacji Update 2 wykorzystuje usługę Microsoft Update. Powinno to spowodować mniej czasu na poprawianie lub podczas pracy w trybie Failover. 
* **Aktualizacje oprogramowania układowego** — następujące oprogramowanie układowe uwzględniono aktualizacje:
  
  * LSI: lsi_sas2.sys wersja produktu 2.00.72.10
  * Tylko dyski SSD (nie aktualizacji HDD): XMGG XGEG, KZ50, F6C2 i VR08
* **Obsługa aktywnego** — Update 2 umożliwia firmie Microsoft w celu ściągnięcia dodatkowych informacji diagnostycznych z urządzenia. Gdy nasz zespół operacyjny identyfikuje urządzeń, które występują problemy, firma Microsoft, są lepiej przystosowany do zbierania informacji z urządzenia i diagnozować problemy. **Akceptując Update 2, umożliwiają nam do obsługi aktywnego**.    

## <a name="issues-fixed-in-update-2"></a>Problemy rozwiązane w wersji Update 2
Poniższe tabele zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji 2.    

| Nie. | Cecha | Problem | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Interfejsy sieciowe |Po uaktualnieniu programu Update 1 usługa StorSimple Manager zgłosił, że porty dane2 i Data3 nie powiodło się na jednym kontrolerze. Ten problem został rozwiązany. |Tak |Nie |
| 2 |Aktualizacje |Po uaktualnieniu programu Update 1 alertów alarm dźwiękowy wystąpił w klasycznej witrynie Azure portal na wielu urządzeniach. Ten problem został rozwiązany. |Yes |Nie |
| 3 |Openstack uwierzytelniania |Używając Openstack jako dostawca usług w chmurze, może wystąpić błąd, ciąg uwierzytelniający usługi chmury jest za długa. Ten problem został rozwiązany. |Tak |Nie |

## <a name="known-issues-in-update-2"></a>Znane problemy w wersji Update 2
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze / obejście | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Dysku kworum |W rzadkich przypadkach jeśli większość dysków w obudowie EBOD się do urządzenia 8600 są odłączone skutkuje dysku kworum, następnie puli magazynu przejdzie w trybie offline. Pozostanie w trybie offline, nawet wtedy, gdy dyski są zakończone. |Będzie konieczne ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą firmy Microsoft Support dla następnych kroków. |Yes |Nie |
| 2 |Identyfikator niepoprawne kontrolera |Podczas zastępowania kontrolera kontrolera 0 może stanowić kontrolera 1. Podczas zastępowania kontrolera gdy obraz jest ładowany z węzła równorzędnego identyfikator kontrolera było wyświetlane początkowo jako identyfikator kontrolera elementów równorzędnych. W rzadkich przypadkach to zachowanie może być widoczny po ponownym uruchomieniu systemu. |Użytkownik jest wymagana żadna akcja. Ta sytuacja sam się rozwiąże po zakończeniu zastępczego kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Za pomocą usługi Storage można usunąć konta magazynu jest to nieobsługiwany scenariusz. Spowoduje to doprowadzić do sytuacji, w której nie można pobrać danych użytkownika. | |Yes |Yes |
| 4 |Tryb failover urządzenia |Wielu przejścia w tryb failover kontenera woluminów na tym samym urządzeniu źródła urządzeniom inny element docelowy nie jest obsługiwane. Tryb failover z jednym urządzeniu martwy do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu w trybie Failover utracić prawa własności do danych. Po włączeniu tych trybu failover te kontenery woluminów wyglądają lub działają inaczej w przypadku można je wyświetlić w klasycznym portalu Azure. | |Tak |Nie |
| 5 |Instalacja |Podczas adaptera StorSimple dla instalacji programu SharePoint musisz podać adres IP urządzenia, w kolejności do pomyślnego ukończenia instalacji. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli w konfiguracji serwera proxy sieci web HTTPS jako określony protokół, komunikacja usługi urządzeń będzie to miało wpływu i urządzenie przejdą w tryb offline. Obsługa pakietów również zostanie wygenerowany w procesie zużywają znaczne zasoby na urządzeniu z systemem. |Upewnij się, że adres URL serwera proxy sieci web ma HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Tak |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurować i włączyć serwer proxy sieci web na zarejestrowanym urządzeniu, należy uruchomić ponownie aktywny kontroler, na urządzeniu. | |Tak |Nie |
| 8 |Czas oczekiwania w chmurze o wysokiej i wysokiego obciążenia We/Wy |Gdy urządzenia StorSimple napotyka chmura bardzo duże opóźnienia (kolejność w sekundach) i wysokiego obciążenia We/Wy, do woluminów urządzenia przechodzi w stan obniżonej wydajności i operacji We/Wy może zakończyć się niepowodzeniem z powodu błędu "urządzenie nie jest gotowy". |Należy ręcznie ponownie uruchomić kontrolery urządzeń lub urządzeń w tryb failover do odzyskania z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |Kiedy używasz polecenia cmdlet usługi StorSimple **Get AzureStorSimpleStorageAccountCredential &#124; Select-Object - najpierw 1 - Wait** do wybrania pierwszego obiektu tak, aby utworzyć nową **elementu VolumeContainer** obiekt, polecenie cmdlet zwraca wszystkie obiekty. |Opakowanie polecenia cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - najpierw 1 - Wait** |Tak |Tak |
| 10 |Migracja |W przypadku wielu kontenerów woluminów są przekazywane do migracji, Inżyniera najnowszej kopii zapasowej jest prawidłowe tylko dla pierwszego kontenera woluminów. Ponadto migracji równoległej rozpocznie się po pierwszych 4 kopie zapasowe w pierwszym kontenera woluminów są migrowane. |Zaleca się migrację jeden kontener woluminów naraz. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad kopii zapasowych lub grupy dysków wirtualnych. |Należy dodać te woluminy do zasad kopii zapasowych w celu tworzenia kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzeń serii 5000 i 7000 nie musi uzyskać dostęp do kontenerów zmigrowanych danych. |Zaleca się usuwanie kontenerów migrowanych danych, po migracji, ukończone i zatwierdzone. |Tak |Nie |
| 13 |Klonuj i odzyskiwanie po awarii |Urządzenia StorSimple z aktualizacją Update 1 nie można sklonować lub odzyskiwanie danych po awarii na urządzeniu z systemem przed aktualizacją oprogramowania 1. |Należy zaktualizować urządzenie docelowe do Update 1, aby zezwolić na te operacje |Yes |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji migracji może zakończyć się niepowodzeniem na urządzeniach serii 5000 – 7000 przypadku grupami woluminów za pomocą skojarzonych woluminów. |Usuń wszystkie grupy pusty wolumin z woluminów skojarzonych, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Yes |Nie |
| 15 |Polecenia cmdlet programu PowerShell systemu Azure i lokalnie przypięte woluminy |Nie można utworzyć woluminu przypiętego lokalnie za pomocą poleceń cmdlet programu Azure PowerShell. (Będą umieszczane woluminów utworzonych za pomocą programu Azure PowerShell.) |Zawsze należy używać usługi StorSimple Manager do skonfigurowania woluminów przypiętych lokalnie. |Tak |Nie |
| 16 |Miejsce dostępne dla woluminów przypiętych lokalnie |Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne do nowych woluminów nie może natychmiast zaktualizować. Usługa StorSimple Manager aktualizuje lokalne miejsce dostępne mniej więcej co godzinę. |Zaczekaj godzinę przed próbą utworzenia nowego woluminu. |Tak |Nie |
| 17 |Lokalnie przypięte woluminy |Zadanie przywracania udostępnia tymczasowe migawki kopii zapasowej w katalogu kopii zapasowej, ale tylko na czas trwania zadania przywracania. Ponadto udostępnia ona grupy dysków wirtualnych z prefiksem **tmpCollection** na **zasady tworzenia kopii zapasowych** strony, ale tylko na czas trwania zadania przywracania. |To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować. Interwencja użytkownika nie jest wymagane. |Tak |Nie |
| 18 |Lokalnie przypięte woluminy |Jeśli anulowanie zadania przywracania, a do kontrolera pracy awaryjnej natychmiast po wyświetli zadanie **przywracania** zamiast **anulowane**. Jeśli zadanie przywracania kończy się niepowodzeniem, a do kontrolera pracy awaryjnej natychmiast po wyświetli zadanie **przywracania** **anulowane** zamiast. |To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować. Interwencja użytkownika nie jest wymagane. |Tak |Nie |
| 19 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania lub jeśli przywracania kończy się niepowodzeniem, a następnie odbywa się praca awaryjna kontrolera, zadania przywracania dodatkowe pojawia się na **zadań** strony. |To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować. Interwencja użytkownika nie jest wymagane. |Yes |Nie |
| 20 |Lokalnie przypięte woluminy |Jeśli użytkownik próbuje przekonwertować wolumin warstwowy (utworzone i sklonowany za pomocą 1.2 aktualizacji lub starszym) do woluminu przypiętego lokalnie i urządzeniu zaczyna brakować miejsca lub występuje awaria chmury, clone(s) może być uszkodzony. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowany za pomocą przed aktualizacją 2 oprogramowania. Powinna to być rzadkie scenariusza. | | |
| 21 |Konwersja woluminu |Nie są uaktualniane rekordów Acr, dołączone do woluminu w trakcie konwersji woluminu (warstwowego na lokalnie przypięty i na odwrót). Aktualizowanie rekordów Acr może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj rekordów Acr przed konwersji woluminu i nie należy wprowadzać wszelkie dalsze aktualizacji rejestru Azure container Registry w trakcie konwersji. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Aktualizacje oprogramowania układowego i kontrolera, w wersji Update 2
W tej wersji aktualizacji sterowników i oprogramowania układowego dysku, na urządzeniu.

* Aby uzyskać więcej informacji na temat oprogramowania układowego LSI aktualizacji, w artykule bazy wiedzy Microsoft Knowledge base 3121900. 
* Aby uzyskać więcej informacji na temat oprogramowania układowego dysku aktualizacji, w artykule bazy wiedzy Microsoft Knowledge base 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizacje urządzenia wirtualnego w wersji Update 2
Ta aktualizacja nie można zastosować do urządzenia wirtualnego. Nowe urządzenia wirtualnego należy ma zostać utworzony. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [instalowanie aktualizacji 2](storsimple-install-update-2.md) na urządzeniu StorSimple.

