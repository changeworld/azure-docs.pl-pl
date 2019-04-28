---
title: Połącz dane Palo Alto Networks przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć dane Palo Alto Networks z platformy Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 0e811fb8d084f90410d48124f488843daa31a5a5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107657"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Łączenie urządzenia Palo Alto Networks

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć przez wartownika platformy Azure do dowolnego urządzenia Palo Alto Networks zapisując pliki dziennika w formacie Syslog CEF. Integracja z platformy Azure przez wartownika pozwala łatwo uruchamiać analizy i zapytań dla danych pliku dziennika z Palo Alto Networks. Aby uzyskać więcej informacji na temat sposobu przez wartownika Azure pozyskuje dane CEF, zobacz [CEF łączenie urządzeń](connect-common-event-format.md).

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>Krok 1: Łączenie urządzenia Palo Alto Networks przy użyciu agenta

Aby połączyć urządzenia Palo Alto Networks z platformy Azure przez wartownika, należy wdrożyć agenta na dedykowanym komputerze (maszyny Wirtualnej lub lokalnie) na potrzeby obsługi komunikacji między urządzeniem i przez wartownika Azure. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępna tylko w przypadku dedykowanej maszynie nową maszynę Wirtualną, tworzysz na platformie Azure. 

Alternatywnie można wdrożyć agenta ręcznie na istniejącej Maszynie wirtualnej platformy Azure, na maszynie Wirtualnej w innej chmurze lub na maszynie lokalnej.

Aby wyświetlić diagram sieciowy obie opcje, zobacz [połączyć źródeł danych](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent-in-azure"></a>Wdrażanie agenta w systemie Azure

1. W portalu Azure przez wartownika kliknij **łączników danych** i wybierz typ urządzenia. 

1. W obszarze **konfiguracji agenta systemu Linux Syslog**:
   - Wybierz **wdrażania automatycznego** Jeśli chcesz utworzyć nową maszynę, jest wstępnie zainstalowany za pomocą agenta usługi Azure przez wartownika, która obejmuje wszystkie niezbędne konfiguracji zgodnie z powyższym opisem. Wybierz **wdrażania automatycznego** i kliknij przycisk **wdrożenia agentami automatycznymi**. Spowoduje to przejście do strony zakupu dla dedykowanych maszynę Wirtualną, która jest automatycznie połączony z obszarem roboczym, jest. Maszyna wirtualna jest **standardowa D2s v3 (2 procesorów wirtualnych Vcpu, 8 GB pamięci RAM)** i ma publiczny adres IP.
      1. W **wdrożenie niestandardowe** strony, zapewniają szczegółowe informacje i wybierz nazwę użytkownika i hasło i jeśli zgadzasz się na warunki i postanowienia, zakup maszyny Wirtualnej.
      1. Konfigurowanie urządzenia w taki sposób, aby wysłać dzienniki przy użyciu ustawień wymienionych w na stronie połączenia. Łącznik ogólnego Common Event Format Użyj następujących ustawień:
         - Protokół = UDP
         - Port = 514
         - Funkcji = lokalne 4
         - Format = CEF
   - Wybierz **ręcznego wdrażania** Jeśli chcesz użyć istniejącej maszyny Wirtualnej jako dedykowane maszyny z systemem Linux, na którym powinien być zainstalowany agent usługi Azure przez wartownika. 
      1. W obszarze **pobrać i zainstalować agenta programu Syslog**, wybierz opcję **maszyny wirtualnej z systemem Linux platformy Azure**. 
      1. W **maszyn wirtualnych** ekran, który zostanie otwarty, wybierz maszynę, o których chcesz użyć, a następnie kliknij przycisk **Connect**.
      1. Na ekranie łącznika w obszarze **Konfiguruj i przekazywania usługi Syslog**Ustaw czy demona usługi Syslog jest **rsyslog.d** lub **demona syslog-ng**. 
      1. Skopiuj te polecenia i uruchamiać je na urządzeniu:
          - W przypadku wybrania rsyslog.d:
              
            1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.
            
            1. Uruchom ponownie demona usługi syslog `sudo service rsyslog restart`
             
          - W przypadku wybrania demona syslog-ng:

              1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.

              3. Uruchom ponownie demona usługi syslog `sudo service syslog-ng restart`
      2. Uruchom ponownie agenta usługi Syslog za pomocą tego polecenia: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Upewnij się, że nie ma żadnych błędów w dzienniku agenta, uruchamiając następujące polecenie: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Wdróż agenta na lokalny serwer systemu Linux

Jeśli nie używasz platformy Azure, ręcznie wdrożyć agenta przez wartownika platformy Azure, aby uruchomić na dedykowanym serwerze z systemem Linux.


1. W portalu Azure przez wartownika kliknij **łączników danych** i wybierz typ urządzenia.
1. Aby utworzyć dedykowane maszyny Wirtualnej systemu Linux w obszarze **konfiguracji agenta systemu Linux Syslog** wybierz **ręcznego wdrażania**.
   1. W obszarze **pobrać i zainstalować agenta programu Syslog**, wybierz opcję **maszyny z systemem Linux spoza platformy Azure**. 
   1. W **agent bezpośredni** ekran, który zostanie otwarty, wybierz **agenta dla systemu Linux** Pobierz agenta lub uruchom następujące polecenie, aby ją pobrać na maszynie z systemem Linux:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Na ekranie łącznika w obszarze **Konfiguruj i przekazywania usługi Syslog**Ustaw czy demona usługi Syslog jest **rsyslog.d** lub **demona syslog-ng**. 
      1. Skopiuj te polecenia i uruchamiać je na urządzeniu:
         - W przypadku wybrania rsyslog:
           1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.
           3. Uruchom ponownie demona usługi syslog `sudo service rsyslog restart`
         - W przypadku wybrania demona syslog-ng:
            1. Poinformuj demona dziennika systemu do nasłuchiwania local_4 funkcji i wysłać komunikaty dziennika systemu do platformy Azure przez wartownika agenta przy użyciu portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Pobierz i zainstaluj [pliku konfiguracyjnego security_events](https://aka.ms/asi-syslog-config-file-linux) , konfiguruje agenta usługi Syslog do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Gdzie {0} należy zamienić na GUID obszaru roboczego.
            3. Uruchom ponownie demona usługi syslog `sudo service syslog-ng restart`
      1. Uruchom ponownie agenta usługi Syslog za pomocą tego polecenia: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Upewnij się, że nie ma żadnych błędów w dzienniku agenta, uruchamiając następujące polecenie: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Krok 2: Przekazywania dzienników Palo Alto Networks z agentem programu Syslog

Skonfiguruj Palo Alto Networks do przesyłania dalej komunikatów Syslog w formacie CEF do obszaru roboczego platformy Azure za pomocą agenta usługi Syslog:
1.  Przejdź do [przewodniki konfiguracji Common Event Format (CEF)](https://docs.paloaltonetworks.com/resources/cef) i Pobierz plik pdf dla danego typu urządzenia. Wykonaj wszystkie instrukcje w przewodniku, aby skonfigurować urządzenia Palo Alto Networks służąca do gromadzenia zdarzeń CEF. 

1.  Przejdź do [Syslog Konfigurowanie monitorowania](https://aka.ms/asi-syslog-paloalto-forwarding) i wykonaj kroki 2 i 3, aby skonfigurować przekazywanie zdarzeń CEF z Twojego urządzenia Palo Alto Networks do platformy Azure przez wartownika.

    1. Upewnij się ustawić **formatu serwera Syslog** do **BSD**.
    1. Upewnij się, możesz ustawić **numer instrumentu** taką samą wartość można ustawić w agencie usługi Syslog.

> [!NOTE]
> Operacje kopiowania i wklejania z pliku PDF, może zmienić tekst i Wstaw losowo wybranych znaków. Aby tego uniknąć, należy skopiować tekst do edytora i Usuń wszystkie znaki, które mogłyby spowodować przerwanie działania format dziennika przed wklejeniem, jak pokazano w tym przykładzie.
 
  ![Problem z kopiowaniem tekstu CEF](./media/connect-cef/paloalto-text-prob1.png)

## <a name="step-3-validate-connectivity"></a>Krok 3: Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 

1. Upewnij się, że dzienniki pojawiają się na prawy port w agencie programu Syslog. Uruchom następujące polecenie na komputerze agenta usługi Syslog: `tcpdump -A -ni any  port 514 -vv` To polecenie wyświetla dzienniki, które są przesyłane z urządzenia do maszyny Syslog. Upewnij się, że dzienniki są otrzymywane z urządzenia źródłowego na prawy port i funkcji odpowiednie.
2. Sprawdź, czy komunikacja między demona usługi Syslog i agenta. Uruchom następujące polecenie na komputerze agenta usługi Syslog: `tcpdump -A -ni any  port 25226 -vv` To polecenie wyświetla dzienniki, które są przesyłane z urządzenia do maszyny Syslog. Upewnij się, że dzienniki są one odbierane w agencie.
3. Jeśli oba te polecenia pomyślne wyniki, sprawdź usługi Log Analytics, aby zobaczyć, jeśli dzienniki są odbierane. Wszystkie zdarzenia przesyłane strumieniowo ze te urządzenia są wyświetlane w nieprzetworzonej postaci, w usłudze Log Analytics w obszarze `CommonSecurityLog` typu.
1. Aby sprawdzić, czy występują błędy, czy dzienniki nie są przychodzących, Szukaj w `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Upewnij się, że rozmiar domyślny wiadomości usługi Syslog wynosi 2048 bajtów (2KB). Jeśli dzienniki są zbyt długie, należy zaktualizować security_events.conf za pomocą tego polecenia: `message_length_limit 4096`
6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla zdarzeń Palo Alto Networks, wyszukaj **CommonSecurityLog**.







## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie pokazaliśmy ci, jak połączyć urządzenia Palo Alto Networks z platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

