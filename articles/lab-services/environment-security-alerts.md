---
title: Alerty zabezpieczeń dla środowisk w Azure DevTest Labs
description: W tym artykule opisano sposób wyświetlania alertów zabezpieczeń dla środowiska w programie DevTest Labs i podejmowania odpowiednich działań.
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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992237"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alerty zabezpieczeń dla środowisk w Azure DevTest Labs
W tym artykule opisano sposób wyświetlania alertów zabezpieczeń dla środowisk w Azure DevTest Labs. 

## <a name="prerequisites"></a>Wymagania wstępne
Obecnie można wyświetlać alerty zabezpieczeń tylko dla środowisk wdrożonych w środowisku laboratoryjnym. Aby przetestować lub użyć tej funkcji, wdróż środowisko w środowisku laboratoryjnym. 

## <a name="view-security-alerts-for-an-environment"></a>Wyświetlanie alertów zabezpieczeń dla środowiska

1. Na stronie głównej laboratorium wybierz pozycję **alerty zabezpieczeń** w menu po lewej stronie. Powinna zostać wyświetlona liczba alertów zabezpieczeń (wysoka, średnia i niska).

    ![Alerty zabezpieczeń — omówienie](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Kliknij prawym przyciskiem myszy trzy kropki (...) w ostatniej kolumnie, a następnie wybierz pozycję **Wyświetl alerty zabezpieczeń**. 

    ![Wyświetlanie alertów zabezpieczeń](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Zobaczysz więcej szczegółowych informacji na temat alertów i zaleceń usługi Advisor. 

    ![Wyświetlanie alertów zabezpieczeń](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o środowiskach, zobacz następujące artykuły:

- [Tworzenie środowisk z obsługą wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych](devtest-lab-configure-use-public-environments.md)
