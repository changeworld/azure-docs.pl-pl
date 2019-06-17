---
title: Włączanie urządzenia serii StorSimple 8000 lub wyłączanie | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak włączyć nowe urządzenie StorSimple, Włącz urządzeń, który został zamknięty lub utraty zasilania i wyłączyć urządzenie uruchomione.
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
ms.openlocfilehash: 29a45c0d32e35b5d321670bf25334a2976b93e56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693674"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Włącz lub wyłącz urządzenie StorSimple 8000 series

## <a name="overview"></a>Omówienie
Wyłączanie urządzenia Microsoft Azure StorSimple nie jest wymagane jako część normalnego działania. Jednak należy włączyć nowe urządzenie lub urządzenia, który ma zostać wyłączony. Ogólnie rzecz biorąc zamknięcie jest wymagany w przypadkach, w których musisz wymienić sprzęt nie powiodło się, fizycznie przenieść jednostka lub wykonać urządzenia z usługi. W tym samouczku opisano procedury wymagane włączanie i wyłączanie urządzenia StorSimple w różnych scenariuszach.

## <a name="turn-on-a-new-device"></a>Włącz nowe urządzenie
Kroki do włączania na urządzeniu StorSimple po raz pierwszy różnią się w zależności od tego, czy urządzenie jest 8100 lub 8600 modelu. 8100 ma jednej obudowie głównej 8600 jest podwójnego Obudowa urządzenia przy użyciu podstawowego obudowy i obudowy EBOD. W poniższych sekcjach znajdują się szczegółowe instrukcje zostały podane w przypadku obu modeli.

* [Nowe urządzenie przy użyciu tylko podstawowy obudowy](#new-device-with-primary-enclosure-only)
* [Nowe urządzenie przy użyciu obudowy EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nowe urządzenie przy użyciu tylko podstawowy obudowy
Model StorSimple 8100 jest obudowy pojedynczego urządzenia. Urządzenia zawiera nadmiarowe zasilania i chłodzenia modułów (PCMs). Zarówno PCMs musi być zainstalowane i połączone z różnymi źródłami zasilania aby zapewnić wysoką dostępność.

Wykonaj poniższe kroki, aby Podłączanie kabli do urządzenia zasilania.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Aby uzyskać przeprowadzenie konfiguracji urządzenia i okablowanie instrukcje, przejdź do [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md). Upewnij się, że dokładnie wykonaj instrukcje.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nowe urządzenie przy użyciu obudowy EBOD
Model StorSimple 8600 ma podstawowy obudowy i obudowy EBOD. Wymaga to jednostki, które można ze sobą rozpakowane Serial Attached SCSI (SAS) łączność i zasilania.

Podczas konfigurowania tego urządzenia po raz pierwszy, sygnatury dostępu Współdzielonego okablowania najpierw wykonaj kroki, a następnie wykonaj kroki odpowiednie dla przewody zasilania.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Aby uzyskać przeprowadzenie konfiguracji urządzenia i okablowanie instrukcje, przejdź do [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md). Upewnij się, że dokładnie wykonaj instrukcje.

## <a name="turn-on-a-device-after-shutdown"></a>Włącz urządzenie po zamknięciu systemu
Włączanie urządzenia StorSimple, po jego zamknięciu kroki są różne w zależności od tego, czy urządzenie jest 8100 lub 8600 modelu. 8100 ma jednej obudowie głównej 8600 jest podwójnego Obudowa urządzenia przy użyciu podstawowego obudowy i obudowy EBOD.

* [Urządzenia przy użyciu tylko podstawowy obudowy](#device-with-primary-enclosure-only)
* [Urządzenia przy użyciu obudowy EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Urządzenia przy użyciu tylko podstawowy obudowy
Po zamknięciu należy użyć poniższej procedury do włączenia urządzenia StorSimple przy użyciu podstawowego obudowy i obudowy nie EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aby włączyć urządzenia przy użyciu tylko podstawowy obudowy
1. Upewnij się, że możliwości zmienia się na obu zasilania i chłodzenia modułów (PCMs) znajdują się w pozycji wył. Jeśli przełączniki nie znajdują się w pozycji WYŁĄCZONY, przerzucić pozycji Wył i poczekaj, aż światła go.
2. Włącz urządzenie, przestawiając wyłączniki zasilania na obu PCMs pozycji dalej. Włącz urządzenie.
3. Sprawdź następujące polecenie, aby sprawdzić, czy urządzenie jest w pełni na:
   
   1. Diod LED OK na obu modułów PCM są zielone.
   2. Stan diod LED oba kontrolery są zielony.
   3. Diody LED w jeden z kontrolerów jest migające, co oznacza, że kontroler jest aktywna.
      
      Jeśli którykolwiek z tych warunków nie zostanie spełniony, urządzenie nie jest dobra. Proszę [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Urządzenia przy użyciu obudowy EBOD
Po zamknięciu systemu należy użyć poniższej procedury do włączenia urządzenia StorSimple przy użyciu podstawowego obudowy i obudowy EBOD. Dokładnie zgodnie z opisem wykonywania każdego kroku w sekwencji. Niewykonanie tej czynności może spowodować utratę danych.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aby włączyć urządzenia przy użyciu podstawowego i obudowy EBOD
1. Upewnij się, że obudowa EBOD jest podłączony do głównej obudowy. Aby uzyskać więcej informacji, zobacz [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że zasilania i chłodzenia modułów (PCMs) EBOD i obudowy głównej znajdują się w pozycji wył. Jeśli przełączniki nie znajdują się w pozycji WYŁĄCZONY, przerzucić pozycji Wył i poczekaj, aż światła go.
3. Obudowa EBOD najpierw włączyć przestawiając wyłączniki zasilania na obu PCMs pozycji dalej. Diod LED PCM powinna być zielona. Zielony kontrolera EBOD LED w tej jednostce wskazuje, czy obudowa EBOD jest włączona.
4. Włącz głównej obudowy przestawiając wyłączniki zasilania na obu PCMs pozycji dalej. Cały system będą znajdować się na.
5. Sprawdź, czy diod LED sygnatury dostępu Współdzielonego są zielone, zapewnia, że połączenie między obudowy EBOD i obudowy podstawowy jest dobra.

## <a name="turn-on-a-device-after-a-power-loss"></a>Włącz urządzenie po utracie zasilania
Awarii zasilania lub przerwania można wyłączyć urządzenie StorSimple. Awaria zasilania może się zdarzyć na jeden z źródła zasilania lub obu zasilacze. Kroki odzyskiwania są różne w zależności od tego, czy urządzenie jest model 8100 lub 8600. 8100 ma jednej obudowie głównej 8600 jest podwójnego Obudowa urządzenia przy użyciu podstawowego obudowy i obudowy EBOD. W tej sekcji opisano procedury odzyskiwania dla każdego scenariusza.

* [Urządzenia przy użyciu tylko podstawowy obudowy](#8100)
* [Urządzenia przy użyciu obudowy EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Urządzenia przy użyciu tylko podstawowy obudowy <a name="8100">
System może nadal jego normalnej pracy, w przypadku utraty zasilania do jednej z jego zasilacze. Jednak w celu zapewnienia wysokiej dostępności urządzenia, przywrócenie moc zasilania tak szybko, jak to możliwe.

Jeśli istnieje, awarii zasilania lub awarii zasilania na obu zasilacze, system zostanie zamknięty w sposób uporządkowany i kontrolowany. Po przywróceniu możliwości, system automatycznie spowoduje włączenie.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Urządzenia przy użyciu obudowy EBOD <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Podaj utraty zasilania jednego zasilania
System może nadal jej normalnej pracy, w przypadku utraty zasilania do jednej z jego zasilacze w głównej obudowy lub obudowy EBOD. Jednak w celu zapewnienia wysokiej dostępności urządzenia, Przywróć zasilania na zasilanie tak szybko, jak to możliwe.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Utraty zasilania zasilacze, zarówno na podstawowej i obudów EBOD
Jeśli istnieje, awarii zasilania lub awarii zasilania na obu zasilacze, obudowy EBOD natychmiast zamknie i głównej obudowy zostanie zamknięty w sposób uporządkowany i kontrolowany. Po przywróceniu zasilania urządzenia zostanie uruchomiony automatycznie.

Jeśli jest włączone zasilanie ręcznie, następnie podjąć poniższe kroki, aby przywrócić zasilania w systemie.

1. Włącz obudowy EBOD.
2. Po obudowy EBOD jest włączona, należy włączyć głównej obudowy.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Utraty zasilania na obu zasilacze w obudowie EBOD
Po skonfigurowaniu kable upewnij się, czy EBOD jest nigdy nie nawiązano połączenia samodzielnie oddzielne PDU. Jeśli EBOD i obudowy podstawowego nie powiedzie się w tym samym czasie, zostanie przywrócona do działania systemu.

Jeśli tylko obudowy EBOD w obu zasilacze kończy się niepowodzeniem, system nie zostanie automatycznie odzyskana. Wykonaj poniższe kroki, aby włączyć system i przywracania go w dobrej kondycji:

1. Jeśli włączono obudowy głównej wyłączyć zasilania i chłodzenia modułów (PCMs).
2. Poczekaj kilka minut, zanim system zamknąć.
3. Włącz obudowy EBOD.
4. Po obudowy EBOD jest włączona, należy włączyć głównej obudowy.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Włącz urządzenie po podstawowej i EBOD obudowy połączenie zostanie przerwane
Jeśli połączenie zostanie przerwane między kontroler zapasowy i odpowiedniego kontrolera EBOD, urządzenie w dalszym ciągu działać. W przypadku utraty połączenia między aktywny kontroler systemu i odpowiedniego kontrolera EBOD trybu failover powinny być wykonywane i urządzenia będą nadal działać w zwykły sposób.

Gdy zarówno kable Serial Attached SCSI (SAS) są usuwane lub połączenie między obudowy EBOD i obudowy podstawowy jest oddzielone, urządzenie przestanie działać. W tym momencie wykonaj następujące czynności.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Aby włączyć na urządzeniu po połączenie zostanie przerwane
1. Dostęp do tyłu urządzenia.
2. Jeśli połączenie kablowe sygnatury dostępu Współdzielonego między obudowy EBOD i obudowy podstawowy został przerwany, wszystkie sygnatury dostępu Współdzielonego lane diod LED na obudowę EBOD będzie wyłączona.
3. Zamknij zasilania i chłodzenia modułów (PCMs) na obudowę EBOD i obudowy podstawowego.
4. Poczekaj, aż wszystkich świateł na odwrocie podkładki zarówno obudowach wyłączyć.
5. Włóż kabli SAS i upewnij się, że istnieje połączenie między obudowy EBOD i obudowy podstawowego.
6. Obudowa EBOD najpierw włączyć przestawiając oba przełączniki PCM pozycji dalej.
7. Upewnij się, że obudowa EBOD w, sprawdzając, czy zielona LED ma wartość ON.
8. Włącz funkcję podstawowego obudowy.
9. Upewnij się, że obudowa głównej w, sprawdzając LED kontrolera zielony ma wartość ON.
10. Sprawdź połączenie obudowy EBOD z podstawowego obudowy dobre, sprawdzając, czy za pomocą sygnatury dostępu Współdzielonego lane diod LED, (cztery na kontrolera EBOD) na wszystkich.

> [!IMPORTANT]
> Jeśli kabli SAS jest uszkodzony lub połączenie między obudowy EBOD i obudowy podstawowego jest nie są odpowiednie, po włączeniu w systemie, przejdzie do trybu odzyskiwania. Proszę [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) w takiej sytuacji.


## <a name="turn-off-a-running-device"></a>Wyłącz uruchamianie urządzenia
Uruchamianie urządzenia StorSimple może być konieczne zamknięcie Jeśli przenoszenia, podjęte poza usługi lub został nieprawidłowo działający składnik, który musi zostać zastąpiony. Kroki różnią się w zależności od tego, czy urządzenia StorSimple to 8100 lub 8600 model. 8100 ma jednej obudowie głównej 8600 jest podwójnego Obudowa urządzenia przy użyciu podstawowego obudowy i obudowy EBOD. W tej sekcji przedstawiono kroki, aby wyłączyć uruchamianie urządzenie.

* [Urządzenia przy użyciu podstawowego obudowy](#8100a)
* [Urządzenia przy użyciu obudowy EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Urządzenia przy użyciu podstawowego obudowy <a name="8100a">
Wyłączenie urządzenia w sposób uporządkowany i kontrolowany, możesz zrobić to za pośrednictwem witryny Azure portal lub za pośrednictwem programu Windows PowerShell dla usługi StorSimple. 

> [!IMPORTANT]
> Nie zamykaj uruchamianie urządzeń za pomocą przycisku zasilania na odwrocie podkładki urządzenia.
> 
> Przed zamknięciem urządzenie, upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W witrynie Azure portal przejdź do **urządzeń** > **Monitor** > **kondycja sprzętu**i sprawdź, czy stan wszystkich składników jest zielony. Jest to istotne tylko w przypadku dobrej kondycji systemu. Jeśli system jest wyłączone w celu wymienić nieprawidłowo działający składnik, zobaczą nie powiodło się (czerwony) lub uszkodzenie stanu (żółty) dla odpowiednich składnika w **stan sprzętu**.
> 
> 

Po uzyskujesz dostęp do programu Windows PowerShell dla usługi StorSimple lub witryny Azure portal, wykonaj kroki opisane w [wyłączyć urządzenie StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Urządzenia przy użyciu obudowy EBOD <a name="8600a">
> [!IMPORTANT]
> Przed wyłączeniem głównej obudowy i obudowy EBOD, upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W witrynie Azure portal przejdź do **urządzeń** > **Monitor** > **kondycja sprzętu**i sprawdź, czy wszystkie składniki są w dobrej kondycji.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Aby wyłączyć uruchamianie urządzenia przy użyciu obudowy EBOD
1. Wykonaj wszystkie kroki opisane w [wyłączyć urządzenie StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) dla podstawowego obudowy.
2. Po obudowy podstawowy jest zamknięta, należy zamknąć EBOD przestawiając wyłączanie zasilania i chłodzenia modułu (PCM) przełączników.
3. Aby sprawdzić, czy EBOD został zamknięty, sprawdź, czy wszystkie światła na odwrocie podkładki obudowy EBOD wyłączyć.

> [!NOTE]
> Kable sygnatury dostępu Współdzielonego, które są używane do łączenia obudowy EBOD głównej obudową nie powinny być usuwane do momentu, po zamknięciu systemu.

## <a name="next-steps"></a>Kolejne kroki
[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) w razie wystąpienia problemów podczas Włączanie lub wyłączanie urządzenia StorSimple.

