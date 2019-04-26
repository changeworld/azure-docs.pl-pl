---
title: Wymiana składników sprzętowych serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak bezpiecznie zastąpić PCMs, baterii, modułów kontrolera, EBOD kontrolerów, dysków i obudów urządzenia StorSimple.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60321831"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Wymienić składnik sprzętowy w urządzeniu StorSimple 8000 series

## <a name="overview"></a>Omówienie
Zastąpienie składnika samouczki opisano składniki sprzętowe w urządzeniu serii Microsoft Azure StorSimple 8000 i kroki niezbędne do usunięcia, a zastępuje je. W tym artykule opisano ikony bezpieczeństwa, zawiera wskaźniki do szczegółowymi samouczkami i wyświetla listę składników, które są wymienne.

> [!IMPORTANT]
> Przed podjęciem próby. Usuń lub Zamień dowolny składnik usługi StorSimple, upewnij się, aby przejrzeć [konwencje ikonę bezpieczeństwa](#safety-icon-conventions) i innych [środki ostrożności](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Konwencje ikonę bezpieczeństwa
W poniższej tabeli opisano ikon bezpieczeństwa, używanych w tych samouczkach. Podczas wykonywania kroków kroki, aby usunąć i Zastąp składniki urządzenia, należy zwracać szczególną uwagę na tych ikon bezpieczeństwa.

| Ikona | Text | Dodatkowe informacje |
|:--- |:--- |:--- |
| ![ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**ZAGROŻENIA!** |Wskazuje niebezpiecznych sytuację, która nie jest to uniknąć, spowoduje śmierci lub poważnemu. Ten wyraz sygnał jest ograniczona do najbardziej ekstremalnych przypadkach. |
| ![ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**OSTRZEŻENIE!** |Wskazuje sytuację zagrożenia, jeśli nie jest to uniknąć, może spowodować śmierci lub poważne szkody. |
| ![Ikona ostrzegawcza](./media/storsimple-hardware-component-replacement/Caution.png) |**UWAGA!** |Wskazuje sytuację zagrożenia, jeśli nie jest to uniknąć, może spowodować szkody drobnych lub Średni. |
| ![Ikona powiadomienia](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**UWAGA:** |Wskazuje informacje uznawane za ważne, ale niepowiązane zagrożenia. |
| ![Ikona elektrycznych wstrząsy](./media/storsimple-hardware-component-replacement/Electric.png) |**Zagrożenia elektrycznych wstrząsy** |Wskazuje wysokie napięcie. |
| ![Waga duże ikony](./media/storsimple-hardware-component-replacement/Weight.png) |**Ciężki** | |
| ![Nie ikony części zdatne do użytku użytkownika](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nie części zdatne do użytku użytkownika** |Nie ma dostępu, chyba że odpowiednio przeszkoleni. |
| ![Przeczytaj instrukcje ikony](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Najpierw Przeczytaj wszystkie instrukcje** | |
| ![Ikona zagrożenie wskazówki](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Porada zagrożenia** | |

### <a name="before-you-begin"></a>Przed rozpoczęciem
Zapoznaj się z bezpieczeństwa informacje używane w tym samouczku ikony urządzenia i bezpieczeństwa. Przejdź do [bezpiecznie zainstalowania i obsługi urządzenia StorSimple](storsimple-safety.md) pełne informacje. Należy przejrzeć [środki ostrożności](storsimple-safety.md#handling-precautions) przed obsługiwać urządzenia StorSimple.

Przed podjęciem próby wymienić składnik, należy wziąć pod uwagę następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) ![ikonę elektrycznych wstrząsy](./media/storsimple-hardware-component-replacement/Electric.png) **ostrzeżenie!**

* Prowadzić samodzielnie prawidłowo przy użyciu elektrostatyczne lub antistatic mat podczas obsługi modułów i składniki urządzenia StorSimple.
* Nie będą dotykać dowolnej obwodu. Użyj podane dojścia i przewodniki dotyczące podczas obsługi składniki, które mogą udostępnić obwodu.

![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) ![ikony](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **powiadomienia:**

Podczas zastępowania modułu **nigdy nie należy pozostawiać pustego bay tyłu obudowa**. Uzyskaj zastąpienia lub pusty moduł przed usunięciem część problem.

## <a name="hardware-component-replacement-procedures"></a>Procedury wymiany składnika sprzętu
Urządzenie StorSimple 8000 series składa się z kilku wtyczki, które znajdują się w podstawowej i/lub EBOD obudowy. 8100 ma jednej obudowie głównej 8600 jest podwójną Obudowa urządzenia przy użyciu podstawowego obudowy i obudowy EBOD.

Składniki sprzętowe głównego na urządzeniu są podsumowane w poniższej tabeli. Kliknij link w **procedury wymiany** kolumny, aby przejść do samouczka skojarzone.

| Składniki | # Obecna | Moduł wtyczki? | Procedura zastępowania |
|:--- |:--- |:--- |:--- |
| Podstawa montażowa |1 |Nie |[Wymiana obudowy na urządzeniu StorSimple](storsimple-8000-chassis-replacement.md) |
| Podstawowy kontrolerów |2 |Yes |[Zastąp moduł kontrolera na urządzeniu StorSimple](storsimple-8000-controller-replacement.md) |
| 764W zasilania i chłodzenia modułów (PCMs) |2 |Yes |[Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Baterii zapasowej |2 |Yes |[Wymiana modułu baterii zapasowej w urządzeniu StorSimple](storsimple-8000-battery-replacement.md) |
| Stacje dysków |12 |Yes |[Wymiana dysku, na urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** składniki sprzętowe w obudowie podstawowego

Podstawowy obudowy i obudowy EBOD różnią się w ich modułów we/wy. Ponadto PCMs mają różne moc. PCMs w obudowie podstawowego są 764 W, a te w obudowie EBOD 580 W. PCMs w obudowie podstawowy również zawierać moduł baterii zapasowej.

| Składniki | # Obecna | Moduł wtyczki? | Procedura zastępowania |
|:--- |:--- |:--- |:--- |
| Podstawa montażowa |1 |Nie |[Wymiana obudowy na urządzeniu StorSimple](storsimple-8000-chassis-replacement.md) |
| Kontrolery EBOD |2 |Yes |[Wymiana kontrolera EBOD na urządzeniu StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W zasilania i chłodzenia modułów (PCMs) |2 |Yes |[Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Stacje dysków |12 |Yes |[Wymiana dysku, na urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** składniki sprzętowe w obudowie EBOD

Wtyczki na urządzeniu zostaną wyróżnione w następujących przedniej i tylnej diagramów. Do określenia lokalizacji różnych wtyczki, jeśli wymagana jest zamiennikiem, można użyć tych diagramów. Front diagram pokazuje dysków oraz tylnej diagramów obudowy EBOD i Pokaż głównej obudowy moduły dodatków plug-in.

![Panelu przedniego urządzenia z stacje dysków](./media/storsimple-hardware-component-replacement/IC741028.png)

**Rysunek 1** frontonu urządzenia

| Label | Opis |
|:--- |:--- |
| 0 - 11 |Stacje dysków (łącznie z 12) |

Podstawowy obudowy i obudowy EBOD mają modułów operatora dysku. Obudowa przechowuje dwunastu 3,5" dyski w formacie 3, 4.

![Płyty montażowej modułów głównej Obudowa urządzenia](./media/storsimple-hardware-component-replacement/IC740994.png)

**Rysunek 2** tylnej stronie głównej obudowy

| Label | Opis |
|:--- |:--- |
| 1 |MODULE PCM 0 |
| 2 |PCM 1 |
| 3 |Kontrolera 0 |
| 4 |Kontrolera 1 |

![Montażowa wtyczki urządzenia EBOD obudowy](./media/storsimple-hardware-component-replacement/IC769599.png)

**Rysunek 3** tylnej stronie obudowy EBOD

| Label | Opis |
|:--- |:--- |
| 1 |MODULE PCM 0 |
| 2 |PCM 1 |
| 3 |Kontrolera EBOD 0 |
| 4 |Kontrolera EBOD 1 |

## <a name="field-replaceable-units"></a>Pola jednostki
Następujące pola wymienne liczby jednostek (FRU) są dostępne dla urządzenia StorSimple:

* Podstawa montażowa (w tym panelu Operacje zintegrowane)
* 764 W AC PCM
* 580 W AC PCM
* Dysk twardy z modułem operatora dysku
* Moduł kontrolera
* Moduł kontrolera EBOD
* Moduł baterii zapasowej
* W stelażu kit szyny

Proszę [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) do dowolnego z tych jednostek zastąpienie kolejność.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj wszystkie [bezpieczeństwa informacji](storsimple-safety.md) przed podjęciem próby wymienić składnik sprzętowy StorSimple.

