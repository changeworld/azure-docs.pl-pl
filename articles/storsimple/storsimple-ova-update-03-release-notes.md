---
title: Informacje o wersji StorSimple wirtualnego aktualizacje tablicy | Dokumentacja firmy Microsoft
description: Opisuje krytyczne Otwórz problemy i rozwiązania dla tablicy wirtualnego StorSimple systemem Update 0.3.
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
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875515"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Informacje o wersji 0,3 Update tablicy z wirtualnego StorSimple
## <a name="overview"></a>Omówienie
W poniższych informacjach o zidentyfikować krytyczne problemy otwarte i rozwiązać problemy dotyczące aktualizacji tablicy wirtualne Microsoft Azure StorSimple.

Informacje o wersji są stale aktualizowane i wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem tablica wirtualnego StorSimple, należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Update 0.3 odpowiada wersji oprogramowania **10.0.10288.0**.

> [!NOTE]
> Aktualizacje są one i ponownym uruchomieniu urządzenia. Jeśli we/wy jest w toku, urządzenie wiąże przestoju.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Nowości w aktualizacji Update 0.3
Update 0.3 jest głównie poprawka błędu kompilacji. W tej wersji rozwiązano kilka błędów, co spowoduje niepowodzenie wykonywania kopii zapasowej w poprzedniej wersji.

## <a name="issues-fixed-in-the-update-03"></a>Problemy rozwiązane w Update 0.3
Poniższa tabela zawiera podsumowanie problemy rozwiązane w tej wersji.

| Nie. | Funkcja | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych |Problem wystąpił w starszej wersji, gdy tworzenie kopii zapasowych nie powiedzie się dla udziału plików. Jeśli ten problem wystąpił, zadanie tworzenia kopii zapasowej nie powiedzie się i zgłoszono alert krytyczny w usłudze Menedżer StorSimple do powiadamiania użytkownika. Ten problem nie wpłynęła na dane na dostęp do danych lub udziałów. Zidentyfikowane i stałym w tej wersji przyczyny głównej. <br></br> Poprawka nie dotyczy wstecznie udziałów, które są już występuje ten problem. Klienci, którzy są występuje ten problem należy najpierw zastosować aktualizację 0,3, a następnie skontaktuj się z Microsoft Support wykonywania kopii zapasowej całego systemu, aby rozwiązać ten problem. Zamiast kontaktowania się z Microsoft Support, klientów można również przywrócić nowy udział z dobrej kopii zapasowej dla odpowiednich udziałów. |
| 2 |iSCSI |Problem wystąpił w starszej wersji, gdzie woluminy zniknie po skopiowaniu danych do woluminu w macierzy wirtualnego StorSimple. Ten problem został rozwiązany w tej wersji. <br></br> Poprawki zostały zastosowane tylko na nowo utworzony woluminów. Poprawki nie dotyczą wstecznie woluminów, które są już występuje ten problem. Klienci są zalecane jest Przełącz odpowiednich woluminów online za pośrednictwem klasycznego portalu Azure, wykonaj kopię zapasową dla tych woluminów, a następnie przywróć te woluminy do nowych woluminów. |

## <a name="known-issues-in-the-update-03"></a>Znane problemy w Update 0.3
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

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,3](storsimple-ova-install-update-01.md) na tablica wirtualne StorSimple.

## <a name="references"></a>Dokumentacja
Szukasz starsze Uwaga wersji? Przejdź do strony: 

* [Informacje o wersji aktualizacji tablicy wirtualnego StorSimple 0,1 i 0,2](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji dostępności ogólne tablicy wirtualnego StorSimple](storsimple-ova-pp-release-notes.md)

