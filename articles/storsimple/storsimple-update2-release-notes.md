---
title: StorSimple 8000 Series Update 2 informacje o wersji | Dokumenty firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i obejścia aktualizacji 2 storsimple 8000 Series.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934058"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 Series Update 2 informacje o wersji

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i identyfikować krytyczne otwarte problemy dla StorSimple 8000 Series Update 2. Zawierają one również listę aktualizacji oprogramowania StorSimple, sterownika i oprogramowania układowego dysku zawartych w tej wersji. 

Aktualizacja 2 może być stosowana do dowolnego urządzenia StorSimple z systemem Release (GA) lub Update 0.1 do update 1.2. Wersja urządzenia skojarzona z aktualizacją 2 to 6.3.9600.17673.

Zapoznaj się z informacjami zawartymi w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Instalacja tej aktualizacji trwa około 4-7 godzin (w tym aktualizacji systemu Windows). 
> * Aktualizacja 2 ma oprogramowanie, sterownik LSI i aktualizacje oprogramowania układowego SSD.
> * W przypadku nowych wersji aktualizacje mogą nie być widoczne natychmiast, ponieważ wprowadzamy aktualizacje stopniowo. Poczekaj kilka dni, a następnie przeskanuj w poszukiwaniu aktualizacji ponownie, ponieważ wkrótce staną się one dostępne.
> 
> 

## <a name="whats-new-in-update-2"></a>Co nowego w aktualizacji 2
Aktualizacja 2 wprowadza następujące nowe funkcje.

* **Woluminy przypięte lokalnie** — w poprzednich wersjach serii StorSimple 8000 bloki danych były warstwowe w chmurze na podstawie użycia. Nie było sposobu, aby zagwarantować, że bloki pozostaną na lokalnym. W aktualizacji 2 podczas tworzenia woluminu można wyznaczyć wolumin jako przypięty lokalnie, a dane podstawowe z tego woluminu nie będą warstwowe w chmurze. Migawki woluminów przypiętych lokalnie będą nadal kopiowane do chmury w celu tworzenia kopii zapasowej, dzięki czemu chmura może być używana do celów mobilności danych i odzyskiwania po awarii. Ponadto można zmienić typ woluminu (czyli konwertować woluminy warstwowe na woluminy przypięte lokalnie i konwertować woluminy przypięte lokalnie na warstwowe). 
* **Ulepszenia urządzenia wirtualnego StorSimple** — wcześniej seria StorSimple 8000 pozycjonowała urządzenie wirtualne jako rozwiązanie do odzyskiwania po awarii lub rozwoju/testowania. Był tylko jeden model urządzenia wirtualnego (model 1100). Aktualizacja 2 wprowadza dwa modele urządzeń wirtualnych: 
  
  * 8010 (dawniej zwany 1100) – Bez zmian; ma pojemność 30 TB i używa standardowego magazynu platformy Azure.
  * 8020 — ma pojemność 64 TB i używa usługi Azure Premium storage w celu zwiększenia wydajności.
    
    Istnieje jeden dysk VHD dla obu modeli urządzeń wirtualnych (8010/8020). Przy pierwszym uruchomieniu urządzenia wirtualnego wykrywa parametry platformy i stosuje poprawną wersję modelu.
* **Ulepszenia sieci —** aktualizacja 2 zawiera następujące ulepszenia sieciowe:
  
  * Wiele kart sieciowych można włączyć dla chmury, dzięki czemu może wystąpić przekłęce awaryjne, jeśli karta sieciowa nie powiedzie się.
  * Ulepszenia routingu ze stałymi metrykami dla bloków z włączoną chmurą.
  * Ponowić próbę ponowienia prób zasobów po awarii przed przełączeniem w tryb failover.
  * Nowe alerty o awariach usługi.
* **Aktualizowanie ulepszeń** — w aktualizacji 1.2 i wcześniejszych seria StorSimple 8000 została zaktualizowana za pośrednictwem dwóch kanałów: Windows Update dla klastrowania, iSCSI i tak dalej oraz Microsoft Update dla plików binarnych i oprogramowania układowego.
    Aktualizacja 2 używa usługi Microsoft Update dla wszystkich pakietów aktualizacji. Powinno to prowadzić do mniej czasu łatanie lub wykonywanie pracy awaryjnej. 
* **Aktualizacje oprogramowania układowego** — uwzględniono następujące aktualizacje oprogramowania układowego:
  
  * LSI: lsi_sas2.sys Wersja produktu 2.00.72.10
  * Tylko dysk SSD (brak aktualizacji dysków twardych): XMGG, XGEG, KZ50, F6C2 i VR08
* **Proaktywna pomoc techniczna** — aktualizacja 2 umożliwia firmie Microsoft pobranie dodatkowych informacji diagnostycznych z urządzenia. Gdy nasz zespół operacyjny identyfikuje urządzenia, które mają problemy, jesteśmy lepiej przygotowani do zbierania informacji z urządzenia i diagnozowania problemów. **Akceptując aktualizację 2, użytkownik pozwala nam zapewnić proaktywne wsparcie.**    

## <a name="issues-fixed-in-update-2"></a>Problemy rozwiązane w aktualizacji 2
Poniższe tabele zawierają podsumowanie problemów, które zostały rozwiązane w aktualizacjach 2.    

| Nie. | Funkcja | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Interfejsy sieciowe |Po uaktualnieniu do aktualizacji 1 usługa StorSimple Manager zgłosiła, że porty Data2 i Data3 nie powiodły się na jednym kontrolerze. Ten problem został rozwiązany. |Tak |Nie |
| 2 |Aktualizacje |Po uaktualnieniu do aktualizacji 1 alerty alarmowe były alarmowe w klasycznej witrynie Azure portal na wielu urządzeniach. Ten problem został rozwiązany. |Tak |Nie |
| 3 |Uwierzytelnianie openstack |Podczas korzystania z Openstack jako dostawcy usług w chmurze, może pojawić się błąd, że ciąg uwierzytelniania w chmurze był zbyt długi. Ten problem został rozwiązany. |Tak |Nie |

## <a name="known-issues-in-update-2"></a>Znane problemy w aktualizacji 2
Poniższa tabela zawiera podsumowanie znanych problemów w tej wersji.

| Nie. | Funkcja | Problem | Komentarze / obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowy EBOD urządzenia 8600 są odłączone, co powoduje brak kworum dysku, a następnie puli magazynu przejdzie do trybu offline. Pozostanie w trybie offline, nawet jeśli dyski zostaną ponownie podłączone. |Musisz ponownie uruchomić urządzenie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać następujące kroki. |Tak |Nie |
| 2 |Nieprawidłowy identyfikator kontrolera |Po wykonaniu wymiany kontrolera kontroler 0 może pojawić się jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może być początkowo pokazywał się jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach to zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagana żadna akcja użytkownika. Ta sytuacja rozwiąże się po zakończeniu wymiany kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Za pomocą usługi magazynowania, aby usunąć konto magazynu jest nieobsługiwał scenariusz. Doprowadzi to do sytuacji, w której nie można pobrać danych użytkownika. | |Tak |Tak |
| 4 |Przewijowanie awaryjne urządzenia |Wiele przechyłów awaryjnych kontenera woluminu z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwany. Przewijanie awaryjne z jednego martwego urządzenia do wielu urządzeń spowoduje, że kontenery woluminów na pierwszym urządzeniu awaryjnym utracić własność danych. Po takiej pracy awaryjnej te kontenery woluminów będą wyświetlane lub zachowywać się inaczej podczas wyświetlania ich w klasycznej witrynie Azure portal. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji karty StorSimple dla programu SharePoint należy podać adres IP urządzenia, aby instalacja zakończyła się pomyślnie. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma https jako określony protokół, komunikacja między urządzeniami zostanie naruszona, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej będą również generowane w procesie, zużywając znaczne zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma http jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Tak |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurujesz i włączysz serwer proxy sieci Web na zarejestrowanym urządzeniu, musisz ponownie uruchomić aktywny kontroler na urządzeniu. | |Tak |Nie |
| 8 |Duże opóźnienia w chmurze i duże obciążenie we/wy |Gdy urządzenie StorSimple napotka kombinację bardzo dużych opóźnień w chmurze (kolejność sekund) i wysokiego obciążenia we/wy, woluminy urządzenia przechodzą w stan pogorszenia, a we/wy mogą zakończyć się niepowodzeniem z błędem "urządzenie nie jest gotowe". |Należy ręcznie ponownie uruchomić kontrolery urządzeń lub wykonać pracy awaryjnej urządzenia, aby odzyskać z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |Korzystając z polecenia cmdlet StorSimple **Get-AzureStorSimpleStorageAccredential &#124; Select-Object -First 1 -Wait,** aby zaznaczyć pierwszy obiekt, aby można było utworzyć nowy obiekt **VolumeContainer,** polecenie cmdlet zwraca wszystkie obiekty. |Zawijanie polecenia cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Tak |Tak |
| 10 |Migracja |Gdy wiele kontenerów woluminów są przekazywane do migracji, ETA dla najnowszej kopii zapasowej jest dokładne tylko dla pierwszego kontenera woluminu. Ponadto migracja równoległa rozpocznie się po migrowanie pierwszych 4 kopii zapasowych w pierwszym kontenerze woluminu. |Zaleca się migrację jednego kontenera woluminów naraz. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad tworzenia kopii zapasowych ani do grupy dysków wirtualnych. |Aby utworzyć kopie zapasowe, należy dodać te woluminy do zasad tworzenia kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzenie serii 5000/7000 nie może uzyskać dostępu do zmigrowanych kontenerów danych. |Zaleca się usunięcie zmigrowanych kontenerów danych po zakończeniu migracji i zaajsnaniu. |Tak |Nie |
| 13 |Klon i dr |Urządzenie StorSimple z aktualizacją 1 nie może sklonować ani wykonywać odzyskiwania po awarii na urządzeniu z uruchomionym oprogramowaniem przed aktualizacją 1. |Należy zaktualizować urządzenie docelowe do aktualizacji 1, aby umożliwić te operacje |Tak |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji dla migracji może zakończyć się niepowodzeniem na urządzeniu z serii 5000-7000, gdy istnieją grupy woluminów bez skojarzonych woluminów. |Usuń wszystkie puste grupy woluminów bez skojarzonych woluminów, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Tak |Nie |
| 15 |Polecenia cmdlet programu Azure PowerShell i woluminy przypięte lokalnie |Nie można utworzyć woluminu przypiętego lokalnie za pośrednictwem poleceń cmdlet programu Azure PowerShell. (Każdy wolumin utworzony za pośrednictwem programu Azure PowerShell będzie warstwowy).) |Zawsze używaj usługi StorSimple Manager do konfigurowania woluminów przypiętych lokalnie. |Tak |Nie |
| 16 |Dostępne miejsce na woluminy przypięte lokalnie |Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa StorSimple Manager aktualizuje lokalne miejsce dostępne około co godzinę. |Poczekaj godzinę, zanim spróbujesz utworzyć nowy wolumin. |Tak |Nie |
| 17 |Lokalnie przypięte woluminy |Zadanie przywracania udostępnia tymczasową kopię zapasową migawki w wykazie kopii zapasowych, ale tylko na czas trwania zadania przywracania. Ponadto udostępnia grupę dysków wirtualnych z prefiksem **tmpCollection** na stronie **Zasady tworzenia kopii zapasowych,** ale tylko na czas trwania zadania przywracania. |To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 18 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania, a kontroler przejdzie w stan failover natychmiast po tym, zadanie przywracania będzie wyświetlane **nie powiodło się** zamiast **Anulowane**. Jeśli zadanie przywracania nie powiedzie się, a kontroler przejdzie w stan failover natychmiast po, zadanie przywracania wyświetli **anulowane** zamiast **nie powiodło się**. |To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 19 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania lub jeśli przywracanie nie powiedzie się, a następnie nastąpi praca awaryjna kontrolera, na stronie **Zadania** zostaną wyświetlona dodatkowe zadanie przywracania. |To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 20 |Lokalnie przypięte woluminy |Jeśli spróbujesz przekonwertować wolumin warstwowy (utworzony i sklonowany za pomocą aktualizacji 1.2 lub wcześniejszej) na wolumin przypięty lokalnie, a na urządzeniu zaczyna brakować miejsca lub występuje awaria chmury, klony mogą zostać uszkodzone. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowane za pomocą oprogramowania przed aktualizacją 2. Powinno to być rzadkie scenariusz. | | |
| 21 |Konwersja woluminów |Nie należy aktualizować AKS dołączonych do woluminu, gdy trwa konwersja woluminu (warstwowa do przypiętych lokalnie lub odwrotnie). Aktualizacja AKS może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj ACR przed konwersją woluminu i nie wprowadzaj żadnych dalszych aktualizacji ACR w trakcie konwersji. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Aktualizacje kontrolera i oprogramowania układowego w aktualizacji 2
Ta wersja aktualizuje sterownik i oprogramowanie układowe dysku na urządzeniu.

* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego LSI, zobacz artykuł 3121900 bazy wiedzy Microsoft Knowledge base. 
* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego dysku, zobacz artykuł 3121899 bazy wiedzy Microsoft Knowledge base.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 2
Tej aktualizacji nie można zastosować do urządzenia wirtualnego. Konieczne będzie utworzenie nowych urządzeń wirtualnych. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację 2](storsimple-install-update-2.md) na urządzeniu StorSimple.

