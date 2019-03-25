---
title: Usługa Azure Security Center — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: monhaber
ms.openlocfilehash: 276b2815b36f05aa49183681b6c9e622155938e9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401133"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące usługi Azure Security Center
Często zadawane pytania dotyczące usługi Azure Security Center, to usługa, która pomaga zapobiegać zagrożeniom, wykrywanie i odpowiadanie na nie dzięki lepszemu wglądowi w i kontroli nad ich zabezpieczeniami zasobami Microsoft Azure.

> [!NOTE]
> Począwszy od początku czerwca 2017 roku, usługa Security Center będzie używać programu Microsoft Monitoring Agent do gromadzenia i przechowywania danych. Aby dowiedzieć się więcej, zobacz [migracja platformy Centrum zabezpieczeń Azure](security-center-platform-migration.md). Informacje przedstawione w tym artykule reprezentują funkcję Security Center po przejściu do programu Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?
Centrum zabezpieczeń Azure ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

### <a name="how-do-i-get-azure-security-center"></a>Jak uzyskać usługę Azure Security Center?
Usługa Azure Security Center jest włączone w ramach subskrypcji Microsoft Azure i dostępne w [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/). ([Zaloguj się do portalu](https://portal.azure.com), wybierz opcję **Przeglądaj**i przewiń do **usługi Security Center**).  

## <a name="billing"></a>Rozliczenia
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak działa rozliczanie usługi Azure Security Center?
Usługa Security Center jest oferowana w dwóch warstwach:

**w warstwie bezpłatna** zapewnia wgląd w stan zabezpieczeń zasobów platformy Azure, podstawowe zasady zabezpieczeń, zaleceń dotyczących zabezpieczeń i integrację z produktów i usług zabezpieczeń oferowanych przez partnerów.

**w warstwie standardowa** dodaje zaawansowanych zagrożeń możliwości wykrywania, w tym zagrożeń analizy, analizę behawioralną, wykrywanie anomalii, zdarzenia związane z bezpieczeństwem i zagrożeń: uznanie autorstwa raportów. Możesz rozpocząć bezpłatny okres próbny w warstwie standardowa. Aby przeprowadzić uaktualnienie, wybierz [warstwy cenowej](https://docs.microsoft.com/azure/security-center/security-center-pricing) w zasadach zabezpieczeń. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="permissions"></a>Uprawnienia
Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center widoczne są tylko informacje związane z zasobem, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnień w usłudze Azure Security Center](security-center-permissions.md) Aby dowiedzieć się więcej na temat ról i dozwolonych akcji w usłudze Security Center.

## <a name="data-collection-agents-and-workspaces"></a>Zbieranie danych, agentów i obszary robocze
Usługa Security Center zbiera dane z maszyn wirtualnych (VM), zestawy skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych), kontenery IaaS i komputery spoza platformy Azure (z uwzględnieniem lokalnej) do monitorowania pod kątem luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Czy są naliczane w przypadku dzienników usługi Azure Monitor na obszarach roboczych utworzonych przez usługę Security Center?
Nie. Obszary robocze utworzone przez usługę Security Center, gdy skonfigurowane dla dzienników usługi Azure Monitor Naliczanie węzła, nie są naliczane opłaty dzienniki usługi Azure Monitor. Usługa Security Center są zawsze naliczane zasad zabezpieczeń usługa Security Center i rozwiązań zainstalowane w obszarze roboczym:

- **W warstwie bezpłatna** — usługa Security Center umożliwia rozwiązanie "SecurityCenterFree" na domyślnego obszaru roboczego. Nie są rozliczane w warstwie bezpłatna.
- **W warstwie standardowa** — usługa Security Center umożliwia rozwiązanie "Zabezpieczenia" na domyślnego obszaru roboczego.

Aby uzyskać więcej informacji na temat cen, zobacz [cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/). Strona cennika adresów zmiany w magazynowaniu danych zabezpieczeń i proporcjonalnie rozliczanie począwszy od czerwca 2017 r.

> [!NOTE]
> Obszary robocze utworzone przez usługę Security Center warstwy cenowej w usłudze log analytics nie ma wpływu na rozliczenia w usłudze Security Center.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co to jest uprawniony Maszynę wirtualną do automatycznej aprowizacji instalacja programu Microsoft Monitoring Agent?
Windows lub maszyn wirtualnych IaaS z systemem Linux kwalifikuje się, jeśli:

- Rozszerzenia Microsoft Monitoring Agent nie jest obecnie zainstalowana na maszynie Wirtualnej.
- Maszyna wirtualna jest w stanie uruchomienia.
- Windows lub Linux [agenta maszyny wirtualnej platformy Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/agent-windows) jest zainstalowany.
- Maszyna wirtualna nie jest używana jako urządzenie, takie jak zapory aplikacji sieci web lub Zapora nowej generacji.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Czy można usunąć domyślne obszary robocze utworzone przez usługę Security Center?
**Nie zaleca się usunięcie domyślnego obszaru roboczego.** Usługa Security Center używa domyślne obszary robocze do przechowywania danych zabezpieczeń z maszyn wirtualnych.  Jeśli usuniesz obszar roboczy, usługa Security Center nie może zbierać dane i niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne.

Aby odzyskać, Usuń program Microsoft Monitoring Agent na maszynach wirtualnych podłączone do usuniętego obszaru roboczego. Usługa Security Center ponownie instaluje agenta i tworzy nowe domyślne obszary robocze.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak użyć Mój istniejący obszar roboczy usługi Log Analytics?

Możesz wybrać istniejący obszar roboczy usługi Log Analytics do przechowywania danych zbieranych przez usługę Security Center. Aby użyć istniejącego obszaru roboczego usługi Log Analytics:

- Obszar roboczy musi być skojarzony z wybranej subskrypcji platformy Azure.
- Jako minimum użytkownik musi mieć uprawnienia odczytu do dostępu do obszaru roboczego.

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **zasady zabezpieczeń — zbierania danych**, wybierz opcję **Użyj innego obszaru roboczego**.

   ![Użyj innego obszaru roboczego][5]

2. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

   > [!NOTE]
   > Ściągnąć menu wyświetlane są tylko obszary robocze, które mają dostęp do i znajdują się w Twojej subskrypcji platformy Azure.
   >
   >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu **Zapisz**, konieczne będzie podanie, jeśli chcesz ponownie skonfigurować monitorowane maszyny wirtualne.

   - Wybierz **nie** Jeśli chcesz, aby nowe ustawienia obszaru roboczego **zastosować nowych maszyn wirtualnych tylko**. Nowe ustawienia obszaru roboczego dotyczą tylko nowe instalacje agentów; odnalezione maszyny wirtualne, które nie mają zainstalowany program Microsoft Monitoring Agent.
   - Wybierz **tak** Jeśli chcesz, aby nowe ustawienia obszaru roboczego **zastosować na wszystkich maszynach wirtualnych**. Ponadto każda maszyna wirtualna podłączone do usługi Security Center, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

   > [!NOTE]
   > Jeśli wybierzesz tak, nie można usuwać obszary robocze utworzone przez usługę Security Center, aż wszystkie maszyny wirtualne mają został ponownie podłączony do nowego docelowego obszaru roboczego. Ta operacja nie powiedzie się, za wczesne usunięcie obszaru roboczego.
   >
   >

   - Wybierz **anulować** anulować operację.

### Co zrobić, jeśli program Microsoft Monitoring Agent został już zainstalowany jako rozszerzenie na maszynie Wirtualnej?<a name="mmaextensioninstalled"></a>
Po zainstalowaniu programu Monitoring Agent jako rozszerzenie Konfiguracja rozszerzenia pozwala na zgłaszanie tylko z jednym obszarem roboczym. Usługa Security Center nie zastępuje istniejące połączenia do obszarów roboczych użytkownika. Usługa Security Center będzie przechowywać dane zabezpieczeń z maszyny Wirtualnej w obszarze roboczym, który jest już połączony, pod warunkiem, że "zabezpieczenia" lub "securityFree" rozwiązania została zainstalowana na nim. Usługa Security Center może Uaktualnij wersję rozszerzenia do najnowszej wersji w ramach tego procesu.

Aby uzyskać więcej informacji, zobacz [automatycznej aprowizacji w przypadku istniejących instalacji agenta](security-center-enable-data-collection.md#preexisting).


### Co zrobić, jeśli wcześniej zainstalowany Microsoft Monitoring Agent bezpośrednio na maszynie, ale nie jako rozszerzenie (Agent bezpośredni)?<a name="directagentinstalled"></a>
Program Microsoft Monitoring Agent jest zainstalowany bezpośrednio na maszynie Wirtualnej (a nie jako rozszerzenie platformy Azure), Centrum zabezpieczeń zainstaluje rozszerzenia Microsoft Monitoring Agent i mogą zostać uaktualnione do najnowszej wersji Microsoft Monitoring agent.
Jest zainstalowany agent będzie kontynuował raportowanie do jego już skonfigurowanego obszarów roboczych, a ponadto będzie zgłaszać do obszaru roboczego skonfigurowane w usłudze Security Center (multihosting jest obsługiwane).
Skonfigurowany obszar roboczy w przypadku obszaru roboczego użytkownika (nie Centrum zabezpieczeń domyślnego obszaru roboczego), będą musieli zainstalować "security / rozwiązania"securityFree"na nim usługi Security Center rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów raportujących do obszaru roboczego.

Dla istniejących maszyn w subskrypcji dołączono do usługi Security Center, przed 2019-03-17, gdy zostanie wykryty istniejącego agenta, rozszerzenia Microsoft Monitoring Agent nie zostanie zainstalowany, a komputer nie ma wpływu na. Dla tych maszyn Zobacz zalecenie "Monitorowanie problemów dotyczących kondycji agenta na maszynach Rozwiąż", aby rozwiązać problemy z instalacją agentów na tych maszynach

 Aby uzyskać więcej informacji, zobacz następną sekcję [co się stanie w przypadku pakietu OMS lub SCOM programu bezpośredni agent jest już zainstalowany na maszynie Wirtualnej?](#scomomsinstalled)

### Co się stanie, jeśli SCOM agent jest już zainstalowany na maszynie Wirtualnej?<a name="scomomsinstalled"></a>
Usługa Security center zostanie zainstalowany program Microsoft Monitoring Agent rozszerzenia side-by-side do istniejącego programu SCOM. Istniejącego agenta programu SCOM będą nadal normalnie raportu do serwera programu SCOM. Należy pamiętać, że SCOM agent i Microsoft Monitoring Agent współużytkują wspólne biblioteki czasu wykonywania, które zostaną zaktualizowane do wersji Najpóźniejsza podczas tego procesu.

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaki jest wpływ usunięcia tych rozszerzeń?
Jeśli usuniesz rozszerzenie monitorowania firmy Microsoft, usługa Security Center nie jest w stanie do zbierania danych zabezpieczeń z maszyny Wirtualnej i niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. W ciągu 24 godzin usługa Security Center określa Brak rozszerzenia i spowoduje ponowne zainstalowanie rozszerzenia maszyny Wirtualnej.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak zatrzymać automatyczne agent instalacji i tworzenie obszaru roboczego?
Możesz wyłączyć automatycznej aprowizacji dla subskrypcji w ramach zasad zabezpieczeń, ale nie jest to zalecane. Wyłączenie automatycznej aprowizacji limity zaleceń usługi Security Center i alerty. Aby wyłączyć automatyczną aprowizację:

1. Jeśli Twoja subskrypcja jest skonfigurowana dla warstwy standardowa, otworzyć przystawkę Zasady zabezpieczeń dla tej subskrypcji i wybierz **bezpłatna** warstwy.

   ![Warstwa cenowa][1]

2. Następnie należy wyłączyć automatyczną aprowizację, wybierając **poza** na **zasady zabezpieczeń — zbierania danych** bloku.
   ![Zbieranie danych][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Należy zrezygnować z automatyczną instalację agenta i tworzenie obszaru roboczego?

> [!NOTE]
> Pamiętaj zapoznać się z sekcjami [jakie są skutki zrezygnować?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) i [zalecane kroki, gdy zrezygnować](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) Jeśli zdecydujesz się zrezygnować z automatycznego inicjowania obsługi administracyjnej.
>
>

Można zrezygnować z automatycznego inicjowania obsługi administracyjnej, jeśli stosuje się do Ciebie:

- Automatyczną instalację agenta przez usługę Security Center ma zastosowanie do całej subskrypcji.  Nie można zastosować automatycznej instalacji dla podzbioru maszyn wirtualnych. W przypadku newralgicznych maszyn wirtualnych, których nie można zainstalować za pomocą programu Microsoft Monitoring Agent, należy zrezygnować z automatycznego inicjowania obsługi administracyjnej.
- Instalowanie rozszerzenia Microsoft Monitoring Agent aktualizacji wersji agenta. Dotyczy to agent bezpośredni i agenta programu SCOM. Jeśli jest zainstalowany agent programu SCOM jest w wersji 2012 i zostanie uaktualniona, możliwości zarządzania mogą zostać utracone, gdy serwer SCOM jest również wersja 2012. Należy rozważyć rezygnacji z automatycznej aprowizacji, jeśli jest zainstalowany agent SCOM jest w wersji 2012.
- Jeśli masz niestandardowe zewnętrzne w stosunku do subskrypcji (scentralizowanych obszarów roboczych) obszar roboczy należy zrezygnować z automatycznego inicjowania obsługi administracyjnej. Można ręcznie zainstalować rozszerzenia Microsoft Monitoring Agent i połączyć go obszaru roboczego bez usługi Security Center zastępowanie połączenia.
- Jeśli chcesz uniknąć tworzenia wielu obszarów roboczych na subskrypcję i masz własnego niestandardowego obszaru roboczego w ramach subskrypcji, masz dwie opcje:

   1. Użytkownik może zrezygnować z automatycznego inicjowania obsługi administracyjnej. Po zakończeniu migracji, Ustaw domyślną ustawienia obszaru roboczego zgodnie z opisem w [jak używać Mój istniejący obszar roboczy usługi Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Lub możesz zezwolić migracji do ukończenia programu Microsoft Monitoring Agent do zainstalowania na maszynach wirtualnych, i maszyny wirtualne podłączone do utworzonego obszaru roboczego. Następnie wybierz własnego niestandardowego obszaru roboczego przez ustawienie domyślne ustawienie obszaru roboczego przy użyciu zgody na korzystanie z ponownej konfiguracji już zainstalowanych agentów. Aby uzyskać więcej informacji, zobacz [jak używać Mój istniejący obszar roboczy usługi Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jakie są skutki rezygnacji z automatycznej aprowizacji?
Po ukończeniu migracji usługi Security Center nie jest w stanie do zbierania danych zabezpieczeń z maszyny Wirtualnej i niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. Jeśli możesz zrezygnować, należy zainstalować program Microsoft Monitoring Agent ręcznie. Zobacz [zalecane kroki, gdy zrezygnować](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jakie są zalecane czynności w przypadku rezygnacji z automatycznej aprowizacji?

Należy ręcznie zainstalować rozszerzenia Microsoft Monitoring Agent, usługa Security Center może zbierania danych zabezpieczeń z maszyn wirtualnych i udostępniania zaleceń i alertów. Zobacz [instalacji agenta maszyny wirtualnej Windows](../virtual-machines/extensions/oms-windows.md) lub [instalację agenta maszyny Wirtualnej systemu Linux](../virtual-machines/extensions/oms-linux.md) wskazówki dotyczące instalacji.

Możesz nawiązać połączenie agenta istniejącego niestandardowego obszaru roboczego lub Centrum zabezpieczeń utworzone obszaru roboczego. Jeśli niestandardowego obszaru roboczego nie ma rozwiązania "Zabezpieczenia" lub "SecurityCenterFree" włączona, należy zastosować rozwiązanie. Aby zastosować, zaznacz niestandardowego obszaru roboczego lub subskrypcji i Zastosuj warstwę cenową za pośrednictwem **zasady zabezpieczeń — warstwa cenowa** bloku.

   ![Warstwa cenowa][1]

Usługa Security Center umożliwi rozwiązanie będzie odpowiednie w oparciu o wybranej warstwy cenowej obszaru roboczego.

### Jak usunąć rozszerzenia pakietu OMS, instalowane przez usługę Security Center?<a name="remove-oms"></a>
Można ręcznie usunąć program Microsoft Monitoring Agent. Nie jest to zalecane, ponieważ ogranicza zaleceń usługi Security Center i alerty.

> [!NOTE]
> Po włączeniu zbierania danych usługa Security Center będzie ponownie zainstalować agenta po jego usunięciu.  Należy wyłączyć zbieranie danych przed ręczne usunięcie agenta. Zobacz, jak zatrzymać automatyczne agent instalacji i tworzenie obszaru roboczego? Aby uzyskać instrukcje dotyczące wyłączania zbierania danych.
>
>

Aby ręcznie usunąć agenta:

1.  Otwórz w portalu, **usługi Log Analytics**.
2.  W bloku usługi Log Analytics wybierz obszar roboczy:
3.  Wybieranie poszczególnych maszyn wirtualnych, których nie chcesz, aby monitorować i wybierz **rozłączenia**.

   ![Usuń agenta][3]

> [!NOTE]
> Jeśli Maszynę wirtualną systemu Linux ma już agenta pakietu OMS bez rozszerzeń, usuwając rozszerzenie usuwa także agenta i klient ma zainstalować go ponownie.
>
>
### <a name="how-do-i-disable-data-collection"></a>Jak wyłączyć zbieranie danych?
Automatyczna aprowizacja jest domyślnie wyłączona. Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. Automatyczna aprowizacja zdecydowanie zalecane jest aby uzyskać alerty zabezpieczeń i zalecenia dotyczące aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu endpoint protection.

Aby wyłączyć zbieranie danych [Zaloguj się do witryny Azure portal](https://portal.azure.com), wybierz opcję **Przeglądaj**, wybierz opcję **usługi Security Center**i wybierz **wybierz zasady**. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację. Po wybraniu subskrypcji **zasady zabezpieczeń — zbierania danych** zostanie otwarty. W obszarze **automatycznej aprowizacji**, wybierz opcję **poza**.

### <a name="how-do-i-enable-data-collection"></a>Jak włączyć zbieranie danych?
Można włączyć zbierania danych dla subskrypcji platformy Azure w ramach zasad zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do witryny Azure portal](https://portal.azure.com), wybierz opcję **Przeglądaj**, wybierz opcję **usługi Security Center**i wybierz **zasady zabezpieczeń**. Wybierz subskrypcję, której chcesz włączyć automatyczną aprowizację. Po wybraniu subskrypcji **zasady zabezpieczeń — zbierania danych** zostanie otwarty. W obszarze **automatycznej aprowizacji**, wybierz opcję **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie, gdy jest włączone zbieranie danych?
Po włączeniu automatycznej aprowizacji Security Center aprowizuje program Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i wszelkie nowe, które są tworzone. Automatycznej aprowizacji jest zdecydowanie zalecane, ale jest również dostępna ręcznej instalacji agenta. [Dowiedz się, jak można zainstalować rozszerzenia Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent umożliwia zdarzeń tworzenia procesu 4688 i *CommandLine* pola wewnątrz zdarzeń 4688. Nowe procesy utworzone na maszynie Wirtualnej są rejestrowane w dzienniku zdarzeń i monitorowana przez Centrum zabezpieczeń usługi wykrywania. Instrukcje dotyczące szczegółów dla każdego nowego procesu, zobacz [pola Opis 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent również zbiera dane zdarzeń 4688, utworzony na maszynie Wirtualnej i przechowuje je w polu wyszukiwania.

Agent umożliwia również zbieranie danych dla [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), usługa Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. To spowoduje, że zasady ograniczeń oprogramowania do wygenerowania zdarzenia, które następnie są pobierane i wykorzystywane przez usługę Security Center. Należy zauważyć, że te zasady nie zostaną skonfigurowane na maszynach, na których jest już skonfigurowane zasady funkcji AppLocker. 

Gdy usługa Security Center wykrywa podejrzane działania na maszynie Wirtualnej, powiadomienia klienta za pośrednictwem poczty e-mail, gdy [zabezpieczeń informacji kontaktowych](security-center-provide-security-contact-details.md) zostało podane. Alert jest również widoczne w pulpicie nawigacyjnym alerty zabezpieczeń Centrum zabezpieczeń.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Czy Monitoring Agent wpływ na wydajność moich serwerów?
Agent korzysta nominalna ilość zasobów systemowych i powinno mieć większego wpływu na wydajność. Aby uzyskać więcej informacji na temat wpływu na wydajność i agent i rozszerzenia, zobacz [przewodnik planowania i obsługi](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Gdzie są przechowywane moje dane?
Dane zbierane z tego agenta są przechowywane w istniejącego obszaru roboczego usługi Log Analytics skojarzonych z subskrypcją lub nowy obszar roboczy. Aby uzyskać więcej informacji, zobacz [bezpieczeństwo danych](security-center-data-security.md).

## Istniejące usługi Azure Monitor dzienniki klientów<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Usługa Security Center, powoduje zastąpienia istniejące połączenia między maszynami wirtualnymi i obszary robocze?
Jeśli maszyna wirtualna ma już zainstalowany program Microsoft Monitoring Agent jako rozszerzenie platformy Azure, usługa Security Center nie zastępuje istniejące połączenie z obszaru roboczego. Zamiast tego usługa Security Center korzysta z istniejącego obszaru roboczego.

Rozwiązania usługi Security Center w obszarze roboczym Jeśli nie została zainstalowana już istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych. Po dodaniu rozwiązania, automatycznie są wdrażane domyślnie wszyscy agenci Windows i Linux, połączonego z obszarem roboczym usługi Log Analytics. [Określanie wartości docelowej rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) można zastosować zakres do rozwiązania.

Jeśli program Microsoft Monitoring Agent jest zainstalowany bezpośrednio na maszynie Wirtualnej (a nie jako rozszerzenie platformy Azure), usługa Security Center nie instaluje program Microsoft Monitoring Agent oraz monitorowanie zabezpieczeń jest ograniczony.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Czy usługa Security Center zainstalowanie rozwiązania na moich istniejących obszarów roboczych usługi Log Analytics Jakie są skutki rozliczeń?
Gdy usługa Security Center zidentyfikuje, że maszyny Wirtualnej jest już połączony obszar roboczy, który został utworzony, usługa Security Center umożliwia rozwiązań na ten obszar roboczy, zgodnie z warstwy cenowej. Rozwiązania są stosowane tylko do odpowiednich maszyn wirtualnych platformy Azure za pośrednictwem [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md), więc opłaty rozliczeniowe pozostają bez zmian.

- **W warstwie bezpłatna** — usługa Security Center instaluje rozwiązanie "SecurityCenterFree" w obszarze roboczym. Nie są rozliczane w warstwie bezpłatna.
- **W warstwie standardowa** — usługa Security Center instaluje rozwiązanie "Zabezpieczenia" w obszarze roboczym.

   ![Rozwiązania na domyślny obszar roboczy][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Mam już obszarów roboczych w mojej środowisku, można z nich korzystać do zbierania danych zabezpieczeń?
Jeśli maszyna wirtualna ma już zainstalowany program Microsoft Monitoring Agent jako rozszerzenie platformy Azure, usługa Security Center korzysta z istniejącego obszaru roboczego połączonych. Rozwiązania usługi Security Center jest zainstalowany w obszarze roboczym Jeśli nie istnieje już i rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych za pośrednictwem [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md).

Gdy usługa Security Center instaluje program Microsoft Monitoring Agent na maszynach wirtualnych, używane domyślne wszystkich obszarach roboczych, utworzonych przez usługę Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Mam już rozwiązania zabezpieczeń na mój obszar roboczy. Jakie są skutki rozliczeń?
Rozwiązanie zabezpieczenia i inspekcja umożliwia włączanie funkcje warstwy standardowej usługi Security Center dla maszyn wirtualnych platformy Azure. Jeśli rozwiązanie zabezpieczenia i inspekcja jest już zainstalowany w obszarze roboczym, usługa Security Center korzysta z istniejącego rozwiązania. Nie ma zmian w rozliczeń.

## <a name="using-azure-security-center"></a>Za pomocą usługi Azure Security Center
### <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?
Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w ramach określonej subskrypcji. W usłudze Azure Security Center można zdefiniować zasady dla subskrypcji platformy Azure zgodnie z wymaganiami w zakresie zabezpieczeń firmy i typem aplikacji oraz poufnością danych w poszczególnych subskrypcjach.

Zasady zabezpieczeń włączone w usłudze Azure Security Center dysku — zalecenia dotyczące zabezpieczeń i monitorowania. Aby dowiedzieć się więcej na temat zasad zabezpieczeń, zobacz [monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, musi być kontem administratora zabezpieczeń lub właścicielem lub współautorem subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Co to jest zalecana ze względów bezpieczeństwa?
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Po zidentyfikowaniu potencjalnych luk w zabezpieczeniach tworzone są zalecenia. Zaleceń prowadzi przez proces konfigurowania wymaganych kontroli. Oto przykłady:

* Inicjowanie obsługi ochrony przed złośliwym kodem do identyfikacji i usuwania złośliwego oprogramowania
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i reguł sterujących ruchem do maszyn wirtualnych
* Inicjowanie obsługi administracyjnej zapory aplikacji sieci web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Tylko te zalecenia, które są włączone w zasadach zabezpieczeń są wyświetlane w tym miejscu.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak wyświetlić bieżący stan zabezpieczeń zasobów platformy Azure, Moje?
**Omówienie usługi Security Center** blok ogólny stan zabezpieczeń środowiska według obliczeniowych, sieci, magazynu i danych i aplikacji. Każdy typ zasobu ma przedstawiający wskaźnik, jeśli zidentyfikowano żadnych potencjalnych luk w zabezpieczeniach. Klikając każdy Kafelek Wyświetla listę problemów z zabezpieczeniami identyfikowane przez usługę Security Center oraz spis zasobów w ramach subskrypcji.

### <a name="what-triggers-a-security-alert"></a>Co wyzwala alert zabezpieczeń?
Usługa Azure Security Center automatycznie zbiera, analizuje i fuses dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak ochrony przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Zaawansowanego złośliwego oprogramowania wykrytego za pomocą usługi raportowania błędów Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń z rozwiązań zabezpieczeń zintegrowanych partnerów, takich jak chroniące przed złośliwym kodem lub zapory aplikacji sieci Web

### Dlaczego secure zmiana wartości wyników? <a name="secure-score-faq"></a>
Począwszy od lutego 2019 r usługa Security Center dostosowane wynik kilka zaleceń, aby lepiej dopasować się ich ważności. W wyniku tego dostosowania mogą wystąpić zmiany w ogólnej secure wartości wyniku.  Aby uzyskać więcej informacji na temat bezpiecznego wynik zobacz [Secure obliczania wyniku](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożenia wykryte i alerty o przez program Microsoft Security Response Center i Azure Security Center?
Narzędzie Microsoft Security Response Center (MSRC) wykonuje monitorowania zabezpieczeń wybierz sieć platformy Azure i infrastrukturą i odbiera skarg analizy i nadużyć zagrożenia pochodzące od innych firm. Gdy MSRC uświadamia sobie, że dane klienta były używane na przez nieupoważnione lub lub że korzystanie z platformy Azure przez klienta nie jest zgodne z warunkami dopuszczalne użycie, Menedżer zdarzenia zabezpieczeń powiadamia klienta. Powiadomienia są zazwyczaj występuje, wysyłając wiadomość e-mail do kontaktów zabezpieczeń określone w usłudze Azure Security Center lub właściciel subskrypcji platformy Azure, jeśli nie określono kontaktu zabezpieczeń.

Usługa Security Center jest usługą platformy Azure, która stale monitoruje środowiska platformy Azure przez klienta i stosuje analytics, aby automatycznie wykrywać szeroką gamę potencjalnie złośliwych działań. Wykrywane odmiany są udostępniane jako alerty zabezpieczeń w pulpicie nawigacyjnym usługi Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Jakie zasoby platformy Azure są monitorowane przez usługę Azure Security Center?
Usługa Azure Security Center monitoruje następujących zasobów platformy Azure:

* Maszyny wirtualne (VM) (w tym [usług w chmurze](../cloud-services/cloud-services-choose-me.md))
* Zestawy skalowania maszyn wirtualnych (VMSSs)
* Sieci wirtualne platformy Azure
* Usługa Azure SQL
* Konto usługi Azure Storage
* Aplikacje sieci Web platformy Azure (w [środowiska App Service Environment](../app-service/environment/intro.md))
* Rozwiązań partnerskich zintegrowanych z subskrypcją platformy Azure, takich jak zapory aplikacji sieci web na maszynach wirtualnych i w środowisku App Service Environment

Ponadto komputery spoza platformy Azure (z uwzględnieniem lokalnej) można również monitorować przez usługę Azure Security Center (zarówno [komputerów Windows](./quick-onboard-windows-computer.md) i [komputerów z systemem Linux](./quick-onboard-linux-computer.md) są obsługiwane)

## <a name="virtual-machines"></a>Maszyny wirtualne
### <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?
Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych (VM) utworzone za pomocą obu [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md).

Zobacz [obsługiwanych platform w usłudze Azure Security Center](security-center-os-coverage.md) Aby uzyskać listę obsługiwanych platform.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego usługa Azure Security Center nie rozpoznaje rozwiązanie chroniące przed złośliwym kodem uruchomiona na maszynie Wirtualnej platformy Azure?
Usługa Azure Security Center ma wgląd we ochrony przed złośliwym kodem zainstalowane za pomocą rozszerzeń platformy Azure. Na przykład usługa Security Center nie jest w stanie wykryć ochrony przed złośliwym kodem, który został wstępnie zainstalowane w obrazie, podane lub jeśli zainstalowano ochrony przed złośliwym oprogramowaniem na maszynach wirtualnych przy użyciu własnych procesów (na przykład systemy zarządzania konfiguracją).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego otrzymuję komunikat "Brak danych skanowania" dla mojej maszyny Wirtualnej?
Ten komunikat pojawia się, gdy nie ma żadnych danych skanowania dla maszyny Wirtualnej. Jego może zająć trochę czasu (mniej niż godzinę) dla danych skanowania wypełnić po włączeniu zbierania danych w usłudze Azure Security Center. Po początkowego zapełniania danych skanowania może odebrane tego komunikatu, ponieważ nie ma żadnych danych skanowania w ogóle lub nie ma żadnych ostatnich danych skanowania. Skanowanie nie wypełnić dla maszyny Wirtualnej w stanie zatrzymania. Ten komunikat może również zostać wyświetlony, jeśli danych skanowania nie została wypełniona ostatnio (zgodnie z zasadami przechowywania dla agenta programu Windows, który ma wartość domyślną w ciągu 30 dni).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często usługa Security Center skanowanie luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu i problemy z ochroną punktu końcowego?
Czas oczekiwania w usłudze Security Center skanuje pod kątem luk w zabezpieczeniach, aktualizacje, problemów i jest:

- Konfiguracje zabezpieczeń systemu operacyjnego — danych jest aktualizowany w ciągu 48 godzin
- Aktualizacje systemu — danych jest aktualizowany w ciągu 24 godzin
- Problemy z programem Endpoint Protection — dane są aktualizowane w ramach 8 godzin

Usługa Security Center zwykle skanowania pod kątem nowych danych co godzinę i odpowiednio odświeża zaleceń. 

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego otrzymuję komunikat "Brak jest Agent maszyny Wirtualnej?"
Musi być zainstalowany Agent maszyny Wirtualnej na maszynach wirtualnych, aby włączyć zbieranie danych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Aby uzyskać informacje dotyczące sposobu instalowania agenta maszyny Wirtualnej na innych maszynach wirtualnych, zobacz wpis w blogu [agenta maszyny Wirtualnej i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
