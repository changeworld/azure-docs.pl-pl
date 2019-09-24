---
title: Ochrona maszyn i aplikacji w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Tym dokumencie przedstawiono zalecenia w usłudze Security Center, które ułatwiają ochronę maszyn wirtualnych i komputerów i aplikacji sieci web i środowisk usługi App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: a3bce8d6312dd09a7f10f8d5d2eaebd4e312d95d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200776"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrona maszyn i aplikacji w usłudze Azure Security Center
Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure, serwerów spoza platformy Azure i maszyn wirtualnych. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek. Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM) i komputerów, aplikacji, networking, SQL i tożsamościami i dostępem.

W tym artykule opisano zaleceń, które są stosowane do maszyn i aplikacji.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Możesz monitorować stan zabezpieczeń zasobów na **Security Center — Przegląd** pulpitu nawigacyjnego. **Zasobów** sekcji omówiono liczbę zidentyfikowane problemy i stan zabezpieczeń dla każdego typu zasobu.

Można wyświetlić listę wszystkich problemów, wybierając **zalecenia**. Aby uzyskać więcej informacji na temat stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

Aby uzyskać pełną listę zasobów obliczeniowych i aplikacji usług zalecenia, zobacz [zalecenia](security-center-virtual-machine-protection.md#compute-and-app-recommendations).

Aby kontynuować, wybierz **obliczenia i aplikacje** w obszarze **zasobów** lub w menu głównym usługi Security Center.
![Pulpit nawigacyjny Centrum zabezpieczeń](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorowanie usług obliczeniowych i aplikacji
W obszarze **obliczeniowe & aplikacje**istnieją następujące karty:

- **Omówienie**: monitorowanie i zalecenia zidentyfikowane przez usługę Security Center.
- **Maszyny wirtualne i komputery**: lista maszyn wirtualnych i komputerów oraz ich bieżący stan zabezpieczeń.
- **Usługi w chmurze**: lista ról internetowych i procesów roboczych monitorowanych przez usługę Security Center.
- **App Services**: Lista środowisk usługi App Service i bieżący stan zabezpieczeń każdego z nich.
- **Kontenery (wersja zapoznawcza)** : Lista kontenerów hostowanych na maszynach z systemem Linux IaaS i Ocena zabezpieczeń ich konfiguracji platformy Docker.
- **Zasoby obliczeniowe (wersja zapoznawcza)** : Lista zaleceń dotyczących zasobów obliczeniowych, takich jak klastry Service Fabric i centra zdarzeń.

Aby kontynuować, wybierz pozycję **obliczeniowe & aplikacje** w obszarze **higiena zabezpieczeń zasobów**.

![Wystąpienia obliczeniowe](./media/security-center-virtual-machine-recommendations/compute.png)

Na każdej karcie znajduje się kilka sekcji, a w każdej sekcji można wybrać poszczególne opcje, aby wyświetlić więcej szczegółowych informacji dotyczących zalecanych kroków rozwiązania konkretnego problemu.

### Niemonitorowane maszyny wirtualne i komputery <a name="unmonitored-vms-and-computers"></a>
Maszynę Wirtualną lub komputer jest niemonitorowana przez usługę Security Center, jeśli komputer nie jest uruchomiona rozszerzenia Microsoft Monitoring Agent. Na komputerze może być już zainstalowany agent lokalny, na przykład Agent usługi OMS Direct lub Agent System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ ci agenci nie są w pełni obsługiwane w usłudze Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

Rozszerzenie można zainstalować na niemonitorowanej maszynie Wirtualnej lub komputerze oprócz już zainstalowanego lokalnego agenta. Obu agentów należy skonfigurować tak samo, łącząc ich z tym samym obszarem roboczym. Dzięki temu usługa Security Center może wchodzić w interakcje z rozszerzeniem programu Microsoft Monitoring Agent i zbierać dane. Zobacz [Włączanie rozszerzenia maszyny wirtualnej](../azure-monitor/learn/quick-collect-azurevm.md), aby uzyskać instrukcje dotyczące sposobu instalowania rozszerzenia programu Microsoft Monitoring Agent.

Zobacz [Monitorowanie problemów dotyczących kondycji agenta](security-center-troubleshooting-guide.md#mon-agent), aby dowiedzieć się więcej na temat powodów, dla których usługa Security Center nie może prawidłowo monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji.

### <a name="recommendations"></a>Zalecenia
Ta sekcja zawiera zestaw zaleceń dotyczących każdej maszyny Wirtualnej i komputera, role sieć web i proces roboczy, Azure App Service Web Apps i Azure App Service Environment, która usługa Security Center monitoruje. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera całkowitą liczbę zasobów, których dotyczy tego zalecenia. Trzecia kolumna przedstawia ważność problemu.

Każdy zalecenie obejmuje zestaw akcji, które można wykonać po jej wybraniu. Na przykład w przypadku wybrania opcji **brakujące aktualizacje systemu**zostanie wyświetlona liczba maszyn wirtualnych i komputerów, na których brakuje poprawek, oraz ważność brakującej aktualizacji.

**Zastosuj aktualizacje systemu** zawiera podsumowanie krytycznych aktualizacji w postaci wykresu: jeden dla Windows i jeden dla systemu Linux. Druga część zawiera tabelę z następującymi informacjami:

- **NAZWA**: Nazwa brakującej aktualizacji.
- **LICZBA Maszyn wirtualnych & komputerów**: Łączna liczba maszyn wirtualnych i komputerów, na których brakuje tej aktualizacji.
- **WAŻNOŚĆ AKTUALIZACJI**: Opisuje ważność danego zalecenia:

    - **Krytyczne**: Istnieje luka w zabezpieczeniach z istotnym zasobem (aplikacją, maszyną wirtualną lub sieciową grupą zabezpieczeń) i wymaga uwagi.
    - **Ważne**: Niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
    - **Umiarkowany**: Należy rozwiązać usterkę, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).


- **STAN**: Bieżący stan zalecenia:

    - **Otwórz**: Zalecenie nie zostało jeszcze uwzględnione.
    - **W toku**: Zalecenie jest obecnie stosowane do tych zasobów i nie jest wymagane wykonanie jakiejkolwiek akcji przez użytkownika.
    - **Rozwiązano**: Zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

Aby wyświetlić szczegóły zalecenia, kliknij nazwę brakującej aktualizacji na liście.


> [!NOTE]
> Zalecenia dotyczące zabezpieczeń w tym miejscu są takie same, jak w obszarze **zalecenia** kafelka. Aby uzyskać więcej informacji o sposobach rozwiązywania zaleceń, zobacz [implementowanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Maszyny wirtualne i komputery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących maszyn wirtualnych i komputerów. Każda kolumna reprezentuje jeden zestaw zaleceń.

![Rekomendacje dotyczące maszyn wirtualnych i komputerów](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Istnieją cztery typy ikon przedstawianych na tej liście:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Maszyna wirtualna platformy Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure Resource Manager.

![Klasyczne maszyny Wirtualnej platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.


![Maszyny wirtualne wskazane w obszarze roboczym](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które są raportowane do obszaru roboczego w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane przy użyciu agenta Operations Manager bezpośredniego, i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko zidentyfikować maszyny Wirtualnej, a komputer, który wymaga uwagi oraz jakiego typu zalecenie ma do nich. Możesz również użyć filtrów, aby przeszukać listę według **typu zasobów** i według **ważności**.

Aby przejść do szczegółów zaleceń dotyczących zabezpieczeń dla każdej maszyny wirtualnej, kliknij maszynę wirtualną.
W tym miejscu wyświetlane szczegóły zabezpieczeń dotyczące maszyny Wirtualnej lub komputera. Na dole można zobaczyć zalecaną akcję i ważność poszczególnych problemów.
![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Usługi w chmurze
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie (co nie jest w przypadku poprzedniego przykładu) należy wykonać kroki zawarte w zaleceniu, aby zaktualizować wersję systemu operacyjnego. Gdy aktualizacja jest dostępna, zostanie wyświetlony alert (czerwony lub pomarańczowy — w zależności od ważności problemu). Po wybraniu tego alertu w WebRole1 (uruchamia system Windows Server z aplikacją sieci Web automatycznie wdrożoną w usługach IIS) lub WorkerRole1 (system Windows Server z aplikacją sieci Web automatycznie wdrożoną w usługach IIS) zobaczysz więcej szczegółowych informacji na temat tego zalecenia.

Aby zobaczyć więcej normatywnych wyjaśnień dotyczących tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **OPIS**.



![Aktualizacja wersji systemu operacyjnego](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Usługi aplikacji
Aby wyświetlić informacje App Service, należy włączyć App Service w subskrypcji. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [ochrona App Service przy użyciu Azure Security Center](security-center-app-services.md).
[!NOTE]
> Monitorowanie usługi App Service jest dostępna w wersji zapoznawczej i jest dostępna tylko w warstwie standardowa usługi Security Center.


W obszarze **usług App services**Znajdź listę środowisk App service Environment i wykonywane podsumowanie kondycji opartego na ocenie usługi Security Center.

![Usługi aplikacji](./media/security-center-virtual-machine-recommendations/app-services.png)

Istnieją trzy typy ikon przedstawianych na tej liście:

![Środowiska usług aplikacji](./media/security-center-virtual-machine-recommendations/ase.png) Środowiska usług aplikacji.

![Aplikacja internetowa](./media/security-center-virtual-machine-recommendations/web-app.png) Aplikacja sieci Web.

![Aplikacja funkcji](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikacja funkcji.

1. Wybierz aplikację sieci web. Otwiera widok podsumowania z trzema kartami:

   - **Zalecenia dotyczące**: oparte na ocen wykonywane przez usługę Security Center, który uległ awarii.
   - **Przekazano ocen**: Lista ocen wykonywane przez usługę Security Center, które przekazane.
   - **Oceny niedostępne**: Lista ocen, którzy nie uruchomili z powodu błędu lub zalecenie nie jest odpowiednie dla określonej usługi App service

   W obszarze **zalecenia** listę zaleceń dla wybranej aplikacji sieci web i ważność poszczególne zalecenia.

   ![Zalecenia dotyczące App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Wybierz zalecenie, aby wyświetlić opis zalecenia i listę zasobów w złej kondycji, zdrowe zasoby i niezeskanowane zasoby.

   - W kolumnie **przekazano oceny** jest to Lista przetestowanych ocen.  Ważność te oceny zawsze ma kolor zielony.

   - Wybierz ocenę przekazany z listy opis oceny, lista zasobów w złej kondycji i w dobrej kondycji i listę nieprzeskanowane zasoby. Ma kartę zasoby w złej kondycji, ale ta lista jest zawsze pusta, ponieważ przekazana oceny.

     ![Korygowanie App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets
Security Center automatycznie wykrywa, czy masz zestawy skalowania i zaleca się zainstalowanie Microsoft Monitoring Agent w tych zestawach skalowania. 

Aby zainstalować Microsoft Monitoring Agent: 

1. Wybierz zalecenie **Zainstaluj agenta monitorowania w zestawie skalowania maszyn wirtualnych**. Otrzymujesz listę niemonitorowanych zestawów skalowania.
2. Wybierz zestaw skalowania w złej kondycji. Postępuj zgodnie z instrukcjami, aby zainstalować agenta monitorowania przy użyciu istniejącego wypełnionego obszaru roboczego lub utworzyć nowy. Upewnij się, że ustawiono [warstwę cenową](security-center-pricing.md) obszaru roboczego, jeśli nie została ustawiona.

   ![Instalowanie MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Aby ustawić nowe zestawy skalowania tak, aby automatycznie instalowały Microsoft Monitoring Agent:
1. Przejdź do Azure Policy i kliknij pozycję **definicje**.
2. Wyszukaj zasady **wdróż log Analytics Agent dla systemu Windows Virtual Machine Scale Sets** i kliknij je.
3. Kliknij przycisk **Przypisz**.
4. Ustaw **zakres** i **obszar roboczy log Analytics** i kliknij przycisk **Przypisz**.

Jeśli chcesz ustawić wszystkie istniejące zestawy skalowania w celu zainstalowania Microsoft Monitoring Agent, w Azure Policy, przejdź do **korygowania** i Zastosuj istniejące zasady do istniejących zestawów skalowania.


## <a name="compute-and-app-recommendations"></a>Zalecenia dotyczące obliczeń i aplikacji
|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|App Service|20|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Tylko ograniczenie dostępu do aplikacji sieci Web przy użyciu protokołu HTTPS.|
|App Service|20|Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS|Tylko ograniczenie dostępu do aplikacji funkcji przy użyciu protokołu HTTPS.|
|App Service|5|Dzienniki diagnostyczne w App Services powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Wyłącz opcję debugowania dla aplikacji sieci Web, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji.|
|App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji|Wyłącz opcję debugowania dla aplikacji funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji.|
|App Service|10|Nie zezwalaj na wszystkie ("*") zasobów uzyskiwanie dostępu do aplikacji| Nie zezwalaj na zestaw parametru WEBSITE_LOAD_CERTIFICATES na "". Ustawienie dla parametru "oznacza, że wszystkie certyfikaty są ładowane do magazynu certyfikatów osobiste aplikacje sieci web. Może to prowadzić do naruszenia zasady najmniejszych uprawnień, ponieważ jest mało prawdopodobne, że witryna musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania.|
|App Service|20|Mechanizm CORS nie powinien zezwalać na każdy zasób dostęp do aplikacji sieci Web|Zezwala na tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe pochodzenia zasobów między źródłami (cors) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do sieci web aplikacji.|
|App Service|20|Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji| Zezwala na tylko wymagane domen do interakcji z aplikacją funkcji. Krzyżowe origin resource sharing (CORS) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do funkcji aplikacji.|
|Obliczenia zasobów (wsadowe)|1|Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych|Konfigurowanie reguł alertów dotyczących metryk na koncie usługi Batch i włączyć metryki puli Usuń zakończenie zdarzenia i puli Usuń Start|
|(Usługa Service fabric) zasobów obliczeniowych|10|W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów|W usłudze Service Fabric, należy wykonać uwierzytelnianie klientów tylko przy użyciu usługi Azure Active Directory.|
|(Konto usługi automation) zasobów obliczeniowych|5|Zmienne konta usługi Automation powinny być szyfrowane|W przypadku przechowywania poufnych danych, należy włączyć szyfrowanie zmiennych elementów zawartości konta usługi Automation.|
|(Wyszukiwanie) zasobów obliczeniowych|5|Inspekcja włączenia dzienników diagnostycznych dla usług wyszukiwania|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service bus) zasobów obliczeniowych|5|Dzienniki diagnostyczne w Service Bus powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa stream analytics) zasobów obliczeniowych|5|Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasobów (wsadowe)|5|Włączanie dzienników diagnostycznych w ramach kont usługi Batch|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasobów (Centrum zdarzeń)|5|Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasoby (aplikacje Logic Apps)|5|Włącz dzienniki diagnostyki w usłudze Logic Apps|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service fabric) zasobów obliczeniowych|15|Ustaw właściwość ClusterProtectionLevel EncryptAndSign w usłudze Service Fabric|Usługa Service Fabric udostępnia trzy poziomy ochrony (Brak, logowania i EncryptAndSign) do komunikacji między węzłami, przy użyciu certyfikatu klastra podstawowego.  Ustaw poziom ochrony, aby zapewnić zaszyfrowany i podpisany cyfrowo wszystkie komunikaty węzła do węzła. |
|(Usługa Service bus) zasobów obliczeniowych|1|Usuń wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw usługi Service Bus |Klienci usługi Service Bus nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Obliczenia zasobów (Centrum zdarzeń)|1|Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń|Klienci Centrum zdarzeń nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Obliczenia zasobów (Centrum zdarzeń)|5|Należy zdefiniować reguły autoryzacji w jednostce centrum zdarzeń|Inspekcja reguły autoryzacji na jednostce Centrum zdarzeń przyznania najmniej uprzywilejowanego dostępu.|
|Machine|50|Zainstaluj agenta monitorowania na maszynach|Zainstaluj agenta monitorowania, aby włączyć zbieranie danych, aktualizacje skanowania, skanowania linii bazowej i programem endpoint protection na każdym komputerze.|
|Maszyna|50|Włączanie automatycznej aprowizacji i zbierania danych dla subskrypcji |Włączanie automatycznej aprowizacji i zbierania danych dla maszyn w Twoich subskrypcjach, aby włączyć zbieranie danych aktualizacji skanowania, skanowania linii bazowej i programem endpoint protection na każdej maszynie, dodać do swojej subskrypcji.|
|Maszyna|40|Rozwiąż problemy dotyczące kondycji agenta monitorowania na maszynach|Dla pełnej ochrony usługa Security Center należy rozwiązać problemy dotyczące monitorowania agentów na maszynach zgodnie z instrukcjami w przewodniku rozwiązywania problemów| 
|Maszyna|40|Rozwiąż problemy dotyczące kondycji ochrony punktu końcowego na maszynach|Dla pełnej ochrony usługa Security Center należy rozwiązać problemy dotyczące monitorowania agenta na maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.|
|Maszyna|40|Rozwiąż problemy dotyczące brakujących danych skanowania na maszynach|Rozwiązywanie problemów z Brak danych skanowania na maszynach wirtualnych i komputerów. Brak danych skanowania na wyniki maszyn brakujących oceny zabezpieczeń, takich jak aktualizacji, skanowania linii bazowej skanowanie i brak Trwa skanowanie rozwiązania ochrony punktu końcowego.|
|Maszyna|40|Aktualizacje systemu powinny być zainstalowane na maszynach|Instalowanie brakujących zabezpieczeń systemu i zabezpieczanie maszyn wirtualnych Windows i Linux oraz komputerach krytyczne aktualizacje
|Machine|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie umożliwiającymi zainstalowanie zapory aplikacji sieci web w celu zabezpieczenia aplikacji sieci web. |
|Machine|40|Zaktualizuj wersję systemu operacyjnego dla ról usługi w chmurze|Zaktualizuj wersję systemu operacyjnego (OS) dla ról usługi w chmurze do najnowszej wersji dostępnej dla Twojej rodziny systemów operacyjnych.|
|Maszyna|35|Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować|Korygowanie luk w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach w taki sposób, aby chronić je przed atakami.|
|Maszyna|35|Korygowanie luk w zabezpieczeniach w konfiguracji zabezpieczeń na kontenerów|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach z zainstalowaną platformą Docker, aby ochronić je przed atakami.|
|Maszyna|25|Włącz adaptacyjne kontrole aplikacji|Włącz aplikację formantami, które aplikacje mogą być uruchamiane na maszynach wirtualnych na platformie Azure. Ułatwi to zabezpiecza maszyny wirtualne przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego w celu analizuje aplikacje uruchomione na każdej maszynie Wirtualnej i pomaga stosowanych umożliwiają reguły za pomocą tej analizy. Ta funkcja upraszcza proces konfigurowania i utrzymywania aplikacji umożliwiają reguły.|
|Maszyna|20|Zainstaluj rozwiązanie do ochrony punktu końcowego na maszynach|Zainstalować rozwiązanie do ochrony punktu końcowego na maszynach wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach.|
|Maszyna|20|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu i zabezpieczyć maszyny przed lukami w zabezpieczeniach.|
|Machine|15|Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Szyfrowanie dysków maszyn wirtualnych przy użyciu usługi Azure Disk Encryption zarówno dla maszyn wirtualnych Windows i Linux. Usługa Azure dysku Encryption (ADE) korzysta z branży standardowych funkcji BitLocker Windows oraz funkcji DM-Crypt systemu Linux w celu zapewnienia szyfrowania dysku systemu operacyjnego i danych, aby pomóc chronić dane i sprostać organizacyjne bezpieczeństwa i zgodności zobowiązań klientów usługi Azure key vault. Gdy Twoje wymagania zgodności i zabezpieczeń wymaga szyfrowania danych typu end to end przy użyciu kluczy szyfrowania, łącznie z szyfrowaniem efemerycznego dysku (tymczasowo podłączonych lokalnie), użyj usługa Azure disk encryption. Alternatywnie domyślnie Managed Disks są magazynowanych domyślnie szyfrowane za pomocą usługi Azure Storage Service Encryption, gdzie klucze szyfrowania są klucze zarządzany przez firmę Microsoft na platformie Azure. Jeśli to nie spełnia wymagania zabezpieczeń i zgodności, na których, można korzystać z szyfrowania dysku zarządzanego domyślne, zgodnie z wymaganiami.|
|Machine|30|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie umożliwiającymi zainstalowanie zapory aplikacji sieci web w celu zabezpieczenia aplikacji sieci web. |
|Maszyna|30|Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono luk w zabezpieczeniach 3rd strona rozwiązania do oceny są stale oceniane względem aplikacji i luk w zabezpieczeniach systemu operacyjnego. Zawsze, gdy zostaną znalezione lukami umożliwiającymi takie ataki, są dostępne, aby uzyskać więcej informacji, w ramach zalecenia.|
|Machine|30|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Machine|1|Maszyny wirtualne powinny być migrowane do nowych zasobów AzureRM|Użyj Azure Resource Manager dla maszyn wirtualnych, aby zapewnić lepsze zabezpieczenia, takie jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, wdrażanie i zarządzanie oparte na Menedżer zasobówach, dostęp do zarządzanych tożsamości, dostęp do magazynu kluczy dla wpisów tajnych, Uwierzytelnianie oparte na usłudze Azure AD i obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami. |
|Machine|30|Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono luk w zabezpieczeniach 3rd strona rozwiązania do oceny są stale oceniane względem aplikacji i luk w zabezpieczeniach systemu operacyjnego. Zawsze, gdy zostaną znalezione lukami umożliwiającymi takie ataki, są dostępne, aby uzyskać więcej informacji, w ramach zalecenia.|
|Zestaw skalowania maszyn wirtualnych |4|Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone|Włącz dzienniki i zachowaj je przez maksymalnie rok. Dzięki temu można odtworzyć ślady aktywności do celów dochodzeniowych. Jest to przydatne w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Zestaw skalowania maszyn wirtualnych|35|Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować|Koryguj luki w zabezpieczeniach w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |
|Zestaw skalowania maszyn wirtualnych|5|Koryguj błędy kondycji programu Endpoint Protection w zestawach skalowania maszyn wirtualnych|Skoryguj błędy kondycji programu Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|10|Program Endpoint Protection powinien być zainstalowany na maszynach wirtualnych|Zainstaluj rozwiązanie Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|40|Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Zainstaluj brakujące zabezpieczenia systemu i aktualizacje krytyczne, aby zabezpieczyć zestawy skalowania maszyn wirtualnych z systemami Windows i Linux. |
 





## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:


* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

