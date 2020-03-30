---
title: Zarządzanie alertami z programu System Center Operations Manager, Zabbix i Nagios w usłudze Azure Monitor
description: Zarządzanie alertami z programu System Center Operations Manager, Zabbix i Nagios w usłudze Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667452"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Zarządzanie alertami z programu System Center Operations Manager, Zabbix i Nagios w usłudze Azure Monitor

Teraz możesz wyświetlać alerty z Nagios, Zabbix i System Center Operations Manager w [usłudze Azure Monitor](https://aka.ms/azure-alerts-overview). Te alerty pochodzą z integracji z serwerami Nagios/Zabbix lub Programem Operacyjnym Centrum systemu w usłudze Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne
Wszystkie rekordy w repozytorium usługi Log Analytics z typem alertu zostaną zaimportowane do usługi Azure Monitor, dlatego należy wykonać konfigurację, która jest wymagana do zbierania tych rekordów.
1. W przypadku **alertów Nagios** i **Zabbix** [skonfiguruj te serwery](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) do [wysyłania alertów](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) do usługi Log Analytics.
1. W przypadku alertów **programu System Center Operations Manager** [połącz grupę zarządzania programu Operations Manager z obszarem roboczym usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Następnie wdrożyć rozwiązanie [do zarządzania alertami](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) z rynku rozwiązań platformy Azure. Po zakończeniu wszystkie alerty utworzone w programie System Center Operations Manager są importowane do usługi Log Analytics.

## <a name="view-your-alert-instances"></a>Wyświetlanie wystąpień alertów
Po skonfigurowaniu importu do usługi Log Analytics można rozpocząć wyświetlanie wystąpień alertów z tych usług monitorowania w [usłudze Azure Monitor](https://aka.ms/azure-alerts-overview). Gdy są one obecne w usłudze Azure Monitor, można [zarządzać wystąpieniami alertów,](https://aka.ms/managing-alert-instances) [zarządzać inteligentnymi grupami utworzonymi w tych alertach](https://aka.ms/managing-smart-groups) i [zmieniać stan alertów i grup inteligentnych.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  1. To rozwiązanie umożliwia wyświetlanie tylko system center operations manager/Zabbix/Nagios zwolnionych wystąpień alertów w usłudze Azure Monitor. Konfigurację reguły alertu można wyświetlać/edytować tylko w odpowiednich narzędziach monitorowania. 
>  1. Wszystkie zwolnione wystąpienia alertów będą dostępne zarówno w usłudze Azure Monitor, jak i usłudze Azure Log Analytics. Obecnie nie ma możliwości wyboru między dwoma lub pozyskiwania tylko określonych alertów zwolnionych.
>  1. Wszystkie alerty z Programu Operacyjnego System Center, Zabbix i Nagios mają typ sygnału "Nieznany", ponieważ podstawowy typ telemetrii nie jest dostępny.
>  1. Alerty Nagios nie są stanowe — na przykład [stan monitora](https://aka.ms/azure-alerts-overview) alertu nie przejdzie z "Zwolniony" do "Rozwiązany". Zamiast tego zarówno "Fired" i "Resolved" są wyświetlane jako oddzielne wystąpienia alertów. 

