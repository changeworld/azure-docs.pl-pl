---
title: Połącz dane CEF z podglądem platformy Azure — Wersja zapoznawcza | Microsoft Docs
description: Dowiedz się, jak połączyć dane CEF z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 25ae54ea8dd75fae74d4578b33146483ade53e4c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240746"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Łączenie rozwiązania zewnętrznego przy użyciu typowego formatu zdarzeń

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Możesz połączyć wskaźnik platformy Azure z zewnętrznym rozwiązaniem, które umożliwia zapisywanie plików dziennika w dzienniku systemowym. Jeśli urządzenie umożliwia zapisywanie dzienników jako dziennika zdarzeń common Event format (CEF), integracja z platformą Azure ze wskaźnikami umożliwia łatwe uruchamianie analiz i zapytań w ramach danych.

> [!NOTE] 
> Dane są przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="how-it-works"></a>Jak to działa

Połączenie między wskaźnikiem produktu Azure a urządzeniem CEF odbywa się w trzech krokach:

1. Na urządzeniu należy ustawić te wartości, aby urządzenie wysyła niezbędne dzienniki w niewystarczającym formacie do agenta dziennika systemu Azure wskaźnikowego na podstawie Microsoft Monitoring Agent. Te parametry można modyfikować w urządzeniu, o ile są one również modyfikowane w demona dziennika systemowego w ramach agenta wskaźnikowego platformy Azure.
    - Protokół = UDP
    - Port = 514
    - Funkcja = Local4
    - Format = CEF
2. Agent dziennika systemu zbiera dane i przesyła je bezpiecznie do Log Analytics, w którym są analizowane i wzbogacane.
3. Agent przechowuje dane w obszarze roboczym Log Analytics, dzięki czemu będzie można z nich wykonywać zapytania, korzystając z analizy, reguł korelacji i pulpitów nawigacyjnych.

> [!NOTE]
> Agent może zbierać dzienniki z wielu źródeł, ale muszą być zainstalowane na dedykowanym komputerze.


 ![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej. 

 ![CEF lokalnie](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. Aby ulepszyć konfigurację zabezpieczeń komputera, można użyć następujących instrukcji:  [bezpieczna maszyna wirtualna na platformie Azure](../virtual-machines/linux/security-policy.md), [najlepsze rozwiązania dotyczące zabezpieczeń sieci](../security/fundamentals/network-best-practices.md).

Aby można było korzystać z komunikacji TLS między rozwiązaniem zabezpieczeń a maszyną dziennika systemowego, należy skonfigurować demona dziennika systemu (rsyslog lub Dziennik systemowy-NG) do komunikacji w protokole TLS: [Szyfrowanie ruchu dziennika systemu przy użyciu protokołu TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [szyfrowanie komunikatów dzienników przy użyciu protokołu TLS — dziennik systemowy-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Krok 1: Konfigurowanie maszyny wirtualnej dziennika systemowego

Należy wdrożyć agenta na dedykowanym komputerze z systemem Linux (maszynie wirtualnej lub lokalnie) do obsługi komunikacji między urządzeniem a platformą Azure. 

> [!NOTE]
> Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. 


1. W portalu Azure wskaźnikowym kliknij pozycję **Łączniki danych** i wybierz pozycję **Common Event format (CEF)** , a następnie **Otwórz stronę łącznik**. 

1. W obszarze **Pobierz i Zainstaluj agenta dziennika**systemu wybierz typ komputera, na platformie Azure lub lokalnie. 
1. Na ekranie **maszyny wirtualne** , który zostanie otwarty, wybierz maszynę, której chcesz użyć, a następnie kliknij przycisk **Połącz**.
1. W przypadku wybrania opcji **Pobierz i Zainstaluj agenta dla maszyn wirtualnych platformy Azure z systemem Linux**wybierz maszynę, a następnie kliknij pozycję **Połącz**. W przypadku wybrania opcji **Pobierz i Zainstaluj agenta dla maszyn wirtualnych z systemem innym niż Azure**, na ekranie **agenta bezpośredniego** Uruchom skrypt w obszarze **Pobierz i Dołącz agenta dla systemu Linux**.
1. Na ekranie łącznika CEF, w obszarze **Konfigurowanie i przekazywanie dziennika**systemowego, określ, czy demon dziennika systemowego ma **rsyslog. d** lub **Dziennik**systemowy. 
1. Skopiuj te polecenia i uruchom je na urządzeniu:
    - W przypadku wybrania rsyslog. d:
              
       1. Poinformuj demona dziennika systemu, aby nasłuchiwać local_4 i wysyłać komunikaty dziennika systemowego do agenta wskaźnikowego platformy Azure przy użyciu portu 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Pobierz i zainstaluj [plik konfiguracyjny security_events](https://aka.ms/asi-syslog-config-file-linux) , który konfiguruje agenta dziennika systemowego do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Gdzie {0} należy zastąpić identyfikator GUID obszaru roboczego.
            
       1. Uruchom ponownie demona dziennika systemu`sudo service rsyslog restart`<br> Aby uzyskać więcej informacji, zobacz [dokumentację rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
    - W przypadku wybrania dziennika systemowego — NG:
       1. Poinformuj demona dziennika systemu, aby nasłuchiwać local_4 i wysyłać komunikaty dziennika systemowego do agenta wskaźnikowego platformy Azure przy użyciu portu 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Pobierz i zainstaluj [plik konfiguracyjny security_events](https://aka.ms/asi-syslog-config-file-linux) , który konfiguruje agenta dziennika systemowego do nasłuchiwania na porcie 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Gdzie {0} należy zastąpić identyfikator GUID obszaru roboczego.

        3. Uruchom ponownie demona dziennika systemu`sudo service syslog-ng restart` <br>Aby uzyskać więcej informacji, zobacz [dokumentację dziennika systemu](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
1. Uruchom ponownie agenta dziennika systemu przy użyciu tego polecenia:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Upewnij się, że w dzienniku agenta nie ma błędów, uruchamiając następujące polecenie:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń CEF, wyszukaj ciąg `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Krok 2: Dzienniki usługi Common Event format (CEF) do agenta dziennika systemu

Ustaw rozwiązanie zabezpieczeń, aby wysyłać komunikaty dziennika systemu w formacie CEF do agenta dziennika systemowego. Upewnij się, że używasz tych samych parametrów, które są wyświetlane w konfiguracji agenta. Są to zazwyczaj:

- Port 514
- Local4a funkcji

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
W tym dokumencie przedstawiono sposób łączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

