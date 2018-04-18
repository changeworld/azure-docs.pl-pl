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
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Łączenie komputerów bez dostępu do Internetu za pomocą bramy OMS
W tym dokumencie opisano sposób konfigurowania komunikacji przy użyciu automatyzacji Azure i analizy dzienników przy użyciu bramy OMS, gdy bezpośrednio połączonych komputerów lub komputerów monitorowanych programu Operations Manager nie ma dostępu do Internetu. Brama OMS jest serwera proxy do przodu HTTP, który obsługuje protokół HTTP tunelowania za pomocą polecenia połączenia HTTP. Umożliwia zbieranie danych i wysyłania do automatyzacji Azure i analizy dzienników imieniu komputerów bez dostępu do Internetu.  

Brama OMS umożliwia:

* Hybrydowymi elementami roboczymi Runbook usługi Automatyzacja Azure  
* Komputery z systemem Windows z programu Microsoft Monitoring Agent jest podłączony bezpośrednio do obszaru roboczego analizy dzienników
* Komputery z systemem Linux z agentem pakietu OMS Linux bezpośrednio podłączone do obszaru roboczego analizy dzienników  
* System Center Operations Manager 2012 SP1 z pakietem UR7, Operations Manager 2012 R2 UR3, Operations Manager 2016 i grupę zarządzania programu Operations Manager w wersji 1801 zintegrowany z analizy dzienników  

Jeśli zasady zabezpieczeń IT nie zezwalaj na niektórych komputerach w sieci, aby nawiązać połączenie z Internetem (na przykład urządzeń w punktach sprzedaży lub serwerów, które obsługują usługi IT), ale należy połączyć je do usługi Automatyzacja Azure lub analizy dzienników, aby zarządzać i monitorować je, ich można skonfigurować do bezpośredniego komunikowania się z bramą OMS.

 Jeśli te komputery są skonfigurowane z agentem pakietu OMS bezpośrednio z obszaru roboczego analizy dzienników, wszystkie komputery zamiast komunikować się z bramy OMS. Brama OMS przesyła dane z agentów do usługi bezpośrednio. Nie jakichkolwiek danych analizować podczas przesyłania.

Gdy grupy zarządzania programu Operations Manager jest zintegrowany z analizy dzienników, serwery zarządzania można skonfigurować do nawiązania połączenia bramy OMS do odbierania informacji o konfiguracji i wysyłania zebranych danych. Agenci programu Operations Manager wysłać niektórych danych, takich jak alerty programu Operations Manager, oceny konfiguracji, miejsce zajmowane przez wystąpienia i pojemności danych na serwerze zarządzania. Inne dane dużych, takich jak dzienniki programu IIS, informacje o wydajności i zdarzeń zabezpieczeń są wysyłane bezpośrednio do bramy OMS.  

Jeśli jeden lub więcej serwerów bramę programu Operations Manager, które są wdrażane w w sieci obwodowej lub innych izolowaną sieć, do monitorowania systemów niezaufanych, nie mogą komunikować się z bramą OMS. Serwery programu Operations Manager bramy tylko może raportować do serwera zarządzania. 

Po skonfigurowaniu grupy zarządzania programu Operations Manager do komunikowania się z bramą OMS informacje o konfiguracji serwera proxy jest automatycznie dystrybuowana do co komputer zarządzany przez agenta, który jest skonfigurowany do zbierania danych do analizy dzienników nawet wtedy, gdy ustawienie jest puste.    

Aby zapewnić wysoką dostępność dla bezpośrednio połączonych lub grup zarządzania operacje, które komunikują się z analizy dzienników za pośrednictwem bramy OMS, użyć równoważenia obciążenia sieciowego przekierowania, czyli rozdzielenie ruchu na wielu serwerach bramy OMS. Jeśli jeden serwer bramy ulegnie awarii, ruch jest przekierowywany do innego węzła dostępne.  

Firma Microsoft zaleca, należy zainstalować agenta pakietu OMS na komputerze, na którym działa oprogramowanie OMS bramy, aby ją monitorować i analizować dane wydajności lub zdarzeń. Ponadto agent pomaga bramy OMS zidentyfikować potrzebne do komunikowania się z punktów końcowych usługi.

Wszyscy agenci musi mieć łączność sieciową z ich bramy, tak aby ich automatycznie transferu danych do i z bramy. Nie zaleca się instalowanie bramy na kontrolerze domeny.

Na poniższym diagramie przedstawiono przepływ danych przez bezpośrednie agentów do automatyzacji Azure i analizy dzienników przy użyciu serwera bramy. Konfiguracja serwera proxy agenta musi używać tego samego portu, który bramy OMS używa do komunikacji z usługą.  

![komunikacji agenta bezpośrednio z diagramu usług](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Na poniższym diagramie przedstawiono przepływ danych z grupy zarządzania programu Operations Manager do analizy dzienników.   

![Operations Manager komunikację z diagramu analizy dzienników](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Wymagania wstępne

Po wskazaniu komputer, aby uruchomić bramę OMS, upewnij się, że ma ona następujące składniki:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET Framework 4.5
* Co najmniej 4 rdzenie procesora i 8 GB pamięci 

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
W poniższej tabeli wymieniono obsługiwane liczbę agentów, które komunikują się z serwerem bramy. Ta obsługa jest oparta na agentów przekazywania około 200 KB danych co 6 sekund. Ilość danych na badane agenta jest około 2.7 GB na dzień.

|Brama |Przybliżoną liczbę agentów, które są obsługiwane|  
|--------|----------------------------------|  
|-Procesora: Intel XEON E5 Procesora 2660 v3 @ 2.6 GHz 2 rdzeni<br> -Pamięci: 4 GB<br> — Przepustowość sieci: 1 GB/s| 600|  
|-Procesora: Intel XEON E5 Procesora 2660 v3 @ 2.6 GHz 4 rdzenie<br> -Pamięci: 8 GB<br> — Przepustowość sieci: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Pobierz bramę OMS

Istnieją dwa sposoby, aby pobrać najnowszą wersję pliku instalacyjnego bramy OMS.

1. Pobierz go z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Pobierz go z portalu Azure. Po zalogowaniu się do portalu Azure, wykonaj następujące czynności:  

   1. Przejrzyj listę usług, a następnie wybierz **analizy dzienników**.  
   2. Wybierz obszar roboczy.
   3. W bloku obszaru roboczego w obszarze **ogólne**, wybierz pozycję **Szybki Start**.
   4. W obszarze **wybierz źródło danych do nawiązania połączenia obszaru roboczego**, wybierz pozycję **komputerów**.
   5. W **bezpośredniego agenta** panelu, wybierz opcję **Pobierz bramy OMS**.
   
    ![Pobierz OMS bramy](./media/log-analytics-oms-gateway/download-gateway.png)

--LUB-- 

   1. W bloku obszaru roboczego w obszarze **ustawienia**, wybierz pozycję **Zaawansowane ustawienia**.
   2. Wybierz **połączone źródła** > **serwerów z systemem Windows**. Następnie wybierz **Pobierz bramy OMS**.

## <a name="install-the-oms-gateway"></a>Zainstaluj bramę OMS

Aby zainstalować bramę, wykonaj następujące kroki. Jeśli zainstalowano poprzednią wersję, nazywanych *dziennika analizy usługi przesyłania dalej*, zostaną uaktualnione do tej wersji.  

1. W folderze docelowym, wybierz **OMS Gateway.msi**.
2. Na **stronie powitalnej** wybierz pozycję **Dalej**.

 ![Kreator instalacji bramy](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. Na **umowy licencyjnej** wybierz pozycję **akceptuję warunki umowy licencyjnej**. Następnie wybierz przycisk **Dalej**.

4. Na **portu i serwera proxy adres** strony:

   a. Wpisz numer portu TCP używanego do bramy. Instalator konfiguruje regułę ruchu przychodzącego z tego numeru portu w Zaporze systemu Windows. Wartość domyślna to 8080. Prawidłowy zakres numer portu to od 1 do 65535. Jeśli dane wejściowe nie należy do tego zakresu, zostanie wyświetlony komunikat o błędzie.

   b. Opcjonalnie serwera, na którym zainstalowano bramę musi komunikować się za pośrednictwem serwera proxy, wpisz serwera proxy, do którego musi połączyć bramę. Przykładem może być `http://myorgname.corp.contoso.com:80`, jak pokazano na poniższym zrzucie ekranu. Jeśli to pole pozostanie puste, brama próbuje połączyć się z Internetem bezpośrednio.  Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło.
   
    ![Konfiguracja serwera proxy kreatora bramy](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Wybierz opcję **Dalej**.

5. Jeśli nie masz włączona usługa Microsoft Update, zostanie wyświetlona strona Microsoft Update i można ją włączyć. Wybierz odpowiednią opcję, a następnie wybierz **dalej**. W przeciwnym razie przejdź do kolejnego etapu.

6. Na **Folder docelowy** strony, pozostaw domyślny folder jako **bramy Files\OMS C:\Program** lub wpisz lokalizację, w którym chcesz zainstalować bramę OMS. Następnie wybierz przycisk **Dalej**.

7. Na **gotowe do zainstalowania** wybierz pozycję **zainstalować**. Kontrola konta użytkownika może pojawiać się żądania uprawnienia do zainstalowania. Jeśli żądanie dla uprawnień, wybierz **tak**.

8. Po zakończeniu działania Instalatora, wybierz **Zakończ**. Należy sprawdzić, czy usługa jest uruchomiona przez otwarcie przystawki services.msc, upewniając się, że **bramy OMS** pojawią się na liście usług, oraz że jego stan jest **systemem**.

    ![Usługi — Brama OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurowanie równoważenia obciążenia sieciowego 
Za pomocą (NLB) do równoważenia obciążenia sieciowego można skonfigurować bramy w celu zapewnienia wysokiej dostępności. Użyj usługi równoważenia obciążenia oparte na sprzęcie lub Microsoft Równoważenie obciążenia sieciowego.  Moduł równoważenia obciążenia zarządza ruchu przez przekierowywanie żądanego połączenia z usługą OMS agentów lub serwery zarządzania programu Operations Manager w jego węzłach. Jeśli jeden serwer bramy ulegnie awarii, ruch jest kierowany do innych węzłów.

Aby dowiedzieć się więcej o projektowaniu i wdrożyć klaster równoważenia obciążenia sieciowego systemu Windows Server 2016, zobacz [równoważenia obciążenia sieciowego](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  W poniższych krokach opisano sposób konfigurowania klastra równoważenia obciążenia sieciowego firmy Microsoft.  

1.  Zaloguj się przy użyciu konta administracyjnego do systemu Windows server, który jest członkiem klastra równoważenia obciążenia Sieciowego.  

2.  W Menedżerze serwera Otwórz Menedżer równoważenia obciążenia sieciowego. Wybierz **narzędzia**, a następnie wybierz **Menedżera równoważenia obciążenia sieciowego**.

3. Aby połączyć się z serwerem bramy OMS z programu Microsoft Monitoring Agent zainstalowany, kliknij prawym przyciskiem myszy adres IP klastra, a następnie wybierz **Dodaj hosta do klastra**.

 ![Obciążenia w sieci, równoważenia Manager — Dodaj hosta do klastra](./media/log-analytics-oms-gateway/nlb02.png)

4. Wprowadź adres IP serwera bramy, z którym chcesz się połączyć.

    ![Sieci Menedżera równoważenia obciążenia — Dodaj hosta do klastra: połączenie](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Konfigurowanie agenta pakietu OMS i grupę zarządzania programu Operations Manager
W następujących sekcjach w tym artykule opisano kroki dotyczące sposobu konfigurowania bezpośrednio połączone agentów OMS, grupy zarządzania programu Operations Manager lub procesów roboczych Runbook hybrydowego usługi Automatyzacja Azure z bramą OMS do komunikacji z usługi Automatyzacja Azure Log Analytics.  

Aby poznać wymagania i kroki wymagane do zainstalowania agenta pakietu OMS na komputerach z systemem Windows, które bezpośrednio z analizy dzienników, zobacz [zbierania danych z komputerów w środowisku z analizy dzienników](log-analytics-concept-hybrid.md#prerequisites).

 Aby komputery z systemem Linux, zobacz [komputerów połączyć Linux Log Analytics](log-analytics-quick-collect-linux-computer.md). Aby uzyskać informacje dotyczące automatyzacji hybrydowy proces roboczy, zobacz [wdrożenia hybrydowego procesu roboczego elementu Runbook](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Konfigurowanie agenta pakietu OMS autonomiczny
Informacji o konfigurowaniu agenta do użycia serwera proxy (czyli w tym przypadku bramy), zobacz [zbierania danych z komputerów w środowisku z analizy dzienników](log-analytics-concept-hybrid.md#prerequisites). Jeśli wdrożono wiele serwerów bramy za modułem równoważenia obciążenia sieciowego, konfiguracji serwera proxy agenta pakietu OMS jest wirtualny adres IP Równoważenie obciążenia Sieciowego:

![Microsoft Monitoring Agent właściwości — ustawienia serwera proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Konfigurowanie programu Operations Manager: wszystkich agentów, użyj tego samego serwera proxy
Możesz skonfigurować programu Operations Manager, aby dodać serwer bramy.  Konfiguracji serwera proxy programu Operations Manager jest automatycznie stosowane do wszystkich agentów, którzy raportują do programu Operations Manager, nawet jeśli ustawienie jest pusta.  

Aby użyć bramy OMS do obsługi programu Operations Manager, następujące składniki należy spełnić:

* Microsoft Monitoring Agent (wersja agenta **8.0.10900.0** lub nowszym) zainstalowany na serwerze bramy i skonfigurowany do analizy dzienników obszarów roboczych, z którymi chcesz się komunikować.

* Bramy, która ma połączenie z Internetem lub połączenia z serwerem proxy, który wykonuje.

> [!NOTE]
> Jeśli nie zostanie określona wartość dla bramy, puste wartości jest przypisany do wszystkich agentów.
> 

Jeśli jest to rejestruje grupę zarządzania programu Operations Manager z obszaru roboczego analizy dzienników po raz pierwszy, opcję, aby określić konfigurację serwera proxy dla grupy zarządzania nie będzie dostępny w konsoli operacje. 

Grupa zarządzania nie mają być pomyślnie zarejestrowane w usłudze, zanim ta opcja jest dostępna. Zaktualizuj konfigurację serwera proxy systemu za pomocą narzędzia Netsh, w tym samym systemie, na którym jest uruchomiona konsola operacje oraz wszystkich serwerów zarządzania w grupie zarządzania.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.

    a. Przejdź do **Start**. Następnie wpisz **cmd**.
    
    b. Kliknij prawym przyciskiem myszy **wiersza polecenia**. Następnie wybierz **Uruchom jako administrator**.
    
2. Wprowadź następujące polecenie, a następnie wybierz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po ukończeniu integracji z analizy dzienników można usunąć zmian, uruchamiając `netsh winhttp reset proxy`. Następnie użyj **Konfiguruj serwer proxy** opcję w konsoli operacje, aby określić serwer bramy OMS. 

1. Otwórz konsolę programu Operations Manager. W obszarze **Operations Management Suite**, wybierz pozycję **połączenia**. Następnie wybierz **Konfiguracja serwera Proxy**.

    ![Operations Manager — Konfiguracja serwera Proxy](./media/log-analytics-oms-gateway/scom01.png)

2. Wybierz **Użyj serwera proxy, aby uzyskać dostępu do usługi Operations Management Suite**. Następnie wpisz adres IP serwera bramy OMS lub wirtualny adres IP Równoważenie obciążenia Sieciowego. Upewnij się, że rozpoczyna `http://` prefiks.

    ![Operations Manager — adres serwera proxy](./media/log-analytics-oms-gateway/scom02.png)

3. Wybierz pozycję **Finish** (Zakończ). Grupę zarządzania programu Operations Manager został skonfigurowany do komunikowania się za pośrednictwem serwera bramy z usługą analizy dzienników.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Konfigurowanie programu Operations Manager: określonych agenci używają serwera proxy
W przypadku środowisk dużych lub złożonych tylko można określonych serwerów (lub grupy), aby użyć serwera bramy OMS. Na tych serwerach nie można zaktualizować agenta programu Operations Manager, ponieważ ta wartość zostanie zastąpiona wartości globalnej grupy zarządzania. Zamiast tego można wyłączyć regułę, która służy do wypychania tych wartości.  

> [!NOTE] 
> Ten sam sposób konfiguracji może służyć do korzystania z wielu serwerów bramy OMS w danym środowisku. Na przykład może wymagać określonych serwerów bramy OMS należy określić na podstawie na region.
>  

1. Otwórz konsolę programu Operations Manager, a następnie wybierz **tworzenie** obszaru roboczego.

2. W obszarze roboczym tworzenie, wybierz **reguły**. Następnie wybierz **zakres** przycisk na pasku narzędzi programu Operations Manager. Jeśli ten przycisk jest niedostępny, sprawdź, upewnij się, że wybrany obiekt, nie folder w **monitorowanie** okienka. **Zakres obiektów pakietu administracyjnego** okno dialogowe wyświetla listę typowych klasy docelowej, grupami lub obiektami. 

3. w **Wyszukaj** wpisz **usługi kondycji**. Wybierz go z listy. Kliknij przycisk **OK**.  

4. Wyszukaj regułę **regułę ustawienie serwera Proxy usługi Advisor**. Na pasku narzędzi konsoli Operacje wybierz **zastępuje**. Następnie wybierz **zastąpienia Rule\For konkretnego obiektu klasy: Usługa kondycji**. 

5. Następnie wybierz pozycję określonego obiektu z listy. 

6. (Opcjonalnie) Utwórz grupy niestandardowej, która zawiera obiekt usługi kondycji serwerów, do których chcesz zastosować to zastąpienie. Następnie można użyć zastąpienia dla tej grupy.

7. W **właściwości zastąpienia** okno dialogowe, kliknij, aby zaznaczyć w **zastąpienia** obok kolumny **WebProxyAddress** parametru.  W **wartość zastąpienia** wprowadź adres URL serwera bramy OMS, zapewnienia, że rozpoczyna się od `http://` prefiks.  

    >[!NOTE]
    > Nie trzeba włączyć zasadę. Jest już zarządzany automatycznie za pomocą zastąpienia, który ma zawartych w pakiecie administracyjnym programu Microsoft System Center Advisor bezpiecznego odwołania Override którego element docelowy Microsoft System Center Advisor monitorowania grupie serwerów.
    >   

8. Wybierz pakiet administracyjny z **wybierz docelowy pakiet administracyjny** listy lub utworzyć nowego niezapieczętowanego pakietu, wybierając **nowy**. 

9. Po zakończeniu zmiany, wybierz **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Konfigurowanie bramy OMS dla automatyzacji hybrydowymi elementami roboczymi Runbook
Jeśli masz automatyzacji hybrydowymi elementami roboczymi Runbook w środowisku, w poniższych krokach przedstawiono ręczne, tymczasowego obejścia, aby skonfigurować bramę OMS do ich obsługi.

W poniższych krokach musisz wiedzieć region platformy Azure, w którym znajduje się na koncie automatyzacji. Aby znaleźć lokalizację, należy wykonać następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz usługi Automatyzacja Azure.
3. Wybierz odpowiednie konto usługi Automatyzacja Azure.
4. W obszarze **lokalizacji**, wyświetlić region konta.

    ![Portal Azure — Lokalizacja konta automatyzacji](./media/log-analytics-oms-gateway/location.png)  

Aby zidentyfikować adres URL dla każdej lokalizacji, należy użyć następujących tabel:

**Zadanie adresy URL usługi danych dla środowiska uruchomieniowego**

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

Jeśli komputer jest automatycznie rejestrowane jako hybrydowy proces roboczy elementu Runbook dla stosowania poprawek za pomocą rozwiązania do zarządzania aktualizacjami, wykonaj następujące kroki:

1. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy hostów dozwolone w bramie OMS. Na przykład wpisz następujące polecenie: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Uruchom ponownie usługę OMS bramy przy użyciu następującego polecenia cmdlet programu PowerShell: `Restart-Service OMSGatewayService`

Jeśli lokalnego komputera do automatyzacji Azure za pomocą polecenia cmdlet rejestracji hybrydowy proces roboczy elementu Runbook, wykonaj następujące kroki:

1. Dodaj adres URL rejestracji usługi agenta do listy hostów dozwolone w bramie OMS. Na przykład wpisz następujące polecenie: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Dodaj adresy URL usługi danych czasu wykonywania zadania do listy hostów dozwolone w bramie OMS. Na przykład wpisz następujące polecenie: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Uruchom ponownie usługę OMS bramy:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Przydatne poleceń cmdlet programu PowerShell
Polecenia cmdlet ułatwiają wykonywanie zadań, które są niezbędne do aktualizowania ustawień konfiguracji bramy OMS. Przed ich użyciem, należy wykonać następujące czynności:

1. Zainstaluj bramę OMS (MSI).
2. Otwórz okno konsoli programu PowerShell.
3. Zaimportuj moduł, wpisując polecenie: `Import-Module OMSGateway`.
4. Jeśli błąd wystąpił w poprzednim kroku, moduł został pomyślnie zaimportowany i można użyć poleceń cmdlet. Wpisz polecenie `Get-Module OMSGateway`.
5. Po wprowadzeniu zmian przy użyciu poleceń cmdlet, upewnij się, uruchom ponownie usługę bramy OMS.

Jeśli wystąpi błąd w kroku 3, moduł nie został zaimportowany. Błąd może wystąpić, gdy nie będzie mógł odnaleźć modułu programu PowerShell. Można znaleźć go w ścieżce instalacji bramy OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **Polecenia cmdlet** | **Parametry** | **Opis** | **Przykład** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klucz |Pobiera konfigurację usługi |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klucz (wymagane) <br> Wartość |Umożliwia zmianę konfiguracji usługi |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Pobiera adres serwera proxy (nadrzędnego) przekazywania |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Nazwa użytkownika<br> Hasło |Ustawia adres (i poświadczeń) serwera proxy (nadrzędnego) przekazywania |1. Ustaw proxy przekazywania i poświadczeń:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ustaw przekazywania serwer proxy, który nie wymaga uwierzytelniania: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wyczyść ustawienie serwera proxy przekazywania:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Pobiera obecnie dozwolone hosta (tylko lokalnie skonfigurowanych dozwolone hosta; nie obejmuje automatycznie pobrane dozwolonych hostów) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (wymagane) |Dodaje hosta do listy dozwolonych |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (wymagane) |Usuwa hosta z listy dozwolonych |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Dodaje certyfikat klienta może ulec listy dozwolonych |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Temat (wymagane) |Usuwa podmiotu certyfikatu klienta z białej listy |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Pobiera obecnie dozwolone klienta podmiotom certyfikatów (lokalnie skonfigurowanych tematów; dozwolone tylko nie obejmuje automatycznie pobrane tematów dozwolonych) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zbieranie zdarzeń, które są rejestrowane przez bramę, należy również jest zainstalowany agent pakietu OMS.

![Podgląd zdarzeń — dziennik bramy OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identyfikatory zdarzeń bramy OMS wraz z opisami**

W poniższej tabeli przedstawiono identyfikatory zdarzeń i opisy bramy OMS zdarzenia dziennika:

| **Identyfikator** | **Opis** |
| --- | --- |
| 400 |Błąd żadnych aplikacji, który nie ma określonego identyfikatora. |
| 401 |Złej konfiguracji. Na przykład: listenPort = "text" zamiast liczbą całkowitą. |
| 402 |Wyjątek podczas analizowania wiadomości uzgadniania TLS. |
| 403 |Wystąpił błąd sieci. Na przykład: nie można nawiązać połączenia z serwerem docelowym. |
| 100 |Informacje ogólne. |
| 101 |Usługa została uruchomiona. |
| 102 |Usługa została zatrzymana. |
| 103 |Odebrano polecenie połączenia HTTP z klienta. |
| 104 |Nie połączenia HTTP polecenia. |
| 105 |Serwer docelowy nie jest na liście dozwolonych lub port docelowy nie jest bezpieczny port (port 443). <br> <br> Upewnij się, że agent MMA na serwerze bramy i agenci, którzy komunikują się z bramą są podłączone do tego samego obszaru roboczego analizy dzienników. |
| 105 |Błąd TcpConnection — nieprawidłowy certyfikat: CN = bramy <br><br> Upewnij się, że: <br>    <br> — Za pomocą bramy numer wersji 1.0.395.0 lub nowszej. <br> — Agent MMA na serwerze bramy oraz agentów, które komunikują się z bramą są podłączone do tego samego obszaru roboczego analizy dzienników. |
| 106 |Brama OMS obsługuje tylko protokołu TLS 1.0, TLS 1.1 i 1.2.  Nie obsługuje protokołu SSL. Dla dowolnego nieobsługiwanej wersji protokołu TLS/SSL bramy OMS generuje 106 identyfikator zdarzenia.|
| 107 |Zweryfikowano sesji protokołu TLS. |

**Liczniki wydajności do**

W poniższej tabeli przedstawiono liczniki wydajności, które są dostępne dla bramy OMS. Liczniki można dodać za pomocą Monitora wydajności.

| **Nazwa** | **Opis** |
| --- | --- |
| Połączenie klienta bramy/aktywny OMS |Liczba aktywnych połączeń z klientami sieci (TCP) |
| Liczba błędów/bramy OMS |Liczba błędów |
| OMS/połączenia bramy klienta |Liczba połączonych klientów |
| Liczba bramy/odrzucenia OMS |Liczba odrzuceń z powodu błędu sprawdzania poprawności TLS |

![Liczniki wydajności bramy OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Uzyskiwanie pomocy
Jeśli logujesz się do portalu Azure, można utworzyć żądanie pomocy z bramy OMS lub innych usług Azure lub funkcji usługi.

Aby poprosić o pomoc, wybierz symbol znak zapytania w prawym górnym rogu portalu. Następnie wybierz **nowy obsługuje żądania**. Ukończ nowego formularza żądania pomocy technicznej.

![Nowe żądanie pomocy technicznej](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Kolejne kroki
[Dodaj źródła danych](log-analytics-data-sources.md) do zbierania danych ze źródeł połączonych i zapisz go w obszarze roboczym analizy dzienników.
