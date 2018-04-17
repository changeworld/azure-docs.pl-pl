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
ms.date: 04/09/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 160ba42c5cbdd3e8b999040cba8254d4c87f7c63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure stos obsługi zasad
W tym artykule opisano zasady obsługi w stosie Azure zintegrowanych systemów i co należy zrobić, aby zapewnić systemu do stanu umożliwiającego. 

## <a name="update-package-types"></a>Typy pakietów aktualizacji

Istnieją dwa typy pakietów aktualizacji dla zintegrowanych systemów: 

- **Aktualizacje oprogramowania Microsoft**. Microsoft jest odpowiedzialny za end-to-end obsługi cyklu życia dla pakietów aktualizacji oprogramowania firmy Microsoft. Te pakiety mogą zawierać najnowsze aktualizacje zabezpieczeń systemu Windows Server, aktualizacje zabezpieczeń i aktualizacje funkcji Azure stosu. Możesz pobrać pakietów aktualizacji je bezpośrednio od firmy Microsoft.

- **Aktualizacje dostarczonym sprzętu OEM**. Azure stosu dostawców sprzętu będących partnerami są odpowiedzialne za end-to-end obsługi cyklu życia (w tym wskazówki) związanych ze sprzętem oprogramowania układowego i pakietów aktualizacji sterownika. Ponadto partnerów sprzętowych stosu Azure własne i obsługa wskazówki dotyczące wszystkich sprzętu na hoście cyklu życia sprzętu i oprogramowania. Producenta OEM obsługuje te pakiety na ich własnych witryny pobierania aktualizacji.


## <a name="update-package-release-cadence"></a>Szybko wersji pakietu aktualizacji
Microsoft oczekuje wersji pakietów aktualizacji oprogramowania w okresach miesięcznych. Jednak jest możliwe wersje aktualizacji wielokrotny lub nie w ciągu miesiąca. Dostawcy sprzętu OEM ich aktualizacje na zgodnie z potrzebami. 

Znajdź dokumentacji na temat planowania aktualizacji i zarządzanie nimi i sposobu określania bieżącej wersji w [Zarządzaj aktualizuje omówienie](azure-stack-updates.md). Aby uzyskać informacje dotyczące określonej aktualizacji łącznie ze sposobem go pobrać, zobacz informacje o wersji dla tej aktualizacji: 
- [Azure aktualizacji 1803 stosu](azure-stack-update-1803.md)
- [Azure aktualizacji 1802 stosu](azure-stack-update-1802.md)
- [Azure aktualizacji 1712 stosu](azure-stack-update-1712.md)



## <a name="hotfixes"></a>Poprawki
Od czasu do czasu, firma Microsoft udostępnia poprawki stosu Azure tego adresu konkretny problem, który jest często zapobiegawcze lub określonym terminie ważności.  Każda poprawka jest publikowana z odpowiedni artykuł bazy wiedzy Microsoft Knowledge Base, ze szczegółami, problem, przyczynę i rozwiązanie. 

Poprawki są pobrać i zainstalować podobnie jak pakiety regularne pełnej aktualizacji na stos Azure. Jednak w przeciwieństwie do pełnej aktualizacji poprawek można zainstalować w minutach. Firma Microsoft zaleca zestawu Azure operatory stos obsługi systemu windows podczas instalowania poprawki. Poprawki zaktualizuj wersję chmury Azure stosu, aby ułatwić ustalenie, czy zostały zastosowane poprawki. Dostępna jest poprawka osobne dla każdej wersji Azure stosu, który jest nadal w obsłudze. Każdy poprawkę dotyczącą określonego iteracji kumulują się i zawiera poprzednie aktualizacje dla tej samej wersji. Możesz przeczytać więcej na temat stosowania poprawki w poprawki, odpowiadająca wiedzy artykułu.  


## <a name="keep-your-system-under-support"></a>Aktualizowanie systemu jako pomoc techniczna
Aby kontynuować uzyskać pomoc techniczną, należy pozostawić wdrożenia stosu Azure bieżącej. Zasady odroczenia aktualizacji: dla danego wdrożenia stosu Azure ma pozostać w obsłudze musi niedawno wydany zaktualizowanej wersji, lub uruchom dowolne z poprzednich dwóch wersji aktualizacji. Poprawki nie są traktowane jako wersje główne aktualizacji. Jeśli chmury Azure stos jest za przez *więcej niż dwóch aktualizacji*, jest uznawane za niezgodne, a należy zaktualizować co najmniej minimalna obsługiwana wersja uzyskanie pomocy technicznej. 

Na przykład, jeśli niedawno dostępny zaktualizowanej wersji, która jest 1805, i poprzednich dwóch pakietów aktualizacji w wersji 1804 i 1803, zarówno 1803 i 1804 pozostają w pomocy technicznej. Jednak 1802 jest obsługiwany. Zasady jest spełniony, gdy istnieje bez zwalniania miesiąca lub dwóch. Na przykład jeśli bieżąca wersja to 1805 i nie było żadnych wersji 1804, poprzednie dwa pakiety aktualizacji 1803 i 1802 pozostają w pomocy technicznej.

Pakiety aktualizacji oprogramowania firmy Microsoft są skumulowanych i wymagają poprzedniego pakietu aktualizacji jako warunek wstępny. Jeśli zdecydujesz się odroczenie co najmniej jednej aktualizacji, należy wziąć pod uwagę ogólną środowiska uruchomieniowego Jeśli chcesz uzyskać dostęp do najnowszej wersji. 


## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie aktualizacjami w stosie Azure](azure-stack-updates.md)


