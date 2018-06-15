---
title: Wymiana składników sprzętowych serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje sposób bezpieczne zastąpienie PCMs, baterii, moduły kontrolera, EBOD kontrolerów, dysków i obudowy urządzenia StorSimple.
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
ms.openlocfilehash: 6de50c5031db59176bdf17ecc69b934559220f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927607"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Zamień na urządzeniu z serii StorSimple 8000 składnik sprzętowy

## <a name="overview"></a>Omówienie
Zastąpienie składnika samouczki opisano składniki sprzętowe urządzenia Microsoft Azure StorSimple 8000 serii i kroki niezbędne do usunięcia i zastąp je. W tym artykule opisano ikony bezpieczeństwa, udostępnia wskaźniki do szczegółowe samouczki oraz listy składników, które są wymienne.

> [!IMPORTANT]
> Przed próbą należy usunąć lub zamienić dowolny składnik StorSimple, upewnij się, należy przejrzeć [bezpieczeństwa ikona konwencje](#safety-icon-conventions) i innych [środki ostrożności](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Konwencje ikona bezpieczeństwa
W poniższej tabeli opisano ikon bezpieczeństwa, używanych w tych samouczkach. Zwracać szczególną uwagę na następujące ikony bezpieczeństwa podczas wykonywania kroków w celu usunięty i zastąpiony składniki.

| Ikona | Tekst | Dodatkowe informacje |
|:--- |:--- |:--- |
| ![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**ZAGROŻENIA!** |Wskazuje niebezpiecznych sytuacji, który nie uniknąć spowoduje śmierci lub poważnej szkody. Ten wyraz sygnału jest ograniczona do najbardziej skrajne sytuacji. |
| ![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) |**OSTRZEŻENIE!** |Wskazuje niebezpiecznych sytuację, w której, jeśli nie jest to uniknąć, może spowodować śmierci lub poważnej szkody. |
| ![Ikonę ostrzeżenia](./media/storsimple-hardware-component-replacement/Caution.png) |**UWAGA!** |Wskazuje sytuację zagrożenia co, jeśli nie jest to uniknąć, może spowodować szkody drobne lub średniego. |
| ![Ikona powiadomienia](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**UWAGA:** |Wskazuje informacje uznawane za ważne, ale nie zagrożenia związane z. |
| ![Ikona elektrycznego uderzenia](./media/storsimple-hardware-component-replacement/Electric.png) |**Uderzenia elektrycznego zagrożenia** |Wskazuje wysokie napięcie. |
| ![Ikona ciężki](./media/storsimple-hardware-component-replacement/Weight.png) |**Ciężki** | |
| ![Brak ikony obsługiwanych części użytkownika](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nie części obsługiwanych użytkownika** |Brak dostępu do chyba, że poprawnie uczony. |
| ![Ikona instrukcje odczytu](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Najpierw przeczytać wszystkie instrukcje** | |
| ![Ikona zagrożenie wskazówki](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Porada zagrożenia** | |

### <a name="before-you-begin"></a>Przed rozpoczęciem
Zapoznaj się z bezpieczeństwa informacje używane w tym samouczku ikony urządzenia i bezpieczeństwa. Przejdź do [bezpiecznie zainstalowania i obsługi urządzenia StorSimple](storsimple-safety.md) pełne informacje. Należy przejrzeć [środki ostrożności](storsimple-safety.md#handling-precautions) przed obsługi urządzenia StorSimple.

Przed przystąpieniem do zastąpienia składnika, należy wziąć pod uwagę następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) ![ikona uderzenia elektrycznego](./media/storsimple-hardware-component-replacement/Electric.png) **ostrzeżenie!**

* Tła samodzielnie prawidłowo przy użyciu elektrostatyczne lub antistatic mat podczas obsługi modułów i składniki urządzenia StorSimple.
* Nie touch żadnych obwodu. Podczas obsługi składników, które mogą być narażone obwody za pomocą dostarczonego dojść i przewodniki.

![Ikona ostrzeżenia](./media/storsimple-hardware-component-replacement/Warning.png) ![ikony](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **POWIADOMIEŃ:**

Podczas zastępowania modułu, **nigdy nie należy pozostawiać pustego bay tyłu obudowa**. Uzyskaj zastąpienia lub pusty moduł przed usunięciem części problem.

## <a name="hardware-component-replacement-procedures"></a>Procedury wymiany składników sprzętu
Urządzenie serii StorSimple 8000 składa się z kilku wtyczki w podstawowej i/lub EBOD obudowy. 8100 ma jednej obudowie głównej 8600 jest urządzeniem podwójną obudowa z głównej obudowy i obudowy EBOD.

Składniki sprzętowe głównego w urządzeniu podsumowano w poniższych tabelach. Kliknij łącze w **procedury wymiany** kolumny, aby przejść do skojarzonego samouczka.

| Składniki | # Obecnie | Moduł wtyczki? | Procedury wymiany |
|:--- |:--- |:--- |:--- |
| Podstawa montażowa |1 |Nie |[Zastąp podstawę na urządzeniu StorSimple](storsimple-8000-chassis-replacement.md) |
| Podstawowy kontrolerów |2 |Tak |[Zastąp modułu kontroler na urządzeniu StorSimple](storsimple-8000-controller-replacement.md) |
| 764W zasilania i chłodzenia modułów (PCMs) |2 |Tak |[Zamień na urządzeniu StorSimple zasilania i chłodzenia modułu](storsimple-8000-power-cooling-module-replacement.md) |
| Kopia zapasowa baterii |2 |Tak |[Zastąpienie modułu baterii kopii zapasowych w urządzeniu StorSimple](storsimple-8000-battery-replacement.md) |
| Stacje dysków |12 |Tak |[Zamień na dysku w urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** składniki sprzętowe w obudowie podstawowego

Podstawowy obudowy i obudowy EBOD różnią się w ich modułów we/wy. Ponadto PCMs mają różne moc. PCMs w obudowie głównej są 764 W, a w obudowie EBOD 580 W. PCMs w obudowie podstawowy również zawierać modułu baterii kopii zapasowej.

| Składniki | # Obecnie | Moduł wtyczki? | Procedury wymiany |
|:--- |:--- |:--- |:--- |
| Podstawa montażowa |1 |Nie |[Zastąp podstawę na urządzeniu StorSimple](storsimple-8000-chassis-replacement.md) |
| Kontrolery EBOD |2 |Tak |[Zastąp kontrolera EBOD na urządzeniu StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W zasilania i chłodzenia modułów (PCMs) |2 |Tak |[Zamień na urządzeniu StorSimple zasilania i chłodzenia modułu](storsimple-8000-power-cooling-module-replacement.md) |
| Stacje dysków |12 |Tak |[Zamień na dysku w urządzeniu StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** składniki sprzętowe w obudowie EBOD

Wtyczki na urządzeniu są wyróżniane w przód następujące i diagramy tylnej. Diagramy te można określić lokalizacji różnych wtyczki, jeśli wymagana jest zastępczy. Front diagram zawiera dysków oraz tylnej diagramy obudowa EBOD i Pokaż obudowa głównej moduły dodatków plug-in.

![Frontplane urządzenia z dyskami](./media/storsimple-hardware-component-replacement/IC741028.png)

**Rysunek 1** Front urządzenia

| Etykieta | Opis |
|:--- |:--- |
| 0 - 11 |Stacje dysków (łącznie z 12) |

Zarówno obudowa podstawowego, jak i obudowy EBOD ma dysk nośnika modułów. Obudowy przechowuje dwanaście 3,5" dyski w formacie 3 na 4.

![Płyty montażowej urządzenia podstawowego obudowy modułów](./media/storsimple-hardware-component-replacement/IC740994.png)

**Rysunek 2** obu obudowa podstawowego

| Etykieta | Opis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontrolera 0 |
| 4 |Kontrolera 1 |

![Montażowa urządzenia EBOD obudowa wtyczki](./media/storsimple-hardware-component-replacement/IC769599.png)

**Rysunek 3** obu obudowa EBOD

| Etykieta | Opis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD kontrolera 0 |
| 4 |EBOD kontrolera 1 |

## <a name="field-replaceable-units"></a>Pole jednostki
Następujące pola replaceable liczby jednostek (FRU) są dostępne dla urządzenia StorSimple:

* Podstawa montażowa (w tym panelu Operacje zintegrowane)
* 764 W AC PCM
* 580 W AC PCM
* Dysk twardy z modułem operatora dysku
* Moduł kontrolera
* Moduł kontrolera EBOD
* Moduł kopii zapasowej baterii
* Instalowanie zestawu kolei stojak

Sprawdź [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) żadnego z tych jednostek zastąpienie porządkowania.

## <a name="next-steps"></a>Następne kroki
Przejrzyj wszystkie [bezpieczeństwa informacji](storsimple-safety.md) przed przystąpieniem do zastąpienia składnik sprzętowy StorSimple.

