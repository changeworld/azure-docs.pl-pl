---
title: Łączenie komputerów przy użyciu bramy OMS | Dokumentacja firmy Microsoft
description: Uzyskuj dostęp do Twojego urządzenia i komputery monitorowane programu Operations Manager bramy OMS do przesyłania danych do usługi Automatyzacja Azure i usługi analizy dzienników, gdy nie mają dostępu do Internetu.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: magoedte
ms.openlocfilehash: b3055e6b22e3f391c0bc3f321cd8117d55a95cf5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271653"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Łączenie komputerów bez dostępu do Internetu za pomocą bramy OMS
W tym dokumencie opisano sposób konfigurowania komunikacji przy użyciu automatyzacji Azure i analizy dzienników przy użyciu bramy OMS, gdy bezpośrednie połączenia lub Operations Manager monitorowane komputery nie mają dostępu do Internetu.  Brama OMS, czyli protokołu HTTP do przodu serwera proxy, który obsługuje tunelowania przy użyciu połączenia HTTP polecenia HTTP, można zbierać dane i przesyła do automatyzacji Azure i analizy dzienników w ich imieniu.  

Brama OMS umożliwia:

* Hybrydowymi elementami roboczymi Runbook usługi Automatyzacja Azure  
* Komputery z systemem Windows z programu Microsoft Monitoring Agent jest podłączony bezpośrednio do obszaru roboczego analizy dzienników
* Komputery z systemem Linux z agentem pakietu OMS Linux bezpośrednio podłączone do obszaru roboczego analizy dzienników  
* System Center Operations Manager 2012 SP1 z pakietem UR7, Operations Manager 2012 R2 UR3, Operations Manager 2016 i grupę zarządzania programu Operations Manager w wersji 1801 zintegrowane z usługą analizy dzienników.  

Jeśli zasady zabezpieczeń IT nie zezwalaj na komputerach w sieci, aby nawiązać połączenie z Internetem, takie jak punkt urządzeń sprzedaży (POS) lub usługi IT, pomocnicze serwery, ale musisz podłącz je do usługi Automatyzacja Azure lub analizy dzienników, zarządzanie i monitorowanie ich , można je skonfigurować do bezpośredniego komunikowania się z bramą OMS odbierać konfigurację i przekazują dane w ich imieniu.  Jeśli te komputery są skonfigurowane z agentem pakietu OMS bezpośrednio z obszaru roboczego analizy dzienników, wszystkie komputery zamiast niego będą komunikować się z bramy OMS.  Bramy przesyła dane z agentów do usługi bezpośrednio, nie analizuje dane podczas przesyłania.

Gdy grupa zarządzania programu Operations Manager jest zintegrowany z analizy dzienników, serwery zarządzania można skonfigurować do nawiązania połączenia bramy OMS do odbierania informacji o konfiguracji i wysyłania danych zebranych w zależności od rozwiązania, które aktywowano.  Agenci programu Operations Manager wysyłać niektóre dane, takie jak alerty programu Operations Manager, oceny konfiguracji, miejsce zajmowane przez wystąpienia i pojemności danych na serwerze zarządzania. Inne dane dużych, takich jak dzienniki programu IIS, wydajności i zdarzeń zabezpieczeń są wysyłane bezpośrednio do bramy OMS.  Jeśli masz wdrożone w strefa DMZ lub innych izolowaną sieć, do monitorowania systemów niezaufanych jeden lub więcej serwerów bramę programu Operations Manager nie może komunikować się z bramą OMS.  Serwery programu Operations Manager bramy tylko może raportować do serwera zarządzania.  Po skonfigurowaniu grupy zarządzania programu Operations Manager do komunikowania się z bramą OMS informacje o konfiguracji serwera proxy jest automatycznie dystrybuowana do co komputer zarządzany przez agenta, który jest skonfigurowany do zbierania danych do analizy dzienników nawet wtedy, gdy ustawienie jest puste.    

Aby zapewnić wysoką dostępność bezpośrednio połączone lub grup zarządzania operacje, które komunikują się z analizy dzienników za pośrednictwem bramy, skorzystać z równoważenia obciążenia sieciowego do przekierowywania i dystrybuowanie ruchu między wieloma serwerami bramy.  Jeśli jeden serwer bramy ulegnie awarii, ruch jest przekierowywany do innego węzła dostępne.  

Agent pakietu OMS jest wymagany na komputerze z uruchomionym OMS bramy w celu identyfikacji punktów końcowych usługi potrzebne do komunikowania się z i monitorowanie bramy OMS do analizowania jego wydajności lub dane zdarzenia.

Każdy agent musi mieć łączność sieciową z bramy, tak aby agentów automatycznie przesyłania danych do i z bramy. Nie jest zalecane instalowanie bramy na kontrolerze domeny.

Na poniższym diagramie przedstawiono przepływ danych przez bezpośrednie agentów do automatyzacji Azure i analizy dzienników przy użyciu serwera bramy.  Agenci musi mieć konfigurację serwera proxy, ich dopasowanie tego samego portu, bramy OMS jest skonfigurowany do komunikowania się z usługą.  

![komunikacji agenta bezpośrednio z diagramu usług](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Na poniższym diagramie przedstawiono przepływ danych z grupy zarządzania programu Operations Manager do analizy dzienników.   

![Operations Manager komunikację z diagramu analizy dzienników](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Wymagania wstępne

Podczas wyznaczania komputer, aby uruchomić bramę OMS, ten komputer musi mieć następujące czynności:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, w systemie Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* Program .net framework 4.5
* Co najmniej 4 rdzenie procesora i 8 GB pamięci 
* Agent pakietu OMS dla systemu Windows 

### <a name="language-availability"></a>Dostępność języka

Brama OMS jest dostępna w następujących językach:

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
- Hiszpański (Międzynarodowa)

### <a name="supported-encryption-protocols"></a>Szyfrowania obsługiwanych protokołów
Brama OMS obsługuje tylko zabezpieczeń TLS (Transport Layer) 1.0, 1.1 i 1.2.  Nie obsługuje protokołu Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Obsługiwana liczba połączeń agenta
Poniższa tabela prezentuje obsługiwanej liczby agentów komunikację z serwerem bramy.  Ta obsługa jest oparta na agentów przekazywania ~ 200KB danych co 6 sekund. Ilość danych na badane agenta jest około 2.7GB na dzień.

|Brama |Szacunkowa liczba agentów obsługiwane|  
|--------|----------------------------------|  
|-Procesora: Intel XEON E5 Procesora 2660 v3 @ 2.6GHz 2 rdzeni<br> -Pamięci: 4 GB<br> — Przepustowość sieci: 1 GB/s| 600|  
|-Procesora: Intel XEON E5 Procesora 2660 v3 @ 2.6GHz 4 rdzenie<br> -Pamięci: 8 GB<br> — Przepustowość sieci: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Pobierz bramę OMS

Istnieją dwa sposoby, aby pobrać najnowszą wersję pliku instalacyjnego bramy OMS.

1. Pobierz z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Pobierz z portalu Azure.  Po zalogowaniu się w portalu Azure:  

   1. Przejrzyj listę usług, a następnie wybierz **analizy dzienników**.  
   2. Wybierz obszar roboczy.
   3. W bloku w obszarze roboczym **ogólne**, kliknij przycisk **Szybki Start**.
   4. W obszarze **wybierz źródło danych do nawiązania połączenia obszaru roboczego**, kliknij przycisk **komputerów**.
   5. W **bezpośredniego agenta** bloku, kliknij przycisk **Pobierz bramy OMS**.<br><br> ![Pobierz OMS bramy](./media/log-analytics-oms-gateway/download-gateway.png)

lub 

   1. W bloku w obszarze roboczym **ustawienia**, kliknij przycisk **Zaawansowane ustawienia**.
   2. Przejdź do **połączonych źródeł** > **serwerów z systemem Windows** i kliknij przycisk **Pobierz bramy OMS**.

## <a name="install-the-oms-gateway"></a>Zainstaluj bramę OMS

Aby zainstalować bramę, wykonaj następujące kroki.  Jeśli zainstalowano poprzednią wersję, nazywanych *dziennika analizy usługi przesyłania dalej*, zostaną uaktualnione do tej wersji.  

1. Folder docelowy, kliknij dwukrotnie **OMS Gateway.msi**.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.<br><br> ![Kreator instalacji bramy](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Na **umowy licencyjnej** wybierz pozycję **akceptuję warunki umowy licencyjnej** zgodę na umowę licencyjną, a następnie kliknij przycisk **dalej**.
4. Na **portu i serwera proxy adres** strony:
   1. Wpisz numer portu TCP używanego do bramy. Instalator konfiguruje regułę ruchu przychodzącego z tego numeru portu w Zaporze systemu Windows.  Wartość domyślna to 8080.
      Prawidłowy zakres numer portu to 1-65535. Jeśli dane wejściowe nie należy do tego zakresu, zostanie wyświetlony komunikat o błędzie.
   2. Opcjonalnie Jeśli serwer, na której zainstalowano bramę musi komunikować się za pośrednictwem serwera proxy, wpisz adres serwera proxy, gdy brama musi połączyć. Na przykład `http://myorgname.corp.contoso.com:80`.  Jeżeli pole pozostanie puste, spróbuj połączyć się z Internetem bezpośrednio bramy.  Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło.<br><br> ![Konfiguracja serwera proxy kreatora bramy](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Kliknij przycisk **Dalej**.
5. Jeśli nie ma włączone usługi Microsoft Update, zostanie wyświetlona strona usługi Microsoft Update, gdzie można ją włączyć. Wybierz odpowiednią opcję, a następnie kliknij przycisk **dalej**. W przeciwnym razie przejdź do kolejnego etapu.
6. Na **Folder docelowy** strony, pozostaw domyślny folder C:\Program Files\OMS bramy lub wpisz lokalizację, w którym chcesz zainstalować bramę, a następnie kliknij przycisk **dalej**.
7. Na **gotowe do zainstalowania** kliknij przycisk **zainstalować**. Kontrola konta użytkownika może pojawiać się żądania uprawnienia do zainstalowania. Jeśli tak, kliknij przycisk **tak**.
8. Po zakończeniu instalacji kliknij przycisk **Zakończ**. Można sprawdzić, czy usługa jest uruchomiona przez otwarcie przystawki services.msc i upewnij się, że **bramy OMS** pojawią się na liście usług i jego stan jest **systemem**.<br><br> ![Usługi — Brama OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurowanie równoważenia obciążenia sieciowego 
Można skonfigurować bramy w celu zapewnienia wysokiej dostępności przy użyciu równoważenia obciążenia sieciowego (NLB) firmy Microsoft sieci równoważenia obciążenia (NLB) lub usługi równoważenia obciążenia oparte na sprzęcie.  Moduł równoważenia obciążenia zarządza ruchu przez przekierowywanie żądanego połączenia z usługą OMS agentów lub serwery zarządzania programu Operations Manager w jego węzłach. Jeśli jeden serwer bramy ulegnie awarii, ruch jest kierowany do innych węzłów.

Aby dowiedzieć się więcej o projektowaniu i wdrożyć klaster równoważenia obciążenia sieciowego systemu Windows Server 2016, zobacz [równoważenia obciążenia sieciowego](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  W poniższych krokach opisano sposób konfigurowania klastra równoważenia obciążenia sieciowego firmy Microsoft.  

1. Zaloguj się na serwerze z systemem Windows, należącego do klastra równoważenia obciążenia Sieciowego przy użyciu konta administracyjnego.  
2. Otwórz Menedżera równoważenia obciążenia sieciowego w Menedżerze serwera, kliknij przycisk **narzędzia**, a następnie kliknij przycisk **Menedżera równoważenia obciążenia sieciowego**.
3. Aby połączyć się z serwerem bramy OMS z programu Microsoft Monitoring Agent zainstalowany, kliknij prawym przyciskiem myszy adres IP klastra, a następnie kliknij przycisk **Dodaj hosta do klastra**.<br><br> ![Obciążenia w sieci, Menedżer równoważenia — Dodaj hosta do klastra](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Wprowadź adres IP serwera bramy, którym chcesz się połączyć.<br><br> ![Sieci Menedżera równoważenia obciążenia — Dodaj hosta do klastra: połączenie](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Konfigurowanie agenta pakietu OMS i grupy zarządzania programu Operations Manager
Poniższa sekcja zawiera kroki dotyczące sposobu konfigurowania bezpośrednio połączone OMS agentów grupy zarządzania programu Operations Manager i Azure Automation hybrydowymi elementami roboczymi Runbook z bramą OMS do komunikacji z usługi Automatyzacja Azure Log Analytics.  

### <a name="configure-standalone-oms-agent"></a>Konfigurowanie agenta pakietu OMS autonomiczny
Aby poznać wymagania i kroki dotyczące instalowania agenta pakietu OMS na komputerach z systemem Windows bezpośredniego połączenia analizy dzienników, zobacz [połączyć komputery do analizy dzienników](log-analytics-windows-agents.md) lub Linux komputerów znajduje się w temacie [połączyć systemu Linux komputery z analizą dzienników](log-analytics-quick-collect-linux-computer.md). Określanie serwera proxy podczas konfigurowania agenta, do miejsca tej wartości Zamień adres IP serwera bramy OMS i numer portu.  Jeśli wdrożono wiele serwerów bramy za modułem równoważenia obciążenia sieciowego, konfiguracji serwera proxy agenta pakietu OMS jest wirtualny adres IP Równoważenie obciążenia Sieciowego.  

Aby uzyskać informacje dotyczące automatyzacji hybrydowy proces roboczy, zobacz [wdrożenia hybrydowego procesu roboczego elementu Runbook](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Konfigurowanie programu Operations Manager — wszystkich agentów, użyj tego samego serwera proxy
Możesz skonfigurować programu Operations Manager, aby dodać serwer bramy.  Konfiguracja serwera proxy programu Operations Manager jest automatycznie stosowane do wszystkich agentów podlegających serwerowi programu Operations Manager, nawet jeśli ustawienie jest pusta.  

Aby użyć bramy do obsługi programu Operations Manager, musi mieć:

* Microsoft Monitoring Agent (wersja agenta — **8.0.10900.0** lub nowszym) zainstalowany na serwerze bramy i skonfigurowany do analizy dzienników obszarów roboczych, z którymi chcesz się komunikować.
* Bramy muszą mieć połączenie z Internetem lub być podłączone do serwera proxy, który wykonuje.

> [!NOTE]
> Jeśli nie określisz wartości dla bramy, puste wartości jest przypisany do wszystkich agentów.
> 

Jeśli jest to rejestruje grupę zarządzania programu Operations Manager z obszaru roboczego analizy dzienników po raz pierwszy, opcję, aby określić konfigurację serwera proxy dla grupy zarządzania nie jest dostępne w konsoli operacje.  Grupa zarządzania nie mają być pomyślnie zarejestrowane w usłudze, zanim ta opcja jest dostępna.  Musisz zaktualizować konfigurację serwera proxy systemu za pomocą narzędzia Netsh w systemie konsoli operacje z systemem do skonfigurowania integracji i wszystkich serwerów zarządzania w grupie zarządzania.  

1. Otwórz podniesionego wiersza poleceń.
   a. Przejdź do **Start** i typ **cmd**.
   b. Kliknij prawym przyciskiem myszy **wiersza polecenia** i wybierz polecenie Uruchom jako administrator **.
2. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po zakończeniu integracji z analizy dzienników, można usunąć zmiany, uruchamiając `netsh winhttp reset proxy` , a następnie użyj **Konfiguruj serwer proxy** opcję w konsoli operacje, aby określić serwer bramy OMS. 

1. Otwórz konsolę programu Operations Manager i w obszarze **Operations Management Suite**, kliknij przycisk **połączenia** , a następnie kliknij przycisk **Konfiguracja serwera Proxy**.<br><br> ![Operations Manager — Konfiguracja serwera Proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Wybierz **Użyj serwera proxy, aby uzyskać dostępu do usługi Operations Management Suite** , a następnie wpisz adres IP serwera bramy OMS lub wirtualny adres IP Równoważenie obciążenia Sieciowego. Upewnij się, że rozpoczyna `http://` prefiks.<br><br> ![Operations Manager — adres serwera proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Kliknij przycisk **Zakończ**. Grupę zarządzania programu Operations Manager został skonfigurowany do komunikowania się za pośrednictwem serwera bramy z usługą analizy dzienników.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurowanie programu Operations Manager — określonych agentów Użyj serwera proxy
W przypadku środowisk dużych lub złożonych tylko możesz określonych serwerów (lub grupy), aby użyć serwera bramy OMS.  Na tych serwerach nie można zaktualizować agenta programu Operations Manager bezpośrednio, ponieważ ta wartość zostanie zastąpiona wartości globalnej grupy zarządzania.  Zamiast tego należy Zastąp zasadę wykorzystane do zmuszenia tych wartości.  

> [!NOTE] 
> Ten sam sposób konfiguracji może służyć do korzystania z wielu serwerów bramy OMS w danym środowisku.  Na przykład może wymagać określonych serwerów bramy OMS należy określić na podstawie na region.
>  

1. Otwórz konsolę programu Operations Manager i wybierz **tworzenie** obszaru roboczego.  
2. W obszarze roboczym tworzenie, wybierz **reguły** i kliknij przycisk **zakres** przycisk na pasku narzędzi programu Operations Manager. Jeśli ten przycisk jest niedostępny, sprawdź, upewnij się, że wybrany obiekt, nie folder, w okienku monitorowanie. **Zakres obiektów pakietu administracyjnego** okno dialogowe wyświetla listę typowych klasy docelowej, grupami lub obiektami. 
3. Typ **usługi kondycji** w **Wyszukaj** pola i wybierz go z listy.  Kliknij przycisk **OK**.  
4. Wyszukaj regułę **regułę ustawienie serwera Proxy usługi Advisor** i na pasku narzędzi konsoli operacje kliknij **zastępuje** , a następnie wskaż polecenie **zastąpienia Rule\For konkretnego obiektu klasy: usługi kondycji**  i wybierz konkretnego obiektu z listy.  Można opcjonalnie utworzyć grupę niestandardowego obiektu usługi kondycji serwerów, które chcesz zastosować to zastąpienie, a następnie zastosować zastąpienia tej grupy.
5. W **właściwości zastąpienia** okno dialogowe, kliknij, aby zaznaczyć w **zastąpienia** obok kolumny **WebProxyAddress** parametru.  W **wartość zastąpienia** wprowadź adres URL sprawdzeniu serwera bramy OMS rozpoczynających się od `http://` prefiks.  

    >[!NOTE]
    > Nie trzeba włączyć reguły, ponieważ jest już zarządzany automatycznie za pomocą zastąpienia zawartych w pakiecie administracyjnym programu Microsoft System Center Advisor bezpiecznego odwołania Override przeznaczonych dla Microsoft System Center Advisor monitorowania grupie serwerów.
    >   

6. Wybierz pakiet administracyjny z **wybierz docelowy pakiet administracyjny** listy lub utworzenie nowego niezapieczętowanego pakietu przez kliknięcie przycisku **nowy**. 
7. Po zakończeniu zmiany, kliknij przycisk **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Konfigurowanie dla automatyzacji hybrydowych procesów roboczych
Jeśli masz automatyzacji hybrydowymi elementami roboczymi Runbook w środowisku, w poniższych krokach przedstawiono ręczne, tymczasowego obejścia, aby skonfigurować bramę do ich obsługi.

W poniższych krokach musisz wiedzieć region platformy Azure, w którym znajduje się na koncie automatyzacji. Aby znaleźć lokalizację:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz usługi Automatyzacja Azure.
3. Wybierz odpowiednie konto usługi Automatyzacja Azure.
4. Wyświetl jego regionu, w obszarze **lokalizacji**.<br><br> ![Portal Azure — Lokalizacja konta automatyzacji](./media/log-analytics-oms-gateway/location.png)  

Aby zidentyfikować adres URL dla każdej lokalizacji, należy użyć następujących tabel:

**Zadanie adresy URL usługi danych dla środowiska uruchomieniowego**

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

Jeśli komputer jest zarejestrowany jako hybrydowy proces roboczy elementu Runbook automatycznie dla stosowania poprawek za pomocą rozwiązania do zarządzania aktualizacjami, wykonaj następujące kroki:

1. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy hostów dozwolone w bramie OMS. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Uruchom ponownie usługę OMS bramy przy użyciu następującego polecenia cmdlet programu PowerShell: `Restart-Service OMSGatewayService`

Jeśli komputer jest na dodawanej do automatyzacji Azure za pomocą polecenia cmdlet rejestracji hybrydowy proces roboczy elementu Runbook, wykonaj następujące kroki:

1. Dodaj adres URL rejestracji usługi agenta do listy hostów dozwolone w bramie OMS. Na przykład: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy hostów dozwolone w bramie OMS. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Uruchom ponownie usługę bramy OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Przydatne poleceń cmdlet programu PowerShell
Polecenia cmdlet ułatwiają wykonywanie zadań, które są potrzebne, aby zaktualizować ustawienia konfiguracji bramy OMS. Przed ich użyciem, należy koniecznie:

1. Zainstaluj bramę OMS (MSI).
2. Otwórz okno konsoli programu PowerShell.
3. Aby zaimportować moduł, wpisz następujące polecenie: `Import-Module OMSGateway`
4. Jeśli błąd wystąpił w poprzednim kroku, moduł został pomyślnie zaimportowany i można użyć poleceń cmdlet. Typ `Get-Module OMSGateway`
5. Po wprowadzeniu zmian przy użyciu poleceń cmdlet, upewnij się, uruchom ponownie usługę bramy.

Jeśli wystąpi błąd w kroku 3, moduł nie został zaimportowany. Błąd może wystąpić, gdy nie będzie mógł odnaleźć modułu programu PowerShell. Możesz go znaleźć w ścieżce instalacji bramy: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Polecenia cmdlet** | **Parametry** | **Opis** | **Przykład** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klucz |Pobiera konfigurację usługi |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klucz (wymagane) <br> Wartość |Umożliwia zmianę konfiguracji usługi |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Pobiera adres serwera proxy (nadrzędnego) przekazywania |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Nazwa użytkownika<br> Hasło |Ustawia adres (i poświadczeń) przekazywania proxy (nadrzędnego) |1. Ustaw proxy przekazywania i poświadczeń:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ustaw przekazywania serwer proxy, który nie wymaga uwierzytelniania: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wyczyść ustawienie serwera proxy przekazywania:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Pobiera obecnie dozwolone hosta (tylko lokalnie skonfigurowanych dozwolonych hosta, nie ma automatycznie pobrane dozwolonych hostów) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (wymagane) |Dodaje hosta do listy dozwolonych |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (wymagane) |Usuwa hosta z listy dozwolonych |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Dodaje certyfikat klienta może ulec listy dozwolonych |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Usuwa podmiotu certyfikatu klienta z białej listy |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Pobiera obecnie dozwolone klienta podmiotom certyfikatów (lokalnie skonfigurowanych dozwolone tylko tematów, nie ma automatycznie pobrane tematów dozwolonych) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Aby zbierać zdarzenia zarejestrowane przez bramę, musisz również jest zainstalowany agent pakietu OMS.<br><br> ![Podgląd zdarzeń — dziennik bramy OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identyfikatory zdarzeń bramy OMS wraz z opisami**

W poniższej tabeli przedstawiono identyfikatory zdarzeń i opisy zdarzeń w dzienniku bramy OMS.

| **Identyfikator** | **Opis** |
| --- | --- |
| 400 |Błąd żadnych aplikacji, który nie ma identyfikator unikatowy |
| 401 |Złej konfiguracji. Na przykład: listenPort = "tekst", a liczba całkowita |
| 402 |Wyjątek podczas analizowania wiadomości uzgadnianie TLS |
| 403 |Wystąpił błąd sieci. Na przykład: nie można nawiązać połączenia z serwerem docelowym |
| 100 |Informacje ogólne |
| 101 |Usługa została uruchomiona |
| 102 |Usługa została zatrzymana |
| 103 |Odebrano polecenie połączenia HTTP z klienta |
| 104 |Nie poleceń POŁĄCZYĆ HTTP |
| 105 |Serwer docelowy nie jest na liście dozwolonych lub port docelowy nie jest bezpieczny port (port 443) <br> <br> Upewnij się, że agent MMA na serwerze bramy i agenci komunikowania się z bramą są podłączone do tego samego obszaru roboczego analizy dzienników. |
| 105 |Błąd TcpConnection — nieprawidłowy certyfikat: CN = bramy <br><br> Upewnij się, że: <br>    <br> &#149;Korzystania z bramy z numerem wersji 1.0.395.0 lub nowszej. <br> &#149;Agent MMA na serwerze bramy oraz agentów komunikowania się z bramą są podłączone do tego samego obszaru roboczego analizy dzienników. |
| 106 |Brama OMS obsługuje tylko protokołu TLS 1.0, TLS 1.1 i 1.2.  Nie obsługuje protokołu SSL. Dla dowolnego nieobsługiwanej wersji protokołu TLS/SSL bramy OMS generuje 106 identyfikator zdarzenia.|
| 107 |Zweryfikowano sesji protokołu TLS |

**Liczniki wydajności do**

W poniższej tabeli przedstawiono dostępne liczniki wydajności bramy OMS. Możesz dodać liczniki Monitora wydajności.

| **Nazwa** | **Opis** |
| --- | --- |
| Połączenie klienta bramy/aktywny OMS |Liczba aktywnych połączeń z klientami sieci (TCP) |
| Liczba błędów/bramy OMS |Liczba błędów |
| OMS/połączenia bramy klienta |Liczba połączonych klientów |
| Liczba bramy/odrzucenia OMS |Liczba odrzuceń z powodu błędu sprawdzania poprawności TLS |

![Liczniki wydajności bramy OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Uzyskiwanie pomocy
Jeśli logujesz się do portalu Azure, można utworzyć żądanie pomocy z bramy OMS lub innych usług Azure lub funkcji usługi.
Aby poprosić o pomoc, kliknij symbol znak zapytania w prawym górnym rogu portalu, a następnie kliknij przycisk **nowy obsługuje żądania**. Następnie należy wykonać nowego formularza żądania pomocy technicznej.

![Nowe żądanie pomocy technicznej](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Kolejne kroki
[Dodaj źródła danych](log-analytics-data-sources.md) do zbierania danych z połączonych źródeł i zapisz go w obszarze roboczym analizy dzienników.
