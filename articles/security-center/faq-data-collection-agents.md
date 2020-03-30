---
title: Usługa Azure Security Center — często zadawane pytania dotyczące zbierania danych i agentów
description: Często zadawane pytania dotyczące gromadzenia danych, agentów i obszarów roboczych usługi Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
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
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245475"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Często zadawane pytania — pytania dotyczące gromadzenia danych, agentów i obszarów roboczych

Usługa Security Center zbiera dane z maszyn wirtualnych platformy Azure (maszyn wirtualnych), zestawów skalowania maszyn wirtualnych, kontenerów IaaS i komputerów innych niż platformy Azure (w tym komputerów lokalnych) w celu monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Czy naliczane są naliczane dane dziennika usługi Azure Monitor w obszarach roboczych utworzonych przez usługę Security Center?

Nie. Obszary robocze utworzone przez usługę Security Center, skonfigurowane dla dzienników usługi Azure Monitor na węzeł rozliczeń, nie ponoszą opłat dziennika usługi Azure Monitor. Rozliczenia w uchowach Security Center są zawsze oparte na zasadach zabezpieczeń centrum zabezpieczeń i rozwiązaniach zainstalowanych w obszarze roboczym:

- **Warstwa bezpłatna** — usługa Security Center włącza rozwiązanie "SecurityCenterFree" w domyślnym obszarze roboczym. Nie zostanie naliczona naliczona naliczona warstwa Bezpłatna.

- **Warstwa standardowa** — usługa Security Center włącza rozwiązanie "Zabezpieczenia" w domyślnym obszarze roboczym.

Aby uzyskać więcej informacji na temat cen, zobacz [Ustalanie cen w centrum zabezpieczeń](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Warstwa cenowa analizy dzienników obszarów roboczych utworzonych przez usługa Security Center nie ma wpływu na rozliczenia usługi Security Center.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co kwalifikuje maszynę wirtualną do automatycznego inicjowania obsługi administracyjnej instalacji agenta monitorowania firmy Microsoft?

Maszyny wirtualne IaaS systemu Windows lub Linux kwalifikują się, jeśli:

- Rozszerzenie programu Microsoft Monitoring Agent nie jest obecnie zainstalowane na maszynie wirtualnej.
- Maszyna wirtualna jest w stanie uruchomionym.
- Zainstalowany jest [agent maszyny wirtualnej platformy](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) Windows lub Linux Azure.
- Maszyna wirtualna nie jest używana jako urządzenie, takie jak zapora aplikacji sieci web lub zapora nowej generacji.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Czy można usunąć domyślne obszary robocze utworzone przez Centrum zabezpieczeń?

**Nie zaleca się usuwania domyślnego obszaru roboczego.** Usługa Security Center używa domyślnych obszarów roboczych do przechowywania danych zabezpieczeń z maszyn wirtualnych. Jeśli usuniesz obszar roboczy, Usługa Security Center nie będzie mogła zbierać tych danych, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne.

Aby odzyskać, usuń agenta monitorowania firmy Microsoft na maszynach wirtualnych połączonych z usuniętym obszarem roboczym. Usługa Security Center ponownie instaluje agenta i tworzy nowe domyślne obszary robocze.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak mogę korzystać z istniejącego obszaru roboczego usługi Log Analytics?

Można wybrać istniejący obszar roboczy usługi Log Analytics do przechowywania danych zebranych przez Centrum zabezpieczeń. Aby użyć istniejącego obszaru roboczego usługi Log Analytics:

- Obszar roboczy musi być skojarzony z wybraną subskrypcją platformy Azure.
- Aby uzyskać dostęp do obszaru roboczego, musisz mieć co najmniej uprawnienia do odczytu.

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **Zasady zabezpieczeń — zbieranie danych**wybierz pozycję Użyj innego obszaru **roboczego**.

    ![Korzystanie z innego obszaru roboczego][4]

1. Z menu rozwijanego wybierz obszar roboczy do przechowywania zebranych danych.

    > [!NOTE]
    > W menu rozwijanym są wyświetlane tylko obszary robocze, do których masz dostęp i które znajdują się w subskrypcji platformy Azure.

1. Wybierz **pozycję Zapisz**. Zostaniesz zapytany, czy chcesz ponownie skonfigurować monitorowane maszyny wirtualne.

    - Wybierz **opcję Nie,** jeśli chcesz, aby nowe ustawienia obszaru roboczego **były stosowane tylko na nowych maszynach wirtualnych**. Nowe ustawienia obszaru roboczego dotyczą tylko nowych instalacji agenta; nowo wykryte maszyny wirtualne, które nie mają zainstalowanego agenta monitorowania firmy Microsoft.
    - Wybierz **opcję Tak,** jeśli chcesz, aby nowe ustawienia obszaru roboczego **były stosowane na wszystkich maszynach wirtualnych**. Ponadto każda maszyna wirtualna połączona z utworzonym obszarem roboczym centrum zabezpieczeń jest ponownie połączona z nowym docelowym obszarem roboczym.

    > [!NOTE]
    > Jeśli wybierzesz **opcję Tak,** nie usuwaj żadnych obszarów roboczych utworzonych przez Centrum zabezpieczeń, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja kończy się niepowodzeniem, jeśli obszar roboczy zostanie usunięty zbyt wcześnie.

    - Aby anulować operację, wybierz pozycję **Anuluj**.

## <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co zrobić, jeśli agent monitorujący firmy Microsoft został już zainstalowany jako rozszerzenie na maszynie wirtualnej?<a name="mmaextensioninstalled"></a>

Gdy agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko do jednego obszaru roboczego. Usługa Security Center nie zastępuje istniejących połączeń z obszarami roboczymi użytkowników. Usługa Security Center będzie przechowywać dane zabezpieczeń z maszyny Wirtualnej w obszarze roboczym, który jest już podłączony, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "SecurityCenterFree". Usługa Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.

Aby uzyskać więcej informacji, zobacz [Automatyczne inicjowanie obsługi administracyjnej w przypadku istniejącej instalacji agenta.](security-center-enable-data-collection.md#preexisting)



## <a name="what-if-a-microsoft-monitoring-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Co zrobić, jeśli agent monitorujący firmy Microsoft jest zainstalowany bezpośrednio na komputerze, ale nie jako rozszerzenie (agent bezpośredni)?<a name="directagentinstalled"></a>

Jeśli agent monitorowania firmy Microsoft jest zainstalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Usługa Security Center zainstaluje rozszerzenie agenta monitorowania firmy Microsoft i może uaktualnić agenta monitorowania firmy Microsoft do najnowszej wersji.

Zainstalowany agent będzie nadal raportował do swoich już skonfigurowanych obszarów roboczych, a ponadto raportuje do obszaru roboczego skonfigurowanego w Centrum zabezpieczeń (Multi-homing jest obsługiwany na komputerach z systemem Windows).

Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie domyślnym obszarem roboczym usługi Security Center), należy zainstalować na nim rozwiązanie "Security/"SecurityCenterFree", aby usługa Security Center rozpoczęła przetwarzanie zdarzeń z maszyn wirtualnych i komputerów raportujących do tego obszaru roboczego.

W przypadku komputerów z systemem Linux agent multi-homing nie jest jeszcze obsługiwany — w związku z tym, jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne inicjowanie obsługi administracyjnej nie nastąpi i konfiguracja komputera nie zostanie zmieniona.

W przypadku istniejących komputerów w ramach subskrypcji wbudowanych w usługę Security Center przed 17 marca 2019 r., gdy zostanie wykryty istniejący agent, rozszerzenie programu Microsoft Monitoring Agent nie zostanie zainstalowane i nie zostanie to naruszone. W przypadku tych maszyn zobacz zalecenie "Rozwiązywanie problemów ze zdrowiem agenta monitorowania na komputerach", aby rozwiązać problemy z instalacją agenta na tych komputerach

Aby uzyskać więcej informacji, zobacz następną sekcję [Co się stanie, jeśli program System Center Operations Manager lub agent bezpośredni systemu OMS jest już zainstalowany na mojej maszynie wirtualnej?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Co zrobić, jeśli agent programu Operations Manager programu System Center jest już zainstalowany na mojej maszynie wirtualnej?<a name="scomomsinstalled"></a>

Centrum zabezpieczeń zainstaluje rozszerzenie programu Microsoft Monitoring Agent obok istniejącego agenta programu Operations Manager w programie System Center. Istniejący agent będzie normalnie raportować serwer programu System Center Operations Manager. Należy zauważyć, że agent programu Operations Manager i agent monitorujący firmy Microsoft współużytkują wspólne biblioteki w czasie wykonywania, które zostaną zaktualizowane do najnowszej wersji podczas tego procesu. Uwaga - Jeśli zainstalowana jest wersja 2012 agenta programu Operations Manager, nie należy włączać automatycznego inicjowania obsługi administracyjnej (możliwości zarządzania mogą zostać utracone, gdy serwer programu Operations Manager jest również w wersji 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaki jest wpływ usuwania tych rozszerzeń?

Jeśli usuniesz rozszerzenie monitorowania firmy Microsoft, Usługa Security Center nie będzie mogła zbierać danych zabezpieczeń z maszyny Wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. W ciągu 24 godzin usługa Security Center określa, że na maszynie Wirtualnej brakuje rozszerzenia i ponownie instaluje rozszerzenie.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak zatrzymać automatyczną instalację agenta i tworzenie obszaru roboczego?

Automatyczne inicjowanie obsługi administracyjnej subskrypcji można wyłączyć w zasadach zabezpieczeń, ale nie jest to zalecane. Wyłączenie automatycznego inicjowania obsługi administracyjnej ogranicza zalecenia i alerty w centrum zabezpieczeń. Aby wyłączyć automatyczne inicjowanie obsługi administracyjnej:

1. Jeśli subskrypcja jest skonfigurowana dla warstwy Standardowa, otwórz zasady zabezpieczeń dla tej subskrypcji i wybierz warstwę **bezpłatną.**

   ![Warstwa cenowa][1]

1. Następnie wyłącz automatyczne inicjowanie obsługi administracyjnej, wybierając **pozycję Wyłączone** na stronie Zasady zabezpieczeń — **zbieranie danych.**
   ![Zbieranie danych][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Czy należy zrezygnować z automatycznej instalacji agenta i tworzenia obszaru roboczego?

> [!NOTE]
> Zapoznaj się z sekcjami [Jakie są konsekwencje rezygnacji?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) [recommended steps when opting out](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)

Możesz zrezygnować z automatycznego inicjowania obsługi administracyjnej, jeśli dotyczy cię:

- Automatyczna instalacja agenta przez program Security Center dotyczy całej subskrypcji. Nie można zastosować instalacji automatycznej do podzbioru maszyn wirtualnych. Jeśli istnieją krytyczne maszyny wirtualne, których nie można zainstalować za pomocą agenta monitorowania firmy Microsoft, należy zrezygnować z automatycznego inicjowania obsługi administracyjnej.
- Instalacja rozszerzenia programu Microsoft Monitoring Agent (MMA) aktualizuje wersję agenta. Dotyczy to agenta bezpośredniego i agenta programu Operations Manager w centrum systemu (w tym ostatnim program Operations Manager i MMA współużytkują wspólne biblioteki wykonawcze , które będą aktualizowane w procesie). Jeśli zainstalowany agent programu Operations Manager jest w wersji 2012 i jest uaktualniany, możliwości zarządzania mogą zostać utracone, gdy serwer programu Operations Manager jest również w wersji 2012. Należy rozważyć rezygnację z automatycznego inicjowania obsługi administracyjnej, jeśli zainstalowany agent programu Operations Manager jest w wersji 2012.
- Jeśli masz niestandardowy obszar roboczy zewnętrzny do subskrypcji (scentralizowanego obszaru roboczego), należy zrezygnować z automatycznego inicjowania obsługi administracyjnej. Rozszerzenie programu Microsoft Monitoring Agent można zainstalować ręcznie i połączyć go z obszarem roboczym bez zastępowania połączenia przez centrum zabezpieczeń.
- Jeśli chcesz uniknąć tworzenia wielu obszarów roboczych na subskrypcję i masz własny niestandardowy obszar roboczy w ramach subskrypcji, masz dwie opcje:

   1. Możesz zrezygnować z automatycznego inicjowania obsługi administracyjnej. Po migracji ustaw domyślne ustawienia obszaru roboczego zgodnie z opisem w [sekcji Jak mogę korzystać z istniejącego obszaru roboczego usługi Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Można też zezwolić na zakończenie migracji, zainstalowanie agenta monitorowania firmy Microsoft na maszynach wirtualnych oraz maszyny wirtualne połączone z utworzonym obszarem roboczym. Następnie wybierz własny niestandardowy obszar roboczy, ustawiając domyślne ustawienie obszaru roboczego z określeniem ponownego konfigurowania już zainstalowanych agentów. Aby uzyskać więcej informacji, zobacz [Jak korzystać z istniejącego obszaru roboczego usługi Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jakie są konsekwencje rezygnacji z automatycznego udostępniania administracyjnego?

Po zakończeniu migracji usługa Security Center nie może zbierać danych zabezpieczeń z maszyny Wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. Jeśli zrezygnujesz, zainstaluj agenta monitorowania firmy Microsoft ręcznie. Zobacz [zalecane kroki podczas rezygnacji](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jakie są zalecane kroki przy rezygnacji z automatycznego inicjowania obsługi administracyjnej?

Ręcznie zainstaluj rozszerzenie microsoft monitoruj, aby usługa Security Center mogła zbierać dane zabezpieczeń z maszyn wirtualnych oraz dostarczać rekomendacje i alerty. Zobacz [instalację agenta dla maszyny Wirtualnej systemu Windows](../virtual-machines/extensions/oms-windows.md) lub [instalacji agenta dla maszyny Wirtualnej systemu Linux, aby](../virtual-machines/extensions/oms-linux.md) uzyskać wskazówki dotyczące instalacji.

Agenta można połączyć z dowolnym istniejącym niestandardowym obszarem roboczym lub utworzonym w Centrum zabezpieczeń obszarem roboczym. Jeśli niestandardowy obszar roboczy nie ma włączonych rozwiązań "Security" lub "SecurityCenterFree", należy zastosować rozwiązanie. Aby zastosować, wybierz niestandardowy obszar roboczy lub subskrypcję i zastosuj warstwę cenową za pośrednictwem strony **Zasady zabezpieczeń — warstwa cenowa.**

   ![Warstwa cenowa][1]

Usługa Security Center włączy poprawne rozwiązanie w obszarze roboczym na podstawie wybranej warstwy cenowej.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Jak usunąć rozszerzenia systemu OMS zainstalowane przez program Security Center?<a name="remove-oms"></a>

Agent monitorowania firmy Microsoft można usunąć ręcznie. Nie jest to zalecane, ponieważ ogranicza zalecenia i alerty w centrum zabezpieczeń.

> [!NOTE]
> Jeśli zbieranie danych jest włączone, usługa Security Center zainstaluje agenta ponownie po jego usunięciu.  Przed ręcznym usunięciem agenta należy wyłączyć zbieranie danych. Zobacz Jak zatrzymać automatyczną instalację agenta i tworzenie obszaru roboczego? instrukcje dotyczące wyłączania gromadzenia danych.

Aby ręcznie wyjąć agenta:

1.  W portalu otwórz **dzienniki analizy**.

1.  Na stronie Analiza dzienników wybierz obszar roboczy:

1.  Wybierz maszyny wirtualne, których nie chcesz monitorować, i wybierz pozycję **Rozłącz**.

   ![Usuń agenta][3]

> [!NOTE]
> Jeśli maszyna wirtualna z systemem Linux ma już agenta OMS bez rozszerzenia, usunięcie rozszerzenia usuwa agenta i będziesz musiał go ponownie zainstalować.


## <a name="how-do-i-disable-data-collection"></a>Jak wyłączyć zbieranie danych?

Automatyczne inicjowanie obsługi administracyjnej jest domyślnie wyłączone. Automatyczne inicjowanie obsługi administracyjnej z zasobów można wyłączyć w dowolnym momencie, wyłączając to ustawienie w zasadach zabezpieczeń. Automatyczne inicjowanie obsługi administracyjnej jest wysoce zalecane w celu uzyskania alertów zabezpieczeń i zaleceń dotyczących aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i ochrony punktów końcowych.

Aby wyłączyć zbieranie danych, [zaloguj się do portalu Azure](https://portal.azure.com), wybierz pozycję **Przeglądaj,** wybierz pozycję **Centrum zabezpieczeń**i wybierz pozycję **Wybierz zasadę**. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację. Po wybraniu subskrypcji zostanie otwarta zasada zabezpieczeń — zostanie otwarte **zbieranie danych.** W obszarze **Automatyczne inicjowanie obsługi administracyjnej**wybierz pozycję **Wyłączone**.


## <a name="how-do-i-enable-data-collection"></a>Jak włączyć zbieranie danych?

Zbieranie danych dla subskrypcji platformy Azure można włączyć w zasadach zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do portalu Azure](https://portal.azure.com), wybierz pozycję **Przeglądaj,** wybierz pozycję **Centrum zabezpieczeń**i wybierz pozycję Zasady **zabezpieczeń**. Wybierz subskrypcję, która ma włączyć automatyczne inicjowanie obsługi administracyjnej. Po wybraniu subskrypcji zostanie otwarta zasada zabezpieczeń — zostanie otwarte **zbieranie danych.** W obszarze **Automatyczne inicjowanie obsługi administracyjnej**wybierz pozycję **Włączone**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie, gdy zbieranie danych jest włączone?

Gdy automatyczne inicjowanie obsługi administracyjnej jest włączone, usługa Security Center aprowizacji Microsoft Agent monitorowania na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkie nowe, które są tworzone. Zalecana jest automatyczna inicjowanie obsługi administracyjnej, ale dostępna jest również instalacja agenta ręcznego. [Dowiedz się, jak zainstalować rozszerzenie programu Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent włącza zdarzenie tworzenia procesu 4688 i *CommandLine* pole wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez EventLog i monitorowane przez usługi wykrywania usługi usługi security center. Aby uzyskać więcej informacji na temat szczegółów zarejestrowanych dla każdego nowego procesu, zobacz [pola opisu w 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent zbiera również 4688 zdarzeń utworzonych na maszynie wirtualnej i przechowuje je w wyszukiwaniu.

Agent umożliwia również zbieranie danych dla [adaptacyjnych kontroli aplikacji,](security-center-adaptive-application.md)Centrum zabezpieczeń konfiguruje lokalną zasadę zasad ograniczeń oprogramowania w trybie inspekcji, aby umożliwić wszystkim aplikacjom. Ta zasada spowoduje, że funkcja AppLocker będzie generować zdarzenia, które są następnie zbierane i wykorzystywane przez centrum zabezpieczeń. Należy pamiętać, że ta zasada nie będzie skonfigurowana na żadnych komputerach, na których istnieje już skonfigurowana zasada ograniczeń oprogramowania. 

Gdy usługa Security Center wykryje podejrzaną aktywność na maszynie wirtualnej, klient jest powiadamiany pocztą e-mail, jeśli podano [informacje kontaktowe zabezpieczeń.](security-center-provide-security-contact-details.md) Alert jest również widoczny na pulpicie nawigacyjnym alertów zabezpieczeń w urzekania zabezpieczeń w urzekania zabezpieczeń.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Czy usługa Security Center będzie działać przy użyciu bramy oms?

Tak. Usługa Azure Security Center wykorzystuje usługę Azure Monitor do zbierania danych z maszyn wirtualnych i serwerów platformy Azure przy użyciu agenta monitorowania firmy Microsoft.
Aby zebrać dane, każda maszyna wirtualna i serwer muszą łączyć się z Internetem przy użyciu protokołu HTTPS. Połączenie może być bezpośrednie, za pomocą serwera proxy lub za pośrednictwem [bramy OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Czy agent monitorowania wpływa na wydajność moich serwerów?

Agent zużywa nominalną ilość zasobów systemowych i powinien mieć niewielki wpływ na wydajność. Aby uzyskać więcej informacji na temat wpływu na wydajność oraz agenta i rozszerzenia, zobacz [przewodnik planowania i operacji](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Gdzie są przechowywane moje dane?

Dane zebrane z tego agenta są przechowywane w istniejącym obszarze roboczym usługi Log Analytics skojarzonym z subskrypcją lub nowym obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Bezpieczeństwo danych](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png