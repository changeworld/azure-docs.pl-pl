---
title: Azure Security Center często zadawane pytania — zbieranie danych i agenci
description: Często zadawane pytania dotyczące zbierania danych, agentów i obszarów roboczych dla Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245475"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Często zadawane pytania — pytania dotyczące zbierania danych, agentów i obszarów roboczych

Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz komputerów spoza platformy Azure (w tym maszyn lokalnych) do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Czy są naliczane opłaty za dzienniki Azure Monitor w obszarach roboczych utworzonych przez Security Center?

Nie. Obszary robocze utworzone przez Security Center, podczas gdy skonfigurowano do rozliczania Azure Monitor dzienników na węzeł, nie są naliczane opłaty za dzienniki Azure Monitor. Usługa Security Center są zawsze naliczane zasad zabezpieczeń usługa Security Center i rozwiązań zainstalowane w obszarze roboczym:

- **Warstwa Bezpłatna** — Security Center włącza rozwiązanie "SecurityCenterFree" w domyślnym obszarze roboczym. Nie zostanie naliczona opłata za warstwę bezpłatna.

- **Warstwa standardowa** — Security Center włącza rozwiązanie "zabezpieczenia" w domyślnym obszarze roboczym.

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Warstwa cenowa usługi log Analytics dla obszarów roboczych utworzonych przez Security Center nie ma wpływu na Security Center rozliczanie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Co to jest uprawniony Maszynę wirtualną do automatycznej aprowizacji instalacja programu Microsoft Monitoring Agent?

Windows lub maszyn wirtualnych IaaS z systemem Linux kwalifikuje się, jeśli:

- Rozszerzenia Microsoft Monitoring Agent nie jest obecnie zainstalowana na maszynie Wirtualnej.
- Maszyna wirtualna jest w stanie uruchomienia.
- [Agent maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) z systemem Windows lub Linux jest zainstalowany.
- Maszyna wirtualna nie jest używana jako urządzenie, takie jak zapory aplikacji sieci web lub Zapora nowej generacji.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Czy można usunąć domyślne obszary robocze utworzone przez usługę Security Center?

**Nie zaleca się usuwania domyślnego obszaru roboczego.** Usługa Security Center używa domyślne obszary robocze do przechowywania danych zabezpieczeń z maszyn wirtualnych. Jeśli usuniesz obszar roboczy, usługa Security Center nie może zbierać dane i niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne.

Aby odzyskać, Usuń program Microsoft Monitoring Agent na maszynach wirtualnych podłączone do usuniętego obszaru roboczego. Usługa Security Center ponownie instaluje agenta i tworzy nowe domyślne obszary robocze.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak użyć Mój istniejący obszar roboczy usługi Log Analytics?

Możesz wybrać istniejący obszar roboczy usługi Log Analytics do przechowywania danych zbieranych przez usługę Security Center. Aby użyć istniejącego obszaru roboczego usługi Log Analytics:

- Obszar roboczy musi być skojarzony z wybranej subskrypcji platformy Azure.
- Jako minimum użytkownik musi mieć uprawnienia odczytu do dostępu do obszaru roboczego.

Aby wybrać istniejący obszar roboczy usługi Log Analytics:

1. W obszarze **zasady zabezpieczeń — zbieranie danych**wybierz opcję **Użyj innego obszaru roboczego**.

    ![Użyj innego obszaru roboczego][4]

1. Z menu rozwijanego wybierz obszar roboczy do zapisywania zebranych danych.

    > [!NOTE]
    > Ściągnąć menu wyświetlane są tylko obszary robocze, które mają dostęp do i znajdują się w Twojej subskrypcji platformy Azure.

1. Wybierz pozycję **Zapisz**. Zostanie wyświetlony monit o ponowne skonfigurowanie monitorowanych maszyn wirtualnych.

    - Wybierz pozycję **nie** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były **stosowane tylko do nowych maszyn wirtualnych**. Nowe ustawienia obszaru roboczego dotyczą tylko nowe instalacje agentów; odnalezione maszyny wirtualne, które nie mają zainstalowany program Microsoft Monitoring Agent.
    - Wybierz pozycję **tak** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były **stosowane do wszystkich maszyn wirtualnych**. Ponadto każda maszyna wirtualna podłączone do usługi Security Center, obszar roboczy utworzony ponownie nawiązał połączenie nowy docelowy obszar roboczy.

    > [!NOTE]
    > W przypadku wybrania opcji **tak**nie usuwaj żadnych obszarów roboczych utworzonych przez Security Center, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja nie powiedzie się, za wczesne usunięcie obszaru roboczego.

    - Aby anulować operację, wybierz pozycję **Anuluj**.

## Co zrobić, jeśli Microsoft Monitoring Agent zostało już zainstalowane jako rozszerzenie na maszynie wirtualnej?<a name="mmaextensioninstalled"></a>

Gdy Agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko jednego obszaru roboczego. Usługa Security Center nie zastępuje istniejące połączenia do obszarów roboczych użytkownika. Security Center będą przechowywać dane zabezpieczeń z maszyny wirtualnej w obszarze roboczym, który jest już połączony, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "SecurityCenterFree". Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.

Aby uzyskać więcej informacji, zobacz [Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta](security-center-enable-data-collection.md#preexisting).



## Co zrobić, jeśli Microsoft Monitoring Agent jest bezpośrednio zainstalowana na komputerze, ale nie jako rozszerzenie (Agent bezpośredni)?<a name="directagentinstalled"></a>

Jeśli Microsoft Monitoring Agent jest zainstalowana bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center zainstaluje rozszerzenie Microsoft Monitoring Agent i może uaktualnić program Microsoft Monitoring Agent do najnowszej wersji.

Zainstalowany agent będzie kontynuował raportowanie do już skonfigurowanych obszarów roboczych, a ponadto wyśle raport do obszaru roboczego skonfigurowanego w Security Center (wiele multihostingu jest obsługiwana na maszynach z systemem Windows).

Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie Security Center domyślnym obszarem roboczym), należy zainstalować na nim rozwiązanie "Security/" SecurityCenterFree "dla Security Center, aby rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów zgłaszanych do tego obszaru roboczego.

W przypadku maszyn z systemem Linux Agent multihostingu nie jest jeszcze obsługiwany — w związku z tym jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne Inicjowanie obsługi nie zostanie wykonane i konfiguracja maszyny nie zostanie zmieniona.

W przypadku istniejących maszyn w ramach subskrypcji dołączanych do Security Center przed 17 2019 marca, gdy zostanie wykryty istniejący Agent, rozszerzenie Microsoft Monitoring Agent nie zostanie zainstalowane i nie będzie to miało takiego oddziaływania. W przypadku tych maszyn Zobacz zalecenie "Rozwiązywanie problemów z kondycją agenta monitorowania na komputerach", aby rozwiązać problemy z instalacją agenta na tych maszynach

Aby uzyskać więcej informacji, zobacz następną sekcję [co się stanie, jeśli System Center Operations Manager lub Agent usługi OMS Direct jest już zainstalowany na mojej maszynie wirtualnej?](#scomomsinstalled)

## Co zrobić, jeśli Agent System Center Operations Manager jest już zainstalowany na mojej maszynie wirtualnej?<a name="scomomsinstalled"></a>

Program Security Center zainstaluje rozszerzenie Microsoft Monitoring Agent obok istniejącego agenta System Center Operations Manager. Istniejący agent będzie kontynuował raportowanie do serwera System Center Operations Manager w normalny sposób. Należy pamiętać, że Agent Operations Manager i Microsoft Monitoring Agent współdzielą typowe biblioteki uruchomieniowe, które zostaną zaktualizowane do najnowszej wersji w trakcie tego procesu. Uwaga — Jeśli jest zainstalowana wersja 2012 agenta Operations Manager, nie włączaj automatycznej aprowizacji (możliwości zarządzania mogą zostać utracone, gdy serwer Operations Manager jest również w wersji 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaki jest wpływ usunięcia tych rozszerzeń?

Jeśli usuniesz rozszerzenie monitorowania firmy Microsoft, usługa Security Center nie jest w stanie do zbierania danych zabezpieczeń z maszyny Wirtualnej i niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. W ciągu 24 godzin usługa Security Center określa Brak rozszerzenia i spowoduje ponowne zainstalowanie rozszerzenia maszyny Wirtualnej.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak zatrzymać automatyczne agent instalacji i tworzenie obszaru roboczego?

Możesz wyłączyć automatycznej aprowizacji dla subskrypcji w ramach zasad zabezpieczeń, ale nie jest to zalecane. Wyłączenie automatycznej aprowizacji limity zaleceń usługi Security Center i alerty. Aby wyłączyć automatyczną aprowizację:

1. Jeśli subskrypcja jest skonfigurowana dla warstwy Standardowa, Otwórz zasady zabezpieczeń dla tej subskrypcji i wybierz warstwę **bezpłatna** .

   ![Warstwa cenowa][1]

1. Następnie wyłącz automatyczne Inicjowanie obsługi, wybierając pozycję **wyłączone** na stronie **zasady zabezpieczeń — zbieranie danych** .
   ![Zbieranie danych][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Należy zrezygnować z automatyczną instalację agenta i tworzenie obszaru roboczego?

> [!NOTE]
> Pamiętaj, aby przejrzeć sekcje [, jakie są implikacje rezygnacji?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) i [zalecanych czynności](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) , jeśli zdecydujesz się zrezygnować z automatycznej aprowizacji.

Można zrezygnować z automatycznego inicjowania obsługi administracyjnej, jeśli stosuje się do Ciebie:

- Automatyczną instalację agenta przez usługę Security Center ma zastosowanie do całej subskrypcji. Nie można zastosować automatycznej instalacji dla podzbioru maszyn wirtualnych. W przypadku newralgicznych maszyn wirtualnych, których nie można zainstalować za pomocą programu Microsoft Monitoring Agent, należy zrezygnować z automatycznego inicjowania obsługi administracyjnej.
- Instalacja rozszerzenia Microsoft Monitoring Agent (MMA) aktualizuje wersję agenta. Dotyczy to zarówno agenta bezpośredniego, jak i agenta System Center Operations Manager (w tym drugim, Operations Manager i MMA udostępniania wspólnych bibliotek środowiska uruchomieniowego, które zostaną zaktualizowane w procesie). Jeśli zainstalowany agent Operations Manager jest w wersji 2012 i zostanie uaktualniony, możliwości zarządzania mogą zostać utracone, gdy serwer Operations Manager jest również w wersji 2012. Rozważ rezygnację z automatycznej aprowizacji, jeśli zainstalowany agent Operations Manager jest w wersji 2012.
- Jeśli masz niestandardowy obszar roboczy poza subskrypcją (scentralizowany obszar roboczy), należy zrezygnować z automatycznej aprowizacji. Można ręcznie zainstalować rozszerzenia Microsoft Monitoring Agent i połączyć go obszaru roboczego bez usługi Security Center zastępowanie połączenia.
- Jeśli chcesz uniknąć tworzenia wielu obszarów roboczych na subskrypcję i masz własnego niestandardowego obszaru roboczego w ramach subskrypcji, masz dwie opcje:

   1. Użytkownik może zrezygnować z automatycznego inicjowania obsługi administracyjnej. Po migracji Ustaw domyślne ustawienia obszaru roboczego zgodnie z opisem w artykule [Jak mogę użyć istniejącego obszaru roboczego log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Lub możesz zezwolić migracji do ukończenia programu Microsoft Monitoring Agent do zainstalowania na maszynach wirtualnych, i maszyny wirtualne podłączone do utworzonego obszaru roboczego. Następnie wybierz własnego niestandardowego obszaru roboczego przez ustawienie domyślne ustawienie obszaru roboczego przy użyciu zgody na korzystanie z ponownej konfiguracji już zainstalowanych agentów. Aby uzyskać więcej informacji, zobacz [Jak mogę korzystać z istniejącego obszaru roboczego log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jakie są skutki rezygnacji z automatycznej aprowizacji?

Po zakończeniu migracji Security Center nie może zbierać danych zabezpieczeń z maszyny wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. Jeśli zrezygnujesz z programu, zainstaluj Microsoft Monitoring Agent ręcznie. Przed [rezygnacją Zobacz zalecane kroki](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jakie są zalecane czynności w przypadku rezygnacji z automatycznej aprowizacji?

Ręcznie zainstaluj rozszerzenie Microsoft Monitoring Agent, aby Security Center mógł zbierać dane dotyczące zabezpieczeń z maszyn wirtualnych i udostępniać zalecenia i alerty. Aby uzyskać wskazówki dotyczące instalacji, zobacz [Instalacja agenta dla maszyny wirtualnej z systemem Windows](../virtual-machines/extensions/oms-windows.md) lub [instalacji agenta dla maszyny wirtualnej z systemem Linux](../virtual-machines/extensions/oms-linux.md) .

Możesz nawiązać połączenie agenta istniejącego niestandardowego obszaru roboczego lub Centrum zabezpieczeń utworzone obszaru roboczego. Jeśli niestandardowego obszaru roboczego nie ma rozwiązania "Zabezpieczenia" lub "SecurityCenterFree" włączona, należy zastosować rozwiązanie. Aby zastosować tę opcję, wybierz niestandardowy obszar roboczy lub subskrypcję i Zastosuj warstwę cenową na stronie **zasady zabezpieczeń — warstwa cenowa** .

   ![Warstwa cenowa][1]

Usługa Security Center umożliwi rozwiązanie będzie odpowiednie w oparciu o wybranej warstwy cenowej obszaru roboczego.


## Jak mogę usunąć rozszerzenia pakietu OMS zainstalowane przez program Security Center?<a name="remove-oms"></a>

Można ręcznie usunąć program Microsoft Monitoring Agent. Nie jest to zalecane, ponieważ ogranicza zaleceń usługi Security Center i alerty.

> [!NOTE]
> Po włączeniu zbierania danych usługa Security Center będzie ponownie zainstalować agenta po jego usunięciu.  Należy wyłączyć zbieranie danych przed ręczne usunięcie agenta. Zobacz Jak mogę Zatrzymaj automatyczną instalację agenta i tworzenie obszaru roboczego? instrukcje dotyczące wyłączania zbierania danych.

Aby ręcznie usunąć agenta:

1.  W portalu Otwórz **log Analytics**.

1.  Na stronie Log Analytics wybierz obszar roboczy:

1.  Wybierz Maszyny wirtualne, które nie mają być monitorowane, i wybierz pozycję **Rozłącz**.

   ![Usuń agenta][3]

> [!NOTE]
> Jeśli maszyna wirtualna z systemem Linux ma już agenta pakietu OMS bez rozszerzenia, usunięcie tego rozszerzenia spowoduje również konieczność jego ponownego zainstalowania.


## <a name="how-do-i-disable-data-collection"></a>Jak wyłączyć zbieranie danych?

Automatyczna aprowizacja jest domyślnie wyłączona. Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. Automatyczna obsługa administracyjna jest zdecydowanie zalecana w celu uzyskania alertów zabezpieczeń i zaleceń dotyczących aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu Endpoint Protection.

Aby wyłączyć zbieranie danych, [Zaloguj się do Azure Portal](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Security Center**, a następnie wybierz pozycję **Wybierz zasady**. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację. Po wybraniu zasad zabezpieczeń subskrypcji zostanie otwarte **zbieranie danych** . W obszarze **autoinicjowanie obsługi**wybierz pozycję **wyłączone**.


## <a name="how-do-i-enable-data-collection"></a>Jak włączyć zbieranie danych?

Można włączyć zbierania danych dla subskrypcji platformy Azure w ramach zasad zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do Azure Portal](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Security Center**, a następnie wybierz pozycję **zasady zabezpieczeń**. Wybierz subskrypcję, której chcesz włączyć automatyczną aprowizację. Po wybraniu zasad zabezpieczeń subskrypcji zostanie otwarte **zbieranie danych** . W obszarze **autoinicjowanie obsługi**wybierz pozycję **włączone**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie, gdy jest włączone zbieranie danych?

Po włączeniu automatycznej aprowizacji Security Center aprowizuje program Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i wszelkie nowe, które są tworzone. Zalecana jest Automatyczna obsługa administracyjna, ale jest również dostępna ręczna instalacja agenta. [Dowiedz się, jak zainstalować rozszerzenie programu Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent włącza zdarzenie tworzenia procesu 4688 i pole *wiersza polecenia* wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie Wirtualnej są rejestrowane w dzienniku zdarzeń i monitorowana przez Centrum zabezpieczeń usługi wykrywania. Aby uzyskać więcej informacji na temat szczegółów zarejestrowanych dla każdego nowego procesu, zobacz [Opis pól w 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent również zbiera dane zdarzeń 4688, utworzony na maszynie Wirtualnej i przechowuje je w polu wyszukiwania.

Agent umożliwia również zbieranie danych na potrzeby [adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md), Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. Te zasady spowodują generowanie przez funkcję AppLocker zdarzeń, które następnie są zbierane i wykorzystywane przez Security Center. Należy zauważyć, że te zasady nie zostaną skonfigurowane na maszynach, na których jest już skonfigurowane zasady funkcji AppLocker. 

Gdy Security Center wykryje podejrzane działanie na maszynie wirtualnej, klient zostanie powiadomiony pocztą e-mail, jeśli podano [Informacje o kontakcie zabezpieczeń](security-center-provide-security-contact-details.md) . Alert jest również widoczne w pulpicie nawigacyjnym alerty zabezpieczeń Centrum zabezpieczeń.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Czy Security Center będzie działała przy użyciu bramy pakietu OMS?

Tak. Azure Security Center wykorzystuje Azure Monitor do zbierania danych z maszyn wirtualnych i serwerów platformy Azure przy użyciu Microsoft Monitoring Agent.
Aby zebrać dane, każda maszyna wirtualna i serwer muszą łączyć się z Internetem przy użyciu protokołu HTTPS. Połączenie może być bezpośrednie, przy użyciu serwera proxy lub przez [bramę pakietu OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Czy Monitoring Agent wpływ na wydajność moich serwerów?

Agent korzysta nominalna ilość zasobów systemowych i powinno mieć większego wpływu na wydajność. Aby uzyskać więcej informacji na temat wpływu na wydajność oraz agenta i rozszerzenia, zobacz [Przewodnik planowania i](security-center-planning-and-operations-guide.md#data-collection-and-storage)obsługi.


## <a name="where-is-my-data-stored"></a>Gdzie są przechowywane moje dane?

Dane zbierane z tego agenta są przechowywane w istniejącego obszaru roboczego usługi Log Analytics skojarzonych z subskrypcją lub nowy obszar roboczy. Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png