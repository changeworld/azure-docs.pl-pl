---
title: Alerty zabezpieczeń dla środowisk w laboratoriach DevTest Azure
description: W tym artykule pokazano, jak wyświetlić alerty zabezpieczeń dla środowiska w DevTest Labs i podjąć odpowiednie działania.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588709"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alerty zabezpieczeń dla środowisk w laboratoriach DevTest Azure
Jako użytkownik laboratorium możesz teraz wyświetlać alerty usługi Azure Security Center dla środowisk laboratoryjnych. Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów. W usłudze Security Center jest wyświetlana lista alertów zabezpieczeń uporządkowanych według priorytetu oraz informacje potrzebne do szybkiego analizowania problemu i zalecenia dotyczące postępowania w razie ataku. [Dowiedz się więcej o alertach zabezpieczeń w usłudze Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Wymagania wstępne
Obecnie można wyświetlać alerty zabezpieczeń tylko dla platformy jako usługi (PaaS) środowisk wdrożonych w laboratorium. Aby przetestować lub użyć tej funkcji, [należy wdrożyć środowisko w laboratorium](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Wyświetlanie alertów zabezpieczeń dla środowiska

1. Na stronie głównej laboratorium wybierz pozycję **Alerty zabezpieczeń** w menu po lewej stronie. Powinna zostać wyświetleni liczba alertów zabezpieczeń (wysoki, średni i niski). Dowiedz się więcej o [klasyfikowania alertów](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Alerty zabezpieczeń — omówienie](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Kliknij prawym przyciskiem myszy trzy kropki (...) w ostatniej kolumnie i wybierz pozycję **Wyświetl alerty zabezpieczeń**. 

    ![Wyświetlanie alertów zabezpieczeń](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Zobaczysz więcej szczegółów na temat alertów i zaleceń doradcy. Dowiedz się więcej na [temat zarządzania alertami zabezpieczeń i odpowiadania na nie w usłudze Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Wyświetlanie alertów zabezpieczeń](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o środowiskach, zobacz następujące artykuły:

- [Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych](devtest-lab-configure-use-public-environments.md)
