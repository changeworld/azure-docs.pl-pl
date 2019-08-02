---
title: Połącz dane F5 z podglądem platformy Azure — Wersja zapoznawcza | Microsoft Docs
description: Dowiedz się, jak połączyć dane F5 z platformą Azure wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 7ad95515ff58e2990102fa2b71d0ce66d4377617
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679292"
---
# <a name="connect-your-f5-appliance"></a>Podłączanie urządzenia F5

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć wskaźnik na platformie Azure z dowolnym urządzeniem F5, zapisując pliki dziennika jako dziennik systemowy CEF. Integracja z platformą Azure — wskaźnikiem, umożliwia łatwe uruchamianie analiz i zapytań w danych pliku dziennika z F5. Aby uzyskać więcej informacji na temat sposobu pozyskiwania danych CEF przez platformę Azure, zobacz [Łączenie urządzeń CEF](connect-common-event-format.md).

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="step-1-connect-your-f5-appliance-using-an-agent"></a>Krok 1: Podłączanie urządzenia F5 przy użyciu agenta

Aby podłączyć urządzenie F5 do centrum danych platformy Azure, należy wdrożyć agenta na dedykowanym komputerze (maszynie wirtualnej lub lokalnie) w celu obsługi komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure.

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej.

> [!NOTE]
> Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. 

Aby wyświetlić diagram sieci w obu opcjach, zobacz [łączenie ze źródłami danych](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Wdrażanie agenta 

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **F5** , a następnie **Otwórz stronę łącznik**. 

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


## <a name="step-2-forward-f5-logs-to-the-syslog-agent"></a>Krok 2: Przekazanie dzienników F5 do agenta dziennika systemu

Skonfiguruj F5 do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu:

Przejdź do pozycji F5 [Konfigurowanie rejestrowania zdarzeń zabezpieczeń aplikacji](https://aka.ms/asi-syslog-f5-forwarding)i postępuj zgodnie z instrukcjami, aby skonfigurować rejestrowanie zdalne, korzystając z następujących wskazówek:
  - Ustaw **Typ magazynu zdalnego** na **CEF**.
  - Ustaw **Protokół** na **UDP**.
  - Ustaw **adres IP** na adres IP serwera dziennika systemowego.
  - Ustaw **numer portu** na **514**lub port, który ma być używany przez agenta.
  - Ustaw dla **funkcji** wartość ustawioną w agencie dziennika systemu (Domyślnie agent ustawia tę opcję na **local4**).
  - **Maksymalny rozmiar ciągu zapytania** można ustawić na rozmiar ustawiony w agencie.

## <a name="step-3-validate-connectivity"></a>Krok 3: Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 

1. Upewnij się, że używasz odpowiedniej funkcji. Ta funkcja musi być taka sama w urządzeniu i w wskaźniku na platformie Azure. Możesz sprawdzić, który plik funkcji jest używany na platformie Azure, i zmodyfikować go w pliku `security-config-omsagent.conf`. 

2. Upewnij się, że dzienniki znajdują się na odpowiednim porcie w agencie dziennika systemowego. Uruchom to polecenie na komputerze agenta dziennika systemu: `tcpdump -A -ni any  port 514 -vv`To polecenie umożliwia wyświetlenie dzienników przesyłanych strumieniowo z urządzenia do maszyny dziennika systemowego. Upewnij się, że dzienniki są odbierane z urządzenia źródłowego z właściwym portem i odpowiednim obiektem.

3. Upewnij się, że dzienniki są zgodne ze specyfikacją [RFC 3164](https://tools.ietf.org/html/rfc3164).

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
W tym dokumencie pokazano, jak podłączyć urządzenia F5 do usługi Azure wskaźnikowej. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

