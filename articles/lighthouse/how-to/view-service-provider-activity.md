---
title: Wyświetlanie działań dostawcy usług
description: Klienci mogą wyświetlać zarejestrowane działania, aby wyświetlić akcje wykonywane przez dostawców usług za pośrednictwem usługi Azure zarządzanie zasobami delegowanymi.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649640"
---
# <a name="view-service-provider-activity"></a>Wyświetlanie działań dostawcy usług

Klienci, którzy mają delegowane subskrypcje dla zarządzania zasobami delegowanymi platformy Azure można wyświetlić dane [dziennika aktywności platformy Azure,](../../azure-monitor/platform/platform-logs-overview.md) aby wyświetlić wszystkie akcje podjęte. Zapewnia to klientom pełny wgląd w operacje, które dostawcy usług wykonują za pośrednictwem usługi Azure zarządzanie zasobami delegowanymi, wraz z operacjami wykonywanymi przez użytkowników w ramach własnej dzierżawy usługi Azure Active Directory (Azure AD) klienta.

> [!TIP]
> Udostępniamy również wbudowaną definicję zasad usługi Azure Policy, aby inspekcji delegowania zakresów do dzierżawy zarządzającej. Aby uzyskać więcej informacji, zobacz [Inspekcja delegowania w twoim środowisku](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Wyświetlanie danych dziennika aktywności

Dziennik aktywności można [wyświetlić z](../../azure-monitor/platform/activity-log-view.md) menu **Monitor** w witrynie Azure portal. Aby ograniczyć wyniki do określonej subskrypcji, użyj filtrów, aby wybrać określoną subskrypcję. Można również [programowo wyświetlać i pobierać zdarzenia dziennika aktywności.](../../azure-monitor/platform/activity-log-view.md)

> [!NOTE]
> Użytkownicy w dzierżawie dostawcy usług mogą wyświetlać wyniki dziennika aktywności dla delegowanej subskrypcji w dzierżawie klienta, jeśli przyznano im rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy ta subskrypcja była dołączana do zarządzania zasobami delegowanymi platformy Azure.

W dzienniku aktywności zobaczysz nazwę operacji i jej stan wraz z datą i godziną jej wykonania. **Zdarzenie zainicjowane przez** kolumnę pokazuje, który użytkownik wykonał operację, czy był to użytkownik w dzierżawie dostawcy usług działający za pośrednictwem usługi Azure zarządzanie zasobami delegowanymi, czy użytkownik we własnej dzierżawie klienta. Należy zauważyć, że nazwa użytkownika jest wyświetlana, a nie dzierżawy lub roli, która została przypisana do tej subskrypcji.

Zarejestrowana aktywność jest dostępna w witrynie Azure portal w ciągu ostatnich 90 dni. Aby dowiedzieć się, jak przechowywać te dane przez okres dłuższy niż 90 dni, zobacz [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Użytkownicy z dostawcy usług są wyświetlane w dzienniku działań, ale tych użytkowników i ich przypisania ról nie są wyświetlane w **kontroli dostępu (IAM)** lub podczas pobierania informacji o przypisaniu roli za pośrednictwem interfejsów API.

## <a name="set-alerts-for-critical-operations"></a>Ustawianie alertów dla operacji krytycznych

Aby być świadomym krytycznych operacji, które wykonują dostawcy usług (lub użytkownicy we własnej dzierżawie), zalecamy tworzenie [alertów dziennika aktywności.](../../azure-monitor/platform/activity-log-alerts.md) Na przykład można śledzić wszystkie akcje administracyjne dla subskrypcji lub otrzymywać powiadomienia o usunięciu dowolnej maszyny wirtualnej w określonej grupie zasobów. Podczas tworzenia alertów będą one obejmować akcje wykonywane przez użytkowników w dzierżawie klienta, a także w dzierżawie zarządzającej.

Aby uzyskać więcej informacji, zobacz [Tworzenie alertów dziennika aktywności i zarządzanie nimi](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Tworzenie zapytań dziennika

Można tworzyć zapytania do analizowania zarejestrowanej aktywności lub skupić się na określonych elementach. Na przykład być może inspekcja wymaga raportowania wszystkich działań na poziomie administracyjnym wykonywanych w ramach subskrypcji. Można utworzyć kwerendę, aby filtrować tylko te akcje i sortować wyniki według użytkownika, daty lub innej wartości.

Aby uzyskać więcej informacji, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Monitor](../../azure-monitor/index.yml).
- Dowiedz się, jak [wyświetlać oferty dostawców usług i zarządzać nimi](view-manage-service-providers.md) w witrynie Azure portal.