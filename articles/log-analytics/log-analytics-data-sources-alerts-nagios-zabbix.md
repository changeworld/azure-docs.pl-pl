---
title: Zbieraj alerty Nagios i Zabbix w OMS Log Analytics | Dokumentacja firmy Microsoft
description: Nagios i Zabbix są typu open source, narzędzia do monitorowania. Alerty można zbierać z tych narzędzi do analizy dzienników w celu przeanalizowania wraz z alertów z innych źródeł.  W tym artykule opisano sposób konfigurowania agenta pakietu OMS dla systemu Linux zbierać alerty z tych systemów.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: magoedte
ms.openlocfilehash: a34a4be75488aca46fe232331e4bac3e0ac414b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637773"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Zbieraj alerty z Nagios i Zabbix w Log Analytics z usługą OMS agenta dla systemu Linux 
[Nagios](https://www.nagios.org/) i [Zabbix](http://www.zabbix.com/) są typu open source, narzędzia do monitorowania. Można zbierać alerty z tych narzędzi do analizy dzienników w celu przeanalizowania wraz z [alertów z innych źródeł](log-analytics-alerts.md).  W tym artykule opisano sposób konfigurowania agenta pakietu OMS dla systemu Linux zbierać alerty z tych systemów.
 
## <a name="prerequisites"></a>Wymagania wstępne
Agent pakietu OMS Linux obsługuje zbieranie alertów z Nagios do wersji 4.2.x i Zabbix do wersji 2.x.

## <a name="configure-alert-collection"></a>Konfigurowanie zbierania alertów

### <a name="configuring-nagios-alert-collection"></a>Konfigurowanie zbierania alertów Nagios
Aby zbierać alerty, wykonaj następujące czynności na serwerze Nagios.

1. Przyznaj użytkownikowi **omsagent** dostęp do odczytu do pliku dziennika Nagios `/var/log/nagios/nagios.log`. Zakładając, że plik nagios.log jest właścicielem grupy `nagios`, można dodać użytkownika **omsagent** do **nagios** grupy. 

    sudo usermod - -G nagios omsagent

2.  Modyfikowanie pliku konfiguracji na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Upewnij się, że następujące wpisy są obecne i nie komentarze wychodzących:  

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

3. Uruchom ponownie demona omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Konfigurowanie zbierania alertów Zabbix
Aby zbierać alerty z serwera Zabbix, należy określić użytkownika i hasło w *zwykły tekst*.  Gdy nie nadaje się doskonale, zaleca się utworzenie Zabbix użytkownika z uprawnieniami tylko do odczytu do odpowiednich alarmy catch.

Aby zbierać alerty na serwerze Nagios, wykonaj następujące kroki.

1. Modyfikowanie pliku konfiguracji na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Upewnij się, że następujące wpisy są obecne i nie komentarze wychodzących.  Zmień wartości w danym środowisku Zabbix nazwy użytkownika i hasła.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Uruchom ponownie demona omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Rejestruje alertu
Rejestruje alertu można pobrać z Nagios i Zabbix przy użyciu [dziennika wyszukiwania](log-analytics-log-searches.md) w analizy dzienników.

### <a name="nagios-alert-records"></a>Rejestruje Nagios Alert

Alert ma rekordy zebrane przez Nagios **typu** z **alertu** i **SourceSystem** z **Nagios**.  Mają one właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Alert* |
| SourceSystem |*Nagios* |
| AlertName |Nazwa alertu. |
| AlertDescription | Opis alertu. |
| AlertState | Stan usługi lub hosta.<br><br>OK<br>OSTRZEŻENIE<br>W GÓRĘ<br>W DÓŁ |
| Nazwa hosta | Nazwa hosta, który utworzony alert. |
| PriorityNumber | Poziom priorytetu alertu. |
| StateType | Typ stanu alertu.<br><br>SOFT - problem, który nie został zresetowany.<br>TWARDE — problem, który został zresetowany przez określoną liczbę razy.  |
| TimeGenerated |Data i godzina utworzenia alertu. |


### <a name="zabbix-alert-records"></a>Rejestruje alertu Zabbix
Alert ma rekordy zebrane przez Zabbix **typu** z **alertu** i **SourceSystem** z **Zabbix**.  Mają one właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Typ |*Alert* |
| SourceSystem |*Zabbix* |
| AlertName | Nazwa alertu. |
| AlertPriority | Ważność alertu.<br><br>nie sklasyfikowane<br>informacje<br>ostrzeżenie<br>średnia<br>Wysoka<br>po awarii  |
| AlertState | Stan alertu.<br><br>0 — jest aktualna.<br>1 — stan jest nieznany.  |
| AlertTypeNumber | Określa, czy alert może wygenerować wiele zdarzeń problem.<br><br>0 — jest aktualna.<br>1 — stan jest nieznany.    |
| Komentarze | Dodatkowe uwagi o alercie. |
| Nazwa hosta | Nazwa hosta, który utworzony alert. |
| PriorityNumber | Wartość wskazująca, ważność alertu.<br><br>0 — nie sklasyfikowane<br>1 — informacje<br>2 — ostrzeżenie<br>3 — średnia<br>4 - Wysoka<br>5 — po awarii |
| TimeGenerated |Data i godzina utworzenia alertu. |
| TimeLastModified |Data i godzina ostatniej zmiany stanu alertu. |


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [alerty](log-analytics-alerts.md) w analizy dzienników.
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania. 
