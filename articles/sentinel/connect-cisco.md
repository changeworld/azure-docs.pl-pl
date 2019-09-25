---
title: Łączenie danych Cisco z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć dane Cisco z platformą Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6218d3a53df38e938b7d7805c45facd30f850105
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240160"
---
# <a name="connect-your-cisco-asa-appliance"></a>Łączenie urządzenia Cisco ASA 



Możesz połączyć urządzenie wskaźnikowe platformy Azure z dowolnym urządzeniem Cisco ASA. Cisco ASA jest natywnie zintegrowana z platformą Azure wskaźnikiem do pozyskiwania danych, dzięki czemu mimo że urządzenie Cisco nie zapisuje dzienników jako CEF, wskaźnik na platformie Azure pozyskuje je w taki sam sposób, jak w przypadku dzienników CEF. Integracja z platformą Azure wskaźnikiem umożliwia łatwe uruchamianie analiz i zapytań w ramach danych pliku dziennika z programu Cisco ASA. 

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="step-1-connect-your-cisco-asa-appliance-using-an-agent"></a>Krok 1: Łączenie urządzenia Cisco ASA przy użyciu agenta

Aby połączyć urządzenie Cisco ASA z platformą Azure, należy wdrożyć agenta na dedykowanym komputerze (maszynie wirtualnej lub lokalnie) w celu obsługi komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure. 

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej.

> [!NOTE]
> Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. 

Aby wyświetlić diagram sieci w obu opcjach, zobacz [łączenie ze źródłami danych](connect-data-sources.md).

### <a name="deploy-the-agent-on-your-machine"></a>Wdróż agenta na maszynie

1. W portalu usługi Azure wskaźnikowym kliknij pozycję **Łączniki danych** i wybierz pozycję **Cisco ASA** , a następnie **Otwórz stronę łącznik**. 

1. W obszarze **Pobierz i Zainstaluj agenta dziennika**systemu wybierz typ komputera, na platformie Azure lub lokalnie. 
1. Na ekranie **maszyny wirtualne** , który zostanie otwarty, wybierz maszynę, której chcesz użyć, a następnie kliknij przycisk **Połącz**.
1. W przypadku wybrania opcji **Pobierz i Zainstaluj agenta dla maszyn wirtualnych platformy Azure z systemem Linux**wybierz maszynę, a następnie kliknij pozycję **Połącz**. W przypadku wybrania opcji **Pobierz i Zainstaluj agenta dla maszyn wirtualnych z systemem innym niż Azure**, na ekranie **agenta bezpośredniego** Uruchom skrypt w obszarze **Pobierz i Dołącz agenta dla systemu Linux**.
1. Na ekranie łącznika w obszarze **Konfigurowanie i przekazywanie dziennika**systemowego Określ, czy demon dziennika systemu ma być **rsyslog. d** , czy **Dziennik**systemowy. 
1. Skopiuj te polecenia i uruchom je na urządzeniu:
    - W przypadku wybrania rsyslog. d:
              
       1. Poinformuj demona dziennika systemu, aby nasłuchiwać local_4 i wysyłać komunikaty dziennika systemowego do agenta wskaźnikowego platformy Azure przy użyciu portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Pobierz i zainstaluj [plik konfiguracyjny security_events](https://aka.ms/asi-syslog-config-file-linux) , który konfiguruje agenta dziennika systemowego do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Gdzie {0} należy zastąpić identyfikator GUID obszaru roboczego.
            
      1. Uruchom ponownie demona dziennika systemu`sudo service rsyslog restart`
             
    - W przypadku wybrania dziennika systemowego — NG:

        1. Poinformuj demona dziennika systemu, aby nasłuchiwać local_4 i wysyłać komunikaty dziennika systemowego do agenta wskaźnikowego platformy Azure przy użyciu portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
        2. Pobierz i zainstaluj [plik konfiguracyjny security_events](https://aka.ms/asi-syslog-config-file-linux) , który konfiguruje agenta dziennika systemowego do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Gdzie {0} należy zastąpić identyfikator GUID obszaru roboczego.

        3. Uruchom ponownie demona dziennika systemu`sudo service syslog-ng restart`
1. Uruchom ponownie agenta dziennika systemu przy użyciu tego polecenia:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Upewnij się, że w dzienniku agenta nie ma błędów, uruchamiając następujące polecenie:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>Krok 2: Przekazywanie dzienników Cisco ASA do agenta dziennika systemu

Cisco ASA nie obsługuje CEF, więc dzienniki są wysyłane jako dziennik systemowy, a agent wskaźnikowego platformy Azure wie, jak przeanalizować je tak, jakby znajdowały się w dziennikach CEF. Skonfiguruj Cisco ASA do przesyłania dalej komunikatów dziennika systemowego do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu:

Przejdź do obszaru [wysyłanie komunikatów dziennika systemowego do zewnętrznego serwera dziennika](https://aka.ms/asi-syslog-cisco-forwarding)systemu i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie. Użyj tych parametrów po wyświetleniu monitu:
- Ustaw **port** na 514 lub port ustawiony w agencie.
- Ustaw **syslog_ip** na adres IP agenta.
- Ustaw **funkcję rejestrowania** na obiekt ustawiony w agencie. Domyślnie agent ustawia funkcję na 4.

Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń Cisco, wyszukaj ciąg `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>Krok 3: Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 

1. Upewnij się, że używasz odpowiedniej funkcji. Ta funkcja musi być taka sama w urządzeniu i w wskaźniku na platformie Azure. Możesz sprawdzić, który plik funkcji jest używany na platformie Azure, i zmodyfikować go w pliku `security-config-omsagent.conf`. 

2. Upewnij się, że dzienniki znajdują się na odpowiednim porcie w agencie dziennika systemowego. Uruchom to polecenie na komputerze agenta dziennika systemu: `tcpdump -A -ni any  port 514 -vv`To polecenie umożliwia wyświetlenie dzienników przesyłanych strumieniowo z urządzenia do maszyny dziennika systemowego. Upewnij się, że dzienniki są odbierane z urządzenia źródłowego z właściwym portem i odpowiednim obiektem.

3. Upewnij się, że dzienniki są zgodne ze [specyfikacją RFC 3164](https://tools.ietf.org/html/rfc3164).

4. Upewnij się, że te porty 514, 25226 są otwarte i nasłuchuje na komputerze z uruchomionym agentem dziennika `netstat -a -n:`systemowego, używając polecenia. Aby uzyskać więcej informacji na temat korzystania z tego polecenia, zobacz [stronę sieci Web z systemem Linux (8)](https://linux.die.net/man/8/netstat). Jeśli nasłuchuje prawidłowo, zobaczysz:

   ![Porty wskaźnikowe platformy Azure](./media/connect-cef/ports.png) 

5. Upewnij się, że demon jest skonfigurowany do nasłuchiwania na porcie 514, na którym są wysyłane dzienniki.
    - Dla rsyslog:<br>Upewnij się, że plik `/etc/rsyslog.conf` zawiera następującą konfigurację:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Aby uzyskać więcej informacji, [Zobacz imudp: Moduł](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) wejściowy dziennika systemowego UDP i [imtcp: Moduł danych wejściowych dziennika systemu TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Dla dziennika systemowego — NG:<br>Upewnij się, że plik `/etc/syslog-ng/syslog-ng.conf` zawiera następującą konfigurację:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Aby uzyskać więcej informacji, zobacz Dziennik systemowy [-ng Open Source Edition 3,16 — Przewodnik administrowania](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Sprawdź, czy jest dostępna komunikacja między demonem dziennika systemowego a agentem. Uruchom to polecenie na komputerze agenta dziennika systemu: `tcpdump -A -ni any  port 25226 -vv`To polecenie umożliwia wyświetlenie dzienników przesyłanych strumieniowo z urządzenia do maszyny dziennika systemowego. Upewnij się, że dzienniki są również odbierane w agencie.

6. Jeśli oba te polecenia zapewniały pomyślne wyniki, sprawdź Log Analytics, aby sprawdzić, czy dzienniki są odbierane. Wszystkie zdarzenia przesyłane strumieniowo z tych urządzeń są wyświetlane w formacie nieprzetworzonym w log Analytics w obszarze `CommonSecurityLog` typ.

7. Aby sprawdzić, czy występują błędy lub czy dzienniki nie docierają, zapoznaj się `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`z tematem. Jeśli komunikat ma błędy niezgodności formatu dziennika, przejdź do `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` pliku `security_events.conf`i sprawdź go i upewnij się, że dzienniki są zgodne z formatem wyrażenia regularnego widocznym w tym pliku.

8. Upewnij się, że rozmiar domyślnego komunikatu dziennika systemu jest ograniczony do 2048 bajtów (2 KB). Jeśli dzienniki są zbyt długie, zaktualizuj security_events. conf przy użyciu tego polecenia:`message_length_limit 4096`




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń z programem Cisco ASA z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

