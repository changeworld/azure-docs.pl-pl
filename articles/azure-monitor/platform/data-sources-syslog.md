---
title: Zbieraj i Analizuj komunikaty dziennika systemu w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: SYSLOG jest protokołem rejestrowania zdarzeń, który jest wspólny dla systemu Linux. W tym artykule opisano sposób konfigurowania kolekcji komunikaty dziennika systemowego w usłudze Log Analytics i szczegóły rekordów, które tworzą.
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
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 41ea6222689516f224fc23ce6a658d17f7f81866
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240847"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>SYSLOG źródeł danych w usłudze Azure Monitor
SYSLOG jest protokołem rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będzie wysyłać komunikaty, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego Syslog. Po zainstalowaniu agenta usługi Log Analytics dla systemu Linux umożliwia skonfigurowanie lokalnego demona usługi Syslog do przekazywania komunikatów do agenta. Agent wysyła następnie komunikatu do usługi Azure Monitor, gdy odpowiedni rekord zostanie utworzony.  

> [!NOTE]
> Usługa Azure Monitor obsługują zbieranie komunikatów wysyłanych przez rsyslog lub syslog-ng, gdzie rsyslog jest demona domyślne. Demon syslog domyślne w wersji 5 (sysklog) w wersji w systemie Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń dziennika systemu. Aby zebrać dane usługi syslog z tej wersji tych dystrybucji [demona rsyslog](http://rsyslog.com) powinien być zainstalowany i skonfigurowany tak, aby zastąpić sysklog.
>
>

![Zbieranie dzienników systemowych](media/data-sources-syslog/overview.png)

Następujące obiekty są obsługiwane przez moduł Syslog:

* Kerning
* Użytkownik
* poczta
* Demon
* uwierzytelnianie
* syslog
* lpr
* wiadomości
* UUCP
* usługi cron
* authpriv
* ftp
* local0 local7

Dla dowolnego obiektu [Konfigurowanie źródła danych dzienników niestandardowych](data-sources-custom-logs.md) w usłudze Azure Monitor.
 
## <a name="configuring-syslog"></a>Konfigurowanie usługi Syslog
Agenta usługi Log Analytics dla systemu Linux tylko będzie zbierać zdarzenia z urządzeń i ważności, które są określone w konfiguracji. Syslog można skonfigurować za pomocą witryny Azure portal lub zarządzając pliki konfiguracji agentów systemu Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurowanie usługi Syslog w witrynie Azure portal
Konfigurowanie usługi Syslog z [menu danych w zaawansowanych ustawieniach](agent-data-sources.md#configuring-data-sources). Ta konfiguracja jest dostarczana do pliku konfiguracji na każdym agencie systemu Linux.

Można dodawać nowych funkcji, wpisując jej nazwę, a następnie klikając polecenie **+** . Dla każdego obiektu zostanie pobrana tylko komunikaty z wybranej ważności.  Zaznacz ważności dla określonego obiektu, które mają być zbierane. Nie zawiera żadnych dodatkowych kryteriów filtrowania wiadomości.

![Konfigurowanie usługi Syslog](media/data-sources-syslog/configure.png)

Domyślnie wszystkie zmiany konfiguracji są automatycznie przekazywane do wszystkich agentów. Jeśli chcesz skonfigurować dziennik systemowy ręcznie na każdym agencie systemu Linux, usuń zaznaczenie pola *Zastosuj poniższą konfigurację do moich maszyn z systemem Linux*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurowanie usługi Syslog na agenta systemu Linux
Gdy [usługi Log Analytics agent jest zainstalowany na komputerze klienckim systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), program domyślny plik konfiguracji usługi syslog do definiowania funkcji i ważność komunikatów, które są zbierane. Można zmodyfikować tego pliku, aby zmienić konfigurację. Plik konfiguracyjny różni się w zależności od demona dziennika systemu, który klient został zainstalowany.

> [!NOTE]
> Jeśli edytujesz konfigurację dziennika systemu, należy ponownie uruchomić demona dziennika systemu, aby zmiany zaczęły obowiązywać.
>
>

#### <a name="rsyslog"></a>rsyslog
Plik konfiguracji dla usługi rsyslog znajduje się w **/etc/rsyslog.d/95-omsagent.conf**. Poniżej przedstawiono domyślną zawartość. Gromadzi informacje o komunikaty dziennika systemowego wysyłany przez agenta lokalnego dla wszystkich urządzeń z poziomu, ostrzeżenie lub nowszej.

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
Plik konfiguracji demona syslog-ng jest lokalizacji **/etc/syslog-ng/syslog-ng.conf**.  Poniżej przedstawiono domyślną zawartość. Gromadzi informacje o komunikaty dziennika systemu wysłanych z lokalnego agenta dla wszystkich urządzeń i wszystkich stopni ważności.   

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
Agenta usługi Log Analytics wykrywa komunikaty dziennika systemu na lokalnym kliencie, na porcie 25224.  Po zainstalowaniu agenta na domyślnej konfiguracji usługi syslog zostaną zastosowane i znaleźć w następującej lokalizacji:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Demona SYSLOG-ng: `/etc/syslog-ng/syslog-ng.conf`

Możesz zmienić numer portu, tworząc dwa pliki konfiguracji: plik konfiguracji FluentD i plik ng rsyslog lub syslog, w zależności od demona usługi Syslog został zainstalowany.  

* Plik konfiguracji FluentD powinien być nowy plik znajdujący się w: `/etc/opt/microsoft/omsagent/conf/omsagent.d` i zastąp wartość symbolu w **portu** wpis, za pomocą Twojego niestandardowego numer portu.

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

* Dla usługi rsyslog, należy utworzyć plik konfiguracji na terenie: `/etc/rsyslog.d/` i zastąp wartość % SYSLOG_PORT numer portu niestandardowego.  

    > [!NOTE]
    > Jeśli zmodyfikujesz tę wartość w pliku konfiguracyjnym `95-omsagent.conf`, zostaną zastąpione, gdy agent nie ma zastosowania konfiguracji domyślnej.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Config demona syslog-ng powinny być modyfikowane przez skopiowanie Przykładowa konfiguracja pokazany poniżej i dodawanie modyfikacji ustawień niestandardowych do końca pliku konfiguracji usługi syslog ng.conf znajduje się w `/etc/syslog-ng/`. Czy **nie** Użyj etykiety domyślnej **% WORKSPACE_ID % _oms** lub **% WORKSPACE_ID_OMS**, zdefiniuj ułatwia odróżnienie zmiany etykiety niestandardowej.  

    > [!NOTE]
    > W przypadku zmodyfikowania wartości domyślne w pliku konfiguracji zostaną zastąpione podczas agent ma zastosowanie konfiguracji domyślnej.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po zakończeniu zmiany, Syslog i Log Analytics, usługi agenta musi zostać ponownie uruchomione, aby upewnić się, zmiany konfiguracji zostały wprowadzone.   

## <a name="syslog-record-properties"></a>Właściwości rekordu dziennika systemu
Rekordy SYSLOG mają typ **Syslog** i mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, który zostały zebrane zdarzenia. |
| Funkcji |Definiuje części systemu, który wygenerował komunikat. |
| HostIP |Adres IP systemu wysyłania komunikatu. |
| Nazwa hosta |Nazwa systemu wysyłania komunikatu. |
| SeverityLevel |Poziom ważności zdarzenia. |
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

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rejestrowania zapytań](../../azure-monitor/log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.
* Użyj [pól niestandardowych](../../azure-monitor/platform/custom-fields.md) do analizowania danych z rekordy syslog na poszczególne pola.
* [Konfigurowanie agentów systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) do gromadzenia innych typów danych.
