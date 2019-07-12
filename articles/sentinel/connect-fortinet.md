---
title: Połącz dane Fortinet przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych Fortinet przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: f3ab4861e874074e7de059c7c50064d53749748c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611296"
---
# <a name="connect-your-fortinet-appliance"></a>Połącz urządzenie Fortinet

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług. Nie zalecamy jej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć przez wartownika platformy Azure na dowolnym urządzeniu Fortinet, zapisując pliki dziennika jako Syslog Common Event Format (CEF). Dzięki integracji z platformy Azure przez wartownika można łatwo uruchomić analizy i zapytań dla danych pliku dziennika z Fortinet. Aby uzyskać więcej informacji na temat sposobu przez wartownika Azure pozyskuje dane CEF, zobacz [CEF łączenie urządzeń](connect-common-event-format.md).

> [!NOTE]
> Dane są przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym jest uruchamiany przez wartownika platformy Azure.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Krok 1: Połącz urządzenie Fortinet przy użyciu agenta

Aby połączyć urządzenie Fortinet przez wartownika platformy Azure, wdrażanie agenta w dedykowanej maszynie Wirtualnej lub na komputerze lokalnym do obsługi komunikacji między urządzeniem i przez wartownika Azure. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępna tylko w przypadku dedykowanej maszynie nowej maszyny Wirtualnej tworzonej na platformie Azure.

Możesz również wdrożyć agenta ręcznie na istniejącej Maszynie wirtualnej platformy Azure, na maszynie Wirtualnej w innej chmurze lub na maszynie lokalnej.

Aby wyświetlić diagram sieciowy obie opcje, zobacz [połączyć źródeł danych](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Wdrażanie agenta w systemie Azure

1. W portalu Azure przez wartownika wybierz **łączników danych** i wybierz typ urządzenia.

1. W obszarze **konfiguracji agenta systemu Linux Syslog**:
   - Wybierz **wdrażania automatycznego** Jeśli chcesz utworzyć nową maszynę, która jest wstępnie z agentem Azure przez wartownika i obejmuje całą konfigurację to konieczne, jak opisano wcześniej. Wybierz **wdrażania automatycznego** > **wdrożenia agentami automatycznymi**. Zostanie wyświetlona strona zakupu dla dedykowanych maszynę Wirtualną, która jest automatycznie połączony z obszarem roboczym. Maszyna wirtualna jest **standardowa D2s v3 (2 procesorów wirtualnych Vcpu, 8 GB pamięci RAM)** i ma publiczny adres IP.
      1. Na **wdrożenie niestandardowe** strony, zapewniają szczegółowe informacje, wprowadź nazwę użytkownika i hasło, a jeśli wyrażasz zgodę na warunki i postanowienia, należy kupić maszyny Wirtualnej.
      1. Konfigurowanie urządzenia w taki sposób, aby wysłać dzienniki przy użyciu ustawień wymienionych na tej stronie połączenia. Łącznik ogólnego Common Event Format Użyj następujących ustawień:
         - Protokół = UDP
         - Port = 514
         - Funkcji = lokalne 4
         - Format = CEF
   - Wybierz **ręcznego wdrażania** Jeśli chcesz użyć istniejącej maszyny Wirtualnej jako dedykowane maszyny z systemem Linux, na którym jest zainstalowany agent usługi Azure przez wartownika. 
      1. W obszarze **pobrać i zainstalować agenta programu Syslog**, wybierz opcję **maszyny wirtualnej z systemem Linux platformy Azure**. 
      1. Na **maszyn wirtualnych** ekranu, wybierz maszynę, o których chcesz użyć, a następnie wybierz pozycję **Connect**.
      1. Na ekranie łącznika w obszarze **Konfiguruj i przekazywania usługi Syslog**Ustaw czy demona usługi Syslog jest **rsyslog.d** lub **demona syslog-ng**. 
      1. Skopiuj te polecenia i uruchamiać je na urządzeniu:
          - W przypadku wybrania rsyslog.d:
              
            1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do agenta przez wartownika platformy Azure przy użyciu portu 25226. Użyj tego polecenia: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. Użyj tego polecenia: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` gdzie {0} należy zamienić na GUID obszaru roboczego.
            1. Za pomocą tego polecenia, należy ponownie uruchomić demona dziennika systemu: `sudo service rsyslog restart`
             
          - W przypadku wybrania demona syslog-ng:

              1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do agenta przez wartownika platformy Azure przy użyciu portu 25226. Użyj tego polecenia: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. Użyj tego polecenia: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` gdzie {0} należy zamienić na GUID obszaru roboczego.
              1. Za pomocą tego polecenia, należy ponownie uruchomić demona dziennika systemu: `sudo service syslog-ng restart`
      1. Za pomocą tego polecenia, należy ponownie uruchomić agenta usługi Syslog: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Upewnij się, że nie ma żadnych błędów w dzienniku agenta, uruchamiając następujące polecenie: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Wdróż agenta na serwerze z systemem Linux w środowisku lokalnym

Jeśli nie używasz platformy Azure, ręcznie wdrożyć agenta przez wartownika platformy Azure, aby uruchomić na dedykowanym serwerze z systemem Linux.

1. W portalu Azure przez wartownika wybierz **łączników danych** i wybierz typ urządzenia.
1. Aby utworzyć dedykowane maszyny Wirtualnej systemu Linux w obszarze **konfiguracji agenta systemu Linux Syslog** wybierz **ręcznego wdrażania**.

    1. W obszarze **pobrać i zainstalować agenta programu Syslog**, wybierz opcję **maszyny z systemem Linux spoza platformy Azure**.
    1. W **agent bezpośredni** ekran, który zostanie otwarty, wybierz **agenta dla systemu Linux** Pobierz agenta lub uruchom następujące polecenie, aby ją pobrać na maszynie z systemem Linux: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. Na ekranie łącznika w obszarze **Konfiguruj i przekazywania usługi Syslog**Ustaw czy demona usługi Syslog jest **rsyslog.d** lub **demona syslog-ng**.
       1. Skopiuj te polecenia i uruchamiać je na urządzeniu:

          - W przypadku wybrania rsyslog:

            1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. Użyj tego polecenia: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. Użyj tego polecenia: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` gdzie {0} należy zamienić na GUID obszaru roboczego.
            1. Za pomocą tego polecenia, należy ponownie uruchomić demona dziennika systemu: `sudo service rsyslog restart`

          - W przypadku wybrania demona syslog-ng:

            1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do agenta przez wartownika platformy Azure przy użyciu portu 25226. Użyj tego polecenia: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. Użyj tego polecenia: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` gdzie {0} należy zamienić na GUID obszaru roboczego.
            1. Za pomocą tego polecenia, należy ponownie uruchomić demona dziennika systemu: `sudo service syslog-ng restart`

      1. Za pomocą tego polecenia, należy ponownie uruchomić agenta usługi Syslog: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Upewnij się, że nie ma żadnych błędów w dzienniku agenta, uruchamiając następujące polecenie: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Krok 2: Do przodu Fortinet dzienników do agenta usługi Syslog

Skonfiguruj Fortinet do przesyłania dalej komunikatów Syslog w formacie CEF do obszaru roboczego platformy Azure za pomocą agenta usługi Syslog.

1. Otwórz interfejs wiersza polecenia, w której znajduje się urządzenie Fortinet i uruchom następujące polecenia:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Zastąp serwera **adresu ip** o adresie IP agenta.
    - Ustaw **facility_name** można skorzystać z funkcji skonfigurowane w agencie. Domyślnie agent ustawia to local4.
    - Ustaw **syslog port** do **514** lub port ustawiony na agencie.
    - Aby włączyć format CEF w Wczesne wersje FortiOS, konieczne może być uruchamiania zestawu poleceń **Wyłącz csv**.
 
   > [!NOTE] 
   > Aby uzyskać więcej informacji, przejdź do [biblioteki dokumentów Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Wybierz wersję, a następnie użyć **podręcznik** i **dokumentacja komunikatów dziennika**.

 Aby użyć odpowiednich schematu w usłudze Azure Monitor Log Analytics dla zdarzeń Fortinet, wyszukaj `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Krok 3: Zweryfikuj łączność

Może potrwać do 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 

1. Upewnij się, że używasz prawo funkcji. Urządzenie to musi być takie same, w której znajduje się urządzenie i przez wartownika platformy Azure. Sprawdź, który plik funkcji jest używane w ramach platformy Azure przez wartownika i zmodyfikować go w pliku `security-config-omsagent.conf`. 

2. Upewnij się, że dzienniki pojawiają się na prawy port w agencie programu Syslog. Uruchom następujące polecenie na komputerze agenta usługi Syslog: `tcpdump -A -ni any  port 514 -vv`. To polecenie wyświetla dzienniki przesyłania strumieniowego z urządzenia do maszyny Syslog. Upewnij się, że dzienniki są otrzymywane z urządzenia źródłowego na prawy port i odpowiednie funkcji.

3. Upewnij się, że dzienniki wysyłane są zgodne z [RFC 5424](https://tools.ietf.org/html/rfc542).

4. Na komputerze z agentem usługi Syslog, upewnij się, porty 514 i 25226 są otwarte i czy działa nasłuchiwanie przy użyciu polecenia `netstat -a -n:`. Aby uzyskać więcej informacji na temat korzystania z tego polecenia zobacz [netstat(8) - strony ataków typu man Linux](https://linux.die.net/man/8/netstat). Nasłuchuje prawidłowo, zobacz:

   ![Usługa Azure porty wartownik](./media/connect-cef/ports.png) 

5. Upewnij się, że demon jest ustawiony do nasłuchiwania na porcie 514, na którym one wysyłać dzienniki.
    - Demona rsyslog:<br>Upewnij się, że plik `/etc/rsyslog.conf` ta konfiguracja obejmuje:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Aby uzyskać więcej informacji, zobacz [imudp: Moduł wejściowy UDP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) i [imtcp: Moduł wejściowy TCP, Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Demona syslog-NG:<br>Upewnij się, że plik `/etc/syslog-ng/syslog-ng.conf` ta konfiguracja obejmuje:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Aby uzyskać więcej informacji, zobacz [imudp: Moduł wejściowy UDP Syslog](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) i [wersji "Open source" demona syslog-ng 3.16 — Przewodnik administrowania](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Sprawdź, czy komunikacja między demona usługi Syslog i agenta. Uruchom następujące polecenie na komputerze agenta usługi Syslog: `tcpdump -A -ni any  port 25226 -vv`. To polecenie wyświetla dzienniki przesyłania strumieniowego z urządzenia do maszyny Syslog. Upewnij się, że dzienniki są one odbierane w agencie.

6. Jeśli oba te polecenia pomyślne wyniki, sprawdź usługi Log Analytics, aby zobaczyć, jeśli dzienniki są odbierane. Wszystkie zdarzenia przesyłane strumieniowo ze te urządzenia są wyświetlane w nieprzetworzonej postaci, w usłudze Log Analytics w obszarze `CommonSecurityLog` typu.

7. Aby sprawdzić, czy istnieją błędy, lub jeśli dzienniki nie są przychodzących, poszukaj `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Jeśli występują błędy niezgodności format dziennika z nazwą, przejdź do strony `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` i przyjrzyj się plik `security_events.conf`. Upewnij się, że dzienniki zgodny z formatem wyrażeń regularnych, widocznej w tym pliku.

8. Upewnij się, że rozmiar domyślny wiadomości usługi Syslog wynosi 2048 bajtów (2 KB). Jeśli dzienniki są zbyt długie, należy zaktualizować security_events.conf za pomocą tego polecenia: `message_length_limit 4096`

10. Jeśli dzienniki Fortinet nie są odbierane przez agenta, uruchom następujące polecenie, w zależności od tego, jakiego typu demona usługi Syslog korzystasz, funkcji i ustawić dzienniki aby wyszukać słowo Fortinet w dziennikach:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Za pomocą tego polecenia, należy ponownie uruchomić demona dziennika systemu: `sudo service rsyslog restart`
       - Demona SYSLOG-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Za pomocą tego polecenia, należy ponownie uruchomić demona dziennika systemu: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób łączenia urządzeń Fortinet do platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

