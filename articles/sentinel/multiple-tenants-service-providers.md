---
title: Praca z wieloma dzierżawcami usługi Azure Sentinel dla dostawców usług MSSP| Dokumenty firmy Microsoft
description: Jak pracować z wieloma dzierżawami usługi Azure Sentinel dla dostawców usług MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476019"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Praca z wieloma dzierżawcami w usłudze Azure Sentinel 

Jeśli jesteś dostawcą usług zabezpieczeń zarządzanych (MSSP) i używasz [latarni morskiej platformy Azure](../lighthouse/overview.md) do zarządzania centrami operacji zabezpieczeń klientów (SOC), będziesz mógł zarządzać zasobami warty Azure swoich klientów bez łączenia się bezpośrednio z dzierżawą klienta z własnej dzierżawy platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
- [Wbudowana latarnia morska platformy Azure](../lighthouse/how-to/onboard-customer.md)
- Aby to działało poprawnie, dzierżawa musi być zarejestrowana w dostawcy zasobów wartowniczych platformy Azure w co najmniej jednej subskrypcji. Jeśli masz zarejestrowaną usługę Azure Sentinel w dzierżawie, możesz rozpocząć pracę. Jeśli nie, wybierz subskrypcje z **witryny** Azure portal, a następnie **dostawców zasobów**.  Następnie na ekranie **SOC - Resource** providers `Microsoft.OperationalInsights` `Microsoft.SecurityInsights`wyszukaj i wybierz, a następnie wybierz pozycję **Zarejestruj**.
   ![Sprawdzanie dostawców zasobów](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Jak uzyskać dostęp do usługi Azure Sentinel od innych dzierżawców
1. W obszarze **Subskrypcja katalogu +** wybierz katalogi delegowane i subskrypcje, w których znajdują się obszary robocze usługi Azure Sentinel klienta.

   ![Generowanie incydentów związanych z bezpieczeństwem](media/multiple-tenants-service-providers/directory-subscription.png)

1. Otwórz usługę Azure Sentinel. Zobaczysz wszystkie obszary robocze w wybranych subskrypcjach i będziesz mógł pracować z nimi bezproblemowo, jak każdy obszar roboczy we własnej dzierżawie.

> [!NOTE]
> Nie będzie można wdrożyć łączników w usłudze Azure Sentinel z poziomu zarządzanego obszaru roboczego. Aby wdrożyć łącznik, należy bezpośrednio zalogować się do dzierżawy, w którym chcesz wdrożyć łącznik i uwierzytelnić tam z wymaganych uprawnień.





## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak bezproblemowo zarządzać wieloma dzierżawami usługi Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).

