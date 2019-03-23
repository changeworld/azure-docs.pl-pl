---
title: Łączenie komputerów przy użyciu bramy usługi Log Analytics | Dokumentacja firmy Microsoft
description: Połącz swoje urządzenia i komputery monitorowane przez menedżera operacji przy użyciu bramy usługi Log Analytics do wysyłania danych do usługi Azure Automation i usługę Log Analytics, gdy nie mają dostępu do Internetu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: 47b589d32accc4a699e7260b9e4b2de4cca58f2b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369619"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Łączenie komputerów bez dostępu do Internetu za pomocą bramy usługi Log Analytics w usłudze Azure Monitor

>[!NOTE]
>Zgodnie z programu Microsoft Operations Management Suite (OMS) przejścia do systemu Microsoft Azure Monitor, terminologii ulegnie zmianie. Ten artykuł odnosi się do bramy pakietu OMS jako bramy usługi Azure Log Analytics. 
>

W tym artykule opisano sposób konfigurowania komunikacji przy użyciu usługi Azure Automation i Azure Monitor przy użyciu bramy usługi Log Analytics, gdy komputery, które są połączone bezpośrednio lub które są monitorowane przez program Operations Manager mają Brak dostępu do Internetu. 

Brama usługi Log Analytics jest przekierowania serwera proxy HTTP obsługującego tunelowania HTTP za pomocą polecenia połączenia protokołu HTTP. Ta Brama może zbierać dane i wysyłać je do usługi Azure Automation i obszar roboczy usługi Log Analytics w usłudze Azure Monitor imieniu komputerów, które nie są połączone z Internetem.  

Brama usługi Log Analytics obsługuje:

* Raportowanie do tego samego cztery usługi Log Analytics agenci obszaru roboczego, które są pod nim, które są skonfigurowane przy użyciu usługi Azure Automation hybrydowego Runbook Worker.  
* Komputery Windows, na których program Microsoft Monitoring Agent bezpośrednio połączone z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor.
* Komputery z systemem Linux na których agenta usługi Log Analytics dla systemu Linux jest bezpośrednio połączony z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor.  
* System Center Operations Manager 2012 z dodatkiem SP1 z pakietem zbiorczym aktualizacji 7, Operations Manager 2012 R2 przy użyciu UR3 lub grupy zarządzania w programie Operations Manager 2016 lub nowszym zintegrowanej z usługą Log Analytics.  

Niektóre z zasadami bezpieczeństwa IT nie zezwalaj na połączenia internetowego komputerów w sieci. Te komputery niepołączonych może być punktu sprzedaży (POS) urządzeniami lub serwerami obsługującymi funkcję usług informatycznych, na przykład. Do łączenia z tych urządzeń do usługi Azure Automation lub obszaru roboczego usługi Log Analytics, co umożliwia zarządzanie i monitorowanie ich je skonfigurować do komunikowania się bezpośrednio z bramą usługi Log Analytics. Brama usługi Log Analytics może odbierać informacje o konfiguracji i przekazują dane w ich imieniu. Jeśli komputery są skonfigurowane przy użyciu agenta usługi Log Analytics można łączyć się bezpośrednio do obszaru roboczego usługi Log Analytics, komputery zamiast komunikowania się z bramą usługi Log Analytics.  

Brama usługi Log Analytics przesyła dane z agentów do usługi bezpośrednio. Nie analizować dane podczas przesyłania.

W przypadku grupy zarządzania programu Operations Manager jest zintegrowany z usługą Log Analytics, do połączenia z bramą usługi Log Analytics do odbierania informacji o konfiguracji i wysyłać zebrane dane, w zależności od rozwiązania, które można skonfigurować serwery zarządzania .  Agenci programu Operations Manager wysyłać dane do serwera zarządzania. Na przykład agentów może wysyłać alertów programu Operations Manager, dane oceny konfiguracji, dane przestrzeni wystąpienia i dane wydajności. Inne dane w dużej liczby, takich jak dzienniki, dane dotyczące wydajności i zdarzeń zabezpieczeń Internet Information Services (IIS) są wysyłane bezpośrednio do bramy usługi Log Analytics. 

Jeśli jeden lub więcej serwerów bramę programu Operations Manager są wdrażane do monitorowania niezaufanych systemów w sieci obwodowej lub sieci izolowanej, te serwery nie mogą komunikować się z bramą usługi Log Analytics.  Serwery programu Operations Manager Gateway może zgłaszać tylko na serwerze zarządzania.  Gdy grupy zarządzania programu Operations Manager jest skonfigurowany do komunikowania się z bramą usługi Log Analytics, informacje o konfiguracji serwera proxy jest automatycznie przekazywana do każdego komputera zarządzanego przez agenta, który jest skonfigurowany do gromadzenia danych dziennika dla usługi Azure Monitor nawet jeśli ustawienie jest pusty.    

Aby zapewnić wysoką dostępność bezpośrednio połączone lub grupy zarządzania operacjami, które komunikują się z obszarem roboczym usługi Log Analytics za pośrednictwem bramy, użyj równoważenia obciążenia sieciowego (NLB) do przekierowywania i dystrybuowanie ruchu między wieloma serwerami bramy. W ten sposób, jeśli jeden serwer bramy ulegnie awarii, ruch jest przekierowywany do innego węzła dostępności.  

Komputer z uruchomioną bramą usługi Log Analytics wymaga agenta Log Analytics Windows do identyfikacji punktów końcowych usługi, które musi komunikować się z bramą. Agenta należy również kierować bramy do raportowania do tego samego obszaru roboczego, agentów lub grupy zarządzania programu Operations Manager za bramą są skonfigurowane przy użyciu. Ta konfiguracja umożliwia bramy i agenta do komunikowania się z ich przypisanej obszaru roboczego.

Brama może być wieloadresowe, aby maksymalnie czterech obszarów roboczych. Jest to całkowita liczba obszarów roboczych, których Windows agent obsługuje.  

Każdy agent muszą mieć łączność sieciową do bramy, dzięki czemu agenci automatycznie mogą przesyłać dane do i z bramy. Unikaj instalowania bramy na kontrolerze domeny.

Na poniższym diagramie przedstawiono danych napływających z bezpośrednich agentów, za pośrednictwem bramy, aby usługa Azure Automation i Log Analytics. Konfiguracja serwera proxy agenta muszą być zgodne port, który skonfigurowano bramy usługi Log Analytics.  

![Diagram przedstawiający agent bezpośredni komunikacja z usługami](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Na poniższym diagramie przedstawiono przepływ danych z grupy zarządzania programu Operations Manager do usługi Log Analytics.   

![Diagram przedstawiający komunikacji programu Operations Manager z usługą Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Konfigurowanie systemu

Komputery wyznaczone do uruchamiania bramy usługi Log Analytics musi mieć następującą konfigurację:

* System Windows 10, Windows 8.1 lub Windows 7
* Systemu Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 lub systemu Windows Server 2008
* Microsoft .NET Framework 4.5
* Co najmniej 4-rdzeniową procesora i 8 GB pamięci 
* A [agenta usługi Log Analytics dla Windows](agent-windows.md) skonfigurowany do raportowania do tego samego obszaru roboczego wraz z agentami, które komunikują się za pośrednictwem bramy

### <a name="language-availability"></a>Dostępne wersje językowe

Brama usługi Log Analytics jest dostępna w tych językach:

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
- koreański
- Polski
- Portugalski (Brazylia)
- Portugalski (Portugalia)
- Rosyjski
- Hiszpański (międzynarodowy)

### <a name="supported-encryption-protocols"></a>Szyfrowanie obsługiwane protokoły
Brama usługi Log Analytics obsługuje tylko zabezpieczeń TLS (Transport Layer) 1.0, 1.1 i 1.2.  Go nie obsługuje protokołu Secure Sockets Layer (SSL).  Aby zapewnić bezpieczeństwo danych przesyłanych do usługi Log Analytics, należy skonfigurować bramę do użycia w co najmniej protokołu TLS 1.2. Starsze wersje protokołu TLS lub SSL są zagrożone. Chociaż obecnie umożliwiają one zgodności z poprzednimi wersjami, należy unikać używania ich.  

Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Obsługiwana liczba połączeń agenta
W poniższej tabeli przedstawiono około liczby agentów może komunikować się z serwerem bramy. Obsługa opiera się na agentów przekazujących około 200 KB danych co 6 sekund. Dla każdego agenta, przetestowane ilość danych jest około 2.7 GB dziennie.

|Brama |Agenci obsługiwani (w przybliżeniu)|  
|--------|----------------------------------|  
|Procesor CPU: Intel Xeon procesora E5 2660 v3 \@ 2,6 GHz, 2 rdzenie<br> Pamięć: 4 GB<br> Przepustowość sieci: 1 Gb/s| 600|  
|Procesor CPU: Intel Xeon procesora E5 2660 v3 \@ 2,6 GHz 4 rdzenie<br> Pamięć: 8 GB<br> Przepustowość sieci: 1 Gb/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Pobierz bramę usługi Log Analytics

Pobierz najnowszą wersję pliku instalacyjnego bramy usługi Log Analytics z poziomu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) lub witrynie Azure portal.

Aby uzyskać bramy usługi Log Analytics w witrynie Azure portal, wykonaj następujące kroki:

1. Przejrzyj listę usług, a następnie wybierz **usługi Log Analytics**. 
1. Wybierz obszar roboczy.
1. W bloku obszaru roboczego w obszarze **ogólne**, wybierz opcję **— Szybki Start**. 
1. W obszarze **wybierz źródło danych do łączenia z obszarem roboczym**, wybierz opcję **komputerów**.
1. W **Agent bezpośredni** bloku wybierz **pobierania usługi Log Analytics gateway**.
 
   ![Zrzut ekranu kroki, aby pobrać bramę usługi Log Analytics](./media/gateway/download-gateway.png)

lub 

1. W bloku obszaru roboczego w obszarze **ustawienia**, wybierz opcję **Zaawansowane ustawienia**.
1. Przejdź do **połączone źródła** > **serwerów Windows** i wybierz **pobierania usługi Log Analytics gateway**.

## <a name="install-the-log-analytics-gateway"></a>Instalowanie bramy usługi Log Analytics

Aby zainstalować bramę, wykonaj następujące kroki.  (Jeśli zainstalowano poprzednią wersję, o nazwie usługi przesyłania dalej Log Analytics, nastąpi uaktualnienie do tej wersji.)

1. Folder docelowy, kliknij dwukrotnie **gateway.msi usługi Log Analytics**.
1. Na **stronie powitalnej** wybierz pozycję **Dalej**.

   ![Zrzut ekranu powitalnej strony Kreatora instalacji bramy](./media/gateway/gateway-wizard01.png)

1. Na **umowy licencyjnej** wybierz opcję **akceptuję warunki umowy licencyjnej** zgodę na postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie wybierz pozycję **dalej**.
1. Na **portu i serwera proxy adres** strony:

   a. Wprowadź numer portu TCP, który ma być używany dla bramy. Instalator używa tego numeru portu, aby skonfigurować regułę ruchu przychodzącego zapory Windows.  Wartość domyślna to 8080.
      Prawidłowy zakres numer portu to od 1 do 65535. Jeśli dane wejściowe nie należy do tego zakresu, pojawi się komunikat o błędzie.

   b. Jeśli serwer, na której zainstalowano bramę musi komunikować się za pośrednictwem serwera proxy, należy wprowadzić adres serwera proxy, gdy brama musi połączyć. Na przykład wprowadź wartość `http://myorgname.corp.contoso.com:80`.  Jeśli to pole pozostanie puste, brama podejmie próbę bezpośrednie połączenie z Internetem.  Jeśli Twój serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło.

   c. Wybierz opcję **Dalej**.

   ![Zrzut ekranu przedstawiający konfigurację serwera proxy bramy](./media/gateway/gateway-wizard02.png)

1. Jeśli nie masz włączona usługa Microsoft Update, zostanie wyświetlona strona usługi Microsoft Update i można ją włączyć. Wybierz opcję, a następnie wybierz pozycję **dalej**. W przeciwnym razie przejdź do następnego kroku.
1. Na **Folder docelowy** strony, pozostaw domyślny folder C:\Program Files\OMS bramy lub wprowadź lokalizację, w którym chcesz zainstalować bramę. Następnie wybierz przycisk **Dalej**.
1. Na **gotowe do zainstalowania** wybierz opcję **zainstalować**. Jeśli Kontrola konta użytkownika żąda uprawnienia do instalacji, wybierz **tak**.
1. Po zakończeniu działania Instalatora, wybierz **Zakończ**. Aby sprawdzić, czy usługa jest uruchomiona, otwórz przystawkę services.msc i sprawdź, czy **bramy pakietu OMS** pojawia się na liście usług i że jego stan to **systemem**.

   ![Zrzut ekranu przedstawiający usług lokalnych, pokazujący, że jest uruchomiona brama pakietu OMS](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Konfigurowanie równoważenia obciążenia sieciowego 
Można skonfigurować bramy w celu zapewnienia wysokiej dostępności przy użyciu równoważenia obciążenia sieciowego (NLB) firmy Microsoft [obciążenia równoważenia sieciowego (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [usługi Azure Load Balancer](../../load-balancer/load-balancer-overview.md), lub modułów równoważenia obciążenia oparte na sprzęcie. Moduł równoważenia obciążenia zarządza ruchem, przekierowując żądanego połączenia z agentów usługi Log Analytics lub serwerów zarządzania programu Operations Manager w jego węzłach. Jeśli jeden serwer bramy ulegnie awarii, ruch jest kierowany do innych węzłów.

### <a name="microsoft-network-load-balancing"></a>Równoważenie obciążenia sieciowego firmy Microsoft
Aby dowiedzieć się, jak zaprojektować i wdrożyć klaster równoważenia obciążenia sieciowego systemu Windows Server 2016, zobacz [równoważenia obciążenia sieciowego](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Poniżej opisano sposób konfigurowania klastra równoważenia obciążenia sieciowego firmy Microsoft.  

1. Zaloguj się na serwerze Windows, który jest członkiem klastra równoważenia obciążenia Sieciowego przy użyciu konta administracyjnego.  
2. Otwórz Menedżera równoważenia obciążenia sieciowego w Menedżerze serwera, kliknij pozycję **narzędzia**, a następnie kliknij przycisk **Menedżera równoważenia obciążenia sieciowego**.
3. Aby połączyć się z serwerem bramy usługi Log Analytics z programu Microsoft Monitoring Agent zainstalowany, kliknij prawym przyciskiem myszy adres IP klastra, a następnie kliknij przycisk **Dodaj hosta do klastra**. 

    ![Sieć obciążenia równoważenia Manager — Dodaj hosta do klastra](./media/gateway/nlb02.png)
 
4. Wprowadź adres IP serwera bramy, który chcesz się połączyć. 

    ![Sieć obciążenia równoważenia Manager — Dodaj hosta do klastra: Połączenie](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Aby dowiedzieć się, jak zaprojektować i wdrożyć usługi Azure Load Balancer, zobacz [co to jest usługa Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Aby wdrożyć podstawowego modułu równoważenia obciążenia, wykonaj czynności opisane w tym [Szybki Start](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) z wyłączeniem kroki opisane w sekcji **tworzenie serwerów zaplecza**.   

> [!NOTE]
> Konfigurowanie przy użyciu usługi Azure Load Balancer **podstawowej jednostki SKU**, wymaga się, że maszyny wirtualne platformy Azure należą do zestawu dostępności. Aby dowiedzieć się więcej o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych Windows na platformie Azure](../../virtual-machines/windows/manage-availability.md). Aby dodać istniejące maszyny wirtualne do zestawu dostępności, zobacz [zestawu usługi Azure Resource Manager zestawu dostępności maszyny Wirtualnej](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Po utworzeniu modułu równoważenia obciążenia, pulę zaplecza musi zostać utworzona, która dystrybuuje ruch do jednego lub więcej serwerów bramy. Wykonaj kroki opisane w sekcji artykułu szybkiego startu [tworzenie zasobów modułu równoważenia obciążenia](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Podczas konfigurowania sondę kondycji, powinien on zostać skonfigurowany do korzystania z portu protokołu TCP serwera bramy. Sonda kondycji dynamicznie dodaje lub usuwa serwery bramy z rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurowanie agenta usługi Log Analytics i grupa zarządzania programu Operations Manager
W tej sekcji pokazano, jak skonfigurować bezpośrednio połączonych agentów usługi Log Analytics, grupy zarządzania programu Operations Manager lub usługi Azure Automation hybrydowych — procesów roboczych Runbook z bramą usługi Log Analytics do komunikowania się z usługi Azure Automation i Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurowanie agenta usługi Log Analytics autonomiczny
Podczas konfigurowania agenta usługi Log Analytics, Zamień adres IP serwera bramy programu Log Analytics i jego numer portu wartość serwera proxy. Jeśli wdrożono wiele serwerów bramy za modułem równoważenia obciążenia w konfiguracji serwera proxy agenta usługi Log Analytics jest wirtualny adres IP modułu równoważenia obciążenia.  

>[!NOTE]
>Aby zainstalować agenta usługi Log Analytics na komputerach Windows, które łączyć się bezpośrednio do usługi Log Analytics i bramy, zobacz [Windows łączenie komputerów do usługi Log Analytics na platformie Azure](agent-windows.md). Aby połączyć komputery z systemem Linux, zobacz [konfigurowania agenta usługi Log Analytics dla komputerów z systemem Linux w środowisku hybrydowym](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Po zainstalowaniu agenta na serwerze bramy, należy skonfigurować go do obszaru roboczego lub agenci obszaru roboczego, które komunikują się z bramą. Jeśli nie zainstalowano agenta programu Windows Analytics dziennika w bramie, 300 jest zapisanie w dzienniku zdarzeń bramy pakietu OMS, wskazujący, że agent musi być zainstalowany. Jeśli agent jest zainstalowany, ale nie jest skonfigurowany do raportowania do tego samego obszaru roboczego wraz z agentami, które komunikują się przy jego użyciu, zdarzeń 105 są zapisywane do tego samego dziennika, wskazujący, że agent w bramie musi zostać skonfigurowany do raportowania do tego samego obszaru roboczego wraz z agentami, tego co mmunicate z bramą.

Po wykonaniu konfiguracji, uruchom ponownie usługę bramy pakietu OMS, aby zastosować zmiany. W przeciwnym razie bramy spowoduje odrzucenie agentów, którzy próby komunikacji z usługą Log Analytics i zgłasza zdarzenie 105 w dzienniku zdarzeń bramy pakietu OMS. Nastąpi to również podczas dodawania lub usunąć obszar roboczy z konfiguracji agenta na serwerze bramy.   

Aby uzyskać informacje dotyczące automatyzacji hybrydowego procesu roboczego Runbook, zobacz [Automatyzuj zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego Runbook](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Skonfigurować program Operations Manager, w którym wszyscy agenci korzystać z tego samego serwera proxy
Konfiguracja serwera proxy programu Operations Manager jest automatycznie stosowany do wszystkich agentów, którzy raportują do programu Operations Manager, nawet jeśli ustawienie jest pusty.  

Aby użyć bramy pakietu OMS do obsługi programu Operations Manager, musisz mieć:

* Program Microsoft Monitoring Agent (wersja 8.0.10900.0 lub nowszej) zainstalowane na serwerze bramy pakietu OMS i z tego samego obszarów roboczych usługi Log Analytics, że grupy zarządzania jest skonfigurowany do raportu, aby.
* Łączność z Internetem. Alternatywnie bramy pakietu OMS należy połączyć serwer proxy, który jest połączony z Internetem.

> [!NOTE]
> Jeśli nie określisz żadnej wartości dla bramy, wartości puste są wypychane do wszystkich agentów.
>

Jeśli rejestruje grupę zarządzania programu Operations Manager z obszarem roboczym usługi Log Analytics po raz pierwszy, nie zobaczysz opcję, aby określić konfigurację serwera proxy dla grupy zarządzania w konsoli operacje. Ta opcja jest dostępna tylko w przypadku grupy zarządzania został zarejestrowany w usłudze.  

Aby skonfigurować integrację, należy zaktualizować system konfiguracji serwera proxy przy użyciu polecenia Netsh w systemie, w którym pracujesz w konsoli operacje, jak i na wszystkich serwerach zarządzania w grupie zarządzania. Wykonaj następujące kroki:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień:

   a. Wybierz **Start** i wprowadź **cmd**.  

   b. Kliknij prawym przyciskiem myszy **polecenia** i wybierz **Uruchom jako administrator**.  

1. Wprowadź następujące polecenie:

   `netsh winhttp set proxy <proxy>:<port>`

Po ukończeniu integracji z usługą Log Analytics, należy usunąć zmiany, uruchamiając `netsh winhttp reset proxy`. Następnie w konsoli operacje, należy użyć **skonfiguruj poświadczenia serwera proxy** opcję, aby określić serwer bramy usługi Log Analytics. 

1. W konsoli programu Operations Manager w obszarze **pakietu Operations Management Suite**, wybierz opcję **połączenia**, a następnie wybierz pozycję **Konfiguruj serwer Proxy**.

   ![Zrzut ekranu programu Operations Manager, przedstawiający wybór skonfiguruj poświadczenia serwera Proxy](./media/gateway/scom01.png)

1. Wybierz **Użyj serwera proxy do dostępu do pakietu Operations Management Suite** , a następnie wprowadź adres IP serwera bramy programu Log Analytics lub wirtualny adres IP modułu równoważenia obciążenia. Uważaj rozpoczynają się prefiksem `http://`.

   ![Zrzut ekranu programu Operations Manager, przedstawiający adres serwera proxy](./media/gateway/scom02.png)

1. Wybierz pozycję **Finish** (Zakończ). Grupa zarządzania programu Operations Manager został skonfigurowany do komunikowania się za pośrednictwem serwera bramy do usługi Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Skonfigurować program Operations Manager, gdzie określone agentów, Użyj serwera proxy
W przypadku dużych lub złożonych środowisk można tylko określonych serwerów (lub grupy) do używania serwera bramy programu Log Analytics.  Na tych serwerach nie można zaktualizować agenta programu Operations Manager bezpośrednio, ponieważ ta wartość jest zastępowany przez wartość globalny dla grupy zarządzania.  Zamiast tego należy zastąpić regułę używaną do wypychania tych wartości.  

> [!NOTE] 
> Użyj tej techniki konfiguracji, jeśli chcesz zezwolić na wiele serwerów bramy usługi Log Analytics w danym środowisku.  Na przykład można wymagać określonych serwerów bramy usługi Log Analytics, należy określić na podstawie regionalnej.
>

Aby skonfigurować konkretnych serwerów lub grupy, aby użyć serwera bramy programu Log Analytics: 

1. Otwórz konsolę programu Operations Manager i wybierz **tworzenie** obszaru roboczego.  
1. W obszarze roboczym tworzenie wybierz **reguły**. 
1. Na pasku narzędzi programu Operations Manager wybierz **zakres** przycisku. Jeśli ten przycisk jest niedostępny, upewnij się, po wybraniu obiektu, nie folder w **monitorowanie** okienka. **Zakres obiektów pakietu administracyjnego** okno dialogowe wyświetla listę typowych klas docelowych, grup lub obiektów. 
1. W **poszukaj** wprowadź **usługa kondycji** i wybierz ją z listy. Kliknij przycisk **OK**.  
1. Wyszukaj **reguła ustawienia serwera Proxy usługi Advisor**. 
1. Na pasku narzędzi programu Operations Manager wybierz **zastępuje** i wskaż **zastąpienia Rule\For konkretnego obiektu klasy: Usługa kondycji** i wybierz obiekt z listy.  Lub Utwórz niestandardową grupę, która zawiera obiekt usługi kondycji serwerów, które chcesz zastosować przesłonięcia w celu. Następnie można zastosować przesłonięcia do grupy niestandardowej.
1. W **właściwości zastąpienia** okna dialogowego Dodaj znacznik wyboru w **zastąpienia** obok kolumny **WebProxyAddress** parametru.  W **wartość zastąpienia** wprowadź adres URL serwera bramy usługi Log Analytics. Uważaj rozpoczynają się prefiksem `http://`.  

    >[!NOTE]
    > Nie potrzebujesz włączyć zasadę. On już zarządzane automatycznie za pomocą zastąpienia w pakiecie administracyjnym programu Microsoft Center Advisor bezpiecznego odwołania zastąpienie systemowe przeznaczonego Microsoft grupy System Center Advisor Monitoring Server.
    > 

1. Wybierz pakiet administracyjny z **wybierz docelowy pakiet administracyjny** listy lub Utwórz nowy niezapieczętowany pakiet administracyjny, wybierając **New**. 
1. Po zakończeniu wybierz pozycję **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurowanie dla usługi Automation hybrydowych procesów roboczych Runbook
W przypadku usługi Automation hybrydowych procesów roboczych Runbook w środowisku, wykonaj te kroki ręczne, tymczasowego obejścia, aby skonfigurować bramę pakietu OMS do obsługi pracowników.

Aby wykonać kroki opisane w tej sekcji, musisz wiedzieć region platformy Azure, w której znajduje się konto usługi Automation. Aby znaleźć tej lokalizacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz usługę Azure Automation.
1. Wybierz odpowiednie konto usługi Azure Automation.
1. Wyświetl regionie na podstawie **lokalizacji**.

   ![Zrzut ekranu przedstawiający Lokalizacja konta usługi Automation w witrynie Azure portal](./media/gateway/location.png)

Użyj następujących tabel, aby ustalić adres URL dla każdej lokalizacji.

**Adresy URL usługi danych czasu wykonywania zadania**

| **Lokalizacja** | **Adres URL** |
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

| **Lokalizacja** | **Adres URL** |
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

Jeśli komputer jest zarejestrowany jako hybrydowy proces roboczy elementu Runbook automatycznie, należy użyć rozwiązania do zarządzania aktualizacjami do zarządzania poprawki. Wykonaj następujące kroki:

1. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy dozwolone hosta w bramie usługi Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy usługi Log Analytics, za pomocą następującego polecenia cmdlet programu PowerShell: `Restart-Service OMSGatewayService`

Jeśli komputer jest przyłączony do usługi Azure Automation za pomocą polecenia cmdlet rejestracji hybrydowego procesu roboczego Runbook, wykonaj następujące kroki:

1. Adres URL rejestracji usługi agenta należy dodać do listy dozwolone hosta w bramie usługi Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy dozwolone hosta w bramie usługi Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy usługi Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Przydatne poleceń cmdlet programu PowerShell
Można użyć poleceń cmdlet w celu wykonania zadań, aby zaktualizować ustawienia konfiguracji bramy usługi Log Analytics. Przed użyciem polecenia cmdlet, należy koniecznie:

1. Zainstaluj bramę usługi Log Analytics (Instalator systemu Microsoft Windows).
1. Otwórz okno konsoli programu PowerShell.
1. Zaimportuj moduł, wpisując polecenie: `Import-Module OMSGateway`
1. Jeśli żaden błąd nie wystąpił w poprzednim kroku, moduł został pomyślnie zaimportowany i używać poleceń cmdlet. Wprowadź wartość `Get-Module OMSGateway`
1. Wprowadź zmiany przy użyciu polecenia cmdlet, uruchom ponownie usługę bramy pakietu OMS.

Błąd w kroku 3 oznacza, że moduł nie został zaimportowany. Ten błąd może wystąpić, gdy programu PowerShell nie można odnaleźć modułu. Moduł znajduje się w ścieżce instalacji bramy pakietu OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Polecenia cmdlet** | **Parametry** | **Opis** | **Przykład** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klucz |Pobiera konfigurację usługi |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klucz (wymagane) <br> Wartość |Umożliwia zmianę konfiguracji usługi |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Pobiera adres serwera proxy (nadrzędnego) przekazywania |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Nazwa użytkownika<br> Hasło |Ustawia adres (i poświadczeń) przekazywania (nadrzędnego) serwera proxy |1. Ustaw serwer proxy usługi relay i poświadczenia:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ustaw serwer proxy usługi relay, która nie wymaga uwierzytelniania: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wyczyść ustawienia serwera proxy usługi relay:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Pobiera obecnie dozwolone hosta (tylko lokalnie skonfigurowanych hosta dozwolone, nie są automatycznie pobierane dozwolone hostów) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hosta (wymagane) |Dodanie hosta do listy dozwolonych |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hosta (wymagane) |Usuwa hosta z listy dozwolonych |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Dodaje certyfikat klienta z zastrzeżeniem listy dozwolonych |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Usuwa podmiot certyfikatu klienta z białej listy |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Pobiera obecnie dozwolone klienta podmiotom certyfikatów (tylko lokalnie skonfigurowanych tematów dozwolone, nie są automatycznie pobierane dozwolone przedmioty) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Aby zbierać zdarzenia zarejestrowane przez bramę, należy mieć zainstalowany agent usługi Log Analytics.

![Zrzut ekranu przedstawiający listę Podglądu zdarzeń w dzienniku bramy usługi Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Identyfikatory zdarzeń bramy log Analytics i opisów

W poniższej tabeli przedstawiono identyfikatorów zdarzeń i opisów dla zdarzenia dziennika bramy usługi Log Analytics.

| **Identyfikator** | **Opis** |
| --- | --- |
| 400 |Wszystkie błędu aplikacji, który ma nie określonych identyfikator. |
| 401 |Niewłaściwej konfiguracji. Na przykład listenPort = "text", a nie liczbą całkowitą. |
| 402 |Wyjątek podczas analizowania komunikatów uzgadniania TLS. |
| 403 |Błąd sieci. Na przykład nie można połączyć z serwerem docelowym. |
| 100 |Informacje ogólne. |
| 101 |Usługa została uruchomiona. |
| 102 |Usługa została zatrzymana. |
| 103 |To polecenie połączenia protokołu HTTP została odebrana od klienta. |
| 104 |Nie połączenia HTTP polecenia. |
| 105 |Serwer docelowy nie ma na liście dozwolonych lub port docelowy nie jest bezpieczne (443). <br> <br> Upewnij się, że na serwerze bramy pakietu OMS agent MMA i agentów, które komunikują się za pomocą bramy pakietu OMS są podłączone do tego samego obszaru roboczego usługi Log Analytics. |
| 105 |Błąd TcpConnection — nieprawidłowy certyfikat klienta: CN=Gateway. <br><br> Upewnij się, że używasz bramy pakietu OMS wersji 1.0.395.0 lub nowszej. Upewnij się również, że na serwerze bramy pakietu OMS agent MMA i agentów podczas komunikowania się z bramą pakietu OMS są podłączone do tego samego obszaru roboczego usługi Log Analytics. |
| 106 |Nieobsługiwana wersja protokołu TLS/SSL.<br><br> Brama usługi Log Analytics obsługuje tylko protokół TLS 1.0, TLS 1.1 i 1.2. Nie obsługuje protokołu SSL.|
| 107 |Zweryfikowano sesji TLS. |

### <a name="performance-counters-to-collect"></a>Liczniki wydajności do zbierania

W poniższej tabeli przedstawiono dostępne dla bramy usługi Log Analytics liczników wydajności. Aby dodać liczniki, należy użyć Monitora wydajności.

| **Nazwa** | **Opis** |
| --- | --- |
| Połączenia klienta usługi log Analytics bramy/aktywny |Liczba aktywnych połączeń z klientami sieci (TCP) |
| Liczba bram/błędów analizy dziennika |Liczba błędów |
| Log Analytics połączony bramy klienta |Liczba połączonych klientów |
| Liczba bramy/odrzucenia analizy dzienników |Liczba odrzuceń z powodu dowolnego błędu sprawdzania poprawności protokołu TLS |

![Interfejs bramy zrzut ekranu usługi Log Analytics, przedstawiający liczników wydajności](./media/gateway/counters.png)

## <a name="assistance"></a>Pomoc
Po zalogowaniu witrynie Azure Portal, możesz uzyskać pomoc przy użyciu bramy usługi Log Analytics lub innych usług platformy Azure lub funkcji.
Aby uzyskać pomoc, wybierz ikonę znaku zapytania w prawym górnym rogu portalu i wybierz **nowe żądanie obsługi**. Wypełnij formularz nowego żądania pomocy technicznej.

![Zrzut ekranu przedstawiający nowe żądanie obsługi](./media/gateway/support.png)

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie źródeł danych](../../azure-monitor/platform/agent-data-sources.md) zbieranie danych z połączonych źródeł w celu przechowywania danych w obszarze roboczym usługi Log Analytics.