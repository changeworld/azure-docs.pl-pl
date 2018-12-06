---
title: Utwórz obszar roboczy usługi Log Analytics w witrynie Azure Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obszar roboczy usługi Log Analytics, aby włączyć zarządzanie rozwiązań i gromadzenie danych ze środowiska w chmurze i lokalnych, w witrynie Azure portal.
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
ms.date: 08/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: db0945d9151303e29ac4d585222818fa1d71dbab
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975032"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal
W witrynie Azure portal, które można skonfigurować obszar roboczy usługi Log Analytics, która jest unikatowa środowisko usługi Log Analytics z własnym repozytorium danych, źródłami danych i rozwiązań.  Kroki opisane w tym artykule są wymagane, jeśli jest planowane na zbieranie danych z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji
* Lokalnych komputerów monitorowanych przez program System Center Operations Manager
* Kolekcje w programie System Center Configuration Manager 
* Dane diagnostyczne lub dziennika z usługi Azure storage

W przypadku innych źródeł, takie jak maszyny wirtualne platformy Azure i Windows lub maszyny wirtualne systemu Linux w środowisku zobacz następujące tematy:

*  [Zbieranie danych z maszyn wirtualnych platformy Azure](../../log-analytics/log-analytics-quick-collect-azurevm.md) 
*  [Zbieranie danych z komputera z systemem Linux hybrydowe](../../log-analytics/log-analytics-quick-collect-linux-computer.md)
*  [Zbieranie danych z komputera Windows hybrydowe](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Kliknij pozycję **Utwórz**, a następnie wybierz opcje dla następujących elementów:

  * Podaj nazwę dla nowego **obszaru roboczego usługi Log Analytics**, na przykład *DefaultLAWorkspace*. 
  * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
  * Dla **grupy zasobów**, chce użyć istniejącego zasobu, grupy już instalacji lub utworzyć nowy.  
  * Wybierz dostępny **lokalizacji**.  Aby uzyskać więcej informacji, zobacz, które [usługi Log Analytics jest dostępna w regionach](https://azure.microsoft.com/regions/services/).
  * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  Jeśli tworzysz obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją Enterprise Agreement (EA), wybierz preferowaną warstwę cenową.  Aby uzyskać więcej informacji na temat konkretnej warstwy, zobacz [Log Analytics — szczegóły cennika](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po podaniu wymaganych informacji w okienku **Obszar roboczy usługi Log Analytics** kliknij przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy masz dostępnego obszaru roboczego, możesz skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiają przeszukiwanie dzienników w celu analizowania danych i dodać rozwiązanie do zarządzania w celu zapewnienia dodatkowych danych i szczegółowych informacji analitycznych. 

* Aby włączyć zbieranie danych z zasobów platformy Azure Diagnostyka Azure lub usługi Azure storage, zobacz [zbieranie dzienników platformy Azure usługi i metryk do użycia w usłudze Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md).  
* [Dodaj System Center Operations Manager jako źródła danych](../../azure-monitor/platform/om-agents.md) do zbierania danych z agentów raportujących do grupy zarządzania programu Operations Manager i zapisać ją w obszarze roboczym usługi Log Analytics. 
* Połącz [programu Configuration Manager](../../log-analytics/log-analytics-sccm.md) do zaimportowania komputerów, które są członkami kolekcji w hierarchii.  
* Przegląd [rozwiązań do zarządzania](https://docs.microsoft.com/azure/monitoring/monitoring-solutions-inventory?toc=%2fazure%2flog-analytics%2ftoc.json) dostępne i jak dodać lub usunąć rozwiązanie z obszaru roboczego.
