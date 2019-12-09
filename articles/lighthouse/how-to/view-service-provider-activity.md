---
title: Wyświetlanie działania dostawcy usług
description: Klienci mogą wyświetlać zarejestrowane działanie, aby zobaczyć akcje wykonywane przez dostawców usług za pomocą funkcji zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932686"
---
# <a name="view-service-provider-activity"></a>Wyświetlanie działania dostawcy usług

Klienci, którzy mają delegowane subskrypcje do zarządzania zasobami delegowanymi przez platformę Azure, mogą [wyświetlać dane dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) , aby zobaczyć wszystkie wykonane akcje. Zapewnia to klientom pełny wgląd w operacje wykonywane przez dostawców usług za pomocą delegowania zasobów platformy Azure, a także tych wykonywanych przez użytkowników w ramach dzierżawy usługi Azure Active Directory (Azure AD) klienta.

## <a name="view-activity-log-data"></a>Wyświetl dane dziennika aktywności

[Dziennik aktywności można wyświetlić](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) z menu **Monitoruj** w Azure Portal. Aby ograniczyć wyniki do określonej subskrypcji, można użyć filtrów do wybrania określonej subskrypcji. Możesz również [wyświetlać i pobierać zdarzenia dziennika aktywności](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) .

> [!NOTE]
> Użytkownicy w dzierżawie dostawcy usług mogą wyświetlać wyniki dziennika aktywności dla delegowanej subskrypcji w dzierżawie klienta, jeśli otrzymali rolę [czytelnika](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy subskrypcja została dołączona do funkcji zarządzania zasobami delegowanymi przez platformę Azure.

W dzienniku aktywności zobaczysz nazwę operacji i jej stan wraz z datą i godziną wykonania. **Zdarzenie zainicjowane przez** kolumnę pokazuje, który użytkownik wykonał operację, niezależnie od tego, czy był on użytkownikiem w dzierżawie dostawcy usług działającym przez delegowane zarządzanie zasobami platformy Azure, czy użytkownikiem w dzierżawie klienta. Należy zauważyć, że nazwa użytkownika jest wyświetlana zamiast dzierżawy lub roli, do której użytkownik został przypisany do tej subskrypcji.

Zarejestrowane działanie jest dostępne w Azure Portal przez ostatnie 90 dni. Aby dowiedzieć się, jak przechowywać te dane przez dłużej niż 90 dni, zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

## <a name="set-alerts-for-critical-operations"></a>Ustawianie alertów dla operacji krytycznych

Aby zachować świadomość krytycznych operacji wykonywanych przez dostawców usług (lub użytkowników w Twojej dzierżawie), zalecamy tworzenie [alertów dziennika aktywności](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Na przykład możesz chcieć śledzić wszystkie akcje administracyjne dla subskrypcji lub otrzymywać powiadomienia o usunięciu dowolnej maszyny wirtualnej w danej grupie zasobów. Podczas tworzenia alertów będą one obejmować akcje podejmowane przez użytkowników w dzierżawie klienta, a także w innych dzierżawach.

Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności i zarządzanie nimi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

## <a name="create-log-queries"></a>Tworzenie zapytań dziennika

Możesz tworzyć zapytania, aby analizować zarejestrowane działanie lub skupić się na określonych elementach. Na przykład, być może Inspekcja wymaga, aby zgłosić wszystkie akcje na poziomie administracyjnym wykonywane w ramach subskrypcji. Można utworzyć zapytanie, aby odfiltrować tylko te akcje i sortować wyniki według użytkownika, daty lub innej wartości.

Aby uzyskać więcej informacji, zobacz [Omówienie zapytań dzienników w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/).
- Dowiedz się, jak [wyświetlać oferty dostawców usług i zarządzać nimi](view-manage-service-providers.md) w Azure Portal.