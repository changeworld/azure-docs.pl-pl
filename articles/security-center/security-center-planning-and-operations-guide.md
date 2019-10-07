---
title: Security Center Przewodnik planowania i obsługi | Microsoft Docs
description: Ten dokument ułatwia planowanie przed przyjęciem Azure Security Center i zagadnień dotyczących codziennych operacji.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: abcd6dc8c50b819dd02347b938602af7f2152d04
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996618"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Przewodnik planowania i obsługi Azure Security Center
Ten przewodnik jest przeznaczony dla specjalistów IT, architektów IT, analityków zabezpieczeń informacji i administratorów chmury do korzystania z Azure Security Center.


## <a name="planning-guide"></a>Przewodnik planowania
W tym przewodniku opisano zadania, które można wykonać, aby zoptymalizować korzystanie z Security Center na podstawie wymagań dotyczących zabezpieczeń i modelu zarządzania chmurą w organizacji. Aby w pełni korzystać z Security Center, ważne jest, aby zrozumieć, w jaki sposób różne osoby lub zespoły w organizacji korzystają z usługi, aby zaspokoić potrzeby bezpiecznego projektowania i działania, monitorowania, zarządzania i reagowania na zdarzenia. Kluczowe obszary, które należy wziąć pod uwagę podczas planowania użycia Security Center są następujące:

* Role zabezpieczeń i kontrola dostępu
* Zasady zabezpieczeń i zalecenia
* Zbieranie i przechowywanie danych
* Bieżące zasoby spoza platformy Azure
* Bieżące monitorowanie zabezpieczeń
* Odpowiedź na zdarzenie

W następnej sekcji dowiesz się, jak planować każdy z tych obszarów i zastosować te zalecenia w zależności od wymagań.


> [!NOTE]
> Przeczytaj [Azure Security Center często zadawanych pytań (FAQ)](security-center-faq.md) , aby zapoznać się z listą typowych pytań, które mogą być również przydatne w fazie projektowania i planowania.
>

## <a name="security-roles-and-access-controls"></a>Role zabezpieczeń i kontrola dostępu
W zależności od wielkości i struktury organizacji wiele osób i zespołów może używać Security Center do wykonywania różnych zadań związanych z zabezpieczeniami. Na poniższym diagramie przedstawiono przykład fikcyjnej osób i ich odpowiednich ról i obowiązków związanych z zabezpieczeniami:

![Pełnione](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Security Center umożliwia tym osobom spełnienie tych różnych obowiązków. Na przykład:

**Jan (właściciel obciążenia)**

* Zarządzanie obciążeniem chmury i powiązanymi zasobami
* Odpowiedzialność za wdrożenie i utrzymanie ochrony zgodnie z zasadami zabezpieczeń firmy

**Aneta (CISO/CIO)**

* Odpowiedzialny za wszystkie aspekty zabezpieczeń firmy
* Chce zrozumieć stan zabezpieczeń firmy w ramach obciążeń w chmurze
* Musi być informowany o najważniejszych atakach i zagrożeniach

**David (zabezpieczenia INFORMATYCZNe)**

* Ustawia zasady zabezpieczeń firmy w celu zapewnienia, że stosowane są odpowiednie zabezpieczenia
* Monitoruje zgodność z zasadami
* Generuje raporty dotyczące liderów lub audytorów

**Jan (operacje zabezpieczeń)**

* Monitoruje i reaguje na alerty zabezpieczeń 24/7
* Przekazuje do właściciela obciążenia w chmurze lub analityka zabezpieczeń IT

**Sam (analityk zabezpieczeń)**

* Zbadaj ataki
* Współpraca z właścicielem obciążenia chmury w celu zastosowania korygowania

Security Center korzysta z [Access Control opartych na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), które udostępnia [wbudowane role](../role-based-access-control/built-in-roles.md) , które można przypisać do użytkowników, grup i usług na platformie Azure. Gdy użytkownik otworzy Security Center, zobaczy tylko informacje związane z zasobami, do których mają dostęp. Oznacza to, że użytkownik ma przypisaną rolę właściciela, współautora lub czytelnika do subskrypcji lub grupy zasobów, do której należy zasób. Oprócz tych ról istnieją dwie konkretne role Security Center:

- **Czytelnik zabezpieczeń**: użytkownik, który należy do tej roli, może wyświetlać tylko Security Center konfiguracje, które obejmują zalecenia, alerty, zasady i kondycję, ale nie może wprowadzać zmian.
- **Administrator zabezpieczeń**: taki sam jak czytelnik zabezpieczeń, ale może również zaktualizować zasady zabezpieczeń, odrzucić rekomendacje i alerty.

Opisane powyżej role Security Center nie mają dostępu do innych obszarów usługi platformy Azure, takich jak Storage, Web & Mobile lub Internet rzeczy.

Przy użyciu osób wyjaśnionego na poprzednim diagramie wymagana jest następująca kontrola RBAC:

**Jan (właściciel obciążenia)**

* Właściciel/współautor grupy zasobów

**David (zabezpieczenia INFORMATYCZNe)**

* Właściciel/współautor subskrypcji lub administrator zabezpieczeń

**Jan (operacje zabezpieczeń)**

* Czytelnik subskrypcji lub czytelnik zabezpieczeń, aby wyświetlić alerty
* Właściciel/współautor subskrypcji lub administrator zabezpieczeń wymagany do odrzucania alertów

**Sam (analityk zabezpieczeń)**

* Czytelnik subskrypcji przeglądający alerty
* Właściciel/współautor subskrypcji wymagany do odrzucania alertów
* Może być wymagany dostęp do obszaru roboczego

Niektóre inne ważne informacje, które należy wziąć pod uwagę:

* Tylko właściciele lub Współautorzy subskrypcji i administratorzy zabezpieczeń mogą edytować zasady zabezpieczeń.
* Tylko właściciele i współautorzy subskrypcji i grupy zasobów mogą stosować zalecenia dotyczące zabezpieczeń dla zasobu.

Podczas planowania kontroli dostępu przy użyciu RBAC dla Security Center należy pamiętać o tym, kto w organizacji będzie używać Security Center. Ponadto typy zadań, które będą wykonywane, a następnie odpowiednio skonfigurują RBAC.

> [!NOTE]
> Zalecamy przypisanie najmniej niewystarczającej roli potrzebnej użytkownikom do wykonywania swoich zadań. Na przykład użytkownicy, którzy muszą tylko wyświetlić informacje o stanie zabezpieczeń zasobów, ale nie podejmują działania, na przykład stosując zalecenia lub edycję zasad, powinni mieć przypisaną rolę czytelnik.
>
>

## <a name="security-policies-and-recommendations"></a>Zasady zabezpieczeń i zalecenia
Zasady zabezpieczeń definiują żądaną konfigurację obciążeń i zapewniają zgodność z wymaganiami firmy lub przepisami bezpieczeństwa. W Security Center można zdefiniować zasady dla subskrypcji platformy Azure, które mogą być dostosowane do typu obciążenia lub czułości danych.

Zasady Security Center zawierają następujące składniki:
- [Zbieranie danych](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): ustawienia aprowizacji agentów i zbierania danych.
- [Zasady zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies): [Azure Policy](../governance/policy/overview.md) , który określa, które kontrolki są monitorowane i zalecane przez Security Center, lub użyj Azure Policy do tworzenia nowych definicji, definiowania dodatkowych zasad i przypisywania zasad w grupach zarządzania.
- [Powiadomienia e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): kontakty zabezpieczeń i ustawienia powiadomień.
- [Warstwa cenowa](https://docs.microsoft.com/azure/security-center/security-center-pricing): wybór cen bezpłatnych lub standardowych, które określają, które funkcje Security Center są dostępne dla zasobów w zakresie (można je określić dla subskrypcji, grup zasobów i obszarów roboczych).

> [!NOTE]
> Określenie kontaktu z zabezpieczeniami zapewni, że platforma Azure będzie mogła skontaktować się z odpowiednią osobą w organizacji w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem. Przeczytaj [szczegóły dotyczące kontaktu z zabezpieczeniami w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) , aby uzyskać więcej informacji na temat włączania tego zalecenia.

### <a name="security-policies-definitions-and-recommendations"></a>Definicje i zalecenia dotyczące zasad zabezpieczeń
Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej subskrypcji platformy Azure. Zasady można edytować w Security Center lub użyć Azure Policy do tworzenia nowych definicji, definiowania dodatkowych zasad i przypisywania zasad w ramach Grupy zarządzania (które mogą reprezentować całą organizację, jednostkę biznesową itp.) oraz monitorować zgodność z te zasady w tych zakresach.

Przed skonfigurowaniem zasad zabezpieczeń należy przejrzeć poszczególne [zalecenia dotyczące zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-recommendations)i określić, czy te zasady są odpowiednie dla różnych subskrypcji i grup zasobów. Ważne jest również, aby zrozumieć, jakie działania powinny zostać podjęte w celu uwzględnienia zaleceń dotyczących zabezpieczeń oraz kto w organizacji będzie odpowiedzialny za monitorowanie pod kątem nowych zaleceń i podejmowanie wymaganych kroków.

## <a name="data-collection-and-storage"></a>Zbieranie i przechowywanie danych
Azure Security Center używa Microsoft Monitoring Agent — jest to ten sam Agent, który jest używany przez usługę Azure Monitor do zbierania danych zabezpieczeń z maszyn wirtualnych. [Dane zbierane](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) z tego agenta będą przechowywane w obszarach roboczych log Analytics.

### <a name="agent"></a>Agent

Jeśli w zasadach zabezpieczeń włączono automatyczną obsługę administracyjną, Microsoft Monitoring Agent (dla [systemu Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) lub [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) są instalowane na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i tworzonych nowych. Jeśli na maszynie wirtualnej lub komputerze jest już zainstalowany Microsoft Monitoring Agent, Azure Security Center będzie korzystać z aktualnie zainstalowanego agenta. Proces agenta został zaprojektowany jako nieinwazyjny i ma minimalny wpływ na wydajność maszyny wirtualnej.

Microsoft Monitoring Agent dla systemu Windows wymaga użycia portu TCP 443. Dodatkowe szczegóły można znaleźć w artykule dotyczącym [rozwiązywania problemów](security-center-troubleshooting-guide.md) .

Jeśli w pewnym momencie chcesz wyłączyć zbieranie danych, możesz je wyłączyć w zasadach zabezpieczeń. Jednak ponieważ Microsoft Monitoring Agent mogą być używane przez inne usługi zarządzania i monitorowania platformy Azure, Agent nie zostanie odinstalowany automatycznie po wyłączeniu zbierania danych w programie Security Center. W razie konieczności można ręcznie odinstalować agenta.

> [!NOTE]
> Aby znaleźć listę obsługiwanych maszyn wirtualnych, Przeczytaj [Azure Security Center często zadawanych pytań](security-center-faq.md).
>

### <a name="workspace"></a>Obszar roboczy

Obszar roboczy jest zasobem platformy Azure, który służy jako kontener dla danych. Ty lub inni członkowie organizacji mogą używać wielu obszarów roboczych do zarządzania różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

Dane zbierane z Microsoft Monitoring Agent (w imieniu Azure Security Center) będą przechowywane w istniejących obszarach roboczych Log Analytics skojarzonych z subskrypcją platformy Azure lub w nowych obszarach roboczych, uwzględniając lokalizację geograficzną maszyny wirtualnej.

W Azure Portal można przeglądać, aby wyświetlić listę obszarów roboczych Log Analytics, w tym wszystkich utworzonych przez Azure Security Center. Dla nowych obszarów roboczych zostanie utworzona powiązana Grupa zasobów. Obie te konwencją nazewnictwa:

* Obszar roboczy: *DefaultWorkspace-[Identyfikator subskrypcji]-[lokalizacja geograficzna]*
* Grupa zasobów: *DefaultResourceGroup-[Geo]*

W przypadku obszarów roboczych utworzonych przez Azure Security Center dane są przechowywane przez 30 dni. W przypadku istniejących obszarów roboczych przechowywanie zależy od warstwy cenowej obszaru roboczego. Jeśli chcesz, możesz również użyć istniejącego obszaru roboczego.

> [!NOTE]
> Firma Microsoft podejmuje mocne zobowiązania w zakresie ochrony prywatności i bezpieczeństwa tych danych. Firma Microsoft stosuje się do rygorystycznych wskazówek dotyczących zgodności i zabezpieczeń — od kodowania do obsługi usługi. Aby uzyskać więcej informacji na temat obsługi danych i prywatności, Przeczytaj [Azure Security Center zabezpieczenia danych](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Dołączanie zasobów nienależących do platformy Azure

Security Center może monitorować stan zabezpieczeń na komputerach spoza platformy Azure, ale musisz najpierw dołączyć te zasoby. Aby uzyskać więcej informacji na temat sposobu dołączania zasobów niezwiązanych z platformą Azure, zapoznaj się z tematem dołączania [do Azure Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) .

## <a name="ongoing-security-monitoring"></a>Bieżące monitorowanie zabezpieczeń
Po wstępnej konfiguracji i zastosowaniu zaleceń Security Center następnym krokiem jest uwzględnienie Security Center procesów operacyjnych.

Security Center przegląd zawiera ujednolicony widok zabezpieczeń dla wszystkich zasobów platformy Azure i wszystkich zasobów nienależących do platformy Azure, z którymi nawiązano połączenie. W poniższym przykładzie przedstawiono środowisko z wieloma problemami, które należy rozwiązać:

![cyfrowego](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center nie zakłóca normalnych procedur operacyjnych, będzie pasywnie monitorować wdrożenia i udostępniać zalecenia na podstawie włączonych zasad zabezpieczeń.

Podczas pierwszego korzystania z Security Center dla bieżącego środowiska platformy Azure upewnij się, że zawarto przegląd wszystkich zaleceń, które można wykonać na kafelku **rekomendacje** lub na zasób (dane dotyczące**obliczeń**, **sieci**, **magazynu &** , **Aplikacja**).

Po rozwiązaniu wszystkich zaleceń sekcja **zapobiegania** powinna być zielona dla wszystkich zasobów, które zostały rozkierowane. Bieżące monitorowanie w tym punkcie stanie się łatwiejsze, ponieważ podejmujesz tylko akcje na podstawie zmian w kafelkach kondycja zabezpieczeń zasobów i zalecenia.

Sekcja **wykrywanie** jest bardziej reaktywna. są to alerty dotyczące problemów, które mają miejsce już teraz lub wystąpiły w przeszłości i zostały wykryte przez Security Center kontroli i systemów innych firm. Kafelek alerty zabezpieczeń pokazuje wykresy słupkowe przedstawiające liczbę alertów wykrywania zagrożeń, które zostały znalezione w poszczególnych dniach, i ich dystrybucję do różnych kategorii ważności (niski, średni, wysoki). Aby uzyskać więcej informacji na temat alertów zabezpieczeń, zobacz temat [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md).

Zaplanuj odwiedzenie opcji [analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) w ramach codziennych operacji zabezpieczeń. Istnieje możliwość zidentyfikowania zagrożeń bezpieczeństwa w środowisku, na przykład określenie, czy określony komputer jest częścią botnetu.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorowanie nowych lub zmienionych zasobów
Większość środowisk platformy Azure jest dynamiczna, a zasoby są regularnie tworzone, można je w górę lub w dół, ponownie skonfigurować i zmienić. Security Center pomaga zapewnić widoczność stanu zabezpieczeń nowych zasobów.

Po dodaniu nowych zasobów (maszyn wirtualnych, baz danych SQL) do środowiska platformy Azure Security Center automatycznie odnajdzie te zasoby i rozpocznie monitorowanie ich zabezpieczeń. Obejmuje to również role sieci Web PaaS i role procesów roboczych. Jeśli w [zasadach zabezpieczeń](tutorial-security-policy.md)włączono funkcję zbierania danych, w przypadku maszyn wirtualnych zostaną automatycznie włączone dodatkowe funkcje monitorowania.

![Kluczowe obszary](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. W przypadku maszyn wirtualnych kliknij pozycję **obliczeniowe & aplikacje**w sekcji **higiena zabezpieczeń zasobów** . Wszystkie problemy z włączaniem danych lub powiązanymi zaleceniami zostaną wyświetlone na karcie **Przegląd** i sekcji **zalecenia dotyczące monitorowania** .
2. Zapoznaj się z **zaleceniami** , aby zobaczyć, jakie zagrożenia bezpieczeństwa zostały zidentyfikowane dla nowego zasobu.
3. Podczas dodawania nowych maszyn wirtualnych do środowiska jest bardzo często instalowany tylko system operacyjny. Właściciel zasobu może potrzebować pewnego czasu, aby wdrożyć inne aplikacje, które będą używane przez te maszyny wirtualne.  Najlepiej zapoznaj się z końcowym zamiarem tego obciążenia. Czy będzie to serwer aplikacji? W oparciu o to, co będzie miało być nowe obciążenie, możesz włączyć odpowiednie **zasady zabezpieczeń**, które są trzecią czynnością w tym przepływie pracy.
4. W miarę dodawania nowych zasobów do środowiska platformy Azure nowe alerty mogą pojawić się na kafelku **alerty zabezpieczeń** . Poszukaj nowych alertów na tym kafelku i postępuj zgodnie z zaleceniami.

Należy również regularnie monitorować istniejące zasoby w celu wprowadzenia zmian w konfiguracji, które mogłyby spowodować ryzyko związane z bezpieczeństwem, odniesieniu od zalecanych linii bazowych i alertów zabezpieczeń. Zacznij od pulpitu nawigacyjnego Security Center. Z tego miejsca masz trzy główne obszary, które można sprawdzić spójnie.

![Składowa](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Panel sekcja **zapobiegania** zapewnia szybki dostęp do kluczowych zasobów. Ta opcja służy do monitorowania danych i aplikacji obliczeniowych, sieci, magazynu &.
2. Panel **rekomendacje** umożliwia zapoznanie się z zaleceniami Security Center. W trakcie ciągłego monitorowania może się okazać, że nie masz żadnych zaleceń codziennie, co jest normalne, ponieważ zostały uwzględnione wszystkie zalecenia dotyczące wstępnej konfiguracji Security Center. Z tego powodu w tej sekcji mogą nie znajdować się nowe informacje codziennie i wystarczy uzyskać do nich dostęp w razie potrzeby.
3. Sekcja **wykrywanie** może ulec zmianie na bardzo częste lub bardzo rzadko. Zawsze sprawdzaj alerty zabezpieczeń i podejmuj działania na podstawie zaleceń Security Center.

### <a name="hardening-access-and-applications"></a>Ograniczanie dostępu i aplikacji

W ramach operacji związanych z zabezpieczeniami należy również przyjąć środki zapobiegawcze, aby ograniczyć dostęp do maszyn wirtualnych i kontrolować aplikacje działające na maszynach wirtualnych. Blokując ruch przychodzący do maszyn wirtualnych platformy Azure, zmniejszasz narażenie na ataki i w tym samym czasie, zapewniając łatwy dostęp do łączenia z maszynami wirtualnymi w razie potrzeby. Użyj funkcji dostępu [just in Time do maszyny wirtualnej](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) w celu ograniczenia dostępu do maszyn wirtualnych.

Możesz użyć [adaptacyjnych kontrolek aplikacji](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) , aby ograniczyć aplikacje, które mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Dzięki temu można zwiększyć ochronę maszyn wirtualnych przed złośliwym oprogramowaniem. Za pomocą uczenia maszynowego Security Center analizuje procesy uruchomione na maszynie wirtualnej, aby ułatwić tworzenie reguł listy dozwolonych.


## <a name="incident-response"></a>Odpowiedź na zdarzenie
Security Center wykrywa zagrożenia i ostrzega o nich w miarę ich występowania. Organizacje powinny monitorować nowe alerty zabezpieczeń i podejmować działania w razie potrzeby, aby dokładniej zbadać lub skorygować atak. Aby uzyskać więcej informacji na temat sposobu działania wykrywania zagrożeń Security Center, Przeczytaj, [jak Azure Security Center wykrywa zagrożenia i reaguje na](security-center-alerts-overview.md#detect-threats)nie.

Chociaż w tym artykule nie ma potrzeby pomocy przy tworzeniu własnego planu reagowania na zdarzenia, będziemy używać Microsoft Azure odpowiedzi na zabezpieczenia w cyklu życia chmury jako podstawę etapów reagowania na zdarzenia. Etapy przedstawiono na poniższym diagramie:

![Podejrzane działanie](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> [Przewodnik obsługi zdarzeń zabezpieczeń systemu](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) National Institute of Standards and Technology (NIST) może służyć jako dokumentacja ułatwiająca tworzenie własnych.
>

Alertów Security Center można używać w następujących etapach:

* **Wykrywanie**: identyfikowanie podejrzanych działań w jednym lub większej liczbie zasobów.
* **Ocena**: Wykonaj wstępną ocenę, aby uzyskać więcej informacji o podejrzanych działaniach.
* **Diagnozuj**: wykonaj czynności zaradcze, aby przeprowadzić procedurę techniczną dotyczącą problemu.

Każdy alert zabezpieczeń zawiera informacje, które mogą być używane do lepszego zrozumienia charakteru ataku i zasugerować możliwe środki zaradcze. Niektóre alerty zawierają również linki do dodatkowych informacji lub innych źródeł informacji w ramach platformy Azure. Możesz użyć dostarczonych informacji do dalszej analizy i rozpocząć środki zaradcze, a także przeszukać dane związane z zabezpieczeniami przechowywane w obszarze roboczym.

W poniższym przykładzie przedstawiono podejrzane działania dotyczące protokołu RDP:

![Podejrzane działanie](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Na tej stronie przedstawiono szczegóły dotyczące czasu, przez który miało miejsce atak, źródłową nazwę hosta, docelową maszynę wirtualną, a także przedstawiono kroki zalecenia. W pewnych okolicznościach informacje źródłowe ataku mogą być puste. Odczytaj [brakujące informacje źródłowe w alertach Azure Security Center](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) , aby uzyskać więcej informacji na temat tego typu zachowań.

Na tej stronie możesz również rozpocząć [Badanie](https://docs.microsoft.com/azure/security-center/security-center-investigation) , aby lepiej zrozumieć oś czasu ataku, jak miało miejsce atak, które systemy były potencjalnie naruszone, których poświadczeń użyto, i zobaczyć graficzną reprezentację całego łańcuch ataków.

Po zidentyfikowaniu systemu, który został naruszony, można uruchomić [elementy PlayBook](https://docs.microsoft.com/azure/security-center/security-center-playbooks) zabezpieczeń, które zostały wcześniej utworzone. Security element PlayBook to zbiór procedur, które mogą być wykonywane z Security Center po wyzwoleniu określonego element PlayBook z wybranego alertu.

W obszarze [jak korzystać z Azure Security Center & Microsoft Operations Management Suite dla wideo z odpowiedzią na zdarzenia](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) można zobaczyć niektóre demonstracje, które mogą pomóc zrozumieć, jak Security Center może być używany w każdym z tych etapów.

> [!NOTE]
> Aby uzyskać więcej informacji na temat korzystania z funkcji Security Center, zobacz temat [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md) .
>
>

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób planowania wdrażania Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące tematy:

* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Monitorowanie rozwiązań partnerskich za pomocą Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center często zadawane](security-center-faq.md) pytania — Znajdź często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
