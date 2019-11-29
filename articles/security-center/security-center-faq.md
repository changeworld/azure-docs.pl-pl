---
title: Azure Security Center często zadawanych pytań (FAQ) | Microsoft Docs
description: Często zadawane pytania dotyczą Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: memildin
ms.openlocfilehash: 896db06204188c4347fbdced0b1bb3f216f56ef9
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558660"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące usługi Azure Security Center
Ten często zadawane pytania zawierają odpowiedzi na pytania dotyczące Azure Security Center, usługi, która pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem Microsoft Azure zasobów.

> [!NOTE]
> Security Center używa Microsoft Monitoring Agent do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center migracji platformy](security-center-platform-migration.md).
>
>

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-security-center"></a>Co to jest Azure Security Center?
Centrum zabezpieczeń Azure ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

### <a name="how-do-i-get-azure-security-center"></a>Jak mogę uzyskać Azure Security Center?
Azure Security Center jest włączona z subskrypcją Microsoft Azure i jest dostępna z [Azure Portal](https://azure.microsoft.com/features/azure-portal/). ([Zaloguj się do portalu](https://portal.azure.com), wybierz pozycję **Przeglądaj**, a następnie przewiń do **Security Center**).  

## <a name="billing"></a>Rozliczenia
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak działa rozliczenia dla Azure Security Center?
Usługa Security Center jest oferowana w dwóch warstwach:

**Warstwa Bezpłatna** zapewnia wgląd w stan zabezpieczeń zasobów platformy Azure, podstawowe zasady zabezpieczeń, zalecenia dotyczące zabezpieczeń i integrację z produktami i usługami zabezpieczeń z partnerów.

**Warstwa standardowa** dodaje możliwości zaawansowanego wykrywania zagrożeń, w tym analizy zagrożeń, analizy behawioralnej, wykrywania anomalii, zdarzeń związanych z zabezpieczeniami i raportów dotyczących autorstwa zagrożeń. Możesz rozpocząć korzystanie z bezpłatnej wersji próbnej warstwy Standardowa. W celu uaktualnienia wybierz pozycję [warstwa cenowa](https://docs.microsoft.com/azure/security-center/security-center-pricing) w obszarze Zasady zabezpieczeń. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Jak mogę śledzić, kto w organizacji wykonał zmiany warstwy cenowej w Azure Security Center
Subskrypcje platformy Azure mogą mieć wielu administratorów z uprawnieniami do zmiany warstwy cenowej. Aby dowiedzieć się, który użytkownik wykonał zmianę warstwy cenowej, użyj dziennika aktywności platformy Azure. Aby uzyskać więcej informacji, zobacz [tutaj](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## <a name="permissions"></a>Uprawnienia
Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów i luk w zabezpieczeniach. W Security Center widoczne są tylko informacje związane z zasobem, gdy przypiszesz rolę właściciela, współautora lub czytelnika dla subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnienia w Azure Security Center](security-center-permissions.md) , aby dowiedzieć się więcej o rolach i dozwolonych akcjach w programie Security Center.

## <a name="data-collection-agents-and-workspaces"></a>Zbieranie danych, agenci i obszary robocze
Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz komputerów spoza platformy Azure (w tym lokalnych) do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Czy są naliczane opłaty za dzienniki Azure Monitor w obszarach roboczych utworzonych przez Security Center?
Nie. Obszary robocze utworzone przez Security Center, podczas gdy skonfigurowano do rozliczania Azure Monitor dzienników na węzeł, nie są naliczane opłaty za dzienniki Azure Monitor. Rozliczanie Security Center jest zawsze uzależnione od zasad zabezpieczeń Security Center i rozwiązań zainstalowanych w obszarze roboczym:

- **Warstwa Bezpłatna** — Security Center włącza rozwiązanie "SecurityCenterFree" w domyślnym obszarze roboczym. Nie zostanie naliczona opłata za warstwę bezpłatna.
- **Warstwa standardowa** — Security Center włącza rozwiązanie "zabezpieczenia" w domyślnym obszarze roboczym.

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Warstwa cenowa usługi log Analytics dla obszarów roboczych utworzonych przez Security Center nie ma wpływu na Security Center rozliczanie.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co kwalifikuje maszynę wirtualną do automatycznej aprowizacji instalacji Microsoft Monitoring Agent?
Maszyny wirtualne z systemem Windows lub Linux IaaS kwalifikują się, jeśli:

- Rozszerzenie Microsoft Monitoring Agent nie jest obecnie zainstalowane na maszynie wirtualnej.
- Maszyna wirtualna jest w stanie uruchomienia.
- [Agent maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) z systemem Windows lub Linux jest zainstalowany.
- Maszyna wirtualna nie jest używana jako urządzenie, takie jak Zapora aplikacji sieci Web lub Zapora nowej generacji.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Czy mogę usunąć domyślne obszary robocze utworzone przez Security Center?
**Nie zaleca się usuwania domyślnego obszaru roboczego.** Security Center używa domyślnych obszarów roboczych do przechowywania danych zabezpieczeń z maszyn wirtualnych.  W przypadku usunięcia obszaru roboczego Security Center nie będzie mógł zebrać tych danych, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne.

Aby odzyskać, Usuń Microsoft Monitoring Agent z maszyn wirtualnych połączonych z usuniętym obszarem roboczym. Security Center ponownie instaluje agenta i tworzy nowe domyślne obszary robocze.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak mogę użyć istniejącego obszaru roboczego Log Analytics?

Można wybrać istniejący obszar roboczy Log Analytics do przechowywania danych zbieranych przez Security Center. Aby użyć istniejącego obszaru roboczego Log Analytics:

- Obszar roboczy musi być skojarzony z wybraną subskrypcją platformy Azure.
- Aby uzyskać dostęp do obszaru roboczego, musisz mieć uprawnienia do odczytu.

Aby wybrać istniejący obszar roboczy Log Analytics:

1. W obszarze **zasady zabezpieczeń — zbieranie danych**wybierz opcję **Użyj innego obszaru roboczego**.

   ![Użyj innego obszaru roboczego][5]

2. Z menu rozwijanego wybierz obszar roboczy, w którym mają być przechowywane zebrane dane.

   > [!NOTE]
   > W menu rozwijanym są wyświetlane tylko obszary robocze, do których masz dostęp i znajdują się w subskrypcji platformy Azure.
   >
   >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu opcji **Zapisz**zostanie wyświetlony monit z prośbą o ponowne skonfigurowanie monitorowanych maszyn wirtualnych.

   - Wybierz pozycję **nie** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były **stosowane tylko do nowych maszyn wirtualnych**. Nowe ustawienia obszaru roboczego mają zastosowanie tylko do nowych instalacji agenta; nowo odnalezione maszyny wirtualne, na których nie zainstalowano Microsoft Monitoring Agent.
   - Wybierz pozycję **tak** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były **stosowane do wszystkich maszyn wirtualnych**. Ponadto wszystkie maszyny wirtualne połączone z Security Center utworzonym obszarem roboczym są ponownie połączone z nowym docelowym obszarem roboczym.

   > [!NOTE]
   > W przypadku wybrania opcji tak nie należy usuwać obszarów roboczych utworzonych przez Security Center, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja kończy się niepowodzeniem, jeśli obszar roboczy zostanie zbyt wcześnie usunięty.
   >
   >

   - Wybierz pozycję **Anuluj** , aby anulować operację.

### Co zrobić, jeśli Microsoft Monitoring Agent zostało już zainstalowane jako rozszerzenie na maszynie wirtualnej?<a name="mmaextensioninstalled"></a>
Gdy Agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko jednego obszaru roboczego. Security Center nie przesłania istniejących połączeń z obszarami roboczymi użytkowników. Security Center będą przechowywać dane zabezpieczeń z maszyny wirtualnej w obszarze roboczym, który jest już połączony, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "SecurityCenterFree". Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.

Aby uzyskać więcej informacji, zobacz [Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta](security-center-enable-data-collection.md#preexisting).


### Co zrobić, jeśli na maszynie jest bezpośrednio zainstalowana Microsoft Monitoring Agent, ale nie jako rozszerzenie (Agent bezpośredni)?<a name="directagentinstalled"></a>
Jeśli Microsoft Monitoring Agent jest zainstalowana bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center zainstaluje rozszerzenie Microsoft Monitoring Agent i może uaktualnić program Microsoft Monitoring Agent do najnowszej wersji.
Zainstalowany agent będzie kontynuował raportowanie do już skonfigurowanych obszarów roboczych, a ponadto wyśle raport do obszaru roboczego skonfigurowanego w Security Center (wiele multihostingu jest obsługiwana na maszynach z systemem Windows).
Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie Security Center domyślnym obszarem roboczym), należy zainstalować na nim rozwiązanie "Security/" SecurityCenterFree "dla Security Center, aby rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów zgłaszanych do tego obszaru roboczego.

W przypadku maszyn z systemem Linux Agent multihostingu nie jest jeszcze obsługiwany — w związku z tym jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne Inicjowanie obsługi nie zostanie wykonane i konfiguracja maszyny nie zostanie zmieniona.

W przypadku istniejących maszyn w ramach subskrypcji dołączanych do Security Center przed 17 2019 marca, gdy zostanie wykryty istniejący Agent, rozszerzenie Microsoft Monitoring Agent nie zostanie zainstalowane i nie będzie to miało takiego oddziaływania. W przypadku tych maszyn Zobacz zalecenie "Rozwiązywanie problemów z kondycją agenta monitorowania na komputerach", aby rozwiązać problemy z instalacją agenta na tych maszynach

 Aby uzyskać więcej informacji, zobacz następną sekcję [co się stanie, jeśli System Center Operations Manager lub Agent usługi OMS Direct jest już zainstalowany na mojej maszynie wirtualnej?](#scomomsinstalled)

### Co się stanie, jeśli Agent System Center Operations Manager jest już zainstalowany na mojej maszynie wirtualnej?<a name="scomomsinstalled"></a>
Program Security Center zainstaluje rozszerzenie Microsoft Monitoring Agent obok istniejącego agenta System Center Operations Manager. Istniejący agent będzie kontynuował raportowanie do serwera System Center Operations Manager w normalny sposób. Należy pamiętać, że Agent Operations Manager i Microsoft Monitoring Agent współdzielą typowe biblioteki uruchomieniowe, które zostaną zaktualizowane do najnowszej wersji w trakcie tego procesu. Uwaga — Jeśli jest zainstalowana wersja 2012 agenta Operations Manager, nie włączaj automatycznej aprowizacji (możliwości zarządzania mogą zostać utracone, gdy serwer Operations Manager jest również w wersji 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaki jest wpływ usunięcia tych rozszerzeń?
Jeśli usuniesz rozszerzenie Microsoft Monitoring, Security Center nie będzie w stanie zbierać danych zabezpieczeń z maszyny wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. W ciągu 24 godzin Security Center określa, że maszyna wirtualna nie ma rozszerzenia i ponownie instaluje rozszerzenie.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak mogę zatrzymać automatyczną instalację agenta i tworzenie obszaru roboczego?
Automatyczne Inicjowanie obsługi dla subskrypcji w zasadach zabezpieczeń można wyłączyć, ale nie jest to zalecane. Wyłączenie automatycznych limitów aprowizacji Security Center zaleceń i alertów. Aby wyłączyć automatyczne Inicjowanie obsługi:

1. Jeśli subskrypcja jest skonfigurowana dla warstwy Standardowa, Otwórz zasady zabezpieczeń dla tej subskrypcji i wybierz warstwę **bezpłatna** .

   ![Warstwa cenowa][1]

2. Następnie wyłącz automatyczne Inicjowanie obsługi, wybierając pozycję **wyłączone** na stronie **zasady zabezpieczeń — zbieranie danych** .
   ![Zbieranie danych][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Czy należy zrezygnować z automatycznej instalacji agenta i tworzenia obszaru roboczego?

> [!NOTE]
> Pamiętaj, aby przejrzeć sekcje [, jakie są implikacje rezygnacji?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) i [zalecanych czynności](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) , jeśli zdecydujesz się zrezygnować z automatycznej aprowizacji.
>
>

Możesz zrezygnować z automatycznej aprowizacji, jeśli ma to zastosowanie:

- Automatyczna instalacja agenta Security Center ma zastosowanie do całej subskrypcji. Nie można zastosować instalacji automatycznej do podzestawu maszyn wirtualnych. Jeśli istnieją krytyczne maszyny wirtualne, których nie można zainstalować przy użyciu Microsoft Monitoring Agent, należy zrezygnować z automatycznej aprowizacji.
- Instalacja rozszerzenia Microsoft Monitoring Agent (MMA) aktualizuje wersję agenta. Dotyczy to zarówno agenta bezpośredniego, jak i agenta System Center Operations Manager (w tym drugim, Operations Manager i MMA udostępniania wspólnych bibliotek środowiska uruchomieniowego, które zostaną zaktualizowane w procesie). Jeśli zainstalowany agent Operations Manager jest w wersji 2012 i zostanie uaktualniony, możliwości zarządzania mogą zostać utracone, gdy serwer Operations Manager jest również w wersji 2012. Rozważ rezygnację z automatycznej aprowizacji, jeśli zainstalowany agent Operations Manager jest w wersji 2012.
- Jeśli masz niestandardowy obszar roboczy poza subskrypcją (scentralizowany obszar roboczy), należy zrezygnować z automatycznej aprowizacji. Możesz ręcznie zainstalować rozszerzenie Microsoft Monitoring Agent i połączyć je z obszarem roboczym bez Security Center zastępowania połączenia.
- Jeśli chcesz uniknąć tworzenia wielu obszarów roboczych na subskrypcję i masz własny niestandardowy obszar roboczy w ramach subskrypcji, będziesz mieć dwie opcje:

   1. Możesz zrezygnować z automatycznej aprowizacji. Po migracji Ustaw domyślne ustawienia obszaru roboczego zgodnie z opisem w artykule [Jak mogę użyć istniejącego obszaru roboczego log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Można też zezwolić na ukończenie migracji, Microsoft Monitoring Agent do zainstalowania na maszynach wirtualnych oraz maszyn wirtualnych połączonych z utworzonym obszarem roboczym. Następnie wybierz własny niestandardowy obszar roboczy przez ustawienie domyślnego ustawienia obszaru roboczego z ustawieniem w celu ponownego skonfigurowania już zainstalowanych agentów. Aby uzyskać więcej informacji, zobacz [Jak mogę korzystać z istniejącego obszaru roboczego log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jakie są implikacje rezygnacji z automatycznej aprowizacji?
Po zakończeniu migracji Security Center nie może zbierać danych zabezpieczeń z maszyny wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. Jeśli zrezygnujesz z programu, zainstaluj Microsoft Monitoring Agent ręcznie. Przed [rezygnacją Zobacz zalecane kroki](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jakie zalecane kroki należy wykonać w przypadku rezygnacji z automatycznej aprowizacji?

Ręcznie zainstaluj rozszerzenie Microsoft Monitoring Agent, aby Security Center mógł zbierać dane dotyczące zabezpieczeń z maszyn wirtualnych i udostępniać zalecenia i alerty. Aby uzyskać wskazówki dotyczące instalacji, zobacz [Instalacja agenta dla maszyny wirtualnej z systemem Windows](../virtual-machines/extensions/oms-windows.md) lub [instalacji agenta dla maszyny wirtualnej z systemem Linux](../virtual-machines/extensions/oms-linux.md) .

Możesz połączyć agenta z dowolnym istniejącym obszarem roboczym lub Security Center utworzonym obszarem roboczym. Jeśli w niestandardowym obszarze roboczym nie włączono rozwiązań "Security" lub "SecurityCenterFree", należy zastosować rozwiązanie. Aby zastosować tę opcję, wybierz niestandardowy obszar roboczy lub subskrypcję i Zastosuj warstwę cenową na stronie **zasady zabezpieczeń — warstwa cenowa** .

   ![Warstwa cenowa][1]

Security Center włączy poprawne rozwiązanie w obszarze roboczym w oparciu o wybraną warstwę cenową.

### Jak mogę usunąć rozszerzenia pakietu OMS zainstalowane przez program Security Center?<a name="remove-oms"></a>
Microsoft Monitoring Agent można usunąć ręcznie. Nie jest to zalecane, ponieważ ogranicza Security Center zaleceń i alertów.

> [!NOTE]
> Jeśli zbieranie danych jest włączone, Security Center zainstaluje agenta po jego usunięciu.  Należy wyłączyć zbieranie danych przed ręcznym usunięciem agenta. Zobacz Jak mogę Zatrzymaj automatyczną instalację agenta i tworzenie obszaru roboczego? instrukcje dotyczące wyłączania zbierania danych.
>
>

Aby ręcznie usunąć agenta:

1.  W portalu Otwórz **log Analytics**.
2.  Na stronie Log Analytics wybierz obszar roboczy:
3.  Wybierz Maszyny wirtualne, które nie mają być monitorowane, i wybierz pozycję **Rozłącz**.

   ![Usuwanie agenta][3]

> [!NOTE]
> Jeśli maszyna wirtualna z systemem Linux ma już agenta pakietu OMS bez rozszerzenia, usunięcie tego rozszerzenia spowoduje również jego ponowne zainstalowanie.
>
>
### <a name="how-do-i-disable-data-collection"></a>Jak mogę wyłączyć zbieranie danych?
Automatyczne Inicjowanie obsługi jest domyślnie wyłączone. Automatyczne Inicjowanie obsługi można wyłączyć z zasobów w dowolnym momencie, wyłączając to ustawienie w zasadach zabezpieczeń. Automatyczna obsługa administracyjna jest zdecydowanie zalecana w celu uzyskania alertów zabezpieczeń i zaleceń dotyczących aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu Endpoint Protection.

Aby wyłączyć zbieranie danych, [Zaloguj się do Azure Portal](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Security Center**, a następnie wybierz pozycję **Wybierz zasady**. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację. Po wybraniu zasad zabezpieczeń subskrypcji zostanie otwarte **zbieranie danych** . W obszarze **autoinicjowanie obsługi**wybierz pozycję **wyłączone**.

### <a name="how-do-i-enable-data-collection"></a>Jak mogę włączyć zbieranie danych?
Zbieranie danych dla subskrypcji platformy Azure można włączyć w zasadach zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do Azure Portal](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Security Center**, a następnie wybierz pozycję **zasady zabezpieczeń**. Wybierz subskrypcję, dla której chcesz włączyć automatyczną obsługę administracyjną. Po wybraniu zasad zabezpieczeń subskrypcji zostanie otwarte **zbieranie danych** . W obszarze **autoinicjowanie obsługi**wybierz pozycję **włączone**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie, gdy zbieranie danych jest włączone?
Gdy włączona jest Automatyczna obsługa administracyjna, program Security Center Inicjuje obsługę Microsoft Monitoring Agent we wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Zalecana jest Automatyczna obsługa administracyjna, ale jest również dostępna ręczna instalacja agenta. [Dowiedz się, jak zainstalować rozszerzenie programu Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent włącza zdarzenie tworzenia procesu 4688 i pole *wiersza polecenia* wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane przez usługi wykrywania Security Center. Aby uzyskać więcej informacji na temat szczegółów zarejestrowanych dla każdego nowego procesu, zobacz [Opis pól w 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent zbiera również zdarzenia 4688 utworzone na maszynie wirtualnej i zapisuje je w wyszukiwaniu.

Agent umożliwia również zbieranie danych na potrzeby [adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md), Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. Te zasady spowodują generowanie przez funkcję AppLocker zdarzeń, które następnie są zbierane i wykorzystywane przez Security Center. Należy pamiętać, że te zasady nie zostaną skonfigurowane na wszystkich komputerach, na których już skonfigurowano zasady funkcji AppLocker. 

Gdy Security Center wykryje podejrzane działanie na maszynie wirtualnej, klient zostanie powiadomiony pocztą e-mail, jeśli podano [Informacje o kontakcie zabezpieczeń](security-center-provide-security-contact-details.md) . Alert jest również widoczny na pulpicie nawigacyjnym alertów zabezpieczeń Security Center.

### <a name="will-security-center-work-using-an-oms-gateway"></a>Czy Security Center będzie działała przy użyciu bramy pakietu OMS?
Tak. Azure Security Center wykorzystuje Azure Monitor do zbierania danych z maszyn wirtualnych i serwerów platformy Azure przy użyciu Microsoft Monitoring Agent.
Aby zebrać dane, każda maszyna wirtualna i serwer muszą łączyć się z Internetem przy użyciu protokołu HTTPS. Połączenie może być bezpośrednie, przy użyciu serwera proxy lub przez [bramę pakietu OMS](../azure-monitor/platform/gateway.md).

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Czy Agent monitorowania ma wpływ na wydajność moich serwerów?
Agent zużywa nominalną ilość zasobów systemowych i powinien mieć niewielki wpływ na wydajność. Aby uzyskać więcej informacji na temat wpływu na wydajność oraz agenta i rozszerzenia, zobacz [Przewodnik planowania i](security-center-planning-and-operations-guide.md#data-collection-and-storage)obsługi.

### <a name="where-is-my-data-stored"></a>Gdzie są przechowywane moje dane?
Dane zbierane z tego agenta są przechowywane w istniejącym obszarze roboczym Log Analytics skojarzonym z subskrypcją lub nowym obszarem roboczym. Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych](security-center-data-security.md).

## Istniejący Azure Monitor dzienniki klientów<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Czy Security Center zastąpić wszystkie istniejące połączenia między maszynami wirtualnymi i obszarami roboczymi?
Jeśli maszyna wirtualna ma już zainstalowaną Microsoft Monitoring Agent jako rozszerzenie platformy Azure, Security Center nie zastąpi istniejącego połączenia z obszarem roboczym. Zamiast tego Security Center używa istniejącego obszaru roboczego. Maszyna wirtualna będzie chroniona pod warunkiem, że rozwiązanie "Security" lub "SecurityCenterFree" zostało zainstalowane w obszarze roboczym, w którym jest raportowane. 

Rozwiązanie Security Center jest instalowane w obszarze roboczym wybranym na ekranie zbierania danych, jeśli nie istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych. Dodanie rozwiązania jest automatycznie wdrażane domyślnie dla wszystkich agentów systemu Windows i Linux połączonych z obszarem roboczym Log Analytics. [Określanie celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres do rozwiązań.

Jeśli Microsoft Monitoring Agent jest zainstalowana bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center nie zainstaluje Microsoft Monitoring Agent i monitorowanie zabezpieczeń jest ograniczone.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Czy Security Center instalować rozwiązania na moich istniejących Log Analytics obszarach roboczych? Jakie są implikacje rozliczeń?
Gdy Security Center określa, że maszyna wirtualna jest już połączona z utworzonym obszarem roboczym, Security Center umożliwia korzystanie z rozwiązań w tym obszarze roboczym zgodnie z warstwą cenową. Rozwiązania są stosowane tylko do odpowiednich maszyn wirtualnych platformy Azure, za pośrednictwem [określania wartości docelowej rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md), więc rozliczenia pozostają takie same.

- **Warstwa Bezpłatna** — Security Center instaluje rozwiązanie "SecurityCenterFree" w obszarze roboczym. Nie zostanie naliczona opłata za warstwę bezpłatna.
- **Warstwa standardowa** — Security Center instaluje rozwiązanie "zabezpieczenia" w obszarze roboczym.

   ![Rozwiązania w domyślnym obszarze roboczym][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Mam już obszary robocze w moim środowisku, czy mogę ich używać do zbierania danych zabezpieczeń?
Jeśli maszyna wirtualna ma już zainstalowaną Microsoft Monitoring Agent jako rozszerzenie platformy Azure, Security Center używa istniejącego połączonego obszaru roboczego. Rozwiązanie Security Center jest zainstalowane w obszarze roboczym, jeśli nie istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych za pośrednictwem [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md).

Gdy Security Center instaluje Microsoft Monitoring Agent na maszynach wirtualnych, używa domyślnych obszarów roboczych utworzonych przez Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Mam już rozwiązanie zabezpieczeń w obszarze Moje obszary robocze. Jakie są implikacje rozliczeń?
Rozwiązanie Security & Audit służy do włączania Security Center funkcji warstwy standardowej dla maszyn wirtualnych platformy Azure. Jeśli rozwiązanie Security & Audit jest już zainstalowane w obszarze roboczym, Security Center używa istniejącego rozwiązania. Nie wprowadzono zmian w rozliczeniach.

## <a name="using-azure-security-center"></a>Używanie Azure Security Center
### <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?
Zasady zabezpieczeń definiują zestaw formantów, które są zalecane dla zasobów w ramach określonej subskrypcji. W Azure Security Center definiujesz zasady dla subskrypcji platformy Azure zgodnie z wymaganiami firmy dotyczącymi zabezpieczeń oraz typem aplikacji lub poufnością danych w poszczególnych subskrypcjach.

Zasady zabezpieczeń włączone w Azure Security Center na dysku zalecenia dotyczące zabezpieczeń i monitorowania. Aby dowiedzieć się więcej na temat zasad zabezpieczeń, zobacz [monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kto może zmodyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, musisz mieć uprawnienia administratora zabezpieczeń lub właściciela lub współautora subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Co to jest zalecenie dotyczące zabezpieczeń?
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Po zidentyfikowaniu potencjalnych luk w zabezpieczeniach są tworzone zalecenia. Zalecenia przeprowadzą Cię przez proces konfigurowania wymaganego formantu. Oto przykłady:

* Inicjowanie obsługi programu chroniącego przed złośliwym oprogramowaniem w celu identyfikowania i usuwania złośliwego oprogramowania
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i reguły sterujące ruchem do maszyn wirtualnych
* Inicjowanie obsługi zapory aplikacji sieci Web w celu ułatwienia obrony przed atakami ukierunkowanymi na aplikacje sieci Web
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Tutaj są wyświetlane tylko zalecenia włączone w zasadach zabezpieczeń.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak można zobaczyć bieżący stan zabezpieczeń moich zasobów platformy Azure?
Na stronie **przegląd Security Centeru** są wyświetlane ogólne stan zabezpieczeń środowiska, podzielone na dane dotyczące zasobów obliczeniowych, sieci, magazynu & i aplikacji. Każdy typ zasobu ma wskaźnik pokazujący, czy zidentyfikowano potencjalne luki w zabezpieczeniach. Kliknięcie każdego kafelka powoduje wyświetlenie listy problemów dotyczących zabezpieczeń zidentyfikowanych przez Security Center oraz spisu zasobów w ramach subskrypcji.

### <a name="what-triggers-a-security-alert"></a>Co wyzwala alert zabezpieczeń?
Azure Security Center automatycznie gromadzi, analizuje i odrzuca dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak oprogramowanie chroniące przed złośliwym kodem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Wykryto zaawansowane złośliwe oprogramowanie korzystające z raportowania błędów systemu Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń z zintegrowanych rozwiązań zabezpieczeń partnerów, takich jak zapory chroniące przed złośliwym oprogramowaniem lub aplikacje sieci Web

### Dlaczego są zmieniane wartości ocen? <a name="secure-score-faq"></a>
Od lutego 2019 Security Center skorygowany wynik kilku zaleceń w celu lepszego dopasowania ich ważności. W wyniku tej korekty mogą wystąpić zmiany ogólnych, bezpiecznych wartości oceny.  Aby uzyskać więcej informacji na temat zabezpieczeń oceny, zobacz [Secure Score calculation](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożeniami wykrytymi przez centrum Microsoft Security Response Center a Azure Security Centerymi
Centrum Microsoft Security Response Center (MSRC) wykonuje wybór opcji monitorowanie zabezpieczeń sieci i infrastruktury platformy Azure, a następnie odbiera analizę zagrożeń i reklamacji nadużycia od stron trzecich. Gdy centrum MSRC uzyska dostęp do danych klienta przez nielegalną lub nieautoryzowaną stronę albo że klient korzystający z systemu Azure nie spełnia warunków akceptowalnego użycia, Menedżer zdarzeń zabezpieczeń powiadamia klienta. Powiadomienie zwykle odbywa się przez wysłanie wiadomości e-mail do kontaktów zabezpieczeń określonych w Azure Security Center lub właściciela subskrypcji platformy Azure, jeśli nie określono kontaktu zabezpieczeń.

Security Center to usługa platformy Azure, która stale monitoruje środowisko platformy Azure klienta i stosuje analizę w celu automatycznego wykrywania szerokiego zakresu potencjalnie złośliwych działań. Te wykrycia są rozłączone jako alerty zabezpieczeń na pulpicie nawigacyjnym Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Które zasoby platformy Azure są monitorowane przez Azure Security Center?
Azure Security Center monitoruje następujące zasoby platformy Azure:

* Maszyny wirtualne (z uwzględnieniem [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtual Machine Scale Sets
* Sieci wirtualne platformy Azure
* Usługa Azure SQL
* Konto usługi Azure Storage
* Web Apps platformy Azure (w [App Service Environment](../app-service/environment/intro.md))
* Rozwiązania partnerskie zintegrowane z subskrypcją platformy Azure, taką jak Zapora aplikacji sieci Web na maszynach wirtualnych i na App Service Environment

Ponadto komputery z systemem innym niż Azure (łącznie z lokalnymi) mogą być monitorowane przez Azure Security Center (obsługiwane są zarówno [komputery Windows](./quick-onboard-windows-computer.md) , jak i komputery z systemem [Linux](./quick-onboard-linux-computer.md) )

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?
Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych utworzonych przy użyciu zarówno [klasycznych, jak i Menedżer zasobów modeli wdrażania](../azure-classic-rm.md).

Zobacz [obsługiwane platformy w Azure Security Center](security-center-os-coverage.md) , aby uzyskać listę obsługiwanych platform.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego nie Azure Security Center rozpoznać rozwiązania chroniącego przed złośliwym kodem uruchomionego na maszynie wirtualnej platformy Azure?
Azure Security Center ma wgląd w oprogramowanie chroniące przed złośliwym kodem instalowany za pomocą rozszerzeń platformy Azure. Na przykład program Security Center nie może wykryć ochrony przed złośliwym kodem, która została wstępnie zainstalowana na dostarczonym obrazie, lub jeśli zainstalowano oprogramowanie chroniące przed złośliwym oprogramowaniem na maszynach wirtualnych przy użyciu własnych procesów (takich jak systemy zarządzania konfiguracją).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego otrzymuję komunikat "brakujące dane skanowania" dla mojej maszyny wirtualnej?
Ten komunikat pojawia się, gdy dla maszyny wirtualnej nie ma żadnych danych skanowania. Po włączeniu funkcji zbierania danych w usłudze Azure Security Center wypełnienie danych skanowania może zająć trochę czasu (mniej niż godzinę). Po początkowym wypełnieniu danych skanowania możesz otrzymać taki komunikat, ponieważ w ogóle nie ma danych skanowania lub nie ma ostatnich danych skanowania. Skanowanie nie wypełnia się dla zatrzymanej maszyny wirtualnej. Ten komunikat może również zostać wyświetlony, jeśli dane skanowania nie zostały ostatnio zapełnione (zgodnie z zasadami przechowywania dla agenta systemu Windows, którego wartość domyślna to 30 dni).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często program Security Center skanuje w poszukiwaniu luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu oraz problemów z programem Endpoint Protection?
Poniżej przedstawiono czasy opóźnienia Security Center skanowania luk w zabezpieczeniach, aktualizacji i problemów:

- Konfiguracje zabezpieczeń systemu operacyjnego — dane są aktualizowane w ciągu 48 godzin
- Aktualizacje systemu — dane są aktualizowane w ciągu 24 godzin
- Problemy Endpoint Protection — dane są aktualizowane w ciągu 8 godzin

Program Security Center zwykle skanuje nowe dane co godzinę i odświeża odpowiednio zalecenia. 

> [!NOTE]
> Security Center używa Microsoft Monitoring Agent do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center migracji platformy](security-center-platform-migration.md).
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego otrzymuję komunikat "Brak agenta maszyny wirtualnej"?
Aby można było włączyć zbieranie danych, Agent maszyny wirtualnej musi być zainstalowany na maszynach wirtualnych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Aby uzyskać informacje na temat sposobu instalowania agenta maszyny wirtualnej na innych maszynach wirtualnych, zobacz wpis w blogu [Agent i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
