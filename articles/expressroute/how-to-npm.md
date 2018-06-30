---
title: Konfigurowanie monitora wydajności sieci dla usługi Azure ExpressRoute obwodów | Dokumentacja firmy Microsoft
description: Konfigurowanie monitorowania (NPM) dla usługi Azure ExpressRoute obwodów sieci opartej na chmurze. Obejmuje to monitorowanie przez prywatnej komunikacji równorzędnej ExpressRoute i komunikacji równorzędnej firmy Microsoft.
documentationcenter: na
services: expressroute
author: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: 47f219b7319e4d2bbadf03954f7bd7f6f39da3b4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128983"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute

Monitor wydajności sieci (NPM) jest sieci opartej na chmurze rozwiązanie monitorujące, które monitoruje łączność między wdrożenia chmury Azure i lokalizacje lokalnymi (biurach oddziałów, itp.). NPM jest częścią analizy dzienników. NPM udostępnia rozszerzenie dla usługi ExpressRoute, który umożliwia monitorowanie wydajności sieci za pośrednictwem obwody usługi ExpressRoute, które są skonfigurowane do używania prywatnej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft. Po skonfigurowaniu programu NPM dla usługi ExpressRoute, można wykrywać problemy z siecią do identyfikowania i eliminowania. Ta usługa jest również dostępny do chmury Azure dla instytucji rządowych.

Możesz:

* Monitorowanie utraty i opóźnienia między różnymi sieciami wirtualnymi i Ustaw alerty

* Monitorowanie wszystkich ścieżek (w tym nadmiarowe ścieżki) w sieci

* Rozwiązywanie problemów przejściowych i w momencie sieci, które są trudne do replikacji

* Pomagają w ustaleniu określonego segmentu sieci, która jest odpowiedzialna za pogorszenie wydajności

* Pobierz przepływności dla sieci wirtualnej (Jeśli masz agentów zainstalowanych w każdej sieci wirtualnej)

* Wyświetlać stan systemu ExpressRoute z poprzedniego punktu w czasie

## <a name="workflow"></a>Przepływ pracy

Monitorowania agenci są zainstalowani na wielu serwerach, zarówno lokalnie i na platformie Azure. Agenci komunikują się ze sobą, ale nie wysyłaj danych, wysyłają pakiety uzgadnianie protokołu TCP. Komunikacja między agentami umożliwia platformie Azure mapy topologii sieci i ścieżkę, którą może zająć ruchu.

1. Utwórz obszar roboczy programu NPM. Jest to ten sam obszar roboczy OMS.
2. Instalowanie i konfigurowanie agentów oprogramowania: 
    * Agenci monitorowania należy zainstalować na serwerach lokalnych i maszyn wirtualnych platformy Azure (dla prywatnej komunikacji równorzędnej).
    * Skonfiguruj ustawienia na serwerze agenta monitorowania umożliwia agenci monitorowania do komunikacji. (Otwórz porty zapory itp.)
3. Konfigurowanie reguł grupa zabezpieczeń sieci umożliwia agenta monitorowania zainstalowanych na maszynach wirtualnych Azure do komunikowania się z lokalnymi monitorowanie agentów.
4. Konfigurowanie monitorowania: Wykryj automatycznie i zarządzanie sieciami, które są widoczne w NPM.

Jeśli korzystasz już z Monitora wydajności sieci do monitorowania innych obiektów lub usługi, a masz już obszar roboczy w jednym z obsługiwanych regionów, można pominąć krok 1 i 2 i rozpocząć konfigurację z kroku 3.

## <a name="configure"></a>Krok 1: Tworzenie obszaru roboczego

Tworzenie obszaru roboczego w subskrypcji, która ma łącze sieci wirtualnych do ExpressRoute circuit(s).

1. W [portalu Azure](https://portal.azure.com), wybierz subskrypcję, która ma sieci wirtualne połączyć za pomocą do obwodu usługi ExpressRoute. Następnie wyszukaj na liście usług w **Marketplace** "Monitor wydajności sieci". Powrotu, kliknij, aby otworzyć **monitora wydajności sieci** strony.

   >[!NOTE]
   >Utwórz nowy obszar roboczy lub korzystać z istniejącym obszarem roboczym. Jeśli chcesz korzystać z istniejącym obszarem roboczym należy się upewnić, że obszar roboczy przeprowadzono migrację do nowego języka zapytań. [Więcej informacji...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](.\media\how-to-npm\3.png)<br><br>
2. W dolnej części głównym **monitora wydajności sieci** kliknij przycisk **Utwórz** otworzyć **sieci monitora wydajności — Utwórz nowe rozwiązanie** strony. Kliknij przycisk **obszarem roboczym pakietu OMS — wybierz obszar roboczy** aby otworzyć stronę obszarów roboczych. Kliknij przycisk **+ Utwórz nowy obszar roboczy** aby otworzyć stronę obszaru roboczego.
3. Na **obszarem roboczym pakietu OMS** wybierz pozycję **Utwórz nowy**, następnie skonfiguruj następujące ustawienia:

  * Obszar roboczy OMS - wpisanie nazwy obszaru roboczego.
  * Subskrypcja — Jeśli masz wiele subskrypcji, wybierz jedną, które chcesz skojarzyć z nowego obszaru roboczego.
  * Grupa zasobów — Utwórz grupę zasobów lub użyć istniejącego.
  * Lokalizacja — ta lokalizacja jest używana do określenia lokalizacji konta magazynu, który służy do dzienników połączenia agenta.
  * Warstwa cenowa — wybierz warstwę cenową.
  
    >[!NOTE]
    >Obwód usługi expressroute może być dowolnym miejscu na świecie. Nie ma w tym samym regionie co obszaru roboczego.
    >
  
    ![obszar roboczy](.\media\how-to-npm\4.png)<br><br>
4. Kliknij przycisk **OK** Aby zapisać i wdrożyć ustawienia szablonu. Po weryfikuje szablonu, kliknij przycisk **Utwórz** do wdrożenia w obszarze roboczym.
5. Po wdrożeniu obszaru roboczego, przejdź do **NetworkMonitoring(name)** utworzony zasób. Sprawdź poprawność ustawień, a następnie kliknij przycisk **rozwiązanie wymaga dodatkowej konfiguracji**.

   ![dodatkowa konfiguracja](.\media\how-to-npm\5.png)

## <a name="agents"></a>Krok 2: Instalowanie i konfigurowanie agentów

### <a name="download"></a>2.1: Pobierz plik Instalatora agenta

1. Przejdź do **typowe ustawienia** karcie **konfiguracji monitora wydajności sieci** strony dla zasobu. Kliknij agenta, który odpowiada procesora na serwerze z **zainstalować agentów OMS** sekcji i pobranie pliku konfiguracji.
2. Następnie skopiuj **identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
3. Z **Konfigurowanie agentów OMS monitorowania przy użyciu protokołu TCP** sekcji, Pobierz skrypt programu Powershell. Skrypt programu PowerShell ułatwia otworzyć port zapory odpowiednie dla transakcji protokołu TCP.

  ![Skrypt programu PowerShell](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: Zainstaluj agenta monitorowania na każdym serwerze monitorowania (w każdej sieci Wirtualnej, który chcesz monitorować)

Zalecamy zainstalowanie co najmniej dwóch agentów po obu stronach połączenia ExpressRoute w celu zapewnienia nadmiarowości (na przykład w infrastrukturze lokalnej, sieci wirtualnych platformy Azure). Musi być zainstalowany agent w systemie Windows Server (2008 z dodatkiem SP1 lub nowszym). Monitorowanie przy użyciu systemu operacyjnego Windows pulpitu i systemu operacyjnego Linux obwody usługi ExpressRoute nie jest obsługiwane. Aby zainstalować agentów, wykonaj następujące kroki:
   
  >[!NOTE]
  >Wypychana agentów przez SCOM (obejmuje [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) nie może być spójnie wykryć ich lokalizacji, jeśli są one hostowane na platformie Azure. Firma Microsoft zaleca, aby używać tych agentów w sieci wirtualnych platformy Azure do monitorowania usługi ExpressRoute.
  >

1. Uruchom **Instalator** do zainstalowania agenta na każdym serwerze, który ma być używany do monitorowania usługi ExpressRoute. Używanego do monitorowania serwera może być maszyny Wirtualnej lub lokalnymi i musi mieć dostęp do Internetu. Należy zainstalować co najmniej jeden lokalny agent i jednego agenta w każdym segmencie sieci, które mają być monitorowane na platformie Azure.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na **postanowień licencyjnych** odczytu licencji, a następnie kliknij przycisk **zgadzam się**.
4. Na **Folder docelowy** , zmienić lub zachować domyślny folder instalacji, a następnie kliknij przycisk **dalej**.
5. Na **opcje instalacji agenta** strony, użytkownik może Połącz agenta z usługi Analiza dzienników Azure lub programu Operations Manager. Lub puste opcji, jeśli chcesz później skonfigurować agenta. Po wprowadzeniu selection(s) Twojego, kliknij przycisk **dalej**.

  * Jeśli chcesz połączyć się z **Azure Log Analytics**, Wklej **identyfikator obszaru roboczego** i **klucz obszaru roboczego** (klucz podstawowy) skopiowane do Notatnika w poprzedniej sekcji. Następnie kliknij przycisk **Dalej**.

    ![Identyfikator i klucz](.\media\how-to-npm\8.png)
  * Jeśli chcesz połączyć się z **programu Operations Manager**na **Konfiguracja grupy zarządzania** wpisz **Nazwa grupy zarządzania**, **serwera zarządzania** i **Port serwera zarządzania**. Następnie kliknij przycisk **Dalej**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Na **konto akcji agenta** wybierz **systemu lokalnego** konta, lub **domeny lub lokalnego konta komputera**. Następnie kliknij przycisk **Dalej**.

    ![Konto](.\media\how-to-npm\10.png)
6. Na **gotowy do instalacji** , przejrzyj wybrane opcje, a następnie kliknij przycisk **zainstalować**.
7. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.
8. Po zakończeniu programu Microsoft Monitoring Agent pojawi się w Panelu sterowania. Można przejrzeć konfigurację istnieje i sprawdź, czy agent jest podłączony do Analiza dzienników Azure (OMS). Po podłączeniu, agent wyświetla komunikat z informacją: **programu Microsoft Monitoring Agent pomyślnie połączył się z usługą Microsoft Operations Management Suite**.

9. Powtórz tę procedurę dla każdej sieci Wirtualnej, które powinny być monitorowane.

### <a name="proxy"></a>2.3: Konfigurowanie ustawień serwera proxy (opcjonalnie)

Jeśli używasz serwera proxy sieci web do uzyskania dostępu do Internetu, następujące kroki umożliwiają konfigurowanie ustawień serwera proxy dla programu Microsoft Monitoring Agent. Wykonaj te kroki dla każdego serwera. Jeśli masz wiele serwerów, które trzeba skonfigurować, łatwiejszym rozwiązaniem może być użycie skryptu automatyzującego ten proces. Jeśli tak, zobacz [skonfigurować ustawienia serwera proxy dla programu Microsoft Monitoring Agent za pomocą skryptu](../log-analytics/log-analytics-windows-agent.md).

Aby skonfigurować ustawienia serwera proxy dla programu Microsoft Monitoring Agent w Panelu sterowania:

1. Otwórz **Panel sterowania**.
2. Otwórz program **Microsoft Monitoring Agent**.
3. Kliknij kartę **Ustawienia serwera proxy**.
4. Wybierz **Użyj serwera proxy** i wpisz adres URL i numer portu, jeśli jest ono wymagane. Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uzyskać dostęp do serwera proxy.

  ![Serwer proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: Sprawdź łączność agenta

Można łatwo sprawdzić, czy komunikują się agentów.

1. Na serwerze z agenta monitorowania, należy otworzyć **Panelu sterowania**.
2. Otwórz **programu Microsoft Monitoring Agent**.
3. Kliknij przycisk **Azure Log Analytics** kartę.
4. W **stan** kolumny, powinny być widoczne czy agent pomyślnie połączony analizy dzienników.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: otworzyć porty zapory na serwerze agenta monitorowania

Aby korzystać z protokołu TCP, należy otworzyć porty zapory, aby upewnić się, że agenci monitorowania może komunikować się.

Można uruchomić skryptu programu PowerShell do tworzenia kluczy rejestru wymaganych przez Monitor wydajności sieci. Ten skrypt tworzy również reguł zapory systemu Windows w celu umożliwienia monitorowania agentów do utworzenia połączenia TCP ze sobą. Klucze rejestru utworzony przez skrypt Określ, czy dzienniki debugowania i ścieżka do pliku dzienniki dziennika. Definiuje również port TCP agent używany do komunikacji. Wartości te klucze są automatycznie ustawiane przez skrypt. Nie należy ręcznie zmienić tych kluczy.

Port 8084 jest domyślnie otwierany. Można użyć niestandardowego numeru portu, podając parametr "numer_portu" do skryptu. Jednak zrobić, należy określić ten sam port dla wszystkich serwerów, na których uruchomiono skrypt.

>[!NOTE]
>Skrypt programu PowerShell "EnableRules" umożliwia skonfigurowanie reguł zapory systemu Windows tylko na serwerze, na którym skrypt jest uruchamiany. Jeśli masz zapory sieciowej, należy upewnić się, że umożliwia ruch kierowany do portu TCP używany przez Monitor wydajności sieci.
>
>

Na serwerach agenta Otwórz okno programu PowerShell z uprawnieniami administracyjnymi. Uruchom [EnableRules](https://aka.ms/npmpowershellscript) skrypt programu PowerShell, (który został wcześniej pobrany). Nie należy używać żadnych parametrów.

![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Krok 3: Konfigurowanie zasad grupy zabezpieczeń sieci

Aby monitorować serwery agenta, które znajdują się w usłudze Azure, należy skonfigurować reguły grupa zabezpieczeń sieci zezwalająca na ruch TCP na porcie używany przez NPM dla transakcji syntetycznych. Domyślny port to 8084. Dzięki temu agent monitorowania zainstalowany na maszynie Wirtualnej platformy Azure do komunikowania się z lokalnymi agenta monitorowania.

Aby uzyskać więcej informacji na temat grupy NSG, zobacz [grup zabezpieczeń sieci](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Upewnij się, zainstalowano agentów (agent serwera lokalnego i agent serwera Azure) i zostało uruchomione przed kontynuowaniem skrypt programu PowerShell w ramach tego kroku.
>

## <a name="setupmonitor"></a>Krok 4: Odnajdywanie połączenia komunikacji równorzędnej

1. Przejdź do monitora wydajności sieci kafelka przeglądu, przechodząc do **wszystkie zasoby** strony, a następnie kliknij na białej obszaru roboczego programu NPM.

  ![obszar roboczy programu npm](.\media\how-to-npm\npm.png)
2. Kliknij przycisk **monitora wydajności sieci** kafelka przeglądu, aby wyświetlić pulpit nawigacyjny. Pulpit nawigacyjny zawiera strony ExpressRoute, który pokazuje, że usługi ExpressRoute w "stanu nieskonfigurowanego". Kliknij przycisk **instalacji funkcji** aby otworzyć stronę konfiguracji monitora wydajności sieci.

  ![Ustawienia funkcji](.\media\how-to-npm\npm2.png)
3. Na stronie konfiguracji przejdź do karty "ExpressRoute komunikacji równorzędnych", znajduje się w okienku po lewej stronie. Następnie kliknij przycisk **odnajdywanie teraz**.

  ![odnajdź](.\media\how-to-npm\13.png)
4. Po zakończeniu odnajdowania, zostanie wyświetlona lista zawierająca następujące elementy:
  * Wszystkie połączenia komunikacji równorzędnej firmy Microsoft w circuit(s) ExpressRoute, które są skojarzone z tą subskrypcją.
  * Wszystkie prywatnej komunikacji równorzędnej połączeń sieci wirtualne są skojarzone z tą subskrypcją.
            
## <a name="configmonitor"></a>Krok 5: Konfigurowanie monitorów

W tej sekcji skonfigurujesz monitorów. Wykonaj kroki dla typu komunikacji równorzędnej, które chcesz monitorować: **prywatnej komunikacji równorzędnej**, lub **komunikacji równorzędnej firmy Microsoft**.

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

Dla prywatnej komunikacji równorzędnej, po zakończeniu odnajdywania, jest wyświetlany będzie zasady unikatowy **nazwę obwodu** i **nazwa sieci wirtualnej**. Początkowo te zasady są wyłączone.

![rules](.\media\how-to-npm\14.png)

1. Sprawdź **monitorować komunikacji równorzędnej** wyboru.
2. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla komunikacji równorzędnej**.
3. Wybierz warunki monitorowania. Można ustawić niestandardowe progi, aby wygenerować kondycji zdarzeń, wpisując wartości progowe. Zawsze, gdy wartość warunku wykraczają poza jego próg dla pary wybranego sieć/podsieć, jest generowane zdarzenie kondycji.
4. Kliknij AGENTÓW lokalnych ON **dodać agentów** przycisk, aby dodać serwerów lokalnych, z których chcesz monitorować połączenia prywatnej komunikacji równorzędnej. Upewnij się, wybierz tylko tych agentów, które mają połączenia z punktem końcowym usługi firmy Microsoft, który określono w sekcji Krok 2. Agenci lokalnej musi mieć możliwość łączności z punktem końcowym przy użyciu połączenia ExpressRoute.
5. Zapisz ustawienia.
6. Po włączenie zasad i wybraniu wartości i agenci mają być monitorowane, jest Poczekaj około 30 – 60 minut dla wartości rozpocząć wypełniania i **ExpressRoute monitorowania** Kafelki, które stają się dostępne.

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

Dla komunikacji równorzędnej firmy Microsoft, kliknij połączenia komunikacji równorzędnej firmy Microsoft, który chcesz monitorować, a następnie skonfiguruj ustawienia.

1. Sprawdź **monitorować komunikacji równorzędnej** wyboru. 
2. (Opcjonalnie) Punkt końcowy usługi Microsoft docelowej, można zmienić. Domyślnie NPM wybiera punkt końcowy usługi firmy Microsoft jako element docelowy. NPM monitoruje łączność z serwerami lokalnymi do tego punktu końcowego docelowym za pośrednictwem usługi ExpressRoute. 
    * Aby zmienić ten punkt końcowy docelowych, kliknij przycisk **(Edycja)** łącze w obszarze **docelowych:** i wybierz z listy adresów URL punktu końcowego innej firmy Microsoft usługi docelowej.
      ![edytowanie docelowego](.\media\how-to-npm\edit_target.png)<br>

    * Można użyć niestandardowego adresu URL lub adres IP. Ta opcja jest szczególnie ważne, jeśli używasz komunikację równorzędną, aby nawiązać połączenie z usług Azure PaaS, takich jak usługi Azure Storage, baz danych i witryn sieci Web, które są oferowane na publiczne adresy IP firmy Microsoft. Aby to zrobić, kliknij łącze **(Użyj niestandardowy adres URL lub adres IP)** w dolnej części listy adresów URL, a następnie wprowadź publiczny punkt końcowy usługi Azure PaaS, który jest połączony za pośrednictwem komunikacji równorzędnej ExpressRoute firmy Microsoft.
    ![Niestandardowy adres URL](.\media\how-to-npm\custom_url.png)<br>

    * Jeśli używasz tych opcjonalnych ustawień, upewnij się, czy tylko punkt końcowy usługi Microsoft wybrano tutaj. Punkt końcowy musi być połączony ExpressRoute i osiągalny przez agentów lokalnych.
3. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla komunikacji równorzędnej**.
4. Wybierz warunki monitorowania. Można ustawić niestandardowe progi, aby wygenerować kondycji zdarzeń, wpisując wartości progowe. Zawsze, gdy wartość warunku wykraczają poza jego próg dla pary wybranego sieć/podsieć, jest generowane zdarzenie kondycji.
5. Kliknij AGENTÓW lokalnych ON **dodać agentów** przycisk, aby dodać serwerów lokalnych, z których chcesz monitorować połączenia komunikacji równorzędnej firmy Microsoft. Upewnij się, wybierz tylko tych agentów, które mają łączność z punktów końcowych usługi firmy Microsoft, które zostały określone w sekcji Krok 2. Agenci lokalnej musi mieć możliwość łączności z punktem końcowym przy użyciu połączenia ExpressRoute.
6. Zapisz ustawienia.
7. Po włączenie zasad i wybraniu wartości i agenci mają być monitorowane, jest Poczekaj około 30 – 60 minut dla wartości rozpocząć wypełniania i **ExpressRoute monitorowania** Kafelki, które stają się dostępne.

## <a name="explore"></a>Krok 6: Wyświetl Kafelki monitorowania

Gdy zobaczysz Kafelki monitorowania obwody usługi ExpressRoute i siecią połączenia są monitorowane przez NPM. Kliknięcie kafelka Microsoft Peering drążenie kondycję połączenia Peering firmy Microsoft.

![monitorowanie kafelków](.\media\how-to-npm\15.png)

### <a name="dashboard"></a>Strona wydajności monitorowania sieci

Na stronie NPM zawiera stronę usługi ExpressRoute, który zawiera przegląd kondycji obwody usługi ExpressRoute i komunikacji równorzędnych.

![Pulpit nawigacyjny](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Lista obwody

Aby wyświetlić listę wszystkich monitorowanych obwody usługi ExpressRoute, kliknij przycisk **obwody usługi ExpressRoute** kafelka. Możesz wybrać obwód i wyświetlić jej stan kondycji, wykresy trendu do utraty pakietów, użycie przepustowości i opóźnień. Wykresy są interaktywne. Możesz wybrać okno czasu niestandardowych do kreślenia wykresy. Na wykresie powiększanie i zobaczyć punkty szczegółowych danych można przeciągnąć myszy nad obszarem.

![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Trend utraty, opóźnienia i przepływności

Wykresy przepustowości, opóźnienia i utratę są interaktywne. W dowolnej sekcji wykresy te można powiększyć za pomocą formantów myszy. Przepustowość, czas oczekiwania i utraty danych można wyświetlić dla innych interwałów, klikając **daty/godziny**, który znajduje się poniżej przycisk akcje w lewym górnym rogu.

![Trend](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Lista komunikacji równorzędnych

Aby wyświetlić listę wszystkich połączeń do sieci wirtualnych za pośrednictwem prywatnej komunikacji równorzędnej, kliknij przycisk **prywatnej komunikacji równorzędnych** kafelka na pulpicie nawigacyjnym. W tym miejscu można wybrać wirtualnej połączenie sieciowe i wyświetlić jej stan kondycji, wykresy trendu do utraty pakietów, użycie przepustowości i opóźnień.

![Lista obwodu](.\media\how-to-npm\peerings.png)

### <a name="nodes"></a>Wyświetlanie węzłów

Aby wyświetlić listę wszystkich łączy między węzłami lokalnymi a punktami końcowymi usług Azure maszyny wirtualne/Microsoft dla wybranego połączenia komunikacji równorzędnej ExpressRoute, kliknij przycisk **wyświetlanie łączy węzłów**. Można wyświetlić stan kondycji każdego łącza, a także trend utraty i opóźnienia skojarzonych z nimi.

![Wyświetlanie węzłów](.\media\how-to-npm\nodes.png)

### <a name="topology"></a>Topologia obwodu

Aby wyświetlić topologii obwód, kliknij przycisk **topologii** kafelka. Powoduje to przejście do wybranego widoku topologii obwodu lub komunikacji równorzędnej. Diagram topologii zawiera czas oczekiwania na każdy z segmentów w sieci. Każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Kliknięcie przeskoku ujawnia więcej szczegółów na temat przeskoku.

Można zwiększyć poziom widoczności uwzględnienie przeskoków lokalnymi Przesuń suwak poniżej **filtry**. Przenoszenie suwak w lewo lub w prawo, zwiększa/zmniejsza liczbę przeskoków na wykresie topologii. Czas oczekiwania w każdym segmencie jest widoczny, która umożliwia szybsze izolacji segmentów duże opóźnienie w sieci.

![filtry](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Widok szczegółowy topologii obwodu

Ten widok przedstawia połączeń sieci wirtualnej.
![szczegółowe topologii](.\media\how-to-npm\17.png)