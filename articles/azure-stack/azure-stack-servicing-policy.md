---
title: Azure stos obsługi zasad | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obsługi zasad i sposobu na utrzymanie zintegrowany system do stanu umożliwiającego stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure stos obsługi zasad
W tym artykule opisano zasady obsługi w stosie Azure zintegrowanych systemów i co należy zrobić, aby zapewnić systemu do stanu umożliwiającego. 

## <a name="update-package-types"></a>Typy pakietów aktualizacji

Istnieją dwa typy pakietów aktualizacji dla zintegrowanych systemów. Aktualizacje oprogramowania i aktualizacje, które są specyficzne dla dostawcy sprzętu producenta sprzętu (OEM), takie jak sterowniki i oprogramowanie układowe. Te aktualizacje są dostarczane jako osobne pakiety aktualizacji stosu Azure i są zarządzane niezależnie.

- **Aktualizacje oprogramowania Microsoft**. Microsoft jest odpowiedzialny za end-to-end obsługi cyklu życia dla pakietów aktualizacji oprogramowania firmy Microsoft. Te pakiety mogą zawierać najnowsze aktualizacje zabezpieczeń systemu Windows Server, aktualizacje zabezpieczeń i aktualizacje funkcji Azure stosu. Możesz pobrać pakietów aktualizacji je bezpośrednio od firmy Microsoft.
- **Aktualizacje dostarczonym sprzętu OEM**. Azure stosu dostawców sprzętu będących partnerami są odpowiedzialne za end-to-end obsługi cyklu życia (w tym wskazówki) związanych ze sprzętem oprogramowania układowego i pakietów aktualizacji sterownika. Ponadto partnerów sprzętowych stosu Azure własne i obsługa wskazówki dotyczące wszystkich sprzętu na hoście cyklu życia sprzętu i oprogramowania. Producenta OEM obsługuje te pakiety na ich własnych witryny pobierania aktualizacji.

## <a name="update-package-release-cadence"></a>Szybko wersji pakietu aktualizacji

Microsoft oczekuje wersji pakietów aktualizacji oprogramowania w okresach miesięcznych. Jednak jest możliwe wersje aktualizacji wielokrotny lub nie w ciągu miesiąca. Dostawcy sprzętu OEM ich aktualizacje na zgodnie z potrzebami.

Pakiet aktualizacji firmy Microsoft ma następującą konwencją nazewnictwa pomaga łatwo zidentyfikować Data wydania:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Na przykład aktualizację oprogramowania firmy Microsoft, wydanej w dniu 15 czerwca 2017 musi wersji "1.0.170615.1".

## <a name="keep-your-system-under-support"></a>Aktualizowanie systemu jako pomoc techniczna
Aby kontynuować uzyskać pomoc techniczną, należy pozostawić wdrożenia stosu Azure bieżącej. Zasady dla odroczenia aktualizacji nie stosu Azure ma pozostać w obsłudze, jego musi niedawno wydany zaktualizowanej wersji, lub uruchom dowolne z poprzednich dwóch wersji dużej aktualizacji.  Poprawki nie są traktowane jako wersje główne aktualizacji.  Jeśli chmury Azure stos jest za przez *więcej niż dwóch aktualizacji*, jest uznawane za niezgodne, a należy zaktualizować co najmniej minimalna obsługiwana wersja uzyskanie pomocy technicznej. 

Na przykład, jeśli niedawno dostępny zaktualizowanej wersji, która jest 1805, i poprzednich dwóch pakietów aktualizacji w wersji 1804 i 1803, zarówno 1803 i 1804 pozostają w pomocy technicznej. Jednak 1802 jest obsługiwany. Zasady jest spełniony, gdy istnieje bez zwalniania miesiąca lub dwóch. Na przykład jeśli bieżąca wersja to 1805 i nie było żadnych wersji 1804, poprzednie dwa pakiety aktualizacji 1803 i 1802 pozostanie w obsłudze.

Pakiety aktualizacji oprogramowania firmy Microsoft są skumulowanych i wymagają poprzedniego pakietu aktualizacji jako warunek wstępny. Jeśli zdecydujesz się odroczenie co najmniej jednej aktualizacji, należy wziąć pod uwagę ogólną środowiska uruchomieniowego Jeśli chcesz uzyskać dostęp do najnowszej wersji. 

W poniższej tabeli przedstawiono przykład aktualizacji pakietu wersji, ich wymagań wstępnych i minimalna obsługiwana wersja systemu musi wynosić do obsługi pomocy technicznej. Tabela jest oparta na początkowa wersja stosu Azure zintegrowanych systemów (kompilacja 1708), z pierwszej aktualizacji wersji pakietu (1709), w września 2017 r. 

| Najnowszy pakiet aktualizacji (*przykład*) | Wymagania wstępne | Minimalna obsługiwana wersja |
| -- | -- | -- |
| 1710 | 1709 | ND |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
W powyższej tabeli nie ma żadnych wersji 1801.

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie aktualizacjami w stosie Azure](azure-stack-updates.md)


