---
title: Łączenie programu Operations Manager z monitorem platformy Azure | Dokumenty firmy Microsoft
description: Aby obsługiwać istniejącą inwestycję w programie System Center Operations Manager i korzystać z rozszerzonych możliwości usługi Log Analytics, można zintegrować program Operations Manager z obszarem roboczym.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274348"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Łączenie programu Operations Manager z monitorem platformy Azure

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby utrzymać istniejące inwestycje w [programie System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) i korzystać z rozszerzonych funkcji za pomocą usługi Azure Monitor, można zintegrować program Operations Manager z obszarem roboczym usługi Log Analytics. Dzięki temu można wykorzystać możliwości dzienników w usłudze Azure Monitor, kontynuując korzystanie z programu Operations Manager do:

* Monitorowanie kondycji usług IT za pomocą programu Operations Manager
* Utrzymywanie integracji z rozwiązaniami ITSM obsługującymi zarządzanie zdarzeniami i problemami
* Zarządzanie cyklem życia agentów wdrożonych lokalnie oraz na maszynach wirtualnych IaaS w chmurze publicznej monitorowanych za pomocą programu Operations Manager

Integracja z programem System Center Operations Manager zwiększa wartość strategii operacji usługi przy użyciu szybkości i wydajności usługi Azure Monitor w zbieraniu, przechowywaniu i analizowaniu danych dziennika z programu Operations Manager. Zapytania dziennika usługi Azure Monitor pomagają skorelować i pracować nad identyfikowaniem usterek problemów i napawaniem się cyklami obsługi istniejącego procesu zarządzania problemami. Elastyczność aparatu zapytań do badania wydajności, zdarzeń i danych alertów, z rozbudowanych pulpitów nawigacyjnych i możliwości raportowania, aby udostępnić te dane w znaczący sposób, pokazuje siłę Usługi Azure Monitor przynosi komplementowanie Operations Manager.

Agenci raportujący grupę zarządzania programu Operations Manager zbierają dane z serwerów na podstawie [źródeł danych usługi Log Analytics](agent-data-sources.md) i rozwiązań włączonych w obszarze roboczym. W zależności od włączonych rozwiązań ich dane są wysyłane bezpośrednio z serwera zarządzania programu Operations Manager do usługi lub ze względu na ilość danych zebranych w systemie zarządzanym przez agenta są wysyłane bezpośrednio z agenta do obszaru roboczego usługi Log Analytics. Serwer zarządzania przekazuje dane bezpośrednio do usługi. Nie są one nigdy zapisywane w magazynie danych ani w operacyjnej bazie danych. Gdy serwer zarządzania traci łączność z usługą Azure Monitor, buforuje dane lokalnie, dopóki komunikacja nie zostanie przywrócona. Jeśli serwer zarządzania jest w trybie offline z powodu planowanej konserwacji lub nieplanowanej awarii, inny serwer zarządzania w grupie zarządzania wznawia łączność z usługą Azure Monitor.  

Na poniższym diagramie przedstawiono połączenie między serwerami zarządzania i agentami w grupie zarządzania programu Operations Manager systemu System Center i monitorze platformy Azure, w tym kierunek i porty.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, serwery zarządzania można skonfigurować tak, aby łączyły się z bramą usługi Log Analytics w celu odbierania informacji o konfiguracji i wysyłania zebranych danych w zależności od rozwiązań Włączone. Aby uzyskać więcej informacji i kroków dotyczących konfigurowania grupy zarządzania programu Operations Manager do komunikowania się za pośrednictwem bramy usługi Log Analytics z usługą Azure Monitor, zobacz [Łączenie komputerów z usługą Azure Monitor przy użyciu bramy usługi Log Analytics](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem zapoznaj się z następującymi wymaganiami.

* Usługa Azure Monitor obsługuje tylko program System Center Operations Manager 2016 lub nowszy, program Operations Manager 2012 z dodatku SP1 UR6 lub nowszy oraz program Operations Manager 2012 R2 UR2 lub nowszy. Obsługa serwera proxy została dodana w programach Operations Manager 2012 SP1 UR7 i Operations Manager 2012 R2 UR3.
* Integracja programu System Center Operations Manager 2016 z chmurą instytucji rządowych USA wymaga zaktualizowanego pakietu administracyjnego doradcy dołączonego do pakietu zbiorczego aktualizacji 2 lub nowszego. Program System Center Operations Manager 2012 R2 wymaga zaktualizowanego pakietu administracyjnego doradcy dołączonego do pakietu zbiorczego aktualizacji 3 lub nowszego.
* Wszystkie agenty programu Operations Manager muszą spełniać minimalne wymagania dotyczące obsługi. Upewnij się, że agenci są przy minimalnej aktualizacji, w przeciwnym razie komunikacja agenta systemu Windows może zakończyć się niepowodzeniem i generować błędy w dzienniku zdarzeń programu Operations Manager.
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zapoznaj [się z omówieniem obszaru roboczego usługi Log Analytics](design-logs-deployment.md). 
* Uwierzytelniasz się na platformie Azure przy użyciu konta, które jest członkiem [roli współautora usługi Log Analytics](manage-access.md#manage-access-using-azure-permissions).

* Obsługiwane regiony — tylko następujące regiony platformy Azure są obsługiwane przez program System Center Operations Manager w celu nawiązania połączenia z obszarem roboczym usługi Log Analytics:
    - Zachodnio-środkowe stany USA
    - Australia Południowo-Wschodnia
    - Europa Zachodnia
    - Wschodnie stany USA
    - Azja Południowo-Wschodnia
    - Japonia Wschodnia
    - Południowe Zjednoczone Królestwo
    - Indie Środkowe
    - Kanada Środkowa
    - Zachodnie stany USA 2

>[!NOTE]
>Ostatnie zmiany w interfejsach API platformy Azure uniemożliwią klientom pomyślne skonfigurowanie integracji między ich grupą zarządzania a usługą Azure Monitor po raz pierwszy. W przypadku klientów, którzy już zintegrowali swoją grupę zarządzania z usługą, nie ma to wpływu, chyba że trzeba ponownie skonfigurować istniejące połączenie.  
>Nowy pakiet administracyjny został wydany dla następujących wersji programu Operations Manager:
> - W przypadku programu System Center Operations Manager 2019 ten pakiet administracyjny jest dołączony do nośnika źródłowego i instalowany podczas konfigurowania nowej grupy zarządzania lub podczas uaktualniania.
>- Pakiet administracyjny programu Operations Manager 1801 ma również zastosowanie do programu Operations Manager 1807.
>- W przypadku programu System Center Operations Manager 1801 pobierz pakiet administracyjny [z tego miejsca](https://www.microsoft.com/download/details.aspx?id=57173).
>- W przypadku programu System Center 2016 — Program Operations Manager pobierz pakiet administracyjny [z tego miejsca](https://www.microsoft.com/download/details.aspx?id=57172).  
>- W przypadku programu System Center Operations Manager 2012 R2 pobierz pakiet administracyjny [z tego miejsca](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Network (Sieć)

Poniższe informacje zawiera listę informacji o konfiguracji serwera proxy i zapory wymaganych dla agenta programu Operations Manager, serwerów zarządzania i konsoli Operacje do komunikowania się z usługą Azure Monitor. Ruch z każdego składnika jest wychodzący z sieci do usługi Azure Monitor.

|Zasób | Numer portu| Obejście inspekcji HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Tak|  
|\*.oms.opinsights.azure.com| 443|Tak|  
|\*.blob.core.windows.net| 443|Tak|  
|\*.azure-automation.net| 443|Tak|  
|**Serwer zarządzania**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Tak|  
|\*.ods.opinsights.azure.com| 443| Tak|  
|*.azure-automation.net | 443| Tak|  
|**Konsola programu Operations Manager do usługi Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 i 443||  
|\*.microsoft.com| 80 i 443||  
|\*.microsoftonline.com| 80 i 443||  
|\*.mms.microsoft.com| 80 i 443||  
|login.windows.net| 80 i 443||  
|portal.loganalytics.io| 80 i 443||
|api.loganalytics.io| 80 i 443||
|docs.loganalytics.io| 80 i 443||  

### <a name="tls-12-protocol"></a>Protokół TLS 1.2

Aby ubezpieczyć bezpieczeństwo danych przesyłanych do usługi Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta i grupy zarządzania do używania co najmniej usługi Transport Layer Security (TLS) 1.2. Starsze wersje TLS/Secure Sockets Layer (SSL) okazały się podatne na ataki i chociaż nadal działają, aby umożliwić zgodność z powrotem, nie są **zalecane.** Aby uzyskać dodatkowe informacje, zapoznaj [się z bezpiecznymi informacjami o wysyłaniu danych za pomocą protokołu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Łączenie programu Operations Manager z monitorem platformy Azure

Wykonaj następujące czynności, aby skonfigurować grupę zarządzania programu Operations Manager na potrzeby nawiązywania połączenia z jednym z obszarów roboczych usługi Log Analytics.

Podczas początkowej rejestracji grupy zarządzania programu Operations Manager w obszarze roboczym usługi Log Analytics opcja określania konfiguracji serwera proxy dla grupy zarządzania nie jest dostępna w konsoli Operacje.  Ta opcja staje się dostępna dopiero po pomyślnym zarejestrowaniu grupy zarządzania w usłudze.  Aby obejść ten problem, należy zaktualizować konfigurację serwera proxy systemu przy użyciu programu Netsh w systemie, z którego uruchomi się konsola Operacje, aby skonfigurować integrację, oraz wszystkie serwery zarządzania w grupie zarządzania.  

1. Otwórz wiersz polecenia z podwyższonymi uprawnieniami.
   a. Przejdź do **ekranu startowego** i wpisz **polecenie cmd**.
   b. Kliknij prawym przyciskiem myszy **wiersz polecenia** i wybierz polecenie Uruchom jako administrator**.
1. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po wykonaniu następujących kroków do integracji z usługą `netsh winhttp reset proxy` Azure Monitor, można usunąć konfigurację, uruchamiając, a następnie użyć opcji **Konfiguruj serwer proxy** w konsoli Operacje, aby określić serwer proxy lub serwer bramy usługi Log Analytics.

1. W konsoli programu Operations Manager wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Zarejestruj się w konsoli Operations Management Suite**.
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Uwierzytelnianie** wprowadź adres e-mail lub numer telefonu i hasło konta administratora, które jest skojarzone z subskrypcją pakietu OMS, a następnie kliknij przycisk **Zaloguj się**.

   >[!NOTE]
   >Nazwa pakietu Operations Management Suite została wycofana.

1. Po pomyślnym uwierzytelnieniu na stronie **Kreator dołączania pakietu Operations Management Suite: Wybierz obszar roboczy** zostanie wyświetlony monit o wybranie obszaru roboczego dzierżawy, subskrypcji i usługi Log Analytics platformy Azure. Jeśli masz więcej niż jeden obszar roboczy, wybierz z listy rozwijanej ten obszar roboczy, który ma zostać zarejestrowany w grupie zarządzania programu Operations Manager, a następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Program Operations Manager obsługuje jednocześnie tylko jeden obszar roboczy usługi Log Analytics. Połączenie i komputery, które zostały zarejestrowane w usłudze Azure Monitor z poprzedniego obszaru roboczego są usuwane z usługi Azure Monitor.
   >
   >
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Podsumowanie** sprawdź ustawienia i jeśli są one poprawne, kliknij przycisk **Utwórz**.
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Kończenie** kliknij przycisk **Zamknij**.

### <a name="add-agent-managed-computers"></a>Dodawanie komputerów zarządzanych przez agenta

Po skonfigurowaniu integracji z obszarem roboczym usługi Log Analytics ustanawia tylko połączenie z usługą, żadne dane nie są zbierane od agentów raportujących do grupy zarządzania. Stanie się to dopiero po skonfigurowaniu, które określone komputery zarządzane przez agenta zbierają dane dziennika dla usługi Azure Monitor. Obiekty komputerów można wybierać indywidualnie lub przez wybranie grupy zawierającej obiekty komputerów z systemem Windows. Nie można wybrać grupy zawierającej wystąpienia innej klasy, na przykład dysków logicznych lub baz danych SQL.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Dodaj komputer/grupę** pod nagłówkiem Akcje w prawej części okienka.
1. W oknie dialogowym **Wyszukiwanie komputerów** można przeprowadzić wyszukiwanie komputerów lub grup monitorowanych przez program Operations Manager. Wybierz komputery lub grupy, w tym serwer zarządzania programu Operations Manager, który ma być wbudowany w usługę Azure Monitor, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

Komputery i grupy skonfigurowane na potrzeby zbierania danych z węzła Komputery zarządzane można wyświetlić w sekcji Operations Management Suite w obszarze roboczym **Administracja** konsoli zarządzania operacjami. W tym miejscu możesz odpowiednio do potrzeb dodawać lub usuwać komputery i grupy.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurowanie ustawień serwera proxy w konsoli zarządzania operacjami

Wykonaj następujące kroki, jeśli wewnętrzny serwer proxy znajduje się między grupą zarządzania a usługą Azure Monitor. Te ustawienia są centralnie zarządzane z grupy zarządzania i dystrybuowane do systemów zarządzanych przez agenta, które są zawarte w zakresie do zbierania danych dziennika dla usługi Azure Monitor.  Jest to korzystne, gdy niektóre rozwiązania pomijają serwer zarządzania i wysyłają dane bezpośrednio do usługi.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite, a następnie kliknij pozycję **Połączenia**.
1. W widoku Połączenie OMS kliknij pozycję **Konfiguruj serwer proxy**.
1. Na stronie **Kreator konsoli Operations Management Suite: Serwer proxy** wybierz pozycję **Użyj serwera proxy, aby uzyskać dostęp do konsoli Operations Management Suite**, a następnie wpisz adres URL z numerem portu, na przykład http://corpproxy:80 i kliknij przycisk **Zakończ**.

Jeśli serwer proxy wymaga uwierzytelniania, wykonaj następujące kroki, aby skonfigurować poświadczenia i ustawienia, które muszą być propagowane do zarządzanych komputerów, które raportują do usługi Azure Monitor w grupie zarządzania.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. W obszarze **Konfiguracja Uruchom jako** wybierz pozycję **Profile**.
1. Otwórz profil **Serwer proxy profilu Uruchom jako usługi System Center Advisor**.
1. W kreatorze profilu Uruchom jako kliknij przycisk Dodaj, aby użyć konta Uruchom jako. Możesz utworzyć [konto Uruchom jako](https://technet.microsoft.com/library/hh321655.aspx) lub użyć istniejącego konta. Konto to musi mieć wystarczające uprawnienia do komunikacji za pośrednictwem serwera proxy.
1. Aby ustawić konto do zarządzania, wybierz pozycję **Wybrana klasa, grupa lub obiekt**, kliknij polecenie **Wybierz...**, a następnie kliknij pozycję **Grupuj...** w celu otwarcie okna **Wyszukiwanie grup**.
1. Wyszukaj **grupę serwerów monitorowania usługi Microsoft System Center Advisor**, a następnie ją wybierz. Po wybraniu grupy kliknij przycisk **OK**, aby zamknąć okno **Wyszukiwanie grup**.
1. Kliknij przycisk **OK**, aby zamknąć okno **Dodawanie konta Uruchom jako**.
1. Kliknij przycisk **Zapisz**, aby zakończyć pracę kreatora i zapisać zmiany.

Po utworzeniu połączenia i skonfigurowaniu agentów, którzy będą zbierać i raportować dane dziennika do usługi Azure Monitor, w grupie zarządzania jest stosowana następująca konfiguracja, niekoniecznie w kolejności:

* Tworzone jest konto Uruchom jako o nazwie **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Zostaje ono skojarzone z profilem Uruchom jako **Microsoft System Center Advisor Run As Profile Blob** i dotyczy dwóch klas — **Collection Server** i **Operations Manager Management Group**.
* Tworzone są dwa łączniki.  Pierwszy nosi nazwę **Microsoft.SystemCenter.Advisor.DataConnector** i jest automatycznie konfigurowany z subskrypcją, która przekazuje wszystkie alerty generowane z wystąpień wszystkich klas w grupie zarządzania do usługi Azure Monitor. Drugi łącznik to **Łącznik klasyfikatora,** który jest odpowiedzialny za komunikowanie się z usługą Azure Monitor i udostępnianie danych.
* Agenci i grupy wybrane do zbierania danych w grupie zarządzania są dodawane do **grupy serwera monitorowania usługi Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizacje pakietu administracyjnego

Po zakończeniu konfiguracji grupa zarządzania programu Operations Manager nawiązuje połączenie z usługą Azure Monitor. Serwer zarządzania synchronizuje się z usługą internetową i odbiera zaktualizowane informacje o konfiguracji w postaci pakietów administracyjnych dla włączonych rozwiązań zintegrowanych z programem Operations Manager. Program Operations Manager sprawdza dostępność aktualizacji tych pakietów administracyjnych i automatycznie pobiera je i importuje, gdy są dostępne. To zachowanie jest kontrolowane w szczególności przez dwie reguły:

* **Microsoft.SystemCenter.Advisor.MPUpdate** — aktualizuje podstawowe pakiety administracyjne usługi Azure Monitor. Ta reguła jest domyślnie uruchamiana co 12 godzin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** — aktualizuje pakiety administracyjne rozwiązań, które zostały włączone w obszarze roboczym. Ta reguła jest domyślnie uruchamiana co pięć (5) minut.

Można zastąpić te dwie reguły, aby zapobiec automatycznemu pobieraniu, wyłączając je, lub zmodyfikować częstotliwość synchronizacji serwera zarządzania z usługą Azure Monitor w celu ustalenia, czy nowy pakiet administracyjny jest dostępny i powinien zostać pobrany. Wykonaj instrukcje [How to Override a Rule or Monitor](https://technet.microsoft.com/library/hh212869.aspx) (Jak przesłonić regułę lub monitor), aby zmodyfikować parametr **Frequency** (Częstotliwość) wartością w sekundach w celu zmiany harmonogramu synchronizacji lub parametr **Enabled** (Włączone) w celu wyłączenia reguł. Przesłonięcie dotyczy wszystkich obiektów klasy Operations Manager Management Group.

Aby kontynuować śledzenie istniejącego procesu kontroli zmian w celu kontrolowania wersji pakietu administracyjnego w grupie zarządzania produkcją, można wyłączyć reguły i włączyć je w określonych godzinach, gdy aktualizacje są dozwolone. Jeśli w swoim środowisku masz grupę deweloperską lub grupę zarządzania kontroli jakości, która ma połączenie z Internetem, możesz skonfigurować tę grupę zarządzania za pomocą obszaru roboczego usługi Log Analytics do obsługi tego scenariusza. Dzięki temu można przejrzeć i ocenić iteracyjne wersje pakietów administracyjnych usługi Azure Monitor przed wydaniem ich do grupy zarządzania produkcją.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Przełączanie grupy programu Operations Manager do nowego obszaru roboczego usługi Log Analytics

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics** i utwórz obszar roboczy.  
1. Otwórz konsolę programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Analityka dzienników i wybierz pozycję **Połączenia**.
1. Wybierz link **Skonfiguruj ponownie konsolę Operation Management Suite** na środku panelu.
1. Postępuj zgodnie z **Kreatorem dołączania do usługi Log Analytics** i wprowadź adres e-mail lub numer telefonu oraz hasło konta administratora skojarzonego z nowym obszarem roboczym usługi Log Analytics.

   > [!NOTE]
   > Na stronie **Kreator dołączania konsoli Operations Management Suite: Wybieranie obszaru roboczego** przedstawiono istniejący używany obszar roboczy.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Sprawdzanie poprawności integracji programu Operations Manager z usługą Azure Monitor

Istnieje kilka różnych sposobów sprawdzenia, czy integracja usługi Azure Monitor z programem Operations Manager zakończy się pomyślnie.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Potwierdzanie integracji w witrynie Azure Portal

1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane.
1. Na liście obszarów roboczych usługi Log Analytics wybierz odpowiedni obszar roboczy.  
1. Wybierz kolejno pozycje **Ustawienia zaawansowane**, **Połączone źródła**, **System Center**.
1. W tabeli w sekcji System Center Operations Manager powinna zostać wyświetlona nazwa grupy zarządzania z liczbą agentów i stanem z godziny ostatniego odebrania danych.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Potwierdzanie integracji w konsoli zarządzania operacjami

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Wybierz pozycję **Pakiety administracyjne** i w polu tekstowym **Wyszukaj:** wpisz **Advisor** lub **Intelligence**.
1. W zależności od włączonych rozwiązań w wynikach wyszukiwania zobaczysz nazwę odpowiedniego pakietu administracyjnego.  Na przykład jeśli włączono rozwiązanie Alert Management, na liście zostanie wyświetlony pakiet administracyjny Microsoft System Center Advisor Alert Management.
1. Z widoku **Monitorowanie** przejdź do widoku **Operations Management Suite\Stan kondycji**.  W okienku **Stan serwera zarządzania** wybierz serwer zarządzania i w okienku **Widok szczegółów** potwierdź, że wartość właściwości **Identyfikator URI usługi uwierzytelniania** jest zgodny z identyfikatorem obszaru roboczego usługi Log Analytics.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Usuń integrację z usługą Azure Monitor

Jeśli integracja między grupą zarządzania programu Operations Manager i obszarem roboczym usługi Log Analytics nie jest już potrzebna, należy wykonać kilka czynności, aby prawidłowo usunąć połączenie i konfigurację grupy zarządzania. Poniższa procedura powoduje zaktualizowanie obszaru roboczego usługi Log Analytics przez usunięcie odwołania do grupy zarządzania, usunięcie łączników usługi Azure Monitor, a następnie usunięcie pakietów administracyjnych obsługujących integrację z usługą.  

Pakietów administracyjnych dla rozwiązań, które zostały włączone, które integrują się z programem Operations Manager i pakietów administracyjnych wymaganych do obsługi integracji z usługą Azure Monitor nie można łatwo usunąć z grupy zarządzania. Dzieje się tak, ponieważ niektóre pakiety administracyjne usługi Azure Monitor mają zależności od innych powiązanych pakietów administracyjnych. Aby usunąć pakiety administracyjne, które są zależne od innych pakietów administracyjnych, pobierz skrypt [usuwający pakiet administracyjny z zależnościami](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z Centrum skryptów w witrynie TechNet.  

1. Otwórz powłokę poleceń programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.

    > [!WARNING]
    > Przed przejściem do następnego kroku upewnij się, nie masz żadnych niestandardowych pakietów administracyjnych z ciągiem Advisor lub IntelligencePack w nazwie. W przeciwnym razie kolejne kroki spowodują ich usunięcie z grupy zarządzania.
    >

1. W wierszu polecenia powłoki wpisz `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Następnie wpisz `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Aby usunąć wszystkie pozostałe pakiety administracyjne, które są zależne od innych pakietów administracyjnych programu System Center Advisor, użyj skryptu *RecursiveRemove.ps1* pobranego wcześniej z Centrum skryptów w witrynie TechNet.  

    > [!NOTE]
    > Krok do usunięcia pakietów administracyjnych doradcy za pomocą programu PowerShell nie spowoduje automatycznego usunięcia wewnętrznych pakietów administracyjnych Programu Microsoft System Center Advisor lub Microsoft System Center Advisor.  Nie próbuj ich usuwać.  
    >  

1. Otwórz konsolę zarządzania operacjami programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
1. W obszarze **Administracja** wybierz węzeł **Pakiety administracyjne**, a następnie w polu tekstowym **Wyszukaj:** wpisz **Advisor** i upewnij się, że następujące pakiety administracyjne są nadal zaimportowane w Twojej grupie zarządzania:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. W witrynie Azure portal kliknij kafelek **Ustawienia.**
1. Wybierz **pozycję Połączone źródła**.
1. W tabeli w sekcji Program Operations Manager centrum systemu powinna zostać wyświetlona nazwa grupy zarządzania, którą chcesz usunąć z obszaru roboczego. W kolumnie **Ostatnie dane** kliknij link **Usuń**.  

    > [!NOTE]
    > Link **Usuń** nie będzie dostępny po 14 dniach, jeśli nie zostanie wykryta żadna aktywność w połączonej grupie zarządzania.  
    >

1. Zostanie wyświetlone okno z prośbą o potwierdzenie operacji usunięcia.  Kliknij pozycję **Tak**, aby kontynuować.

Aby usunąć dwa łączniki — jeden o nazwie Microsoft.SystemCenter.Advisor.DataConnector, a drugi o nazwie Advisor Connector, zapisz poniższy skrypt programu PowerShell na swoim komputerze i wykonaj go, korzystając z poniższych przykładów:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Z wyjątkiem serwera zarządzania komputer, na którym uruchomisz ten skrypt, powinien mieć zainstalowaną odpowiednią powłokę poleceń programu Operations Manager w zależności od wersji grupy zarządzania.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

W przyszłości, jeśli planujesz ponowne połączenie grupy zarządzania z obszarem roboczym `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` usługi Log Analytics, musisz ponownie zaimportować plik pakietu administracyjnego. W zależności od wersji programu System Center Operations Manager wdrożonego w środowisku ten plik można znaleźć w następującej lokalizacji:

* Na nośniku źródłowym w folderze `\ManagementPacks` dla programu System Center 2016 — Operations Manager lub nowszego.
* W najnowszym pakiecie zbiorczym aktualizacji zastosowanym w grupie zarządzania. W przypadku programu Operations Manager 2012 folder źródłowy jest `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` i dla `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`2012 R2 znajduje się w programie .

## <a name="next-steps"></a>Następne kroki

Aby dodać funkcje i zebrać dane, zobacz [Dodawanie rozwiązań usługi Azure Monitor z Galerii rozwiązań](../../azure-monitor/insights/solutions.md).
