---
title: StorSimple 8000 seria wymiana komponentów sprzętowych | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak bezpiecznie wymienić pcms, baterii, modułów kontrolerów, kontrolerów EBOD, dysków i obudowy urządzenia StorSimple.
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
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: e05a37122647d4979089f0ba00b1fc15f9b84b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60321831"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Wymień komponent sprzętowy na urządzeniu z serii StorSimple 8000

## <a name="overview"></a>Omówienie
Samouczki zastępowania składników sprzętowych opisują składniki sprzętowe urządzenia z serii Microsoft Azure StorSimple 8000 oraz kroki niezbędne do ich usunięcia i wymiany. W tym artykule opisano ikony bezpieczeństwa, zawiera wskaźniki do szczegółowych samouczków i wyświetla listę składników, które są wymienne.

> [!IMPORTANT]
> Przed podjęciem próby usunięcia lub wymiany dowolnego komponentu StorSimple należy zapoznać się z [konwencjami ikon bezpieczeństwa](#safety-icon-conventions) i innymi [środkami ostrożności.](storsimple-safety.md)


### <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa
W poniższej tabeli opisano ikony bezpieczeństwa używane w tych samouczkach. Należy zwrócić szczególną uwagę na te ikony bezpieczeństwa, jak przejść przez kroki, aby usunąć i wymienić elementy urządzenia.

| Ikona | Tekst | Dodatkowe informacje |
|:--- |:--- |:--- |
| ![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**Niebezpieczeństwo!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, doprowadzi do śmierci lub poważnych obrażeń. To słowo sygnału jest ograniczone do najbardziej ekstremalnych sytuacji. |
| ![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**Ostrzeżenie!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, może spowodować śmierć lub poważne obrażenia ciała. |
| ![Ikona Przestroga](./media/storsimple-hardware-component-replacement/Caution.png) |**Ostrożność!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, może spowodować niewielkie lub umiarkowane obrażenia. |
| ![Ikona powiadomienia](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**Zauważyć:** |Wskazuje informacje uważane za ważne, ale nie związane z zagrożeniami. |
| ![Ikona porażenia prądem elektrycznym](./media/storsimple-hardware-component-replacement/Electric.png) |**Zagrożenie porażeniem prądem elektrycznym** |Wskazuje wysokie napięcie. |
| ![Ikona wagi ciężkiej](./media/storsimple-hardware-component-replacement/Weight.png) |**Waga ciężka** | |
| ![Brak ikony części z możliwością serwisowania użytkownika](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Brak części z możliwością serwisowania przez użytkownika** |Nie należy do nich korzystać, chyba że zostanie odpowiednio przeszkolony. |
| ![Ikona instrukcji czytania](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Najpierw przeczytaj wszystkie instrukcje** | |
| ![Ikona zagrożenia końcówką](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Ryzyko końcówki** | |

### <a name="before-you-begin"></a>Przed rozpoczęciem
Zapoznaj się z informacjami dotyczącymi bezpieczeństwa urządzenia i ikonami bezpieczeństwa używanymi w tym samouczku. Przejdź do [Bezpiecznego instalowania i obsługi urządzenia StorSimple, aby](storsimple-safety.md) uzyskać pełne informacje. Przed obsłużeniem urządzenia StorSimple należy zapoznać się z [zaleceniami dotyczącymi bezpieczeństwa.](storsimple-safety.md#handling-precautions)

Przed podjęciem próby zastąpienia składnika należy wziąć pod uwagę następujące informacje.

![Ikona](./media/storsimple-hardware-component-replacement/Warning.png) ![ostrzeżenia](./media/storsimple-hardware-component-replacement/Electric.png) Ikona porażenia prądem **elektrycznym OSTRZEŻENIE!**

* Uziemić się prawidłowo za pomocą wyładowania elektrostatycznego lub maty antystatycznej podczas obsługi modułów i komponentów urządzenia StorSimple.
* Nie dotykaj żadnych obwodów. Dołączone uchwyty i prowadnice należy używać podczas obsługi komponentów, które mogły mieć odsłonięte obwody.

![Ostrzeżenie](./media/storsimple-hardware-component-replacement/Warning.png) ![Ikona](./media/storsimple-hardware-component-replacement/NoticeIcon.png) Zawiadomienie **Uwaga:**

Po wymianie modułu **NIGDY nie pozostawiaj pustej wnęki z tyłu obudowy.** Przed usunięciem części problemowej należy uzyskać moduł zastępczy lub pusty.

## <a name="hardware-component-replacement-procedures"></a>Procedury wymiany komponentów sprzętowych
Urządzenie z serii StorSimple 8000 składa się z kilku modułów wtyczek w obudowach podstawowych i/lub EBOD. Model 8100 jest wyposażony w pojedynczą obudowę podstawową, natomiast model 8600 jest podwójną obudową z obudową podstawową i obudową EBOD.

Główne składniki sprzętowe urządzenia są podsumowane w poniższych tabelach. Kliknij łącze w kolumnie **Procedura zastępowanie,** aby przejść do skojarzonego samouczka.

| Składniki | # Obecny | Moduł plug-in? | Procedura wymiany |
|:--- |:--- |:--- |:--- |
| Obudowa |1 |Nie |[Wymień obudowę urządzenia StorSimple](storsimple-8000-chassis-replacement.md) |
| Sterowniki podstawowe |2 |Tak |[Wymień moduł kontrolera na urządzeniu StorSimple](storsimple-8000-controller-replacement.md) |
| Moduły zasilania i chłodzenia 764W (PCM) |2 |Tak |[Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Zapasowa bateria |2 |Tak |[Wymiana modułu baterii zapasowej w urządzeniu StorSimple](storsimple-8000-battery-replacement.md) |
| Stacje dysków |12 |Tak |[Zastępowanie dysku na urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** Składniki sprzętowe w obudowie podstawowej

Obudowa podstawowa i obudowa EBOD różnią się modułami we/wy. Dodatkowo, PCMs mają różne moce. PcMs w obudowie podstawowej są 764 W, podczas gdy te w obudowie EBOD są 580 W. PcM w obudowie podstawowej zawiera również moduł baterii zapasowej.

| Składniki | # Obecny | Moduł plug-in? | Procedura wymiany |
|:--- |:--- |:--- |:--- |
| Obudowa |1 |Nie |[Wymień obudowę urządzenia StorSimple](storsimple-8000-chassis-replacement.md) |
| Sterowniki EBOD |2 |Tak |[Wymień kontroler EBOD na urządzeniu StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| Moduły zasilania i chłodzenia 580W (PCM) |2 |Tak |[Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Stacje dysków |12 |Tak |[Zastępowanie dysku na urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** Komponenty sprzętowe w obudowie EBOD

Moduły wtyczek urządzenia są podświetlone na poniższych schematach przednich i tylnych. Za pomocą tych diagramów można określić lokalizację różnych modułów wtyczek, jeśli wymagana jest wymiana. Przedni diagram pokazuje napędy dysków, a na tylnych diagramach obudowy EBOD i głównej obudowy wyświetlane są moduły wtykowe.

![Przedni panel urządzenia z napędami dyskowymi](./media/storsimple-hardware-component-replacement/IC741028.png)

**Rysunek 1** Przód urządzenia

| Label | Opis |
|:--- |:--- |
| 0 - 11 |Dyski (łącznie 12) |

Zarówno obudowa podstawowa, jak i obudowa EBOD są posiadane moduły nośne napędów. W obudowie znajduje się dwanaście dysków 3,5" rozmieszczonych w formacie 3 na 4.

![Zaoplanowa obudowy podstawowego urządzenia](./media/storsimple-hardware-component-replacement/IC740994.png)

**Rysunek 2** Tył obudowy podstawowej

| Label | Opis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontroler 0 |
| 4 |Kontroler 1 |

![Zaoplanowa instalacja modułów wtykowych obudowy EBOD urządzenia](./media/storsimple-hardware-component-replacement/IC769599.png)

**Rysunek 3** Tył obudowy EBOD

| Label | Opis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Sterownik EBOD 0 |
| 4 |Sterownik EBOD 1 |

## <a name="field-replaceable-units"></a>Jednostki wymienne w terenie
Dla urządzenia StorSimple dostępne są następujące jednostki zastępujące pole:

* Podwozie (w tym zintegrowany panel operacyjny)
* 764 W AC PCM
* 580 W AC PCM
* Dysk twardy z modułem nośnika dysków
* Moduł kontrolera
* Moduł kontrolera EBOD
* Moduł baterii zapasowej
* Zestaw szyn montażowych w stelażu

Skontaktuj [się z pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby zamówić którykolwiek z tych urządzeń zastępczych.

## <a name="next-steps"></a>Następne kroki
Przejrzyj wszystkie [informacje dotyczące bezpieczeństwa](storsimple-safety.md) przed podjęciem próby zastąpienia składnika sprzętowego StorSimple.

