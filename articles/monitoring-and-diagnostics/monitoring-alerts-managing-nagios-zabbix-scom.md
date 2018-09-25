---
title: Zarządzanie alertami z innymi usługami monitorowania
description: Zarządzanie alertami Nagios i Zabbix SCOM w usłudze Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 0a3e0f1ecc40213aca37e37e80c9ba35abedb3d6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961217"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Zarządzanie alertami z innymi usługami monitorowania

Teraz można wyświetlać alerty z programu System Center Operations Manager w, Nagios i Zabbix [ujednolicone środowisko alertów](https://aka.ms/azure-alerts-overview). Te alerty pochodzą z integracji z serwery Nagios/Zabbix lub System Center Operations Manager w usłudze Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne
Wszystkie rekordy w repozytorium usługi Log Analytics z typem alertu zostanie Pobierz zaimportowany do usługi ujednolicone środowisko alertów na, więc musi przeprowadzić konfigurację, który jest wymagany do zbierania tych rekordów.
1. Aby uzyskać **Nagios** i **Zabbix** alertów, [Konfigurowanie tych serwerów](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents) do wysyłania alertów do usługi Log Analytics.
1. Aby uzyskać **System Center Operations Manager** alertów, [Połącz grupę zarządzania programu Operations Manager do obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents). Alerty utworzone w programie System Center Operations Manager są importowane do usługi Log Analytics.

## <a name="view-your-alert-instances"></a>Wyświetlanie wystąpień alertu
Po skonfigurowaniu importowania do usługi Log Analytics youd można rozpocząć wyświetlanie wystąpień alertów z tych monitorowania usług w [ujednolicone środowisko alertów](https://aka.ms/azure-alerts-overview). Po są obecne w środowisko alertów ujednolicone, możesz [Zarządzanie wystąpieniami danego alertu](https://aka.ms/managing-alert-instances), [Zarządzanie grupami inteligentne utworzone w tych alertach](https://aka.ms/managing-smart-groups) i [zmienić stan alertów i inteligentne grupy](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Nagios alerty w środowisku ujednoliconego alerty są stanowe — na przykład [monitorować stan](https://aka.ms/azure-alerts-overview) alertu nie zmieni się z "Fired" do stanu "rozwiązany". Zamiast tego "Fired" i "Rozwiązane" są wyświetlane jako oddzielne wystąpienia alertu. 
