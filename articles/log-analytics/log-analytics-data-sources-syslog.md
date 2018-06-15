---
title: Zbieranie i analizowanie komunikaty dziennika systemowego w OMS Log Analytics | Dokumentacja firmy Microsoft
description: SYSLOG to protokół rejestrowania zdarzeń jest wspólny dla systemu Linux. W tym artykule opisano sposób konfigurowania kolekcji komunikaty dziennika systemowego w analizy dzienników i szczegóły rekordów tworzonych w repozytorium OMS.
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
ms.date: 09/28/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 011eaf1a4705f9078225b9b871f81b4333b05ee8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011098"
---
# <a name="syslog-data-sources-in-log-analytics"></a>SYSLOG źródeł danych w analizy dzienników
SYSLOG to protokół rejestrowania zdarzeń jest wspólny dla systemu Linux.  Aplikacje będą wysyłać wiadomości, które mogą być przechowywane na komputerze lokalnym lub dostarczone do modułu zbierającego Syslog.  Po zainstalowaniu Agent pakietu OMS dla systemu Linux, konfiguruje lokalnego demon Syslog do przekazywania wiadomości do agenta.  Agent wysyła następnie komunikat do analizy dzienników, w którym odpowiedni rekord jest tworzony w repozytorium OMS.  

> [!NOTE]
> Analiza dzienników obsługuje kolekcji komunikatów wysłanych przez rsyslog lub syslog ng, gdzie rsyslog jest demona domyślne. Demon syslog domyślne w wersji 5 (sysklog) w wersji Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń usługi syslog. Do zbierania danych z serwera syslog z tej wersji tych dystrybucji [demon rsyslog](http://rsyslog.com) powinna być zainstalowana i skonfigurowana zastąpić sysklog.
>
>

![Kolekcja SYSLOG](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Konfigurowanie usługi Syslog
Agent pakietu OMS dla systemu Linux są zbierane zdarzenia z urządzeń i ważności, które są określone w konfiguracji.  Syslog można skonfigurować za pośrednictwem portalu Azure lub przez zarządzanie plikami konfiguracji na agentów systemu Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Skonfigurować dziennik systemowy w portalu Azure
Skonfigurować dziennik systemowy z [menu dane w dzienniku Analytics Zaawansowane ustawienia](log-analytics-data-sources.md#configuring-data-sources).  Ta konfiguracja jest dostarczany do pliku konfiguracji na każdym agenta systemu Linux.

Można dodać nowego zakładu, wpisując jej nazwę, a następnie klikając polecenie **+**.  Dla każdego obiektu zostaną zebrane tylko komunikatów dla wybranej ważności.  Sprawdzanie ważności dla konkretnego obiektu, który chcesz zebrać.  Nie można podać wszelkie dodatkowe kryteria filtrowania wiadomości.

![Konfigurowanie usługi Syslog](media/log-analytics-data-sources-syslog/configure.png)

Domyślnie wszystkie zmiany konfiguracji są automatycznie przypisany do wszystkich agentów.  Jeśli chcesz ręcznie skonfigurować Syslog dla każdego agenta systemu Linux, usuń zaznaczenie pola *Zastosuj poniższą konfigurację na moich maszynach z systemem Linux*.

### <a name="configure-syslog-on-linux-agent"></a>Skonfiguruj Syslog na agenta systemu Linux
Gdy [OMS agent jest zainstalowany na komputerze klienckim Linux](log-analytics-linux-agents.md), instaluje domyślny plik konfiguracji programu syslog definiujący funkcje i ważność komunikatów, które są zbierane.  Można modyfikować tego pliku, aby zmienić konfigurację.  Plik konfiguracji jest różne w zależności od demon Syslog, które klient został zainstalowany.

> [!NOTE]
> Po zmodyfikowaniu konfiguracji programu syslog, należy ponownie uruchomić demona syslog, aby zmiany zaczęły obowiązywać.
>
>

#### <a name="rsyslog"></a>rsyslog
Plik konfiguracji rsyslog znajduje się w **/etc/rsyslog.d/95-omsagent.conf**.  Poniżej przedstawiono domyślną zawartość.  Umożliwia zbieranie informacji wysyłanych z lokalnego agenta dla wszystkich obiektów o poziomie ostrzeżenia lub komunikaty dziennika systemowego.

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

Należy usunąć obiekt przez usunięcie jego sekcji pliku konfiguracji.  Można ograniczyć wag, które są zbierane dla konkretnego obiektu przez zmodyfikowanie wpisu tego obiektu.  Na przykład aby ograniczyć funkcji użytkownika do wiadomości o ważności błąd lub należy zmodyfikować tego wiersza do następującego pliku konfiguracji:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>SYSLOG ng
Plik konfiguracji do syslog ng jest lokalizacji **/etc/syslog-ng/syslog-ng.conf**.  Poniżej przedstawiono domyślną zawartość.  Umożliwia zbieranie informacji wysyłanych z lokalnego agenta dla wszystkich urządzeń i wszystkich wag komunikaty dziennika systemowego.   

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

Należy usunąć obiekt przez usunięcie jego sekcji pliku konfiguracji.  Można ograniczyć wag, które są zbierane dla konkretnego obiektu, usuwając je z listy.  Na przykład aby ograniczyć możliwość użytkownik po prostu alertów i krytycznych wiadomości, można zmodyfikować tej sekcji pliku konfiguracji do następującego:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Zbieranie danych z dodatkowych portów usługi Syslog
Agent pakietu OMS odbiera komunikaty dziennika systemowego na lokalnym kliencie na porcie 25224.  Po zainstalowaniu agenta domyślnej konfiguracji programu syslog jest stosowane i znaleźć w następującej lokalizacji:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* SYSLOG ng: `/etc/syslog-ng/syslog-ng.conf`

Można zmienić numer portu przez utworzenie dwóch plików konfiguracyjnych: FluentD pliku konfiguracji i pliku ng rsyslog lub syslog, w zależności od demon Syslog został zainstalowany.  

* Plik konfiguracji FluentD powinien być nowy plik znajduje się w: `/etc/opt/microsoft/omsagent/conf/omsagent.d` i Zastąp wartości w **portu** wpis o numer portu niestandardowego.

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

* Rsyslog, należy utworzyć plik konfiguracji znajduje się w: `/etc/rsyslog.d/` i Zastąp wartości % SYSLOG_PORT % numer portu niestandardowego.  

    > [!NOTE]
    > Jeśli zmodyfikujesz tę wartość w pliku konfiguracyjnym `95-omsagent.conf`, zostaną zastąpione, gdy agent stosuje konfigurację domyślną.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Konfiguracji usługi syslog ng powinno zostać zmodyfikowane przez skopiowanie konfiguracji przykładzie pokazano poniżej i dodawanie zmodyfikowane ustawienia niestandardowe na końcu pliku konfiguracji programu syslog ng.conf znajduje się w `/etc/syslog-ng/`.  Czy **nie** Użyj etykiety domyślnej **% WORKSPACE_ID % _oms** lub **% WORKSPACE_ID_OMS**, zdefiniuj ułatwia odróżnienie zmiany etykiety niestandardowej.  

    > [!NOTE]
    > Zmodyfikowanie wartości domyślnych w pliku konfiguracji, zostaną one zastąpione, gdy agent stosuje konfigurację domyślną.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po zakończeniu zmiany, Syslog i agent pakietu OMS usługi musi zostać uruchomiony ponownie, aby upewnić się, że zmiany konfiguracji zostały wprowadzone.   

## <a name="syslog-record-properties"></a>Właściwości rekordu dziennika systemowego
Rekordy dziennika systemowego zawiera typu **Syslog** i mieć właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, który zostały zebrane zdarzenia. |
| Funkcje |Definiuje część systemu, który wygenerował komunikat. |
| HostIP |Adres IP systemu wysyłania wiadomości. |
| Nazwa hosta |Nazwa systemu wysyłania wiadomości. |
| Poziom ważności |Poziom ważności zdarzenia. |
| SyslogMessage |Tekst komunikatu. |
| Identyfikator procesu |Identyfikator procesu, który wygenerował komunikat. |
| eventTime |Data i godzina wygenerowania zdarzenia. |

## <a name="log-queries-with-syslog-records"></a>Dziennik zapytań dotyczących rekordów dziennika systemowego
Poniższa tabela zawiera przykłady różnych dziennika zapytań, które pobierają rekordy dziennika systemowego.

| Zapytanie | Opis |
|:--- |:--- |
| Dziennik systemu |Wszystkie audyt dzienników systemowych. |
| SYSLOG &#124; gdzie poziom ważności == "error" |Wszystkie rekordy dziennika systemowego o ważności błędu. |
| SYSLOG &#124; Podsumuj AggregatedValue = count() przez komputer |Liczba Syslog rejestruje przez komputer. |
| SYSLOG &#124; Podsumuj AggregatedValue = count() przez funkcje |Liczba Syslog rejestruje przez funkcje. |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania.
* Użyj [pola niestandardowe](log-analytics-custom-fields.md) do analizowania danych z rekordów dziennika systemowego do poszczególnych pól.
* [Konfigurowanie agentów systemu Linux](log-analytics-linux-agents.md) służąca do gromadzenia innych typów danych.
