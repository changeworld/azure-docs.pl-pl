---
title: Zbieranie i analizowanie wiadomości syslogu w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Syslog to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. W tym artykule opisano sposób konfigurowania kolekcji wiadomości Syslog w usłudze Log Analytics i szczegółów rekordów, które tworzą.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274725"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Syslog data sources in Azure Monitor (Źródła danych usługi Syslog w usłudze Azure Monitor)
Syslog to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będą wysyłać wiadomości, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego Syslog. Po zainstalowaniu agenta usługi Log Analytics dla systemu Linux konfiguruje lokalny demon Syslog do przekazywania wiadomości do agenta. Agent następnie wysyła wiadomość do usługi Azure Monitor, gdzie tworzony jest odpowiedni rekord.  

> [!NOTE]
> Usługa Azure Monitor obsługuje zbieranie wiadomości wysyłanych przez rsyslog lub syslog-ng, gdzie rsyslog jest domyślnym demonem. Domyślny demon syslog w wersji 5 red hat enterprise linux, CentOS i Oracle Linux wersja (sysklog) nie jest obsługiwana dla kolekcji zdarzeń syslog. Aby zebrać dane syslog z tej wersji tych dystrybucji, [demon rsyslog](http://rsyslog.com) powinien być zainstalowany i skonfigurowany do zastąpienia sysklog.
>
>

![Kolekcja Syslog](media/data-sources-syslog/overview.png)

Następujące obiekty są obsługiwane przez kolektor syslog:

* Kern
* użytkownik
* mail (poczta)
* Daemon
* Auth
* syslog
* Lpr
* news
* Uucp
* Cron
* authpriv
* ftp
* local0-local7

W przypadku dowolnej innej funkcji [skonfiguruj źródło danych dzienników niestandardowych](data-sources-custom-logs.md) w usłudze Azure Monitor.
 
## <a name="configuring-syslog"></a>Konfigurowanie sysloga
Agent usługi Log Analytics dla systemu Linux będzie zbierać tylko zdarzenia z obiektami i ważnościami, które są określone w jego konfiguracji. Syslog można skonfigurować za pośrednictwem witryny Azure portal lub zarządzając plikami konfiguracyjnymi na swoich agentów systemu Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurowanie sysloga w witrynie Azure portal
Skonfiguruj syslog z [menu Dane w ustawieniach zaawansowanych](agent-data-sources.md#configuring-data-sources). Ta konfiguracja jest dostarczana do pliku konfiguracyjnego dla każdego agenta systemu Linux.

Możesz dodać nową placówkę, najpierw wybierając opcję **Zastosuj poniżej konfigurację do moich maszyn,** a następnie wpisując jej nazwę i klikając **+** przycisk . Dla każdej placówki będą zbierane tylko wiadomości o wybranych ważnościach.  Sprawdź ważność dla konkretnej placówki, którą chcesz zebrać. Nie można podać żadnych dodatkowych kryteriów filtrowania wiadomości.

![Konfigurowanie sysloga](media/data-sources-syslog/configure.png)

Domyślnie wszystkie zmiany konfiguracji są automatycznie wypychane do wszystkich agentów. Jeśli chcesz skonfigurować Syslog ręcznie na każdym agencie Linuksa, a następnie odznacz pole *Zastosuj poniżej konfiguracji do moich maszyn*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurowanie Sysloga w programie Linux agent
Gdy [agent usługi Log Analytics jest zainstalowany na kliencie systemu Linux,](../../azure-monitor/learn/quick-collect-linux-computer.md)instaluje domyślny plik konfiguracji syslog, który definiuje obiekt i ważność wiadomości, które są zbierane. Można zmodyfikować ten plik, aby zmienić konfigurację. Plik konfiguracyjny różni się w zależności od demona Sysloga zainstalowanego przez klienta.

> [!NOTE]
> W przypadku edycji konfiguracji syslog należy ponownie uruchomić demon syslog, aby zmiany zostały wprowadzone.
>
>

#### <a name="rsyslog"></a>Rsyslog
Plik konfiguracyjny dla rsyslog znajduje się na **/etc/rsyslog.d/95-omsagent.conf**. Jego domyślna zawartość przedstawiona jest poniżej. To zbiera syslog wiadomości wysyłane z lokalnego agenta dla wszystkich obiektów z poziomem ostrzeżenia lub wyższym.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Obiekt można usunąć, usuwając jej sekcję pliku konfiguracyjnego. Można ograniczyć ważność, które są zbierane dla określonego obiektu, modyfikując wpis tej placówki. Na przykład, aby ograniczyć ść użytkownika do komunikatów o ważności błędu lub wyższej, należy zmodyfikować ten wiersz pliku konfiguracyjnego do następujących:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Plik konfiguracyjny dla syslog-ng znajduje się pod adresem **/etc/syslog-ng/syslog-ng.conf**.  Jego domyślna zawartość przedstawiona jest poniżej. To zbiera syslog wiadomości wysyłane z lokalnego agenta dla wszystkich obiektów i wszystkie ważność.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Obiekt można usunąć, usuwając jej sekcję pliku konfiguracyjnego. Można ograniczyć ważność, które są zbierane dla określonej placówki, usuwając je z listy.  Na przykład, aby ograniczyć możliwości użytkownika do tylko alertów i krytycznych komunikatów, należy zmodyfikować tę sekcję pliku konfiguracyjnego do następujących czynności:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Zbieranie danych z dodatkowych portów Syslog
Agent usługi Log Analytics nasłuchuje komunikatów Syslog na kliencie lokalnym na porcie 25224.  Po zainstalowaniu agenta domyślna konfiguracja syslog jest stosowana i znajdowana w następującej lokalizacji:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

Numer portu można zmienić, tworząc dwa pliki konfiguracyjne: plik konfiguracyjny FluentD i plik rsyslog-or-syslog-ng w zależności od zainstalowanego demona Syslog.  

* Plik konfiguracyjny FluentD powinien być `/etc/opt/microsoft/omsagent/conf/omsagent.d` nowym plikiem znajdującym się w: i zastąpić wartość wpisu **portu** niestandardowym numerem portu.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* W przypadku rsyslog należy utworzyć nowy plik `/etc/rsyslog.d/` konfiguracyjny znajdujący się w: i zastąpić wartość %SYSLOG_PORT% niestandardowym numerem portu.  

    > [!NOTE]
    > Jeśli zmodyfikujesz tę `95-omsagent.conf`wartość w pliku konfiguracyjnym, zostanie ona zastąpiona, gdy agent zastosuje konfigurację domyślną.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Syslog-ng config powinien zostać zmodyfikowany przez skopiowanie przykładowej konfiguracji pokazanej poniżej i dodanie niestandardowych zmodyfikowanych ustawień na `/etc/syslog-ng/`końcu pliku konfiguracyjnego syslog-ng.conf znajdującego się w pliku . **Nie** należy używać etykiety domyślnej **%WORKSPACE_ID%_oms** lub **%WORKSPACE_ID_OMS**, zdefiniuj etykietę niestandardową, aby odróżnić zmiany.  

    > [!NOTE]
    > Jeśli zmodyfikujesz wartości domyślne w pliku konfiguracyjnym, zostaną one zastąpione, gdy agent zastosuje konfigurację domyślną.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

After completing the changes, the Syslog and the Log Analytics agent service needs to be restarted to ensure the configuration changes take effect.   

## <a name="syslog-record-properties"></a>Właściwości rekordu Syslog
Rekordy Syslog mają typ **Syslog** i mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, z których odebrano zdarzenie. |
| Obiektu |Definiuje część systemu, która wygenerowała wiadomość. |
| HostIP (HostIP) |Adres IP systemu wysyłającego wiadomość. |
| HostName |Nazwa systemu wysyłającego wiadomość. |
| WażnośćPoziom |Poziom ważności zdarzenia. |
| SyslogMessage (SyslogMessage) |Tekst wiadomości. |
| ProcessId |Identyfikator procesu, który wygenerował wiadomość. |
| Eventtime |Data i godzina wygenerowania zdarzenia. |

## <a name="log-queries-with-syslog-records"></a>Rejestrowanie zapytań z rekordami Syslog
Poniższa tabela zawiera różne przykłady zapytań dziennika, które pobierają rekordy Syslog.

| Zapytanie | Opis |
|:--- |:--- |
| Dziennik systemu |Wszystkie Syslogs. |
| Syslog &#124; gdzie SeverityLevel == "błąd" |Wszystkie rekordy Syslog z ważnością błędu. |
| Syslog &#124; podsumować AggregatedValue = count() przez komputer |Liczba rekordów Syslog według komputera. |
| Syslog &#124; podsumować AggregatedValue = count() według obiektu |Liczba rekordów Syslog według obiektu. |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](../../azure-monitor/log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.
* [Użyj pól niestandardowych,](../../azure-monitor/platform/custom-fields.md) aby przeanalizować dane z rekordów syslogu w poszczególnych polach.
* [Skonfiguruj agentów systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) do zbierania innych typów danych.
