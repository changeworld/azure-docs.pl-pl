---
title: Informacje o wersji StorSimple wirtualnego aktualizacje tablicy | Dokumentacja firmy Microsoft
description: Opisuje krytyczne Otwórz problemy i rozwiązania dla tablicy wirtualnego StorSimple uruchomieniu Update 0.2 i 0,1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Informacje o wersji wirtualnej tablicy Update 0.2 i 0,1 StorSimple
## <a name="overview"></a>Przegląd
W poniższych informacjach o zidentyfikować krytyczne problemy otwarte i rozwiązać problemy dotyczące aktualizacji tablicy wirtualne Microsoft Azure StorSimple. (Microsoft Azure StorSimple wirtualnego tablicy jest znanej także jako urządzenie wirtualne StorSimple lokalnymi lub urządzenie wirtualne StorSimple). 

Informacje o wersji są stale aktualizowane i wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem urządzenia wirtualnego StorSimple, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Update 0.2 odpowiada wersji oprogramowania **10.0.10280.0**; Wersja jest aktualizacja 0,1 **10.0.10279.0**. Poniższe rozdziały zawierają listę zmian dla każdej aktualizacji. 

> [!NOTE]
> Aktualizacje są one i zostanie ponownie uruchomić urządzenie. Jeśli we/wy jest w toku, urządzenia będą naliczane przestoju.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemy rozwiązane w Update 0.2
Aktualizacja 0,2 obejmuje wszystkie zmiany z Update 0.1, oprócz poprawki opisanej w poniższej tabeli:

| Cecha | Problem |
| --- | --- |
| Aktualizacje |W ostatniej wersji aktualizacje nie zostały wykrywane automatycznie w klasycznym portalu Azure, więc konieczne było użycie lokalnego interfejsu użytkownika sieci Web do zainstalowania aktualizacji. Tego problemu w tej wersji. Po zainstalowaniu aktualizacji 0,2, należy zainstalować przyszłych aktualizacji przy użyciu klasycznego portalu Azure. |

## <a name="whats-new-in-the-update-01"></a>Nowości w aktualizacji 0,1
Aktualizacja 0,1 zawiera następujące ulepszenia i poprawki błędów. 

* **Większa odporność na awarie chmury**: Ta wersja zawiera kilka poprawek dotyczących odzyskiwania po awarii, kopii zapasowych, przywracania i Obsługa poziomów w przypadku przerwania połączenia chmury. 
* **Większa wydajność przywracania**: Ta wersja zawiera poprawki, które mają znacznie skrócić czas ukończenia zadań przywracania.
* **Automatyczne odzyskiwanie optymalizacje**: usunięcie danych na woluminy alokowane elastycznie bloki nieużywane magazynu muszą można odzyskać. Ta wersja ulepszono proces odzyskiwanie miejsca z chmury, co powoduje nieużywane miejsce staje się dostępna szybciej niż w poprzednich wersjach.
* **Nowych obrazów dysku wirtualnego**: nowego wirtualnego dysku twardego, VHDX i VMDK są teraz dostępne za pośrednictwem klasycznego portalu Azure. Możesz pobrać tych obrazów do obsługi administracyjnej nowych urządzeń Update 0.1.
* **Poprawianie dokładności stan zadania w portalu**: W starszej wersji oprogramowania, stan zadania raportowania w portalu nie szczegółowego. Ten problem zostanie rozwiązany w tej wersji.
* **Środowisko sprzężenia domeny**: poprawki błędów związanych z przyłączanie się do domeny i zmiana nazwy urządzenia.

## <a name="issues-fixed-in-the-update-01"></a>Problemy rozwiązane w Update 0.1
Poniższa tabela zawiera podsumowanie problemy rozwiązane w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |VMDK |W niektórych wersjach VMware dysk systemu operacyjnego został postrzegane jako rozrzedzony przyczyną alertów i zakłócania normalnych operacji. Problem został rozwiązany w tej wersji. |
| 2 |Serwer iSCSI |W ostatniej wersji użytkownika było wymagane do określenia bramy dla każdego interfejsu sieciowego włączone, urządzenia wirtualnego StorSimple. To zachowanie zostanie zmieniona w tej wersji, dzięki czemu użytkownik ma do skonfigurowania co najmniej jedną bramę dla wszystkich interfejsów sieciowych włączone. |
| 3 |Pakiet pomocy technicznej |W starszej wersji oprogramowania obsługuje kolekcja pakietów nie powiodło się, gdy rozmiar pakietu wynosił większą niż 1 GB. Tego problemu w tej wersji. |
| 4 |Dostęp do chmury |W ostatniej wersji Jeśli tablica wirtualnego StorSimple nie ma łączności sieciowej i ponownego uruchomienia lokalnego interfejsu użytkownika musi problemy z połączeniem. Ten problem został rozwiązany w tej wersji. |
| 5 |Monitorowanie wykresów |W poprzedniej wersji, następujące urządzenia trybu failover wykresy wykorzystania pojemności chmury wyświetlane nieprawidłowe wartości w klasycznym portalu Azure. Jest to stała w bieżącej wersji. |

## <a name="known-issues-in-the-update-01"></a>Znane problemy w aktualizacji 0,1
Poniższa tabela zawiera podsumowanie znane problemy dla tablicy wirtualnego StorSimple oraz problemów wymienionych wersji z poprzednich wersji. **Wersja problemów wymienionych w tej wersji są oznaczone gwiazdką. Prawie wszystkie problemy na liście zostały przeniesione z wersji GA tablicy wirtualne StorSimple.**

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzenia wirtualne utworzone w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualnego należy można przełączyć w wersji ogólnodostępnej za pomocą przepływu pracy (DR) odzyskiwania po awarii. |
| **2.** |Dysk z danymi udostępnione |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzyć odpowiednie urządzenia wirtualnego StorSimple, użytkownik musi nie zwiększania lub zmniejszania dysk z danymi. Takie próby spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączony do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Sprawdź, czy tablica wirtualnej jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i żadne obiekty zasad grupy (GPO) są stosowane do niego. |
| **4.** |Lokalnego interfejsu użytkownika sieci web |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (nazywana konfiguracją IE ESC), niektórych lokalnego interfejsu użytkownika strony, takich jak rozwiązywanie problemów lub konserwacji może nie działać poprawnie. Przyciski na tych stronach również mogą nie działać. |Wyłącz funkcje zwiększonych zabezpieczeń programu Internet Explorer. |
| **5.** |Lokalnego interfejsu użytkownika sieci web |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze zawiera 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Woluminy warstwowe lub udziały |Zakres bajtów blokady aplikacji współdziałających ze StorSimple woluminy warstwowe nie jest obsługiwane. Jeśli jest włączone blokowanie zakresu bajtów, Obsługa poziomów StorSimple nie będzie działać. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logiki aplikacji.<br></br>Wybierz, aby umieścić dane dla tej aplikacji w woluminów przypiętych lokalnie, zamiast woluminy warstwowe.<br></br>*Zastrzeżenie:*: w przypadku lokalnie przy użyciu przypięty woluminów i jest włączone blokowanie zakresu bajtów, należy pamiętać, że woluminu przypiętego lokalnie może być w trybie online nawet przed zakończeniem przywracania. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowych |Praca z dużych plików może spowodować wolne warstwy w poziomie. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż rozmiar udziału % 3. |
| **8.** |Używane pojemności dla akcji |Może zostać wyświetlony udostępnianie użycie w przypadku braku żadnych danych w udziale. Jest to spowodowane pojemność używane udziały obejmuje metadanych. | |
| **9.** |Odzyskiwanie po awarii |Może wyłącznie wykonywać odzyskiwanie po awarii, serwera plików do tej samej domeny co urządzenia źródłowego. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |To są realizowane w nowszej wersji. |
| **10.** |Azure PowerShell |Urządzenia wirtualne StorSimple nie można zarządzać za pomocą programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem klasycznego portalu Azure i lokalnego interfejsu użytkownika sieci web. |
| **11.** |Zmienianie hasła |Konsoli urządzenia wirtualnego tablicy akceptuje tylko dane wejściowe w formacie klawiatury en US. | |
| **12.** |PROTOKOŁU CHAP |Nie można usunąć poświadczenia CHAP raz utworzony. Ponadto Jeśli zmodyfikujesz poświadczeń protokołu CHAP, konieczne będzie woluminy Przełącz do trybu offline, a następnie przełączyć je na online, aby zmiany zaczęły obowiązywać. |Te zostaną rozwiązane w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą się różnić w usługi Menedżer StorSimple i hosta iSCSI. |Hosta iSCSI ma widok systemu plików.<br></br>Urządzenie widzi bloki przydzielone, gdy wolumin w maksymalnym rozmiarze. |
| **14.** |Serwer plików * |Jeśli plik w folderze alternatywnych danych strumienia (AD) skojarzonych z nim, REKLAM nie jest kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizacje](storsimple-ova-install-update-01.md) na tablica wirtualne StorSimple.

