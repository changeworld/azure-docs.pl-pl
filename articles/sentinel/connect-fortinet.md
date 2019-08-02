---
title: Łączenie danych Fortinet z platformą Azure wskaźnikiem wersji zapoznawczej | Microsoft Docs
description: Dowiedz się, jak połączyć dane Fortinet z platformą Azure.
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
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679277"
---
# <a name="connect-your-fortinet-appliance"></a>Połącz urządzenie Fortinet

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Możesz połączyć wskaźnik na platformie Azure z dowolnym urządzeniem Fortinet, zapisując pliki dziennika jako dziennik Common Event format (CEF). Dzięki integracji z platformą Azure — wskaźnikiem, można łatwo uruchomić analizy i zapytania w danych pliku dziennika od firmy Fortinet. Aby uzyskać więcej informacji na temat sposobu pozyskiwania danych CEF przez platformę Azure, zobacz [Łączenie urządzeń CEF](connect-common-event-format.md).

> [!NOTE]
> Dane są przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Krok 1: Łączenie urządzenia Fortinet przy użyciu agenta

Aby połączyć urządzenie Fortinet z platformą Azure, należy wdrożyć agenta na dedykowanej maszynie wirtualnej lub maszynie lokalnej w celu zapewnienia obsługi komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure. 

Agenta można również wdrożyć ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej.

> [!NOTE]
> Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. 

Aby wyświetlić diagram sieci w obu opcjach, zobacz [łączenie ze źródłami danych](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Wdrażanie agenta

1. W portalu Azure Research kliknij pozycję **Łączniki danych** i wybierz pozycję **Fortinet** , a następnie **Otwórz stronę łącznik**. 

1. W obszarze **Pobierz i Zainstaluj agenta dziennika**systemu wybierz typ komputera, na platformie Azure lub lokalnie. 
1. Na ekranie **maszyny wirtualne** , który zostanie otwarty, wybierz maszynę, której chcesz użyć, a następnie kliknij przycisk **Połącz**.
1. W przypadku wybrania opcji **Pobierz i Zainstaluj agenta dla maszyn wirtualnych platformy Azure z systemem Linux**wybierz maszynę, a następnie kliknij pozycję **Połącz**. W przypadku wybrania opcji **Pobierz i Zainstaluj agenta dla maszyn wirtualnych z systemem innym niż Azure**, na ekranie **agenta bezpośredniego** Uruchom skrypt w obszarze **Pobierz i Dołącz agenta dla systemu Linux**.
1. Na ekranie łącznika w obszarze **Konfigurowanie i przekazywanie dziennika**systemowego Określ, czy demon dziennika systemu ma być **rsyslog. d** , czy **Dziennik**systemowy. 
1. Skopiuj te polecenia i uruchom je na urządzeniu:
   - W przypadku wybrania rsyslog. d:
            
     1. Poinformuj demona dziennika systemu, aby nasłuchiwać local_4 i wysyłać komunikaty dziennika systemowego do agenta wskaźnikowego platformy Azure przy użyciu portu 25226. Użyj tego polecenia:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Pobierz i zainstaluj [plik konfiguracyjny security_events](https://aka.ms/asi-syslog-config-file-linux) , który konfiguruje agenta dziennika systemowego do nasłuchiwania na porcie 25226. Użyj tego polecenia: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` gdzie {0} należy zastąpić identyfikator GUID obszaru roboczego.
     1. Uruchom ponownie demona dziennika systemu przy użyciu tego polecenia:`sudo service rsyslog restart`
            
   - W przypadku wybrania dziennika systemowego — NG:

      1. Poinformuj demona dziennika systemu, aby nasłuchiwać local_4 i wysyłać komunikaty dziennika systemowego do agenta wskaźnikowego platformy Azure przy użyciu portu 25226. Użyj tego polecenia:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Pobierz i zainstaluj [plik konfiguracyjny security_events](https://aka.ms/asi-syslog-config-file-linux) , który konfiguruje agenta dziennika systemowego do nasłuchiwania na porcie 25226. Użyj tego polecenia: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` gdzie {0} należy zastąpić identyfikator GUID obszaru roboczego.
      1. Uruchom ponownie demona dziennika systemu przy użyciu tego polecenia:`sudo service syslog-ng restart`
1. Uruchom ponownie agenta dziennika systemu przy użyciu tego polecenia:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Upewnij się, że w dzienniku agenta nie ma błędów, uruchamiając następujące polecenie:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Krok 2: Przekazywanie dzienników firmy Fortinet do agenta dziennika systemu

Skonfiguruj konfigurację Fortinet do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Otwórz interfejs wiersza polecenia na urządzeniu Fortinet i uruchom następujące polecenia:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Zastąp **adres IP** serwera adresem IP agenta.
    - Ustaw **facility_name** do korzystania z funkcji skonfigurowanej w agencie. Domyślnie agent ustawia tę wartość na local4.
    - Ustaw wartość w polu **port dziennika** systemowego na **514** lub port ustawiony w agencie.
    - Aby włączyć format CEF w wersjach wczesnego FortiOS, może być konieczne uruchomienie opcji wyłączania **CSV**zestawu poleceń.
 
   > [!NOTE] 
   > Aby uzyskać więcej informacji, przejdź do [biblioteki dokumentów Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Wybierz swoją wersję, a następnie użyj dokumentacji podręcznika i **komunikatu dziennika**.

 Aby użyć odpowiedniego schematu w Azure Monitor Log Analytics dla imprez Fortinet, wyszukaj ciąg `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Krok 3: Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

1. Upewnij się, że używasz odpowiedniej funkcji. Ta funkcja musi być taka sama w urządzeniu i w wskaźniku na platformie Azure. Możesz sprawdzić, który plik funkcji jest używany na platformie Azure, i zmodyfikować go w pliku `security-config-omsagent.conf`. 

2. Upewnij się, że dzienniki znajdują się na odpowiednim porcie w agencie dziennika systemowego. Uruchom to polecenie na komputerze agenta dziennika systemu: `tcpdump -A -ni any  port 514 -vv`. To polecenie umożliwia wyświetlenie dzienników przesyłanych strumieniowo z urządzenia do maszyny dziennika systemowego. Upewnij się, że dzienniki są odbierane z urządzenia źródłowego na odpowiednim porcie i właściwej funkcji.

3. Upewnij się, że dzienniki są zgodne ze specyfikacją [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. Upewnij się, że porty 514 i 25226 są otwarte i nasłuchuje `netstat -a -n:`na komputerze z uruchomionym agentem dziennika systemowego. Aby uzyskać więcej informacji na temat korzystania z tego polecenia, zobacz [stronę sieci Web z systemem Linux (8)](https://linux.die.net/man/8/netstat). Jeśli nasłuchuje prawidłowo, zobaczysz:

   ![Porty wskaźnikowe platformy Azure](./media/connect-cef/ports.png) 

5. Upewnij się, że demon jest skonfigurowany do nasłuchiwania na porcie 514, na którym są wysyłane dzienniki.
    - Dla rsyslog:<br>Upewnij się, że plik `/etc/rsyslog.conf` zawiera następującą konfigurację:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Aby uzyskać więcej informacji, [Zobacz imudp: Moduł](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) wejściowy dziennika systemowego UDP i [imtcp: Moduł](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)danych wejściowych dziennika systemu TCP.

   - Dla dziennika systemowego — NG:<br>Upewnij się, że plik `/etc/syslog-ng/syslog-ng.conf` zawiera następującą konfigurację:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Aby uzyskać więcej informacji, [Zobacz imudp: Moduł](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) wejściowy dziennika systemowego UDP i [Dziennik systemu w wersji Open Source Edition 3,16 — Podręcznik administrowania](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Sprawdź, czy istnieje komunikacja między demonem dziennika systemowego a agentem. Uruchom to polecenie na komputerze agenta dziennika systemu: `tcpdump -A -ni any  port 25226 -vv`. To polecenie umożliwia wyświetlenie dzienników przesyłanych strumieniowo z urządzenia do maszyny dziennika systemowego. Upewnij się, że dzienniki są również odbierane w agencie.

6. Jeśli oba te polecenia zapewniały pomyślne wyniki, sprawdź Log Analytics, aby sprawdzić, czy dzienniki są odbierane. Wszystkie zdarzenia przesyłane strumieniowo z tych urządzeń są wyświetlane w formacie nieprzetworzonym w log Analytics w obszarze `CommonSecurityLog` typ.

7. Aby sprawdzić, czy występują błędy lub czy dzienniki nie docierają, zapoznaj się `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`z tematem. Jeśli komunikat ma błędy niezgodności formatu dziennika, przejdź do `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` pliku `security_events.conf`i sprawdź go. Upewnij się, że dzienniki są zgodne z formatem wyrażeń regularnych, które są widoczne w tym pliku.

8. Upewnij się, że rozmiar domyślnego komunikatu dziennika systemu jest ograniczony do 2048 bajtów (2 KB). Jeśli dzienniki są zbyt długie, zaktualizuj security_events. conf przy użyciu tego polecenia:`message_length_limit 4096`

10. Jeśli dzienniki firmy Fortinet nie są odbierane przez agenta, Uruchom to polecenie, w zależności od używanego typu demona dziennika systemu, aby ustawić funkcję i skonfigurować dzienniki w celu wyszukania słowa Fortinet w dziennikach:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Uruchom ponownie demona dziennika systemu przy użyciu tego polecenia:`sudo service rsyslog restart`
       - Dziennik systemowy — NG:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Uruchom ponownie demona dziennika systemu przy użyciu tego polecenia:`sudo service syslog-ng restart`

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób łączenia urządzeń Fortinet z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

