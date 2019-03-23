---
title: Zasady wycofywania i możliwości obsługi przewodnik dotyczący systemu operacyjnego gościa platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera informacje o jakie firmy Microsoft będzie obsługiwać w odniesieniu do systemu operacyjnego gościa platformy Azure używane przez usługi w chmurze.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: ce66d44c0ddb84ed8c2908d02b8062195d6b461d
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351019"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Usługa Azure policy wycofywania i możliwości obsługi systemu operacyjnego gościa
Informacje na tej stronie dotyczą systemu operacyjnego gościa platformy Azure ([systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md)) dla role usług w chmurze sieci web i proces roboczy (PaaS). Nie ma zastosowania do maszyn wirtualnych (IaaS).

Firma Microsoft ma publikowania [zasady pomocy technicznej dla systemu operacyjnego gościa](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Strony odczytywanym teraz w tym artykule opisano sposób implementacji zasad.

Zasady

1. Firma Microsoft będzie obsługuje **co najmniej najnowsze dwóch rodzin systemów operacyjnych gościa**. Po wycofaniu rodziny klienci mają 12 miesięcy od dnia oficjalne wycofywania można zaktualizować do nowszej obsługiwanej rodziny systemów operacyjnych gościa.
2. Firma Microsoft będzie obsługuje **co najmniej dwa najnowsze wersje obsługiwanych rodzin systemów operacyjnych gościa**.
3. Firma Microsoft będzie obsługuje **co najmniej dwa najnowsze wersje zestawu Azure SDK**. W przypadku wersji zestawu SDK został wycofany, klienci będą mieli 12 miesięcy od daty oficjalne wycofywania można zaktualizować do nowszej wersji.

W czasie więcej niż dwóch rodziny i wersji może być obsługiwany. Oficjalna informacje pomocy technicznej systemu operacyjnego gościa, pojawi się na [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Po wycofaniu wersji systemu operacyjnego gościa
Nowy system operacyjny gościa **wersji** wprowadzono o co miesiąc, aby zawierały najnowsze aktualizacje MSRC. Ze względu na regularne comiesięcznych aktualizacji wersja systemu operacyjnego gościa jest wyłączona zwykle około 60 dni po jego wydaniu. To działanie powoduje co najmniej dwie wersje systemu operacyjnego gościa w ramach każdej rodziny, które są dostępne do użycia.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces podczas wycofywania do rodziny systemów operacyjnych gościa
Po wycofaniu zostanie zaanonsowana, klienci mają okresu 12-miesięcznego "przejście", zanim starsze rodzinie oficjalnie zostanie usunięte z usługi. Ten czas przejścia może zostać rozszerzona według uznania firmy Microsoft. Aktualizacje zostaną opublikowane na [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).

Proces stopniowego wycofania rozpocznie się sześć (6) miesięcy w okresie przejściowym. W tym czasie:

1. Firma Microsoft powiadomi klientów o wycofaniu.
2. Nowsza wersja zestawu SDK platformy Azure nie obsługuje wycofane rodziny systemów operacyjnych gościa.
3. Nowych wdrożeń i redeployments usług w chmurze nie mogą być wycofane rodziny

Firma Microsoft będzie wprowadzenie nowej wersji systemu operacyjnego gościa, zawierające najnowsze aktualizacje MSRC do ostatniego dnia okresu przejściowego, znane jako "Data wygaśnięcia". Na datę wygaśnięcia usług w chmurze nadal działa będą obsługiwane w ramach umowy SLA platformy Azure. Firma Microsoft ma prawo do wymuszenia uaktualnienia, usuń lub Zatrzymaj te usługi po tej dacie.

### <a name="process-during-a-guest-os-version-retirement"></a>Proces podczas wycofywania wersji systemu operacyjnego gościa
Jeśli klienci ustawić ich system operacyjny gościa do automatycznego aktualizowania, nigdy nie mają już martwić się o obsłudze wersji systemu operacyjnego gościa. Będą zawsze korzystać z najnowszej wersji systemu operacyjnego gościa.

Wersje systemów operacyjnych gościa są wydawane co miesiąc. Ze względu na częstotliwość standardowych wersji każda wersja ma stały czas.

W ciągu 60 dni do czasu działania, jest to wersja "*wyłączone*". "Wyłączone" oznacza, że wersja została usunięta z portalu. Nie można ustawić wersji z pliku konfiguracji w pliku CSCFG. Istniejące wdrożenia są pozostawiony jako uruchomiony. Ale nowych wdrożeń i kodem i konfiguracją aktualizacje istniejących wdrożeń będzie niemożliwe.

Później, po staje się "wyłączone", wersja systemu operacyjnego gościa "wygasa", a wszelkie instalacje z tego wygasłą wersją są narażone na zabezpieczenia i problemy z luk w zabezpieczeniach. Ogólnie rzecz biorąc wygaśnięcia odbywa się w partiach, więc okres inwalidztwem do wygaśnięcia może się różnić.

Klienci, którzy konfigurują swoje usługi, aby zaktualizować system operacyjny gościa ręcznie, należy upewnić się, że jego usługi są uruchomione na obsługiwanych systemów operacyjnych gościa. Jeśli usługa jest skonfigurowany do automatycznej aktualizacji systemu operacyjnego gościa, podstawowej platformy zapewnia zgodność i zostanie uaktualniona do najnowszej systemu operacyjnego gościa.

Okresy te mogą wydłużać według uznania firmy Microsoft do jej obsługi ułatwiają realizację przejścia klienta. Wszelkie zmiany zostaną przekazane na [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Powiadomienia podczas wycofywania
* **Wycofanie rodziny** <br>Firma Microsoft użyje wpisów w blogu i powiadomienia w portalu. Klienci, którzy nadal używają wycofane rodziny systemów operacyjnych gościa będzie powiadamiany przez bezpośrednią komunikację (wiadomości e-mail, wiadomości portalu, połączenie telefoniczne) administratorom przypisanego do usługi. Wszystkie zmiany zostaną opublikowane w [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).
* **Wycofanie wersji** <br>Wszystkie zmiany i daty ich wystąpienia, zostanie on opublikowany na [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md), w tym wydania, wyłączona i wygaśnięcia. Administratorzy usług będą otrzymywać wiadomości e-mail, jeśli mają one wdrożeń uruchomionych na wyłączone wersji systemu operacyjnego gościa lub rodziny. Chronometraż dla tych wiadomości e-mail mogą się różnić. Zazwyczaj są one co najmniej miesiąc przed kalectwa, że tego czasu nie ma umowy SLA oficjalnych.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Jak można zmniejszyć wpływ migracji?**

Zalecamy użycie najnowszej rodziny systemów operacyjnych gościa do projektowania usługi w chmurze.

1. Rozpocznij, planowania migracji do nowszych rodziny wcześnie.
2. Konfigurowanie wdrożenia testowe tymczasowego do testowania usługi w chmurze systemem Nowa rodzina.
3. Ustawiona wersja Twojego systemu operacyjnego gościa na **automatyczne** (osVersion = * w [.cscfg](cloud-services-model-and-package.md#cscfg) pliku) dzięki migracji do nowej wersji systemu operacyjnego gościa odbywa się automatycznie.

**Co zrobić, jeśli Moja aplikacja sieci web wymaga lepszą integrację z systemem operacyjnym?**

Jeśli architektury aplikacji sieci web jest zależny od funkcji systemu operacyjnego, użyj możliwości obsługiwanych platform takich jak [zadania uruchamiania](cloud-services-startup-tasks.md) lub innych mechanizmów rozszerzalności. Alternatywnie, można również użyć [maszyn wirtualnych platformy Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS — infrastruktura jako usługa), gdzie jesteś odpowiedzialny za prowadzenie zasadniczego systemu operacyjnego.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj najnowsze [wersji systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md).
