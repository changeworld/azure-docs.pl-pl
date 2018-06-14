---
title: Wirtualne aktualizacji tablicy StorSimple 0,4 informacje o wersji | Dokumentacja firmy Microsoft
description: Opisuje krytyczne Otwórz problemy i rozwiązania uruchomioną aktualizacją 0,4 tablicy wirtualne StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927761"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Informacje o wersji 0,4 Update tablicy z wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W poniższych informacjach o zidentyfikować krytyczne problemy otwarte i rozwiązać problemy dotyczące aktualizacji tablicy wirtualne Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane i wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem tablica wirtualnego StorSimple, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 0,4 odpowiada wersji oprogramowania **10.0.10289.0**.

> [!NOTE]
> Aktualizacje są one i ponownym uruchomieniu urządzenia. Jeśli we/wy jest w toku, urządzenie wiąże przestoju.


## <a name="whats-new-in-the-update-04"></a>Nowości w aktualizacji 0,4
Aktualizacja 0,4 jest głównie kompilacji Poprawka usterki sprzężona kilka ulepszeń. W tej wersji rozwiązano kilka błędów, co spowoduje niepowodzenie wykonywania kopii zapasowej w poprzedniej wersji. Główne ulepszeń i poprawek usterek są następujące:

- **Wykonaj kopię zapasową usprawnień wydajności** -tej wersji wprowadził kilka ulepszeń klucza, aby poprawić wydajność kopii zapasowych. W związku z tym kopie zapasowe, które obejmuje dużą liczbę plików, zobacz znaczny spadek czas wymagany do ukończenia pełnych i przyrostowych kopii zapasowych.

- **Lepszą wydajnością przywracania** — ta wersja zawiera ulepszenia, które znacznie zwiększyć wydajność przywracania, korzystając z dużej liczby plików. Przy użyciu 2-4 miliony plików, firma Microsoft zaleca obsługi administracyjnej wirtualnego tablicy z 16 GB pamięci RAM, aby zobaczyć ulepszenia. Korzystając z mniej niż 2 miliony plików, minimalne wymagania dla maszyny wirtualnej jest nadal 8 GB pamięci RAM.

- **Ulepszenia pakietu obsługi** — ulepszenia obejmują rejestrowania statystyki dla dysku, Procesora, pamięci, sieci i chmury w pakiecie pomocy technicznej, zwiększając proces diagnozowania/debugowanie problemów z urządzeniami.

- **Woluminy iSCSI do 200 GB przypięty lokalnie limit** — dla woluminów przypiętych lokalnie, zaleca się ograniczyć do woluminu iSCSI 200 GB na tablica wirtualne StorSimple. Rezerwacja lokalne woluminy warstwowe nadal 10% rozmiaru alokowanego woluminu, ale jest ograniczona do 200 GB. 

- **Poprawki błędów związanych z kopii zapasowej** — w poprzednich wersjach oprogramowania, wystąpiły problemy związane z kopii zapasowych, które mogłyby spowodować niepowodzenie wykonywania kopii zapasowej. Te błędy rozwiązano w tej wersji.


## <a name="issues-fixed-in-the-update-04"></a>Problemy rozwiązane w ramach aktualizacji 0,4

Poniższa tabela zawiera podsumowanie problemy rozwiązane w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Wydajność tworzenia kopii zapasowej|We wcześniejszych wersjach kopie zapasowe obejmujące wiele plików może zająć dużo czasu do ukończenia (według dni). W tej wersji zarówno pełne i przyrostowe kopie zapasowe Zobacz znaczny spadek czas do ukończenia. |
| 2 |Pakiet pomocy technicznej|Dysk, Procesora, pamięci, sieci i chmury statystyki teraz zalogować się do dzienniki pomocy technicznej, tworzenie pakietów obsługi bardzo efektywnym sposobem, w dowolnym rozwiązywania problemów z urządzeniami.|
| 3 |Tworzenie kopii zapasowych |We wcześniejszych wersjach długotrwałą kopii zapasowych może spowodować brak miejsca na urządzeniu, co spowoduje niepowodzenie wykonywania kopii zapasowej. Ta usterka zostanie rozwiązana w tej wersji, zezwalając nie więcej niż 5 kopii zapasowych do kolejki w tym samym czasie.|
| 4 |iSCSI | We wcześniejszych wersjach lokalnego rezerwacji woluminów warstwowych lub przypięty lokalnie jest 10% rozmiaru alokowanego woluminu. W tej wersji lokalnej rezerwacji wszystkie woluminy iSCSI (lokalnie przypięty lub do warstwy) jest ograniczona do 10% maksymalnie maksymalnie 200 GB (dla woluminów warstwowych większych niż 2 TB) tym samym zwalnianie więcej miejsca na dysku lokalnym. Zaleca się woluminów przypiętych lokalnie w tej wersji maksymalnie 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Znane problemy w aktualizacji 0,4

Poniższa tabela zawiera podsumowanie znane problemy dla tablicy wirtualnego StorSimple oraz problemów wymienionych wersji z poprzednich wersji. 

| Nie. | Funkcja | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzenia wirtualne utworzone w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualnego należy można przełączyć w wersji ogólnodostępnej za pomocą przepływu pracy (DR) odzyskiwania po awarii. |
| **2.** |Dysk z danymi udostępnione |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzyć odpowiednie urządzenia wirtualnego StorSimple, użytkownik musi nie zwiększania lub zmniejszania dysk z danymi. Próby spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączony do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Sprawdź, czy tablica wirtualnej jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i żadne obiekty zasad grupy (GPO) są stosowane do niego. |
| **4.** |Lokalnego interfejsu użytkownika sieci web |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (nazywana konfiguracją IE ESC), niektórych lokalnego interfejsu użytkownika strony, takich jak rozwiązywanie problemów lub konserwacji może nie działać poprawnie. Przyciski na tych stronach również mogą nie działać. |Wyłącz funkcje zwiększonych zabezpieczeń programu Internet Explorer. |
| **5.** |Lokalnego interfejsu użytkownika sieci web |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze zawiera 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Woluminy warstwowe lub udziały |Zakres bajtów blokady aplikacji współdziałających ze StorSimple woluminy warstwowe nie jest obsługiwane. Jeśli jest włączone blokowanie zakresu bajtów, Obsługa poziomów StorSimple nie działa. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logiki aplikacji.<br></br>Wybierz, aby umieścić dane dla tej aplikacji w woluminów przypiętych lokalnie, zamiast woluminy warstwowe.<br></br>*Zastrzeżenie:*: gdy lokalnie przy użyciu przypięty woluminów i jest włączone blokowanie zakresu bajtów, nawet przed zakończeniem przywracania można online woluminu przypiętego lokalnie. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowych |Praca z dużych plików może spowodować wolne warstwy w poziomie. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż rozmiar udziału % 3. |
| **8.** |Używane pojemności dla akcji |Może zostać wyświetlony udostępnianie zużycia, gdy nie ma żadnych danych w udziale. Jest to spowodowane pojemność używane udziały obejmuje metadanych. | |
| **9.** |Odzyskiwanie po awarii |Może wyłącznie wykonywać odzyskiwanie po awarii, serwera plików do tej samej domeny co urządzenia źródłowego. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Ten sposób jest implementowany w nowszej wersji. |
| **10.** |Azure PowerShell |Urządzenia wirtualne StorSimple nie można zarządzać za pomocą programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem klasycznego portalu Azure i lokalnego interfejsu użytkownika sieci web. |
| **11.** |Zmienianie hasła |Konsoli urządzenia wirtualnego tablicy akceptuje tylko dane wejściowe w formacie klawiatury en US. | |
| **12.** |PROTOKOŁU CHAP |Nie można usunąć poświadczenia CHAP raz utworzony. Ponadto zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy do trybu offline, a następnie przełączyć je na online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą się różnić w usługi Menedżer StorSimple i hosta iSCSI. |Hosta iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze alternatywnych danych strumienia (AD) skojarzonych z nim, REKLAM nie jest kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |
| **15.** |Serwer plików |Łącza symbolicznego nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez Windows System szyfrowania plików (EFS) po skopiowaniu przez lub przechowywany w tablicy wirtualnego StorSimple wynik serwera plików w nieobsługiwanej konfiguracji.  | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,4](storsimple-virtual-array-install-update-04.md) na tablica wirtualne StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starsze Uwaga wersji? Przejdź do strony: 

* [Informacje o wersji 0,3 aktualizacji tablicy wirtualnego StorSimple](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji aktualizacji tablicy wirtualnego StorSimple 0,1 i 0,2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne tablicy wirtualnego StorSimple](storsimple-ova-pp-release-notes.md)

