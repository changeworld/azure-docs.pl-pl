---
title: Przewodnik po zasadach obsługi i wycofywania dla systemu operacyjnego azure dla gości | Dokumenty firmy Microsoft
description: Zawiera informacje o tym, co firma Microsoft będzie obsługiwać w odniesieniu do systemu operacyjnego azure gości używanych przez usługi w chmurze.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68945448"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Zasady obsługi i emerytury usługi Azure Guest OS
Informacje na tej stronie odnoszą się do systemu operacyjnego Azure Guest[(Guest OS)](cloud-services-guestos-update-matrix.md)dla pracowników usług w chmurze i ról sieci web (PaaS). Nie dotyczy maszyn wirtualnych (IaaS).

Firma Microsoft ma opublikowane [zasady pomocy technicznej dla systemu operacyjnego gościa](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Czytana strona opisuje teraz sposób implementacji zasad.

Polityka jest

1. Firma Microsoft będzie **obsługiwać co najmniej dwie ostatnie rodziny systemu operacyjnego gościa.** Gdy rodzina jest na emeryturze, klienci mają 12 miesięcy od oficjalnej daty przejścia na emeryturę, aby zaktualizować do nowszej obsługiwanej rodziny systemu operacyjnego gościa.
2. Firma Microsoft będzie **obsługiwać co najmniej dwie najnowsze wersje obsługiwanych rodzin systemu operacyjnego gościa.**
3. Firma Microsoft będzie **obsługiwać co najmniej dwie najnowsze wersje zestawu Azure SDK.** Po wycofaniu wersji pakietu SDK klienci będą mieli 12 miesięcy od oficjalnej daty przejścia na emeryturę na aktualizację do nowszej wersji.

Czasami może być obsługiwana więcej niż dwie rodziny lub wydania. Oficjalne informacje o pomocy technicznej dla gości pojawią się w [wersjach systemu operacyjnego azure guest i macierzy zgodności SDK.](cloud-services-guestos-update-matrix.md)

## <a name="when-a-guest-os-version-is-retired"></a>Po wycofaniu wersji systemu operacyjnego gościa
Nowe wersje systemu **operacyjnego** gościa są wprowadzane co miesiąc, aby włączyć najnowsze aktualizacje MSRC. Ze względu na regularne miesięczne aktualizacje wersja systemu operacyjnego gościa jest zwykle wyłączona około 60 dni po jej wydaniu. To działanie przechowuje co najmniej dwie wersje systemu operacyjnego gościa dla każdej rodziny dostępne do użycia.

### <a name="process-during-a-guest-os-family-retirement"></a>Proces podczas emerytury rodzinnej systemu operacyjnego gościa
Po ogłoszeniu emerytury klienci mają 12-miesięczny okres "przejściowy", zanim starsza rodzina zostanie oficjalnie usunięta z usługi. Ten czas przejścia może zostać przedłużony według uznania firmy Microsoft. Aktualizacje zostaną opublikowane w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).

Stopniowy proces przechodzenia na emeryturę rozpocznie się sześć (6) miesięcy w okresie przejściowym. W tym czasie:

1. Firma Microsoft powiadomi klientów o przejściu na emeryturę.
2. Nowsza wersja zestawu SDK platformy Azure nie będzie obsługiwać wycofanej rodziny systemu operacyjnego gościa.
3. Nowe wdrożenia i ponowne wdrożenie usług w chmurze nie będą dozwolone w rodzinie emerytów

Firma Microsoft będzie nadal wprowadzać nową wersję systemu operacyjnego gościa zawierającą najnowsze aktualizacje MSRC do ostatniego dnia okresu przejściowego, znanego jako "data wygaśnięcia". W dniu wygaśnięcia usługi w chmurze nadal uruchomione będą nieobsługiwały w ramach umowy SLA platformy Azure. Firma Microsoft ma prawo wymuszać uaktualnianie, usuwanie lub zatrzymywać te usługi po tej dacie.

### <a name="process-during-a-guest-os-version-retirement"></a>Proces podczas wycofywania wersji systemu operacyjnego gościa
Jeśli klienci ustawią system operacyjny gościa do automatycznej aktualizacji, nigdy nie muszą się martwić o radzenie sobie z wersjami systemu operacyjnego gościa. Zawsze będą używać najnowszej wersji systemu operacyjnego gościa.

Wersje systemu operacyjnego gościa są wydawane co miesiąc. Ze względu na szybkość regularnych wydań każda wersja ma stałą żywotność.

Po 60 dniach w ciągu życia wersja jest "*wyłączona*". "Wyłączone" oznacza, że wersja jest usuwana z portalu. Wersji nie można już ustawić z pliku konfiguracyjnego CSCFG. Istniejące wdrożenia pozostają uruchomione. Jednak nowe wdrożenia oraz aktualizacje kodu i konfiguracji istniejących wdrożeń nie będą dozwolone.

Jakiś czas po tym, jak stała się "wyłączona", wersja systemu operacyjnego gościa "wygasa", a wszystkie instalacje nadal uruchomione, że wygasła wersja są narażone na problemy z zabezpieczeniami i lukami w zabezpieczeniach. Ogólnie rzecz biorąc, wygaśnięcie odbywa się w partiach, więc okres od wyłączenia do wygaśnięcia może się różnić.

Klienci, którzy skonfigurują swoje usługi do ręcznego aktualizowania systemu operacyjnego gościa, powinni upewnić się, że ich usługi są uruchomione w obsługiwanym systemu operacyjnego gościa. Jeśli usługa jest skonfigurowana do automatycznego aktualizowania systemu operacyjnego gościa, podstawowa platforma zapewni zgodność i zostanie uaktualniona do najnowszego systemu operacyjnego gościa.

Okresy te mogą być dłuższe według uznania firmy Microsoft, aby ułatwić przejście klientów. Wszelkie zmiany będą przekazywane w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności SDK.](cloud-services-guestos-update-matrix.md)

### <a name="notifications-during-retirement"></a>Powiadomienia podczas przejścia na emeryturę
* **Emerytura rodzinna** <br>Firma Microsoft będzie używać wpisów w blogu i powiadomień portalu. Klienci, którzy nadal korzystają z wycofajej rodziny systemów operacyjnych gości, zostaną powiadomieni za pośrednictwem bezpośredniej komunikacji (wiadomości e-mail, wiadomości portalu, połączenia telefoniczne) przypisanym administratorom usług. Wszystkie zmiany zostaną opublikowane w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności SDK](cloud-services-guestos-update-matrix.md).
* **Wersja emerytury** <br>Wszystkie zmiany i daty, które wystąpią zostaną opublikowane w [wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności zestawów SDK](cloud-services-guestos-update-matrix.md), w tym wersji, wyłączone i wygaśnięcia. Administratorzy usług otrzymają wiadomości e-mail, jeśli mają wdrożenia uruchomione w wyłączonej wersji systemu operacyjnego gościa lub w rodzinie. Czas tych wiadomości e-mail może się różnić. Zazwyczaj są one co najmniej miesiąc przed inwalidztwem, choć ten czas nie jest oficjalnym SLA.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Jak złagodzić skutki migracji?**

Do projektowania usług w chmurze zaleca się używanie najnowszej rodziny systemu operacyjnego gościa.

1. Zacznij wcześnie planować migrację do nowszej rodziny.
2. Skonfiguruj tymczasowe wdrożenia testowe, aby przetestować usługę w chmurze działającą w nowej rodzinie.
3. Ustaw wersję systemu operacyjnego gościa na **Automatyczną** (osVersion=* w pliku [cscfg),](cloud-services-model-and-package.md#cscfg) aby migracja do nowych wersji systemu operacyjnego gościa odbywała się automatycznie.

**Co zrobić, jeśli moja aplikacja internetowa wymaga głębszej integracji z os?**

Jeśli architektura aplikacji sieci web zależy od podstawowych funkcji systemu operacyjnego, użyj możliwości obsługiwanych przez platformę, takich jak [zadania uruchamiania](cloud-services-startup-tasks.md) lub inne mechanizmy rozszerzalności. Alternatywnie można również użyć [maszyny wirtualne platformy Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS — infrastruktura jako usługa), gdzie jesteś odpowiedzialny za utrzymanie podstawowego systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [najnowszymi wydaniami systemu operacyjnego dla gości](cloud-services-guestos-update-matrix.md).
