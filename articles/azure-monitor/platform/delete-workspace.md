---
title: Usuń obszar roboczy usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć obszaru roboczego usługi Log Analytics, jeśli został on utworzony w ramach subskrypcji osobistych lub restrukturyzacji modelu obszaru roboczego.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: a6542838acba3143123dc90d96746179a2b4469b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236129"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Usuń obszar roboczy usługi Azure Log Analytics w witrynie Azure portal
W tym artykule przedstawiono sposób użyj witryny Azure portal, aby usunąć obszar roboczy usługi Log Analytics może nie są już potrzebne. 

## <a name="to-delete-a-workspace"></a>Aby usunąć obszar roboczy 
Jeśli usuniesz obszar roboczy usługi Log Analytics, wszystkie dane związane z obszaru roboczego jest usunięte z usługi w ciągu 30 dni.  Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ może być ważne dane i konfiguracji, która może mieć negatywny wpływ na operacje usługi. Należy wziąć pod uwagę innych usług platformy Azure i źródłach danych przechowujących dane w usłudze Log Analytics, takie jak:

* Application Insights
* Azure Security Center
* Azure Automation
* Agentów uruchamianych na maszynach wirtualnych Windows i Linux
* Agentów z systemem Windows i Linux komputerów w środowisku
* System Center Operations Manager
* Rozwiązania do zarządzania 

Wszystkich agentów i skonfigurowany pod kątem raportowania do obszaru roboczego grupy zarządzania programu System Center Operations Manager nadal w stanie oddzielony.  Zapasów jakie agentów, rozwiązań i innych usług platformy Azure są zintegrowane z obszarem roboczym, przed kontynuowaniem.   
 
Jeśli jesteś administratorem i z tym obszarem roboczym jest skojarzonych wielu użytkowników, skojarzenie między tymi użytkownikami i obszarem roboczym zostanie przerwane. Jeśli użytkownicy są skojarzeni z innymi obszarami roboczymi, mogą nadal z nich korzystać w usłudze Log Analytics. Jednakże jeśli nie są oni skojarzeni z innymi obszarami roboczymi następnie muszą utworzyć obszar roboczy, aby użyć usługi Log Analytics. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszarów roboczych usługi Log Analytics**.
3. W okienku subskrypcji usługi Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Usuń** od góry w środkowym okienku.<br><br> ![Usuń opcję z okienka właściwości obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace.png)<br>  
4. Gdy pojawi się okno komunikatu potwierdzenia, za pomocą prośbą o potwierdzenie usunięcia obszaru roboczego, kliknij przycisk **tak**.<br><br> ![Potwierdzenie usunięcia obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

