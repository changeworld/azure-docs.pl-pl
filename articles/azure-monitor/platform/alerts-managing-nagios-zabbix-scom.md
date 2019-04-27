---
title: Zarządzanie alertami programu SCOM, Zabbix i Nagios w usłudze Azure Monitor
description: Zarządzanie alertami programu SCOM, Zabbix i Nagios w usłudze Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 48fb9d8eaf2003834a420b48d649c830c608fd6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712988"
---
# <a name="manage-alerts-from-scom-zabbix-and-nagios-in-azure-monitor"></a>Zarządzanie alertami programu SCOM, Zabbix i Nagios w usłudze Azure Monitor

Teraz można wyświetlać alerty z programu System Center Operations Manager w, Nagios i Zabbix [usługi Azure Monitor](https://aka.ms/azure-alerts-overview). Te alerty pochodzą z integracji z serwery Nagios/Zabbix lub System Center Operations Manager w usłudze Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne
Wszystkie rekordy w repozytorium usługi Log Analytics za pomocą typu alertu zostanie Pobierz zaimportowany do usługi Azure Monitor, więc musi przeprowadzić konfigurację, który jest wymagany do zbierania tych rekordów.
1. Aby uzyskać **Nagios** i **Zabbix** alertów, [Konfigurowanie tych serwerów](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) do [wysyłania alertów](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) do usługi Log Analytics.
1. Aby uzyskać **System Center Operations Manager** alertów, [Połącz grupę zarządzania programu Operations Manager do obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). W związku z tym, należy wdrożyć [zarządzania alertami](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) rozwiązania z witryny marketplace rozwiązań platformy Azure. Po zakończeniu alerty utworzone w programie System Center Operations Manager są importowane do usługi Log Analytics.

## <a name="view-your-alert-instances"></a>Wyświetlanie wystąpień alertu
Po skonfigurowaniu importowania do usługi Log Analytics, możesz rozpocząć wyświetlanie wystąpień alertów z tych monitorowania usług w [usługi Azure Monitor](https://aka.ms/azure-alerts-overview). Po są obecne w usłudze Azure Monitor, możesz [Zarządzanie wystąpieniami danego alertu](https://aka.ms/managing-alert-instances), [Zarządzanie grupami inteligentne utworzone w tych alertach](https://aka.ms/managing-smart-groups) i [zmienić stan alertów i grup inteligentne](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. To rozwiązanie służy tylko do wyświetlania wystąpień alertów programu SCOM/Zabbix/Nagios uruchamiane w usłudze Azure Monitor. Konfiguracji reguły alertu można tylko wyświetlić/edycji w odpowiednich narzędzi monitorowania. 
>  1. Wszystkie wystąpienia alertu wyzwolone będą dostępne, zarówno w usłudze Azure Monitor i Azure Log Analytics. Obecnie nie ma możliwości do pozyskiwania tylko do określonych wyzwolone alerty lub wybrać między nimi.
>  1. Wszystkie alerty z programu SCOM i Zabbix Nagios ma typ sygnału "Nieznane", ponieważ nie jest typem podstawowym danych telemetrycznych.
>  1. Nagios alerty są stanowe — na przykład [monitorować stan](https://aka.ms/azure-alerts-overview) alertu nie zmieni się z "Fired" do stanu "rozwiązany". Zamiast tego "Fired" i "Rozwiązane" są wyświetlane jako oddzielne wystąpienia alertu. 

