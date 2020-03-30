---
title: Zmiana powiadomień o wykrywaniu inteligentnego — usługa Azure Application Insights
description: Zmień na domyślnych adresatów powiadomień z inteligentnego wykrywania. Inteligentne wykrywanie umożliwia monitorowanie śladów aplikacji za pomocą usługi Azure Application Insights pod kątem nietypowych wzorców w telemetrii śledzenia.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671787"
---
# <a name="smart-detection-e-mail-notification-change"></a>Zmiana powiadomień e-mail z funkcją wykrywania inteligentnego

Na podstawie opinii klientów, 1 kwietnia 2019 r. zmieniamy domyślne role, które otrzymują powiadomienia e-mail z inteligentnego wykrywania.

## <a name="what-is-changing"></a>Co się zmienia?

Obecnie powiadomienia e-mail z funkcją wykrywania inteligentnego są wysyłane domyślnie do ról _Właściciel subskrypcji,_ _Współautor subskrypcji_i _Czytnik subskrypcji._ Role te często obejmują użytkowników, którzy nie są aktywnie zaangażowani w monitorowanie, co powoduje, że wielu z tych użytkowników niepotrzebnie otrzymywać powiadomienia. Aby poprawić to środowisko, wprowadzamy zmiany, dzięki czemu powiadomienia e-mail domyślnie przechodzą tylko do ról [czytnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) i [monitorowanie współautora.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="scope-of-this-change"></a>Zakres tej zmiany

Ta zmiana będzie miała wpływ na wszystkie reguły inteligentnego wykrywania, z wyłączeniem następujących reguł:

* Reguły inteligentnego wykrywania oznaczone jako podgląd. Te reguły inteligentnego wykrywania nie obsługują powiadomień e-mail.

* Reguła anomalie awarii. Ta reguła rozpocznie kierowanie na nowe role domyślne po migracji z klasycznego alertu do platformy ujednoliconych alertów (więcej informacji jest dostępnych [tutaj).](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)

## <a name="how-to-prepare-for-this-change"></a>Jak przygotować się do tej zmiany?

Aby upewnić się, że powiadomienia e-mail z inteligentnego wykrywania są wysyłane do odpowiednich użytkowników, ci użytkownicy muszą być przypisane do [monitorowania czytnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) lub [monitorowania współautora](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ról subskrypcji.

Aby przypisać użytkowników do ról czytnika monitorowania lub monitorowania współautora za pośrednictwem witryny Azure portal, wykonaj kroki opisane w artykule [Dodawanie przypisania roli.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) Upewnij się, że jako rolę, do której przypisani są użytkownicy, wybierz _czytnik monitorowania_ lub _współautora monitorowania._

> [!NOTE]
> Zmiana ta nie będzie miała wpływu na określonych odbiorców powiadomień z funkcją wykrywania inteligentnego, skonfigurowanych przy użyciu opcji _Dodatkowe adresaci wiadomości e-mail_ w ustawieniach reguły. Odbiorcy ci będą nadal otrzymywać powiadomienia e-mail.

Jeśli masz jakieś pytania lub wątpliwości dotyczące tej zmiany, nie wahaj się [z nami skontaktować.](mailto:smart-alert-feedback@microsoft.com)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o inteligentnym wykrywaniu:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)
