---
title: Informacje o wersji aktualizacji macierzy wirtualnej magazynu StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano krytyczne nierozwiązane problemy i rozwiązania dla macierzy wirtualnej StorSimple z aktualizacją Update 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 635b5f4edf5d403c569b4957540fc105997b3e8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629275"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple wirtualnego tablicy Update 0.3 informacje o wersji
## <a name="overview"></a>Omówienie
W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązania problemów dotyczących aktualizacji Microsoft Azure StorSimple Virtual Array.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem rozwiązania StorSimple Virtual Array, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Update 0.3 odnosi się do wersji oprogramowania **10.0.10288.0**.

> [!NOTE]
> Aktualizacje są naturalnymi i ponownym uruchomieniu urządzenia. W przypadku operacji We/Wy w toku, urządzenie wiąże się z przestojem.
> 
> 

## <a name="whats-new-in-the-update-03"></a>What's new in Update 0.3
Update 0.3 to przede wszystkim poprawki błędów kompilacji. W tej wersji rozwiązano kilka błędów skutkuje niepowodzeniami tworzenia kopii zapasowych w poprzedniej wersji.

## <a name="issues-fixed-in-the-update-03"></a>Problemy rozwiązane w Update 0.3
Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych |Problem występowało w starszej wersji, których kopie zapasowe będą nie powiedzie się dla udziału plików. Jeśli ten problem wystąpił, zadanie tworzenia kopii zapasowej może zakończyć się niepowodzeniem, a alert krytyczny został zgłoszony w usługi StorSimple Manager w celu powiadomienia użytkownika. Ten problem wpływa na dane na udziały lub dostęp do danych. Zidentyfikowane i rozwiązane w tej wersji przyczyny głównej. <br></br> Poprawka nie dotyczy wstecznie udziałów, które są już widoczne ten problem. Klienci, którzy widzisz ten problem należy najpierw zastosować Update 0.3, a następnie skontaktuj się z Microsoft Support wykonywania kopii zapasowej całego systemu, aby rozwiązać ten problem. Zamiast kontaktując się z pomocą firmy Microsoft Support, klientów można przywrócić na nowy udział z dobrej kondycji kopii zapasowej, których to dotyczy udziałów. |
| 2 |iSCSI |Wystąpił problem występowało w starszej wersji, w którym woluminy znikała podczas kopiowania danych do woluminu w macierzy wirtualnej StorSimple. Ten problem został rozwiązany w tej wersji. <br></br> Poprawki zastosowane tylko na nowo utworzonych woluminach. Poprawki nie dotyczą wstecznie woluminów, które są już widoczne ten problem. Klienci doradza się przełączyć dotyczy woluminy do trybu online za pośrednictwem klasycznego portalu Azure, wykonaj kopię zapasową dla tych woluminów, a następnie przywróć te woluminy do nowych woluminów. |

## <a name="known-issues-in-the-update-03"></a>Znane problemy w programie Update 0.3
Poniższa tabela zawiera podsumowanie informacji o znanych problemach do rozwiązania StorSimple Virtual Array i obejmuje problemy z wymienionych wersji z poprzednich wersji. 

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Urządzenia wirtualne utworzone w wersji zapoznawczej nie można zaktualizować do obsługiwanej wersji ogólnodostępnej. |Te urządzenia wirtualne muszą Failover dla wersji ogólnodostępnej, za pomocą przepływu pracy odzyskiwania po awarii. |
| **2.** |Dysk aprowizowane dane |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzyć odpowiednie urządzenia wirtualnego StorSimple, należy nie rozwinąć lub zmniejszania dysku danych. Takie próby wyniki spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, stosowanie zasad grupy może niekorzystnie wpłynąć na urządzeniu. |Upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Active Directory i są do niego zastosowane nie obiekty zasad grupy (GPO). |
| **4.** |Lokalnego internetowego interfejsu użytkownika |Ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony interfejsu użytkownika lokalnego internetowego, takich jak rozwiązywanie problemów lub konserwacji może nie działać prawidłowo. Przyciski na tych stronach również mogą nie działać. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5.** |Lokalnego internetowego interfejsu użytkownika |Na maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w sieci web interfejsu użytkownika są wyświetlane jako 10 GB/s interfejsów. |To zachowanie jest odbicia funkcji Hyper-v. Funkcji Hyper-V jest zawsze wyświetli 10 GB/s dla wirtualnych kart sieciowych. |
| **6.** |Warstwowe woluminy lub udziały |Zakres bajtów blokowania dla aplikacji, które działają z usługą StorSimple woluminy warstwowe nie jest obsługiwana. Jeśli jest włączone blokowanie zakresu bajtów, StorSimple obsługi warstw nie działa. |Zalecane środki obejmują: <br></br>Wyłącz zakresu bajtów blokowania w logice aplikacji.<br></br>Wybierz przełączyć woluminy przypięte lokalnie, w przeciwieństwie do woluminów warstwowych dane dla tej aplikacji.<br></br>*Zastrzeżenie:* : Jeśli jest włączone blokowanie zakresu bajtów przy użyciu lokalnie przypięte woluminy, woluminu przypiętego lokalnie może być online, nawet przed zakończeniem przywracania. W takich przypadkach jeśli przywracania jest w toku, następnie należy poczekać na ukończenie przywracania. |
| **7.** |Warstwowych udziałów |Praca z dużymi plikami, może spowodować wolne warstwy do zewnątrz. |Podczas pracy z dużymi plikami, zaleca się, że największy plik jest mniejszy niż 3% rozmiaru udziału. |
| **8.** |Używana pojemność udziałów |Może zostać wyświetlony udostępnić zużycie, gdy nie ma żadnych danych w udziale. Jest to spowodowane używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Można wykonać tylko odzyskiwania po awarii serwera plików do tej samej domeny, jak urządzenie źródłowe. Odzyskiwanie po awarii do urządzenia docelowego w innej domenie nie jest obsługiwane w tej wersji. |Jest to zaimplementowane w nowszej wersji. |
| **10.** |Azure PowerShell |Nie można zarządzać urządzeń wirtualnych StorSimple przy użyciu programu Azure PowerShell w tej wersji. |Zarządzanie urządzeń wirtualnych powinna być wykonywana za pośrednictwem klasycznego portalu Azure i lokalnego Interfejsu w przeglądarce. |
| **11.** |Zmiana hasła |W konsoli urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en US. | |
| **12.** |PROTOKÓŁ CHAP |Nie można usunąć poświadczeń protokołu CHAP, po utworzeniu. Ponadto w przypadku zmodyfikowania poświadczeń protokołu CHAP, należy przełączyć woluminy w tryb offline, a następnie przełącz je na online, aby zmiana zaczęła obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **13.** |Serwer iSCSI |"Użyto magazynu" wyświetlany dla woluminu iSCSI mogą różnić się w usłudze StorSimple Manager i hosta iSCSI. |Hosta iSCSI zawiera widok systemu plików.<br></br>Urządzenie będzie widział bloków, przydzielany, gdy wolumin został w maksymalnym rozmiarze. |
| **14.** |Serwer plików |Jeśli plik w folderze zawiera alternatywny Data Stream (ADS) skojarzonych z nim, REKLAM jest nie kopię zapasową lub przywrócić za pomocą odzyskiwania po awarii, powielania i odzyskiwanie na poziomie elementu. | |

## <a name="next-step"></a>Następny krok
[Instalowanie aktualizacji Update 0.3](storsimple-ova-install-update-01.md) na rozwiązania StorSimple Virtual Array.

## <a name="references"></a>Dokumentacja
Szukasz starszej wersji? Przejdź do strony: 

* [Informacje o wersji Update macierzy wirtualnej StorSimple 0,1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)

