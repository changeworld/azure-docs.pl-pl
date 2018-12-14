---
title: Zbieraj alerty programów Nagios i Zabbix w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Programów Nagios i Zabbix są typu open source, narzędzi do monitorowania. Z tych narzędzi może zbierać alerty w usłudze Log Analytics, aby analizować je oraz alertów z innych źródeł.  W tym artykule opisano sposób konfigurowania agenta usługi Log Analytics dla systemu Linux w celu gromadzenia alertów z poziomu tych systemów.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: magoedte
ms.openlocfilehash: 2d90c0c1ab1a6423a206345b7d7d6f34874d2366
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335731"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-log-analytics-agent-for-linux"></a>Zbieraj alerty z programów Nagios i Zabbix w usłudze Log Analytics z agenta usługi Log Analytics dla systemu Linux 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
[Nagios](https://www.nagios.org/) i [Zabbix](http://www.zabbix.com/) typu open source, narzędzi do monitorowania. Można zbierać alerty z tych narzędzi w usłudze Log Analytics w celu przeanalizowania wraz z [alertów z innych źródeł](../../azure-monitor/platform/alerts-overview.md).  W tym artykule opisano sposób konfigurowania agenta usługi Log Analytics dla systemu Linux w celu gromadzenia alertów z poziomu tych systemów.
 
## <a name="prerequisites"></a>Wymagania wstępne
Agenta usługi Log Analytics dla systemu Linux obsługuje zbieranie alertów z Nagios do wersji 4.2.x i Zabbix do wersji 2.x.

## <a name="configure-alert-collection"></a>Konfigurowanie zbierania alertów

### <a name="configuring-nagios-alert-collection"></a>Konfigurowanie zbierania alertów Nagios
Aby zbierać alerty, należy wykonać następujące czynności na serwerze Nagios.

1. Przyznaj użytkownikowi **omsagent** dostęp do odczytu do pliku dziennika Nagios `/var/log/nagios/nagios.log`. Zakładając, że plik nagios.log jest należące do grupy `nagios`, można dodać użytkownika **omsagent** do **nagios** grupy. 

    "sudo" usermod - -G nagios omsagent

2.  Zmodyfikuj plik konfiguracji na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Upewnij się, że istnieje i nie skomentowanej się następujące wpisy:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Ponownie uruchom demona omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Konfigurowanie zbierania alertów Zabbix
Aby zebrać alerty serwer Zabbix, należy określić użytkownika i hasło w *zwykły tekst*.  Chociaż nie jest idealnym rozwiązaniem, zaleca się utworzenie Zabbix użytkownika z uprawnieniami tylko do odczytu do przechwytywania istotnych alarmy.

Aby zbierać alerty na Nagios serwerze, wykonaj następujące czynności.

1. Zmodyfikuj plik konfiguracji na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Upewnij się, że istnieje i nie skomentowanej się następujące wpisy.  Zmień nazwę użytkownika i hasło do wartości w danym środowisku Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Ponownie uruchom demona omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Rekordy alertów
Rekordy alertów można pobrać z programów Nagios i Zabbix przy użyciu [dziennikach](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics.

### <a name="nagios-alert-records"></a>Rekordy Nagios alertu

Zgłoś alert, ma rekordów zbieranych przez Nagios **typu** z **alertu** i **system źródłowy** z **Nagios**.  One mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Zgłoś alert* |
| SourceSystem |*Nagios* |
| AlertName |Nazwa alertu. |
| AlertDescription | Opis alertu. |
| AlertState | Stan hosta lub usługi.<br><br>OK<br>OSTRZEŻENIE<br>W GÓRĘ<br>W DÓŁ |
| Nazwa hosta | Nazwa hosta, która utworzyła alert. |
| PriorityNumber | Priorytet alertu. |
| StateType | Typ stanu alertu.<br><br>SOFT - problem, który nie został zresetowany.<br>TWARDE — problem, który został zresetowany określoną liczbę razy.  |
| TimeGenerated |Data i godzina utworzenia alertu. |


### <a name="zabbix-alert-records"></a>Rekordy alertów Zabbix
Zgłoś alert, ma rekordów zbieranych przez Zabbix **typu** z **alertu** i **system źródłowy** z **Zabbix**.  One mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Zgłoś alert* |
| SourceSystem |*Zabbix* |
| AlertName | Nazwa alertu. |
| AlertPriority | Ważność alertu.<br><br>niesklasyfikowanych<br>informacje<br>ostrzeżenie<br>średnia<br>Wysoka<br>po awarii  |
| AlertState | Stan alertu.<br><br>0 — jest aktualna.<br>1 — stan jest nieznany.  |
| AlertTypeNumber | Określa, czy alert może wygenerować wiele zdarzeń problem.<br><br>0 — jest aktualna.<br>1 — stan jest nieznany.    |
| Komentarze | Dodatkowe komentarze dla alertu. |
| Nazwa hosta | Nazwa hosta, która utworzyła alert. |
| PriorityNumber | Wartość wskazująca, ważność alertu.<br><br>0 - niesklasyfikowanych<br>1 — informacje<br>2 — ostrzeżenie<br>3 — średnia<br>4 — Wysoka<br>5 — po awarii |
| TimeGenerated |Data i godzina utworzenia alertu. |
| TimeLastModified |Data i godzina ostatniej zmiany stanu alertu. |


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [alerty](../../azure-monitor/platform/alerts-overview.md) w usłudze Log Analytics.
* Dowiedz się więcej o [dziennikach](../../azure-monitor/log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 
