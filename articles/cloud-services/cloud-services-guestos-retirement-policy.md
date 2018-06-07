---
title: Możliwość obsługi i zasadami wycofania przewodnik dla systemu operacyjnego gościa Azure | Dokumentacja firmy Microsoft
description: Zawiera informacje o co będzie pomocy technicznej firmy Microsoft w odniesieniu do systemu operacyjnego gościa Azure używany przez usługi w chmurze.
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
ms.openlocfilehash: dfa3bac95b9827789950b4931e3198237de4a1fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608566"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure zasad obsługi i wycofania systemu operacyjnego gościa
Informacje na tej stronie odnosi się do systemu operacyjnego gościa Azure ([systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md)) dla usługi w chmurze sieci web i proces roboczy ról (PaaS). Nie ma zastosowania do maszyn wirtualnych (IaaS).

Firma Microsoft ma opublikowanej [zasady pomocy technicznej dla systemu operacyjnego gościa](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Strona, do której są odczytywanie opisuje teraz implementowania zasad.

Zasady

1. Microsoft będzie obsługiwać **co najmniej dwóch najnowsze rodzin systemu operacyjnego gościa**. Po wycofaniu rodzina klienci mają 12 miesięcy od daty oficjalnego wycofywania można zaktualizować do nowszej obsługiwanej rodziny systemu operacyjnego gościa.
2. Firma Microsoft będzie obsługuje **co najmniej dwóch najnowszej wersji obsługiwanych rodzin systemu operacyjnego gościa**.
3. Firma Microsoft będzie obsługuje **co najmniej dwóch najnowszej wersji zestawu Azure SDK**. Gdy wersja zestawu SDK jest wycofywany, klienci będą mieli 12 miesięcy od daty oficjalnego wycofywania można zaktualizować do nowszej wersji.

W czasie więcej niż dwie grupy lub wersji może być obsługiwana. Informacje pomocy technicznej systemu operacyjnego gościa oficjalnego pojawią się na [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Po wycofaniu wersji systemu operacyjnego gościa
Nowy system operacyjny gościa **wersji** wprowadzono o co miesiąc, w celu uwzględnienia najnowszych aktualizacji MSRC. Z powodu regularnych comiesięcznych aktualizacji wersja systemu operacyjnego gościa zwykle jest wyłączona około 60 dni po jego wersji. To działanie umożliwia zachowanie co najmniej dwie wersje systemu operacyjnego gościa, dla każdej rodziny, które są dostępne do użycia.

### <a name="process-during-a-guest-os-family-retirement"></a>Podczas wycofywania rodziny systemu operacyjnego gościa
Po ogłoszenia wycofania klienci mają okres 12 miesięcy "przejścia", zanim starsze rodziny oficjalnie zostaną usunięte z usługi. Ten czas przejścia może zostać rozszerzony według uznania firmy Microsoft. Aktualizacje zostaną opublikowane na [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).

Proces stopniowego wycofania rozpocznie sześciu (6) miesięcy do okresu przejściowego. W tym czasie:

1. Firma Microsoft będzie wysyłać klientów wycofania.
2. Nowsza wersja zestawu SDK platformy Azure nie obsługuje wycofane rodziny systemów operacyjnych gościa.
3. Nowych wdrożeń i redeployments usługi w chmurze nie będą mogły wycofane rodziny

Firma Microsoft będzie wprowadzenie nowej wersji systemu operacyjnego gościa, implementując najnowsze aktualizacje MSRC do ostatniego dnia okres przejściowy, nazywany "Data wygaśnięcia". Na datę wygaśnięcia będą obsługiwane usługi w chmurze nadal działa w ramach umowy SLA platformy Azure. Firma Microsoft ma prawo wymusić uaktualnienia, usuń lub Zatrzymaj usługi po tym dniu.

### <a name="process-during-a-guest-os-version-retirement"></a>Podczas wycofywania wersji systemu operacyjnego gościa
Jeśli klienci ich systemu operacyjnego gościa, można automatycznie zaktualizować, ich nie musisz się martwić o obsłudze wersji systemu operacyjnego gościa. Mogą zawsze używać najnowszej wersji systemu operacyjnego gościa.

Wersje systemu operacyjnego gościa są wydawane co miesiąc. Ze względu na częstotliwość regularne wersji każdej wersji ma stały czas działania.

W ciągu 60 dni do czasu działania jest wersja "*wyłączone*". "Wyłączone" oznacza, że wersja jest usunięte z portalu. Nie można ustawić wersję z pliku konfiguracji CSCFG. Istniejące wdrożenia pozostają uruchomione. Jednak nowych wdrożeń i kodem i konfiguracją aktualizacje do istniejących wdrożeń będzie niemożliwe.

Pewien czas po przekształceniu "wyłączone", wersja systemu operacyjnego gościa "*wygasa*" oraz wszystkie instalacje nadal działa w tej wersji są życie uaktualnione, a opcja automatycznej aktualizacji systemu operacyjnego gościa w przyszłości. Wygaśnięcia odbywa się w partiach, więc czas inwalidztwem do wygaśnięcia może się różnić.

Okresy te mogą wydłużać według uznania firmy Microsoft, aby ułatwić przejścia klienta. Wszelkie zmiany zostaną przekazane na [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Powiadomienia podczas wycofywania
* **Wycofanie rodziny** <br>Firma Microsoft będzie używać blogach i powiadomieniu portalu. Klienci, którzy nadal używają wycofane rodziny systemu operacyjnego gościa zostanie powiadomiony przez bezpośrednią komunikację (poczty e-mail, wiadomości portalu, połączenie telefoniczne) do administratorów przypisanego do usługi. Wszystkie zmiany zostaną opublikowane w [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).
* **Wycofywania wersji** <br>Wszystkie zmiany i dat występowania zostaną opublikowane w [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md), w tym wersja, wyłączona i wygaśnięcia. Administratorzy usług będziesz otrzymywać wiadomości e-mail, jeśli mają wdrożeń uruchomionych na wyłączone wersji systemu operacyjnego gościa lub rodziny. Terminy te wiadomości e-mail mogą się różnić. Zazwyczaj są co najmniej miesiąc przed inwalidztwo, chociaż ten czas nie jest oficjalną umowy SLA.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Jak można ograniczyć wpływ migracji?**

Zalecane jest użycie najnowszej rodziny systemów operacyjnych gościa za projektowanie usługi w chmurze.

1. Uruchom planowania migracji do nowszej rodziny wcześniej.
2. Konfigurowanie wdrożenia testowe tymczasowego do testowania usługi w chmurze systemem nowej rodziny.
3. Wartość używanej wersji systemu operacyjnego gościa **automatyczne** (osVersion = * w [.cscfg](cloud-services-model-and-package.md#cscfg) pliku) dzięki migracji do nowej wersji systemu operacyjnego gościa odbywa się automatycznie.

**Co zrobić, jeśli Moja aplikacja sieci web wymaga lepsza integracja z systemem operacyjnym?**

Jeśli architektury aplikacji sieci web jest zależny od funkcji systemu operacyjnego, użyj platformy obsługiwane funkcje takie jak [uruchamiania zadań](cloud-services-startup-tasks.md) lub innych mechanizmów rozszerzalności. Alternatywnie można również użyć [maszyny wirtualne Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS — infrastruktura jako usługa), gdzie jest odpowiedzialny za konserwację system operacyjny.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj najnowszej [wersje systemu operacyjnego gościa](cloud-services-guestos-update-matrix.md).
