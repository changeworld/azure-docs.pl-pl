---
title: Wykrywanie inteligentne platformy Azure Application Insights — nadchodząca zmiana domyślnego adresatów powiadomień | Microsoft Docs
description: Monitoruj ślady aplikacji za pomocą usługi Azure Application Insights dla nietypowych wzorców w telemetrii śledzenia.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1f98b5a9ee592a4c702e87e365eff7941194d4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820611"
---
# <a name="smart-detection-e-mail-notification-change"></a>Zmiana powiadomień e-mail dotyczących wykrywania inteligentnego

W oparciu o opinie klientów 1 kwietnia 2019 zmieniamy domyślne role, które odbierają powiadomienia e-mail od wykrywania inteligentnego.

## <a name="what-is-changing"></a>Co się zmieni?

Obecnie powiadomienia e-mail dotyczące wykrywania inteligentnego są domyślnie wysyłane do ról _właściciel subskrypcji_, _współautor subskrypcji_i _czytelnik subskrypcji_ . Te role często obejmują użytkowników, którzy nie biorą aktywnie udziału w monitorowaniu, co powoduje, że wielu użytkowników niepotrzebnie otrzymuje powiadomienia. Aby ulepszyć to środowisko, wprowadzamy zmianę, dzięki czemu powiadomienia e-mail są domyślnie wysyłane tylko do [czytnika monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) i [kontrolowania ról współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) .

## <a name="scope-of-this-change"></a>Zakres tej zmiany

Ta zmiana będzie mieć wpływ na wszystkie reguły wykrywania inteligentnego z wyjątkiem następujących reguł:

* Reguły wykrywania inteligentnego oznaczone jako wersja zapoznawcza. Te reguły inteligentnego wykrywania nie obsługują obecnie powiadomień e-mail.

* Reguła anomalii błędów. Ta zasada zacznie korzystać z nowych ról domyślnych po migracji z klasycznego alertu do platformy ujednoliconych alertów (więcej informacji jest dostępnych [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)).

## <a name="how-to-prepare-for-this-change"></a>Jak przygotować się do tej zmiany?

Aby zapewnić, że powiadomienia e-mail z wykrywania inteligentnego są wysyłane do odpowiednich użytkowników, użytkownicy muszą być przypisani do [czytnika monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) lub ról [współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) subskrypcji.

Aby przypisać użytkowników do czytnika monitorowania lub monitorować role współautor za pośrednictwem Azure Portal, wykonaj kroki opisane w artykule [Dodawanie przypisania roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) . Upewnij się, że jako rolę, do której użytkownicy są przypisani, ma zostać wybrany _czytnik monitorowania_ lub _współautor monitorowania_ .

> [!NOTE]
> Ta zmiana nie wpłynie na określonych odbiorców powiadomień o inteligentnym wykryciu skonfigurowanych przy użyciu opcji _dodatkowe Adresaci poczty e-mail_ w ustawieniach reguły. Ci adresaci będą nadal otrzymywać powiadomienia e-mail.

Jeśli masz jakieś pytania lub wątpliwości dotyczące tej zmiany, nie niechętnie zezwalają się z [nami](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykrywania inteligentnego:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)