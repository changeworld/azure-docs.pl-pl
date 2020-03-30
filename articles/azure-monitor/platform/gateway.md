---
title: Łączenie komputerów przy użyciu bramy usługi Log Analytics | Dokumenty firmy Microsoft
description: Połącz urządzenia i komputery monitorowane przez program Operations Manager przy użyciu bramy usługi Log Analytics do wysyłania danych do usługi Azure Automation i usługa Log Analytics, gdy nie mają dostępu do Internetu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298334"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Łączenie komputerów bez dostępu do Internetu przy użyciu bramy usługi Log Analytics w usłudze Azure Monitor

>[!NOTE]
>W miarę jak pakiet Microsoft Operations Management Suite (OMS) przechodzi do monitora Microsoft Azure Monitor, terminologia się zmienia. Ten artykuł odnosi się do bramy OMS jako bramy usługi Azure Log Analytics. 
>

W tym artykule opisano sposób konfigurowania komunikacji z usługą Azure Automation i usługi Azure Monitor przy użyciu bramy usługi Log Analytics, gdy komputery, które są bezpośrednio połączone lub monitorowane przez program Operations Manager nie mają dostępu do Internetu. 

Brama usługi Log Analytics jest serwerem proxy http do przodu, który obsługuje tunelowanie HTTP za pomocą polecenia HTTP CONNECT. Ta brama wysyła dane do usługi Azure Automation i obszaru roboczego usługi Log Analytics w usłudze Azure Monitor w imieniu komputerów, które nie mogą bezpośrednio łączyć się z Internetem. 

Brama usługi Log Analytics obsługuje:

* Raportowanie do tych samych obszarów roboczych usługi Log Analytics skonfigurowanych na każdym agencie za nim i które są skonfigurowane za pomocą procesów roboczych hybrydowych śmięty hybrydowego roboczej systemu Azure Automation.  
* Komputery z systemem Windows, na których agent monitorowania firmy Microsoft jest bezpośrednio połączony z obszarem roboczym analizy dzienników w usłudze Azure Monitor.
* Komputery z systemem Linux, na których agent usługi Log Analytics dla systemu Linux jest bezpośrednio połączony z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor.  
* System Center Operations Manager 2012 SP1 z UR7, Operations Manager 2012 R2 z UR3 lub grupy zarządzania w programie Operations Manager 2016 lub nowszym, która jest zintegrowana z usługą Log Analytics.  

Niektóre zasady zabezpieczeń IT nie zezwalają na połączenie internetowe z komputerami sieciowymi. Te niepołączone komputery mogą być urządzeniami punktu sprzedaży (POS) lub serwerami obsługującymi usługi IT, na przykład. Aby połączyć te urządzenia z usługą Azure Automation lub obszarem roboczym usługi Log Analytics, aby można było nimi zarządzać i monitorować, skonfiguruj je do bezpośredniej komunikacji z bramą usługi Log Analytics. Brama usługi Log Analytics może odbierać informacje o konfiguracji i przesyłać dalej dane w ich imieniu. Jeśli komputery są skonfigurowane za pomocą agenta usługi Log Analytics, aby bezpośrednio łączyć się z obszarem roboczym usługi Log Analytics, komputery komunikują się z bramą usługi Log Analytics.  

Brama usługi Log Analytics przesyła dane bezpośrednio z agentów do usługi. Nie analizuje żadnych danych w tranzycie i bramy nie buforuje danych, gdy traci łączność z usługą. Gdy brama nie może komunikować się z usługą, agent kontynuuje uruchamianie i kolejkuje zebrane dane na dysku monitorowanego komputera. Po przywróceniu połączenia agent wysyła buforowane dane zebrane do usługi Azure Monitor.

Gdy grupa zarządzania programu Operations Manager jest zintegrowana z usługą Log Analytics, serwery zarządzania można skonfigurować tak, aby łączyły się z bramą usługi Log Analytics w celu odbierania informacji o konfiguracji i wysyłania zebranych danych, w zależności od włączonego rozwiązania. .  Agenci programu Operations Manager wysyłają niektóre dane do serwera zarządzania. Na przykład agenci mogą wysyłać alerty programu Operations Manager, dane oceny konfiguracji, dane miejsca wystąpienia i dane pojemności. Inne duże ilości danych, takich jak dzienniki internetowych usług informacyjnych (IIS), dane dotyczące wydajności i zdarzenia zabezpieczeń, są wysyłane bezpośrednio do bramy usługi Log Analytics. 

Jeśli jeden lub więcej serwerów bramy programu Operations Manager jest wdrażanych w celu monitorowania niezaufanych systemów w sieci obwodowej lub sieci izolowanej, te serwery nie mogą komunikować się z bramą usługi Log Analytics.  Serwery bramy programu Operations Manager mogą raportować tylko serwer zarządzania.  Gdy grupa zarządzania programu Operations Manager jest skonfigurowana do komunikowania się z bramą usługi Log Analytics, informacje o konfiguracji serwera proxy są automatycznie dystrybuowane do każdego komputera zarządzanego przez agenta, który jest skonfigurowany do zbierania danych dziennika dla usługi Azure Monitor, nawet jeśli ustawienie jest puste.

Aby zapewnić wysoką dostępność dla grup bezpośrednio połączonych lub grup zarządzania operacjami, które komunikują się z obszarem roboczym usługi Log Analytics za pośrednictwem bramy, użyj równoważenia obciążenia sieciowego, aby przekierować i rozesłać ruch na wielu serwerach bramy. W ten sposób, jeśli jeden serwer bramy ulegnie upadkowi, ruch zostanie przekierowany do innego dostępnego węzła.  

Komputer, na który działa brama usługi Log Analytics, wymaga agenta systemu Windows usługi Log Analytics do identyfikowania punktów końcowych usługi, z którymi brama musi się komunikować. Agent musi również skierować bramę do raportu do tych samych obszarów roboczych, które są skonfigurowane przez agentów lub grupę zarządzania programu Operations Manager za bramą. Ta konfiguracja umożliwia bramie i agentowi komunikowanie się z przypisanym obszarem roboczym.

Brama może być wieloadresowa do maksymalnie czterech obszarów roboczych. Jest to całkowita liczba obszarów roboczych, które obsługuje agent systemu Windows.  

Każdy agent musi mieć łączność sieciową z bramą, aby agenci mogli automatycznie przesyłać dane do i z bramy. Należy unikać instalowania bramy na kontrolerze domeny. Komputery z systemem Linux, które znajdują się za serwerem bramy, nie mogą używać metody [instalacji skryptu otoki](agent-linux.md#install-the-agent-using-wrapper-script) do zainstalowania agenta usługi Log Analytics dla systemu Linux. Agent musi zostać pobrany ręcznie, skopiowany na komputer i zainstalowany ręcznie, ponieważ brama obsługuje tylko komunikację z wymienionymi wcześniej usługami platformy Azure.

Na poniższym diagramie przedstawiono dane płynące z agentów bezpośrednich, za pośrednictwem bramy, do usługi Azure Automation i usługi Log Analytics. Konfiguracja serwera proxy agenta musi być zgodna z portem skonfigurowanym przez bramę usługi Log Analytics.  

![Diagram bezpośredniej komunikacji agenta z usługami](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Na poniższym diagramie przedstawiono przepływ danych z grupy zarządzania programu Operations Manager do usługi Log Analytics.   

![Diagram komunikacji programu Operations Manager z usługą Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Konfigurowanie systemu

Komputery wyznaczone do uruchamiania bramy usługi Log Analytics muszą mieć następującą konfigurację:

* Windows 10, Windows 8.1 lub Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 lub Windows Server 2008
* Microsoft .NET Framework 4.5
* Co najmniej 4-rdzeniowy procesor i 8 GB pamięci 
* [Agent usługi Log Analytics dla systemu Windows](agent-windows.md) skonfigurowany do raportowania do tego samego obszaru roboczego co agenci komunikujący się za pośrednictwem bramy

### <a name="language-availability"></a>Dostępność języka

Brama usługi Log Analytics jest dostępna w następujących językach:

- Chiński uproszczony
- Chiński (tradycyjny)
- Czeski
- Niderlandzki
- Polski
- Francuski
- Niemiecki
- węgierski
- Włoski
- Japoński
- Koreański
- Polski
- portugalski (Brazylia)
- Portugalski (Portugalia)
- Rosyjski
- Hiszpański (Międzynarodowy)

### <a name="supported-encryption-protocols"></a>Obsługiwane protokoły szyfrowania

Brama usługi Log Analytics obsługuje tylko usługi Transport Layer Security (TLS) 1.0, 1.1 i 1.2.  Nie obsługuje warstwy SSL (Secure Sockets Layer).  Aby zapewnić bezpieczeństwo danych przesyłanych do usługi Log Analytics, skonfiguruj bramę tak, aby używała co najmniej protokołu TLS 1.2. Starsze wersje protokołu TLS lub SSL są zagrożone. Chociaż obecnie zezwalają na zgodność z powrotem, należy unikać ich używania.  

Aby uzyskać dodatkowe informacje, zapoznaj [się z bezpiecznymi informacjami o wysyłaniu danych za pomocą protokołu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Obsługiwana liczba połączeń agenta

W poniższej tabeli przedstawiono w przybliżeniu, ilu agentów może komunikować się z serwerem bramy. Obsługa jest oparta na agentach, którzy przesyłają około 200 KB danych co 6 sekund. Dla każdego testowanego agenta ilość danych wynosi około 2,7 GB dziennie.

|Brama |Obsługiwane agenty (przybliżone)|  
|--------|----------------------------------|  
|Procesor: Procesor Intel Xeon E5-2660 v3 \@ 2,6 GHz 2 rdzenie<br> Pamięć: 4 GB<br> Przepustowość sieci: 1 Gb/s| 600|  
|Procesor: Procesor Intel Xeon E5-2660 v3 \@ 2.6 GHz 4 rdzenie<br> Pamięć: 8 GB<br> Przepustowość sieci: 1 Gb/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Pobieranie bramy usługi Log Analytics

Pobierz najnowszą wersję pliku instalatora bramy usługi Log Analytics z Centrum pobierania Microsoft[(Download Link)](https://go.microsoft.com/fwlink/?linkid=837444)lub portalu Azure.

Aby uzyskać bramę usługi Log Analytics z witryny Azure portal, wykonaj następujące kroki:

1. Przejrzyj listę usług, a następnie wybierz **pozycję Usługa Log Analytics**. 
1. Wybierz obszar roboczy.
1. W bloku obszaru roboczego w obszarze **Ogólne**wybierz pozycję **Szybki start**. 
1. W obszarze **Wybierz źródło danych, aby połączyć się z obszarem roboczym**wybierz pozycję **Komputery**.
1. W bloku **Agent bezpośredni** wybierz pozycję **Pobierz bramę usługi Log Analytics**.
 
   ![Zrzut ekranu przedstawiający kroki pobierania bramy usługi Log Analytics](./media/gateway/download-gateway.png)

lub 

1. W bloku obszaru roboczego w obszarze **Ustawienia**wybierz pozycję **Ustawienia zaawansowane**.
1. Przejdź do **usługi Podłączone źródła serwerów** > **systemu Windows** i wybierz pozycję Pobierz **bramę usługi Log Analytics**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Instalowanie bramy usługi Log Analytics przy użyciu kreatora konfiguracji

Aby zainstalować bramę za pomocą kreatora instalacji, wykonaj następujące kroki. 

1. W folderze docelowym kliknij dwukrotnie pozycję **Log Analytics gateway.msi**.
1. Na **stronie powitalnej** wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający stronę powitalną w Kreatorze konfiguracji bramy](./media/gateway/gateway-wizard01.png)

1. Na stronie **Umowa licencyjna** wybierz pozycję **Akceptuję warunki umowy licencyjnej,** aby zaakceptować Postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Adresu portu i serwera proxy:**

   a. Wprowadź numer portu TCP, który ma być używany dla bramy. Instalator używa tego numeru portu do skonfigurowania reguły przychodzącej w Zaporze systemu Windows.  Wartość domyślna to 8080.
      Prawidłowy zakres numeru portu wynosi od 1 do 65535. Jeśli dane wejściowe nie należą do tego zakresu, zostanie wyświetlony komunikat o błędzie.

   b. Jeśli serwer, na którym zainstalowana jest brama, musi komunikować się za pośrednictwem serwera proxy, wprowadź adres serwera proxy, pod którym brama musi się połączyć. Na przykład wprowadź wartość `http://myorgname.corp.contoso.com:80`.  Jeśli to pole pozostanie puste, brama spróbuje połączyć się bezpośrednio z Internetem.  Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź nazwę użytkownika i hasło.

   d. Wybierz **pozycję Dalej**.

   ![Zrzut ekranu przedstawiający konfigurację serwera proxy bramy](./media/gateway/gateway-wizard02.png)

1. Jeśli nie masz włączonej usługi Microsoft Update, zostanie wyświetlona strona Microsoft Update i można ją włączyć. Dokonaj wyboru, a następnie wybierz pozycję **Dalej**. W przeciwnym razie przejdź do następnego kroku.
1. Na stronie **Folder docelowy** pozostaw domyślny folder C:\Program Files\OMS Gateway lub wprowadź lokalizację, w której chcesz zainstalować bramę. Następnie wybierz **przycisk Dalej**.
1. Na stronie **Gotowe do zainstalowania** wybierz pozycję **Zainstaluj**. Jeśli kontrola konta użytkownika żąda uprawnień do instalacji, wybierz opcję **Tak**.
1. Po zakończeniu instalacji wybierz pozycję **Zakończ**. Aby sprawdzić, czy usługa jest uruchomiona, otwórz przystawkę services.msc i sprawdź, czy **brama OMS** jest wyświetlana na liście usług i czy jej stan jest **uruchomiony**.

   ![Zrzut ekranu przedstawiający usługi lokalne pokazujące, że brama systemu OMS jest uruchomiona](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Instalowanie bramy usługi Log Analytics przy użyciu wiersza polecenia

Pobrany plik bramy to pakiet Instalatora Windows, który obsługuje instalację dyskretną z wiersza polecenia lub innej zautomatyzowanej metody. Jeśli nie znasz standardowych opcji wiersza polecenia instalatora Windows, zobacz [Opcje wiersza polecenia](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
W poniższej tabeli przedstawiono parametry obsługiwane przez instalatora.

|Parametry| Uwagi|
|----------|------| 
|Numer_portu | Numer portu TCP dla bramy do nasłuchiwać na |
|Serwera proxy | Adres IP serwera proxy |
|Installdir | W pełni kwalifikowana ścieżka do określania katalogu instalacji plików oprogramowania bramy |
|USERNAME | Identyfikator użytkownika do uwierzytelniania za pomocą serwera proxy |
|Hasło | Hasło identyfikatora użytkownika do uwierzytelniania za pomocą serwera proxy |
|LicenseAccepted | Określ wartość **1,** aby zweryfikować akceptację umowy licencyjnej |
|HASAUTH (HASAUTH) | Określ wartość **1,** gdy określono parametry NAZWY UŻYTKOWNIKA/HASŁA |
|HASPROXY (WZT. | Określ wartość **1** podczas określania adresu IP dla parametru **PROXY** |

Aby dyskretnie zainstalować bramę i skonfigurować ją przy określonym adresie serwera proxy, numer portu, wpisz następujące polecenie:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Za pomocą opcji wiersza polecenia /qn ukrywa konfigurację, /qb pokazuje konfigurację podczas instalacji dyskretnej.  

Jeśli musisz podać poświadczenia do uwierzytelniania za pomocą serwera proxy, wpisz następujące czynności:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Po instalacji można potwierdzić, że ustawienia są akceptowane (z wyłączeniem nazwy użytkownika i hasła) przy użyciu następujących poleceń cmdlet programu PowerShell:

- **Get-OMSGatewayConfig** — zwraca port TCP, na który brama jest skonfigurowana do nasłuchiwania.
- **Get-OMSGatewayRelayProxy** — zwraca adres IP serwera proxy skonfigurowanego do komunikowania się z nim.

## <a name="configure-network-load-balancing"></a>Konfigurowanie równoważenia obciążenia sieciowego

Bramę można skonfigurować pod kątem wysokiej dostępności przy użyciu równoważenia obciążenia sieciowego przy użyciu [równoważenia obciążenia sieciowego firmy Microsoft, równoważenia obciążenia](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)sieciowego , [modułu Równoważenia obciążenia platformy Azure](../../load-balancer/load-balancer-overview.md)lub sprzętowych modułów równoważenia obciążenia. Moduł równoważenia obciążenia zarządza ruchem, przekierowując żądane połączenia z agentów usługi Log Analytics lub serwerów zarządzania programu Operations Manager w swoich węzłach. Jeśli jeden serwer bramy ulegnie upadkowi, ruch zostanie przekierowany do innych węzłów.

### <a name="microsoft-network-load-balancing"></a>Równoważenie obciążenia sieci firmy Microsoft

Aby dowiedzieć się, jak zaprojektować i wdrożyć klaster równoważenia obciążenia sieciowego systemu Windows Server 2016, zobacz [Równoważenie obciążenia sieciowego](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). W poniższych krokach opisano sposób konfigurowania klastra równoważenia obciążenia sieciowego firmy Microsoft.  

1. Zaloguj się na serwerze windows, który jest członkiem klastra równoważenia obciążenia sieciowego przy za pomocą konta administracyjnego.  
2. Otwórz Menedżera równoważenia obciążenia sieciowego w Menedżerze serwera, kliknij pozycję **Narzędzia**, a następnie kliknij pozycję **Menedżer równoważenia obciążenia sieciowego**.
3. Aby połączyć serwer bramy usługi Log Analytics z zainstalowanym agentem monitorowania firmy Microsoft, kliknij prawym przyciskiem myszy adres IP klastra, a następnie kliknij polecenie **Dodaj hosta do klastra**. 

    ![Menedżer równoważenia obciążenia sieciowego — dodawanie hosta do klastra](./media/gateway/nlb02.png)
 
4. Wprowadź adres IP serwera bramy, z którą chcesz się połączyć. 

    ![Menedżer równoważenia obciążenia sieciowego — dodawanie hosta do klastra: Łączenie](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Aby dowiedzieć się, jak zaprojektować i wdrożyć moduł równoważenia obciążenia platformy Azure, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](../../load-balancer/load-balancer-overview.md). Aby wdrożyć podstawowy moduł równoważenia obciążenia, wykonaj kroki opisane w tym [przewodniku Szybki start,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) z wyłączeniem kroków opisanych w sekcji **Tworzenie serwerów zaplecza**.   

> [!NOTE]
> Konfigurowanie modułu równoważenia obciążenia platformy Azure przy użyciu **podstawowej jednostki SKU**wymaga, aby maszyny wirtualne platformy Azure należały do zestawu dostępności. Aby dowiedzieć się więcej o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure](../../virtual-machines/windows/manage-availability.md). Aby dodać istniejące maszyny wirtualne do zestawu dostępności, zobacz [Ustawianie zestawu dostępności maszyn wirtualnych usługi Azure Resource Manager](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Po utworzeniu modułu równoważenia obciążenia należy utworzyć pulę wewnętrznej bazy danych, która rozdziela ruch do jednego lub więcej serwerów bramy. Wykonaj kroki opisane w sekcji Szybki start artykułu [Tworzenie zasobów dla modułu równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>Podczas konfigurowania sondy kondycji należy skonfigurować do używania portu TCP serwera bramy. Sonda kondycji dynamicznie dodaje lub usuwa serwery bramy z rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na testy kondycji. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurowanie agenta usługi Log Analytics i grupy zarządzania programem Operations Manager

W tej sekcji zobaczysz, jak skonfigurować bezpośrednio połączonych agentów usługi Log Analytics, grupę zarządzania programu Operations Manager lub pracowników hybrydowych śmiękliń y systemu Azure Automation z bramą usługi Log Analytics do komunikowania się z usługą Azure Automation lub log analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurowanie autonomicznego agenta usługi Log Analytics

Podczas konfigurowania agenta usługi Log Analytics zastąp wartość serwera proxy adresem IP serwera bramy usługi Log Analytics i jego numerem portu. Jeśli za modułem równoważenia obciążenia wdrożono wiele serwerów bramy, konfiguracja serwera proxy agenta usługi Log Analytics jest wirtualnym adresem IP modułu równoważenia obciążenia.  

>[!NOTE]
>Aby zainstalować agenta usługi Log Analytics na bramie i komputerach z systemem Windows, które bezpośrednio łączą się z usługą Log Analytics, zobacz [Łączenie komputerów z systemem Windows z usługą analizy dzienników na platformie Azure](agent-windows.md). Aby połączyć komputery z systemem Linux, zobacz [Łączenie komputerów z systemem Linux z usługą Azure Monitor](agent-linux.md). 
>

Po zainstalowaniu agenta na serwerze bramy skonfiguruj go tak, aby raportował do obszaru roboczego lub agentów obszaru roboczego, którzy komunikują się z bramą. Jeśli agent systemu Windows usługi Log Analytics nie jest zainstalowany w bramie, zdarzenie 300 jest zapisywane w dzienniku zdarzeń bramy systemu OMS, co oznacza, że agent musi zostać zainstalowany. Jeśli agent jest zainstalowany, ale nie skonfigurowany do raportowania do tego samego obszaru roboczego, co agenci, którzy komunikują się za jego pośrednictwem, zdarzenie 105 jest zapisywane w tym samym dzienniku, co oznacza, że agent na bramie musi być skonfigurowany do raportowania do tego samego obszaru roboczego co agenci, którzy komunikować się z bramą.

Po zakończeniu konfiguracji uruchom ponownie usługę **Brama systemu OMS,** aby zastosować zmiany. W przeciwnym razie brama odrzuci agentów, którzy próbują komunikować się z usługą Log Analytics i zgłosi zdarzenie 105 w dzienniku zdarzeń bramy oms. Będzie to również możliwe po dodaniu lub usunięciu obszaru roboczego z konfiguracji agenta na serwerze bramy.

Aby uzyskać informacje dotyczące procesu roboczego hybrydowego systemu uruchamiania automatyzacji, zobacz [Automatyzuj zasoby w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego żyli .](../../automation/automation-hybrid-runbook-worker.md)

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurowanie programu Operations Manager, w którym wszyscy agenci używają tego samego serwera proxy

Konfiguracja serwera proxy programu Operations Manager jest automatycznie stosowana do wszystkich agentów, którzy zgłaszają się do programu Operations Manager, nawet jeśli ustawienie jest puste.  

Aby używać bramy oms do obsługi programu Operations Manager, musisz mieć:

* Agent monitorowania firmy Microsoft (wersja 8.0.10900.0 lub nowsza) zainstalowany na serwerze bramy oms i skonfigurowany przy użyciu tych samych obszarów roboczych usługi Log Analytics, które grupa zarządzania jest skonfigurowana do raportowania.
* Połączenie z Internetem. Alternatywnie brama systemu OMS musi być połączona z serwerem proxy połączonym z Internetem.

> [!NOTE]
> Jeśli nie określisz żadnej wartości bramy, puste wartości są wypychane do wszystkich agentów.
>

Jeśli grupa zarządzania programu Operations Manager rejestruje się w obszarze roboczym usługi Log Analytics po raz pierwszy, nie zostanie wyświetlone określenie konfiguracji serwera proxy dla grupy zarządzania w konsoli Operacje. Ta opcja jest dostępna tylko wtedy, gdy grupa zarządzania została zarejestrowana w usłudze.  

Aby skonfigurować integrację, należy zaktualizować konfigurację serwera proxy systemu przy użyciu programu Netsh w systemie, w którym jest uruchomiona konsola Operacje, oraz na wszystkich serwerach zarządzania w grupie zarządzania. Wykonaj następujące kroki:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień:

   a. Wybierz **start** i wprowadź **polecenie cmd**.  

   b. Kliknij prawym przyciskiem myszy **wiersz polecenia** i wybierz polecenie Uruchom **jako administrator**.  

1. Wprowadź następujące polecenie:

   `netsh winhttp set proxy <proxy>:<port>`

Po zakończeniu integracji z log analytics, `netsh winhttp reset proxy`usuń zmianę, uruchamiając . Następnie w konsoli Operacje użyj opcji **Konfiguruj serwer proxy,** aby określić serwer bramy usługi Log Analytics. 

1. Na konsoli programu Operations Manager w obszarze **Pakiet zarządzania operacjami**wybierz pozycję **Połączenie**, a następnie wybierz pozycję **Konfiguruj serwer proxy**.

   ![Zrzut ekranu przedstawiający program Operations Manager z wyborem Konfiguruj serwer proxy](./media/gateway/scom01.png)

1. Wybierz **pozycję Użyj serwera proxy, aby uzyskać dostęp do pakietu Operations Management Suite,** a następnie wprowadź adres IP serwera bramy usługi Log Analytics lub wirtualnego adresu IP modułu równoważenia obciążenia. Należy uważać, aby `http://`rozpocząć od prefiksu .

   ![Zrzut ekranu przedstawiający program Operations Manager przedstawiający adres serwera proxy](./media/gateway/scom02.png)

1. Wybierz **pozycję Zakończ**. Grupa zarządzania programu Operations Manager jest teraz skonfigurowana do komunikowania się za pośrednictwem serwera bramy z usługą analizy dzienników.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurowanie programu Operations Manager, w którym określone agenci używają serwera proxy

W przypadku dużych lub złożonych środowisk można używać serwera bramy usługi Log Analytics tylko określonym serwerom (lub grupom).  W przypadku tych serwerów nie można zaktualizować agenta programu Operations Manager bezpośrednio, ponieważ ta wartość jest zastępowany przez wartość globalną dla grupy zarządzania.  Zamiast tego należy zastąpić regułę używaną do wypychania tych wartości.  

> [!NOTE] 
> Użyj tej techniki konfiguracji, jeśli chcesz zezwolić na wiele serwerów bramy usługi Log Analytics w twoim środowisku.  Na przykład można wymagać określonych serwerów bramy usługi Log Analytics, które mają być określone na poziomie regionalnym.
>

Aby skonfigurować określone serwery lub grupy do korzystania z serwera bramy usługi Log Analytics: 

1. Otwórz konsolę programu Operations Manager i wybierz obszar **roboczy Tworzenie.**  
1. W obszarze roboczym Tworzenie wybierz pozycję **Reguły**. 
1. Na pasku narzędzi programu Operations Manager wybierz przycisk **Zakres.** Jeśli ten przycisk nie jest dostępny, upewnij się, że w okienku **Monitorowanie** wybrano obiekt, a nie folder. W oknie dialogowym **Obiekty pakietu administracyjnego zakresu** jest wyświetlana lista typowych klas, grup lub obiektów przeznaczonych do użycia. 
1. W polu **Szukaj** wprowadź **usługę kondycji** i wybierz ją z listy. Kliknij przycisk **OK**.  
1. Wyszukaj **regułę ustawiania serwera proxy doradcy**. 
1. Na pasku narzędzi programu Operations Manager wybierz pozycję **Zastąp,** a następnie wskaż pozycję **Zastąp regułę\Dla określonego obiektu klasy: Usługa kondycji** i wybierz obiekt z listy.  Możesz też utworzyć grupę niestandardową zawierającą obiekt usługi kondycji serwerów, do których chcesz zastosować to zastąpienie. Następnie zastosuj zastąpienie do grupy niestandardowej.
1. W oknie dialogowym **Zastępowanie właściwości** dodaj znacznik wyboru w kolumnie **Zastąrzyj** obok parametru **WebProxyAddress.**  W polu **Zastąpaj wartość** wprowadź adres URL serwera bramy usługi Log Analytics. Należy uważać, aby `http://`rozpocząć od prefiksu .  

    >[!NOTE]
    > Nie musisz włączać reguły. Jest już zarządzany automatycznie z zastąpieniem w pakiecie administracyjnym Microsoft System Center Advisor Secure Reference Override, który jest przeznaczony dla grupy serwerów monitorowania programu Microsoft System Center Advisor.
    > 

1. Wybierz pakiet administracyjny z listy **Wybierz docelowy pakiet administracyjny** lub utwórz nowy niezamężony pakiet administracyjny, wybierając pozycję **Nowy**. 
1. Po zakończeniu wybierz pozycję **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurowanie hybrydowych pomieszczeń ceł hybrydowych automatyzacji

Jeśli masz środowiska Automation Hybrydowe procesy owebook pracowników w swoim środowisku, wykonaj następujące kroki, aby skonfigurować bramę do obsługi pracowników.

Zapoznaj się z sekcją [Konfigurowanie sieci](../../automation/automation-hybrid-runbook-worker.md#network-planning) dokumentacji automatyzacji, aby znaleźć adres URL dla każdego regionu.

Jeśli komputer jest automatycznie zarejestrowany jako hybrydowy proces roboczy systemu runbook, na przykład jeśli rozwiązanie do zarządzania aktualizacjami jest włączone dla jednej lub kilku maszyn wirtualnych, wykonaj następujące kroki:

1. Dodaj adresy URL usługi danych środowiska wykonawczego zadania do listy Dozwolony host na bramie usługi Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy usługi Log Analytics przy użyciu następującego polecenia cmdlet programu PowerShell:`Restart-Service OMSGatewayService`

Jeśli komputer jest przyłączony do usługi Azure Automation przy użyciu polecenia cmdlet rejestracji hybrydowego procesu roboczego systemu runbook, wykonaj następujące kroki:

1. Dodaj adres URL rejestracji usługi agenta do listy Dozwolony host na bramie usługi Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Dodaj adresy URL usługi danych środowiska wykonawczego zadania do listy Dozwolony host na bramie usługi Log Analytics. Na przykład: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Uruchom ponownie usługę bramy usługi usługi Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Przydatne polecenia cmdlet programu PowerShell

Polecenia cmdlet służy do wykonywania zadań, aby zaktualizować ustawienia konfiguracji bramy usługi Log Analytics. Przed użyciem poleceń cmdlet należy pamiętać o:

1. Zainstaluj bramę usługi Log Analytics (Instalator Microsoft Windows).
1. Otwórz okno konsoli programu PowerShell.
1. Zaimportuj moduł, wpisując to polecenie:`Import-Module OMSGateway`
1. Jeśli w poprzednim kroku nie wystąpił żaden błąd, moduł został pomyślnie zaimportowany i można użyć poleceń cmdlet. Wprowadź wartość `Get-Module OMSGateway`
1. Po dokonaniu zmian za pomocą poleceń cmdlet uruchom ponownie usługę Brama systemu OMS.

Błąd w kroku 3 oznacza, że moduł nie został zaimportowany. Błąd może wystąpić, gdy program PowerShell nie może znaleźć modułu. Moduł można znaleźć w ścieżce instalacji bramy systemu OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Polecenie cmdlet** | **Parametry** | **Opis** | **Przykład** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Klucz |Pobiera konfigurację usługi |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Klucz (wymagany) <br> Wartość |Zmienia konfigurację usługi |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Pobiera adres serwera proxy przekazywania (nadrzędnego) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Nazwa użytkownika<br> Hasło (bezpieczny ciąg znaków) |Ustawia adres (i poświadczenia) serwera proxy przekazywania (nadrzędnego) |1. Ustaw serwer proxy i poświadczenia przekazywania:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ustaw serwer proxy przekazywania, który nie wymaga uwierzytelniania:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Wyczyść ustawienie serwera proxy przekaźnika:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Pobiera aktualnie dozwolony host (tylko lokalnie skonfigurowany dozwolony host, nie jest automatycznie pobierany dozwolony host) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (wymagany) |Dodaje hosta do listy dozwolonych |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (wymagany) |Usuwa hosta z listy dozwolonych |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Temat (wymagany) |Dodaje przedmiot certyfikatu klienta do listy dozwolonych |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Temat (wymagany) |Usuwa podmiot certyfikatu klienta z listy dozwolonych |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Pobiera aktualnie dozwolone podmioty certyfikatu klienta (tylko lokalnie skonfigurowane dozwolone tematy, a nie automatycznie pobierane dozwolone tematy) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby zbierać zdarzenia zarejestrowane przez bramę, powinien być zainstalowany agent usługi Log Analytics.

![Zrzut ekranu przedstawiający listę Podgląd zdarzeń w dzienniku bramy usługi Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Identyfikatory zdarzeń i opisy bramy usługi Log Analytics

W poniższej tabeli przedstawiono identyfikatory zdarzeń i opisy zdarzeń dziennika bramy usługi Log Analytics.

| **ID** | **Opis** |
| --- | --- |
| 400 |Każdy błąd aplikacji, który nie ma określonego identyfikatora. |
| 401 |Nieprawidłowa konfiguracja. Na przykład listenPort = "tekst" zamiast liczby całkowitej. |
| 402 |Wyjątek podczas analizowania komunikatów uzgadniania TLS. |
| 403 |Błąd sieci. Na przykład nie można połączyć się z serwerem docelowym. |
| 100 |Informacje ogólne. |
| 101 |Usługa została uruchomiona. |
| 102 |Usługa została zatrzymana. |
| 103 |Odebrano polecenie HTTP CONNECT od klienta. |
| 104 |Nie jest to polecenie HTTP CONNECT. |
| 105 |Serwer docelowy nie znajduje się na liście dozwolonych lub port docelowy nie jest bezpieczny (443). <br> <br> Upewnij się, że agent MMA na serwerze bramy OMS i agenci komunikujący się z bramą OMS są połączeni z tym samym obszarem roboczym usługi Log Analytics. |
| 105 |BŁĄD TcpConnection — nieprawidłowy certyfikat klienta: CN=Brama. <br><br> Upewnij się, że używasz bramy OMS w wersji 1.0.395.0 lub większej. Upewnij się również, że agent MMA na serwerze bramy OMS i agenci komunikujący się z bramą OMS są połączeni z tym samym obszarem roboczym usługi Log Analytics. |
| 106 |Nieobsługiwała wersja protokołu TLS/SSL.<br><br> Brama usługi Log Analytics obsługuje tylko protokół TLS 1.0, TLS 1.1 i 1.2. Nie obsługuje ssl.|
| 107 |Sesja TLS została zweryfikowana. |

### <a name="performance-counters-to-collect"></a>Liczniki wydajności do zebrania

W poniższej tabeli przedstawiono liczniki wydajności dostępne dla bramy usługi Log Analytics. Użyj Monitora wydajności, aby dodać liczniki.

| **Nazwa** | **Opis** |
| --- | --- |
| Usługa Log Analytics Gateway/Aktywne połączenie klienta |Liczba połączeń aktywnej sieci klienta (TCP) |
| Liczba błędów bramy/błędów usługi Log Analytics |Liczba błędów |
| Brama analizy dzienników/połączony klient |Liczba połączonych klientów |
| Liczba bram/odrzuceń usługi Log Analytics |Liczba odrzuceń spowodowanych błędem sprawdzania poprawności TLS |

![Zrzut ekranu przedstawiający interfejs bramy usługi Log Analytics z licznikami wydajności](./media/gateway/counters.png)

## <a name="assistance"></a>Pomoc

Po zalogowaniu się do witryny Azure portal można uzyskać pomoc dotyczącą bramy usługi Log Analytics lub dowolnej innej usługi lub funkcji platformy Azure.
Aby uzyskać pomoc, wybierz ikonę znaku zapytania w prawym górnym rogu portalu i wybierz **pozycję Nowe żądanie pomocy technicznej**. Następnie wypełnij nowy formularz żądania pomocy technicznej.

![Zrzut ekranu przedstawiający nowe żądanie pomocy technicznej](./media/gateway/support.png)

## <a name="next-steps"></a>Następne kroki

[Dodaj źródła danych](../../azure-monitor/platform/agent-data-sources.md) do zbierania danych z połączonych źródeł i przechowuj dane w obszarze roboczym usługi Log Analytics.
