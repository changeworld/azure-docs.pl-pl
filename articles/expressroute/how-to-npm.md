---
title: 'Azure ExpressRoute: Konfigurowanie npm dla obwodów'
description: Skonfiguruj monitorowanie sieci w chmurze (NPM) dla obwodów usługi Azure ExpressRoute. Obejmuje to monitorowanie za pomocą usługi ExpressRoute prywatnej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076588"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute

Ten artykuł ułatwia skonfigurowanie rozszerzenia Monitora wydajności sieci do monitorowania usługi ExpressRoute. Network Performance Monitor (NPM) to program do monitorowania sieci w chmurze, który monitoruje łączność między wdrożeniami w chmurze na platformie Azure a lokalizacjami lokalnymi (oddziałami firmy itp.). Program NPM jest częścią dzienników usługi Azure Monitor. Program NPM oferuje rozszerzenie dla usługi ExpressRoute, które pozwala monitorować wydajność sieci przez obwody usługi ExpressRoute skonfigurowane do korzystania z prywatnej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft. Po skonfigurowaniu programu NPM dla usługi ExpressRoute można wykrywać problemy z siecią, określać ich przyczyny i je eliminować. Ta usługa jest również dostępna dla platformy Azure Government Cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Możesz:

* Monitoruj utratę i opóźnienia w różnych sieciach wirtualnych i ustawiaj alerty

* Monitorowanie wszystkich ścieżek (w tym ścieżek nadmiarowych) w sieci

* Rozwiązywanie problemów z siecią przejściową i punktu w czasie, które są trudne do powtórzenia

* Pomoc w określeniu określonego segmentu w sieci, który jest odpowiedzialny za spadek wydajności

* Uzyskaj przepływność na sieć wirtualną (jeśli masz agentów zainstalowanych w każdej sieci wirtualnej)

* Wyświetlanie stanu systemu Usługi ExpressRoute z poprzedniego punktu w czasie

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Agenci monitorowania są instalowani na wielu serwerach, zarówno lokalnie, jak i na platformie Azure. Agenci komunikują się ze sobą, ale nie wysyłają danych, wysyłają pakiety uzgadniania TCP. Komunikacja między agentami umożliwia platformie Azure mapowanie topologii sieci i ścieżki ruchu może podjąć.

1. Tworzenie obszaru roboczego npm. Jest to taka sama jak obszar roboczy usługi Log Analytics.
2. Instalowanie i konfigurowanie agentów oprogramowania. (Jeśli chcesz monitorować tylko za pomocą funkcji Komunikacji równorzędnej firmy Microsoft, nie trzeba instalować i konfigurować agentów oprogramowania.): 
    * Zainstaluj agentów monitorowania na serwerach lokalnych i maszynach wirtualnych platformy Azure (dla prywatnej komunikacji równorzędnej).
    * Skonfiguruj ustawienia na serwerach agenta monitorowania, aby umożliwić agentom monitorowania komunikowanie się. (Otwórz porty zapory itp.)
3. Skonfiguruj reguły sieciowej grupy zabezpieczeń (NSG), aby agent monitorowania zainstalowany na maszynach wirtualnych platformy Azure komunikował się z lokalnymi agentami monitorowania.
4. Konfigurowanie monitorowania: Automatyczne wykrywanie sieci i zarządzanie nimi w sieciach NPM.

Jeśli monitor wydajności sieci jest już używany do monitorowania innych obiektów lub usług, a masz już obszar roboczy w jednym z obsługiwanych regionów, możesz pominąć krok 1 i krok 2 i rozpocząć konfigurację w kroku 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Krok 1: Tworzenie obszaru roboczego

Utwórz obszar roboczy w subskrypcji, w ramach których łącze sieci wirtualnych ma łącze do obwodów usługi ExpressRoute.

1. W [witrynie Azure portal](https://portal.azure.com)wybierz subskrypcję, która ma wirtualne maszyny wirtualne równorzędne z obwodem usługi ExpressRoute. Następnie wyszukaj listę usług w **portalu Marketplace** pod kątem "Monitor wydajności sieci". W zamian kliknij, aby otworzyć stronę **Monitor wydajności sieci.**

   >[!NOTE]
   >Można utworzyć nowy obszar roboczy lub użyć istniejącego obszaru roboczego. Jeśli chcesz użyć istniejącego obszaru roboczego, upewnij się, że obszar roboczy został zmigrowany do nowego języka kwerendy. [Więcej informacji...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. U dołu głównej strony **Monitor wydajności sieci** kliknij pozycję **Utwórz,** aby otworzyć monitor wydajności sieci — tworzenie nowej strony **rozwiązania.** Kliknij **pozycję Obszar roboczy analizy dzienników — wybierz obszar roboczy,** aby otworzyć stronę Obszary robocze. Kliknij **pozycję + Utwórz nowy obszar roboczy,** aby otworzyć stronę Obszar roboczy.
3. Na stronie **Obszaru roboczego usługi Log Analytics** wybierz pozycję **Utwórz nowy**, a następnie skonfiguruj następujące ustawienia:

   * Obszar roboczy analizy dzienników — wpisz nazwę obszaru roboczego.
   * Subskrypcja — jeśli masz wiele subskrypcji, wybierz tę, którą chcesz skojarzyć z nowym obszarem roboczym.
   * Grupa zasobów — utwórz grupę zasobów lub użyj istniejącej.
   * Lokalizacja — ta lokalizacja służy do określania lokalizacji konta magazynu używanego dla dzienników połączeń agenta.
   * Warstwa cenowa — wybierz warstwę cenową.
  
     >[!NOTE]
     >Obwód usługi ExpressRoute może znajdować się w dowolnym miejscu na świecie. Nie musi znajdować się w tym samym regionie co obszar roboczy.
     >
  
     ![obszar roboczy](./media/how-to-npm/4.png)<br><br>
4. Kliknij **przycisk OK,** aby zapisać i wdrożyć szablon ustawień. Po sprawdzeniu poprawności szablonu kliknij przycisk **Utwórz,** aby wdrożyć obszar roboczy.
5. Po wdrożeniu obszaru roboczego przejdź do utworzonego zasobu **NetworkMonitoring(name).** Sprawdź poprawność ustawień, a następnie kliknij przycisk **Rozwiązanie wymaga dodatkowej konfiguracji**.

   ![dodatkowa konfiguracja](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Krok 2: Instalowanie i konfigurowanie agentów

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Pobierz plik instalacyjny agenta

1. Przejdź do karty **Ustawienia wspólne** na stronie **Konfiguracja monitora wydajności sieci** dla zasobu. Kliknij agenta odpowiadającego procesorowi serwera z sekcji **Zainstaluj agentów analizy dzienników** i pobierz plik instalacyjny.
2. Następnie skopiuj **identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
3. Z sekcji **Konfigurowanie agentów analizy dzienników do monitorowania przy użyciu protokołu TCP** pobierz skrypt programu Powershell. Skrypt programu PowerShell ułatwia otwieranie odpowiedniego portu zapory dla transakcji TCP.

   ![Skrypt programu PowerShell](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Zainstaluj agenta monitorowania na każdym serwerze monitorowania (na każdej sieci wirtualnej, którą chcesz monitorować)

Zaleca się zainstalowanie co najmniej dwóch agentów po każdej stronie połączenia usługi ExpressRoute w celu zapewnienia nadmiarowości (na przykład lokalnie, usługi Azure VNETs). Agent musi być zainstalowany w systemie Windows Server (2008 z dodatku SP1 lub nowszym). Monitorowanie obwodów usługi ExpressRoute przy użyciu systemu operacyjnego Windows Desktop i systemu operacyjnego Linux nie jest obsługiwane. Aby zainstalować agentów, należy wykonać następujące czynności:
   
  >[!NOTE]
  >Agenci wypychane przez SCOM (w tym [MMA)](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)mogą nie być w stanie konsekwentnie wykryć ich lokalizacji, jeśli są hostowane na platformie Azure. Zaleca się, aby nie używać tych agentów w sieciach wirtualnych platformy Azure do monitorowania usługi ExpressRoute.
  >

1. Uruchom **Instalatora,** aby zainstalować agenta na każdym serwerze, którego chcesz użyć do monitorowania usługi ExpressRoute. Serwer używany do monitorowania może być maszyną wirtualną lub lokalną i musi mieć dostęp do Internetu. Musisz zainstalować co najmniej jednego agenta lokalnie i jednego agenta w każdym segmencie sieci, który chcesz monitorować na platformie Azure.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na stronie **Postanowienia licencyjne** przeczytaj licencję, a następnie kliknij przycisk **Zgadzam się**.
4. Na stronie **Folder docelowy** zmień lub zachowaj domyślny folder instalacyjny, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Opcje instalacji agenta** można połączyć agenta z dziennikami usługi Azure Monitor lub programem Operations Manager. Możesz też pozostawić opcje puste, jeśli chcesz skonfigurować agenta później. Po dokonaniu wyboru kliknij przycisk **Dalej**.

   * Jeśli zdecydujesz się połączyć z **usługą Azure Log Analytics,** wklej **identyfikator obszaru roboczego** i **klucz obszaru roboczego** (klucz podstawowy), które zostały skopiowane do Notatnika w poprzedniej sekcji. Następnie kliknij przycisk **Dalej**.

     ![Identyfikator i klucz](./media/how-to-npm/8.png)
   * Jeśli wybrano połączenie z **programem Operations Manager,** na stronie **Konfiguracja grupy zarządzania** wpisz **nazwę grupy zarządzania,** **serwer zarządzania**i **port serwera zarządzania**. Następnie kliknij przycisk **Dalej**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na stronie **Konto akcji agenta** wybierz konto **System lokalny** lub **Domenę lub Konto komputera lokalnego**. Następnie kliknij przycisk **Dalej**.

     ![Konto](./media/how-to-npm/10.png)
6. Na stronie **Gotowe do zainstalowania** przejrzyj swoje wybory, a następnie kliknij pozycję **Zainstaluj**.
7. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.
8. Po zakończeniu agent monitorowania firmy Microsoft pojawi się w Panelu sterowania. Można tam przejrzeć konfigurację i sprawdzić, czy agent jest połączony z dziennikami usługi Azure Monitor. Po podłączeniu agent wyświetla komunikat: **Agent monitorowania firmy Microsoft pomyślnie połączył się z usługą Microsoft Operations Management Suite**.

9. Powtórz tę procedurę dla każdej sieci wirtualnej, która musi być monitorowana.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Konfigurowanie ustawień serwera proxy (opcjonalnie)

Jeśli do uzyskania dostępu do Internetu jest używany internetowy serwer proxy, należy wykonać następujące kroki, aby skonfigurować ustawienia serwera proxy dla programu Microsoft Monitoring Agent. Wykonaj te kroki dla każdego serwera. Jeśli masz wiele serwerów, które trzeba skonfigurować, łatwiejszym rozwiązaniem może być użycie skryptu automatyzującego ten proces. Jeśli tak, zobacz [Aby skonfigurować ustawienia serwera proxy dla programu Microsoft Monitoring Agent przy użyciu skryptu](../log-analytics/log-analytics-windows-agent.md).

Aby skonfigurować ustawienia serwera proxy dla programu Microsoft Monitoring Agent przy użyciu Panelu sterowania:

1. Otwórz **Panel sterowania**.
2. Otwórz program **Microsoft Monitoring Agent**.
3. Kliknij kartę **Ustawienia serwera proxy**.
4. Wybierz **pozycję Użyj serwera proxy** i wpisz adres URL i numer portu, jeśli jest potrzebny. Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uzyskać dostęp do serwera proxy.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Weryfikacja łączności agenta

Możesz łatwo sprawdzić, czy twoi agenci komunikują się.

1. Na serwerze z agentem monitorującym otwórz **Panel sterowania**.
2. Otwórz program **Microsoft Monitoring Agent**.
3. Kliknij kartę **Usługi Azure Log Analytics.**
4. W kolumnie **Stan** powinien zostać wyświetlony, że agent pomyślnie połączony z dziennikami usługi Azure Monitor.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Otwórz porty zapory na serwerach agentów monitorowania

Aby użyć protokołu TCP, należy otworzyć porty zapory, aby upewnić się, że agenci monitorowania mogą się komunikować.

Można uruchomić skrypt programu PowerShell, aby utworzyć klucze rejestru wymagane przez Monitor wydajności sieci. Ten skrypt tworzy również reguły Zapory systemu Windows, aby umożliwić agentom monitorowania tworzenie połączeń TCP ze sobą. Klucze rejestru utworzone przez skrypt określają, czy dzienniki debugowania mają być rejestrowane, oraz ścieżkę dla pliku dzienników. Definiuje również port TCP agenta używany do komunikacji. Wartości dla tych kluczy są automatycznie ustawiane przez skrypt. Nie należy ręcznie zmieniać tych klawiszy.

Port 8084 jest domyślnie otwarty. Można użyć portu niestandardowego, podając parametr "portNumber" do skryptu. Jednak w takim przypadku należy określić ten sam port dla wszystkich serwerów, na których jest uruchamiany skrypt.

>[!NOTE]
>Skrypt programu PowerShell "EnableRules" konfiguruje reguły Zapory systemu Windows tylko na serwerze, na którym skrypt jest uruchamiany. Jeśli masz zaporę sieciową, upewnij się, że zezwala ona na ruch przeznaczony dla portu TCP używanego przez Monitor wydajności sieci.
>
>

Na serwerach agenta otwórz okno programu PowerShell z uprawnieniami administracyjnymi. Uruchom skrypt [EnableRules](https://aka.ms/npmpowershellscript) PowerShell (który został pobrany wcześniej). Nie używaj żadnych parametrów.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Krok 3: Konfigurowanie reguł sieciowej grupy zabezpieczeń

Aby monitorować serwery agentów, które znajdują się na platformie Azure, należy skonfigurować reguły sieciowej grupy zabezpieczeń (NSG), aby zezwolić na ruch TCP na porcie używanym przez npm dla transakcji syntetycznych. Domyślny port to 8084. Dzięki temu agent monitorowania zainstalowany na maszynie Wirtualnej platformy Azure do komunikowania się z lokalnym agentem monitorowania.

Aby uzyskać więcej informacji na temat sieciowej grupy zabezpieczeń, zobacz [Sieciowe grupy zabezpieczeń](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Upewnij się, że zainstalowano agentów (zarówno lokalnego agenta serwera, jak i agenta serwera platformy Azure) i przed przystąpieniem do tego kroku uruchomiliśmy skrypt programu PowerShell.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Krok 4: Odkryj połączenia komunikacji równorzędnej

1. Przejdź do kafelka przeglądu Monitor wydajności sieci, przechodząc do strony **Wszystkie zasoby,** a następnie kliknij obszar roboczy NPM umieszczony na białej liście.

   ![npm obszar roboczy](./media/how-to-npm/npm.png)
2. Kliknij kafelek **przeglądowy Monitor wydajności sieci,** aby przywołać pulpit nawigacyjny. Pulpit nawigacyjny zawiera stronę usługi ExpressRoute, która pokazuje, że usługa ExpressRoute jest w stanie "nieskonfigurowanym". Kliknij **pozycję Ustawienia funkcji,** aby otworzyć stronę konfiguracji Monitor wydajności sieci.

   ![konfiguracja funkcji](./media/how-to-npm/npm2.png)
3. Na stronie konfiguracji przejdź do karty "Peerings" usługi ExpressRoute, znajdującej się na panelu po lewej stronie. Następnie kliknij przycisk **Odkryj teraz**.

   ![Odkryj](./media/how-to-npm/13.png)
4. Po zakończeniu odnajdywania zostanie wyświetlona lista zawierająca następujące elementy:
   * Wszystkie połączenia komunikacji równorzędnej firmy Microsoft w obwodach usługi ExpressRoute, które są skojarzone z tą subskrypcją.
   * Wszystkie prywatne połączenia komunikacji równorzędnej, które łączą się z sieciami wirtualnymi skojarzonymi z tą subskrypcją.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Krok 5: Konfigurowanie monitorów

W tej sekcji można skonfigurować monitory. Wykonaj kroki dotyczące typu komunikacji równorzędnej, którą chcesz monitorować: **prywatna komunikacja równorzędna**lub **komunikacja równorzędna firmy Microsoft**.

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

W przypadku prywatnej komunikacji równorzędnej po zakończeniu odnajdywania zostaną wyświetlane reguły **unikatowej nazwy obwodu** i **nazwy sieci wirtualnej**. Początkowo te reguły są wyłączone.

![rules](./media/how-to-npm/14.png)

1. Zaznacz pole wyboru **Monitoruj tę komunikację równorzędnej.**
2. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**.
3. Wybierz warunki monitorowania. Można ustawić niestandardowe progi do generowania zdarzeń kondycji, wpisując wartości progowe. Za każdym razem, gdy wartość warunku przekracza wybrany próg dla wybranej pary sieci/podsieci, generowane jest zdarzenie kondycji.
4. Kliknij przycisk **Dodawanie agentów** ON-PREM, aby dodać serwery lokalne, z których chcesz monitorować prywatne połączenie komunikacji równorzędnej. Upewnij się, że wybierasz tylko agentów, którzy mają łączność z punktem końcowym usługi firmy Microsoft określonym w sekcji dla kroku 2. Agenci lokalni muszą mieć możliwość osiągnięcia punktu końcowego przy użyciu połączenia usługi ExpressRoute.
5. Zapisz ustawienia.
6. Po włączeniu reguł i wybraniu wartości i agentów, które chcesz monitorować, należy poczekać około 30-60 minut, aby wartości zaczęły wypełniać i kafelki **monitorowania usługi ExpressRoute,** aby stały się dostępne.

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

W przypadku komunikacji równorzędnej firmy Microsoft kliknij połączenia komunikacji równorzędnej firmy Microsoft, które chcesz monitorować, i skonfiguruj ustawienia.

1. Zaznacz pole wyboru **Monitoruj tę komunikację równorzędnej.** 
2. (Opcjonalnie) Można zmienić docelowy punkt końcowy usługi firmy Microsoft. Domyślnie npm wybiera punkt końcowy usługi firmy Microsoft jako miejsce docelowe. Serwer NPM monitoruje łączność z serwerów lokalnych z tym docelowym punktem końcowym za pośrednictwem usługi ExpressRoute. 
    * Aby zmienić ten docelowy punkt końcowy, kliknij łącze **(edytuj)** w obszarze **Miejsce docelowe:** i wybierz inny docelowy punkt końcowy usługi firmy Microsoft z listy adresów URL.
      ![edytuj cel](./media/how-to-npm/edit_target.png)<br>

    * Możesz użyć niestandardowego adresu URL lub adresu IP. Ta opcja jest szczególnie istotna, jeśli używasz komunikacji równorzędnej firmy Microsoft do nawiązania połączenia z usługami PaaS platformy Azure, takimi jak usługa Azure Storage, bazy danych SQL i witryny sieci Web oferowane na publicznych adresach IP. Aby to zrobić, kliknij **łącze (Użyj niestandardowego adresu URL lub adresu IP)** u dołu listy adresów URL, a następnie wprowadź publiczny punkt końcowy usługi PaaS platformy Azure, która jest połączona za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute.
    ![niestandardowy adres URL](./media/how-to-npm/custom_url.png)<br>

    * Jeśli używasz tych ustawień opcjonalnych, upewnij się, że w tym miejscu jest zaznaczony tylko punkt końcowy usługi firmy Microsoft. Punkt końcowy musi być połączony z usługą ExpressRoute i osiągalny przez agentów lokalnych.
3. Zaznacz pole wyboru **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**.
4. Wybierz warunki monitorowania. Można ustawić niestandardowe progi do generowania zdarzeń kondycji, wpisując wartości progowe. Za każdym razem, gdy wartość warunku przekracza wybrany próg dla wybranej pary sieci/podsieci, generowane jest zdarzenie kondycji.
5. Kliknij przycisk **Dodawanie agentów** ON-PREM, aby dodać serwery lokalne, z których chcesz monitorować połączenie komunikacji równorzędnej firmy Microsoft. Upewnij się, że wybierasz tylko agentów, którzy mają łączność z punktami końcowymi usługi firmy Microsoft, które zostały określone w sekcji dla kroku 2. Agenci lokalni muszą mieć możliwość osiągnięcia punktu końcowego przy użyciu połączenia usługi ExpressRoute.
6. Zapisz ustawienia.
7. Po włączeniu reguł i wybraniu wartości i agentów, które chcesz monitorować, należy poczekać około 30-60 minut, aby wartości zaczęły wypełniać i kafelki **monitorowania usługi ExpressRoute,** aby stały się dostępne.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Krok 6: Wyświetlanie kafelków monitorowania

Po wyświetleniu kafelków monitorowania obwody usługi ExpressRoute i zasoby połączeń są monitorowane przez serwer NPM. Możesz kliknąć kafelek Komunikacji równorzędnej firmy Microsoft, aby przejść do szczegółów kondycji połączeń równorzędnych firmy Microsoft.

![monitorowanie płytek](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Strona Monitor wydajności sieci

Strona NPM zawiera stronę usługi ExpressRoute, która zawiera przegląd kondycji obwodów i komunikacji równorzędnej usługi ExpressRoute.

![Pulpit nawigacyjny](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lista obwodów

Aby wyświetlić listę wszystkich monitorowanych obwodów usługi ExpressRoute, kliknij kafelek **obwodów usługi ExpressRoute.** Można wybrać obwód i wyświetlić jego stan kondycji, wykresy trendów dla utraty pakietów, wykorzystania przepustowości i opóźnienia. Wykresy są interaktywne. Można wybrać niestandardowe okno czasu do drukowania wykresów. Możesz przeciągnąć wskaźnik myszy na obszar wykresu, aby powiększyć i wyświetlić szczegółowe punkty danych.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Tendencja strat, opóźnień i przepływności

Wykresy przepustowości, opóźnienia i strat są interaktywne. Za pomocą kontrolek myszy można powiększać dowolną sekcję tych wykresów. Możesz również wyświetlić dane o przepustowości, opóźnieniach i stratach dla innych interwałów, klikając **pozycję Data/godzina**, znajdującą się poniżej przycisku Akcje w lewym górnym rogu.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Lista komunikacji równorzędnej

Aby wyświetlić listę wszystkich połączeń z sieciami wirtualnymi za pośrednictwem prywatnej komunikacji równorzędnej, kliknij kafelek **Private Peerings** na pulpicie nawigacyjnym. W tym miejscu można wybrać połączenie sieci wirtualnej i wyświetlić jego stan kondycji, wykresy trendów dotyczące utraty pakietów, wykorzystania przepustowości i opóźnienia.

![lista obwodów](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Widok węzłów

Aby wyświetlić listę wszystkich łączy między węzłami lokalnymi a punktami końcowymi usługi Azure VM/Microsoft dla wybranego połączenia równorzędnego usługi ExpressRoute, kliknij pozycję **Wyświetl łącza węzłów**. Można wyświetlić stan kondycji każdego łącza, a także trend utraty i opóźnienia skojarzone z nimi.

![widok węzłów](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Topologia obwodów

Aby wyświetlić topologię obwodów, kliknij kafelek **Topologia.** Spowoduje to przejście do widoku topologii wybranego obwodu lub komunikacji równorzędnej. Diagram topologii zawiera opóźnienie dla każdego segmentu w sieci. Każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Kliknięcie na hop ujawnia więcej szczegółów na temat hopu.

Można zwiększyć poziom widoczności, aby uwzględnić przeskoki lokalne, przesuwając pasek suwaka poniżej **filtrów**. Przesuwając pasek suwaka w lewo lub w prawo, zwiększa/zmniejsza liczbę przeskoków na wykresie topologii. Opóźnienie w każdym segmencie jest widoczne, co pozwala na szybszą izolację segmentów o dużym opóźnieniu w sieci.

![filtry](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Szczegółowy widok topologii obwodu

W tym widoku są wyświetlane połączenia sieci wirtualnej.
![szczegółowa topologia](./media/how-to-npm/17.png)
