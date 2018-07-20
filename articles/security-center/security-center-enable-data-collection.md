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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: d70eb1a329b2d1ba560aecbbb4132d2a8e2b7df1
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160131"
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w usłudze Azure Security Center
Usługa Security Center zbiera dane z maszyn wirtualnych (VM) i komputerów spoza platformy Azure do monitorowania pod kątem luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy. Przykłady takich danych to: typ systemu operacyjnego i wersji, operacyjne dzienniki systemu (Windows dzienniki zdarzeń), uruchomione procesy, Nazwa maszyny, adresy IP, zalogowany użytkowników, zdarzeń funkcji AppLocker i identyfikator dzierżawy. Program Microsoft Monitoring Agent kopiuje również pliki zrzutu awaryjnego do swojego obszaru roboczego.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Włącz automatyczną aprowizację programu Microsoft Monitoring Agent     
Automatyczna aprowizacja jest domyślnie wyłączona. Po włączeniu automatycznej aprowizacji Security Center aprowizuje program Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i wszelkie nowe, które są tworzone. Automatycznej aprowizacji jest zdecydowanie zalecane, ale jest również dostępna ręcznej instalacji agenta. [Dowiedz się, jak można zainstalować rozszerzenia Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> - Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów. Aby dowiedzieć się więcej, zobacz [wyłączyć automatyczną aprowizację](security-center-enable-data-collection.md#disable-automatic-provisioning) w tym artykule. Kolekcja artefaktów i migawki dysków maszyny Wirtualnej są włączone, nawet jeśli automatycznej aprowizacji jest wyłączone.
> - Aby włączyć zbieranie danych dla [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), usługa Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. To spowoduje, że zasady ograniczeń oprogramowania do wygenerowania zdarzenia, które następnie są pobierane i wykorzystywane przez usługę Security Center. Należy zauważyć, że te zasady nie zostaną skonfigurowane na maszynach, na których jest już skonfigurowane zasady funkcji AppLocker. 
>

Aby włączyć automatyczną aprowizację programu Microsoft Monitoring Agent:
1. W menu głównym usługi Security Center wybierz **zasady zabezpieczeń**.
2. Wybierz subskrypcję.

  ![Wybieranie subskrypcji][7]

3. W obszarze **Zasady zabezpieczeń** wybierz pozycję **Zbieranie danych**.
4. W obszarze **automatycznej aprowizacji**, wybierz opcję **na** Aby włączyć automatyczną aprowizację.
5. Wybierz pozycję **Zapisz**.

  ![Włączanie automatycznej aprowizacji][1]

## <a name="default-workspace-configuration"></a>Domyślna konfiguracja obszaru roboczego
Dane zbierane przez usługę Security Center są przechowywane w obszarach roboczych usługi Log Analytics.  Możesz zdecydować się na dane zbierane z maszyn wirtualnych platformy Azure, przechowywane w obszary robocze utworzone przez usługę Security Center lub istniejący obszar roboczy, który został utworzony.

Aby użyć istniejącego obszaru roboczego usługi Log Analytics:
- Obszar roboczy musi być skojarzony z wybranej subskrypcji platformy Azure.
- Jako minimum użytkownik musi mieć uprawnienia odczytu do dostępu do obszaru roboczego.

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **domyślną konfigurację obszaru zabezpieczeń**, wybierz opcję **Użyj innego obszaru roboczego**.

   ![Wybierz istniejący obszar roboczy][2]

2. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

  > [!NOTE]
  > W ściągnąć menu dostępne są wszystkie obszary robocze dla wszystkich subskrypcji. Zobacz [cross wybór obszaru roboczego subskrypcji](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Aby uzyskać więcej informacji.
  >
  >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu **Zapisz**, konieczne będzie podanie, jeśli chcesz ponownie skonfigurować monitorowane maszyny wirtualne.

   - Wybierz **nie** Jeśli chcesz, aby nowe ustawienia obszaru roboczego, można zastosować tylko nowych maszyn wirtualnych. Nowe ustawienia obszaru roboczego dotyczą tylko nowe instalacje agentów; odnalezione maszyny wirtualne, które nie mają zainstalowany program Microsoft Monitoring Agent.
   - Wybierz **tak** Jeśli chcesz, aby nowe ustawienia obszaru roboczego, aby zastosować na wszystkich maszynach wirtualnych. Ponadto każda maszyna wirtualna podłączone do usługi Security Center, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszary robocze utworzone przez usługę Security Center, aż wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, za wczesne usunięcie obszaru roboczego.
   >
   >

   - Wybierz **anulować** anulować operację.

     ![Wybierz istniejący obszar roboczy][3]

## <a name="cross-subscription-workspace-selection"></a>Obejmujące wiele subskrypcji, wybór obszaru roboczego
Po wybraniu obszaru roboczego do przechowywania danych, wszystkie obszary robocze dla wszystkich subskrypcji są dostępne. Między subskrypcjami wybór obszaru roboczego umożliwia zbieranie danych z maszyn wirtualnych działających w różnych subskrypcjach i zapisz go w obszarze roboczym wybór. Ta funkcja działa w przypadku obu maszyn wirtualnych z systemem Linux lub Windows.

> [!NOTE]
> Między subskrypcjami wybór obszaru roboczego jest częścią usługi Azure Security Center w warstwie bezpłatna. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>
>

## <a name="data-collection-tier"></a>Warstwa kolekcji danych
Usługa Security Center można zmniejszyć liczby zdarzeń przy zachowaniu wystarczającej liczby zdarzeń do badania, inspekcji i wykrywania zagrożeń. Możesz wybrać zasady dla subskrypcji i obszarów roboczych cztery rodzaje zdarzeń filtrowania prawej strony mają zostać zebrane przez agenta.

- **Wszystkie zdarzenia** — dla klientów, którzy chcą, aby upewnić się, że wszystkie zdarzenia są zbierane. Jest to opcja domyślna.
- **Typowe** — jest to zestaw zdarzeń, który spełnia większość klientów i umożliwia im pełnego dziennika inspekcji.
- **Minimalny** — mniejszy zestaw zdarzeń, które klienci, którzy chcą, aby zminimalizować wolumin zdarzeń.
- **Brak** — Wyłącz zbieranie zdarzeń zabezpieczeń z zabezpieczeniami i dzienniki AppLocker. W przypadku klientów, którzy wybierz tę opcję ich pulpitami nawigacyjnymi zabezpieczeń ma tylko dzienniki zapory Windows i ocen proaktywnych np. ochrony przed złośliwym oprogramowaniem, linii bazowej i aktualizacji.

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
>

Aby wybrać zasady filtrowania:
1. Na **zasady zabezpieczeń zbierania danych** bloku wybierz zasady filtrowania w obszarze **zdarzeń związanych z zabezpieczeniami**.
2. Wybierz pozycję **Zapisz**.

   ![Wybierz zasady filtrowania][5]

## <a name="disable-automatic-provisioning"></a>Wyłącz automatyczne udostępnianie
Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. Automatyczna aprowizacja zdecydowanie zalecane jest aby uzyskać alerty zabezpieczeń i zalecenia dotyczące aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu endpoint protection.

> [!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia programu Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, na których aprowizowano agenta.
>
>

1. Wróć do menu głównego usługi Security Center i wybierz zasady zabezpieczeń.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. Na **zasady zabezpieczeń — zbierania danych** bloku, w obszarze **automatycznej aprowizacji** wybierz **poza**.
4. Wybierz pozycję **Zapisz**.

  ![Wyłącz automatyczne udostępnianie][6]

Wyłączenie automatycznej aprowizacji (wyłączone), sekcja konfiguracji domyślnego obszaru roboczego nie są wyświetlane.

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł pokazuje, jak zbieranie danych i automatycznej aprowizacji w usłudze Security Center działa. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w usłudze Azure Security Center](security-center-data-security.md) — Dowiedz się, jak jest zarządzane i chronione są dane w usłudze Security Center.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
