---
title: Zbieranie danych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób instalowania agenta Log Analytics i ustawiania obszaru roboczego Log Analytics, w którym będą przechowywane zebrane dane.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 71c30e0a86f67a2e2826859032144aa491c0cee1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597035"
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w usłudze Azure Security Center
Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz innych niż platformy Azure (w tym komputerów lokalnych) do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. Przykłady takich danych to: operacyjnych, typ i wersja, dzienniki systemu (Windows dzienniki zdarzeń), operacyjnego systemu uruchomione procesy, Nazwa maszyny, adresy IP i zalogowanego użytkownika. Agent Log Analytics kopiuje także pliki zrzutu awaryjnego do obszaru roboczego.

Zbieranie danych jest wymagane w celu zapewnienia wglądu w brakujące aktualizacje, nieprawidłowej konfiguracji ustawień zabezpieczeń systemu operacyjnego, stanu programu Endpoint Protection oraz wykrywania kondycji i zagrożeń. 

W tym artykule opisano sposób instalowania agenta Log Analytics i ustawiania obszaru roboczego Log Analytics, w którym będą przechowywane zebrane dane. Obie operacje są wymagane do włączenia zbierania danych. 

> [!NOTE]
> - Zbieranie danych jest wymagana tylko w przypadku zasobów obliczeniowych (maszyn wirtualnych, zestawów skalowania maszyn wirtualnych, kontenerów IaaS i komputerów spoza platformy Azure). Mogą korzystać z usługi Azure Security Center, nawet jeśli nie możesz aprowizować agentów; jednak mają ograniczone zabezpieczeń i możliwości wymienione powyżej są nieobsługiwane.  
> - Listę obsługiwanych platform można znaleźć [w temacie obsługiwane platformy w Azure Security Center](security-center-os-coverage.md).
> - Przechowywanie danych w Log Analytics, bez względu na to, czy używasz nowego, czy istniejącego obszaru roboczego, może nawiązać dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

## Włącz automatyczną obsługę administracyjną agenta Log Analytics<a name="auto-provision-mma"></a>

Aby zebrać dane z maszyn, należy zainstalować agenta Log Analytics. Instalację agenta można wykonać automatycznie (zalecane) lub ręcznie zainstalować agenta.  

>[!NOTE]
> Automatyczna aprowizacja jest domyślnie wyłączona. Aby ustawić Security Center, aby domyślnie zainstalować automatyczną alokację, ustaw ją na wartość **włączone**.
>

Gdy automatyczne Inicjowanie obsługi jest włączone, Security Center inicjuje agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Automatycznej aprowizacji jest zdecydowanie zalecane, ale jest również dostępna ręcznej instalacji agenta. [Dowiedz się, jak zainstalować rozszerzenie agenta log Analytics](#manual-agent).



Aby włączyć automatyczną obsługę administracyjną agenta Log Analytics:
1. W menu głównym Security Center wybierz pozycję **cennik & Ustawienia**.
2. Kliknij odpowiednią subskrypcję

   ![Wybieranie subskrypcji][7]

3. Wybierz pozycję **zbieranie danych**.
4. W obszarze **Automatyczne Inicjowanie obsługi**wybierz pozycję **włączone** , aby włączyć automatyczną obsługę administracyjną.
5. Wybierz pozycję **Zapisz**.

   ![Włączanie automatycznej aprowizacji][1]

>[!NOTE]
> - Aby uzyskać instrukcje dotyczące inicjowania obsługi istniejącej instalacji, zobacz [Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta](#preexisting).
> - Aby uzyskać instrukcje dotyczące ręcznego inicjowania obsługi, zobacz [Ręczne instalowanie rozszerzenia agenta log Analytics](#manual-agent).
> - Aby uzyskać instrukcje dotyczące wyłączania automatycznej aprowizacji, zobacz [wyłączanie automatycznej aprowizacji](#offprovisioning).
> - Aby uzyskać instrukcje na temat sposobu dołączania Security Center przy użyciu programu PowerShell, zobacz Automatyzowanie dołączania [Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguracja obszaru roboczego
Dane zbierane przez usługę Security Center są przechowywane w obszarach roboczych usługi Log Analytics. Możesz wybrać dane zebrane z maszyn wirtualnych platformy Azure, przechowywane w obszary robocze utworzone przez usługę Security Center lub istniejący obszar roboczy, który został utworzony. 

Konfiguracja obszaru roboczego jest ustawiony na subskrypcję, a wiele subskrypcji może używać tego samego obszaru roboczego.

### <a name="using-a-workspace-created-by-security-center"></a>Obszar roboczy utworzony przez usługę Security Center

Usługa Security center może automatycznie tworzyć domyślnego obszaru roboczego, w którym będą przechowywane dane. 

Aby wybrać obszar roboczy utworzony przez usługę Security Center:

1. W obszarze **Domyślna konfiguracja obszaru roboczego**wybierz pozycję Użyj obszarów roboczych utworzonych przez Centrum zabezpieczeń.
   ![wybierz warstwę cenową][10] 

1. Kliknij przycisk **Save** (Zapisz).<br>
    Usługa Security Center utworzy nowy zasób grupy i domyślny obszar roboczy w tym geolokalizacja i łączy agenta do tego obszaru roboczego. Konwencja nazewnictwa dla obszaru roboczego i grupy zasobów jest:<br>
   **Obszar roboczy: DefaultWorkspace-[Identyfikator subskrypcji]-[geograficzna]<br> grupę zasobów: DefaultResourceGroup-[Geo]**

   Jeśli subskrypcja zawiera maszyny wirtualne z wieloma geolocations, usługa Security Center tworzy wiele obszarów roboczych. Wiele obszarów roboczych są tworzone do obsługi zasad ochrony prywatności danych.
1. Usługa Security Center spowoduje automatyczne włączenie rozwiązania Security Center w obszarze roboczym na warstwy cenowej dla subskrypcji. 

> [!NOTE]
> Obszary robocze utworzone przez usługę Security Center warstwy cenowej w usłudze Log Analytics nie ma wpływu na rozliczenia w usłudze Security Center. Rozliczenie usługi Security Center zawsze zależy od zasad zabezpieczeń usługi Security Center i rozwiązań zainstalowanych w obszarze roboczym. W przypadku warstwy Bezpłatna usługa Security Center w domyślnym obszarze roboczym udostępnia rozwiązanie *SecurityCenterFree*. W przypadku warstwy Standardowa usługa Security Center w domyślnym obszarze roboczym udostępnia rozwiązanie *Security*.
> Przechowywanie danych w Log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

Aby uzyskać więcej informacji na temat istniejących kont usługi log Analytics, zobacz [istniejących klientów usługi log Analytics](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Przy użyciu istniejącego obszaru roboczego

Jeśli masz już istniejący obszar roboczy Log Analytics, możesz chcieć użyć tego samego obszaru roboczego.

Aby użyć istniejącego obszaru roboczego usługi Log Analytics, konieczne jest posiadanie Odczyt i zapis w obszarze roboczym.

> [!NOTE]
> Rozwiązania włączone w istniejącym obszarem roboczym zostaną zastosowane do maszyn wirtualnych platformy Azure, które są podłączone do niego. W przypadku płatnych rozwiązań może to spowodować naliczenie dodatkowych opłat. Dla kwestii związanych z prywatnością danych upewnij się, że wybrany obszar roboczy znajduje się w regionie geograficznym bezpośrednio.
> Przechowywanie danych w usłudze log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **Domyślna konfiguracja obszaru roboczego**wybierz pozycję **Użyj innego obszaru roboczego**.

   ![Wybierz istniejący obszar roboczy][2]

2. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

   > [!NOTE]
   > W ściągnąć menu dostępne są wszystkie obszary robocze dla wszystkich subskrypcji. Aby uzyskać więcej informacji, zobacz [obszar roboczy Cross Subscription](security-center-enable-data-collection.md#cross-subscription-workspace-selection) . Musi mieć uprawnienia dostępu do obszaru roboczego.
   >
   >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu opcji **Zapisz**zostanie wyświetlony monit z prośbą o ponowne skonfigurowanie monitorowanych maszyn wirtualnych, które zostały wcześniej połączone z domyślnym obszarem roboczym.

   - Wybierz pozycję **nie** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były stosowane tylko do nowych maszyn wirtualnych. Nowe ustawienia obszaru roboczego mają zastosowanie tylko do nowych instalacji agenta; nowo odnalezione maszyny wirtualne, na których nie zainstalowano agenta Log Analytics.
   - Wybierz pozycję **tak** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były stosowane do wszystkich maszyn wirtualnych. Ponadto każda maszyna wirtualna podłączone do usługi Security Center, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszary robocze utworzone przez usługę Security Center, aż wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, za wczesne usunięcie obszaru roboczego.
   >
   >

   - Wybierz pozycję **Anuluj** , aby anulować operację.

     ![Wybierz istniejący obszar roboczy][3]

5. Wybierz warstwę cenową dla żądanego obszaru roboczego, w której ma zostać ustawiony Agent Log Analytics. <br>Aby użyć istniejącego obszaru roboczego, ustawienie warstwy cenowej dla obszaru roboczego. Jeśli nie jest już obecny spowoduje to zainstalowanie rozwiązania Centrum zabezpieczeń w obszarze roboczym.

    a.  W menu głównym Security Center wybierz pozycję **cennik & Ustawienia**.
     
    b.  Wybierz żądany obszar roboczy, w którym ma zostać połączony Agent.
        ![wybierz obszar roboczy][7] c. Ustawienie warstwy cenowej.
        ![wybierz warstwę cenową][9]
   
   >[!NOTE]
   >Jeśli w obszarze roboczym jest już włączone rozwiązanie **Security** lub **SecurityCenterFree** , Cennik zostanie ustawiony automatycznie. 

## <a name="cross-subscription-workspace-selection"></a>Wybór obszaru roboczego między subskrypcjami
Po wybraniu obszaru roboczego, w którym do przechowywania danych, wszystkie obszary robocze dla wszystkich swoich subskrypcji są dostępne. Wybór między subskrypcjami obszaru roboczego umożliwia zbieranie danych z maszyn wirtualnych działających w różnych subskrypcjach i zapisz go w obszarze roboczym wybór. Zaznacz to pole wyboru jest przydatne, jeśli używasz scentralizowanych obszarów roboczych w Twojej organizacji i chcesz używać go do zbierania danych zabezpieczeń. Aby uzyskać więcej informacji na temat zarządzania obszarami roboczymi, zobacz [Zarządzanie dostępem do obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Warstwa kolekcji danych
Wybieranie warstwy zbierania danych w usłudze Azure Security Center mają wpływ tylko na magazyn zdarzeń zabezpieczeń w obszarze roboczym usługi Log Analytics. Agent Log Analytics będzie nadal zbierać i analizować zdarzenia zabezpieczeń wymagane do wykrywania zagrożeń Azure Security Center, niezależnie od tego, która warstwa zdarzeń zabezpieczeń została wybrana do przechowywania w obszarze roboczym Log Analytics (jeśli istnieje). Jeśli postanowisz przechowywać zdarzenia zabezpieczeń w obszarze roboczym, spowoduje to aktywowanie badania, wyszukiwania i inspekcji tych zdarzeń w obszarze roboczym. 
> [!NOTE]
> Przechowywanie danych w usłudze log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Możesz wybrać zasady dla subskrypcji i obszarów roboczych cztery rodzaje zdarzeń filtrowania prawej strony mają być przechowywane w obszarze roboczym: 

- **Brak** — Wyłącz magazyn zdarzeń zabezpieczeń. Jest to ustawienie domyślne.
- **Minimalny** — mniejszy zestaw zdarzeń dla klientów, którzy chcą zminimalizować wolumin zdarzeń.
- **Wspólne** — jest to zestaw zdarzeń, które spełniają większość klientów i umożliwiają im pełny dziennik inspekcji.
- **Wszystkie zdarzenia** — dla klientów, którzy chcą mieć pewność, że wszystkie zdarzenia są przechowywane.


> [!NOTE]
> Te zestawy zdarzenia zabezpieczeń są dostępne tylko dla warstwy standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
Te zestawy są przeznaczone dla typowych scenariuszy. Upewnij się ocenić, która opcja spełnia Twoje potrzeby, przed rozpoczęciem implementacji.
>
>

Aby określić zdarzenia, które będą należeć do **wspólnych** i **minimalnych** zestawów zdarzeń, firma Microsoft współpracuje z klientami i standardami branżowymi, aby dowiedzieć się więcej o niefiltrowanej częstotliwości poszczególnych zdarzeń i ich użyciu. Użyliśmy następujące wytyczne w ramach tego procesu:

- **Minimalny** — upewnij się, że ten zestaw obejmuje tylko zdarzenia, które mogą wskazywać na pomyślne naruszenie i ważne zdarzenia, które mają bardzo niski wolumin. Na przykład ten zestaw zawiera pomyślne i nieudane Logowanie użytkownika (identyfikatory zdarzeń 4624, 4625), ale nie zawiera wylogowania, co jest ważne w przypadku inspekcji, ale nie ma znaczenia dla wykrywania i ma stosunkowo dużą ilość danych. W większości ilość danych, ten zestaw jest zdarzenia logowania i procesu tworzenia zdarzeń (zdarzenie 4688 identyfikator).
- **Common** — Podaj pełny dziennik inspekcji użytkownika w tym zestawie. Na przykład ten zestaw zawiera identyfikatory logowania użytkowników i wylogowania użytkowników (Identyfikator zdarzenia 4634). Dołączamy inspekcji akcji, takich jak zmiany w grupie zabezpieczeń, operacji protokołu Kerberos kontrolera domeny kluczy i innych zdarzeń, które są zalecane przez organizacje z branży.

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
> - W przypadku używania obiektu zasady grupy (GPO) zaleca się włączenie zdarzenia dotyczącego tworzenia procesu zasad inspekcji 4688 i pola *wiersza polecenia* wewnątrz zdarzenia 4688. Aby uzyskać więcej informacji o zdarzeniu tworzenia procesu 4688, zobacz [często zadawane pytania](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)dotyczące Security Center. Aby uzyskać więcej informacji na temat tych zasad inspekcji, zobacz [zalecenia dotyczące zasad inspekcji](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Aby włączyć zbieranie danych na potrzeby [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. To spowoduje, że zasady ograniczeń oprogramowania do wygenerowania zdarzenia, które następnie są pobierane i wykorzystywane przez usługę Security Center. Należy zauważyć, że te zasady nie zostaną skonfigurowane na maszynach, na których jest już skonfigurowane zasady funkcji AppLocker. 
> - Aby zbierać zdarzenia platformy filtrowania systemu Windows o [identyfikatorze 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), należy włączyć funkcję [filtrowania inspekcji dla platformy](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (auditpol/Set/Subcategory: "Filtering Platform Connection"/Success: Enable)
>

Aby wybrać zasady filtrowania:
1. Na stronie **zbieranie danych** wybierz zasady filtrowania w obszarze **zdarzenia zabezpieczeń**.
2. Wybierz pozycję **Zapisz**.

   ![Wybierz zasady filtrowania][5]

### Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta<a name="preexisting"></a> 

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
    - Aby zobaczyć, w którym obszarze roboczym jest wysyłane dane rozszerzenie, Uruchom test w celu [sprawdzenia łączności z Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Alternatywnie możesz otworzyć obszary robocze Log Analytics, wybrać obszar roboczy, wybrać maszynę wirtualną i sprawdzić połączenie z agentem Log Analytics. 
    - Jeśli masz środowisko, w którym jest zainstalowany agent Log Analytics na stacjach roboczych klienta i raportowanie do istniejącego Log Analytics obszaru roboczego, przejrzyj listę [systemów operacyjnych obsługiwanych przez Azure Security Center](security-center-os-coverage.md) , aby upewnić się, że system operacyjny jest obsługiwany. Aby uzyskać więcej informacji, zobacz [istniejących klientów usługi log Analytics](./faq-azure-monitor-logs.md).
 
### Wyłącz automatyczne Inicjowanie obsługi<a name="offprovisioning"></a>
Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. 


1. Wróć do menu głównego usługi Security Center i wybierz zasady zabezpieczeń.
2. Kliknij pozycję **Edytuj ustawienia** w wierszu subskrypcji, dla której chcesz wyłączyć automatyczną obsługę administracyjną.
3. W bloku **zasady zabezpieczeń — zbieranie danych** w obszarze **autoinicjowanie obsługi** wybierz pozycję **wyłączone**.
4. Wybierz pozycję **Zapisz**.

   ![Wyłącz automatyczne udostępnianie][6]

Wyłączenie automatycznej aprowizacji (wyłączone), sekcja konfiguracji domyślnego obszaru roboczego nie jest wyświetlana.

Jeśli wyłączyć automatyczna obsługa po został wcześniej na:
-   Agenci nie będą udostępniane na nowych maszynach wirtualnych.
-   Usługa Security Center zatrzymuje zbieranie danych z domyślnego obszaru roboczego.
 
> [!NOTE]
>  Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Aby uzyskać informacje dotyczące usuwania rozszerzenia pakietu OMS, zobacz [Jak mogę usuwania rozszerzeń pakietu OMS zainstalowanych przez Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## Ręczna obsługa agentów<a name="manual-agent"></a>
 
Istnieje kilka sposobów ręcznego instalowania agenta Log Analytics. W przypadku ręcznej instalacji upewnij się, że wyłączenie automatycznej aprowizacji.

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite w maszynie Wirtualnej rozszerzenie wdrożenia 

Można ręcznie zainstalować agenta Log Analytics, aby Security Center mógł zbierać dane dotyczące zabezpieczeń z maszyn wirtualnych i udostępniać zalecenia i alerty.
1. Wybierz Auto zaopatrzenie — wyłączone.
2. Utwórz obszar roboczy i ustaw warstwę cenową dla obszaru roboczego, w którym ma zostać ustawiony Agent Log Analytics:

   a.  W menu głównym Security Center wybierz pozycję **zasady zabezpieczeń**.
     
   b.  Wybierz obszar roboczy, w którym trzeba połączyć agenta. Upewnij się, że obszar roboczy jest w tej samej subskrypcji, użyj w usłudze Security Center i czy masz uprawnienia odczytu/zapisu w obszarze roboczym.
       ![wybór obszaru roboczego][8]
3. Ustawienie warstwy cenowej.
   ![wybierz warstwę cenową][9] 
   >[!NOTE]
   >Jeśli w obszarze roboczym jest już włączone rozwiązanie **Security** lub **SecurityCenterFree** , Cennik zostanie ustawiony automatycznie. 
   > 

4. Jeśli chcesz wdrożyć agentów na nowych maszyn wirtualnych przy użyciu szablonu usługi Resource Manager, należy zainstalować rozszerzenia maszyny wirtualnej pakietu OMS:

   a.  [Zainstaluj rozszerzenie maszyny wirtualnej pakietu OMS dla systemu Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalowanie rozszerzenia maszyny wirtualnej pakietu OMS dla systemu Linux](../virtual-machines/extensions/oms-linux.md)
5. Aby wdrożyć rozszerzenia na istniejących maszynach wirtualnych, postępuj zgodnie z instrukcjami w [temacie zbieranie danych o platformie Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Sekcja **zbiera dane zdarzeń i wydajności,** które są opcjonalne.
   >
6. Aby wdrożyć rozszerzenie za pomocą programu PowerShell, skorzystaj z następującego przykładu programu PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Przejdź do **log Analytics** i kliknij pozycję **Ustawienia zaawansowane**.
    
      ![Zestaw usługa log analytics][11]

   2. Skopiuj wartości z **Identyfikator obszaru roboczego** i **klucza podstawowego**.
  
      ![Skopiuj wartości][12]

   3. Wypełnij konfiguracji publicznej i prywatnej konfiguracji następującymi wartościami:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Podczas instalowania na maszynie Wirtualnej Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Podczas instalowania na maszynie Wirtualnej systemu Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Aby uzyskać instrukcje na temat sposobu dołączania Security Center przy użyciu programu PowerShell, zobacz Automatyzowanie dołączania [Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

-   Aby zidentyfikować problemy z instalacją automatycznej obsługi administracyjnej, zobacz [monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Aby zidentyfikować wymagania sieciowe agenta monitorowania, zobacz [Rozwiązywanie problemów z wymaganiami sieci agenta monitorowania](security-center-troubleshooting-guide.md#mon-network-req).
-   Aby identyfikować ręczne problemy z dołączaniem, zobacz [Jak rozwiązywać problemy z dołączaniem do pakietu Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Aby zidentyfikować problemy dotyczące niemonitorowanych maszyn wirtualnych i komputerów:

    Maszynę Wirtualną lub komputer jest niemonitorowana przez usługę Security Center, jeśli komputer nie jest uruchomiona rozszerzenia Microsoft Monitoring Agent. Na komputerze może być już zainstalowany agent lokalny, na przykład Agent usługi OMS Direct lub Agent System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ ci agenci nie są w pełni obsługiwane w usłudze Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

    Aby uzyskać więcej informacji na temat przyczyn, Security Center nie może pomyślnie monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji, zobacz [monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Następne kroki
Ten artykuł pokazuje, jak zbieranie danych i automatycznej aprowizacji w usłudze Security Center działa. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Centrum zabezpieczeń Azure — często zadawane pytania](faq-general.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
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
