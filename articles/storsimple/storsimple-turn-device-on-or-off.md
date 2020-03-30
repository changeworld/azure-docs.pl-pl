---
title: Włączanie i wyłączanie urządzenia z serii StorSimple 8000
description: W tym artykule wyjaśniono, jak włączyć nowe urządzenie StorSimple, włączyć urządzenie, które zostało wyłączone lub straciło zasilanie, i wyłączyć uruchomione urządzenie.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254627"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Włączanie lub wyłączanie urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie
Zamykanie urządzenia Microsoft Azure StorSimple nie jest wymagane jako część normalnej pracy systemu. Może jednak być konieczne włączenie nowego urządzenia lub urządzenia, które musiało zostać wyłączone. Ogólnie rzecz biorąc zamknięcie jest wymagane w przypadkach, w których należy zastąpić nieudany sprzęt, fizycznie przenieść jednostkę lub wyjąć urządzenie z usługi. W tym samouczku opisano procedurę wymaganą do włączania i zamykania urządzenia StorSimple w różnych scenariuszach.

## <a name="turn-on-a-new-device"></a>Włączanie nowego urządzenia
Kroki włączania urządzenia StorSimple po raz pierwszy różnią się w zależności od tego, czy urządzenie jest modelem 8100 czy 8600. Model 8100 jest wyposażony w jedną obudowę podstawową, natomiast model 8600 jest urządzeniem z podwójną obudową z obudową podstawową i obudową EBOD. Szczegółowe kroki dla obu modeli są omówione w poniższych sekcjach.

* [Nowe urządzenie tylko z obudową podstawową](#new-device-with-primary-enclosure-only)
* [Nowe urządzenie z obudową EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nowe urządzenie tylko z obudową podstawową
Model StorSimple 8100 jest pojedynczym urządzeniem obudowy. Urządzenie zawiera nadmiarowe moduły zasilania i chłodzenia (PCM). Oba pcms muszą być zainstalowane i podłączone do różnych źródeł zasilania, aby zapewnić wysoką dostępność.

Wykonaj następujące czynności, aby sprzewodu urządzenia do zasilania.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Aby uzyskać kompletne instrukcje dotyczące konfiguracji i okablowania urządzenia, przejdź do [strony Instalowanie urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md). Upewnij się, że dokładnie postępuj zgodnie z instrukcjami.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nowe urządzenie z obudową EBOD
Model StorSimple 8600 ma zarówno obudowę podstawową, jak i obudowę EBOD. Wymaga to, aby urządzenia były połączone ze sobą w celu łączności i zasilania SCSI (SYK) podłączonej do szeregowej.

Podczas konfigurowania tego urządzenia po raz pierwszy wykonaj kroki dotyczące okablowania SAS, a następnie wykonaj kroki dotyczące okablowania zasilania.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Aby uzyskać kompletne instrukcje dotyczące konfiguracji i okablowania urządzenia, przejdź do [strony Instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md). Upewnij się, że dokładnie postępuj zgodnie z instrukcjami.

## <a name="turn-on-a-device-after-shutdown"></a>Włączanie urządzenia po wyłączeniu
Kroki włączania urządzenia StorSimple po jego wyłączeniu różnią się w zależności od tego, czy urządzenie jest modelem 8100, czy 8600. Model 8100 jest wyposażony w jedną obudowę podstawową, natomiast model 8600 jest urządzeniem z podwójną obudową z obudową podstawową i obudową EBOD.

* [Urządzenie tylko z obudową podstawową](#device-with-primary-enclosure-only)
* [Urządzenie z obudową EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Urządzenie tylko z obudową podstawową
Po zamknięciu należy użyć poniższej procedury, aby włączyć urządzenie StorSimple z główną obudową i bez obudowy EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aby włączyć urządzenie tylko z główną obudową
1. Upewnij się, że przełączniki zasilania zarówno modułów zasilania, jak i modułów chłodzenia (PCM) znajdują się w pozycji WYŁĄCZONEj. Jeśli przełączniki nie znajdują się w pozycji OFF, przerzuć je do pozycji OFF i poczekaj, aż światła zgasną.
2. Włącz urządzenie, przerzucając przełączniki zasilania na obu pcm do pozycji ON. Urządzenie powinno się włączyć.
3. Sprawdź, czy urządzenie jest w pełni włączone:
   
   1. Diody LED OK w obu modułach PCM są zielone.
   2. Diody LED stanu obu sterowników są świecić na zielono.
   3. Niebieska dioda LED na jednym z kontrolerów miga, co oznacza, że kontroler jest aktywny.
      
      Jeśli którykolwiek z tych warunków nie jest spełniony, urządzenie nie jest w dobrej kondycji. Skontaktuj [się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Urządzenie z obudową EBOD
Po zamknięciu należy użyć poniższej procedury, aby włączyć urządzenie StorSimple z główną obudową i obudową EBOD. Wykonaj każdy krok w kolejności dokładnie tak, jak opisano. Niezastosowanie się do tego może spowodować utratę danych.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aby włączyć urządzenie z obudową podstawową i EBOD
1. Upewnij się, że obudowa EBOD jest podłączona do obudowy podstawowej. Aby uzyskać więcej informacji, zobacz [Instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że moduły zasilania i chłodzenia (PCM) zarówno w obudowach EBOD, jak i podstawowych znajdują się w pozycji WYŁĄCZONEj. Jeśli przełączniki nie znajdują się w pozycji OFF, przerzuć je do pozycji OFF i poczekaj, aż światła zgasną.
3. Najpierw włącz obudowę EBOD, przerzucając przełączniki zasilania na obu pcm do pozycji ON. Diody LED PCM powinny być zielone. Zielona dioda LED kontrolera EBOD na tym urządzeniu wskazuje, że obudowa EBOD jest wprowadzona.
4. Włącz obudowę podstawową, przerzucając przełączniki zasilania na obu pcm do pozycji ON. Cały system powinien być teraz włączony.
5. Sprawdź, czy diody LED SAS są zielone, co zapewnia, że połączenie między obudową EBOD a obudową podstawową jest dobre.

## <a name="turn-on-a-device-after-a-power-loss"></a>Włączanie urządzenia po utracie zasilania
Awaria zasilania lub przerwa może wyłączyć urządzenie StorSimple. Awaria zasilania może się zdarzyć na jednym z zasilaczy lub obu zasilaczy. Kroki odzyskiwania różnią się w zależności od tego, czy urządzenie jest modelem 8100 czy 8600. Model 8100 jest wyposażony w jedną obudowę podstawową, natomiast model 8600 jest urządzeniem z podwójną obudową z obudową podstawową i obudową EBOD. W tej sekcji opisano procedurę odzyskiwania dla każdego scenariusza.

* [Urządzenie tylko z obudową podstawową](#8100)
* [Urządzenie z obudową EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Urządzenie tylko z obudową podstawową<a name="8100">
System może kontynuować normalną pracę, jeśli dojdzie do utraty zasilania jednego z zasilaczy. Jednak, aby zapewnić wysoką dostępność urządzenia, przywrócić zasilanie do zasilania tak szybko, jak to możliwe.

W przypadku przerwy w zasilaniu lub przerwy w zasilaniu obu zasilaczy, system zostanie wyłączony w sposób uporządkowany i kontrolowany. Po przywróceniu zasilania system automatycznie się włączy.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Urządzenie z obudową EBOD<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Utrata zasilania na jednym zasilaczu
System może kontynuować normalną pracę, jeśli na jednej z jego zasilaczy w obudowie podstawowej lub obudowie EBOD wystąpi utrata zasilania. Jednak, aby zapewnić wysoką dostępność urządzenia, należy jak najszybciej przywrócić zasilanie do zasilania.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Utrata mocy obu zasilaczy w obudowach podstawowych i EBOD
W przypadku przerwy w zasilaniu lub przerwy w zasilaniu obu zasilaczy, obudowa EBOD zostanie natychmiast zamknięta, a obudowa podstawowa zostanie zamknięta w sposób uporządkowany i kontrolowany. Po przywróceniu zasilania urządzenie uruchomi się automatycznie.

Jeśli zasilanie jest wyłączone ręcznie, należy wykonać następujące czynności, aby przywrócić zasilanie systemu.

1. Włącz obudowę EBOD.
2. Po włączeniu obudowy EBOD włącz obudowę podstawową.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Utrata zasilania obu zasilaczy w obudowie EBOD
Podczas konfigurowania kabli należy upewnić się, że urządzenie EBOD nigdy nie jest podłączone samodzielnie do oddzielnego urządzenia PDU. Jeśli EBOD i obudowa podstawowa nie powiedzie się w tym samym czasie, system zostanie odzyskany.

Jeśli tylko obudowa EBOD ulegnie awarii w obu zasilaczach, system nie zostanie automatycznie odzyskany. Aby włączyć system i przywrócić go do stanu dobrej kondycji, należy wykonać następujące czynności:

1. Jeśli obudowa podstawowa jest włączona, wyłącz zarówno moduły zasilania, jak i moduły chłodzenia (PCM).
2. Poczekaj kilka minut na wyłączenie systemu.
3. Włącz obudowę EBOD.
4. Po włączeniu obudowy EBOD włącz obudowę podstawową.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Włączanie urządzenia po utracie połączenia z obudową podstawową i EBOD
Jeśli połączenie zostanie utracone między kontrolerem rezerwowym a odpowiednim kontrolerem EBOD, urządzenie będzie nadal działać. Jeśli połączenie między aktywnym kontrolerem systemu a odpowiednim kontrolerem EBOD zostanie utracone, powinno nastąpić praca awaryjna, a urządzenie powinno kontynuować pracę normalnie.

Po usunięciu obu kable SCSI (SAS) złączem szeregowym (SAS) lub zerwaniu połączenia między obudową EBOD a obudową podstawową urządzenie przestanie działać. W tym momencie wykonaj następujące kroki.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Aby włączyć urządzenie po utracie połączenia
1. Dostęp do tylnej części urządzenia.
2. Jeśli połączenie kablowe SAS między obudową EBOD a obudową podstawową zostanie przerwane, wszystkie diody LED pasa dostępu SAS w obudowie EBOD zostaną wyłączone.
3. Wyłącz zarówno moduły zasilania, jak i moduły chłodzenia (PCM) w obudowie EBOD i obudowie podstawowej.
4. Poczekaj, aż wszystkie światła z tyłu obu obudowy zgasną.
5. Włóż ponownie kable SAS i upewnij się, że istnieje dobre połączenie między obudową EBOD a obudową podstawową.
6. Najpierw włącz obudowę EBOD, przerzucając oba przełączniki PCM w pozycję ON.
7. Upewnij się, że obudowa EBOD jest wprowadzona, sprawdzając, czy zielona dioda LED jest wł..
8. Włącz obudowę podstawową.
9. Upewnij się, że obudowa podstawowa jest wprowadzona, sprawdzając, czy zielona dioda LED kontrolera jest wprowadzona.
10. Sprawdź, czy połączenie obudowy EBOD z obudową podstawową jest dobre, sprawdzając, czy diody LED pasa dostępu SAS (cztery na sterownik EBOD) są włączone.

> [!IMPORTANT]
> Jeśli kable SAS są uszkodzone lub połączenie między obudową EBOD a obudową podstawową nie jest dobre, po włączeniu systemu przejdzie on w tryb odzyskiwania. Jeśli tak się stanie, [skontaktuj się z pomocą techniczną firmy Microsoft.](storsimple-8000-contact-microsoft-support.md)


## <a name="turn-off-a-running-device"></a>Wyłączanie uruchomionego urządzenia
Uruchomione urządzenie StorSimple może wymagać zamknięcia, jeśli jest przenoszone, wyjęte z eksploatacji lub ma nieprawidłowo działający składnik, który wymaga wymiany. Kroki różnią się w zależności od tego, czy urządzenie StorSimple jest modelem 8100 czy 8600. Model 8100 jest wyposażony w jedną obudowę podstawową, natomiast model 8600 jest urządzeniem z podwójną obudową z obudową podstawową i obudową EBOD. W tej sekcji opisano kroki zamykania uruchomionego urządzenia.

* [Urządzenie z obudową podstawową](#8100a)
* [Urządzenie z obudową EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Urządzenie z obudową podstawową<a name="8100a">
Aby zamknąć urządzenie w sposób uporządkowany i kontrolowany, można to zrobić za pośrednictwem witryny Azure portal lub za pośrednictwem programu Windows PowerShell dla StorSimple. 

> [!IMPORTANT]
> Nie wyłączaj uruchomionego urządzenia za pomocą przycisku zasilania z tyłu urządzenia.
> 
> Przed wyłączeniem urządzenia upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W witrynie Azure portal przejdź do stanu kondycji sprzętu**monitoruj** > **Hardware health** **urządzenia** > i sprawdź, czy stan wszystkich składników jest zielony. Dotyczy to tylko zdrowego systemu. Jeśli system jest wyłączany w celu zastąpienia nieprawidłowego działania komponentu, w stanie sprzętu zostanie wyświetlony uszkodzony (czerwony) lub obniżony (żółty) stan dla danego **składnika**.
> 
> 

Po przejściu do programu Windows PowerShell for StorSimple lub portalu Azure wykonaj kroki opisane w [zamykaniu urządzenia StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Urządzenie z obudową EBOD<a name="8600a">
> [!IMPORTANT]
> Przed wyłączeniem obudowy podstawowej i obudowy EBOD upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W witrynie Azure portal przejdź do **usługi Devices** > **Monitor** > **Kondycji sprzętu**i sprawdź, czy wszystkie składniki są w dobrej kondycji.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Aby wyłączyć uruchomione urządzenie z obudową EBOD
1. Wykonaj wszystkie kroki wymienione w [zamknij urządzenie StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) dla obudowy podstawowej.
2. Po wyłączeniu obudowy podstawowej wyłącz moduł EBOD, wyłącz przełączniki PCM (Power and Cooling Module).
3. Aby sprawdzić, czy moduł EBOD został wyłączony, sprawdź, czy wszystkie światła z tyłu obudowy EBOD są wyłączone.

> [!NOTE]
> Kable SAS używane do podłączenia obudowy EBOD do obudowy podstawowej nie powinny być usuwane dopiero po wyłączeniu systemu.

## <a name="next-steps"></a>Następne kroki
[Jeśli](storsimple-8000-contact-microsoft-support.md) wystąpią problemy podczas włączania lub wyłączania urządzenia StorSimple, skontaktuj się z pomocą techniczną firmy Microsoft.

