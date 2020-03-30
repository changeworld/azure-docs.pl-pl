---
title: Wymień baterię na urządzeniu z serii Microsoft Azure StorSimple 8000
description: W tym artykule opisano sposób usuwania, wymiany i obsługi modułu baterii zapasowej na urządzeniu StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255017"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Wymiana modułu baterii zapasowej w urządzeniu StorSimple

## <a name="overview"></a>Omówienie
Podstawowa obudowa Moduł zasilania i chłodzenia (PCM) na urządzeniu Microsoft Azure StorSimple ma dodatkowy pakiet baterii. Ten pakiet zapewnia zasilanie, dzięki czemu urządzenie StorSimple może zapisywać dane w przypadku utraty zasilania prądem zmiennym w obudowie podstawowej. Ten akumulator jest określany jako *zapasowy moduł baterii*. Moduł baterii zapasowej istnieje tylko dla obudowy podstawowej urządzenia StorSimple (obudowa EBOD nie zawiera zapasowego modułu baterii).

W tym samouczku wyjaśniono:

* Wyjmij zapasowy moduł baterii
* Zainstaluj nowy moduł baterii zapasowej
* Obsługa modułu baterii zapasowej

> [!IMPORTANT]
> Przed wyjęciem i wymianą zapasowego modułu baterii zapoznaj się z informacjami o bezpieczeństwie zawartymi w [części wymiennej komponentu sprzętowego Wprowadzenie do storsimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Wyjmij zapasowy moduł baterii
Moduł baterii zapasowej urządzenia StorSimple jest jednostką wymienną w terenie. Przed zainstalowaniem w PCM moduł baterii należy przechowywać w oryginalnym opakowaniu. Wykonaj następujące czynności, aby wyjąć baterię zapasową.

#### <a name="to-remove-the-backup-battery-module"></a>Aby wyjąć zapasowy moduł baterii
1. W witrynie Azure portal przejdź do bloku usługi StorSimple Device Manager. Przejdź do **witryny Urządzenia,** a następnie wybierz urządzenie z listy urządzeń. Przejdź do **pozycji Monitoruj** > **kondycję sprzętu**. W obszarze **Składniki udostępnione**przyjrzyj się statusowi baterii.
2. Zidentyfikuj pcm, w którym bateria uległa awarii. Rysunek 1 przedstawia tył urządzenia StorSimple.
   
    ![Zaoplanowa podstawowych modułów obudowy urządzenia](./media/storsimple-battery-replacement/IC740994.png)
   
    **Rysunek 1** Tylne urządzenie główne z modułami PCM i kontrolerami
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
   
    Jak pokazano na rysunku 2, wskaźnik monitorowania LED na PCM 0, który odpowiada **awarii baterii,** powinien być zapalony.
   
    ![Płyta montażowa diod LED wskaźnika PCM urządzenia](./media/storsimple-battery-replacement/IC740992.png)
   
    **Rysunek 2** Tył PCM pokazujący diody LED wskaźnika monitorowania
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Awaria zasilania prądem przemienna |
   | 2 |Awaria wentylatora |
   | 3 |Awaria baterii |
   | 4 |PCM OK |
   | 5 |Awaria zasilania prądem stałym |
   | 6 |Bateria zdrowa |
3. Aby wyjąć komputer PCM z nieudaną baterią, wykonaj czynności opisane w [trybie Usuń pcm](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Po wyjęciu pcm podnieś i obróć uchwyt modułu baterii w górę, jak wskazano na poniższym rysunku, i pociągnij go do góry, aby wyjąć baterię.
   
    ![Wyjmowanie baterii z PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Rysunek 3** Wyjmowanie baterii z pcm
5. Umieść moduł w opakowaniu jednostkowym z wymiennym w terenie.
6. Należy zwrócić wadliwe urządzenie do firmy Microsoft w celu prawidłowego serwisowania i obsługi.

## <a name="install-a-new-backup-battery-module"></a>Zainstaluj nowy moduł baterii zapasowej
Wykonaj następujące czynności, aby zainstalować wymienny moduł baterii w pcm w głównej obudowie urządzenia StorSimple.

#### <a name="to-install-the-battery-module"></a>Aby zainstalować moduł baterii
1. Umieść zapasowy moduł baterii w odpowiedniej orientacji w PCM.
2. Naciśnij uchwyt modułu akumulatora do końca, aby zamówić złącze.
3. Wymień pcm w obudowie podstawowej, postępując zgodnie z wytycznymi zawartymi w [wymień moduł zasilania i chłodzenia na urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Po zakończeniu wymiany przejdź do urządzenia, a następnie przejdź do **monitorowania** > **kondycji sprzętu** w witrynie Azure portal. Sprawdź stan baterii, aby upewnić się, że instalacja zakończyła się pomyślnie. Zielony stan oznacza, że bateria jest w dobrej kondycji.

## <a name="maintain-the-backup-battery-module"></a>Obsługa modułu baterii zapasowej
W urządzeniu StorSimple moduł baterii zapasowej zapewnia zasilanie kontrolera podczas zdarzenia utraty zasilania. Umożliwia urządzeniu StorSimple zapisywanie krytycznych danych przed zamknięciem w kontrolowany sposób. Dzięki dwóm w pełni naładowanym bateriom w pcms system może obsłużyć dwa kolejne zdarzenia utraty.

W witrynie Azure portal kondycja **sprzętu** w obszarze **monitora** bloku wskazuje, czy bateria jest nieprawidłowo działać lub zbliża się koniec eksploatacji. Stan baterii jest wskazywany przez **baterię w PCM 0** lub **baterię w PCM 1** w obszarze **Współdzielone komponenty**. Ostrze to pokaże stan **degraded** dla końca życia zbliża się, i **nie udało się** do końca życia osiągnięte.

> [!NOTE]
> Bateria może zgłosić failed, gdy po prostu musi być **naładowana.**


Jeśli pojawi się stan **DEGRADED,** zaleca się następujące działania:

* System mógł doświadczyć ostatniej utraty zasilania lub baterie mogą być poddawane okresowej konserwacji. Należy przestrzegać systemu przez 12 godzin przed kontynuowaniem.
  
  * Jeśli stan jest nadal **obniżony** po 12 godzinach ciągłego podłączenia do zasilania prądem przemiennym z uruchomionymi kontrolerami i pcmami, należy wymienić baterię. Skontaktuj [się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu uzyskania zastępczego modułu baterii zapasowej.
  * Jeśli stan stanie się OK po 12 godzinach, bateria działa i wymaga tylko opłaty konserwacyjnej.
* Jeśli nie doszło do związanej z tym utraty zasilania prądem przemiennym, a pcm jest włączony i podłączony do zasilania prądem zmiennym, należy wymienić baterię. [Skontaktuj się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby zamówić zastępczy moduł baterii zapasowej.

> [!IMPORTANT]
> Zutylizuj zepsutą baterię zgodnie z przepisami krajowymi i regionalnymi.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

