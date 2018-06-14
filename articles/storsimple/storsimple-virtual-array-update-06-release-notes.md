---
title: Wirtualne aktualizacji tablicy StorSimple 0,6 informacje o wersji | Dokumentacja firmy Microsoft
description: Opisuje krytyczne Otwórz problemy i rozwiązania uruchomioną aktualizacją 0,6 tablicy wirtualne StorSimple.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: af202cf652300ee7897eb2dede33f38058fc2837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876439"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Informacje o wersji 0,6 Update tablicy z wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W poniższych informacjach o zidentyfikować krytyczne problemy otwarte i rozwiązać problemy dotyczące aktualizacji tablicy wirtualne Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane i wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem tablica wirtualnego StorSimple, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Aktualizacja 0,6 odpowiada wersji oprogramowania **10.0.10293.0**.

> [!IMPORTANT]
> - Aktualizacje są one i ponownym uruchomieniu urządzenia. Jeśli we/wy jest w toku, urządzenie wiąże przestoju. Aby uzyskać szczegółowe informacje na temat zastosować aktualizację, [instalacji aktualizacji 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Zdecydowanie zaleca się zainstalowanie aktualizacji 0,6 natychmiast, ponieważ zawiera on poprawki błędów krytycznych.


## <a name="whats-new-in-the-update-06"></a>Nowości w aktualizacji 0,6
Aktualizacja 0,6 jest aktualizacji krytycznych i powinny być wdrażane bezpośrednio. Ta aktualizacja zawiera następujące poprawki: 

- **Poprawki zabezpieczeń systemu Windows** — to wydanie zawiera **poprawki zabezpieczenia krytyczne systemu Windows**. Przejrzyj następujące aktualizacje zabezpieczeń, aby uzyskać więcej informacji na temat problemów z zabezpieczeniami i skojarzone poprawki:
    - [Grudnia 2016 jakości tylko zabezpieczeń aktualizacji dla Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017 marca zabezpieczeń, jakości tylko aktualizacji dla Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 maja 2017 — KB4019213 (tylko do zabezpieczeń aktualizacja)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Przywróć poprawka** -we wcześniejszych wersjach został usterki, które mogłyby uniemożliwiać ukończenie przywracania. Ten problem został rozwiązany w tej wersji.


## <a name="issues-fixed-in-the-update-06"></a>Problemy rozwiązane w ramach aktualizacji 0,6

Poniższa tabela zawiera podsumowanie problemy rozwiązane w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Bezpieczeństwo| Ta wersja zawiera aktualizacje krytyczne zabezpieczenia systemu Windows. Zalecamy natychmiastowe zainstalowanie tej aktualizacji.|
| 2 |Przywracanie| Podczas przywracania wystąpił sytuacji wyścigu, która będzie uniemożliwiają ukończenie zadania przywracania. Poprawka błędu adresów sytuacja wyścigu.|


## <a name="known-issues-in-the-update-06"></a>Znane problemy w aktualizacji 0,6

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
| **8.** |Używane pojemności dla akcji |Może zostać wyświetlony udostępnianie zużycia, gdy nie ma żadnych danych w udziale. To zużycie jest ponieważ używane pojemność udziałów obejmuje metadanych. | |
| **9.** |Odzyskiwanie po awarii |Może wyłącznie wykonywać odzyskiwanie po awarii, serwera plików do tej samej domeny co urządzenia źródłowego. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Ten sposób jest implementowany w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu Failover i odzyskiwania po awarii dla macierzy wirtualnego StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Urządzenia wirtualne StorSimple nie można zarządzać za pomocą programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem portalu Azure i lokalnego interfejsu użytkownika sieci web. |
| **11.** |Zmienianie hasła |Konsoli urządzenia wirtualnego tablicy akceptuje tylko danych wejściowych w en-us klawiatury formatu. | |
| **12.** |PROTOKOŁU CHAP |Nie można usunąć poświadczenia CHAP raz utworzony. Ponadto zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy do trybu offline, a następnie przełączyć je na online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą być różne w usługi Menedżer StorSimple urządzenia i hosta iSCSI. |Hosta iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze alternatywnych danych strumienia (AD) skojarzonych z nim, REKLAM nie jest kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |
| **15.** |Serwer plików |Łącza symbolicznego nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez Windows System szyfrowania plików (EFS) po skopiowaniu przez lub przechowywany w tablicy wirtualnego StorSimple wynik serwera plików w nieobsługiwanej konfiguracji.  | |
| **17.** |Aktualizacje |Jeśli zostanie wyświetlony błąd kodu: 2359302 (szesnastkowo 0x240006) podczas próby instalacji poprawki za pośrednictwem lokalnego interfejsu użytkownika, następnie oznacza to, że poprawka jest już zainstalowana na urządzeniu.   | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,6](storsimple-virtual-array-install-update-06.md) na tablica wirtualne StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starsze Uwaga wersji? Przejdź do strony:

* [Informacje o wersji 0,5 aktualizacji tablicy wirtualnego StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Informacje o wersji 0,4 aktualizacji tablicy wirtualnego StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Informacje o wersji 0,3 aktualizacji tablicy wirtualnego StorSimple](storsimple-ova-update-03-release-notes.md)
* [Informacje o wersji aktualizacji tablicy wirtualnego StorSimple 0,1 i 0,2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne tablicy wirtualnego StorSimple](storsimple-ova-pp-release-notes.md)

