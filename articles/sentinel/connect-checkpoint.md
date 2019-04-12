---
title: Połącz dane Check Point przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych Check Point przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 664b09a8ad0cb7d06019281869e390a465637c00
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489180"
---
# <a name="connect-your-check-point-appliance"></a>Połącz urządzenie Check Point

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć przez wartownika platformy Azure na dowolnym urządzeniu Check Point zapisując pliki dziennika w formacie Syslog CEF. Integracja z platformy Azure przez wartownika pozwala łatwo uruchamiać analizy i zapytań dla danych pliku dziennika z Check Point. Aby uzyskać więcej informacji na temat sposobu przez wartownika Azure pozyskuje dane CEF, zobacz [CEF łączenie urządzeń](connect-common-event-format.md).

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>Krok 1: Połącz urządzenie Check Point, przy użyciu agenta

Aby połączyć urządzenie Check Point przez wartownika platformy Azure, musisz wdrożyć agenta na dedykowanej maszynie (maszyna wirtualna lub lokalnej) do obsługi komunikacji między urządzeniem i platformy Azure przez wartownika. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępna tylko w przypadku dedykowanej maszynie nową maszynę Wirtualną, tworzysz na platformie Azure. 

Alternatywnie można wdrożyć agenta ręcznie na istniejącej Maszynie wirtualnej platformy Azure, na maszynie Wirtualnej w innej chmurze lub na maszynie lokalnej.

Aby wyświetlić diagram sieciowy obie opcje, zobacz [połączyć źródeł danych](connect-data-sources.md).

### <a name="deploy-the-agent-in-azure"></a>Wdrażanie agenta w systemie Azure

1. W portalu Azure przez wartownika kliknij **łączników danych** i wybierz typ urządzenia. 

1. W obszarze **konfiguracji agenta systemu Linux Syslog**:
   - Wybierz **wdrażania automatycznego** Jeśli chcesz utworzyć nową maszynę, jest wstępnie zainstalowany za pomocą agenta usługi Azure przez wartownika, która obejmuje wszystkie niezbędne konfiguracji zgodnie z powyższym opisem. Wybierz **wdrażania automatycznego** i kliknij przycisk **wdrożenia agentami automatycznymi**. Spowoduje to przejście do strony zakupu dla dedykowanych maszynę Wirtualną, która jest automatycznie połączony z obszarem roboczym. Maszyna wirtualna jest **standardowa D2s v3 (2 procesorów wirtualnych vcpu, 8 GB pamięci RAM)** i ma publiczny adres IP.
     1. W **wdrożenie niestandardowe** strony, zapewniają szczegółowe informacje i wybierz nazwę użytkownika i hasło i jeśli zgadzasz się na warunki i postanowienia, zakup maszyny Wirtualnej.
      
        1. Uruchom następujące polecenia na maszynie agenta usługi Syslog, aby upewnić się, że wszystkie dzienniki Check Point zostaną zmapowane do platformy Azure przez wartownika agenta:
           - Jeśli używasz demona Syslog-ng, uruchom następujące polecenia (Uwaga jej ponownym uruchomieniu agenta programu Syslog):
            
                "sudo" bash - c "printf" filtrowanie f_local4_oms {facility(local4);}; \ security_oms n docelowego {tcp (\"127.0.0.1\" port(25226));}; \n dziennika {source(src); filter(f_local4_oms) destination(security_oms);}; \n\nfilter f_msg_oms {dopasowania (\"Check Point\" wartość (\" KOMUNIKAT\")); }; security_msg_oms docelowego \n {tcp (\"127.0.0.1\" port(25226));}; \n dziennika {source(src); filter(f_msg_oms) destination(security_msg_oms);}; "> /etc/syslog-ng/security-config-omsagent.conf"

             Uruchom ponownie demona usługi Syslog: `sudo service syslog-ng restart`
           - Jeśli używasz rsyslog, uruchom następujące polecenia (Uwaga jej ponownym uruchomieniu agenta programu Syslog):
                    
                 sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
             Uruchom ponownie demona usługi Syslog: `sudo service rsyslog restart`

   - Wybierz **ręcznego wdrażania** Jeśli chcesz użyć istniejącej maszyny Wirtualnej jako dedykowane maszyny z systemem Linux, na którym powinien być zainstalowany agent usługi Azure przez wartownika. 
      1. W obszarze **pobrać i zainstalować agenta programu Syslog**, wybierz opcję **maszyny wirtualnej z systemem Linux platformy Azure**. 
      1. W **maszyn wirtualnych** ekran, który zostanie otwarty, wybierz maszynę, o których chcesz użyć, a następnie kliknij przycisk **Connect**.
      1. Na ekranie łącznika w obszarze **Konfiguruj i przekazywania usługi Syslog**Ustaw czy demona usługi Syslog jest **rsyslog.d** lub **demona syslog-ng**. 
      1. Skopiuj te polecenia i uruchamiać je na urządzeniu:
          - W przypadku wybrania rsyslog.d:
              
            1. Poinformuj demona dziennika systemu do nasłuchiwania na local_4 funkcji i "Check Point" i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.
           
            1. Uruchom ponownie demona usługi syslog `sudo service rsyslog restart`
             
          - W przypadku wybrania demona syslog-ng:

              1. Poinformuj demona dziennika systemu do nasłuchiwania na local_4 funkcji i "Check Point" i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.

              3. Uruchom ponownie demona usługi syslog `sudo service syslog-ng restart`
      2. Uruchom ponownie agenta usługi Syslog za pomocą tego polecenia: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Upewnij się, że nie ma żadnych błędów w dzienniku agenta, uruchamiając następujące polecenie: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Wdróż agenta na lokalny serwer systemu Linux

Jeśli nie używasz platformy Azure, ręcznie wdrożyć agenta przez wartownika platformy Azure, aby uruchomić na dedykowanym serwerze z systemem Linux.

1. Aby utworzyć dedykowane maszyny Wirtualnej systemu Linux w obszarze **konfiguracji agenta systemu Linux Syslog** wybierz **ręcznego wdrażania**.
   1. W obszarze **pobrać i zainstalować agenta programu Syslog**, wybierz opcję **maszyny z systemem Linux spoza platformy Azure**. 
   1. W **agent bezpośredni** ekran, który zostanie otwarty, wybierz **agenta dla systemu Linux** Pobierz agenta lub uruchom następujące polecenie, aby ją pobrać na maszynie z systemem Linux:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Na ekranie łącznika w obszarze **Konfiguruj i przekazywania usługi Syslog**Ustaw czy demona usługi Syslog jest **rsyslog.d** lub **demona syslog-ng**. 
      1. Skopiuj te polecenia i uruchamiać je na urządzeniu:
         - W przypadku wybrania **rsyslog**:
           1. Poinformuj demona dziennika systemu do nasłuchiwania na local_4 funkcji i "Check Point" i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.
           3. Uruchom ponownie demona usługi syslog `sudo service rsyslog restart`
         - W przypadku wybrania **demona syslog-ng**:
            1. Poinformuj demona dziennika systemu do nasłuchiwania na local_4 funkcji i "Check Point" i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.
            3. Uruchom ponownie demona usługi syslog `sudo service syslog-ng restart`
      1. Uruchom ponownie agenta usługi Syslog za pomocą tego polecenia: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Upewnij się, że nie ma żadnych błędów w dzienniku agenta, uruchamiając następujące polecenie: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>Krok 2: Dzienniki agenta usługi Syslog do przodu Check Point

Skonfiguruj urządzenie Check Point do przesyłania dalej komunikatów Syslog w formacie CEF do obszaru roboczego platformy Azure za pomocą agenta usługi Syslog.

1. Przejdź do [Sprawdź eksportu dziennika punktu](https://aka.ms/asi-syslog-checkpoint-forwarding).
2. Przewiń w dół do **podstawowego wdrożenia** i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie, korzystając z następujących wskazówek:
   - Ustaw **Syslog port** do **514** lub port ustawiony na agencie.
     - Zastąp **nazwa** i **adres IP serwera docelowego** w interfejsie wiersza polecenia przy użyciu nazwy agenta usługi Syslog i adresu IP.
     - Ustaw format na **CEF**.
3. Jeśli używasz wersji R77.30 lub R80.10 przewiń do **instalacje** i postępuj zgodnie z instrukcjami, aby zainstalować eksportu dziennika, dla danej wersji.
 
## <a name="step-3-validate-connectivity"></a>Krok 3: Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 

1. Upewnij się, że dzienniki pojawiają się na prawy port w agencie programu Syslog. Uruchom następujące polecenie na komputerze agenta usługi Syslog: `tcpdump -A -ni any  port 514 -vv` To polecenie wyświetla dzienniki, które są przesyłane z urządzenia do maszyny Syslog. Upewnij się, że dzienniki są otrzymywane z urządzenia źródłowego na prawy port i funkcji odpowiednie.
2. Sprawdź, czy komunikacja między demona usługi Syslog i agenta. Uruchom następujące polecenie na komputerze agenta usługi Syslog: `tcpdump -A -ni any  port 25226 -vv` To polecenie wyświetla dzienniki, które są przesyłane z urządzenia do maszyny Syslog. Upewnij się, że dzienniki są one odbierane w agencie.
3. Jeśli oba te polecenia pomyślne wyniki, sprawdź usługi Log Analytics, aby zobaczyć, jeśli dzienniki są odbierane. Wszystkie zdarzenia przesyłane strumieniowo ze te urządzenia są wyświetlane w nieprzetworzonej postaci, w usłudze Log Analytics w obszarze `CommonSecurityLog` typu.

4. Upewnij się, uruchom następujące polecenia:
  
   - Jeśli używasz demona Syslog-ng, uruchom następujące polecenia (Uwaga jej ponownym uruchomieniu agenta programu Syslog):

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        Uruchom ponownie demona usługi Syslog: `sudo service syslog-ng restart`

   - Jeśli używasz rsyslog, uruchom następujące polecenia (Uwaga jej ponownym uruchomieniu agenta programu Syslog): 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     Uruchom ponownie demona usługi Syslog: `sudo service rsyslog restart`

1. Aby sprawdzić, czy występują błędy, czy dzienniki nie są przychodzących, Szukaj w `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Upewnij się, że rozmiar domyślny wiadomości usługi Syslog wynosi 2048 bajtów (2KB). Jeśli dzienniki są zbyt długie, należy zaktualizować security_events.conf za pomocą tego polecenia: `message_length_limit 4096`



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń Check Point do platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

