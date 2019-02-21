---
title: Usługa Azure Application Insights wykrywanie inteligentne — nadchodzące zmiany domyślnego adresatów powiadomień | Dokumentacja firmy Microsoft
description: Monitoruj śladów aplikacji za pomocą usługi Azure Application Insights nietypowe wzorce w danych telemetrycznych śledzenia.
services: application-insights
author: harelbr
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 02/12/2019
ms.author: harelbr
ms.openlocfilehash: 4bcbed82a78caff62a9459ecb44c6513f367f6b7
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56457993"
---
# <a name="smart-detection-e-mail-notification-change"></a>Zmień powiadomienia e-mail w usłudze inteligentnego wykrywania

Na podstawie opinii klientów, na 1 kwietnia 2019 Zmieniamy domyślne role, którzy otrzymywać powiadomienia e-mail z wykrywania inteligentnego.

## <a name="what-is-changing"></a>Co ulega zmianie?

Obecnie są wysyłane powiadomienia e-mail wykrywania inteligentnego, aby domyślnie _właściciela subskrypcji_, _współautora subskrypcji_, i _Czytelnik subskrypcji_ ról. Role te często zawierają użytkowników, którzy nie są aktywnie związanych z monitorowaniem, co powoduje, że wiele z tych użytkowników, aby otrzymywać powiadomienia o niepotrzebnie. Aby poprawić to środowisko, wprowadzamy zmiany, aby powiadomienia e-mail odbywają się tylko [Czytelnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) i [Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role domyślnie.

## <a name="scope-of-this-change"></a>Zakres tej zmiany

Ta zmiana wpłynie na wszystkie reguły wykrywania inteligentnego, z wyjątkiem poniższych:

* Reguły wykrywania inteligentnego oznaczone jako wersji zapoznawczej. Te reguły wykrywania inteligentnego nie obsługują obecnie powiadomienia e-mail.

* Reguła o niepowodzeniu anomalii. Ta reguła zostanie uruchomiona, przeznaczone dla nowych ról domyślne po jej zmigrowane z klasycznego alertu platformę ujednoliconego alertów (więcej informacji znajduje się [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).)

## <a name="how-to-prepare-for-this-change"></a>Jak przygotować się do tej zmiany?

Aby upewnić się, że powiadomienia e-mail z wykrywania inteligentnego są wysyłane do użytkowników, których Ci użytkownicy muszą być przypisane do [Czytelnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) i [Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ról. Przydziału należy dokonać, albo na poziomie subskrypcji (co wpływa na wszystkie zasoby usługi Application Insights w ramach subskrypcji) lub na poziomie zasobu usługi Application Insights (co wpływa na tylko tego konkretnego zasobu).

Aby przypisać użytkowników do ról monitorowania czytelnika lub współautora monitorowania w witrynie Azure portal, wykonaj czynności opisane w [Dodaj przypisanie roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) artykułu. Upewnij się, że wybrano _Czytelnik monitorowania_ lub _Współautor monitorowania_ jako rola, do którego są przypisane użytkownikom.

> [!NOTE]
> Określonych adresatów powiadomień wykrywania inteligentnego, skonfigurowany przy użyciu _dodatkowi adresaci wiadomości e-mail_ opcji ustawienia reguł, nie będzie mieć wpływ tej zmiany. Te adresaci będą nadal odbierania powiadomień e-mail.

Jeśli masz pytania lub uwagi dotyczące tej zmiany nie wahaj się [skontaktuj się z nami](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat inteligentnego wykrywania:

- [Anomalie błędów](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Przecieki pamięci](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie wydajności](../../azure-monitor/app/proactive-performance-diagnostics.md)