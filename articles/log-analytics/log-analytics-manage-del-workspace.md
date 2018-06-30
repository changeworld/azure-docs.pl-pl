---
title: Usuń z obszarem roboczym usługi Analiza dzienników Azure | Dokumentacja firmy Microsoft
description: Usuwanie obszaru roboczego analizy dzienników, jeśli został on utworzony w osobistym subskrypcji lub restrukturyzacji modelu obszaru roboczego.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 54f2af60751ed0d9c64e71efad6fa9aa3ef06589
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129119"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Usuń z obszarem roboczym usługi Analiza dzienników Azure przy użyciu portalu Azure
W tym artykule pokazano, jak korzystać z portalu Azure można usunąć obszaru roboczego analizy dzienników, który może nie są już potrzebne. 

## <a name="to-delete-a-workspace"></a>Aby usunąć obszar roboczy 
Po usunięciu obszaru roboczego analizy dzienników wszystkie dane związane z obszaru roboczego jest usuwane z usługi w ciągu 30 dni.  Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ może być ważnych danych i konfiguracji, które może niekorzystnie wpłynąć na operacje usługi. Należy wziąć pod uwagę usług platformy Azure i innych źródeł, które jego dane przechowywane w analizy dzienników, takich jak:

* Application Insights
* Azure Security Center
* Azure Automation
* Agentów uruchomionych na maszynach wirtualnych Windows i Linux
* Agentów z systemem Windows i Linux komputerów w środowisku
* System Center Operations Manager
* Rozwiązania do zarządzania 

Wszystkich agentów i skonfigurowane do raportu do obszaru roboczego grupy zarządzania programu System Center Operations Manager nadal w stanie oddzielony.  Jakie agentów rozwiązania w zakresie spisu i innymi usługami platformy Azure są zintegrowane z obszaru roboczego przed kontynuowaniem.   
 
Jeśli jesteś administratorem i z tym obszarem roboczym jest skojarzonych wielu użytkowników, skojarzenie między tymi użytkownikami i obszarem roboczym zostanie przerwane. Jeśli użytkownicy są skojarzeni z innymi obszarami roboczymi, mogą nadal z nich korzystać w usłudze Log Analytics. Jednak jeśli nie są one powiązane z innych obszarów roboczych następnie muszą utworzyć obszaru roboczego, aby użyć analizy dzienników. 

1. Zaloguj się do [Azure Portal](http://portal.azure.com). 
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcje analizy dzienników, wybierz obszar roboczy, a następnie kliknij przycisk **usunąć** od góry w środkowym okienku.<br><br> ![Usuń opcję w okienku właściwości obszaru roboczego](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Po wyświetleniu okna komunikatu potwierdzenia prośbą o potwierdzenie usunięcia obszaru roboczego, kliknij przycisk **tak**.<br><br> ![Potwierdzanie usunięcia obszaru roboczego](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

