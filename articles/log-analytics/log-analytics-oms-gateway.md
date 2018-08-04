---
title: Łączenie komputerów przy użyciu bramy pakietu OMS | Dokumentacja firmy Microsoft
description: Połącz swoje urządzenia i komputery monitorowane przez menedżera operacji za pomocą bramy pakietu OMS do wysyłania danych do usługi Azure Automation i usługę Log Analytics, gdy nie mają dostępu do Internetu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 74da7e96ed52b441bc63d5fb5a032db9c6d57774
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494280"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Łączenie komputerów bez dostępu do Internetu za pomocą bramy pakietu OMS
W tym dokumencie opisano sposób konfigurowania komunikacji z usługą Azure Automation i Log Analytics przy użyciu bramy pakietu OMS, w przypadku bezpośredniego połączenia lub programu Operations Manager monitorowane komputery nie mają dostępu do Internetu.  Bramę pakietu OMS, czyli do przodu serwera proxy HTTP obsługującego tunelowania HTTP za pomocą polecenia połączenia protokołu HTTP, można zbierać dane i wysyłać je do usługi Azure Automation i Log Analytics w ich imieniu.  

Brama pakietu OMS obsługuje:

* Usługa Azure Automation hybrydowych procesów roboczych Runbook  
* Komputery Windows za pomocą programu Microsoft Monitoring Agent bezpośrednio połączone z obszarem roboczym usługi Log Analytics
* Komputery z systemem Linux za pomocą agenta pakietu OMS dla systemu Linux bezpośrednio połączone z obszarem roboczym usługi Log Analytics  
* System Center Operations Manager 2012 z dodatkiem SP1 z pakietem zbiorczym aktualizacji 7, Operations Manager 2012 R2 UR3, Operations Manager 2016 i grupy zarządzania programu Operations Manager w wersji 1801 zintegrowane z usługą Log Analytics.  

Jeśli Twoje informatyczne zasady zabezpieczeń nie pozwalają komputerom w sieci, aby nawiązać połączenie z Internetem, takie jak punkt urządzeń sprzedaży (POS) lub serwerami obsługującymi funkcję usług informatycznych, ale należy podłączyć je do usługi Azure Automation lub usługi Log Analytics, zarządzanie i monitorowanie ich , można je skonfigurować do bezpośredniego komunikowania się z bramy pakietu OMS, aby otrzymać konfiguracji i przekazują dane w ich imieniu.  Jeśli te komputery są skonfigurowane za pomocą agenta pakietu OMS, aby łączyć się bezpośrednio do obszaru roboczego usługi Log Analytics, wszystkie komputery zamiast tego będą komunikować się za pomocą bramy pakietu OMS.  Bramy przesyła dane z agentów do usługi bezpośrednio, nie zostaną przeanalizowane dane podczas przesyłania.

W przypadku grupy zarządzania programu Operations Manager jest zintegrowany z usługą Log Analytics, do połączenia z bramą pakietu OMS do odbierania informacji o konfiguracji i wysyłać zebrane dane, w zależności od rozwiązania, które można skonfigurować serwery zarządzania.  Agenci programu Operations Manager wysyłać pewne dane, takie jak alerty programu Operations Manager, funkcję oceny konfiguracji pakietu, miejsce zajmowane przez wystąpienia i dane wydajności serwera zarządzania. Inne dane w dużej liczby, takich jak dzienniki usług IIS, wydajności i zdarzeń dotyczących zabezpieczeń są wysyłane bezpośrednio do bramy pakietu OMS.  Mając wdrożone w sieci Obwodowej lub innych sieci izolowanej do monitorowania systemów niezaufanych co najmniej jeden serwer bramę programu Operations Manager nie może komunikować się z bramą pakietu OMS.  Serwery programu Operations Manager Gateway tylko zgłosić do serwera zarządzania.  Gdy grupa zarządzania programu Operations Manager jest skonfigurowany do komunikowania się z bramą pakietu OMS, informacje o konfiguracji serwera proxy jest automatycznie dystrybuowany do każdego komputera zarządzanego przez agenta, który jest skonfigurowany do gromadzenia danych dla usługi Log Analytics, nawet wtedy, gdy ustawienie jest puste.    

Aby zapewnić wysoką dostępność bezpośrednio połączone lub operacje grup zarządzania, które komunikują się z usługą Log Analytics za pośrednictwem bramy, co umożliwia równoważenie obciążenia sieciowego przekierowania i dystrybuowanie ruchu między wiele serwerów bramy.  Jeśli jeden serwer bramy ulegnie awarii, ruch jest przekierowywany do innego węzła dostępności.  

Agent pakietu OMS jest wymagany na komputerze z uruchomionym bramy pakietu OMS w celu identyfikowania punktami końcowymi usług wymaganych do komunikacji z i monitorować bramy pakietu OMS do analizowania jego wydajności lub dane zdarzenia.

Każdy agent, muszą mieć łączność sieciową do bramy, dzięki czemu agenci automatycznie mogą przesyłać dane do i z bramy. Instalowanie bramy na kontrolerze domeny nie jest zalecane.

Na poniższym diagramie przedstawiono przepływ danych z bezpośrednich agentów usługi Azure Automation i Log Analytics przy użyciu serwera bramy.  Agenci musi mieć ich konfiguracji serwera proxy, które pasuje do tego samego portu, który bramy pakietu OMS jest skonfigurowany do komunikacji z usługą.  

![agent bezpośredni komunikacji z diagram usług](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Na poniższym diagramie przedstawiono przepływ danych z grupy zarządzania programu Operations Manager do usługi Log Analytics.   

![Komunikacja Menedżera operacji z diagramem usługi Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Wymagania wstępne

Podczas wyznaczania komputera do uruchamiania bramy pakietu OMS, ten komputer należy dysponować następującymi elementami:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET framework 4.5
* Co najmniej 4-rdzeniową procesora i 8 GB pamięci 
* OMS Agent for Windows 

### <a name="language-availability"></a>Dostępne wersje językowe

Brama pakietu OMS jest dostępna w następujących językach:

- Chiński (uproszczony)
- Chiński (tradycyjny)
- Czeski
- Holenderski
- Polski
- Francuski
- Niemiecki
- Węgierski
- Włoski
- Japoński
- Koreański
- Polski
- Portugalski (Brazylia)
- Portugalski (Portugalia)
- Rosyjski
- Hiszpański (międzynarodowy)

### <a name="supported-encryption-protocols"></a>Szyfrowanie obsługiwane protokoły
Brama pakietu OMS obsługuje tylko zabezpieczeń TLS (Transport Layer) 1.0, 1.1 i 1.2.  Nie obsługuje protokołu Secure Sockets Layer (SSL).  Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do usługi Log Analytics, zdecydowanie zachęcamy do skonfigurowania bramy do użycia w co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**.  Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Obsługiwana liczba połączeń agenta
W poniższej tabeli wymieniono obsługiwane liczbę agentów komunikacji z serwerem bramy.  Ta funkcja opiera się na agentów przekazywania ~ 200KB danych co 6 sekund. Ilość danych na agenta testowana jest około 2.7GB dziennie.

|Brama |O liczbę agentów, obsługiwane|  
|--------|----------------------------------|  
|-Procesor CPU: Intel XEON E5 Procesora 2660 v3 \@ 2,6 GHz, 2 rdzenie<br> -Pamięci: 4 GB<br> — Przepustowość sieci: 1 GB/s| 600|  
|-Procesor CPU: Intel XEON E5 Procesora 2660 v3 \@ 2,6 GHz 4 rdzenie<br> -Pamięci: 8 GB<br> — Przepustowość sieci: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Pobierz bramę pakietu OMS

Istnieją dwa sposoby, aby uzyskać najnowszą wersję pliku instalacyjnego bramy pakietu OMS.

1. Pobieranie z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

1. Pobierz z witryny Azure portal.  Po zalogowaniu się w witrynie Azure portal:  

   1. Przejrzyj listę usług, a następnie wybierz **usługi Log Analytics**.  
   1. Wybierz obszar roboczy.
   1. W bloku obszaru roboczego w obszarze **ogólne**, kliknij przycisk **— Szybki Start**.
   1. W obszarze **wybierz źródło danych do łączenia z obszarem roboczym**, kliknij przycisk **komputerów**.
   1. W **Agent bezpośredni** bloku kliknij **Pobierz bramę pakietu OMS**.<br><br> ![Pobierz bramę pakietu OMS](./media/log-analytics-oms-gateway/download-gateway.png)

lub 

   1. W bloku obszaru roboczego w obszarze **ustawienia**, kliknij przycisk **Zaawansowane ustawienia**.
   1. Przejdź do **połączone źródła** > **serwerów Windows** i kliknij przycisk **Pobierz bramę pakietu OMS**.

## <a name="install-the-oms-gateway"></a>Zainstaluj bramę pakietu OMS

Aby zainstalować bramę, wykonaj następujące czynności.  Jeśli zainstalowano poprzednią wersję, nazywanych *Log Analytics usługi przesyłania dalej*, będzie można uaktualnić do tej wersji.  

1. Folder docelowy, kliknij dwukrotnie **OMS Gateway.msi**.
1. Na **stronie powitalnej** kliknij przycisk **Dalej**.<br><br> ![Kreator instalacji bramy](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
1. Na **umowy licencyjnej** wybierz opcję **akceptuję warunki umowy licencyjnej** zgodę na umowę licencyjną, a następnie kliknij przycisk **dalej**.
1. Na **portu i serwera proxy adres** strony:
   1. Wpisz numer portu TCP, który ma być używany dla bramy. Instalator konfiguruje regułę ruchu przychodzącego za pomocą tego numeru portu w Zaporze Windows.  Wartość domyślna to 8080.
      Prawidłowy zakres numer portu to 1-65535. Jeśli dane wejściowe nie należy do tego zakresu, pojawi się komunikat o błędzie.
   1. Opcjonalnie Jeśli serwer, na której zainstalowano bramę musi komunikować się za pośrednictwem serwera proxy, należy wpisać adres serwera proxy, gdy brama musi połączyć. Na przykład `http://myorgname.corp.contoso.com:80`.  Jeśli pole pozostanie puste, brama podejmie próbę bezpośrednie połączenie z Internetem.  Jeśli Twój serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło.<br><br> ![Konfiguracja serwera proxy kreatora bramy](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   1. Kliknij przycisk **Dalej**.
1. Jeśli nie masz jeszcze włączonej usługi Microsoft Update, zostanie wyświetlona strona usługi Microsoft Update, gdzie można je włączyć. Wybierz opcję, a następnie kliknij przycisk **dalej**. W przeciwnym razie przejdź do następnego kroku.
1. Na **Folder docelowy** strony, pozostaw domyślny folder C:\Program Files\OMS bramy lub wpisz lokalizację, w którym chcesz zainstalować bramę, a następnie kliknij przycisk **dalej**.
1. Na **gotowe do zainstalowania** kliknij **zainstalować**. Kontrola konta użytkownika, może pojawić się żądanie uprawnień do zainstalowania. Jeśli tak, kliknij przycisk **tak**.
1. Po zakończeniu instalacji kliknij przycisk **Zakończ**. Możesz sprawdzić, czy usługa jest uruchomiona, Otwieranie przystawki services.msc i upewnij się, że **bramy pakietu OMS** pojawia się na liście usług i jego stan jest **systemem**.<br><br> ![Usługi — Brama pakietu OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurowanie równoważenia obciążenia sieciowego 
Można skonfigurować bramy w celu zapewnienia wysokiej dostępności przy użyciu równoważenia obciążenia sieciowego (NLB) firmy Microsoft obciążenia równoważenia sieciowego (NLB) lub modułów równoważenia obciążenia oparte na sprzęcie.  Moduł równoważenia obciążenia zarządza ruchem, przekierowując żądanego połączenia z agentów pakietu OMS lub serwerów zarządzania programu Operations Manager w jego węzłach. Jeśli jeden serwer bramy ulegnie awarii, ruch jest kierowany do innych węzłów.

Aby dowiedzieć się, jak zaprojektować i wdrożyć klaster równoważenia obciążenia sieciowego systemu Windows Server 2016, zobacz [równoważenia obciążenia sieciowego](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Poniżej opisano sposób konfigurowania klastra równoważenia obciążenia sieciowego firmy Microsoft.  

1. Zaloguj się na serwerze Windows, który jest członkiem klastra równoważenia obciążenia Sieciowego przy użyciu konta administracyjnego.  
1. Otwórz Menedżera równoważenia obciążenia sieciowego w Menedżerze serwera, kliknij pozycję **narzędzia**, a następnie kliknij przycisk **Menedżera równoważenia obciążenia sieciowego**.
1. Aby połączyć się z serwerem bramy pakietu OMS z programu Microsoft Monitoring Agent zainstalowany, kliknij prawym przyciskiem myszy adres IP klastra, a następnie kliknij przycisk **Dodaj hosta do klastra**.<br><br> ![Sieć obciążenia równoważenia Manager — Dodaj hosta do klastra](./media/log-analytics-oms-gateway/nlb02.png)<br> 
1. Wprowadź adres IP serwera bramy, który chcesz się połączyć.<br><br> ![Sieci Menedżera równoważenia obciążenia — Dodaj hosta do klastra: łączenie](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Konfigurowanie agenta pakietu OMS i grupa zarządzania programu Operations Manager
Poniższa sekcja zawiera instrukcje dotyczące sposobu konfigurowania bezpośrednio połączonych agentów pakietu OMS, grupy zarządzania programu Operations Manager lub usługi Azure Automation hybrydowych — procesów roboczych Runbook za pomocą bramy pakietu OMS do komunikowania się z usługi Azure Automation i Log Analytics.  

### <a name="configure-standalone-oms-agent"></a>Konfigurowanie agenta pakietu OMS autonomiczny
Aby poznać wymagania i procedury dotyczące sposobu instalowania agenta pakietu OMS na komputerach Windows bezpośredniego połączenia z usługą Log Analytics, zobacz [Windows łączenie komputerów do usługi Log Analytics](log-analytics-windows-agents.md) lub dla systemu Linux, zobacz komputery [połączyć z systemem Linux komputery z usługą Log Analytics](log-analytics-quick-collect-linux-computer.md). Określanie serwera proxy podczas konfigurowania agenta, do miejsca można zastąpić tę wartość o adresie IP, serwer bramy pakietu OMS i jego numer portu.  Jeśli wdrożono wiele serwerów bramy za modułem równoważenia obciążenia sieciowego konfiguracji serwera proxy agenta pakietu OMS jest wirtualny adres IP Równoważenie obciążenia Sieciowego.  

Aby uzyskać informacje dotyczące automatyzacji hybrydowego procesu roboczego Runbook, zobacz [wdrażanie hybrydowego procesu roboczego Runbook](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Konfigurowanie programu Operations Manager — Wszyscy agenci korzystać z tego samego serwera proxy
Możesz skonfigurować program Operations Manager, aby dodać serwer bramy.  Konfiguracja serwera proxy programu Operations Manager jest automatycznie stosowany do wszystkich agentów raportujących do programu Operations Manager, nawet jeśli ustawienie jest pusty.  

Aby użyć bramy do obsługi programu Operations Manager, musisz mieć:

* Program Microsoft Monitoring Agent (wersja agenta — **8.0.10900.0** lub nowszej) zainstalowany na serwerze bramy i skonfigurowany do obszarów roboczych usługi Log Analytics, z którymi chcesz się komunikować.
* Brama musi mieć połączenie z Internetem lub być podłączone do serwera proxy, który wykonuje.

> [!NOTE]
> Jeśli nie określisz wartości dla bramy, wartości puste są wypychane do wszystkich agentów.
> 

Jeśli po raz pierwszy rejestruje grupę zarządzania programu Operations Manager z obszarem roboczym usługi Log Analytics, opcję, aby określić konfigurację serwera proxy dla grupy zarządzania nie jest dostępne w konsoli operacje.  Ta opcja staje się dostępna dopiero po pomyślnym zarejestrowaniu grupy zarządzania w usłudze.  Aby skonfigurować integrację i wszystkie serwery zarządzania w grupie zarządzania, musisz zaktualizować konfigurację serwera proxy systemu za pomocą narzędzia Netsh w systemie, w którym działa konsola zarządzania operacjami.  

1. Otwórz wiersz polecenia z podwyższonymi uprawnieniami.
   a. Przejdź do **Start** i typ **cmd**.
   b. Kliknij prawym przyciskiem myszy **polecenia** i wybierz polecenie Uruchom jako administrator **.
1. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po ukończeniu integracji z usługą Log Analytics, możesz usunąć zmiany, uruchamiając `netsh winhttp reset proxy` , a następnie użyj **skonfiguruj poświadczenia serwera proxy** opcję w konsoli operacje, aby określić serwer bramy pakietu OMS. 

1. Otwórz konsolę programu Operations Manager i w obszarze **pakietu Operations Management Suite**, kliknij przycisk **połączenia** a następnie kliknij przycisk **Konfiguruj serwer Proxy**.<br><br> ![Operations Manager — Konfiguracja serwera Proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
1. Wybierz **Użyj serwera proxy do dostępu do pakietu Operations Management Suite** i wpisz adres IP serwera bramy pakietu OMS lub wirtualny adres IP Równoważenie obciążenia Sieciowego. Upewnij się, że rozpoczynasz od `http://` prefiks.<br><br> ![Operations Manager — adres serwera proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
1. Kliknij przycisk **Zakończ**. Grupa zarządzania programu Operations Manager został skonfigurowany do komunikowania się za pośrednictwem serwera bramy do usługi Log Analytics.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurowanie programu Operations Manager — określonych agentów, Użyj serwera proxy
W przypadku dużych lub złożonych środowisk może chcesz tylko określonych serwerów (lub grupy), aby użyć serwera bramy pakietu OMS.  Na tych serwerach nie można zaktualizować agenta programu Operations Manager bezpośrednio, ponieważ ta wartość jest zastępowany przez wartość globalny dla grupy zarządzania.  Zamiast tego należy zastąpić regułę używaną do wypychania tych wartości.  

> [!NOTE] 
> Tej samej techniki konfiguracji może służyć do używania wielu serwerów bramy pakietu OMS w danym środowisku.  Na przykład może wymagać określonych serwerów bramy pakietu OMS, należy określić na podstawie regionu.
>  

1. Otwórz konsolę programu Operations Manager i wybierz **tworzenie** obszaru roboczego.  
1. W obszarze roboczym tworzenie wybierz **reguły** i kliknij przycisk **zakres** przycisk na pasku narzędzi programu Operations Manager. Jeśli ten przycisk jest niedostępny, sprawdź, upewnij się, że wybrany obiekt, nie folder, w okienku monitorowanie. **Zakres obiektów pakietu administracyjnego** okno dialogowe wyświetla listę typowych klas docelowych, grup lub obiektów. 
1. Typ **usługa kondycji** w **poszukaj** pola, a następnie wybierz ją z listy.  Kliknij przycisk **OK**.  
1. Wyszukaj regułę **reguła ustawienia serwera Proxy usługi Advisor** a na pasku narzędzi konsoli operacje kliknij **zastępuje** i wskaż **zastąpienia Rule\For konkretnego obiektu klasy: Usługa kondycji**  i wybrać konkretny obiekt z listy.  Opcjonalnie można utworzyć niestandardowe grupy zawierającej obiekt usługi kondycji serwerów, które chcesz zastosować przesłonięcia w celu, a następnie zastosować zastąpienia do tej grupy.
1. W **właściwości zastąpienia** okno dialogowe, kliknij, aby zaznaczyć pole wyboru w **zastąpienia** obok kolumny **WebProxyAddress** parametru.  W **wartość zastąpienia** wprowadź adres URL z koniecznością serwera bramy pakietu OMS, rozpoczynającego się `http://` prefiks.  

    >[!NOTE]
    > Nie trzeba włączyć reguły, ponieważ jest już zarządzane automatycznie za pomocą zastąpienia zawartych w pakiecie administracyjnym programu Microsoft Center Advisor bezpiecznego odwołania zastąpienie systemowe przeznaczone dla Microsoft grupy System Center Advisor Monitoring Server.
    >   

1. Wybierz pakiet administracyjny z **wybierz docelowy pakiet administracyjny** listy lub Utwórz nowy niezapieczętowany pakiet administracyjny, klikając **New**. 
1. Po zakończeniu zmiany, kliknij przycisk **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Konfigurowanie dla usługi automation hybrydowych procesów roboczych
Jeśli masz automatyzacji hybrydowych procesów roboczych Runbook w środowisku, w poniższych krokach przedstawiono ręczne, tymczasowego obejścia, aby skonfigurować bramę do ich obsługi.

W poniższych krokach musisz wiedzieć region platformy Azure, w której znajduje się konto usługi Automation. Aby znaleźć lokalizację:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz usługę Azure Automation.
1. Wybierz odpowiednie konto usługi Azure Automation.
1. Wyświetl regionie na podstawie **lokalizacji**.<br><br> ![Lokalizacja konta usługi Automation z witryny Azure portal](./media/log-analytics-oms-gateway/location.png)  

Aby zidentyfikować adres URL dla każdej lokalizacji, należy użyć następujących tabel:

**Zadanie adresów URL usługi danych dla środowiska uruchomieniowego**

| **location** | **Adres URL** |
| --- | --- |
| Środkowo-północne stany USA |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net |
| Środkowo-południowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Kanada centralnej |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japonia |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia |ase-jobruntimedata-prod-su1.azure-automation.net |

**Adresy URL usługi agenta**

| **location** | **Adres URL** |
| --- | --- |
| Środkowo-północne stany USA |ncus-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-agentservice-prod-1.azure-automation.net |
| Środkowo-południowe stany USA |scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-agentservice-prod-1.azure-automation.net |
| Kanada centralnej |cc-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-agentservice-prod-1.azure-automation.net |
| Indie Środkowe |cid-agentservice-prod-1.azure-automation.net |
| Japonia |jpe-agentservice-prod-1.azure-automation.net |
| Australia |ase-agentservice-prod-1.azure-automation.net |

Jeśli komputer jest zarejestrowany jako hybrydowy proces roboczy elementu Runbook, automatycznie stosowania poprawek za pomocą rozwiązania do zarządzania aktualizacjami, wykonaj następujące kroki:

1. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy dozwolone hosta na bramę pakietu OMS. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy pakietu OMS przy użyciu następującego polecenia cmdlet programu PowerShell: `Restart-Service OMSGatewayService`

Jeśli komputer jest uruchomiona do usługi Azure Automation za pomocą polecenia cmdlet rejestracji hybrydowego procesu roboczego Runbook, wykonaj następujące czynności:

1. Adres URL rejestracji usługi agenta należy dodać do listy dozwolone hosta na bramę pakietu OMS. Na przykład: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy dozwolone hosta na bramę pakietu OMS. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy pakietu OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Przydatne poleceń cmdlet programu PowerShell
Polecenia cmdlet ułatwia wykonywanie zadań, które są niezbędne do aktualizowania ustawień konfiguracji bramy pakietu OMS. Zanim można ich używać, należy koniecznie:

1. Zainstaluj bramę pakietu OMS (MSI).
1. Otwórz okno konsoli programu PowerShell.
1. Aby zaimportować moduł, wpisz następujące polecenie: `Import-Module OMSGateway`
1. Jeśli żaden błąd nie wystąpił w poprzednim kroku, moduł został pomyślnie zaimportowany i używać poleceń cmdlet. Typ `Get-Module OMSGateway`
1. Po wprowadzeniu zmian przy użyciu polecenia cmdlet upewnij się, uruchom ponownie usługę bramy.

Jeśli wystąpi błąd w kroku 3, moduł nie został zaimportowany. Ten błąd może wystąpić po nie można odnaleźć modułu programu PowerShell. Znajdziesz go w ścieżce instalacji bramy: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Polecenia cmdlet** | **Parametry** | **Opis** | **Przykład** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klucz |Pobiera konfigurację usługi |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klucz (wymagane) <br> Wartość |Umożliwia zmianę konfiguracji usługi |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Pobiera adres serwera proxy (nadrzędnego) przekazywania |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Nazwa użytkownika<br> Hasło |Ustawia adres (i poświadczeń) przekazywania (nadrzędnego) serwera proxy |1. Ustaw serwer proxy usługi relay i poświadczenia:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ustaw serwer proxy usługi relay, która nie wymaga uwierzytelniania: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wyczyść ustawienia serwera proxy usługi relay:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Pobiera obecnie dozwolone hosta (tylko lokalnie skonfigurowanych dozwolonych hosta, nie ma automatycznie pobrany dozwolonych hostów) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hosta (wymagane) |Dodanie hosta do listy dozwolonych |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hosta (wymagane) |Usuwa hosta z listy dozwolonych |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Dodaje certyfikat klienta z zastrzeżeniem listy dozwolonych |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Usuwa podmiot certyfikatu klienta z białej listy |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Pobiera obecnie dozwolone klienta podmiotom certyfikatów (tylko lokalnie skonfigurowanych dozwolone przedmioty, nie obejmuje automatycznie pobrany dozwolonych podmiotów) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Aby zbierać zdarzenia zarejestrowane przez bramę, musisz również mieć zainstalowany agent pakietu OMS.<br><br> ![Podgląd zdarzeń — dziennik bramy pakietu OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identyfikatory zdarzeń bramy pakietu OMS i opisów**

W poniższej tabeli przedstawiono identyfikatorów zdarzeń i opisów dla zdarzenia dziennika bramy pakietu OMS.

| **Identyfikator** | **Opis** |
| --- | --- |
| 400 |Wszelkie błędy aplikacji, który nie ma określonego Identyfikatora |
| 401 |Niewłaściwej konfiguracji. Na przykład: listenPort = "text", a nie liczbą całkowitą |
| 402 |Wyjątek podczas analizowania komunikatów uzgadniania TLS |
| 403 |Błąd sieci. Na przykład: nie można nawiązać połączenia z serwerem docelowym |
| 100 |Informacje ogólne |
| 101 |Usługa została uruchomiona |
| 102 |Usługa została zatrzymana |
| 103 |Odebrano polecenie połączenia protokołu HTTP z klienta |
| 104 |Nie polecenie połączenia protokołu HTTP |
| 105 |Serwer docelowy nie ma na liście dozwolonych lub port docelowy nie jest bezpieczny port (port 443) <br> <br> Upewnij się, że agenta MMA na serwerze bramy oraz agentów podczas komunikowania się za pomocą bramy są podłączone do tego samego obszaru roboczego usługi Log Analytics. |
| 105 |Błąd TcpConnection — nieprawidłowy certyfikat klienta: CN = bramy <br><br> Upewnij się, że: <br>    <br> &#149;Brama korzystają z numerem wersji 1.0.395.0 lub nowszej. <br> &#149;Agenta MMA na serwerze bramy oraz agentów podczas komunikowania się za pomocą bramy są podłączone do tego samego obszaru roboczego usługi Log Analytics. |
| 106 |Brama pakietu OMS obsługuje tylko protokół TLS 1.0, TLS 1.1 i 1.2.  Nie obsługuje protokołu SSL. Dla dowolnego nieobsługiwanej wersji protokołu TLS/SSL bramy pakietu OMS generuje zdarzenie 106 identyfikator.|
| 107 |Zweryfikowano sesji protokołu TLS |

**Liczniki wydajności do zbierania**

W poniższej tabeli przedstawiono dostępne liczniki wydajności dla bramy pakietu OMS. Dodaj liczniki, korzystanie z Monitora wydajności.

| **Nazwa** | **Opis** |
| --- | --- |
| Połączenie klienta bramy/aktywny pakietu OMS |Liczba aktywnych połączeń z klientami sieci (TCP) |
| Liczba błąd/bramy pakietu OMS |Liczba błędów |
| Klient połączony bramy pakietu OMS |Liczba połączonych klientów |
| Liczba bram/odrzucenia pakietu OMS |Liczba odrzuceń z powodu dowolnego błędu sprawdzania poprawności protokołu TLS |

![Liczniki wydajności bramy pakietu OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Uzyskaj pomoc
Po zalogowaniu się witrynie Azure Portal, można utworzyć żądanie pomocy za pomocą bramy pakietu OMS lub innych usług platformy Azure lub funkcji usługi.
Aby poprosić o pomoc, kliknij symbol znaku zapytania w prawym górnym rogu portalu, a następnie kliknij przycisk **nowe żądanie obsługi**. Wypełnij formularz nowego żądania pomocy technicznej.

![Nowe żądanie pomocy technicznej](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie źródeł danych](log-analytics-data-sources.md) do zbierania danych z połączonych źródeł i zapisać ją w obszarze roboczym usługi Log Analytics.
