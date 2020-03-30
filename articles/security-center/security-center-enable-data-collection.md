---
title: Zbieranie danych w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób instalowania agenta usługi Log Analytics i ustawiania obszaru roboczego usługi Log Analytics, w którym mają być przechowywane zebrane dane.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245501"
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w usłudze Azure Security Center
Usługa Security Center zbiera dane z maszyn wirtualnych platformy Azure (maszyn wirtualnych), zestawów skalowania maszyn wirtualnych, kontenerów IaaS i komputerów innych niż azure (w tym lokalnych) w celu monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą agenta usługi Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego do analizy. Przykładami takich danych są: typ i wersja systemu operacyjnego, dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwa komputera, adresy IP i zalogowany użytkownik. Agent analizy dzienników również kopiuje pliki zrzutu awaryjnego do obszaru roboczego.

Zbieranie danych jest wymagane, aby zapewnić wgląd w brakujące aktualizacje, nieprawidłowo skonfigurowane ustawienia zabezpieczeń systemu operacyjnego, stan ochrony punktu końcowego oraz ochronę zdrowia i zagrożenia. 

W tym artykule opisano sposób instalowania agenta usługi Log Analytics i ustawiania obszaru roboczego usługi Log Analytics, w którym mają być przechowywane zebrane dane. Obie operacje są wymagane, aby włączyć zbieranie danych. 

> [!NOTE]
> - Zbieranie danych jest potrzebne tylko dla zasobów obliczeniowych (maszyny wirtualne, zestawy skalowania maszyn wirtualnych, kontenery IaaS i komputery inne niż platformy Azure). Możesz korzystać z usługi Azure Security Center, nawet jeśli nie aprowizujesz agentów; Jednak będziesz mieć ograniczone zabezpieczenia i możliwości wymienione powyżej nie są obsługiwane.  
> - Aby uzyskać listę obsługiwanych platform, zobacz [Obsługiwane platformy w usłudze Azure Security Center](security-center-os-coverage.md).
> - Przechowywanie danych w usłudze Log Analytics, niezależnie od tego, czy używasz nowego, czy istniejącego obszaru roboczego, może naliczać dodatkowe opłaty za magazyn danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Włączanie automatycznego inicjowania obsługi administracyjnej agenta usługi Log Analytics<a name="auto-provision-mma"></a>

Aby zebrać dane z komputerów, należy mieć agenta analizy dziennika zainstalowany. Instalacja agenta może odbywać się automatycznie (zalecane) lub można zainstalować agenta ręcznie.  

>[!NOTE]
> Automatyczne inicjowanie obsługi administracyjnej jest domyślnie wyłączone. Aby skonfigurować program Security Center do domyślnego instalowania automatycznego inicjowania obsługi administracyjnej, należy ustawić ją **na Włączone**.
>

Gdy automatyczne inicjowanie obsługi administracyjnej jest włączone, usługa Security Center aprowuje agenta usługi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone. Zdecydowanie zaleca się automatyczne inicjowanie obsługi administracyjnej, ale dostępna jest również instalacja agenta ręcznego. [Dowiedz się, jak zainstalować rozszerzenie Agenta usługi Log Analytics](#manual-agent).



Aby włączyć automatyczne inicjowanie obsługi administracyjnej agenta usługi Log Analytics:
1. W menu głównym Centrum zabezpieczeń wybierz pozycję **Ustawienia & cennika**.
2. Kliknij odpowiednią subskrypcję

   ![Wybieranie subskrypcji][7]

3. Wybierz **pozycję Zbieranie danych**.
4. W obszarze **Automatyczne inicjowanie obsługi administracyjnej**wybierz pozycję **Włącz,** aby włączyć automatyczne inicjowanie obsługi administracyjnej.
5. Wybierz **pozycję Zapisz**.

   ![Włączanie automatycznej aprowizacji][1]

>[!NOTE]
> - Aby uzyskać instrukcje dotyczące obsługi administracyjnej istniejącej instalacji, zobacz [Automatyczne inicjowanie obsługi administracyjnej w przypadku istniejącej wcześniej instalacji agenta](#preexisting).
> - Aby uzyskać instrukcje dotyczące ręcznego inicjowania obsługi [administracyjnej, zobacz ręczne instalowanie rozszerzenia agenta usługi Log Analytics](#manual-agent).
> - Aby uzyskać instrukcje dotyczące wyłączania automatycznego inicjowania obsługi administracyjnej, zobacz [Wyłączanie automatycznego inicjowania obsługi administracyjnej](#offprovisioning).
> - Aby uzyskać instrukcje dotyczące dołączania usługi Security Center za pomocą programu PowerShell, zobacz [Automatyzacja dołączania usługi Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguracja obszaru roboczego
Dane zebrane przez usługę Security Center są przechowywane w obszarach roboczych usługi Log Analytics. Można wybrać, aby dane zbierane z maszyn wirtualnych platformy Azure przechowywane w obszarach roboczych utworzonych przez usługę Security Center lub w istniejącym obszarze roboczym, który został utworzony. 

Konfiguracja obszaru roboczego jest ustawiona na subskrypcję, a wiele subskrypcji może używać tego samego obszaru roboczego.

### <a name="using-a-workspace-created-by-security-center"></a>Korzystanie z obszaru roboczego utworzonego przez centrum zabezpieczeń

Centrum zabezpieczeń może automatycznie utworzyć domyślny obszar roboczy, w którym mają być przechowywane dane. 

Aby wybrać obszar roboczy utworzony przez Centrum zabezpieczeń:

1. W obszarze **Domyślna konfiguracja obszaru roboczego**wybierz pozycję Użyj obszarów roboczych utworzonych przez Centrum zabezpieczeń.
   ![Wybierz warstwę cenową][10] 

1. Kliknij przycisk **Zapisz**.<br>
    Usługa Security Center tworzy nową grupę zasobów i domyślny obszar roboczy w tej geolokalizacji i łączy agenta z tym obszarem roboczym. Konwencja nazewnictwa dla obszaru roboczego i grupy zasobów jest:<br>
   **Obszar roboczy: DefaultWorkspace-[subscription-ID]-[geo]<br> Grupa zasobów: DefaultResourceGroup-[geo]**

   Jeśli subskrypcja zawiera maszyny wirtualne z wielu geolokalizacji, usługa Security Center tworzy wiele obszarów roboczych. W celu zachowania reguł prywatności danych tworzonych jest wiele obszarów roboczych.
1. Usługa Security Center automatycznie włączy rozwiązanie Usługi Security Center w obszarze roboczym zgodnie z warstwą cenową ustawioną dla subskrypcji. 

> [!NOTE]
> Warstwa cenowa usługi Log Analytics obszarów roboczych utworzonych przez usługa Security Center nie ma wpływu na rozliczenia usługi Security Center. Rozliczenie usługi Security Center zawsze zależy od zasad zabezpieczeń usługi Security Center i rozwiązań zainstalowanych w obszarze roboczym. W przypadku warstwy Bezpłatna usługa Security Center w domyślnym obszarze roboczym udostępnia rozwiązanie *SecurityCenterFree*. W przypadku warstwy Standardowa usługa Security Center w domyślnym obszarze roboczym udostępnia rozwiązanie *Security*.
> Przechowywanie danych w usłudze Log Analytics może naliczać dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

Aby uzyskać więcej informacji na temat istniejących kont analizy dzienników, zobacz [Istniejąci klienci analizy dzienników](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Korzystanie z istniejącego obszaru roboczego

Jeśli masz już istniejący obszar roboczy usługi Log Analytics, możesz użyć tego samego obszaru roboczego.

Aby użyć istniejącego obszaru roboczego usługi Log Analytics, musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym.

> [!NOTE]
> Rozwiązania włączone w istniejącym obszarze roboczym zostaną zastosowane do maszyn wirtualnych platformy Azure, które są z nim połączone. W przypadku płatnych rozwiązań może to skutkować dodatkowymi opłatami. Aby uzyskać informacje dotyczące prywatności danych, upewnij się, że wybrany obszar roboczy znajduje się we właściwym regionie geograficznym.
> Przechowywanie danych w analizie dzienników może ponieść dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **Domyślna konfiguracja obszaru roboczego**wybierz pozycję **Użyj innego obszaru roboczego**.

   ![Wybieranie istniejącego obszaru roboczego][2]

2. Z menu rozwijanego wybierz obszar roboczy do przechowywania zebranych danych.

   > [!NOTE]
   > W menu rozwijanym dostępne są wszystkie obszary robocze we wszystkich subskrypcjach. Aby uzyskać więcej informacji, zobacz [wybór obszaru roboczego między subskrypcjami.](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Aby uzyskać dostęp do obszaru roboczego, musisz mieć uprawnienia.
   >
   >

3. Wybierz **pozycję Zapisz**.
4. Po wybraniu **opcji Zapisz**pojawi się pytanie, czy chcesz ponownie skonfigurować monitorowane maszyny wirtualne, które wcześniej były połączone z domyślnym obszarem roboczym.

   - Wybierz **opcję Nie,** jeśli chcesz, aby nowe ustawienia obszaru roboczego były stosowane tylko na nowych maszynach wirtualnych. Nowe ustawienia obszaru roboczego dotyczą tylko nowych instalacji agenta; nowo wykryte maszyny wirtualne, które nie mają zainstalowanego agenta usługi Log Analytics.
   - Wybierz **opcję Tak,** jeśli chcesz, aby nowe ustawienia obszaru roboczego były stosowane na wszystkich maszynach wirtualnych. Ponadto każda maszyna wirtualna połączona z utworzonym obszarem roboczym centrum zabezpieczeń jest ponownie połączona z nowym docelowym obszarem roboczym.

   > [!NOTE]
   > Wybranie opcji Tak nie może być usuwane obszarów roboczych utworzonych przez centrum zabezpieczeń, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja kończy się niepowodzeniem, jeśli obszar roboczy zostanie usunięty zbyt wcześnie.
   >
   >

   - Wybierz **pozycję Anuluj,** aby anulować operację.

     ![Wybieranie istniejącego obszaru roboczego][3]

5. Wybierz warstwę cenową dla żądanego obszaru roboczego, który ma być ustawiony jako agent usługi Log Analytics. <br>Aby użyć istniejącego obszaru roboczego, ustaw warstwę cenową dla obszaru roboczego. Spowoduje to zainstalowanie rozwiązania centrum zabezpieczeń w obszarze roboczym, jeśli nie jest jeszcze obecny.

    a.  W menu głównym Centrum zabezpieczeń wybierz pozycję **Ustawienia & cennika**.
     
    b.  Wybierz żądany obszar roboczy, w którym zamierzasz połączyć agenta.
        ![Wybierz obszar][7] roboczy c. Ustaw warstwę cenową.
        ![Wybierz warstwę cenową][9]
   
   >[!NOTE]
   >Jeśli obszar roboczy ma już włączone rozwiązanie **Security** lub **SecurityCenterFree,** ceny zostaną ustawione automatycznie. 

## <a name="cross-subscription-workspace-selection"></a>Wybór obszaru roboczego między subskrypcjami
Po wybraniu obszaru roboczego, w którym mają być przechowywane dane, dostępne są wszystkie obszary robocze we wszystkich subskrypcjach. Możliwość wybrania obszaru roboczego z innej subskrypcji pozwala na zbieranie danych z maszyn wirtualnych działających w różnych subskrypcjach i przechowywanie ich w wybranym obszarze roboczym. Ten wybór jest przydatny, jeśli w swojej organizacji używasz scentralizowanego obszaru roboczego i chcesz używać go do zbierania danych dotyczących zabezpieczeń. Aby uzyskać więcej informacji na temat zarządzania obszarami roboczymi, zobacz [Zarządzanie dostępem do obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Warstwa zbierania danych
Wybranie warstwy zbierania danych w usłudze Azure Security Center ma wpływ tylko na przechowywanie zdarzeń zabezpieczeń w obszarze roboczym usługi Log Analytics. Agent usługi Log Analytics będzie nadal zbierać i analizować zdarzenia zabezpieczeń wymagane dla ochrony przed zagrożeniami usługi Azure Security Center, niezależnie od warstwy zdarzeń zabezpieczeń, które chcesz przechowywać w obszarze roboczym usługi Log Analytics (jeśli istnieją). Jeśli postanowisz przechowywać zdarzenia zabezpieczeń w obszarze roboczym, spowoduje to aktywowanie badania, wyszukiwania i inspekcji tych zdarzeń w obszarze roboczym. 
> [!NOTE]
> Przechowywanie danych w analizie dzienników może ponieść dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Możesz wybrać odpowiednie zasady filtrowania dla subskrypcji i obszarów roboczych z czterech zestawów zdarzeń, które mają być przechowywane w obszarze roboczym: 

- **Brak** — wyłącz magazyn zdarzeń zabezpieczeń. Jest to ustawienie domyślne.
- **Minimalny** — mniejszy zestaw zdarzeń dla klientów, którzy chcą zminimalizować wolumin zdarzenia.
- **Wspólne** — jest to zestaw zdarzeń, który spełnia większość klientów i pozwala im pełną ścieżkę audytu.
- **Wszystkie zdarzenia** — dla klientów, którzy chcą się upewnić, że wszystkie zdarzenia są przechowywane.


> [!NOTE]
> Te zestawy zdarzeń zabezpieczeń są dostępne tylko w warstwie Standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
Te zestawy zostały zaprojektowane do rozwiązywania typowych scenariuszy. Przed jego wdrożeniem należy ocenić, który z nich odpowiada Twoim potrzebom.
>
>

Aby określić zdarzenia, które będą należeć do wspólnych **i** **minimalnych** zestawów zdarzeń, współpracowaliśmy z klientami i standardami branżowymi, aby dowiedzieć się o niefiltrowane częstotliwości każdego zdarzenia i ich użycia. W tym procesie użyliśmy następujących wytycznych:

- **Minimalny** — upewnij się, że ten zestaw obejmuje tylko zdarzenia, które mogą wskazywać na pomyślne naruszenie i ważne zdarzenia, które mają bardzo małą głośność. Na przykład ten zestaw zawiera dane logowania użytkownika, które powiodły się i nie powiodły się (identyfikatory zdarzeń 4624, 4625), ale nie zawiera wylogowania, co jest ważne dla inspekcji, ale nie ma znaczenia dla wykrywania i ma stosunkowo dużą objętość. Większość ilości danych tego zestawu to zdarzenia logowania i zdarzenie tworzenia procesu (identyfikator zdarzenia 4688).
- **Typowe** — podaj pełną ścieżkę inspekcji użytkownika w tym zestawie. Na przykład ten zestaw zawiera zarówno logowania użytkownika i wylogowania użytkownika (identyfikator zdarzenia 4634). Obejmujemy akcje inspekcji, takie jak zmiany w grupach zabezpieczeń, operacje protokołu Kerberos na kluczowym kontrolerze domeny i inne zdarzenia zalecane przez organizacje branżowe.

Zdarzenia, które mają bardzo małą objętość zostały uwzględnione w common set jako główną motywacją, aby wybrać go na wszystkie zdarzenia jest zmniejszenie głośności i nie odfiltrować określonych zdarzeń.

Oto pełny podział identyfikatorów zdarzeń zabezpieczeń i aplikacji locker dla każdego zestawu:

| Warstwa danych | Zebrane wskaźniki zdarzeń |
| --- | --- |
| Minimalny | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Wspólne | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Jeśli używasz obiektu zasad grupy (GPO), zaleca się włączenie zasad inspekcji Process Creation Event 4688 i *CommandLine* pole wewnątrz zdarzenia 4688. Aby uzyskać więcej informacji o zdarzeniu 4688 dotyczące tworzenia procesu, zobacz [Często zadawane pytania](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)centrum zabezpieczeń. Aby uzyskać więcej informacji na temat tych zasad inspekcji, zobacz [Zalecenia dotyczące zasad inspekcji](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Aby włączyć zbieranie danych dla [adaptacyjnych kontroli aplikacji,](security-center-adaptive-application.md)Centrum zabezpieczeń konfiguruje lokalną zasadę zasad ograniczeń oprogramowania w trybie inspekcji, aby umożliwić wszystkim aplikacjom. Spowoduje to, że funkcja AppLocker będzie generować zdarzenia, które są następnie zbierane i wykorzystywane przez centrum zabezpieczeń. Należy pamiętać, że ta zasada nie będzie skonfigurowana na żadnych komputerach, na których istnieje już skonfigurowana zasada ograniczeń oprogramowania. 
> - Aby zebrać identyfikator zdarzenia platformy filtrowania systemu Windows [5156,](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)należy włączyć [połączenie z platformą filtrowania inspekcji](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

Aby wybrać zasady filtrowania:
1. Na stronie **Zbieranie danych** wybierz zasady filtrowania w obszarze **Zdarzenia zabezpieczeń**.
2. Wybierz **pozycję Zapisz**.

   ![Wybieranie zasad filtrowania][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatyczne inicjowanie obsługi administracyjnej w przypadku istniejącej instalacji agenta<a name="preexisting"></a> 

Poniższe przypadki użycia określają, jak działa automatyczna inicjowanie administracyjne w przypadkach, gdy jest już zainstalowany agent lub rozszerzenie. 

- Agent analizy dziennika jest zainstalowany na komputerze, ale nie jako rozszerzenie (agent bezpośredni)<br>
Jeśli agent analizy dzienników jest zainstalowany bezpośrednio na maszynie Wirtualnej (nie jako rozszerzenie platformy Azure), Usługa Security Center zainstaluje rozszerzenie agenta usługi Log Analytics i może uaktualnić agenta analizy dzienników do najnowszej wersji.
Zainstalowany agent będzie nadal raportował do swoich już skonfigurowanych obszarów roboczych, a dodatkowo raportuje do obszaru roboczego skonfigurowanego w Centrum zabezpieczeń (multi-homing jest obsługiwany na komputerach z systemem Windows).
Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie domyślnym obszarem roboczym usługi Security Center), należy zainstalować na nim rozwiązanie "security/"securityFree", aby usługa Security Center rozpoczęła przetwarzanie zdarzeń z maszyn wirtualnych i komputerów raportujących do tego obszaru roboczego.<br>
<br>
W przypadku komputerów z systemem Linux agent multi-homing nie jest jeszcze obsługiwany — w związku z tym, jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne inicjowanie obsługi administracyjnej nie nastąpi i konfiguracja komputera nie zostanie zmieniona.
<br>
W przypadku istniejących komputerów w ramach subskrypcji wbudowanych w centrum zabezpieczeń przed 2019-03-17, gdy zostanie wykryty istniejący agent, rozszerzenie agenta usługi Log Analytics nie zostanie zainstalowane i nie zostanie to naruszone. W przypadku tych maszyn zobacz zalecenia "Rozwiązywanie problemów ze zdrowiem agenta monitorowania na komputerach", aby rozwiązać problemy z instalacją agenta na tych komputerach.

  
- Agent programu System Center Operations Manager jest zainstalowany na komputerze<br>
Centrum zabezpieczeń zainstaluje rozszerzenie agenta usługi Log Analytics obok istniejącego programu Operations Manager. Istniejący agent programu Operations Manager będzie normalnie raportować serwerowi programu Operations Manager. Należy zauważyć, że agent programu Operations Manager i agent analizy dzienników współużytkują wspólne biblioteki w czasie wykonywania, które zostaną zaktualizowane do najnowszej wersji podczas tego procesu.
Uwaga - Jeśli jest zainstalowany agent programu Operations Manager w wersji 2012, **nie** należy włączać automatycznej inicjowania obsługi administracyjnej.<br>

- Istniejące rozszerzenie maszyny Wirtualnej jest obecne<br>
    - Gdy agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko do jednego obszaru roboczego. Usługa Security Center nie zastępuje istniejących połączeń z obszarami roboczymi użytkowników. Usługa Security Center będzie przechowywać dane zabezpieczeń z maszyny Wirtualnej w już połączonym obszarze roboczym, pod warunkiem że zostało na nim zainstalowane rozwiązanie "security" lub "securityFree". Usługa Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.  
    - Aby sprawdzić, do którego obszaru roboczego wysyłane są dane istniejące rozszerzenie, uruchom test [sprawdzania poprawności łączności za pomocą usługi Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Alternatywnie można otworzyć obszary robocze usługi Log Analytics, wybrać obszar roboczy, wybrać maszynę wirtualną i przyjrzeć się połączeniu agenta usługi Log Analytics. 
    - Jeśli masz środowisko, w którym agent usługi Log Analytics jest zainstalowany na klienckich stacjach roboczych i raportowania do istniejącego obszaru roboczego usługi Log Analytics, przejrzyj listę [systemów operacyjnych obsługiwanych przez usługę Azure Security Center,](security-center-os-coverage.md) aby upewnić się, że system operacyjny jest obsługiwany. Aby uzyskać więcej informacji, zobacz [Istniejący klienci analizy dzienników](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Wyłączanie automatycznego inicjowania obsługi administracyjnej<a name="offprovisioning"></a>
Automatyczne inicjowanie obsługi administracyjnej z zasobów można wyłączyć w dowolnym momencie, wyłączając to ustawienie w zasadach zabezpieczeń. 


1. Wróć do menu głównego Centrum zabezpieczeń i wybierz zasady zabezpieczeń.
2. Kliknij **pozycję Edytuj ustawienia** w wierszu subskrypcji, dla której chcesz wyłączyć automatyczne inicjowanie obsługi administracyjnej.
3. W obszarze **Zasady zabezpieczeń —** zbieranie danych w obszarze Automatyczne **inicjowanie obsługi administracyjnej** wybierz pozycję **Wyłączone**.
4. Wybierz **pozycję Zapisz**.

   ![Wyłączanie automatycznego inicjowania obsługi administracyjnej][6]

Gdy automatyczne inicjowanie obsługi administracyjnej jest wyłączone (wyłączone), domyślna sekcja konfiguracji obszaru roboczego nie jest wyświetlana.

Jeśli wyłączysz automatyczne udostępnianie po jego wcześniejszym włączeniu:
-   Agenci nie będą aprowizacyjnie na nowych maszynach wirtualnych.
-   Usługa Security Center zatrzymuje zbieranie danych z domyślnego obszaru roboczego.
 
> [!NOTE]
>  Wyłączenie automatycznego inicjowania obsługi administracyjnej nie powoduje usunięcia agenta usługi Log Analytics z maszyn wirtualnych platformy Azure, w których agent został aprowizny. Aby uzyskać informacje na temat usuwania rozszerzenia usługi OMS, zobacz [Jak usunąć rozszerzenia systemu OMS zainstalowane przez program Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Ręczne inicjowanie obsługi administracyjnej agenta<a name="manual-agent"></a>
 
Istnieje kilka sposobów ręcznego instalowania agenta usługi Log Analytics Agent. Podczas ręcznej instalacji należy wyłączyć automatyczne inicjowanie obsługi administracyjnej.

### <a name="operations-management-suite-vm-extension-deployment"></a>Wdrożenie rozszerzenia VM pakietu Operations Management Suite 

Agent usługi Log Analytics można zainstalować ręcznie, dzięki czemu usługa Security Center może zbierać dane zabezpieczeń z maszyn wirtualnych oraz dostarczać rekomendacje i alerty.
1. Wybierz opcję Automatyczna inicjowania obsługi administracyjnej — WYŁ.
2. Utwórz obszar roboczy i ustaw warstwę cenową dla obszaru roboczego, który zamierzasz ustawić agenta usługi Log Analytics:

   a.  W menu głównym Centrum zabezpieczeń wybierz polecenie **Zasady zabezpieczeń**.
     
   b.  Wybierz obszar roboczy, w którym zamierzasz połączyć agenta. Upewnij się, że obszar roboczy znajduje się w tej samej subskrypcji, której używasz w u centrum zabezpieczeń i że masz uprawnienia do odczytu/zapisu w obszarze roboczym.
       ![Wybór obszaru roboczego][8]
3. Ustaw warstwę cenową.
   ![Wybierz warstwę cenową][9] 
   >[!NOTE]
   >Jeśli obszar roboczy ma już włączone rozwiązanie **Security** lub **SecurityCenterFree,** ceny zostaną ustawione automatycznie. 
   > 

4. Jeśli chcesz wdrożyć agentów na nowych maszynach wirtualnych przy użyciu szablonu Menedżera zasobów, zainstaluj rozszerzenie maszyny wirtualnej pakietu OMS:

   a.  [Instalowanie rozszerzenia maszyny wirtualnej pakietu OMS dla systemu Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalowanie rozszerzenia maszyny wirtualnej pakietu OMS dla systemu Linux](../virtual-machines/extensions/oms-linux.md)
5. Aby wdrożyć rozszerzenia na istniejących maszynach wirtualnych, postępuj zgodnie z instrukcjami w [zbieranie danych dotyczących maszyn wirtualnych platformy Azure.](../azure-monitor/learn/quick-collect-azurevm.md)

   > [!NOTE]
   > Sekcja **Zbieranie danych zdarzeń i wydajności jest** opcjonalna.
   >
6. Aby wdrożyć rozszerzenie za pomocą programu PowerShell, należy użyć następującego przykładu programu PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Przejdź do **usługi Log Analytics** i kliknij ustawienia **zaawansowane**.
    
      ![Ustawianie analizy dzienników][11]

   2. Skopiuj wartości z **workspaceID** i **klucza podstawowego**.
  
      ![Kopiowanie wartości][12]

   3. Zapełnij konfigurację publiczną i prywatną config z tymi wartościami:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Podczas instalowania na maszynie Wirtualnej systemu Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Podczas instalacji na maszynie wirtualnej z systemem Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Aby uzyskać instrukcje dotyczące dołączania usługi Security Center za pomocą programu PowerShell, zobacz [Automatyzacja dołączania usługi Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

-   Aby zidentyfikować problemy z instalacją automatyczną aprowizacyjną, zobacz [Monitorowanie problemów zdrowotnych agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Aby zidentyfikować wymagania sieciowe agenta monitorowania, zobacz [Rozwiązywanie problemów z wymaganiami sieciowymi agenta monitorowania](security-center-troubleshooting-guide.md#mon-network-req).
-   Aby zidentyfikować problemy z ręcznym dołączaniem, zobacz [Jak rozwiązywać problemy z dołączaniem do pakietu Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Aby zidentyfikować problemy z niemonitorowanymi maszynami wirtualnymi i komputerami:

    Maszyna wirtualna lub komputer nie jest monitorowany przez usługę Security Center, jeśli na komputerze nie jest uruchomione rozszerzenie agenta monitorowania firmy Microsoft. Na komputerze może być już zainstalowany agent lokalny, na przykład agent bezpośredni usługi OMS lub agent programu System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ te agenci nie są w pełni obsługiwane w centrum zabezpieczeń. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

    Aby uzyskać więcej informacji na temat przyczyn, dla których usługa Security Center nie może pomyślnie monitorować maszyn wirtualnych i komputerów zainicjowanych w celu automatycznego inicjowania obsługi administracyjnej, zobacz [Monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak działa zbieranie danych i automatyczne inicjowanie obsługi administracyjnej w u centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

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
