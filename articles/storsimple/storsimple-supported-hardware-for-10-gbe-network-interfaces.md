---
title: Interfejsy sprzętu dla StorSimple 10 GbE | Dokumentacja firmy Microsoft
description: W tym artykule opisano obsługiwane małych obudowie podłączane odbiorcze (SFP), kabli i przełączników dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: db03b3cd668bf8e35913872ac4225de6d4d3edd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927754"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Obsługiwane sprzętu dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple
## <a name="overview"></a>Omówienie
Ten artykuł zawiera informacje o dodatkowych sprzętu, który współpracuje z urządzenia Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista urządzeń przetestowane przez firmę Microsoft
Firma Microsoft przetestowała następujące małych obudowie podłączany (SFP) nadawczo-odbiorczych, kabli i przełączników w celu zapewnienia ich optymalnie działanie z urządzeniami. (Będzie można zaktualizować następujących tabel, jak nowy sprzęt jest testowana.)

### <a name="sfp-transceivers"></a>Nadawczo-odbiorczych SFP +
| Wprowadź | Model |
| --- | --- |
| Cisco |SR-SFP — 10 GB/S |

### <a name="cables"></a>Kable
| Mag. Nie. | Wprowadź | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp Lite |N820 - 05M (OM3) |

### <a name="switches"></a>Przełączniki
| Mag. Nie. | Wprowadź | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista urządzeń testowane w polu
Ta sekcja zawiera listę urządzeń, które zostały pomyślnie wdrożone w polu przez klientów StorSimple. Te nie zostały one przetestowane przez firmę Microsoft, ale mogą działać na urządzeniu StorSimple.

| Parametr | Wartość |
| --- | --- |
| Wprowadź przełącznik |Juniper |
| Model przełącznika |ex4550 32F |
| Wersja systemu operacyjnego przełącznika |JunOS 12.3R9.4 |
| Model bloku |Porty dołączyć (PIC 0) |
| Utwórz odbiorcze |Juniper |
| Model odbiorcze |Numer części 740 021308 <br></br> Numer części 740 030658 |
| Wersja oprogramowania układowego odbiorcze |Rev 01 wersji 0,0 (zgłosić) |
| Model kabel |Dupleks zworek LC/LC 50/125µ, OM3, LSZH |
| StorSimple model |8600 |
| Wersja oprogramowania StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista urządzeń przetestowane przez producenta OEM dostawcy (Mellanox)
Mellanox przetestowała następujące małych obudowie podłączany (SFP) nadawczo-odbiorczych, kabli i przełączników w celu zapewnienia ich optymalnie działanie z Mellanox interfejsy sieciowe, takie jak interfejsy sieciowe 10 GbE w urządzeniu StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabli i obsługiwane przez Mellanox modułów
W poniższej tabeli wymieniono kabli i obsługiwane przez Mellanox modułów. Te nie zostały one przetestowane przez firmę Microsoft, ale mogą działać na urządzeniu StorSimple.

| Mag. Nie. | Szybkość | Model | Opis | Wprowadź |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |PLIK CAB-SFP-SFP - 1M |pasywne kabel miedzi SFP + 10 Gb/s, 1m |Arista |
| 2. |10 GbE |PLIK CAB-SFP-SFP - 2M |pasywne kabel miedzi SFP + 10 Gb/s 2m |Arista |
| 3. |10 GbE |PLIK CAB-SFP-SFP - 3M |pasywne kabel miedzi SFP + 10 Gb/s, 3m |Arista |
| 4. |10 GbE |PLIK CAB-SFP-SFP - 5M |pasywne kabel miedzi SFP + 10 Gb/s, 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10 GB/S |SFP + SFP + 1 mln bezpośrednie Podłącz kabel miedzi |HP |
| 9. |10 GbE |BLc HP 455883 B21 |10Gb SR SFP + opcjonalnych |HP |
| 10. |10 GbE |BLc HP 455886 B21 |10Gb LR SFP + opcjonalnych |HP |
| 11. |10 GbE |BLc HP 487649 B21 |Kabel miedzi SFP + m 0,5 10 GbE. |HP |
| 12. |10 GbE |BLc HP 487652 B21 |Kabel miedzi SFP + 1m 10 GbE. |HP |
| 13. |10 GbE |BLc HP 487655 B21 |Kabel miedzi SFP + 3m 10 GbE. |HP |
| 14. |10 GbE |BLc HP 487658 B21 |Kabel miedzi SFP + 7m 10 GbE. |HP |
| 15. |10 GbE |BLc HP 537963 B21 |Kabel miedzi SFP + 5m 10 GbE. |HP |
| 16. |10 GbE |AP784A HP |3m serii C pasywnym miedzi SFP + kabel |HP |
| 17. |10 GbE |AP785A HP |5m serii C pasywnym miedzi SFP + kabel |HP |
| 18. |10 GbE |AP818A HP |1 mln B serii Active miedzi SFP + kabel |HP |
| 19. |10 GbE |AP819A HP |3m B serii Active miedzi SFP + kabel |HP |
| 20. |10 GbE |J9150A HP |X132 10 G SFP + LC SR odbiorcze |HP |
| 21. |10 GbE |J9151A HP |X132 10 G SFP + LC LR odbiorcze |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3 m DAC kabel |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 m DAC kabel |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0.65 m DAC kabel |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + 1.2 m DAC kabel |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3 m Tato kabel |HP |
| 27. |10 GbE |Mellanox MAM1Q00A QSA |QSFP SFP + karty |Technologie Mellanox |
| 28. |10 GbE |MC2309124 006 Mt |X SFP+ pasywnych kabel miedzi 1 do QSFP 24awg 10 Gb/s 7 m |Technologie Mellanox |
| 29. |10 GbE |MC2309124 007 Mt |X SFP+ pasywnych kabel miedzi 1 do QSFP 24awg 10 Gb/s 7 m |Technologie Mellanox |
| 30. |10 GbE |Mt MC2309130 003 |X SFP+ pasywnych kabel miedzi 1 do QSFP 30awg 10 Gb/s 3 m |Technologie Mellanox |
| 31. |10 GbE |Mt MC2309130 00A |X SFP+ pasywnych kabel miedzi 1 do QSFP 30awg 10 Gb/s 0,5 m |Technologie Mellanox |
| 32. |10 GbE |Mt MC3309124 005 |Pasywne miedzi kabla 1 24awg 10 Gb/s x SFP+ 5 m |Technologie Mellanox |
| 33. |10 GbE |MC3309124 007 Mt |Pasywne miedzi kabla 1 24awg 10 Gb/s x SFP+ 7 m |Technologie Mellanox |
| 34. |10 GbE |Mt MC3309130 003 |Pasywne miedzi kabla 1 30awg 10 Gb/s x SFP+ 3 m |Technologie Mellanox |
| 35. |10 GbE |Mt MC3309130 00A |Pasywne miedzi kabla 1 30awg 10 Gb/s x SFP+ 0,5 m |Technologie Mellanox |

### <a name="switches-supported-by-mellanox"></a>Parametrów obsługiwanych przez Mellanox
W poniższej tabeli wymieniono obsługiwane przez Mellanox przełączników. Te nie zostały one przetestowane przez firmę Microsoft, ale mogą działać na urządzeniu StorSimple.

| Mag. Nie. | Szybkość | Model | Opis | Wprowadź |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733 B21 |Przełącznik sieci Ethernet 10 GbE HP ProCurve 6120XG w bloku |HP |
| 2. |10 GbE |538113 B21 |Typu pass-through 10 GbE HP (PTM) |HP |
| 3. |10 GbE |EN4093 |Moduł przełączania skalowalne 10 GB/s EN4093 IBM PureFlex systemu sieci szkieletowej |IBM |
| 4. |1 GBe |3020 |Cisco Catalyst 3020 1 GBe przełącznika bloku |Cisco |
| 5. |1 GBe |3020 X |Cisco Catalyst 3020 X 1 GBe przełącznika bloku |Cisco |
| 6. |1 GBe |438030 B21 |Moduł przełączania 1 GBe HP - przełącznik bloku Ethernet warstwy 2/3 GbE2c |HP |
| 7. |1 GBe |6120G |HP ProCurve 6120G/XG 1 GBe przełącznika bloku |HP |

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o StorSimple składniki sprzętowe i stan](storsimple-monitor-hardware-status.md).

