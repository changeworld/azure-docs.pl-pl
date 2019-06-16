---
title: Interfejsy sprzętu dla usługi StorSimple 10 GbE | Dokumentacja firmy Microsoft
description: W tym artykule opisano obsługiwane małych współczynnika postaci podłączanych (SFP) urządzeń nadawczo-odbiorczych, kabli i przełączników dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple.
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
ms.openlocfilehash: 8303195f0f3228ee145cbba9e322ea4e5e4c1264
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726963"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Obsługiwany sprzęt dla interfejsów sieciowych 10 GbE na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
Ten artykuł zawiera informacje na temat dodatkowego sprzętu, który działa urządzeń z systemem Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista urządzeń przetestowane przez firmę Microsoft
Firma Microsoft przetestowała następujące małych współczynnika postaci podłączanych () nadawczo-odbiorczych SFP, kabli i przełączników, aby upewnić się, czy działają optymalnie z urządzeniami. (Następujące tabele zostaną zaktualizowane, jak nowy sprzęt jest testowana.)

### <a name="sfp-transceivers"></a>Odbiorniki SFP +
| Tworzenie | Modelowanie |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kable
| Mag. Nie. | Tworzenie | Modelowanie |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp Lite |N820 - 05M (OM3) |

### <a name="switches"></a>Przełączniki
| Mag. Nie. | Tworzenie | Modelowanie |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lista urządzeń przetestowane w polu
Ta sekcja zawiera listę urządzeń, które zostały pomyślnie wdrożone w polu przez klientów usługi StorSimple. To nie zostały przetestowane przez firmę Microsoft, ale mogą działać na urządzeniu StorSimple.

| Parametr | Wartość |
| --- | --- |
| Przełącz na marki |Juniper |
| Model przełącznika |ex4550-32F |
| Wersja systemu operacyjnego przełącznika |JunOS 12.3R9.4 |
| Model bloku |Dołączanie portów (PIC 0) |
| Marka odbiorcze |Juniper |
| Model odbiorcze |Numer części 740 021308 <br></br> Numer części 740 030658 |
| Wersja oprogramowania układowego odbiorcze |Rev 01 wersji 0.0 (zgłaszana) |
| Kabel modelu |Dwukierunkowe jumper LC/LC 50/125µ, OM3, LSZH |
| Model usługi StorSimple |8600 |
| Wersja oprogramowania StorSimple |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista urządzeń przetestowane przez producenta OEM, dostawcy (Mellanox)
Mellanox przetestowała następujące małych współczynnika postaci podłączanych () nadawczo-odbiorczych SFP, kabli i przełączników, aby upewnić się, że działają optymalnie Mellanox interfejsów sieciowych, takich jak interfejsów sieciowych 10 GbE na urządzeniu StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kable i obsługiwane przez Mellanox modułów
W poniższej tabeli wymieniono kabli i obsługiwane przez Mellanox modułów. To nie zostały przetestowane przez firmę Microsoft, ale mogą działać na urządzeniu StorSimple.

| Mag. Nie. | Szybkość | Modelowanie | Opis | Tworzenie |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |pasywnym kabla miedzianego SFP + 10 Gb/s 1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |pasywnym kabla miedzianego SFP + 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |pasywnym kabla miedzianego SFP + 10 Gb/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |pasywnym kabla miedzianego SFP + 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP +, aby SFP + 1 mln dołączane bezpośrednio kabla miedzianego |HP |
| 9. |10 GbE |BLc HP 455883 B21 |10Gb SR SFP + zoptymalizowany pod kątem |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP+ Opt |HP |
| 11. |10 GbE |BLc HP 487649 B21 |SFP + 10 GbE 0,5 m miedzi kabel |HP |
| 12. |10 GbE |BLc HP 487652 B21 |10 GbE SFP + 1 mln miedzi kabel |HP |
| 13. |10 GbE |BLc HP 487655 B21 |SFP + 10 GbE 3m miedzi kabel |HP |
| 14. |10 GbE |BLc HP 487658 B21 |10 GbE SFP + 7m miedzi kabel |HP |
| 15. |10 GbE |537963-B21 HP BLc |10 GbE SFP + 5 min miedzi kabel |HP |
| 16. |10 GbE |AP784A HP |Kabel z serii C pasywnym miedzianego SFP + 3m |HP |
| 17. |10 GbE |AP785A HP |5 mln serii C pasywnym miedzianego SFP + kabel |HP |
| 18. |10 GbE |AP818A HP |Seria B 1 mln aktywnych miedzianego SFP + kabel |HP |
| 19. |10 GbE |AP819A HP |Seria B Active miedzianego SFP + kabel 3m |HP |
| 20. |10 GbE |J9150A HP |X132 10 G SFP + LC SR odbiorcze |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR Transceiver |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ 3m DAC Cable |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ 7m DAC Cable |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+ 0.65m DAC Cable |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ 1.2m DAC Cable |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP+ 3m DAD Cable |HP |
| 27. |10 GbE |Mellanox MAM1Q00A QSA |QSFP SFP + karty |Mellanox technologii |
| 28. |10 GbE |MC2309124-006 Mt |X SFP+ pasywnym kabla miedzianego 1 do 24awg 10 Gb/s QSFP 7 m |Mellanox technologii |
| 29. |10 GbE |Mt MC2309124 007 |X SFP+ pasywnym kabla miedzianego 1 do 24awg 10 Gb/s QSFP 7 m |Mellanox technologii |
| 30. |10 GbE |MC2309130-003 Mt |X SFP+ pasywnym kabla miedzianego 1 do 30awg 10 Gb/s QSFP 3 m |Mellanox technologii |
| 31. |10 GbE |MC2309130-00A Mt |X SFP+ pasywnym kabla miedzianego 1 do 30awg 10 Gb/s QSFP 0,5 m |Mellanox technologii |
| 32. |10 GbE |Mt MC3309124 005 |Pasywnym Coopera okablowanie 1 24awg 10 Gb/s x SFP+ 5 m |Mellanox technologii |
| 33. |10 GbE |Mt MC3309124 007 |Pasywnym Coopera okablowanie 1 24awg 10 Gb/s x SFP+ 7 m |Mellanox technologii |
| 34. |10 GbE |MC3309130-003 Mt |Pasywnym Coopera okablowanie 1 30awg 10 Gb/s x SFP+ 3 m |Mellanox technologii |
| 35. |10 GbE |MC3309130-00A Mt |Pasywnym Coopera okablowanie 1 30awg 10 Gb/s x SFP+ 0,5 m |Mellanox technologii |

### <a name="switches-supported-by-mellanox"></a>Parametrów obsługiwanych przez Mellanox
Poniższa tabela zawiera listę parametrów obsługiwanych przez Mellanox. To nie zostały przetestowane przez firmę Microsoft, ale mogą działać na urządzeniu StorSimple.

| Mag. Nie. | Szybkość | Modelowanie | Opis | Tworzenie |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet Blade Switch |HP |
| 2. |10GbE |538113-B21 |Typu pass-through 10 GbE HP (PTM) |HP |
| 3. |10GbE |EN4093 |Moduł przełączania skalowalne 10 GB EN4093 w sieci szkieletowej systemu IBM PureFlex |IBM |
| 4. |1GbE |3020 |Cisco Catalyst 3020 1GbE przełącznika bloku |Cisco |
| 5. |1GbE |3020X |Cisco Catalyst 3020 X 1GbE przełącznika bloku |Cisco |
| 6. |1GbE |438030-B21 |Moduł przełączania 1GbE HP - przełącznik bloku Ethernet warstwy 2/3 GbE2c |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G/XG 1GbE przełącznika bloku |HP |

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat składników sprzętowych StorSimple i stan](storsimple-monitor-hardware-status.md).

