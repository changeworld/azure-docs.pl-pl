---
title: Łączenie programu Operations Manager do usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Aby obsługiwać istniejącą inwestycję w programie System Center Operations Manager i korzystać z rozszerzonych możliwości usługi Log Analytics, można zintegrować program Operations Manager z obszarem roboczym.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 336a9d9c76114920e92de2000152e500f7dce46f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445316"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Łączenie programu Operations Manager do usługi Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby zachować istniejącą inwestycję w [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) i rozszerzone możliwości za pomocą usługi Azure Monitor, Operations Manager można zintegrować z obszaru roboczego usługi Log Analytics. Dzięki temu możesz korzystać z możliwości dzienników w usłudze Azure Monitor podczas dalszego korzystania z programu Operations Manager:

* Monitorowanie kondycji usług IT za pomocą programu Operations Manager
* Utrzymywanie integracji z rozwiązaniami ITSM obsługującymi zarządzanie zdarzeniami i problemami
* Zarządzanie cyklem życia agentów wdrożonych lokalnie oraz na maszynach wirtualnych IaaS w chmurze publicznej monitorowanych za pomocą programu Operations Manager

Integracja z usługą System Center Operations Manager dodaje wartość do swoją strategię działania usługi, korzystając z szybkości i wydajności usługi Azure Monitor w zbieranie, przechowywanie i analizowanie danych dzienników z programu Operations Manager. Azure Monitor dziennika zapytań pomocy korelowanie i pracy w kierunku identyfikowanie błędów, problemów i udostępniając cykli w odniesieniu do istniejących proces zarządzania problem. Swobodne korzystanie z aparatu zapytań do sprawdzenia wydajności, zdarzeń i alertów danych przy użyciu rozbudowanych pulpitów nawigacyjnych i możliwości raportowania w celu ujawnienia tych danych w przejrzysty sposób przedstawia siłę usługi Azure Monitor udostępnia w complimenting programu Operations Manager.

Agentów raportujących do grupy zarządzania programu Operations Manager zbieranie danych z serwerów na podstawie [źródeł danych usługi Log Analytics](agent-data-sources.md) i rozwiązania zostały włączone w obszarze roboczym. W zależności od rozwiązania włączone, dane są albo przesyłane bezpośrednio z serwera zarządzania programu Operations Manager z usługą lub ze względu na ilość danych zebranych w systemie zarządzane z wykorzystaniem agentów są wysyłane bezpośrednio z agenta z obszarem roboczym usługi Log Analytics. Serwer zarządzania przekazuje dane bezpośrednio do usługi. Nie są one nigdy zapisywane w magazynie danych ani w operacyjnej bazie danych. Gdy serwer zarządzania utraci łączność z usługą Azure Monitor, jego buforuje dane lokalnie do momentu ponownego nawiązywać połączenia. Jeśli serwer zarządzania jest niedostępny z powodu planowanej konserwacji lub nieplanowanych awarii, inny serwer zarządzania w grupie zarządzania wznawia łączność z usługą Azure Monitor.  

Na poniższym diagramie przedstawiono połączenia między agentami a serwerami zarządzania w grupie zarządzania programu System Center Operations Manager i usługi Azure Monitor, w tym kierunku i portów.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Jeśli Twoje informatyczne zasady zabezpieczeń nie zezwalają na komputerach w sieci, aby nawiązać połączenie z Internetem, serwery zarządzania można skonfigurować do połączenia z bramą usługi Log Analytics w celu odbierania informacji o konfiguracji i wysyłania danych zebranych w zależności od rozwiązania włączone. Aby uzyskać więcej informacji oraz instrukcje dotyczące sposobu konfigurowania grupy zarządzania programu Operations Manager do komunikowania się za pośrednictwem bramy usługi Log Analytics do monitorowania platformy Azure, zobacz [łączenie komputerów z usługą Azure Monitor, przy użyciu bramy usługi Log Analytics](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem sprawdź następujące wymagania.

* Usługa Azure Monitor obsługuje wyłącznie System Center Operations Manager 2016 lub nowszego, programu Operations Manager 2012 SP1 UR6 lub nowszej, a programu Operations Manager 2012 R2 UR2 lub nowszej. Obsługa serwera proxy została dodana w programach Operations Manager 2012 SP1 UR7 i Operations Manager 2012 R2 UR3.
* Integracja programu System Center Operations Manager 2016 z chmury dla instytucji rządowych USA wymaga zaktualizowany pakiet zarządzania klasyfikatora się z pakietem zbiorczym aktualizacji 2 lub nowszym. System Center Operations Manager 2012 R2 wymaga zaktualizowany pakiet zarządzania klasyfikatora się z pakietem zbiorczym aktualizacji 3 lub nowszym.
* Wszystkie agenty programu Operations Manager muszą spełniać minimalne wymagania dotyczące obsługi. Upewnij się, że agenci są przy minimalnej aktualizacji, w przeciwnym razie komunikacji agenta Windows może zakończyć się niepowodzeniem i generować błędy w dzienniku zdarzeń programu Operations Manager.
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, przejrzyj [Omówienie obszaru roboczego usługi Log Analytics](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).   
* Uwierzytelnianie na platformie Azure przy użyciu konta będącego członkiem [roli Współautor usługi Log Analytics](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).

* Obsługiwane regiony — tylko poniższych regionów platformy Azure są obsługiwane przez System Center Operations Manager, aby nawiązać połączenie z obszarem roboczym usługi Log Analytics:
    - Środkowo-zachodnie stany USA
    - Australia Południowo-Wschodnia
    - Europa Zachodnia
    - East US
    - Azja Południowo-Wschodnia
    - Japonia Wschodnia
    - Południowe Zjednoczone Królestwo
    - Indie Środkowe
    - Kanada Środkowa
    - Zachodnie stany USA 2

>[!NOTE]
>Ostatnie zmiany do interfejsów API usługi Azure uniemożliwi klientom możliwość pomyślnie konfigurowania integracji między ich grupy zarządzania i monitorowania platformy Azure po raz pierwszy. W przypadku klientów, którzy zintegrowano już program ich grupy zarządzania w usłudze nie są zagrożone o ile nie trzeba ponownie skonfigurować istniejące połączenie.  
>Nowy pakiet administracyjny został zwolniony do następujących wersji programu Operations Manager:
>  
>* Aby System Center Operations Manager 1801 Pobierz pakiet administracyjny z [tutaj](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Dla programu System Center 2016 — Operations Manager, należy pobrać pakiet administracyjny z [tutaj](https://www.microsoft.com/download/details.aspx?id=57172)  
>* Dla programu System Center Operations Manager 2012 R2, należy pobrać pakiet administracyjny z [tutaj](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Ta aktualizacja pakietu zarządzania nie ma zastosowania do System Center Operations Manager 1807, który jest wersja pakietu aktualizacji z wersji 1801 i nie pełnej kompilacji produktu.   

### <a name="network"></a>Sieć

Poniższe informacje lista serwera proxy i zapory konfiguracji wymaganych informacji dla agenta programu Operations Manager, serwerów zarządzania i konsola operacje, do komunikowania się z usługą Azure Monitor. Ruch sieciowy z poszczególnych składników jest ruch wychodzący z sieci lokalnej do usługi Azure Monitor.   

|Resource | Numer portu| Obejście inspekcji HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Yes|  
|\*.oms.opinsights.azure.com| 443|Tak|  
|\*.blob.core.windows.net| 443|Tak|  
|\*.azure-automation.net| 443|Yes|  
|**Serwer zarządzania**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Tak|  
|\*.ods.opinsights.azure.com| 443| Yes|  
|*.azure-automation.net | 443| Tak|  
|**Konsola programu Operations Manager do usługi Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*. live.com| 80 i 443||  
|\*.microsoft.com| 80 i 443||  
|\*. microsoftonline.com| 80 i 443||  
|\*.mms.microsoft.com| 80 i 443||  
|login.windows.net| 80 i 443||  
|portal.loganalytics.io| 80 i 443||
|api.loganalytics.io| 80 i 443||
|docs.loganalytics.io| 80 i 443||  

### <a name="tls-12-protocol"></a>Protokół TLS 1.2

Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do usługi Azure Monitor, zdecydowanie zachęcamy do skonfigurowania grupy zarządzania i agent do użycia w co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**. Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Łączenie programu Operations Manager do usługi Azure Monitor

Wykonaj następujące czynności, aby skonfigurować grupę zarządzania programu Operations Manager na potrzeby nawiązywania połączenia z jednym z obszarów roboczych usługi Log Analytics.

Podczas początkowego rejestrowania grupy zarządzania programu Operations Manager z obszarem roboczym usługi Log Analytics opcję, aby określić konfigurację serwera proxy dla grupy zarządzania nie jest dostępne w konsoli operacje.  Ta opcja staje się dostępna dopiero po pomyślnym zarejestrowaniu grupy zarządzania w usłudze.  Aby obejść ten problem, należy zaktualizować system konfiguracji serwera proxy, za pomocą polecenia Netsh w systemie konsoli operacje z systemem do konfigurowania integracji i wszystkich serwerów zarządzania w grupie zarządzania.  

1. Otwórz wiersz polecenia z podwyższonymi uprawnieniami.
   a. Przejdź do **Start** i typ **cmd**.
   b. Kliknij prawym przyciskiem myszy **polecenia** i wybierz polecenie Uruchom jako administrator **.
1. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po zakończeniu poniższe kroki, aby zintegrować z usługą Azure Monitor, możesz usunąć konfigurację, uruchamiając `netsh winhttp reset proxy` , a następnie użyj **skonfiguruj poświadczenia serwera proxy** opcję w konsoli operacje, aby określić serwer proxy lub dziennika Serwer bramy usługi Analytics.

1. W konsoli programu Operations Manager wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Zarejestruj się w konsoli Operations Management Suite**.
1. Na **Kreator przechodzenia do pakietu Operations Management Suite: Uwierzytelnianie** strony, wprowadź adres e-mail lub numer telefonu i hasło konta administratora, który jest skojarzony z subskrypcją pakietu OMS, a następnie kliknij przycisk **Zaloguj**.

   >[!NOTE]
   >Nazwa pakietu Operations Management Suite został wycofany.

1. Po pomyślnym uwierzytelnieniu na **Kreator przechodzenia do pakietu Operations Management Suite: Wybierz obszar roboczy** stronie zostanie wyświetlony monit wybierz Twojej dzierżawy platformy Azure, subskrypcja i obszar roboczy usługi Log Analytics. Jeśli masz więcej niż jeden obszar roboczy, wybierz z listy rozwijanej ten obszar roboczy, który ma zostać zarejestrowany w grupie zarządzania programu Operations Manager, a następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Program Operations Manager obsługuje jednocześnie tylko jeden obszar roboczy usługi Log Analytics. Połączenie i komputerów, które zostały zarejestrowane do usługi Azure Monitor z poprzednim obszarze roboczym zostaną usunięte z usługi Azure Monitor.
   >
   >
1. Na **Kreator przechodzenia do pakietu Operations Management Suite: Podsumowanie** strony, Potwierdź ustawienia, a jeśli są poprawne, kliknij przycisk **Utwórz**.
1. Na **Kreator przechodzenia do pakietu Operations Management Suite: Zakończ** kliknij **Zamknij**.

### <a name="add-agent-managed-computers"></a>Dodawanie komputerów zarządzanych przez agenta

Po skonfigurowaniu integracji z obszarem roboczym usługi Log Analytics, tylko nawiązuje połączenie z usługą, żadne dane nie są zbierane z agentów raportujących do grupy zarządzania. Nie dzieje, aż po skonfigurowaniu, które określone komputery zarządzane z wykorzystaniem agentów zbierania danych dzienników dla usługi Azure Monitor. Obiekty komputerów można wybierać indywidualnie lub przez wybranie grupy zawierającej obiekty komputerów z systemem Windows. Nie można wybrać grupy zawierającej wystąpienia innej klasy, na przykład dysków logicznych lub baz danych SQL.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Dodaj komputer/grupę** pod nagłówkiem Akcje w prawej części okienka.
1. W oknie dialogowym **Wyszukiwanie komputerów** można przeprowadzić wyszukiwanie komputerów lub grup monitorowanych przez program Operations Manager. Wybierz komputery lub grupy do dołączenia do usługi Azure Monitor, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

Komputery i grupy skonfigurowane na potrzeby zbierania danych z węzła Komputery zarządzane można wyświetlić w sekcji Operations Management Suite w obszarze roboczym **Administracja** konsoli zarządzania operacjami. W tym miejscu możesz odpowiednio do potrzeb dodawać lub usuwać komputery i grupy.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurowanie ustawień serwera proxy w konsoli zarządzania operacjami

Jeśli wewnętrzny serwer proxy między grupą zarządzania a usługa Azure Monitor, należy wykonać następujące czynności. Te ustawienia są centralnie zarządzane z grupy zarządzania i dystrybucji do systemów zarządzanych przez agentów, które wchodzą w zakres, aby zebrać dane dziennika dla usługi Azure Monitor.  Jest to korzystne, gdy niektóre rozwiązania pomijają serwer zarządzania i wysyłają dane bezpośrednio do usługi.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite, a następnie kliknij pozycję **Połączenia**.
1. W widoku Połączenie OMS kliknij pozycję **Konfiguruj serwer proxy**.
1. Na **Kreator Operations Management Suite: Serwer proxy** wybierz opcję **Użyj serwera proxy do dostępu do pakietu Operations Management Suite**, a następnie wpisz adres URL z numerem portu, na przykład http://corpproxy:80 a następnie kliknij przycisk **Zakończ** .

Jeśli Twój serwer proxy wymaga uwierzytelniania, należy wykonać poniższe kroki, aby skonfigurować poświadczenia i ustawienia, które muszą zostać poddane propagacji na zarządzanych komputerach, które raporty do usługi Azure Monitor w grupie zarządzania.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. W obszarze **Konfiguracja Uruchom jako** wybierz pozycję **Profile**.
1. Otwórz profil **Serwer proxy profilu Uruchom jako usługi System Center Advisor**.
1. W kreatorze profilu Uruchom jako kliknij przycisk Dodaj, aby użyć konta Uruchom jako. Możesz utworzyć [konto Uruchom jako](https://technet.microsoft.com/library/hh321655.aspx) lub użyć istniejącego konta. Konto to musi mieć wystarczające uprawnienia do komunikacji za pośrednictwem serwera proxy.
1. Aby ustawić konto do zarządzania, wybierz pozycję **Wybrana klasa, grupa lub obiekt**, kliknij polecenie **Wybierz...** , a następnie kliknij pozycję **Grupuj...** w celu otwarcie okna **Wyszukiwanie grup**.
1. Wyszukaj **grupę serwerów monitorowania usługi Microsoft System Center Advisor**, a następnie ją wybierz. Po wybraniu grupy kliknij przycisk **OK**, aby zamknąć okno **Wyszukiwanie grup**.
1. Kliknij przycisk **OK**, aby zamknąć okno **Dodawanie konta Uruchom jako**.
1. Kliknij przycisk **Zapisz**, aby zakończyć pracę kreatora i zapisać zmiany.

Po utworzeniu połączenia i konfigurowanie agentów będą zbierania i raportowania danych dzienników do usługi Azure Monitor, następująca konfiguracja jest stosowane w grupie zarządzania, niekoniecznie w kolejności:

* Tworzone jest konto Uruchom jako o nazwie **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Zostaje ono skojarzone z profilem Uruchom jako **Microsoft System Center Advisor Run As Profile Blob** i dotyczy dwóch klas — **Collection Server** i **Operations Manager Management Group**.
* Tworzone są dwa łączniki.  Nosi nazwę pierwszego **Microsoft.SystemCenter.Advisor.DataConnector** i jest automatycznie konfigurowany za pomocą subskrypcji, która przekazuje wszystkie alerty wygenerowane z wystąpień wszystkie klasy w grupie zarządzania do usługi Azure Monitor. Drugi łącznik jest **łącznik usługi Advisor**, który jest odpowiedzialny za komunikacji z usługą Azure Monitor i udostępniania danych.
* Agenci i grupy wybrane do zbierania danych w grupie zarządzania są dodawane do **grupy serwera monitorowania usługi Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizacje pakietu administracyjnego

Po zakończeniu konfiguracji grupy zarządzania programu Operations Manager ustanawia połączenie z usługą Azure Monitor. Serwer zarządzania synchronizuje się z usługą internetową i odbiera zaktualizowane informacje o konfiguracji w postaci pakietów administracyjnych dla włączonych rozwiązań zintegrowanych z programem Operations Manager. Operations Manager sprawdza dostępność aktualizacji tych pakietów administracyjnych i automatycznie Pobierz i importuje je, gdy są one dostępne. To zachowanie jest kontrolowane w szczególności przez dwie reguły:

* **Microsoft.SystemCenter.Advisor.MPUpdate** — aktualizuje podstawowy pakiety zarządzania usługi Azure Monitor. Ta reguła jest domyślnie uruchamiana co 12 godzin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** — aktualizuje pakiety administracyjne rozwiązań, które zostały włączone w obszarze roboczym. Ta reguła jest domyślnie uruchamiana co pięć (5) minut.

Możesz zastąpić te dwie reguły, aby uniemożliwić automatyczne pobieranie, wyłączając je, lub zmodyfikować częstotliwości jak często serwer zarządzania programu synchronizuje się z usługą Azure Monitorto określają, jeśli nowy pakiet administracyjny jest dostępny i powinny być pobierane. Wykonaj instrukcje [How to Override a Rule or Monitor](https://technet.microsoft.com/library/hh212869.aspx) (Jak przesłonić regułę lub monitor), aby zmodyfikować parametr **Frequency** (Częstotliwość) wartością w sekundach w celu zmiany harmonogramu synchronizacji lub parametr **Enabled** (Włączone) w celu wyłączenia reguł. Przesłonięcie dotyczy wszystkich obiektów klasy Operations Manager Management Group.

Aby kontynuować, następujące usługi istniejący proces kontroli zmian kontroli wersji pakietu zarządzania w danej grupie zarządzania w środowisku produkcyjnym, można wyłączyć reguły i je włączyć w określonych godzinach, kiedy aktualizacje są dozwolone. Jeśli w swoim środowisku masz grupę deweloperską lub grupę zarządzania kontroli jakości, która ma połączenie z Internetem, możesz skonfigurować tę grupę zarządzania za pomocą obszaru roboczego usługi Log Analytics do obsługi tego scenariusza. Dzięki temu można Przegląd i ocena iteracyjne wersje pakietów administracyjnych usługi Azure Monitor przed ich do grupy zarządzania w środowisku produkcyjnym.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Przełączanie grupy programu Operations Manager do nowego obszaru roboczego usługi Log Analytics

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics** i utwórz obszar roboczy.  
1. Otwórz konsolę programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł usługi Log Analytics, a następnie wybierz pozycję **połączeń**.
1. Wybierz link **Skonfiguruj ponownie konsolę Operation Management Suite** na środku panelu.
1. Postępuj zgodnie z **Kreator przechodzenia do usługi Log Analytics** i wprowadź adres e-mail adres lub numer telefonu oraz hasło konta administratora, który jest skojarzony z nowym obszarem roboczym usługi Log Analytics.

   > [!NOTE]
   > **Kreator przechodzenia do pakietu Operations Management Suite: Wybierz obszar roboczy** strona przedstawia istniejącego obszaru roboczego, który jest używany.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Weryfikowanie integracji programu Operations Manager z usługą Azure Monitor

Istnieje kilka różnych sposobów, możesz sprawdzić, że usługi Azure Monitor do integracji programu Operations Manager zakończyła się powodzeniem.

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

Jeśli integracja między grupą zarządzania programu Operations Manager i obszarem roboczym usługi Log Analytics nie jest już potrzebna, należy wykonać kilka czynności, aby prawidłowo usunąć połączenie i konfigurację grupy zarządzania. Poniższa procedura ma możesz zaktualizować obszar roboczy usługi Log Analytics, usuwając odwołanie do grupy zarządzania, usuwanie łączników usługi Azure Monitor, a następnie usuń pakiety zarządzania obsługuje integrację z usługą.  

Pakiety administracyjne dla rozwiązania zostało włączone, które integrują się z programem Operations Manager i pakiety administracyjne wymagane do obsługi integracji z usługą Azure Monitor nie może zostać łatwo usunięty z grupy zarządzania. Jest to spowodowane niektóre pakiety administracyjne usługi Azure Monitor zależnościami od innych powiązanych pakietów. Aby usunąć pakiety administracyjne, które są zależne od innych pakietów administracyjnych, pobierz skrypt [usuwający pakiet administracyjny z zależnościami](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z Centrum skryptów w witrynie TechNet.  

1. Otwórz powłokę poleceń programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.

    > [!WARNING]
    > Przed przejściem do następnego kroku upewnij się, nie masz żadnych niestandardowych pakietów administracyjnych z ciągiem Advisor lub IntelligencePack w nazwie. W przeciwnym razie kolejne kroki spowodują ich usunięcie z grupy zarządzania.
    >

1. W wierszu polecenia powłoki wpisz `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Następnie wpisz `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Aby usunąć wszystkie pozostałe pakiety administracyjne, które są zależne od innych pakietów administracyjnych programu System Center Advisor, użyj skryptu *RecursiveRemove.ps1* pobranego wcześniej z Centrum skryptów w witrynie TechNet.  

    > [!NOTE]
    > Krok, aby usunąć pakiety zarządzania klasyfikatora przy użyciu programu PowerShell nie usunie automatycznie pakietów administracyjnych programu Microsoft System Center Advisor lub programu Microsoft System Center Advisor wewnętrznego.  Nie należy próbować usunąć je.  
    >  

1. Otwórz konsolę zarządzania operacjami programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
1. W obszarze **Administracja** wybierz węzeł **Pakiety administracyjne**, a następnie w polu tekstowym **Wyszukaj:** wpisz **Advisor** i upewnij się, że następujące pakiety administracyjne są nadal zaimportowane w Twojej grupie zarządzania:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. W witrynie Azure portal kliknij pozycję **ustawienia** kafelka.
1. Wybierz **połączonych źródeł**.
1. W tabeli w sekcji System Center Operations Manager powinien zostać wyświetlony nazwę grupy zarządzania, który chcesz usunąć z obszaru roboczego. W kolumnie **Ostatnie dane** kliknij link **Usuń**.  

    > [!NOTE]
    > Link **Usuń** nie będzie dostępny po 14 dniach, jeśli nie zostanie wykryta żadna aktywność w połączonej grupie zarządzania.  
    >

1. Zostanie wyświetlone okno z prośbą o potwierdzenie operacji usunięcia.  Kliknij pozycję **Tak**, aby kontynuować.

Aby usunąć dwa łączniki — jeden o nazwie Microsoft.SystemCenter.Advisor.DataConnector, a drugi o nazwie Advisor Connector, zapisz poniższy skrypt programu PowerShell na swoim komputerze i wykonaj go, korzystając z poniższych przykładów:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
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

W przyszłości, jeśli planujesz ponowne nawiązywanie połączenia grupy zarządzania do obszaru roboczego usługi Log Analytics, należy ponownie zaimportować `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` pliku pakietu administracyjnego. W zależności od wersji programu System Center Operations Manager wdrożonego w środowisku ten plik można znaleźć w następującej lokalizacji:

* Na nośniku źródłowym w folderze `\ManagementPacks` dla programu System Center 2016 — Operations Manager lub nowszego.
* W najnowszym pakiecie zbiorczym aktualizacji zastosowanym w grupie zarządzania. W przypadku programu Operations Manager 2012 jest folder źródłowy `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` i 2012 R2 znajduje się w `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Kolejne kroki

Aby dodać funkcje i zebrać dane, zobacz [rozwiązań Dodaj usługi Azure Monitor z galerii rozwiązań](../../azure-monitor/insights/solutions.md).
