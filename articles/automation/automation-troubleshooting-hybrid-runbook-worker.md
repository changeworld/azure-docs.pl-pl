---
title: Rozwiązywanie problemów z procesu roboczego elementu Runbook hybrydowego usługi Automatyzacja Azure
description: Opisz objawy, przyczyny i rozwiązania typowych problemów hybrydowy proces roboczy elementu Runbook automatyzacji Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7fe0662d8bf3b52c0de94dbc70126eb813883402
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Porady dotyczące rozwiązywania problemów hybrydowy proces roboczy elementu Runbook

Ten artykuł zawiera pomoc w rozwiązywaniu błędów mogą wystąpić z hybrydowymi elementami roboczymi Runbook automatyzacji, a także sugeruje możliwe rozwiązania, aby je rozwiązać.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Kończy zadanie elementu runbook o stanie zawieszone

Element runbook został wstrzymany, wkrótce po próby wykonania jej trzy razy. Istnieją warunki, które mogą przerwać pomyślne zakończenie działania elementu runbook i powiązane komunikat nie ma żadnych dodatkowych informacji i wskazujący przyczynę. Ten artykuł zawiera kroki rozwiązywania problemów związanych z błędami wykonywania elementu runbook hybrydowy proces roboczy elementu Runbook.

Jeśli problem Azure nie jest opisany w tym artykule, odwiedź fora Azure na [MSDN i przepełnienie stosu](https://azure.microsoft.com/support/forums/). Możesz zamieścić problemu na tych fora lub do [ @AzureSupport w serwisie Twitter](https://twitter.com/AzureSupport). Ponadto można pliku żądania pomocy technicznej platformy Azure, wybierając **uzyskać pomoc techniczną** na [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) lokacji.

### <a name="symptom"></a>Objaw

Wykonanie elementu Runbook nie powiedzie się i zwrócony błąd to "akcji zadania, którego nie można uruchomić"Uaktywnij", ponieważ proces nieoczekiwanie zatrzymał się. Akcja zadania podjęto próbę trzy razy".

Istnieje kilka możliwych przyczyn tego błędu:

1. Hybrydowy proces roboczy jest używany serwer proxy lub Zapora
2. Elementy runbook nie mogą uwierzytelnić się przy użyciu lokalnych zasobów

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Przyczyna 1: Hybrydowy proces roboczy elementu Runbook jest używany serwer proxy lub Zapora

Hybrydowy proces roboczy elementu Runbook na którym działa komputer znajduje się za serwerem zapory lub serwera proxy i dostępu sieciowego ruchu wychodzącego nie może być dozwolone lub prawidłowo skonfigurowane.

#### <a name="solution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp ruchu wychodzącego do *.azure automation.net na porcie 443.

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>2 Przyczyna: Komputer ma mniej niż minimalne wymagania sprzętowe

Komputery z systemem hybrydowy proces roboczy elementu Runbook powinna spełniać minimalne wymagania sprzętowe przed oznaczeniem go do obsługi tej funkcji. W przeciwnym razie w zależności od innych procesów w tle i rywalizacji spowodowane przez elementy runbook podczas wykonywania na wykorzystanie zasobów komputera staje się nadmiernie wykorzystywany i spowodować opóźnienia zadania elementu runbook i przekroczeń limitu czasu.

#### <a name="solution"></a>Rozwiązanie

Najpierw upewnij się, że komputer wyznaczony do uruchomienia funkcji hybrydowego procesu roboczego elementu Runbook spełnia minimalne wymagania sprzętowe. Jeśli tak, monitorowanie wykorzystania procesora CPU i pamięci, aby określić korelacja wydajności procesów hybrydowy proces roboczy elementu Runbook i Windows. Brak pamięci lub dużego wykorzystania procesora CPU, może to oznaczać konieczność uaktualnienia lub dodać dodatkowych procesorów lub zwiększ ilość pamięci do adresów wąskie gardło zasobów i Usuń przyczynę błędu. Opcjonalnie zaznacz zasób różnych obliczeń, który może obsługiwać minimalne wymagania i skalowania, gdy wymaganego obciążenia wskazywać wzrost jest konieczne.

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Przyczyny 3: Elementy Runbook nie można uwierzytelnić z zasobów lokalnych

#### <a name="solution"></a>Rozwiązanie

Sprawdź **Microsoft SMA** odpowiednie zdarzenie z opisem w dzienniku zdarzeń *Win32 proces został zakończony z kodem [4294967295]*. Przyczyną tego błędu jest nie jeszcze skonfigurowane uwierzytelnianie w elementach runbook lub określony poświadczeń Uruchom jako grupy hybrydowych procesów roboczych. Przegląd [uprawnienia elementów Runbook](automation-hrw-run-runbooks.md#runbook-permissions) o potwierdzenie zostały prawidłowo skonfigurowane uwierzytelnianie na elementach runbook.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać Pomoc innych problemów w automatyzacji, [rozwiązywania typowych problemów z usługi Automatyzacja Azure](automation-troubleshooting-automation-errors.md)