---
title: Zbieranie danych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak włączyć funkcję zbierania danych w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/2/2018
ms.author: rkarlin
ms.openlocfilehash: f6a818e0f628924342ddd50322bb54bf1d1d6079
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976665"
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w usłudze Azure Security Center
Usługa Security Center zbiera dane z maszyn wirtualnych (VM) i komputerów spoza platformy Azure do monitorowania pod kątem luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy. Przykłady takich danych to: operacyjnych, typ i wersja, dzienniki systemu (Windows dzienniki zdarzeń), operacyjnego systemu uruchomione procesy, Nazwa maszyny, adresy IP i zalogowanego użytkownika. Program Microsoft Monitoring Agent kopiuje również pliki zrzutu awaryjnego do swojego obszaru roboczego.

Zbieranie danych jest wymagany do zapewniają widoczność brakujących aktualizacji, nieprawidłowo skonfigurowane ustawienia zabezpieczeń systemu operacyjnego, włączenie ochrony punktu końcowego i wykrywania zagrożeń i kondycji. 

Ten artykuł zawiera wskazówki dotyczące sposobu instalacji programu Microsoft Monitoring Agent i ustaw obszar roboczy usługi Log Analytics, w którym będą przechowywane zebrane dane. Obie operacje są wymagane do włączenia zbierania danych. 

> [!NOTE]
> - Zbieranie danych jest potrzebna tylko dla zasobów obliczeniowych (maszyn wirtualnych i komputerów spoza platformy Azure). Mogą korzystać z usługi Azure Security Center, nawet jeśli nie możesz aprowizować agentów; jednak mają ograniczone zabezpieczeń i możliwości wymienione powyżej są nieobsługiwane.  
> - Aby uzyskać listę obsługiwanych platform, zobacz [obsługiwanych platform w usłudze Azure Security Center](security-center-os-coverage.md).
> - Zbieranie danych dla zestawu skalowania maszyn wirtualnych nie jest obecnie obsługiwane.


## Włącz automatyczną aprowizację programu Microsoft Monitoring Agent <a name="auto-provision-mma"></a>

Aby zebrać dane z maszyn powinna mieć zainstalowany program Microsoft Monitoring Agent.  Instalacja agenta może być automatycznie (zalecane) lub możesz zainstalować agenta ręcznie.  

>[!NOTE]
> Automatyczna aprowizacja jest domyślnie wyłączona. Security Center mogła zainstalować automatycznej aprowizacji domyślnie ustawia się je **na**.
>

Automatyczna aprowizacja jest włączona, usługa Security Center aprowizuje program Microsoft Monitoring Agent na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszelkie nowe, które są tworzone. Automatycznej aprowizacji jest zdecydowanie zalecane, ale jest również dostępna ręcznej instalacji agenta. [Dowiedz się, jak można zainstalować rozszerzenia Microsoft Monitoring Agent](#manualagent).



Aby włączyć automatyczną aprowizację programu Microsoft Monitoring Agent:
1. W menu głównym usługi Security Center wybierz **zasady zabezpieczeń**.
2. Kliknij przycisk **edytować ustawienia** w kolumnie ustawień odpowiedniej subskrypcji na liście.

  ![Wybieranie subskrypcji][7]

3. W obszarze **Zasady zabezpieczeń** wybierz pozycję **Zbieranie danych**.
4. W obszarze **automatycznej aprowizacji**, wybierz opcję **na** Aby włączyć automatyczną aprowizację.
5. Wybierz pozycję **Zapisz**.

  ![Włączanie automatycznej aprowizacji][1]

>[!NOTE]
> - Aby uzyskać instrukcje, jak wykonać aprowizację wstępnie istniejącej instalacji, zobacz [automatycznej aprowizacji w przypadku istniejących instalacji agenta](#preexisting).
> - Aby uzyskać instrukcje dotyczące ręcznego inicjowania obsługi administracyjnej, zobacz [ręcznie zainstalować rozszerzenia Microsoft Monitoring Agent](#manualagent).
> - Aby uzyskać instrukcje na wyłączenie automatycznej aprowizacji, zobacz [Wyłącz automatyczną aprowizację](#offprovisioning).
> - Aby uzyskać instrukcje dotyczące dołączania Centrum zabezpieczeń przy użyciu programu PowerShell, zobacz temat [automatyzacji dołączania do usługi Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguracja obszaru roboczego
Dane zbierane przez usługę Security Center są przechowywane w obszarach roboczych usługi Log Analytics.  Możesz wybrać dane zebrane z maszyn wirtualnych platformy Azure, przechowywane w obszary robocze utworzone przez usługę Security Center lub istniejący obszar roboczy, który został utworzony. 

Konfiguracja obszaru roboczego jest ustawiony na subskrypcję, a wiele subskrypcji może używać tego samego obszaru roboczego.

### <a name="using-a-workspace-created-by-security-center"></a>Obszar roboczy utworzony przez usługę Security Center

Usługa Security center może automatycznie tworzyć domyślnego obszaru roboczego, w którym będą przechowywane dane. 

Aby wybrać obszar roboczy utworzony przez usługę Security Center:

1.  W obszarze **domyślną konfigurację obszaru zabezpieczeń**, zaznacz pole wyboru Użyj obszarów roboczych utworzonych przez usługę Security center.
   ![Wybierz warstwę cenową][10] 

2. Kliknij pozycję **Zapisz**.<br>
    Usługa Security Center utworzy nowy zasób grupy i domyślny obszar roboczy w tym geolokalizacja i łączy agenta do tego obszaru roboczego. Konwencja nazewnictwa dla obszaru roboczego i grupy zasobów jest:<br>
**Obszar roboczy: DefaultWorkspace-[identyfikator subskrypcji]-[geograficzna]<br> grupy zasobów: DefaultResourceGroup-[geograficzna]**

   Jeśli subskrypcja zawiera maszyny wirtualne z wieloma geolocations, usługa Security Center tworzy wiele obszarów roboczych. Wiele obszarów roboczych są tworzone do obsługi zasad ochrony prywatności danych.
-   Usługa Security Center spowoduje automatyczne włączenie rozwiązania Security Center w obszarze roboczym na warstwy cenowej dla subskrypcji. 

> [!NOTE]
> Obszary robocze utworzone przez usługę Security Center w warstwie taryfowy usługi log Analytics nie ma wpływu na rozliczenia w usłudze Security Center. Rozliczanie usługi Security Center zawsze zależy od zasad zabezpieczeń usługa Security Center i rozwiązań zainstalowane w obszarze roboczym. Dla warstwy bezpłatnej, usługa Security Center umożliwia *SecurityCenterFree* rozwiązania w domyślnym obszarze roboczym. W przypadku warstwy Standard usługi Security Center umożliwia *zabezpieczeń* rozwiązania w domyślnym obszarze roboczym.

Aby uzyskać więcej informacji na temat cen, zobacz [cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Aby uzyskać więcej informacji na temat istniejących kont usługi Log Analytics, zobacz [klientów istniejącej usługi Log Analytics](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Przy użyciu istniejącego obszaru roboczego

Jeśli masz już istniejący obszar roboczy usługi Log Analytics można użyć tego samego obszaru roboczego.

Aby użyć istniejącego obszaru roboczego usługi Log Analytics, konieczne jest posiadanie Odczyt i zapis w obszarze roboczym.

> [!NOTE]
> Rozwiązania włączone w istniejącym obszarem roboczym zostaną zastosowane do maszyn wirtualnych platformy Azure, które są podłączone do niego. W przypadku płatnych rozwiązań może to spowodować naliczenie dodatkowych opłat. Dla kwestii związanych z prywatnością danych upewnij się, że wybrany obszar roboczy znajduje się w regionie geograficznym bezpośrednio.
>

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **domyślną konfigurację obszaru zabezpieczeń**, wybierz opcję **Użyj innego obszaru roboczego**.

   ![Wybierz istniejący obszar roboczy][2]

2. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

  > [!NOTE]
  > W ściągnąć menu dostępne są wszystkie obszary robocze dla wszystkich subskrypcji. Zobacz [cross wybór obszaru roboczego subskrypcji](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Aby uzyskać więcej informacji. Musi mieć uprawnienia dostępu do obszaru roboczego.
  >
  >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu **Zapisz**, konieczne będzie podanie, jeśli chcesz ponownie skonfigurować monitorowane maszyny wirtualne, które wcześniej zostały podłączone do domyślnego obszaru roboczego.

   - Wybierz **nie** Jeśli chcesz, aby nowe ustawienia obszaru roboczego, można zastosować tylko nowych maszyn wirtualnych. Nowe ustawienia obszaru roboczego dotyczą tylko nowe instalacje agentów; odnalezione maszyny wirtualne, które nie mają zainstalowany program Microsoft Monitoring Agent.
   - Wybierz **tak** Jeśli chcesz, aby nowe ustawienia obszaru roboczego, aby zastosować na wszystkich maszynach wirtualnych. Ponadto każda maszyna wirtualna podłączone do usługi Security Center, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszary robocze utworzone przez usługę Security Center, aż wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, za wczesne usunięcie obszaru roboczego.
   >
   >

   - Wybierz **anulować** anulować operację.

     ![Wybierz istniejący obszar roboczy][3]

5. Wybierz warstwę cenową dla żądanego obszaru roboczego, które mają zostać ustawione Microsoft Monitoring agent. <br>Aby użyć istniejącego obszaru roboczego, ustawienie warstwy cenowej dla obszaru roboczego. Jeśli nie jest już obecny spowoduje to zainstalowanie rozwiązania Centrum zabezpieczeń w obszarze roboczym.

    a.  W menu głównym usługi Security Center wybierz **zasady zabezpieczeń**.
     
    b.  Wybieranie żądanego obszaru roboczego, w którym trzeba połączyć agenta, klikając **edytować ustawienia** w kolumnie ustawień odpowiedniej subskrypcji na liście.
        ![Wybierz obszar roboczy][8] c. Ustawienie warstwy cenowej.
        ![Wybierz warstwę cenową][9] 
   
   >[!NOTE]
   >Jeśli obszar roboczy ma już **zabezpieczeń** lub **SecurityCenterFree** włączonego rozwiązania ceny zostaną ustawione automatycznie. 

## <a name="cross-subscription-workspace-selection"></a>Wybór obszaru roboczego między subskrypcjami
Po wybraniu obszaru roboczego, w którym do przechowywania danych, wszystkie obszary robocze dla wszystkich swoich subskrypcji są dostępne. Wybór między subskrypcjami obszaru roboczego umożliwia zbieranie danych z maszyn wirtualnych działających w różnych subskrypcjach i zapisz go w obszarze roboczym wybór. Zaznacz to pole wyboru jest przydatne, jeśli używasz scentralizowanych obszarów roboczych w Twojej organizacji i chcesz używać go do zbierania danych zabezpieczeń. Aby uzyskać więcej informacji na temat sposobu zarządzania obszarami roboczymi, zobacz [zarządzanie dostępem do obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Warstwa kolekcji danych
Wybieranie warstwy zbierania danych w usłudze Azure Security Center mają wpływ tylko na magazyn zdarzeń zabezpieczeń w obszarze roboczym usługi Log Analytics. Program Microsoft Monitoring Agent nadal będzie Zbieraj i Analizuj zdarzenia zabezpieczeń wymaganych do wykrywania zagrożeń usługi Azure Security Center, niezależnie od tego, która warstwa zdarzeń zabezpieczeń zdecydujesz się przechowywać w obszarze roboczym usługi Log Analytics (jeśli istnieje). Wybieranie do przechowywania zdarzeń związanych z zabezpieczeniami w obszarze roboczym spowoduje włączenie analizy, wyszukiwania i inspekcji tych zdarzeń w obszarze roboczym. 
> [!NOTE]
> Przechowywanie danych w usłudze Log Analytics może Naliczanie dodatkowych opłat za magazyn danych, zobacz stronę cennika, aby uzyskać więcej informacji.
>
Możesz wybrać zasady dla subskrypcji i obszarów roboczych cztery rodzaje zdarzeń filtrowania prawej strony mają być przechowywane w obszarze roboczym: 

- **Brak** — Wyłącz magazyn zdarzeń zabezpieczeń. Jest to ustawienie domyślne.
- **Minimalny** — mniejszy zestaw zdarzeń, które klienci, którzy chcą, aby zminimalizować wolumin zdarzeń.
- **Typowe** — jest to zestaw zdarzeń, który spełnia większość klientów i umożliwia im pełnego dziennika inspekcji.
- **Wszystkie zdarzenia** — dla klientów, którzy chcą, aby upewnić się, że wszystkie zdarzenia są przechowywane.


> [!NOTE]
> Te zestawy zdarzenia zabezpieczeń są dostępne tylko dla warstwy standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
Te zestawy są przeznaczone dla typowych scenariuszy. Upewnij się ocenić, która opcja spełnia Twoje potrzeby, przed rozpoczęciem implementacji.
>
>

Aby określić zdarzenia, które będą należeć do **typowe** i **minimalny** ustawia zdarzenie współpracowaliśmy z klientami i standardów branżowych, aby dowiedzieć się więcej na temat niefiltrowane częstotliwość każdego zdarzenia i ich użycia. Użyliśmy następujące wytyczne w ramach tego procesu:

- **Minimalny** — upewnij się, że ten zestaw obejmuje tylko te zdarzenia, które mogą wskazywać na naruszenie pomyślne i ważne wydarzenia, które mają bardzo niskim poziomie. Na przykład ten zestaw zawiera udane i nieudane logowania użytkownika (zdarzenie 4624 identyfikatory 4625), ale nie zawiera on wylogowania, co jest ważne w przypadku inspekcji, ale nie ma istotnego znaczenia dla wykrywania i ma stosunkowo dużych ilościach. W większości ilość danych, ten zestaw jest zdarzenia logowania i procesu tworzenia zdarzeń (zdarzenie 4688 identyfikator).
- **Typowe** — Podaj użytkownika pełnego dziennika inspekcji, w tym zestawie. Na przykład ten zestaw zawiera identyfikatory logowania użytkownika i wylogowywania użytkowników (zdarzenie ID 4634). Dołączamy inspekcji akcji, takich jak zmiany w grupie zabezpieczeń, operacji protokołu Kerberos kontrolera domeny kluczy i innych zdarzeń, które są zalecane przez organizacje z branży.

Zdarzenia, które mają bardzo małą liczbą zostały uwzględnione w typowych Ustaw jako główny motywacja wybrać za pośrednictwem wszystkich zdarzeń jest redukować ilość danych, a nie odfiltrować określonych zdarzeń.

Tutaj znajduje się pełna lista zabezpieczeń i AppLocker identyfikatory zdarzeń dla każdego zestawu:

| Warstwa danych | Wskaźniki zebranych zdarzeń |
| --- | --- |
| Minimalny | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Wspólne | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Jeśli używasz obiektów zasad grupy (GPO), zalecane jest, aby włączyć zasady inspekcji procesu tworzenia zdarzeń 4688 i *CommandLine* pola wewnątrz zdarzeń 4688. Aby uzyskać więcej informacji na temat procesu tworzenia zdarzeń 4688 korzystać z usługi Security Center [— często zadawane pytania](security-center-faq.md#what-happens-when-data-collection-is-enabled). Aby uzyskać więcej informacji na temat tych zasady inspekcji, zobacz [zalecenia dotyczące zasad inspekcji](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Aby włączyć zbieranie danych dla [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), usługa Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. To spowoduje, że zasady ograniczeń oprogramowania do wygenerowania zdarzenia, które następnie są pobierane i wykorzystywane przez usługę Security Center. Należy zauważyć, że te zasady nie zostaną skonfigurowane na maszynach, na których jest już skonfigurowane zasady funkcji AppLocker. 
> - Aby zebrać Windows filtrowanie platformy [5156 identyfikator zdarzenia](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), należy włączyć [połączenie platformy filtrowania inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/subcategory: "Połączenie platformy filtrowania" /Success:Enable)
>

Aby wybrać zasady filtrowania:
1. Na **zasady zabezpieczeń zbierania danych** bloku wybierz zasady filtrowania w obszarze **zdarzeń związanych z zabezpieczeniami**.
2. Wybierz pozycję **Zapisz**.

   ![Wybierz zasady filtrowania][5]

### Automatyczna aprowizacja w przypadku istniejących instalacji agenta <a name="preexisting"></a> 

Określ następujące przypadki użycia, jak automatyczna aprowizacja działa w przypadku, gdy istnieje już agenta lub rozszerzenia zainstalowane. 

- Program Microsoft Monitoring Agent jest zainstalowany na komputerze, ale nie jako rozszerzenie<br>
Jeśli program Microsoft Monitoring Agent jest zainstalowany bezpośrednio na maszynie Wirtualnej (a nie jako rozszerzenie platformy Azure), usługa Security Center nie instaluje program Microsoft Monitoring Agent. Można włączyć automatycznej aprowizacji i wybierz obszar roboczy odpowiednich użytkownika w Centrum zabezpieczeń automatycznie konfigurację aprowizacji. Po wybraniu tego samego obszaru roboczego, do których maszyna wirtualna jest już połączona z istniejącego agenta zostanie zawinięta przy użyciu rozszerzenia programu Microsoft Monitoring Agent. 

> [!NOTE]
> Jeśli zainstalowano agenta w wersji 2012 programu SCOM, **nie** Włączanie automatycznego inicjowania obsługi na. 

Aby uzyskać więcej informacji, zobacz [co się stanie w przypadku pakietu OMS lub SCOM programu bezpośredni agent jest już zainstalowany na maszynie Wirtualnej?](security-center-faq.md#scomomsinstalled)

-   Istniejące rozszerzenia maszyny Wirtualnej jest obecny<br>
    - Usługa Security center obsługuje istniejących instalacji rozszerzenia, a nie zastępuje istniejące połączenia. Usługa Security Center przechowuje zabezpieczeń danych z maszyny Wirtualnej w obszarze roboczym już połączony i zapewnia ochronę w oparciu o rozwiązania włączone w obszarze roboczym.   
    - Aby zobaczyć, na który obszar roboczy istniejące rozszerzenie wysyła dane, uruchom test, aby [sprawdzania poprawności łączności z usługą Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Alternatywnie można Otwórz program Log analytics, wybierz obszar roboczy, wybierz maszynę Wirtualną i przyjrzyj się połączenia programu Microsoft Monitoring Agent. 
    - Jeśli masz środowisko usługi gdzie programu Microsoft Monitoring Agent jest zainstalowany na klienckich stacjach roboczych i raportowania do istniejącego obszaru roboczego usługi Log Analytics, przejrzyj listę [systemów operacyjnych obsługiwanych przez usługę Azure Security Center](security-center-os-coverage.md) do Upewnij się, system operacyjny jest obsługiwany, a następnie zobacz [klientów istniejącej usługi Log Analytics](security-center-faq.md#existingloganalyticscust) Aby uzyskać więcej informacji.
 
### Wyłącz automatyczną aprowizację <a name="offprovisioning"></a>
Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. 


1. Wróć do menu głównego usługi Security Center i wybierz zasady zabezpieczeń.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. Na **zasady zabezpieczeń — zbierania danych** bloku, w obszarze **automatycznej aprowizacji** wybierz **poza**.
4. Wybierz pozycję **Zapisz**.

  ![Wyłącz automatyczne udostępnianie][6]

Wyłączenie automatycznej aprowizacji (wyłączone), sekcja konfiguracji domyślnego obszaru roboczego nie jest wyświetlana.

Jeśli wyłączyć automatyczna obsługa po został wcześniej na:
-   Agenci nie będą udostępniane na nowych maszynach wirtualnych.
-   Usługa Security Center zatrzymuje zbieranie danych z domyślnego obszaru roboczego.
 
> [!NOTE]
>  Wyłączenie automatycznej aprowizacji nie powoduje usunięcia programu Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, w których aprowizowano agenta. Aby uzyskać informacje na temat usuwania rozszerzenie pakietu OMS, zobacz [jak usunąć zainstalowanych przez usługę Security Center rozszerzeń pakietu OMS](security-center-faq.md#remove-oms).
>
    
## Agent ręcznego inicjowania obsługi administracyjnej <a name="manualagent"></a>
 
Istnieje kilka sposobów, aby ręcznie zainstalować program Microsoft Monitoring Agent. W przypadku ręcznej instalacji upewnij się, że wyłączenie automatycznej aprowizacji.

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite w maszynie Wirtualnej rozszerzenie wdrożenia 

Program Microsoft Monitoring Agent, można zainstalować ręcznie, dzięki czemu usługa Security Center może zbierania danych zabezpieczeń z maszyn wirtualnych i udostępniania zaleceń i alertów.
1.  Wybierz Auto zaopatrzenie — wyłączone.
2.  Utwórz obszar roboczy i ustawienie warstwy cenowej dla obszaru roboczego, które mają zostać ustawione Microsoft Monitoring agent:

    a.  W menu głównym usługi Security Center wybierz **zasady zabezpieczeń**.
     
    b.  Wybierz obszar roboczy, w którym trzeba połączyć agenta. Upewnij się, że obszar roboczy jest w tej samej subskrypcji, użyj w usłudze Security Center i czy masz uprawnienia odczytu/zapisu w obszarze roboczym.
        ![Wybierz obszar roboczy][8]
3. Ustawienie warstwy cenowej.
   ![Wybierz warstwę cenową][9] 
   >[!NOTE]
   >Jeśli obszar roboczy ma już **zabezpieczeń** lub **SecurityCenterFree** włączonego rozwiązania ceny zostaną ustawione automatycznie. 
   > 

4.  Jeśli chcesz wdrożyć agentów na nowych maszyn wirtualnych przy użyciu szablonu usługi Resource Manager, należy zainstalować rozszerzenia maszyny wirtualnej pakietu OMS:

    a.  [Zainstaluj rozszerzenie maszyny wirtualnej pakietu OMS dla Windows](../virtual-machines/extensions/oms-windows.md)
    
    b.  [Zainstaluj rozszerzenie maszyny wirtualnej pakietu OMS dla systemu Linux](../virtual-machines/extensions/oms-linux.md)
5.  Aby wdrożyć rozszerzeń na istniejących maszynach wirtualnych, postępuj zgodnie z instrukcjami [zbierać dane o maszynach wirtualnych Azure](../log-analytics/log-analytics-quick-collect-azurevm.md).

  > [!NOTE]
  > Sekcja **zbierania danych zdarzeń i wydajności** jest opcjonalne.
  >
6. Aby wdrożyć rozszerzenie za pomocą programu PowerShell, skorzystaj z następującego przykładu programu PowerShell:
    1.  Przejdź do **usługi Log Analytics** i kliknij pozycję **Zaawansowane ustawienia**.
    
        ![Zestaw usługa log analytics][11]

    2. Skopiuj wartości z **WorkspaceID** i **klucz podstawowy**.
  
       ![Skopiuj wartości][12]

    3. Wypełnij konfiguracji publicznej i prywatnej konfiguracji następującymi wartościami:
     
            $PublicConf = '{
                "workspaceId": "WorkspaceID value",
                "MultipleConnectistopOnons": true
            }' 
 
            $PrivateConf = '{
                "workspaceKey": "<Primary key value>”
            }' 

      - Podczas instalowania na maszynie Wirtualnej Windows:
        
             Set-AzureRmVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
       - Podczas instalowania na maszynie Wirtualnej systemu Linux:
        
             Set-AzureRmVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Aby uzyskać instrukcje dotyczące dołączania Centrum zabezpieczeń przy użyciu programu PowerShell, zobacz temat [automatyzacji dołączania do usługi Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

-   Aby zidentyfikować problemy z instalacją automatycznej aprowizacji, zobacz [monitorowanie problemów dotyczących kondycji agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Aby zidentyfikować wymagania sieciowe agenta monitorowania, zobacz [rozwiązywania problemów, wymagania sieciowe agenta monitorowania](security-center-troubleshooting-guide.md#mon-network-req).
-   Aby zidentyfikować problemy przy dołączaniu ręczne, zobacz [jak rozwiązywać problemy dotyczące przechodzenia do pakietu Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Aby zidentyfikować niemonitorowanych maszyn wirtualnych i problemy z komputerami, zobacz [niemonitorowanych maszyn wirtualnych i komputerów](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł pokazuje, jak zbieranie danych i automatycznej aprowizacji w usłudze Security Center działa. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
