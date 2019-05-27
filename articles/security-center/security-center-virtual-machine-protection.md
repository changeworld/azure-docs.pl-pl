---
title: Ochrona maszyn i aplikacji w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Tym dokumencie przedstawiono zalecenia w usłudze Security Center, które ułatwiają ochronę maszyn wirtualnych i komputerów i aplikacji sieci web i środowisk usługi App Service.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: v-mohabe
ms.openlocfilehash: e65e3869d420607ad2721df8ba0ef1005c923f73
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968282"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrona maszyn i aplikacji w usłudze Azure Security Center
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure, poza platformą Azure, serwerów i maszyn wirtualnych. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek. Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM) i komputerów, aplikacji, networking, SQL i tożsamościami i dostępem.

W tym artykule opisano zaleceń, które są stosowane do maszyn i aplikacji.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Możesz monitorować stan zabezpieczeń zasobów na **Security Center — Przegląd** pulpitu nawigacyjnego. **Zasobów** sekcji omówiono liczbę zidentyfikowane problemy i stan zabezpieczeń dla każdego typu zasobu.

Można wyświetlić listę wszystkich problemów, wybierając **zalecenia**. Aby uzyskać więcej informacji na temat stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

Aby uzyskać pełną listę zasobów obliczeniowych i aplikacji usług zalecenia, zobacz [zalecenia](security-center-virtual-machine-recommendations.md).

Aby kontynuować, wybierz **obliczenia i aplikacje** w obszarze **zasobów** lub w menu głównym usługi Security Center.
![Pulpit nawigacyjny Centrum zabezpieczeń](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorowanie usług obliczeniowych i aplikacji
W obszarze **obliczenia i aplikacje**, istnieją następujące karty:

- **Omówienie**: monitorowanie i zalecenia zidentyfikowane przez usługę Security Center.
- **Maszyny wirtualne i komputery**: lista maszyn wirtualnych i komputerów oraz ich bieżący stan zabezpieczeń.
- **Usługi w chmurze**: lista ról internetowych i procesów roboczych monitorowanych przez usługę Security Center.
- **Usługi aplikacji**: Lista środowisk usługi App service i bieżący stan zabezpieczeń każdego z nich.
- **Kontenery (wersja zapoznawcza)**: Lista kontenerów hostowanych na maszynach z systemem IaaS Linux i ocenę zabezpieczeń ich konfiguracji platformy Docker.
- **(Wersja zapoznawcza) zasoby obliczeniowe**: listę zaleceń dotyczących zasobów obliczeniowych, takich jak klastry usługi Service Fabric i usługi Event hubs.

Aby kontynuować, wybierz **obliczenia i aplikacje** w obszarze **higieny zabezpieczeń zasobu**.

![Wystąpienia obliczeniowe](./media/security-center-virtual-machine-recommendations/compute.png)

Na każdej karcie znajduje się kilka sekcji, a w każdej sekcji można wybrać poszczególne opcje, aby wyświetlić więcej szczegółowych informacji dotyczących zalecanych kroków rozwiązania konkretnego problemu.

### Niemonitorowane maszyny wirtualne i komputery <a name="unmonitored-vms-and-computers"></a>
Maszynę Wirtualną lub komputer jest niemonitorowana przez usługę Security Center, jeśli komputer nie jest uruchomiona rozszerzenia Microsoft Monitoring Agent. Komputer może mieć zainstalowanego lokalnego agenta, na przykład OMS bezpośrednie lub agent programu System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ ci agenci nie są w pełni obsługiwane w usłudze Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

Rozszerzenie można zainstalować na niemonitorowanej maszynie Wirtualnej lub komputerze oprócz już zainstalowanego lokalnego agenta. Obu agentów należy skonfigurować tak samo, łącząc ich z tym samym obszarem roboczym. Dzięki temu usługa Security Center może wchodzić w interakcje z rozszerzeniem programu Microsoft Monitoring Agent i zbierać dane. Zobacz [Włączanie rozszerzenia maszyny wirtualnej](../azure-monitor/learn/quick-collect-azurevm.md), aby uzyskać instrukcje dotyczące sposobu instalowania rozszerzenia programu Microsoft Monitoring Agent.

Zobacz [Monitorowanie problemów dotyczących kondycji agenta](security-center-troubleshooting-guide.md#mon-agent), aby dowiedzieć się więcej na temat powodów, dla których usługa Security Center nie może prawidłowo monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji.

### <a name="recommendations"></a>Zalecenia
Ta sekcja zawiera zestaw zaleceń dotyczących każdej maszyny Wirtualnej i komputera, role sieć web i proces roboczy, Azure App Service Web Apps i Azure App Service Environment, która usługa Security Center monitoruje. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera całkowitą liczbę zasobów, których dotyczy tego zalecenia. Trzecia kolumna pokazuje wagę problemu.

Każdy zalecenie obejmuje zestaw akcji, które można wykonać po jej wybraniu. Na przykład w przypadku wybrania **brakujące aktualizacje systemu**, zostanie wyświetlona liczba maszyn wirtualnych i komputerów, których brakuje poprawek oraz ważność brakujących aktualizacji.

**Zastosuj aktualizacje systemu** zawiera podsumowanie krytycznych aktualizacji w postaci wykresu: jeden dla Windows i jeden dla systemu Linux. Druga część zawiera tabelę z następującymi informacjami:

- **NAZWA**: Nazwa brakującej aktualizacji.
- **LICZBA Maszyn wirtualnych i komputerów**: Całkowita liczba maszyn wirtualnych i komputerów, które mają tej aktualizacji.
- **WAŻNOŚĆ AKTUALIZACJI**: Opisuje ważność określonego zalecenia:

    - **Krytyczne**: Luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (aplikacji, maszyny wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
    - **Ważne**: Niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
    - **Umiarkowany**: Luki w zabezpieczeniach powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).


- **STAN**: Bieżący stan zalecenia:

    - **Otwórz**: Zalecenie nie rozpoczęto jeszcze wykonywania.
    - **Trwającą**: Zalecenie jest aktualnie stosowane do tych zasobów i jest wymagana żadna akcja.
    - **Rozwiązane**: Zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

Aby wyświetlić szczegóły zalecenia, kliknij nazwę brakującej aktualizacji na liście.


> [!NOTE]
> Zalecenia dotyczące zabezpieczeń w tym miejscu są takie same, jak w obszarze **zalecenia** kafelka. Zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) uzyskać więcej informacji dotyczących stosowania zaleceń.
>
>

### <a name="vms-and-computers"></a>Maszyny wirtualne i komputery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących maszyn wirtualnych i komputerów. Każda kolumna reprezentuje jeden zestaw zaleceń.

![Rekomendacje dotyczące maszyn wirtualnych i komputerów](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Istnieją cztery typy ikon przedstawianych na tej liście:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Maszyna wirtualna platformy Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure Resource Manager.

![Klasyczne maszyny Wirtualnej platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.


![Maszyny wirtualne wskazane w obszarze roboczym](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. To obejmuje maszyny wirtualne z innych subskrypcji tego raportu do obszaru roboczego w tej subskrypcji i maszyn wirtualnych, które zostały zainstalowane przy użyciu programu Operations Manager agent bezpośredni i mieć nie identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko zidentyfikować maszyny Wirtualnej, a komputer, który wymaga uwagi oraz jakiego typu zalecenie ma do nich. Umożliwia także filtry wyszukiwania listę według **typ zasobu** i **ważność**.

Aby przejść do zalecenia dotyczące zabezpieczeń dla każdej maszyny Wirtualnej, kliknij maszynę Wirtualną.
W tym miejscu wyświetlane szczegóły zabezpieczeń dotyczące maszyny Wirtualnej lub komputera. W dolnej części widoczna jest zalecana akcja i ważność poszczególnych problemów.
![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Usługi w chmurze
Dla usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie (co nie jest w przypadku poprzedniego przykładu) należy wykonać kroki zawarte w zaleceniu, aby zaktualizować wersję systemu operacyjnego. Gdy aktualizacja jest dostępna, zostanie wyświetlony alert (czerwony lub pomarańczowy — w zależności od ważności problemu). Po wybraniu tego alertu WebRole1 (działa system Windows Server z aplikacją sieci web automatycznie wdrożoną w usługach IIS) lub WorkerRole1 (działa system Windows Server z aplikacją sieci web automatycznie wdrożoną w usługach IIS) wiersze, zostanie wyświetlone więcej szczegółów na temat tego zalecenia.

Aby zobaczyć więcej normatywnych wyjaśnień dotyczących tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **OPIS**.



![Zaktualizuj wersję systemu operacyjnego](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App services
Należy włączyć usługi App Service w ramach subskrypcji, aby wyświetlić informacje o usłudze App Service. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [ochrony usługi App Service z usługą Azure Security Center](security-center-app-services.md).
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

   ![Zalecenia dotyczące usług aplikacji](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Wybierz zalecenie, aby wyświetlić opis zalecenia oraz listę zasoby w złej kondycji, zasoby w dobrej kondycji i nieprzeskanowane zasoby.

   - W obszarze **przekazywane ocen** kolumnie znajduje się lista oceny zakończone pomyślnie.  Ważność te oceny zawsze ma kolor zielony.

   - Wybierz ocenę przekazany z listy opis oceny, lista zasobów w złej kondycji i w dobrej kondycji i listę nieprzeskanowane zasoby. Ma kartę zasoby w złej kondycji, ale ta lista jest zawsze pusta, ponieważ przekazana oceny.

     ![Korygowanie usługi App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych
Usługa Security Center automatycznie wykrywa, czy zestawy skali i zaleca się zainstalowanie programu Microsoft Monitoring Agent na tych zestawach skalowania. 

Aby zainstalować program Microsoft Monitoring Agent: 

1. Wybierz zalecenie **zainstalować agenta monitorowania w zestawie skalowania maszyn wirtualnych**. Możesz uzyskać listę zestawów skalowania niemonitorowane.
2. Wybierz zestaw skalowania w złej kondycji. Postępuj zgodnie z instrukcjami, aby zainstalować agenta monitorowania przy użyciu istniejącego obszaru roboczego wypełnione lub Utwórz nową. Upewnij się ustawić obszaru roboczego [warstwy cenowej](security-center-pricing.md) Jeśli nie jest ustawiona.

   ![Zainstaluj usługę MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Jeśli chcesz ustawić nowe zestawy skalowania automatycznej instalacji programu Microsoft Monitoring Agent:
1. Przejdź do usługi Azure Policy, a następnie kliknij przycisk **definicje**.
2. Wyszukaj zasady **agenta wdrażania usługi Log Analytics dla zestawów skalowania maszyn wirtualnych Windows** i kliknij go.
3. Kliknij przycisk **Przypisz**.
4. Ustaw **zakres** i **obszaru roboczego usługi Log Analytics** i kliknij przycisk **przypisać**.

Jeśli chcesz ustawić wszystkie istniejące zestawy skalowania, aby zainstalować program Microsoft Monitoring Agent, usługa Azure Policy, przejdź do strony **korygowania** i zastosować istniejących zasad do istniejących zestawów skalowania.


## <a name="compute-and-app-recommendations"></a>Zalecenia dotyczące obliczeń i aplikacji
|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|App Service|20|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Tylko ograniczenie dostępu do aplikacji sieci Web przy użyciu protokołu HTTPS.|
|App Service|20|Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS|Tylko ograniczenie dostępu do aplikacji funkcji przy użyciu protokołu HTTPS.|
|App Service|5|Włącz dzienniki diagnostyki w usłudze App service|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Wyłącz opcję debugowania dla aplikacji sieci Web, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji.|
|App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji|Wyłącz opcję debugowania dla aplikacji funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji.|
|App Service|10|Konfiguruj ograniczenia adresów IP dla aplikacji sieci Web|Definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji. Użyj ograniczeń adresów IP chroni aplikację internetową przed typowymi atakami.|
|App Service|10|Nie zezwalaj na wszystkie ("*") zasobów uzyskiwanie dostępu do aplikacji| Nie zezwalaj na zestaw parametru WEBSITE_LOAD_CERTIFICATES na "". Ustawienie dla parametru "oznacza, że wszystkie certyfikaty są ładowane do magazynu certyfikatów osobiste aplikacje sieci web. Może to prowadzić do naruszenia zasady najmniejszych uprawnień, ponieważ jest mało prawdopodobne, że witryna musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania.|
|App Service|20|Mechanizm CORS nie powinien zezwalać na każdy zasób dostęp do aplikacji sieci Web|Zezwala na tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe pochodzenia zasobów między źródłami (cors) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do sieci web aplikacji.|
|App Service|20|Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji| Zezwala na tylko wymagane domen do interakcji z aplikacją funkcji. Krzyżowe origin resource sharing (CORS) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do funkcji aplikacji.|
|Obliczenia zasobów (wsadowe)|1|Konfigurowanie reguł alertów dotyczących metryk na koncie usługi Batch|Konfigurowanie reguł alertów dotyczących metryk na koncie usługi Batch i włączyć metryki puli Usuń zakończenie zdarzenia i puli Usuń Start|
|(Usługa Service fabric) zasobów obliczeniowych|10|Usługa Azure Active Directory do uwierzytelniania klientów w usłudze Service Fabric|W usłudze Service Fabric, należy wykonać uwierzytelnianie klientów tylko przy użyciu usługi Azure Active Directory.|
|(Konto usługi automation) zasobów obliczeniowych|5| Włącz szyfrowanie konta usługi Automation|W przypadku przechowywania poufnych danych, należy włączyć szyfrowanie zmiennych elementów zawartości konta usługi Automation.|
|(Moduł równoważenia obciążenia) zasobów obliczeniowych|5|Włączanie dzienników diagnostycznych w module równoważenia obciążenia|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Wyszukiwanie) zasobów obliczeniowych|5|Włącz dzienniki diagnostyczne usługi Search|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service bus) zasobów obliczeniowych|5|Włącz dzienniki diagnostyki w usłudze Service Bus|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa stream analytics) zasobów obliczeniowych|5|Włącz dzienniki diagnostyki w usłudze Azure Stream Analytics|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service fabric) zasobów obliczeniowych|5|Włącz dzienniki diagnostyki w usłudze Service Fabric|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasobów (wsadowe)|5|Włączanie dzienników diagnostycznych w ramach kont usługi Batch|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasobów (Centrum zdarzeń)|5|Włącz dzienniki diagnostyki w Centrum zdarzeń|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasoby (aplikacje Logic Apps)|5|Włącz dzienniki diagnostyki w usłudze Logic Apps|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service fabric) zasobów obliczeniowych|15|Ustaw właściwość ClusterProtectionLevel EncryptAndSign w usłudze Service Fabric|Usługa Service Fabric udostępnia trzy poziomy ochrony (Brak, logowania i EncryptAndSign) do komunikacji między węzłami, przy użyciu certyfikatu klastra podstawowego.  Ustaw poziom ochrony, aby zapewnić zaszyfrowany i podpisany cyfrowo wszystkie komunikaty węzła do węzła. |
|(Usługa Service bus) zasobów obliczeniowych|1|Usuń wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw usługi Service Bus |Klienci usługi Service Bus nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby wyrównać z najniższymi uprawnieniami model zabezpieczeń, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Obliczenia zasobów (Centrum zdarzeń)|1|Usuń wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw Centrum zdarzeń |Klienci Centrum zdarzeń nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby wyrównać z najniższymi uprawnieniami model zabezpieczeń, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Obliczenia zasobów (Centrum zdarzeń)|5|Zdefiniuj reguły autoryzacji na jednostce Centrum zdarzeń|Inspekcja reguły autoryzacji na jednostce Centrum zdarzeń przyznania najmniej uprzywilejowanego dostępu.|
|Machine|50|Zainstaluj agenta monitorowania na maszynach|Zainstaluj agenta monitorowania, aby włączyć zbieranie danych, aktualizacje skanowania, skanowania linii bazowej i programem endpoint protection na każdym komputerze.|
|Maszyna|50|Włączanie automatycznej aprowizacji i zbierania danych dla subskrypcji |Włączanie automatycznej aprowizacji i zbierania danych dla maszyn w Twoich subskrypcjach, aby włączyć zbieranie danych aktualizacji skanowania, skanowania linii bazowej i programem endpoint protection na każdej maszynie, dodać do swojej subskrypcji.|
|Maszyna|40|Rozwiąż problemy dotyczące kondycji agenta monitorowania na maszynach|Dla pełnej ochrony usługa Security Center należy rozwiązać problemy dotyczące monitorowania agentów na maszynach zgodnie z instrukcjami w przewodniku rozwiązywania problemów| 
|Maszyna|40|Rozwiąż problemy dotyczące kondycji ochrony punktu końcowego na maszynach|Dla pełnej ochrony usługa Security Center należy rozwiązać problemy dotyczące monitorowania agenta na maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.|
|Maszyna|40|Rozwiąż problemy dotyczące brakujących danych skanowania na maszynach|Rozwiązywanie problemów z Brak danych skanowania na maszynach wirtualnych i komputerów. Brak danych skanowania na wyniki maszyn brakujących oceny zabezpieczeń, takich jak aktualizacji, skanowania linii bazowej skanowanie i brak Trwa skanowanie rozwiązania ochrony punktu końcowego.|
|Maszyna|40|Zainstaluj aktualizacje systemu na maszynach|Instalowanie brakujących zabezpieczeń systemu i zabezpieczanie maszyn wirtualnych Windows i Linux oraz komputerach krytyczne aktualizacje
|Machine|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie umożliwiającymi zainstalowanie zapory aplikacji sieci web w celu zabezpieczenia aplikacji sieci web. |
|Machine|40|Zaktualizuj wersję systemu operacyjnego dla ról usługi w chmurze|Zaktualizuj wersję systemu operacyjnego (OS) dla ról usługi w chmurze do najnowszej wersji dostępnej dla Twojej rodziny systemów operacyjnych.|
|Maszyna|35|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach|Korygowanie luk w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach w taki sposób, aby chronić je przed atakami. |
|Maszyna|35|Korygowanie luk w zabezpieczeniach w konfiguracji zabezpieczeń na kontenerów|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach z zainstalowaną platformą Docker, aby ochronić je przed atakami.|
|Maszyna|25|Włącz adaptacyjne kontrole aplikacji|Włącz aplikację formantami, które aplikacje mogą być uruchamiane na maszynach wirtualnych na platformie Azure. Ułatwi to zabezpiecza maszyny wirtualne przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego w celu analizuje aplikacje uruchomione na każdej maszynie Wirtualnej i pomaga stosowanych umożliwiają reguły za pomocą tej analizy. Ta funkcja upraszcza proces konfigurowania i utrzymywania aplikacji umożliwiają reguły.|
|Maszyna|20|Zainstaluj rozwiązanie do ochrony punktu końcowego na maszynach|Zainstalować rozwiązanie do ochrony punktu końcowego na maszynach wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach.|
|Maszyna|20|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu i zabezpieczyć maszyny przed lukami w zabezpieczeniach.|
|Machine|15|Zastosuj szyfrowanie dysków na maszynach wirtualnych|Szyfrowanie dysków maszyn wirtualnych przy użyciu usługi Azure Disk Encryption zarówno dla maszyn wirtualnych Windows i Linux. Usługa Azure dysku Encryption (ADE) korzysta z branży standardowych funkcji BitLocker Windows oraz funkcji DM-Crypt systemu Linux w celu zapewnienia szyfrowania dysku systemu operacyjnego i danych, aby pomóc chronić dane i sprostać organizacyjne bezpieczeństwa i zgodności zobowiązań klientów usługi Azure key vault. Gdy Twoje wymagania zgodności i zabezpieczeń wymaga szyfrowania danych typu end to end przy użyciu kluczy szyfrowania, łącznie z szyfrowaniem efemerycznego dysku (tymczasowo podłączonych lokalnie), użyj usługa Azure disk encryption. Alternatywnie domyślnie Managed Disks są magazynowanych domyślnie szyfrowane za pomocą usługi Azure Storage Service Encryption, gdzie klucze szyfrowania są klucze zarządzany przez firmę Microsoft na platformie Azure. Jeśli to nie spełnia wymagania zabezpieczeń i zgodności, na których, można korzystać z szyfrowania dysku zarządzanego domyślne, zgodnie z wymaganiami.|
|Machine|30|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie umożliwiającymi zainstalowanie zapory aplikacji sieci web w celu zabezpieczenia aplikacji sieci web. |
|Maszyna|30|Korygowanie luk w zabezpieczeniach przy użyciu rozwiązania do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono luk w zabezpieczeniach 3rd strona rozwiązania do oceny są stale oceniane względem aplikacji i luk w zabezpieczeniach systemu operacyjnego. Zawsze, gdy zostaną znalezione lukami umożliwiającymi takie ataki, są dostępne, aby uzyskać więcej informacji, w ramach zalecenia.|
|Machine|30|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Machine|1|Migrowanie maszyn wirtualnych na nowe zasoby usługi Azure Resource Manager|Usługa Azure Resource Manager dla maszyn wirtualnych zapewnienie wzmocnienia zabezpieczeń, takich jak: silniejsze kontroli dostępu (RBAC), lepiej inspekcji, wdrażania usługi Resource Manager i nadzoru, dostęp do zarządzanych tożsamości i dostępu do magazynu kluczy dla wpisów tajnych oraz ich Uwierzytelnianie na podstawie usługi AD systemu Azure i obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami. |
|Machine|30|Korygowanie luk w zabezpieczeniach przy użyciu rozwiązania do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono luk w zabezpieczeniach 3rd strona rozwiązania do oceny są stale oceniane względem aplikacji i luk w zabezpieczeniach systemu operacyjnego. Zawsze, gdy zostaną znalezione lukami umożliwiającymi takie ataki, są dostępne, aby uzyskać więcej informacji, w ramach zalecenia.|
|Zestaw skalowania maszyn wirtualnych |4|Włącz dzienniki diagnostyczne w usłudze Virtual Machine Scale Sets|Włączanie dzienników i Zachowaj ich do jednego roku. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy. Jest to przydatne, gdy wystąpi incydent zabezpieczeń lub złamania zabezpieczeń sieci.|
|Zestaw skalowania maszyn wirtualnych|35|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń zestawów skalowania maszyn wirtualnych|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń zestawów skalowania maszyn wirtualnych, aby ochronić je przed atakami. |
|Zestaw skalowania maszyn wirtualnych|5|Skoryguj błędy kondycji ochrony punktu końcowego w zestawach skalowania maszyn wirtualnych|Skoryguj błędy kondycji ochrony punktu końcowego w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|10|Instaluj rozwiązanie do ochrony punktu końcowego w zestawach skalowania maszyn wirtualnych|Zainstaluj rozwiązanie do ochrony punktu końcowego na Twoje zestawy skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|40|Instaluj aktualizacje systemu w zestawach skalowania maszyn wirtualnych|Zainstaluj brakujące aktualizacje krytyczne oraz aktualizacje zabezpieczeń systemu w celu zabezpieczenia zestawów skalowania maszyn wirtualnych z systemem Windows i Linux. |
 





## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:


* [Omówienie usługi Azure Security Center zaleceń dotyczących maszyn wirtualnych](security-center-virtual-machine-recommendations.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

