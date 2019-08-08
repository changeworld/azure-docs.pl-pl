---
title: Zbieranie danych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak włączyć funkcję zbierania danych w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2019
ms.author: v-mohabe
ms.openlocfilehash: e87ea5f6d8a92f18fc1b289ebf9ffd1cc0326812
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845902"
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w usłudze Azure Security Center
Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz innych niż platformy Azure (w tym komputerów lokalnych) do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. Przykłady takich danych to: operacyjnych, typ i wersja, dzienniki systemu (Windows dzienniki zdarzeń), operacyjnego systemu uruchomione procesy, Nazwa maszyny, adresy IP i zalogowanego użytkownika. Agent Log Analytics kopiuje także pliki zrzutu awaryjnego do obszaru roboczego.

Zbieranie danych jest wymagany do zapewniają widoczność brakujących aktualizacji, nieprawidłowo skonfigurowane ustawienia zabezpieczeń systemu operacyjnego, włączenie ochrony punktu końcowego i wykrywania zagrożeń i kondycji. 

Ten artykuł zawiera wskazówki dotyczące sposobu instalowania agenta Log Analytics i ustawiania obszaru roboczego Log Analytics, w którym będą przechowywane zebrane dane. Obie operacje są wymagane do włączenia zbierania danych. 

> [!NOTE]
> - Zbieranie danych jest wymagana tylko w przypadku zasobów obliczeniowych (maszyn wirtualnych, zestawów skalowania maszyn wirtualnych, kontenerów IaaS i komputerów spoza platformy Azure). Mogą korzystać z usługi Azure Security Center, nawet jeśli nie możesz aprowizować agentów; jednak mają ograniczone zabezpieczeń i możliwości wymienione powyżej są nieobsługiwane.  
> - Aby uzyskać listę obsługiwanych platform, zobacz [obsługiwanych platform w usłudze Azure Security Center](security-center-os-coverage.md).
> - Przechowywanie danych w Log Analytics, bez względu na to, czy używasz nowego, czy istniejącego obszaru roboczego, może nawiązać dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, zobacz [stronę](https://azure.microsoft.com/pricing/details/security-center/)z cennikiem.

## Włącz automatyczną obsługę administracyjną agenta Log Analytics<a name="auto-provision-mma"></a>

Aby zebrać dane z maszyn, należy zainstalować agenta Log Analytics.  Instalację agenta można wykonać automatycznie (zalecane) lub ręcznie zainstalować agenta.  

>[!NOTE]
> Automatyczna aprowizacja jest domyślnie wyłączona. Security Center mogła zainstalować automatycznej aprowizacji domyślnie ustawia się je **na**.
>

Gdy automatyczne Inicjowanie obsługi jest włączone, Security Center inicjuje agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Automatycznej aprowizacji jest zdecydowanie zalecane, ale jest również dostępna ręcznej instalacji agenta. [Dowiedz się, jak zainstalować rozszerzenie agenta log Analytics](#manualagent).



Aby włączyć automatyczną obsługę administracyjną agenta Log Analytics:
1. W menu głównym Security Center wybierz pozycję **cennik & Ustawienia**.
2. Kliknij odpowiednią subskrypcję

   ![Wybieranie subskrypcji][7]

3. Wybierz pozycję **zbieranie danych**.
4. W obszarze **automatycznej aprowizacji**, wybierz opcję **na** Aby włączyć automatyczną aprowizację.
5. Wybierz pozycję **Zapisz**.

   ![Włączanie automatycznej aprowizacji][1]

>[!NOTE]
> - Aby uzyskać instrukcje, jak wykonać aprowizację wstępnie istniejącej instalacji, zobacz [automatycznej aprowizacji w przypadku istniejących instalacji agenta](#preexisting).
> - Aby uzyskać instrukcje dotyczące ręcznego inicjowania obsługi, zobacz [Ręczne instalowanie rozszerzenia agenta log Analytics](#manualagent).
> - Aby uzyskać instrukcje na wyłączenie automatycznej aprowizacji, zobacz [Wyłącz automatyczną aprowizację](#offprovisioning).
> - Aby uzyskać instrukcje dotyczące dołączania Centrum zabezpieczeń przy użyciu programu PowerShell, zobacz temat [automatyzacji dołączania do usługi Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguracja obszaru roboczego
Dane zbierane przez usługę Security Center są przechowywane w obszarach roboczych usługi Log Analytics.  Możesz wybrać dane zebrane z maszyn wirtualnych platformy Azure, przechowywane w obszary robocze utworzone przez usługę Security Center lub istniejący obszar roboczy, który został utworzony. 

Konfiguracja obszaru roboczego jest ustawiony na subskrypcję, a wiele subskrypcji może używać tego samego obszaru roboczego.

### <a name="using-a-workspace-created-by-security-center"></a>Obszar roboczy utworzony przez usługę Security Center

Usługa Security center może automatycznie tworzyć domyślnego obszaru roboczego, w którym będą przechowywane dane. 

Aby wybrać obszar roboczy utworzony przez usługę Security Center:

1. W obszarze **domyślną konfigurację obszaru zabezpieczeń**, zaznacz pole wyboru Użyj obszarów roboczych utworzonych przez usługę Security center.
   ![Wybierz warstwę cenową][10] 

1. Kliknij pozycję **Zapisz**.<br>
    Usługa Security Center utworzy nowy zasób grupy i domyślny obszar roboczy w tym geolokalizacja i łączy agenta do tego obszaru roboczego. Konwencja nazewnictwa dla obszaru roboczego i grupy zasobów jest:<br>
   **Obszary DefaultWorkspace-[Identyfikator subskrypcji]-[geograficzna]<br> Grupa zasobów: DefaultResourceGroup — [geograficzna]**

   Jeśli subskrypcja zawiera maszyny wirtualne z wieloma geolocations, usługa Security Center tworzy wiele obszarów roboczych. Wiele obszarów roboczych są tworzone do obsługi zasad ochrony prywatności danych.
1. Usługa Security Center spowoduje automatyczne włączenie rozwiązania Security Center w obszarze roboczym na warstwy cenowej dla subskrypcji. 

> [!NOTE]
> Obszary robocze utworzone przez usługę Security Center warstwy cenowej w usłudze Log Analytics nie ma wpływu na rozliczenia w usłudze Security Center. Rozliczanie usługi Security Center zawsze zależy od zasad zabezpieczeń usługa Security Center i rozwiązań zainstalowane w obszarze roboczym. Dla warstwy bezpłatnej, usługa Security Center umożliwia *SecurityCenterFree* rozwiązania w domyślnym obszarze roboczym. W przypadku warstwy Standard usługi Security Center umożliwia *zabezpieczeń* rozwiązania w domyślnym obszarze roboczym.
> Przechowywanie danych w Log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, zobacz [stronę](https://azure.microsoft.com/pricing/details/security-center/)z cennikiem.

Aby uzyskać więcej informacji na temat istniejących kont usługi log Analytics, zobacz [istniejących klientów usługi log Analytics](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Przy użyciu istniejącego obszaru roboczego

Jeśli masz już istniejący obszar roboczy usługi Log Analytics można użyć tego samego obszaru roboczego.

Aby użyć istniejącego obszaru roboczego usługi Log Analytics, konieczne jest posiadanie Odczyt i zapis w obszarze roboczym.

> [!NOTE]
> Rozwiązania włączone w istniejącym obszarem roboczym zostaną zastosowane do maszyn wirtualnych platformy Azure, które są podłączone do niego. W przypadku płatnych rozwiązań może to spowodować naliczenie dodatkowych opłat. Dla kwestii związanych z prywatnością danych upewnij się, że wybrany obszar roboczy znajduje się w regionie geograficznym bezpośrednio.
> Przechowywanie danych w usłudze log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, zobacz [stronę](https://azure.microsoft.com/pricing/details/security-center/)z cennikiem.

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

   - Wybierz **nie** Jeśli chcesz, aby nowe ustawienia obszaru roboczego, można zastosować tylko nowych maszyn wirtualnych. Nowe ustawienia obszaru roboczego mają zastosowanie tylko do nowych instalacji agenta; nowo odnalezione maszyny wirtualne, na których nie zainstalowano agenta Log Analytics.
   - Wybierz **tak** Jeśli chcesz, aby nowe ustawienia obszaru roboczego, aby zastosować na wszystkich maszynach wirtualnych. Ponadto każda maszyna wirtualna podłączone do usługi Security Center, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszary robocze utworzone przez usługę Security Center, aż wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, za wczesne usunięcie obszaru roboczego.
   >
   >

   - Wybierz **anulować** anulować operację.

     ![Wybierz istniejący obszar roboczy][3]

5. Wybierz warstwę cenową dla żądanego obszaru roboczego, w której ma zostać ustawiony Agent Log Analytics. <br>Aby użyć istniejącego obszaru roboczego, ustawienie warstwy cenowej dla obszaru roboczego. Jeśli nie jest już obecny spowoduje to zainstalowanie rozwiązania Centrum zabezpieczeń w obszarze roboczym.

    a.  W menu głównym Security Center wybierz pozycję **cennik & Ustawienia**.
     
    b.  Wybierz żądany obszar roboczy, w którym ma zostać połączony Agent.
        ![Wybierz obszar roboczy][7] c. Ustawienie warstwy cenowej.
        ![Wybierz warstwę cenową][9]
   
   >[!NOTE]
   >Jeśli obszar roboczy ma już **zabezpieczeń** lub **SecurityCenterFree** włączonego rozwiązania ceny zostaną ustawione automatycznie. 

## <a name="cross-subscription-workspace-selection"></a>Wybór obszaru roboczego między subskrypcjami
Po wybraniu obszaru roboczego, w którym do przechowywania danych, wszystkie obszary robocze dla wszystkich swoich subskrypcji są dostępne. Wybór między subskrypcjami obszaru roboczego umożliwia zbieranie danych z maszyn wirtualnych działających w różnych subskrypcjach i zapisz go w obszarze roboczym wybór. Zaznacz to pole wyboru jest przydatne, jeśli używasz scentralizowanych obszarów roboczych w Twojej organizacji i chcesz używać go do zbierania danych zabezpieczeń. Aby uzyskać więcej informacji na temat sposobu zarządzania obszarami roboczymi, zobacz [zarządzanie dostępem do obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Warstwa kolekcji danych
Wybieranie warstwy zbierania danych w usłudze Azure Security Center mają wpływ tylko na magazyn zdarzeń zabezpieczeń w obszarze roboczym usługi Log Analytics. Agent Log Analytics będzie nadal zbierać i analizować zdarzenia zabezpieczeń wymagane do wykrywania zagrożeń Azure Security Center, niezależnie od tego, która warstwa zdarzeń zabezpieczeń została wybrana do przechowywania w obszarze roboczym Log Analytics (jeśli istnieje). Wybieranie do przechowywania zdarzeń związanych z zabezpieczeniami w obszarze roboczym spowoduje włączenie analizy, wyszukiwania i inspekcji tych zdarzeń w obszarze roboczym. 
> [!NOTE]
> Przechowywanie danych w usłudze log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, zobacz [stronę](https://azure.microsoft.com/pricing/details/security-center/)z cennikiem.
> 
> Możesz wybrać zasady dla subskrypcji i obszarów roboczych cztery rodzaje zdarzeń filtrowania prawej strony mają być przechowywane w obszarze roboczym: 

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

- **Minimalny** — upewnij się, że ten zestaw obejmuje tylko te zdarzenia, które mogą wskazywać na naruszenie pomyślne i ważne wydarzenia, które mają bardzo niskim poziomie. Na przykład ten zestaw zawiera pomyślne i nieudane Logowanie użytkownika (identyfikatory zdarzeń 4624, 4625), ale nie zawiera wylogowania, co jest ważne w przypadku inspekcji, ale nie ma znaczenia dla wykrywania i ma stosunkowo dużą ilość danych. W większości ilość danych, ten zestaw jest zdarzenia logowania i procesu tworzenia zdarzeń (zdarzenie 4688 identyfikator).
- **Typowe** — Podaj użytkownika pełnego dziennika inspekcji, w tym zestawie. Na przykład ten zestaw zawiera identyfikatory logowania użytkowników i wylogowania użytkowników (Identyfikator zdarzenia 4634). Dołączamy inspekcji akcji, takich jak zmiany w grupie zabezpieczeń, operacji protokołu Kerberos kontrolera domeny kluczy i innych zdarzeń, które są zalecane przez organizacje z branży.

Zdarzenia, które mają bardzo małą liczbą zostały uwzględnione w typowych Ustaw jako główny motywacja wybrać za pośrednictwem wszystkich zdarzeń jest redukować ilość danych, a nie odfiltrować określonych zdarzeń.

Tutaj znajduje się pełna lista zabezpieczeń i AppLocker identyfikatory zdarzeń dla każdego zestawu:

| Warstwa danych | Wskaźniki zebranych zdarzeń |
| --- | --- |
| Minimalny | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Wspólne | 1, 413, 300, 324, 340, 403, 404, 410, 411, 412,, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
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
1. Na stronie **zbieranie danych** wybierz zasady filtrowania w obszarze **zdarzenia zabezpieczeń**.
2. Wybierz pozycję **Zapisz**.

   ![Wybierz zasady filtrowania][5]

### Automatyczna aprowizacja w przypadku istniejących instalacji agenta <a name="preexisting"></a> 

Określ następujące przypadki użycia, jak automatyczna aprowizacja działa w przypadku, gdy istnieje już agenta lub rozszerzenia zainstalowane. 

- Agent Log Analytics jest zainstalowany na komputerze, ale nie jako rozszerzenie (Agent bezpośredni)<br>
Jeśli Agent Log Analytics jest instalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center zainstaluje rozszerzenie agenta Log Analytics i może uaktualnić agenta Log Analytics do najnowszej wersji.
Zainstalowany agent będzie kontynuował raportowanie do już skonfigurowanych obszarów roboczych, a ponadto przeprowadzi raport do obszaru roboczego skonfigurowanego w Security Center (wiele multihostingu jest obsługiwana na maszynach z systemem Windows).
Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie Security Center domyślnym obszarem roboczym), należy zainstalować na nim rozwiązanie "Security/" securityFree "dla Security Center, aby rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów zgłaszanych do tego obszaru roboczego.<br>
<br>
W przypadku maszyn z systemem Linux Agent multihostingu nie jest jeszcze obsługiwany — w związku z tym jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne Inicjowanie obsługi nie zostanie wykonane i konfiguracja maszyny nie zostanie zmieniona.
<br>
W przypadku istniejących maszyn w ramach subskrypcji do Security Center przed 2019-03-17, gdy zostanie wykryty istniejący Agent, rozszerzenie agenta Log Analytics nie zostanie zainstalowane i nie będzie to miało takiego oddziaływania. W przypadku tych maszyn Zobacz zalecenia dotyczące rozwiązywania problemów z kondycją agenta monitorowania na swoich komputerach, aby rozwiązać problemy z instalacją agenta na tych komputerach.

  
- Agent System Center Operations Manager jest zainstalowany na maszynie<br>
Program Security Center zainstaluje rozszerzenie agenta Log Analytics obok istniejących Operations Manager. Istniejący Agent Operations Manager będzie kontynuował raportowanie do serwera Operations Manager w normalny sposób. Należy pamiętać, że Agent Operations Manager i Agent Log Analytics mają wspólne biblioteki uruchomieniowe, które zostaną zaktualizowane do najnowszej wersji w trakcie tego procesu.
Uwaga — Jeśli zainstalowano agenta Operations Manager w wersji 2012, **nie należy** włączać automatycznej aprowizacji.<br>

- Istniejące rozszerzenia maszyny Wirtualnej jest obecny<br>
    - Gdy Agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko jednego obszaru roboczego. Usługa Security Center nie zastępuje istniejące połączenia do obszarów roboczych użytkownika. Security Center będą przechowywać dane zabezpieczeń z maszyny wirtualnej w już podłączonym obszarze roboczym, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "securityFree". Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.  
    - Aby zobaczyć, na który obszar roboczy istniejące rozszerzenie wysyła dane, uruchom test, aby [sprawdzania poprawności łączności z usługą Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Alternatywnie możesz otworzyć obszary robocze Log Analytics, wybrać obszar roboczy, wybrać maszynę wirtualną i sprawdzić połączenie z agentem Log Analytics. 
    - Jeśli masz środowisko, w którym jest zainstalowany agent Log Analytics na stacjach roboczych klienta i raportowanie do istniejącego Log Analytics obszaru roboczego, przejrzyj listę [systemów operacyjnych obsługiwanych przez Azure Security Center](security-center-os-coverage.md) , aby upewnić się, że system operacyjny jest obsługiwane i zobacz [istniejących klientów usługi log Analytics](security-center-faq.md#existingloganalyticscust) , aby uzyskać więcej informacji.
 
### Wyłącz automatyczną aprowizację <a name="offprovisioning"></a>
Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. 


1. Wróć do menu głównego usługi Security Center i wybierz zasady zabezpieczeń.
2. Kliknij pozycję **Edytuj ustawienia** w wierszu subskrypcji, dla której chcesz wyłączyć automatyczną obsługę administracyjną.
3. Na **zasady zabezpieczeń — zbierania danych** bloku, w obszarze **automatycznej aprowizacji** wybierz **poza**.
4. Wybierz pozycję **Zapisz**.

   ![Wyłącz automatyczne udostępnianie][6]

Wyłączenie automatycznej aprowizacji (wyłączone), sekcja konfiguracji domyślnego obszaru roboczego nie jest wyświetlana.

Jeśli wyłączyć automatyczna obsługa po został wcześniej na:
-   Agenci nie będą udostępniane na nowych maszynach wirtualnych.
-   Usługa Security Center zatrzymuje zbieranie danych z domyślnego obszaru roboczego.
 
> [!NOTE]
>  Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Aby uzyskać informacje na temat usuwania rozszerzenie pakietu OMS, zobacz [jak usunąć zainstalowanych przez usługę Security Center rozszerzeń pakietu OMS](security-center-faq.md#remove-oms).
>
    
## Agent ręcznego inicjowania obsługi administracyjnej <a name="manualagent"></a>
 
Istnieje kilka sposobów ręcznego instalowania agenta Log Analytics. W przypadku ręcznej instalacji upewnij się, że wyłączenie automatycznej aprowizacji.

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite w maszynie Wirtualnej rozszerzenie wdrożenia 

Można ręcznie zainstalować agenta Log Analytics, aby Security Center mógł zbierać dane dotyczące zabezpieczeń z maszyn wirtualnych i udostępniać zalecenia i alerty.
1. Wybierz Auto zaopatrzenie — wyłączone.
2. Utwórz obszar roboczy i ustaw warstwę cenową dla obszaru roboczego, w którym ma zostać ustawiony Agent Log Analytics:

   a.  W menu głównym usługi Security Center wybierz **zasady zabezpieczeń**.
     
   b.  Wybierz obszar roboczy, w którym trzeba połączyć agenta. Upewnij się, że obszar roboczy jest w tej samej subskrypcji, użyj w usłudze Security Center i czy masz uprawnienia odczytu/zapisu w obszarze roboczym.
       ![Wybierz obszar roboczy][8]
3. Ustawienie warstwy cenowej.
   ![Wybierz warstwę cenową][9] 
   >[!NOTE]
   >Jeśli obszar roboczy ma już **zabezpieczeń** lub **SecurityCenterFree** włączonego rozwiązania ceny zostaną ustawione automatycznie. 
   > 

4. Jeśli chcesz wdrożyć agentów na nowych maszyn wirtualnych przy użyciu szablonu usługi Resource Manager, należy zainstalować rozszerzenia maszyny wirtualnej pakietu OMS:

   a.  [Zainstaluj rozszerzenie maszyny wirtualnej pakietu OMS dla Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Zainstaluj rozszerzenie maszyny wirtualnej pakietu OMS dla systemu Linux](../virtual-machines/extensions/oms-linux.md)
5. Aby wdrożyć rozszerzeń na istniejących maszynach wirtualnych, postępuj zgodnie z instrukcjami [zbierać dane o maszynach wirtualnych Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Sekcja **zbierania danych zdarzeń i wydajności** jest opcjonalne.
   >
6. Aby wdrożyć rozszerzenie za pomocą programu PowerShell, skorzystaj z następującego przykładu programu PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Przejdź do **usługi Log Analytics** i kliknij pozycję **Zaawansowane ustawienia**.
    
      ![Zestaw usługa log analytics][11]

   2. Skopiuj wartości z **WorkspaceID** i **klucz podstawowy**.
  
      ![Skopiuj wartości][12]

   3. Wypełnij konfiguracji publicznej i prywatnej konfiguracji następującymi wartościami:
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Podczas instalowania na maszynie Wirtualnej Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Podczas instalowania na maszynie Wirtualnej systemu Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

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
