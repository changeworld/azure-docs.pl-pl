---
title: Konfigurowanie monitora wydajności sieci dla obwodów usługi ExpressRoute — Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie monitorowania (NPM) dla obwodów usługi ExpressRoute platformy Azure sieci opartych na chmurze. W tym artykule opisano monitorowania za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute i komunikacji równorzędnej firmy Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 180075f13be2cc2507a78e3d10a67a49a0c0cb12
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60840323"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute

Ten artykuł pomoże Ci skonfigurować rozszerzenia programu Network Performance Monitor do monitorowania usługi ExpressRoute. Network Performance Monitor (NPM) jest sieci opartych na chmurze rozwiązanie monitorujące, które monitoruje łączność między wdrożeniami w chmurze platformy Azure i lokalizacjami lokalnymi (oddziałów, itp.). NPM jest częścią dzienniki usługi Azure Monitor. NPM oferuje rozszerzenie dla usługi ExpressRoute, która umożliwia monitorowanie wydajności sieci obwodach usługi ExpressRoute, które są skonfigurowane do korzystania z prywatnej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft. Po skonfigurowaniu NPM dla usługi ExpressRoute, można wykryć problemy z siecią, aby zidentyfikować i wyeliminować. Ta usługa jest również dostępny dla chmury dla instytucji rządowych platformy Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Możesz:

* Monitorowanie strat i opóźnień w różnych sieciach wirtualnych i Ustawiaj alerty

* Monitorowanie wszystkich ścieżek (w tym nadmiarowe ścieżki) w sieci

* Błędy przejściowe i w momencie Rozwiązywanie problemów z sieci, które są trudne do replikacji

* Ułatwić określenie konkretnego segmentu sieci odpowiedzialnego za spadek wydajności

* Uzyskiwanie informacji o przepływności na sieć wirtualną, (w przypadku agentów zainstalowanych w każdej sieci wirtualnej)

* Zobacz dane o stanie systemu ExpressRoute z wcześniejszego punktu w czasie

## <a name="workflow"></a>Przepływ pracy

Agentów monitorowania zainstalowanych na wielu serwerach, lokalnie i na platformie Azure. Agenci komunikują się ze sobą, ale nie wysyłaj danych, wysyłają pakiety uzgadnianie protokołu TCP. Komunikacja między agentami umożliwia platformie Azure do mapowania topologii sieci i ścieżki, może potrwać ruchu sieciowego.

1. Tworzenie obszaru roboczego usługi NPM. To jest taka sama jak obszar roboczy usługi Log Analytics.
2. Instalowanie i konfigurowanie agentów oprogramowania. (Jeśli chcesz monitorować za pośrednictwem Peering firmy Microsoft, nie trzeba zainstalować i skonfigurować oprogramowanie agents.): 
    * Zainstaluj agentów monitorowania na serwerach lokalnych i maszyn wirtualnych platformy Azure (dla prywatnej komunikacji równorzędnej).
    * Konfigurowanie ustawień na serwerach programu agent monitorowania, aby umożliwić agentów monitorowania do komunikowania się. (Otwórz porty zapory itp.)
3. Skonfiguruj reguły Sieciowej grupy zabezpieczeń sieci, Zezwalaj na używanie agenta monitorowania, zainstalowane na maszynach wirtualnych platformy Azure do komunikowania się z lokalnymi agentów monitorowania.
4. Konfigurowanie monitorowania: Wykryj automatycznie i zarządzania, które sieci są widoczne w NPM.

Jeśli korzystasz już rozwiązania Network Performance Monitor do monitorowania innych obiektów lub usług, a jeszcze obszaru roboczego w jednym z obsługiwanych regionów, możesz pominąć krok 1 i 2 i rozpocząć konfigurację z kroku 3.

## <a name="configure"></a>Krok 1. Tworzenie obszaru roboczego

Utwórz obszar roboczy w ramach subskrypcji, która ma połączenie sieci wirtualnych obwody usługi ExpressRoute.

1. W [witryny Azure portal](https://portal.azure.com), wybierz subskrypcję, która ma sieci wirtualne skomunikowane równorzędnie do obwodu usługi ExpressRoute. Następnie wyszukaj listę usług w **Marketplace** dla Network Performance Monitor. W przypadku zwracania, kliknij, aby otworzyć **rozwiązania Network Performance Monitor** strony.

   >[!NOTE]
   >Utwórz nowy obszar roboczy lub użyj istniejącego obszaru roboczego. Jeśli chcesz użyć istniejącego obszaru roboczego, upewnij się, że obszar roboczy została zmigrowana do nowego języka zapytań. [Więcej informacji...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. W dolnej części głównej **rozwiązania Network Performance Monitor** kliknij **Utwórz** otworzyć **rozwiązania Network Performance Monitor — Utwórz nowe rozwiązanie** strony. Kliknij przycisk **obszar roboczy usługi Analytics Log - wybierz obszar roboczy** aby otworzyć stronę obszarów roboczych. Kliknij przycisk **+ Utwórz nowy obszar roboczy** aby otworzyć stronę obszaru roboczego.
3. Na **obszaru roboczego usługi Log Analytics** wybierz opcję **Utwórz nowy**, następnie skonfiguruj następujące ustawienia:

   * Zaloguj się obszar roboczy usługi Analytics — wpisz nazwę obszaru roboczego.
   * Subskrypcja — Jeśli masz wiele subskrypcji, wybierz jedną, które chcesz skojarzyć z nowym obszarem roboczym.
   * Grupa zasobów - Utwórz grupę zasobów lub użyć istniejącego.
   * Lokalizacja — ta lokalizacja będzie używana do określenia lokalizacji konta magazynu, które jest używane dla dzienników połączenia agenta.
   * Warstwa cenowa — wybierz warstwę cenową.
  
     >[!NOTE]
     >Obwód usługi ExpressRoute może być dowolnym miejscu na świecie. Nie musi znajdować się w tym samym regionie co obszar roboczy.
     >
  
     ![obszar roboczy](./media/how-to-npm/4.png)<br><br>
4. Kliknij przycisk **OK** Aby zapisać i wdrożyć ustawienia szablonu. Gdy szablon jest weryfikowana, kliknij przycisk **Utwórz** do wdrożenia obszaru roboczego.
5. Po wdrożeniu obszaru roboczego, przejdź do **NetworkMonitoring(name)** utworzonego zasobu. Sprawdź poprawność ustawień, a następnie kliknij przycisk **rozwiązanie wymaga dodatkowej konfiguracji**.

   ![dodatkowa konfiguracja](./media/how-to-npm/5.png)

## <a name="agents"></a>Krok 2. Instalacja i konfiguracja agentów

### <a name="download"></a>2.1: Pobierz plik instalacyjny agenta

1. Przejdź do **typowe ustawienia** karcie **Konfiguracja Monitora wydajności sieci** strony zasobu bazy danych. Kliknij agenta, który odnosi się do serwera procesor **Zainstaluj agentów programu Log Analytics** sekcji, a następnie Pobierz plik instalacyjny.
2. Następnie skopiuj **identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
3. Z **skonfigurować Log Analytics agentów monitorowania przy użyciu protokołu TCP** sekcji, Pobierz skrypt programu Powershell. Skrypt PowerShell ułatwia otworzyć port zapory dla transakcji TCP.

   ![Skrypt programu PowerShell](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: Zainstaluj agenta monitorowania na każdym serwerze monitorowania (w każdej sieci Wirtualnej, który chcesz monitorować)

Zaleca się zainstalowanie co najmniej dwóch agentów po obu stronach połączenia ExpressRoute w celu zapewnienia nadmiarowości (na przykład w środowisku lokalnym, sieciami wirtualnymi platformy Azure). Agent musi być zainstalowany w systemie Windows Server (2008 z dodatkiem SP1 lub nowszym). Monitorowanie obwodów usługi ExpressRoute przy użyciu systemu operacyjnego pulpitu Windows i systemu operacyjnego Linux nie jest obsługiwane. Aby zainstalować agentów, wykonaj następujące kroki:
   
  >[!NOTE]
  >Agenci wypchniętych przez SCOM (obejmuje [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) nie może być spójnie wykryć ich lokalizacji, jeśli są one hostowane na platformie Azure. Zaleca się, że nie używasz tych agentów w sieciach wirtualnych platformy Azure do monitorowania usługi ExpressRoute.
  >

1. Uruchom **Instalatora** Aby zainstalować agenta na każdym serwerze, który ma być używany do monitorowania usługi ExpressRoute. Serwer, który można używać na potrzeby monitorowania może być maszyny Wirtualnej lub w środowisku lokalnym, a musi mieć dostęp do Internetu. Należy zainstalować co najmniej jeden lokalny agent i agenta w każdym segmencie sieci, które mają być monitorowane w systemie Azure.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na **postanowienia licencyjne** strony, zapoznaj się z licencyjnymi, a następnie kliknij **zgadzam się**.
4. Na **Folder docelowy** strony, Zmień lub pozostaw domyślny folder instalacji, a następnie kliknij **dalej**.
5. Na **opcje instalacji agenta** strony, możesz nawiązać połączenia z agentem dzienniki usługi Azure Monitor lub Operations Manager. Lub puste opcje wyboru Jeśli chcesz później skonfigurować agenta. Po wprowadzeniu dokonane wybory, kliknij przycisk **dalej**.

   * Jeśli chcesz nawiązać połączenie z **usługi Azure Log Analytics**, Wklej **identyfikator obszaru roboczego** i **klucz obszaru roboczego** (klucz podstawowy) skopiowane do Notatnika w poprzedniej sekcji. Następnie kliknij przycisk **Dalej**.

     ![Identyfikator i klucz](./media/how-to-npm/8.png)
   * Jeśli chcesz nawiązać połączenie z **programu Operations Manager**na **Konfiguracja grupy zarządzania** wpisz **Nazwa grupy zarządzania**, **serwera zarządzania** i **Port serwera zarządzania**. Następnie kliknij przycisk **Dalej**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na **konto akcji agenta** albo wybierz **systemu lokalnego** konta lub **domeny lub lokalnego konta komputera**. Następnie kliknij przycisk **Dalej**.

     ![Konto](./media/how-to-npm/10.png)
6. Na **gotowy do instalacji** strony, przejrzyj wybrane opcje, a następnie kliknij przycisk **zainstalować**.
7. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.
8. Po zakończeniu programu Microsoft Monitoring Agent zostanie wyświetlona w Panelu sterowania. Możesz tam przejrzeć konfigurację i sprawdzić, czy agent jest połączony do dzienników usługi Azure Monitor. Po nawiązaniu połączenia agent wyświetla komunikat z informacją: **Program Microsoft Monitoring Agent pomyślnie połączył się z usługą Microsoft Operations Management Suite**.

9. Powtórz tę procedurę dla każdej sieci Wirtualnej, które muszą być monitorowane.

### <a name="proxy"></a>2.3: Skonfiguruj ustawienia serwera proxy (opcjonalnie)

Jeśli używasz serwera proxy sieci web dostęp do Internetu, umożliwia skonfigurowanie ustawień serwera proxy dla programu Microsoft Monitoring Agent następujące kroki. Wykonaj te kroki dla każdego serwera. Jeśli masz wiele serwerów, które trzeba skonfigurować, łatwiejszym rozwiązaniem może być użycie skryptu automatyzującego ten proces. Jeśli tak, zobacz [do skonfigurowania ustawień serwera proxy dla programu Microsoft Monitoring Agent za pomocą skryptu](../log-analytics/log-analytics-windows-agent.md).

Aby skonfigurować ustawienia serwera proxy dla programu Microsoft Monitoring Agent za pomocą Panelu sterowania:

1. Otwórz **Panel sterowania**.
2. Otwórz program **Microsoft Monitoring Agent**.
3. Kliknij kartę **Ustawienia serwera proxy**.
4. Wybierz **Użyj serwera proxy** i wpisz adres URL i numer portu, jeśli jest ono wymagane. Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uzyskać dostęp do serwera proxy.

   ![Serwer proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Sprawdź łączność agenta

Można łatwo sprawdzić, czy komunikują się agentów.

1. Na serwerze z agenta monitorowania, należy otworzyć **Panelu sterowania**.
2. Otwórz **Microsoft Monitoring Agent**.
3. Kliknij przycisk **usługi Azure Log Analytics** kartę.
4. W **stan** kolumny, powinien zostać wyświetlony pomyślnie połączono agenta do dzienników usługi Azure Monitor.

   ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Otwórz porty zapory na serwerach agenta monitorowania

Aby skorzystać z protokołu TCP, należy otworzyć porty zapory, aby upewnić się, że może komunikować się agentów monitorowania.

Można uruchomić skryptu programu PowerShell, aby utworzyć klucze rejestru, które są wymagane przez Monitor wydajności sieci. Ten skrypt tworzy także reguły zapory Windows, aby umożliwić monitorowanie agentów do utworzenia połączenia TCP ze sobą. Klucze rejestru, utworzone przez skrypt Określ, czy do rejestrowania, dzienniki debugowania oraz ścieżki dla plików dzienników. Definiuje również port TCP agenta, które są używane do komunikacji. Wartości te klucze są automatycznie ustawiane przy użyciu skryptu. Te klucze nie należy ręcznie zmienić.

Port 8084 jest domyślnie otwierany. Można użyć niestandardowych portów, podając parametr "numer_portu" do skryptu. Jednak możesz to zrobić, należy określić ten sam port dla wszystkich serwerów, na których uruchomiono skrypt.

>[!NOTE]
>Skrypt programu PowerShell "EnableRules" umożliwia skonfigurowanie reguły zapory Windows tylko na serwerze, na którym skrypt jest uruchamiany. Jeśli Zapora sieciowa, należy pamiętać, że umożliwia ruch przeznaczony dla portu TCP używany przez rozwiązania Network Performance Monitor.
>
>

Na serwerach programu agent Otwórz okno programu PowerShell z uprawnieniami administracyjnymi. Uruchom [EnableRules](https://aka.ms/npmpowershellscript) skrypt programu PowerShell (który został wcześniej pobrany). Nie używaj żadnych parametrów.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Krok 3. Konfigurowanie reguł sieciowych grup zabezpieczeń

Aby monitorować serwery agenta, które są na platformie Azure, należy skonfigurować reguły Sieciowej grupy zabezpieczeń sieci zezwalającą na ruch TCP na porcie używana przez narzędzie NPM dla transakcji syntetycznych. Domyślnym portem jest port 8084. Dzięki temu agent monitorowania zainstalowane na maszynie Wirtualnej platformy Azure, nawiązać połączenia z usługą lokalną agenta monitorowania.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [sieciowych grup zabezpieczeń](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Upewnij się, zainstalowano agentów (lokalnego agenta serwera i agenta programu server platformy Azure) i uruchomiono skrypt programu PowerShell przed kontynuowaniem ten krok.
>

## <a name="setupmonitor"></a>Krok 4. Odkryj połączenia komunikacji równorzędnej

1. Przejdź do kafelka przeglądu rozwiązania Network Performance Monitor, przechodząc do **wszystkie zasoby** stronie, a następnie kliknij pozycję na liście dozwolonych NPM w obszarze roboczym.

   ![obszar roboczy npm](./media/how-to-npm/npm.png)
2. Kliknij przycisk **rozwiązania Network Performance Monitor** kafelka przeglądu, aby wyświetlić pulpit nawigacyjny. Pulpit nawigacyjny zawiera na stronie usługi ExpressRoute, który pokazuje, że usługa ExpressRoute jest w "stanie nieskonfigurowanym". Kliknij przycisk **Konfiguracja funkcji** aby otworzyć stronę konfiguracji rozwiązania Network Performance Monitor.

   ![Konfiguracja funkcji](./media/how-to-npm/npm2.png)
3. Na stronie konfiguracji przejdź do karty "Komunikacje równorzędne usługi ExpressRoute", znajduje się w panelu po lewej stronie. Następnie kliknij przycisk **odnajdywanie teraz**.

   ![odnajdź](./media/how-to-npm/13.png)
4. Po zakończeniu odnajdowania, zostanie wyświetlona lista zawierająca następujące elementy:
   * Wszystkie połączenia komunikacji równorzędnej firmy Microsoft w obwody usługi ExpressRoute, które są skojarzone z tą subskrypcją.
   * Wszystkie połączenia prywatnej komunikacji równorzędnej, łączących się z sieciami wirtualnymi skojarzone z tą subskrypcją.
            
## <a name="configmonitor"></a>Krok 5. Konfigurowanie monitorów

W tej sekcji skonfigurujesz monitorów. Postępuj zgodnie z instrukcjami dla typu komunikacji równorzędnej, które chcesz monitorować: **prywatnej komunikacji równorzędnej**, lub **komunikacji równorzędnej firmy Microsoft**.

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

Dla prywatnej komunikacji równorzędnej, po zakończeniu odnajdowania, zostanie wyświetlony zostanie zasady unikatowych **nazwy obwodu** i **nazwa sieci wirtualnej**. Początkowo te zasady są wyłączone.

![rules](./media/how-to-npm/14.png)

1. Sprawdź **Monitoruj tę komunikację równorzędną** pola wyboru.
2. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**.
3. Wybierz warunki monitorowania. Można ustawić niestandardowe progi, aby generować zdarzenia dotyczące kondycji, wpisując wartości progowe. Zawsze, gdy wartość warunku przekroczy wartość progu wybranych dla pary wybranego sieć/podsieci, jest generowane zdarzenie kondycji.
4. Kliknij opcję AGENTÓW lokalne **dodawanie agentów** przycisk, aby dodać serwery lokalne, z których chcesz monitorować prywatne połączenie komunikacji równorzędnej. Upewnij się, wybrać tylko tych agentów, którzy mają łączność z punktem końcowym usługi firmy Microsoft, który określiłeś w sekcji Krok 2. Lokalnych agentach musi umożliwiać dotrzeć do punktu końcowego przy użyciu połączenia usługi ExpressRoute.
5. Zapisz ustawienia.
6. Po Włączanie reguł i wybierając wartości i agentów, które chcesz monitorować, jest Poczekaj około 30 – 60 minut, zanim wartości rozpocząć wypełnianie i **monitorowania usługi ExpressRoute** Kafelki staną się dostępne.

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

W przypadku komunikacji równorzędnej firmy Microsoft kliknij połączenia komunikacji równorzędnej firmy Microsoft, którą chcesz monitorować, a następnie skonfiguruj ustawienia.

1. Sprawdź **Monitoruj tę komunikację równorzędną** pola wyboru. 
2. (Opcjonalnie) Można zmienić docelowy punkt końcowy usługi firmy Microsoft. Domyślnie NPM wybiera punkt końcowy usługi firmy Microsoft jako element docelowy. NPM monitoruje łączność z serwerów lokalnych do tego punktu końcowego docelowego, za pośrednictwem usługi ExpressRoute. 
    * Aby zmienić ten punkt końcowy docelowej, kliknij przycisk **(Edytuj)** łącze w obszarze **docelowego:** , a następnie wybierz inny punkt końcowy docelowej usługi firmy Microsoft z listy adresów URL.
      ![edytowanie docelowego](./media/how-to-npm/edit_target.png)<br>

    * Można użyć niestandardowego adresu URL lub adres IP. Ta opcja jest szczególnie ważne w przypadku korzystania z komunikacji równorzędnej, aby nawiązać połączenie z usługami PaaS platformy Azure, takich jak Azure Storage, bazy danych SQL i witryn sieci Web, które są oferowane na publiczne adresy IP firmy Microsoft. Aby to zrobić, kliknij łącze **(Użyj niestandardowego adresu URL lub adres IP)** w dolnej części listy adresów URL, a następnie wprowadź publiczny punkt końcowy usługi PaaS platformy Azure, która jest połączona za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute.
    ![Niestandardowy adres URL](./media/how-to-npm/custom_url.png)<br>

    * Jeśli używasz tych opcjonalnych ustawień upewnij się, czy w tym miejscu wybrano tylko punkt końcowy usługi firmy Microsoft. Punkt końcowy musi być podłączony do usługi ExpressRoute i jest osiągalny przez agentów w środowisku lokalnym.
3. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**.
4. Wybierz warunki monitorowania. Można ustawić niestandardowe progi, aby generować zdarzenia dotyczące kondycji, wpisując wartości progowe. Zawsze, gdy wartość warunku przekroczy wartość progu wybranych dla pary wybranego sieć/podsieci, jest generowane zdarzenie kondycji.
5. Kliknij opcję AGENTÓW lokalne **dodawanie agentów** przycisk, aby dodać serwery lokalne, z których chcesz monitorować połączenie komunikacji równorzędnej firmy Microsoft. Upewnij się, wybrać tylko tych agentów, którzy mają połączenia z punktami końcowymi usługi firmy Microsoft, które określiłeś w sekcji Krok 2. Lokalnych agentach musi umożliwiać dotrzeć do punktu końcowego przy użyciu połączenia usługi ExpressRoute.
6. Zapisz ustawienia.
7. Po Włączanie reguł i wybierając wartości i agentów, które chcesz monitorować, jest Poczekaj około 30 – 60 minut, zanim wartości rozpocząć wypełnianie i **monitorowania usługi ExpressRoute** Kafelki staną się dostępne.

## <a name="explore"></a>Krok 6. Widok monitorowania kafelków

Po wyświetleniu monitorowania Kafelki do obwodów usługi ExpressRoute i połączeń zasobów są monitorowane przez Menedżera NPM. Możesz kliknąć Kafelek Peering firmy Microsoft, aby przejść do szczegółów w kondycję połączeń Peering firmy Microsoft.

![monitorowanie kafelków](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Strona monitora wydajności sieci

Strona NPM zawiera strony dla usługi ExpressRoute, który zawiera przegląd kondycji obwodów usługi ExpressRoute i połączeń komunikacji równorzędnej.

![Pulpit nawigacyjny](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Lista obwodów

Aby wyświetlić listę wszystkich monitorowane obwody usługi ExpressRoute, kliknij przycisk **obwodów usługi ExpressRoute** kafelka. Można wybrać obwodu i wyświetlić jego stan kondycji, wykresy trendów utraty pakietów, wykorzystania przepustowości i opóźnień. Wykresy są interaktywne. Możesz wybrać przedział czasu niestandardowych do kreślenia wykresów. Na wykresie, aby powiększyć obraz i wyświetlić punkty danych szczegółowych, można przeciągnąć wskaźnik myszy nad obszarem.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Trend utraty, opóźnienia i przepływności

Wykresy przepustowość, opóźnienia i straty są interaktywne. W dowolnej części te wykresy można powiększyć za pomocą myszy formantów. Przepustowość, opóźnienia i utratę danych można wyświetlić dla innych interwałów, klikając **daty/godziny**, który znajduje się poniżej przycisku akcje w lewym górnym rogu.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings"></a>Lista połączeń komunikacji równorzędnej

Aby wyświetlić listę wszystkich połączeń z sieciami wirtualnymi za pośrednictwem prywatnej komunikacji równorzędnej, kliknij przycisk **prywatne komunikacje równorzędne** Kafelek na pulpicie nawigacyjnym. W tym miejscu można wybrać wirtualnej połączenia sieciowego i wyświetlić jego stan kondycji, wykresy trendów utraty pakietów, wykorzystania przepustowości i opóźnień.

![Lista obwodu](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Wyświetlanie węzłów

Aby wyświetlić listę wszystkich łączy między węzłami w środowisku lokalnym i punktów końcowych usługi maszyny wirtualne/firmy Microsoft Azure dla wybranego połączenia komunikacji równorzędnej usługi ExpressRoute, kliknij przycisk **Wyświetl linki węzłów**. Można wyświetlić stan kondycji każdego łącza, a także trend strat i opóźnień skojarzonych z nimi.

![Wyświetlanie węzłów](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Topologia obwodu

Aby wyświetlić topologię obwodu, kliknij **topologii** kafelka. Spowoduje to przejście do widoku topologii wybranego obwód lub komunikacji równorzędnej. Diagram topologii zapewnia opóźnienia dla każdego segmentu w sieci. Każdy przeskok w warstwie 3 jest reprezentowany przez węzeł diagramu. Kliknięcie przeskoków, co spowoduje wyświetlenie więcej szczegółów na temat przeskoków.

Możesz zwiększyć poziom widoczności do uwzględnienia w środowisku lokalnym przeskoków, przenosząc poniżej paska suwaka **filtry**. Przenoszenie paska suwaka do lewej lub prawej strony, zwiększa/zmniejsza liczbę przeskoków na wykresie topologii. Opóźnienie w każdym segmencie jest widoczny, co umożliwia szybsze izolacji segmentów duże opóźnienie w sieci.

![filtry](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Szczegółowy widok topologii obwodu

Ten widok przedstawia połączenia sieci wirtualnej.
![szczegółowe topologii](./media/how-to-npm/17.png)
