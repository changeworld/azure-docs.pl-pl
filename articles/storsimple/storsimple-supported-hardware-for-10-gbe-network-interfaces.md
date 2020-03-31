---
title: Sprzęt dla interfejsów StorSimple 10 GbE | Dokumenty firmy Microsoft
description: Zawiera opis obsługiwanych małych urządzeń nadawczo-odbiorczych typu pluggable (SFP), kabli i przełączników dla interfejsów sieciowych 10 GbE w urządzeniu StorSimple.
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
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965014"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Ten artykuł zawiera informacje o dodatkowym sprzęcie, który współpracuje z urządzeniem Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista urządzeń testowanych przez firmę Microsoft
Firma Microsoft przetestowała następujące urządzenia nadawczo-odbiorcze, kable i przełączniki o małych rozmiarach (SFP), aby zapewnić optymalne działanie urządzeń. (Poniższe tabele zostaną zaktualizowane w miarę testowania nowego sprzętu).

### <a name="sfp-transceivers"></a>Urządzenia nadawczo-odbiorcze SFP+
| Marka | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kable
| Mag. Nie. | Marka | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Okręg wyborczy Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Przełączniki
| Mag. Nie. | Marka | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista urządzeń testowanych w terenie
Ta sekcja zawiera listę urządzeń, które zostały pomyślnie wdrożone w tym polu przez klientów StorSimple. Te nie zostały przetestowane przez firmę Microsoft, ale mogą współpracować z urządzeniem StorSimple.

| Parametr | Wartość |
| --- | --- |
| Przełącznik make |Juniper |
| Model przełącznika |ex4550-32F |
| Przełączanie wersji systemu operacyjnego |Junos 12.3R9.4 |
| Model lemiesza |Porty na pokładzie (PIC 0) |
| Urządzenie nadawczo-odbiorcze |Juniper |
| Model nadawczo-odbiorczy |Numer części 740-021308 <br></br> Numer części 740-030658 |
| Wersja oprogramowania układowego Transceiver |Rev 01 Wersja 0.0 (zgłoszone) |
| Model kabla |Zworka dupleksowa LC/LC 50/125μ, OM3, LSZH |
| Model StorSimple |8600 |
| Wersja oprogramowania StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista urządzeń testowanych przez dostawcę OEM (Mellanox)
Firma Mellanox przetestowała następujące urządzenia nadawczo-odbiorcze, kable i przełączniki o małej obudowie (SFP), aby zapewnić optymalne działanie interfejsów sieciowych Mellanox, takich jak interfejsy sieciowe 10 GbE w urządzeniu StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kable i moduły obsługiwane przez Firmę Mellanox
W poniższej tabeli wymieniono kable i moduły obsługiwane przez firmę Mellanox. Te nie zostały przetestowane przez firmę Microsoft, ale mogą współpracować z urządzeniem StorSimple.

| Mag. Nie. | Szybkość | Model | Opis | Marka |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |pasywny kabel miedziany SFP+ 10 Gb/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |pasywny kabel miedziany SFP+ 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |pasywny kabel miedziany SFP+ 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |pasywny kabel miedziany SFP+ 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Kabel Cisco SFP+ |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Kabel Cisco SFP+ |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Kabel Cisco SFP+ |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP+ do przewód miedziany SFP+ 1m Direct Attach |Hp |
| 9. |10 GbE |455883-B21 KM BLc |10Gb SR SFP + Opt |Hp |
| 10. |10 GbE |455886-B21 KM BLc |10Gb LR SFP + Opt |Hp |
| 11. |10 GbE |487649-B21 KM BLc |Kabel miedziany SFP+ 0,5 m 10 GbE |Hp |
| 12. |10 GbE |487652-B21 KM BLc |Kabel miedziany SFP+ 1m 10GbE |Hp |
| 13. |10 GbE |487655-B21 KM BLc |Kabel miedziany SFP+ 3m 10GbE |Hp |
| 14. |10 GbE |487658-B21 KM BLc |Kabel miedziany SFP+ 7m 10GbE |Hp |
| 15. |10 GbE |537963-B21 KM BLc |Kabel miedziany SFP+ 5m 10GbE |Hp |
| 16. |10 GbE |AP784A HP |3m Pasywny miedziany kabel SFP+ |Hp |
| 17. |10 GbE |AP785A HP |5m Pasywny miedziany kabel SFP+ serii C |Hp |
| 18. |10 GbE |AP818A HP |Kabel active sfp+ z serii B 1m |Hp |
| 19. |10 GbE |AP819A HP |Kabel 3m Z serii B Active Copper SFP+ |Hp |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ LC SR Transceiver |Hp |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR Transceiver |Hp |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ 3m Kabel DAC |Hp |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ 7m Kabel DAC |Hp |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+ 0,65 m kabel DAC |Hp |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ 1,2 m Kabel DAC |Hp |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP + 3m KABEL DAD |Hp |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |Karta QSFP do SFP+ |Technologie Mellanox |
| 28. |10 GbE |MC2309124-006 Mt |Pasywny kabel miedziany 1x SFP+ do QSFP 10 Gb/s 24awg 7m |Technologie Mellanox |
| 29. |10 GbE |MC2309124-007 Mt |Pasywny kabel miedziany 1x SFP+ do QSFP 10 Gb/s 24awg 7m |Technologie Mellanox |
| 30. |10 GbE |MC2309130-003 Mt |Pasywny kabel miedziany 1x SFP+ do QSFP 10 Gb/s 30awg 3m |Technologie Mellanox |
| 31. |10 GbE |MC2309130-00A Mt |Pasywny kabel miedziany 1x SFP+ do QSFP 10 Gb/s 30awg 0,5 m |Technologie Mellanox |
| 32. |10 GbE |MC3309124-005 Mt |Pasywny kabel miedziany 1x SFP+ 10Gb/s 24awg 5m |Technologie Mellanox |
| 33. |10 GbE |MC3309124-007 Mt |Pasywny kabel miedziany 1x SFP+ 10Gb/s 24awg 7m |Technologie Mellanox |
| 34. |10 GbE |MC3309130-003 Mt |Pasywny kabel miedziany 1x SFP+ 10Gb/s 30awg 3m |Technologie Mellanox |
| 35. |10 GbE |MC3309130-00A Mt |Pasywny kabel miedziany 1x SFP+ 10 Gb/s 30awg 0,5 m |Technologie Mellanox |

### <a name="switches-supported-by-mellanox"></a>Przełączniki obsługiwane przez Mellanox
W poniższej tabeli wymieniono przełączniki obsługiwane przez firmę Mellanox. Te nie zostały przetestowane przez firmę Microsoft, ale mogą współpracować z urządzeniem StorSimple.

| Mag. Nie. | Szybkość | Model | Opis | Marka |
| --- | --- | --- | --- | --- |
| 1. |10gbe |516733-B21 |Przełącznik kasetowy HP ProCurve 6120XG 10GbE Ethernet |Hp |
| 2. |10gbe |538113-B21 |Moduł przelotowy HP 10GbE (PTM) |Hp |
| 3. |10gbe |En4093 |IBM PureFlex System Fabric EN4093 10-gigabitowy moduł przełącznika skalowalnego |IBM |
| 4. |1gbe |3020 |Tarcza przełącznika Cisco Catalyst 3020 1GbE |Cisco |
| 5. |1gbe |3020X |Tarcza przełącznika Cisco Catalyst 3020X 1GbE |Cisco |
| 6. |1gbe |438030-B21 |Moduł przełącznika HP 1GbE - przełącznik kasetowy GbE2c Layer 2/3 Ethernet |Hp |
| 7. |1gbe |6120g |Ostrze przełącznika HP ProCurve 6120G/XG 1GbE |Hp |

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o składnikach sprzętowych StorSimple i stanie](storsimple-monitor-hardware-status.md).

