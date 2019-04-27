---
title: Informacje o wersji aktualizacji macierzy wirtualnej magazynu StorSimple | Dokumentacja firmy Microsoft
description: Opis macierzy wirtualnej StorSimple z aktualizacją Update 0.2 i 0.1 krytyczne nierozwiązane problemy i rozwiązania.
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
ms.openlocfilehash: aad60024187ca180c002f119f4b975e8f69796e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629292"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Informacje o wersji programu StorSimple Virtual Array Update 0.2 i 0.1
## <a name="overview"></a>Omówienie
W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array. (Microsoft Azure StorSimple Virtual Array jest również znany jako urządzenie wirtualne StorSimple w środowisku lokalnym lub urządzenie wirtualne StorSimple). 

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem urządzenia wirtualnego StorSimple starannie Przejrzyj informacje zawarte w informacjach o wersji.

Update 0.2 odnosi się do wersji oprogramowania **10.0.10280.0**; Aktualizacji 0.1 jest wersja **10.0.10279.0**. Poniższe rozdziały zawierają listę zmian dla każdej aktualizacji. 

> [!NOTE]
> Aktualizacje są naturalnymi i spowoduje ponowne uruchomienie urządzenia. W przypadku operacji We/Wy w toku, urządzenia będą naliczane przestojów.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemy rozwiązane w aktualizacji 0.2
Update 0.2 zawiera wszystkie zmiany wprowadzone od aktualizacji 0.1, oprócz poprawki opisanej w poniższej tabeli:

| Cecha | Problem |
| --- | --- |
| Aktualizacje |W ostatniej wersji aktualizacje nie zostały automatycznie wykryć w klasycznym portalu Azure, więc trzeba było używać lokalnego internetowego interfejsu użytkownika do zainstalowania aktualizacji. Ten problem jest rozwiązany w tej wersji. Po zainstalowaniu aktualizacji 0.2, można zainstalować przyszłe aktualizacje przy użyciu klasycznego portalu Azure. |

## <a name="whats-new-in-the-update-01"></a>What's new in aktualizacji 0.1
Aktualizacja 0,1 zawiera następujące poprawki błędów i ulepszenia. 

* **Większa odporność na awarie chmury**: Ta wersja ma kilka poprawek usterek w całym odzyskiwania po awarii, kopii zapasowych, przywracania i obsługa warstw w przypadku przerwania połączenia chmury. 
* **Zwiększona wydajność przywracania**: Ta wersja zawiera poprawki błędów, które znacznie zmniejszyć czas ukończenia zadania przywracania.
* **Automatyczne odzyskiwanie optymalizacje**: Po usunięciu danych na woluminy alokowane elastycznie bloki nieużywane magazynu konieczne można odzyskać. W tej wersji ulepszono proces odzyskiwanie miejsca z chmury, co nieużywane miejsce staje się dostępna szybciej w porównaniu do poprzednich wersji.
* **Nowe obrazy dysku wirtualnego**: Nowy dysk VHD, VHDX i VMDK są teraz dostępne za pośrednictwem klasycznego portalu Azure. Możesz pobrać te obrazy do aprowizowania nowych aktualizacji 0.1 urządzeń.
* **Poprawianie dokładności stanu zadania w portalu**: W starszej wersji oprogramowania stan zadania raportowania w portalu nie szczegółowe. Ten problem został rozwiązany w tej wersji.
* **Środowisko przyłączanie do domeny**: Poprawki dotyczące przyłączania do domeny i zmiana nazwy urządzenia.

## <a name="issues-fixed-in-the-update-01"></a>Problemy rozwiązane w aktualizacji 0.1
Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |VMDK |W niektórych wersjach programu VMware, jako rozrzedzony przyczyną alertów i zakłócania normalnej pracy dzięki występowało dysku systemu operacyjnego. Ten problem został rozwiązany w tej wersji. |
| 2 |Serwer iSCSI |W ostatniej wersji użytkownik był wymagany do określenia bramy dla każdego interfejsu sieciowego włączono urządzenie wirtualne StorSimple. To zachowanie nie zostało zmienione w tej wersji, aby użytkownik będzie musiał skonfigurować co najmniej jedną bramę dla wszystkich interfejsów sieciowych włączone. |
| 3 |Pakiet dla pomocy technicznej |W starszej wersji oprogramowania pomocy technicznej kolekcja pakietów nie powiodła się rozmiary pakiet był większy niż 1 GB. Ten problem jest rozwiązany w tej wersji. |
| 4 |Dostęp do chmury |W przypadku ostatniego wydania Jeśli macierz wirtualną StorSimple nie ma łączność sieciową i został ponownie uruchomiony, lokalnego interfejsu użytkownika się problemy z łącznością. Ten problem został rozwiązany w tej wersji. |
| 5 |Wykresy monitorowania |W poprzedniej wersji, następujące urządzenia przejściu w tryb failover wykresy użycia pojemności chmury wyświetlane nieprawidłowe wartości w klasycznym portalu Azure. Problem ten został rozwiązany w bieżącej wersji. |

## <a name="known-issues-in-the-update-01"></a>Znane problemy w aktualizacji 0.1
Poniższa tabela zawiera podsumowanie informacji o znanych problemach do rozwiązania StorSimple Virtual Array i obejmuje problemy z wymienionych wersji z poprzednich wersji. **Wydanie problemów uwzględnionych w tej wersji są oznaczone gwiazdką. Prawie wszystkie problemy na tej liście zostały przeniesione z wersji ogólnie dostępnej macierzy wirtualnej StorSimple.**

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Urządzenia wirtualne utworzone w wersji zapoznawczej nie można zaktualizować do obsługiwanej wersji ogólnodostępnej. |Te urządzenia wirtualne muszą Failover dla wersji ogólnodostępnej, za pomocą przepływu pracy odzyskiwania po awarii. |
| **2.** |Dysk aprowizowane dane |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzyć odpowiednie urządzenia wirtualnego StorSimple, należy nie rozwinąć lub zmniejszania dysku danych. Takie próby spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na urządzeniu. |Upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i są do niego zastosowane nie obiekty zasad grupy (GPO). |
| **4.** |Lokalnego internetowego interfejsu użytkownika |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony interfejsu użytkownika lokalnego internetowego, takich jak rozwiązywanie problemów lub konserwacji może nie działać prawidłowo. Przyciski na tych stronach również mogą nie działać. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalnego internetowego interfejsu użytkownika |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze wyświetli 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Warstwowe woluminy lub udziały |Zakres bajtów blokowania dla aplikacji, które działają z usługą StorSimple woluminy warstwowe nie jest obsługiwana. Jeśli jest włączone blokowanie zakresu bajtów, StorSimple obsługi warstw nie będzie działać. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logice aplikacji.<br></br>Wybierz przełączyć woluminy przypięte lokalnie, w przeciwieństwie do woluminów warstwowych dane dla tej aplikacji.<br></br>*Zastrzeżenie:*: Jeśli jest włączone blokowanie zakresu bajtów przy użyciu lokalnie przypięte woluminy, należy pamiętać, że woluminu przypiętego lokalnie może być w trybie online nawet przed zakończeniem przywracania. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Warstwowych udziałów |Praca z dużymi plikami, może spowodować wolne warstwy do zewnątrz. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż 3% rozmiaru udziału. |
| **8.** |Używana pojemność udziałów |Może zostać wyświetlony udostępnianie użycia w przypadku braku danych w udziale. Jest to spowodowane używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Można wykonać tylko odzyskiwania po awarii serwera plików do tej samej domeny, jak urządzenie źródłowe. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Będą to zaimplementowane w nowszej wersji. |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeń wirtualnych StorSimple przy użyciu programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem klasycznego portalu Azure i lokalnego Interfejsu w przeglądarce. |
| **11.** |Zmiana hasła |W konsoli urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en US. | |
| **12.** |PROTOKÓŁ CHAP |Nie można usunąć poświadczeń protokołu CHAP, po utworzeniu. Ponadto w przypadku zmodyfikowania poświadczeń protokołu CHAP, musisz przełączyć woluminy w tryb offline, a następnie przełącz je na online, aby zmiana zaczęła obowiązywać. |Te problem zostanie rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą różnić się w usłudze StorSimple Manager i hosta iSCSI. |Hosta iSCSI zawiera widok systemu plików.<br></br>Urządzenie będzie widział bloków, przydzielany, gdy wolumin został w maksymalnym rozmiarze. |
| **14.** |Plik server * |Jeśli plik w folderze zawiera alternatywny Data Stream (ADS) skojarzonych z nim, REKLAM jest nie kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |

## <a name="next-step"></a>Następny krok
[Instalowanie aktualizacji](storsimple-ova-install-update-01.md) na rozwiązania StorSimple Virtual Array.

