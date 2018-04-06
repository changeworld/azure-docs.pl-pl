---
title: Zbieranie danych w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: " Informacje o włączaniu zbierania danych w Centrum zabezpieczeń Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: terrylan
ms.openlocfilehash: 90a73545afa82276256a021588eaa594b95ee8da
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w Centrum zabezpieczeń Azure
Centrum zabezpieczeń zbiera dane z maszyn wirtualnych platformy Azure (maszyny wirtualne) i komputerów z systemem innym niż Azure monitorowanie luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy. Przykłady takich danych to typ systemu operacyjnego i jego wersja, dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwa maszyny, adresy IP, zalogowany użytkownik i identyfikator dzierżawy. Microsoft Monitoring Agent kopiuje pliki zrzutu awaryjnego do swojego obszaru roboczego.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Włącz automatyczne Inicjowanie obsługi programu Microsoft Monitoring Agent     
Automatyczne inicjowanie obsługi administracyjnej jest włączona, przepisy Centrum zabezpieczeń firmy Microsoft Monitoring Agent na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i nowe pliki, które są tworzone. Automatyczne udostępnianie zdecydowanie zaleca się, ale ręcznej instalacji agenta jest również dostępna. [Dowiedz się, jak zainstalować rozszerzenie programu Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów. Aby dowiedzieć się więcej, zobacz [Wyłącz automatyczne udostępnianie](security-center-enable-data-collection.md#disable-automatic-provisioning) w tym artykule. Migawki dysków maszyny Wirtualnej i kolekcji artefaktu są włączone, nawet jeśli jest wyłączona, automatyczne udostępnianie.
>
>

Aby włączyć automatyczną aprowizację programu Microsoft Monitoring Agent:
1. W menu głównym usługi Security Center wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję.
3. W obszarze **Zasady zabezpieczeń** wybierz pozycję **Zbieranie danych**.
4. W obszarze **dołączania**, wybierz pozycję **na** Aby włączyć automatyczne udostępnianie.
5. Wybierz pozycję **Zapisz**.

![Włączanie automatycznej aprowizacji][1]

## <a name="default-workspace-configuration"></a>Domyślna konfiguracja obszaru roboczego
Dane zebrane przez Centrum zabezpieczeń jest przechowywany w analizy dzienników obszarów roboczych.  Możesz zdecydować się na dane zbierane z maszyn wirtualnych platformy Azure, przechowywane w obszary robocze tworzone przez Centrum zabezpieczeń lub istniejący obszar roboczy utworzony.

Aby użyć istniejącego obszaru roboczego analizy dzienników:
- Obszar roboczy musi być skojarzony z wybranej subskrypcji Azure.
- Co najmniej użytkownik musi mieć uprawnienia odczytu dostępu do obszaru roboczego.

Aby wybrać istniejący obszar roboczy analizy dzienników:

1. W obszarze **zasady zabezpieczeń — zbieranie danych**, wybierz pozycję **Użyj innego obszaru roboczego**.

   ![Wybierz istniejący obszar roboczy][2]

2. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

> [!NOTE]
> W ściągania menu rozwijane są wyświetlane tylko obszarów roboczych, które mają dostęp do i znajdują się w ramach subskrypcji platformy Azure.
>
>

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu **zapisać**, użytkownik zostanie poproszony, jeśli chcesz monitorować ponownej konfiguracji maszyn wirtualnych.

   - Wybierz **nr** Jeśli chcesz, aby nowe ustawienia obszaru roboczego do zastosowania na tylko nowych maszyn wirtualnych. Nowe ustawienia obszaru roboczego dotyczą tylko nowe instalacje agentów; nowo wykryte maszyny wirtualne, które nie mają zainstalowany program Microsoft Monitoring Agent.
   - Wybierz **tak** Jeśli chcesz, aby nowe ustawienia obszaru roboczego do zastosowania na wszystkich maszynach wirtualnych. Ponadto każdej maszyny Wirtualnej podłączone do Centrum zabezpieczeń, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszarów roboczych, utworzonych przez Centrum zabezpieczeń, dopóki wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, zbyt wcześnie usuniętego obszaru roboczego.
   >
   >

   - Wybierz **anulować** Aby anulować operację.

   ![Wybierz istniejący obszar roboczy][3]

## <a name="data-collection-tier"></a>Warstwa danych kolekcji
Centrum zabezpieczeń może zmniejszyć ilość danych zdarzeń przy zachowaniu za mało zdarzeń do badania, inspekcji i wykrywanie zagrożeń. Można wybrać prawa filtrowania zasady dla subskrypcji i obszarów roboczych z czterech zestawów zdarzeń mają zostać zebrane przez agenta.

- **Wszystkie zdarzenia** — dla klientów, którzy chcą, aby upewnić się, że wszystkie zdarzenia są zbierane. Jest to wartość domyślna.
- **Typowe** — jest to zestaw zdarzeń spełniający większość klientów i umożliwia im dziennik inspekcji pełna.
- **Minimalny** — mniejszy zestaw zdarzeń dla klientów, którzy minimalizowania woluminu zdarzeń.
- **Brak** — Wyłącz zbieranie zdarzeń zabezpieczeń z dzienniki funkcji AppLocker i zabezpieczeń. Dla klientów, którzy wybierz tę opcję pulpity nawigacyjne ich zabezpieczeń ma tylko dzienniki zapory systemu Windows i oceny aktywnego, takich jak ochrony przed złośliwym kodem, linii bazowej i aktualizacji.

> [!NOTE]
> Te zestawy zaprojektowano w celu rozwiązania typowych scenariuszy. Upewnij się ocenić, która z nich potrzebom użytkownika przed jego wdrożeniem.
>
>

Aby określić zdarzenia, które będą należeć do **typowe** i **minimalnego** zestawów zdarzeń, możemy doświadczenie z klientów i standardy branżowe, aby dowiedzieć się więcej o częstotliwości niefiltrowane każdego zdarzenia i ich użycia. Poniższe wskazówki zostały użyte podczas tego procesu:

- **Minimalny** — upewnij się, że ten zestaw obejmuje tylko zdarzenia, które mogą wskazywać pomyślne naruszenia i ważnych wydarzeń, które mają bardzo niskim poziomie. Na przykład ten zestaw zawiera udane i nieudane logowania użytkownika (zdarzenie 4624 identyfikatorów 4625), ale nie zawiera on wylogowania, co jest ważne w przypadku inspekcji, ale nie ma istotnego znaczenia do wykrywania i ma stosunkowo dużej liczby. Większość ilość danych tego zestawu jest zdarzenia logowania i procesu tworzenia zdarzeń (event ID 4688).
- **Typowe** — Podaj dziennik inspekcji logowania, w tym zestawie. Na przykład ten zestaw zawiera identyfikatory logowania użytkownika i wylogowywania użytkowników (zdarzenie ID 4634). Firma Microsoft obejmują akcje, takie jak zmiany grupy zabezpieczeń, operacje Kerberos kontrolera domeny klucza i inne zdarzenia, które są zalecane przez organizacje z branży inspekcji.

Zdarzenia, które mają bardzo niskim poziomie zostały uwzględnione we wspólnym Ustaw jako głównym celem wybranie wszystkich zdarzeń jest zmniejszyć wolumin, a nie odfiltrowywania określonych zdarzeń.

Poniżej przedstawiono pełną podział zabezpieczeń i funkcji AppLocker identyfikatory zdarzeń dla każdego zestawu:

| Warstwa danych | Wskaźniki o zdarzeniach |
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
> Jeśli używasz obiektu zasad grupy (GPO), zaleca się włączenie zasady inspekcji procesu tworzenia zdarzeń 4688 i *CommandLine* pole wewnątrz zdarzenia 4688. Aby uzyskać więcej informacji na temat procesu tworzenia zdarzeń 4688 zobacz Centrum zabezpieczeń [— często zadawane pytania](security-center-faq.md#what-happens-when-data-collection-is-enabled). Aby uzyskać więcej informacji o tych zasady inspekcji, zobacz [zaleceń dotyczących zasad inspekcji](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
>
>

Aby wybrać zasady filtrowania:
1. Na **ustawienia i zasady zabezpieczeń** bloku, wybierz filtrowania zasad w obszarze **zdarzenia zabezpieczeń**.
2. Wybierz pozycję **Zapisz**.

   ![Wybierz zasady filtrowania][5]

## <a name="disable-automatic-provisioning"></a>Wyłącz automatyczne udostępnianie
Możesz wyłączyć automatyczne Inicjowanie obsługi administracyjnej z zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. Automatyczne udostępnianie zdecydowanie zalecane jest aby uzyskać alerty zabezpieczeń i zaleceń dotyczących aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu endpoint protection.

> [!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia programu Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, na których aprowizowano agenta.
>
>

1. Powróć do menu głównego Centrum zabezpieczeń i wybierz zasady zabezpieczeń.

   ![Wyłącz automatyczne udostępnianie][6]

2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. Na **zasady zabezpieczeń — zbieranie danych** bloku, w obszarze **dołączania** wybierz **poza** do Wyłącz automatyczne udostępnianie.
4. Wybierz pozycję **Zapisz**.  

## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano możesz sposób zbierania danych i automatyczne udostępnianie w Centrum zabezpieczeń działa. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md) — Dowiedz się, w jaki sposób zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w Centrum zabezpieczeń Azure](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w Centrum zabezpieczeń Azure](security-center-data-security.md) — Dowiedz się, jak dane są zarządzane i w Centrum zabezpieczeń.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
