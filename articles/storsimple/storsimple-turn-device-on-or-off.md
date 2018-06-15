---
title: Włącz urządzenia serii StorSimple 8000 lub wyłącz | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak włączyć nowe urządzenie StorSimple, Włącz urządzenie, które zostało zamknięte lub utraty zasilania i wyłączyć uruchomione urządzenie.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95fd00608be9cfafb4c703c32ec3ed4713855ca5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2018
ms.locfileid: "27779452"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Włącz lub wyłącz urządzenie serii StorSimple 8000

## <a name="overview"></a>Przegląd
Wyłączanie urządzenia Microsoft Azure StorSimple nie jest wymagane w ramach normalnego działania. Jednak należy włączyć nowe urządzenie lub urządzenia, które ma zostać wyłączony. Ogólnie rzecz biorąc wyłączania jest wymagany w przypadku których należy wymienić sprzęt nie powiodło się, fizycznie przenieść jednostkę lub podjąć urządzenia z usługi. W tym samouczku opisano procedury wymagane włączanie i wyłączanie urządzenia StorSimple w różnych scenariuszach.

## <a name="turn-on-a-new-device"></a>Włącz nowe urządzenie
Kroki włączania urządzenia StorSimple po raz pierwszy różnią się w zależności od tego, czy urządzenie jest 8100 lub 8600 modelu. 8100 ma jednej obudowie głównej 8600 jest urządzeniem podwójnego obudowy z głównej obudowy i obudowy EBOD. W poniższych sekcjach opisano szczegółowo opisano w przypadku obu modeli.

* [Nowe urządzenie z głównej obudowa tylko](#new-device-with-primary-enclosure-only)
* [Nowe urządzenie z EBOD obudowy](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nowe urządzenie z głównej obudowa tylko
Model StorSimple 8100 jest obudowa pojedynczego urządzenia. Urządzenie zawiera nadmiarowe zasilania i chłodzenia modułów (PCMs). Zarówno PCMs musi być zainstalowane i połączone z różnych źródeł napędu aby zapewnić wysoką dostępność.

Wykonaj poniższe kroki, aby Podłączanie kabli do urządzenia zasilania.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Ukończenie konfiguracji urządzenia i okablowanie instrukcje, aby uzyskać [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md). Upewnij się, dokładnie zgodnie z instrukcjami.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nowe urządzenie z EBOD obudowy
Model StorSimple 8600 ma głównej obudowy oraz obudowy EBOD. Wymaga to jednostki do cala ze sobą, połączenie Serial Attached SCSI (SAS) i zasilania.

Podczas konfigurowania tego urządzenia po raz pierwszy, wykonaj kroki dla sygnatury dostępu Współdzielonego okablowania najpierw, a następnie Zakończ kroki okablowania zasilania.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Ukończenie konfiguracji urządzenia i okablowanie instrukcje, aby uzyskać [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md). Upewnij się, dokładnie zgodnie z instrukcjami.

## <a name="turn-on-a-device-after-shutdown"></a>Włącz na urządzeniu po zamknięciu systemu
Kroki włączania urządzenia StorSimple, po jego zamknięciu są różne w zależności od tego, czy urządzenie jest 8100 lub 8600 modelu. 8100 ma jednej obudowie głównej 8600 jest urządzeniem podwójnego obudowy z głównej obudowy i obudowy EBOD.

* [Urządzenia z głównej obudowa tylko](#device-with-primary-enclosure-only)
* [Urządzenia z EBOD obudowy](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Urządzenia z głównej obudowa tylko
Po zamknięciu użyj następującej procedury, aby włączyć na urządzeniu StorSimple z głównej obudowy i obudowy nie EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aby włączyć w urządzeniu o tylko podstawowy obudowy
1. Upewnij się, że zasilania zmienia się na obu zasilania i chłodzenia modułów (PCMs) znajdują się w pozycji OFF. Jeśli przełączniki nie znajdują się w pozycji OFF, przerzucić pozycji OFF i poczekaj, aż nastąpi kontrolki.
2. Przerzucanie przełączniki zasilania na obu PCMs pozycji dalej, aby włączyć na urządzeniu. Urządzenia należy włączyć.
3. Sprawdź następujące polecenie, aby sprawdzić, czy urządzenie jest w pełni na:
   
   1. Na obu modułów PCM LED OK są zielone.
   2. Stan LED na obu kontrolerów są zielony.
   3. Niebieski LED na jeden z kontrolerów jest migający, co oznacza, że kontroler jest aktywny.
      
      Jeśli te warunki nie są spełnione, urządzenie nie jest dobra. Sprawdź [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Urządzenia z EBOD obudowy
Po zamknięciu użyj następującej procedury, aby włączyć na urządzeniu StorSimple z głównej obudowy i obudowy EBOD. Dokładnie zgodnie z opisem wykonywania każdego kroku w sekwencji. Błąd w tym celu może spowodować utratę danych.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aby włączyć w urządzeniu o podstawowym i obudowy EBOD
1. Upewnij się, że obudowa EBOD jest podłączony do głównej obudowy. Aby uzyskać więcej informacji, zobacz [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że zasilania i chłodzenia modułów (PCMs) EBOD i obudowy głównej znajdują się w pozycji OFF. Jeśli przełączniki nie znajdują się w pozycji OFF, przerzucić pozycji OFF i poczekaj, aż nastąpi kontrolki.
3. Obudowa EBOD najpierw włączyć przestawiając przełączniki zasilania na obu PCMs pozycji dalej. LED PCM powinna być zielona. Zielony kontroler EBOD LED w tej jednostce wskazuje, że obudowa EBOD znajduje się na.
4. Włączyć głównej obudowa Przerzucanie przełączniki zasilania na obu PCMs pozycji dalej. Całego systemu powinno być teraz na.
5. Sprawdź, czy LED SAS są zielone, zapewnia, że połączenie między obudowy EBOD i obudowy podstawowy jest dobra.

## <a name="turn-on-a-device-after-a-power-loss"></a>Włącz na urządzeniu po utracie zasilania
Awarii zasilania lub przerwania można wyłączyć urządzenie StorSimple. Na jeden z zasilaczami lub obu zasilacze sytuacja może wystąpić awarii zasilania. Kroki odzyskiwania są różne w zależności od tego, czy urządzenie jest 8100 lub 8600 modelu. 8100 ma jednej obudowie głównej 8600 jest urządzeniem podwójnego obudowy z głównej obudowy i obudowy EBOD. W tej sekcji opisano procedury odzyskiwania dla każdego scenariusza.

* [Urządzenia z głównej obudowa tylko](#8100)
* [Urządzenia z EBOD obudowy](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Urządzenia z głównej obudowa tylko<a name="8100">
System można kontynuować jego normalnego działania, w przypadku utraty zasilania do jednego z jego zasilacze. Jednak w celu zapewnienia wysokiej dostępności urządzenie, przywrócić zasilania zasilania tak szybko, jak to możliwe.

Jeśli istnieje awarii zasilania lub awarii zasilania na obu zasilacze, system zostanie zamknięty w sposób uporządkowany i kontrolowane. Po przywróceniu zasilania, system automatycznie spowoduje włączenie.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Urządzenia z EBOD obudowy<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Podaj utraty zasilania na jednym zasilania
System można kontynuować jego normalnego działania, w przypadku utraty zasilania do jednego z jego zasilacze na obudowę podstawowego lub obudowa EBOD. Jednak w celu zapewnienia wysokiej dostępności urządzenia, Przywróć zasilania do źródła zasilania tak szybko, jak to możliwe.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Zarówno zasilacze na podstawowym i obudowy EBOD utraty zasilania
Jeśli istnieje awarii zasilania lub awarii zasilania na obu zasilacze, obudowa EBOD zostanie natychmiast zamknięty i obudowy głównej zostanie zamknięty w sposób uporządkowany i kontrolowane. Po przywróceniu zasilania urządzenia zostanie uruchomiony automatycznie.

Moc jest wyłączany ręcznie, wykonaj następujące kroki, aby przywrócić zasilania systemu.

1. Włącz obudowa EBOD.
2. Po EBOD znajduje się na, należy włączyć obudowa podstawowego.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Zarówno zasilacze w obudowie EBOD utraty zasilania
Po skonfigurowaniu kable należy EBOD nigdy nie autonomicznie podłączone do oddzielnych PDU. Jeśli EBOD i obudowy podstawowego nie powiedzie się w tym samym czasie, systemu zostanie wznowione.

Jeśli tylko obudowa EBOD nie powiedzie się na obu zasilacze, system nie będzie automatycznie odzyskać. Wykonaj poniższe kroki, aby włączyć system i przywrócić go do stanu dobrej kondycji:

1. Jeśli włączono obudowa głównej wyłączyć zarówno zasilania i chłodzenia modułów (PCMs).
2. Poczekaj kilka minut, aż system zamknąć.
3. Włącz obudowa EBOD.
4. Po EBOD znajduje się na, należy włączyć obudowa podstawowego.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Włącz urządzenie po podstawowej i EBOD obudowa połączenie zostanie przerwane
Jeśli połączenie zostanie przerwane między kontrolerem wstrzymania i odpowiedniego kontrolera EBOD, urządzenie w dalszym ciągu działać. W przypadku utraty połączenia między kontrolerem active systemu i odpowiedniego kontrolera EBOD trybu failover powinny występować i urządzenia będą nadal działać jak zwykle.

Zarówno kable Serial Attached SCSI (SAS) zostały usunięte lub jest Przerwano połączenie między obudowy EBOD i obudowy podstawowego, urządzenie przestanie działać. W tym momencie wykonaj następujące kroki.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Aby włączyć na urządzeniu po połączenie zostanie przerwane
1. Dostęp do tyłu urządzenia.
2. Jeśli połączenie kablowe SAS między obudowy EBOD i obudowy podstawowy został przerwany, wszystkie lane SAS LED na obudowę EBOD będzie wyłączone.
3. Zamknij zarówno zasilania i chłodzenia modułów (PCMs) na obudowę EBOD i obudowy podstawowego.
4. Poczekaj, aż Wyłącz wszystkie kontrolki tyłu zarówno obudowy.
5. Włóż kabli SAS i upewnij się, że istnieje połączenie między obudowy EBOD i obudowy podstawowego.
6. Obudowa EBOD najpierw włączyć przestawiając oba przełączniki PCM pozycji dalej.
7. Upewnij się, że obudowa EBOD znajduje się na sprawdzając zielona LED ma wartość ON.
8. Włącz obudowa podstawowego.
9. Sprawdź, czy głównej obudowy jest na, sprawdzając LED kontrolera zielony ma wartość ON.
10. Sprawdź, czy połączenie obudowy EBOD z głównej obudowy dobrej przez sprawdzenie, czy lane SAS LED (cztery na kontroler EBOD) na wszystkich.

> [!IMPORTANT]
> Jeśli kabli SAS jest uszkodzony lub połączenie między obudowy EBOD i obudowy podstawowego jest nie są odpowiednie, po włączeniu systemu, przejdzie do trybu odzyskiwania. Sprawdź [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) w takim przypadku.


## <a name="turn-off-a-running-device"></a>Wyłączanie uruchomionych urządzenia
Uruchomione urządzenia StorSimple może być konieczne można zamknąć, jeżeli jest przenoszony, podjęte poza usługi lub został nieprawidłowo działający składnik, który ma zostać zamieniony. Te kroki są różne w zależności od tego, czy urządzenie StorSimple to 8100 lub 8600 modelu. 8100 ma jednej obudowie głównej 8600 jest urządzeniem podwójnego obudowy z głównej obudowy i obudowy EBOD. Ta sekcja zawiera szczegóły dotyczące kroki, aby wyłączyć urządzenie uruchomione.

* [Urządzenia z głównej obudowy](#8100a)
* [Urządzenia z EBOD obudowy](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Urządzenia z głównej obudowy<a name="8100a">
Aby wyłączyć urządzenie w sposób uporządkowany i kontrolowane, możesz zrobić to za pośrednictwem portalu Azure lub za pomocą programu Windows PowerShell dla urządzenia StorSimple. 

> [!IMPORTANT]
> Nie są zamykane uruchomionych urządzenia przy użyciu przycisku zasilania z tyłu urządzenia.
> 
> Przed zamknięciem na urządzeniu, upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W portalu Azure, przejdź do **urządzeń** > **Monitor** > **kondycji sprzętu**i sprawdź, czy stan wszystkich składników jest zielony. Dotyczy to tylko w przypadku dobrej kondycji systemu. Jeśli system jest wyłączone w celu zastąpienia nieprawidłowo działający składnik, zostanie wyświetlony nie powiodło się (czerwony) lub nieprawidłowe działanie stan (żółty) dla odpowiednich składników w **stan sprzętu**.
> 
> 

Po uzyskaniu dostępu programu Windows PowerShell dla StorSimple lub w portalu Azure, postępuj zgodnie z instrukcjami [wyłączyć urządzenie StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Urządzenia z EBOD obudowy<a name="8600a">
> [!IMPORTANT]
> Przed zamknięciem głównej obudowy i obudowy EBOD, upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W portalu Azure, przejdź do **urządzeń** > **Monitor** > **kondycji sprzętu**i sprawdź, czy wszystkie składniki są w dobrej kondycji.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Aby wyłączyć urządzenie uruchomionych z EBOD obudowy
1. Wykonaj wszystkie czynności opisane w [zamknięcia urządzenia StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) dla podstawowego obudowy.
2. Po głównej obudowa zostanie zamknięty, zamknięty EBOD przestawiając poza przełączników zarówno zasilania i chłodzenia modułu (PCM).
3. Aby sprawdzić, czy EBOD została zamknięta, sprawdź, czy wszystkie świateł tyłu obudowy EBOD są wyłączone.

> [!NOTE]
> Nie należy usuwać kabli SAS, które są używane do połączenia z obudowy EBOD obudowy głównej dopiero po zamknięciu systemu.

## <a name="next-steps"></a>Kolejne kroki
[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) w razie wystąpienia problemów podczas Włączanie lub wyłączanie urządzenia StorSimple.

