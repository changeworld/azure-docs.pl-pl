---
title: Zbieranie i analizowanie komunikatów dziennika systemowego w Azure Monitor | Microsoft Docs
description: SYSLOG jest protokołem rejestrowania zdarzeń, który jest wspólny dla systemu Linux. W tym artykule opisano sposób konfigurowania kolekcji komunikatów dziennika systemu w Log Analytics i szczegóły dotyczące tworzonych przez siebie rekordów.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274725"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Źródła danych dziennika systemowego w Azure Monitor
SYSLOG jest protokołem rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będzie wysyłać komunikaty, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego Syslog. Po zainstalowaniu agenta Log Analytics dla systemu Linux program skonfiguruje lokalny demon dziennika systemowego, aby przekazywać komunikaty do agenta. Następnie Agent wysyła komunikat do Azure Monitor, w którym zostanie utworzony odpowiedni rekord.  

> [!NOTE]
> Azure Monitor obsługuje zbieranie komunikatów wysyłanych przez rsyslog lub Dziennik systemowy, gdzie rsyslog jest demonem domyślnym. Demon syslog domyślne w wersji 5 (sysklog) w wersji w systemie Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń dziennika systemu. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, [demona rsyslog](http://rsyslog.com) powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.
>
>

![Zbieranie dzienników systemowych](media/data-sources-syslog/overview.png)

W module zbierającym dziennik systemu są obsługiwane następujące obiekty:

* Kerning
* Użytkownik
* mail (poczta)
* Demon
* uwierzytelniania
* syslog
* usługę
* Usenet
* uucp
* zaczyna
* authpriv
* zwrócił
* local0-local7

W przypadku każdej innej funkcji [Skonfiguruj niestandardowe źródło danych dzienników](data-sources-custom-logs.md) w Azure monitor.
 
## <a name="configuring-syslog"></a>Konfigurowanie usługi Syslog
Agent Log Analytics dla systemu Linux będzie zbierać tylko zdarzenia z obiektami i serwerami, które są określone w jego konfiguracji. Syslog można skonfigurować za pomocą witryny Azure portal lub zarządzając pliki konfiguracji agentów systemu Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurowanie usługi Syslog w witrynie Azure portal
Skonfiguruj dziennik systemowy z poziomu [menu dane w oknie Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources). Ta konfiguracja jest dostarczana do pliku konfiguracji na każdym agencie systemu Linux.

Aby dodać nową funkcję, należy najpierw wybrać opcję **Zastosuj poniższą konfigurację do moich maszyn** , a następnie wpisać nazwę i kliknąć przycisk **+** . Dla każdego obiektu zostanie pobrana tylko komunikaty z wybranej ważności.  Zaznacz ważności dla określonego obiektu, które mają być zbierane. Nie zawiera żadnych dodatkowych kryteriów filtrowania wiadomości.

![Konfigurowanie usługi Syslog](media/data-sources-syslog/configure.png)

Domyślnie wszystkie zmiany konfiguracji są automatycznie przekazywane do wszystkich agentów. Jeśli chcesz ręcznie skonfigurować dziennik systemu na każdym agencie systemu Linux, usuń zaznaczenie pola *Zastosuj poniżej konfiguracji do moich maszyn*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurowanie usługi Syslog na agenta systemu Linux
Po [zainstalowaniu agenta log Analytics na kliencie z systemem Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)instalowany jest domyślny plik konfiguracji dziennika systemowego, który określa zakres i ważność komunikatów, które są zbierane. Można zmodyfikować tego pliku, aby zmienić konfigurację. Plik konfiguracyjny różni się w zależności od demona dziennika systemu, który klient został zainstalowany.

> [!NOTE]
> Jeśli edytujesz konfigurację dziennika systemu, należy ponownie uruchomić demona dziennika systemu, aby zmiany zaczęły obowiązywać.
>
>

#### <a name="rsyslog"></a>rsyslog
Plik konfiguracji rsyslog znajduje się w lokalizacji **/etc/rsyslog.d/95-omsagent.conf**. Poniżej przedstawiono domyślną zawartość. Gromadzi informacje o komunikaty dziennika systemowego wysyłany przez agenta lokalnego dla wszystkich urządzeń z poziomu, ostrzeżenie lub nowszej.

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

Obiekt można usunąć przez usunięcie jego sekcję pliku konfiguracji. Można ograniczyć ważności, które są zbierane dla konkretnego obiektu przez zmodyfikowanie wpisu tej funkcji. Na przykład aby ograniczyć funkcji użytkownika do wiadomości o ważności błędu lub należy zmodyfikować tego wiersza w pliku konfiguracji do następujących:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>demona SYSLOG-ng
Plik konfiguracji dla dziennika systemowego jest lokalizacją o godzinie **/etc/syslog-ng/syslog-ng.conf**.  Poniżej przedstawiono domyślną zawartość. Gromadzi informacje o komunikaty dziennika systemu wysłanych z lokalnego agenta dla wszystkich urządzeń i wszystkich stopni ważności.   

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

Obiekt można usunąć przez usunięcie jego sekcję pliku konfiguracji. Można ograniczyć ważności, które są zbierane dla konkretnego pomieszczenia, usuwając je z listy.  Na przykład aby ograniczyć możliwości użytkowników komunikaty po prostu alertów i krytycznych, należy zmodyfikować tę sekcję pliku konfiguracji do następujących:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Zbieranie danych z dodatkowych portów usługi Syslog
Agent Log Analytics nasłuchuje komunikatów dziennika systemowego na kliencie lokalnym na porcie 25224.  Po zainstalowaniu agenta na domyślnej konfiguracji usługi syslog zostaną zastosowane i znaleźć w następującej lokalizacji:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Dziennik systemowy — NG: `/etc/syslog-ng/syslog-ng.conf`

Możesz zmienić numer portu, tworząc dwa pliki konfiguracji: plik konfiguracji FluentD i plik ng rsyslog lub syslog, w zależności od demona usługi Syslog został zainstalowany.  

* Plik konfiguracji, który został pozostały, powinien być nowym plikiem znajdującym się w: `/etc/opt/microsoft/omsagent/conf/omsagent.d` i Zastąp wartość w wpisie **portu** numerem portu niestandardowego.

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

* W przypadku rsyslog należy utworzyć nowy plik konfiguracji znajdujący się w: `/etc/rsyslog.d/` i zastąpić wartość% SYSLOG_PORT% numerem portu niestandardowego.  

    > [!NOTE]
    > Jeśli zmodyfikujesz tę wartość w `95-omsagent.conf`pliku konfiguracji, zostanie ona nadpisywana, gdy Agent zastosuje konfigurację domyślną.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Konfigurację dziennika systemowego-NG należy zmodyfikować przez skopiowanie przykładowej konfiguracji pokazanej poniżej i dodanie niestandardowych ustawień zmodyfikowanych na końcu pliku konfiguracji dziennika systemowego — ng. conf znajdującego się w `/etc/syslog-ng/`. **Nie** Używaj domyślnej etykiety **% WORKSPACE_ID% _oms** lub **% WORKSPACE_ID_OMS**, zdefiniuj etykietę niestandardową, aby ułatwić odróżnienie zmian.  

    > [!NOTE]
    > W przypadku zmodyfikowania wartości domyślne w pliku konfiguracji zostaną zastąpione podczas agent ma zastosowanie konfiguracji domyślnej.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po wprowadzeniu zmian należy ponownie uruchomić dziennik systemowy i usługę agenta Log Analytics, aby upewnić się, że zmiany konfiguracji zostały wprowadzone.   

## <a name="syslog-record-properties"></a>Właściwości rekordu dziennika systemu
Rekordy dziennika systemu mają typ **dziennika** systemowego i mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer |Komputer, który zostały zebrane zdarzenia. |
| Funkcja |Definiuje części systemu, który wygenerował komunikat. |
| HostIP |Adres IP systemu wysyłania komunikatu. |
| Nazwa hosta |Nazwa systemu wysyłania komunikatu. |
| Poziom ważności |Poziom ważności zdarzenia. |
| SyslogMessage |Tekst komunikatu. |
| ProcessId |Identyfikator procesu, który wygenerował komunikat. |
| eventTime |Data i godzina wygenerowania zdarzenia. |

## <a name="log-queries-with-syslog-records"></a>Dziennik zapytań przy użyciu rekordy Syslog
Poniższa tabela zawiera przykłady różnych zapytań dziennika, które pobierają rekordy Syslog.

| Zapytanie | Opis |
|:--- |:--- |
| Dziennik systemu |Wszystkie dzienniki SYSLOG. |
| SYSLOG &#124; gdzie SeverityLevel == "error" |Wszystkie rekordy Syslog z ważność błędu. |
| SYSLOG &#124; summarize AggregatedValue = count() by komputera |Liczba Syslog rejestruje przez komputer. |
| SYSLOG &#124; summarize AggregatedValue = count() by funkcji |Liczba Syslog rekordy według funkcji. |

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](../../azure-monitor/log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.
* [Pola niestandardowe](../../azure-monitor/platform/custom-fields.md) służą do analizowania danych z rekordów dziennika systemowego do poszczególnych pól.
* [Skonfiguruj agentów systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) do zbierania innych typów danych.
