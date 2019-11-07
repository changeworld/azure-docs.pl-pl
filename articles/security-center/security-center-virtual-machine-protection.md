---
title: Ochrona maszyn i aplikacji w Azure Security Center
description: Ten dokument zawiera zalecenia dotyczące Security Center, które pomagają chronić maszyny wirtualne i komputery oraz aplikacje sieci Web i środowiska App Service.
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
ms.openlocfilehash: 803e64c9df1b52a33a1b50714f77b005032bf200
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686358"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrona maszyn i aplikacji w Azure Security Center
Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure, serwerów spoza platformy Azure i maszyn wirtualnych. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek. Zalecenia dotyczą typów zasobów platformy Azure: maszyny wirtualne (maszyny wirtualne) i komputery, aplikacje, sieci, SQL i tożsamość i dostęp.

Ten artykuł dotyczy zaleceń dotyczących maszyn i aplikacji.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Stan zabezpieczeń zasobów można monitorować na pulpicie nawigacyjnym **Security Center — przegląd** . Sekcja **resources** zawiera zidentyfikowaną liczbę problemów i stan zabezpieczeń dla każdego typu zasobu.

Listę wszystkich problemów można wyświetlić, wybierając pozycję **zalecenia**. Więcej informacji dotyczących stosowania rekomendacji można znaleźć w temacie [Wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

Aby uzyskać pełną listę zaleceń dotyczących usług obliczeniowych i aplikacji, zobacz [zalecenia](security-center-virtual-machine-protection.md#compute-and-app-recs).

Aby kontynuować, wybierz pozycję **oblicz & aplikacje** w obszarze **zasoby** lub Security Center menu główne.
![pulpit nawigacyjny Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Monitorowanie usług obliczeniowych i aplikacji
W obszarze **obliczeniowe & aplikacje**istnieją następujące karty:

- **Omówienie**: monitorowanie i zalecenia zidentyfikowane przez usługę Security Center.
- **Maszyny wirtualne i komputery**: lista maszyn wirtualnych i komputerów oraz ich bieżący stan zabezpieczeń.
- **Usługi w chmurze**: lista ról internetowych i procesów roboczych monitorowanych przez usługę Security Center.
- **App Services**: Lista środowisk usługi App Service i bieżący stan zabezpieczeń każdego z nich.
- **Kontenery**: Lista kontenerów i Ocena zabezpieczeń ich konfiguracji.
- **Zasoby obliczeniowe (wersja zapoznawcza)** : Lista zaleceń dotyczących zasobów obliczeniowych, takich jak klastry Service Fabric i centra zdarzeń.

Aby kontynuować, wybierz pozycję **obliczeniowe & aplikacje** w obszarze **higiena zabezpieczeń zasobów**.

![Wystąpienia obliczeniowe](./media/security-center-virtual-machine-recommendations/compute.png)

Na każdej karcie znajduje się kilka sekcji, a w każdej sekcji można wybrać poszczególne opcje, aby wyświetlić więcej szczegółowych informacji dotyczących zalecanych kroków rozwiązania konkretnego problemu.

### Niemonitorowane maszyny wirtualne i komputery<a name="unmonitored-vms-and-computers"></a>
Maszyna wirtualna lub komputer nie jest monitorowany przez Security Center, jeśli na komputerze nie jest uruchomione rozszerzenie Microsoft Monitoring Agent. Na komputerze może być już zainstalowany agent lokalny, na przykład Agent usługi OMS Direct lub Agent System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ nie są w pełni obsługiwane w Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

Możesz zainstalować rozszerzenie na niemonitorowanej maszynie wirtualnej lub komputerze oprócz już zainstalowanego agenta lokalnego. Obu agentów należy skonfigurować tak samo, łącząc ich z tym samym obszarem roboczym. Dzięki temu usługa Security Center może wchodzić w interakcje z rozszerzeniem programu Microsoft Monitoring Agent i zbierać dane. Zobacz [Włączanie rozszerzenia maszyny wirtualnej](../azure-monitor/learn/quick-collect-azurevm.md), aby uzyskać instrukcje dotyczące sposobu instalowania rozszerzenia programu Microsoft Monitoring Agent.

Zobacz [Monitorowanie problemów dotyczących kondycji agenta](security-center-troubleshooting-guide.md#mon-agent), aby dowiedzieć się więcej na temat powodów, dla których usługa Security Center nie może prawidłowo monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji.

### <a name="recommendations"></a>Polecane elementy
Ta sekcja zawiera zestaw zaleceń dotyczących każdej maszyny wirtualnej i komputera, roli sieci Web i procesu roboczego, Azure App Service Web Apps i Azure App Service Environment Security Center monitorów. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera łączną liczbę zasobów, na które mają wpływ te zalecenia. Trzecia kolumna przedstawia ważność problemu.

Każde zalecenie zawiera zestaw akcji, które można wykonać po ich wybraniu. Na przykład w przypadku wybrania opcji **brakujące aktualizacje systemu**zostanie wyświetlona liczba maszyn wirtualnych i komputerów, na których brakuje poprawek, oraz ważność brakującej aktualizacji.

**Zastosowanie aktualizacji systemu** zawiera podsumowanie aktualizacji krytycznych w formacie grafu, po jednym dla systemu Windows, a drugi w systemie Linux. Druga część zawiera tabelę z następującymi informacjami:

- **NAZWA**: nazwa brakującej aktualizacji.
- **Nie. Maszyn wirtualnych & komputerów**: Łączna liczba maszyn wirtualnych i komputerów, na których brakuje tej aktualizacji.
- **Ważność aktualizacji**: opisuje ważność danego zalecenia:

    - **Krytyczne**: istnieje luka w zabezpieczeniach z istotnym zasobem (aplikacją, maszyną wirtualną lub sieciową grupą zabezpieczeń) i wymaga uwagi.
    - **Ważne**: niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
    - **Umiarkowane**: należy rozwiązać usterkę, ale nie wymaga ona natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).


- **STAN**: bieżący stan zalecenia:

    - **Otwarte**: nie rozpoczęto jeszcze wykonywania zalecenia.
    - **W toku**: zalecenie jest aktualnie stosowane do danych zasobów, żadna akcja użytkownika nie jest wymagana.
    - **Rozwiązane**: zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

Aby wyświetlić szczegóły zalecenia, kliknij nazwę brakującej aktualizacji na liście.


> [!NOTE]
> Zalecenia dotyczące zabezpieczeń są takie same jak te, które znajdują się na kafelku **zalecenia** . Aby uzyskać więcej informacji o sposobach rozwiązywania zaleceń, zobacz [implementowanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Maszyny wirtualne i komputery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących maszyn wirtualnych i komputerów. Każda kolumna reprezentuje jeden zestaw zaleceń.

![Rekomendacje dotyczące maszyn wirtualnych i komputerów](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Istnieją cztery typy ikon reprezentowane na tej liście:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Azure Resource Manager maszynę wirtualną](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager maszynę wirtualną.

![Klasyczna maszyna wirtualna platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.


![Maszyny wirtualne zidentyfikowane w obszarze roboczym](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które są raportowane do obszaru roboczego w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane przy użyciu agenta Operations Manager bezpośredniego, i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko określić maszynę wirtualną i komputer wymagający uwagi oraz typ zalecenia. Możesz również użyć filtrów, aby przeszukać listę według **typu zasobów** i według **ważności**.

Aby przejść do szczegółów zaleceń dotyczących zabezpieczeń dla każdej maszyny wirtualnej, kliknij maszynę wirtualną.
W tym miejscu są wyświetlane szczegóły zabezpieczeń dotyczące maszyny wirtualnej lub komputera. Na dole można zobaczyć zalecaną akcję i ważność poszczególnych problemów.
![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Usługi w chmurze
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie (w przypadku poprzedniego przykładu), należy wykonać czynności opisane w zaleceniach, aby zaktualizować wersję systemu operacyjnego. Gdy aktualizacja jest dostępna, zostanie wyświetlony alert (czerwony lub pomarańczowy — w zależności od ważności problemu). Po wybraniu tego alertu w WebRole1 (uruchamia system Windows Server z aplikacją sieci Web automatycznie wdrożoną w usługach IIS) lub WorkerRole1 (system Windows Server z aplikacją sieci Web automatycznie wdrożoną w usługach IIS) zobaczysz więcej szczegółowych informacji na temat tego zalecenia.

Aby zobaczyć więcej normatywnych wyjaśnień dotyczących tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **OPIS**.



![Aktualizacja wersji systemu operacyjnego](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>Usługi aplikacji
Aby wyświetlić informacje App Service, należy włączyć App Service w subskrypcji. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [ochrona App Service przy użyciu Azure Security Center](security-center-app-services.md).
[!NOTE]
> App Service monitorowania jest w wersji zapoznawczej i jest dostępna tylko w warstwie Standardowa Security Center.


W obszarze **usługi App Services**znajdziesz listę środowisk usługi App Service oraz podsumowanie kondycji w oparciu o wykonane Security Center oceny.

![Usługi aplikacji](./media/security-center-virtual-machine-recommendations/app-services.png)

Istnieją trzy typy ikon reprezentowane na tej liście:

![Środowisko usługi App Services](./media/security-center-virtual-machine-recommendations/ase.png) Środowisko usługi App Services.

![Aplikacja internetowa](./media/security-center-virtual-machine-recommendations/web-app.png) Aplikacja sieci Web.

![Aplikacja funkcji](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikacja funkcji.

1. Wybierz aplikację sieci Web. Zostanie otwarty widok podsumowania z trzema kartami:

   - **Zalecenia**: na podstawie ocen wykonanych przez Security Center zakończonych niepowodzeniem.
   - Przeprowadzono **oceny**: Lista ocen wykonanych przez Security Center, które zakończono.
   - **Niedostępne oceny**: Lista ocen, których uruchomienie nie powiodło się z powodu błędu lub zalecenie nie jest istotne dla określonej usługi App Service

   W obszarze **zalecenia** znajduje się lista zaleceń dotyczących wybranej aplikacji sieci Web i ważności poszczególnych zaleceń.

   ![Zalecenia dotyczące App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Wybierz zalecenie, aby wyświetlić opis zalecenia i listę zasobów w złej kondycji, zdrowe zasoby i niezeskanowane zasoby.

   - W kolumnie **przekazano oceny** jest to Lista przetestowanych ocen. Ważność tych ocen jest zawsze zielona.

   - Wybierz przekazaną ocenę z listy, aby zapoznać się z opisem oceny, listą nieprawidłowych i zdrowych zasobów oraz listą niezeskanowanych zasobów. Istnieje karta dla zasobów o złej kondycji, ale ta lista jest zawsze pusta od czasu przeprowadzenia oceny.

     ![Korygowanie App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych
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


## Zalecenia dotyczące obliczeń i aplikacji<a name="compute-and-app-recs"></a>
|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Usługa App Service|20|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ogranicz dostęp do aplikacji sieci Web tylko za pośrednictwem protokołu HTTPS.|
|Usługa App Service|20|aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ogranicz dostęp do aplikacji funkcji tylko za pośrednictwem protokołu HTTPS.|
|Usługa App Service|5|Dzienniki diagnostyczne w App Services powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Usługa App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Wyłącz debugowanie aplikacji sieci Web, jeśli nie potrzebujesz już jej używać. Zdalne debugowanie wymaga otwarcia portów przychodzących na aplikacja funkcji.|
|Usługa App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji|Wyłącz debugowanie dla aplikacja funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga otwarcia portów przychodzących na aplikacja funkcji.|
|Usługa App Service|10|Nie Zezwalaj wszystkim zasobom ("*") na dostęp do aplikacji| Nie Zezwalaj na zestaw parametrów WEBSITE_LOAD_CERTIFICATES "". Ustawienie parametru na "" oznacza, że wszystkie certyfikaty są ładowane do osobistego magazynu certyfikatów aplikacji sieci Web. Może to prowadzić do nadużycia zasady najniższych uprawnień, ponieważ jest mało prawdopodobne, że lokacja musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania.|
|Usługa App Service|20|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Zezwalaj na współpracującie z aplikacją sieci Web tylko wymaganych domen. Udostępnianie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji sieci Web.|
|Usługa App Service|20|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacja funkcji| Zezwalaj na współdziałanie z aplikacją funkcji tylko dla wymaganych domen. Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji.|
|Zasoby obliczeniowe (Batch)|1|Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych|Skonfiguruj reguły alertów metryki na koncie wsadowym i Włącz opcję Usuń wszystkie zdarzenia i Usuń zdarzenia uruchamiania puli metryk|
|Zasoby obliczeniowe (Usługa Service Fabric)|10|W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów|Uwierzytelnianie klienta jest wykonywane tylko za pośrednictwem Azure Active Directory w Service Fabric.|
|Zasoby obliczeniowe (konto usługi Automation)|5|Zmienne konta usługi Automation powinny być szyfrowane|Włącz szyfrowanie zasobów zmiennych konta usługi Automation podczas przechowywania poufnych danych.|
|Zasoby obliczeniowe (wyszukiwanie)|5|Inspekcja włączenia dzienników diagnostycznych dla usług wyszukiwania|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Magistrala usług)|5|Dzienniki diagnostyczne w Service Bus powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Stream Analytics)|5|Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Batch)|5|Włączanie dzienników diagnostycznych na kontach wsadowych|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (centrum zdarzeń)|5|Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Aplikacje logiki)|5|Włączanie dzienników diagnostycznych w Logic Apps|Włącz dzienniki i zachowaj je na rok. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |
|Zasoby obliczeniowe (Usługa Service Fabric)|15|Ustaw właściwość ClusterProtectionLevel na EncryptAndSign w Service Fabric|Service Fabric zapewnia trzy poziomy ochrony (brak, podpisywanie i EncryptAndSign) w przypadku komunikacji między węzłami przy użyciu podstawowego certyfikatu klastra. Ustaw poziom ochrony, aby upewnić się, że wszystkie komunikaty między węzłami są szyfrowane i podpisane cyfrowo. |
|Zasoby obliczeniowe (Magistrala usług)|1|Usuń wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw Service Bus |Klienci Service Bus nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Zasoby obliczeniowe (centrum zdarzeń)|1|Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń|Klienci centrum zdarzeń nie powinni używać zasad dostępu na poziomie przestrzeni nazw, które zapewniają dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby dostosować model zabezpieczeń najniższych uprawnień, należy utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp tylko do określonej jednostki.|
|Zasoby obliczeniowe (centrum zdarzeń)|5|Należy zdefiniować reguły autoryzacji w jednostce centrum zdarzeń|Przeprowadź inspekcję reguł autoryzacji w jednostce centrum zdarzeń, aby udzielić dostępu z najwyższymi uprawnieniami.|
|Maszyna|50|Zainstaluj agenta monitorowania na swoich maszynach|Zainstaluj agenta monitorowania, aby włączyć zbieranie danych, skanowanie aktualizacji, skanowanie linii bazowej i program Endpoint Protection na poszczególnych komputerach.|
|Maszyna|50|Włącz automatyczną obsługę administracyjną i zbieranie danych dla subskrypcji |Włącz automatyczne Inicjowanie obsługi i zbieranie danych dla maszyn w Twoich subskrypcjach, aby umożliwić zbieranie danych, skanowanie aktualizacji, skanowanie linii bazowej i program Endpoint Protection na każdej maszynie, która została dodana do subskrypcji.|
|Maszyna|40|Rozwiązywanie problemów z kondycją agenta monitorowania na maszynach|Aby uzyskać pełną ochronę Security Center, należy rozwiązać problemy z agentem monitorowania na swoich maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.| 
|Maszyna|40|Rozwiązywanie problemów z kondycją programu Endpoint Protection na maszynach|Aby uzyskać pełną ochronę Security Center, należy rozwiązać problemy z agentem monitorowania na swoich maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.|
|Maszyna|40|Rozwiązywanie problemów z brakującymi danymi skanowania na maszynach|Rozwiązywanie problemów z brakującymi danymi skanowania na maszynach wirtualnych i komputerach. Brak danych skanowania na swoich maszynach powoduje brak oceny zabezpieczeń, takich jak skanowanie aktualizacji, skanowanie linii bazowej i brak skanowania rozwiązań Endpoint Protection.|
|Maszyna|40|Aktualizacje systemu powinny być zainstalowane na maszynach|Instalowanie brakujących zabezpieczeń systemu i aktualizacji krytycznych w celu zabezpieczania maszyn wirtualnych i komputerów z systemami Windows i Linux
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie zapory aplikacji sieci Web (WAF), aby zabezpieczyć aplikacje sieci Web. |
|Maszyna|40|Aktualizowanie wersji systemu operacyjnego dla ról usługi w chmurze|Zaktualizuj wersję systemu operacyjnego dla ról usługi w chmurze do najnowszej wersji dostępnej dla rodziny systemów operacyjnych.|
|Maszyna|35|Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować|Koryguj luki w zabezpieczeniach na maszynach w celu ochrony przed atakami.|
|Maszyna|35|Koryguj luki w zabezpieczeniach w konfiguracjach w kontenerach|Koryguj luki w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach z zainstalowanym systemem Docker, aby chronić je przed atakami.|
|Maszyna|25|Włącz adaptacyjne kontrolki aplikacji|Włącz sterowanie aplikacjami, aby kontrolować, które aplikacje mogą być uruchamiane na maszynach wirtualnych znajdujących się na platformie Azure. Ułatwi to ochronę maszyn wirtualnych przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach wirtualnych i ułatwia stosowanie reguł zezwalania przy użyciu tej analizy. Ta funkcja upraszcza proces konfigurowania i konserwowania reguł zezwalania aplikacji.|
|Maszyna|20|Zainstaluj rozwiązanie Endpoint Protection na swoich maszynach|Zainstaluj rozwiązanie Endpoint Protection na maszynach wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach.|
|Maszyna|20|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu i zabezpieczyć maszynę przed lukami w zabezpieczeniach.|
|Maszyna|15|Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Szyfruj dyski maszyn wirtualnych przy użyciu Azure Disk Encryption obu dla maszyn wirtualnych z systemami Windows i Linux. Azure Disk Encryption (ADE) korzysta z standardowej funkcji funkcji BitLocker systemu Windows i funkcji DM-Crypt w systemie Linux, aby zapewnić szyfrowanie dysków systemu operacyjnego i danych w celu zapewnienia lepszej ochrony i ochrony danych oraz zapewnienia bezpieczeństwa i zgodności w organizacji. zobowiązania w magazynie kluczy platformy Azure. Jeśli wymagania dotyczące zgodności i zabezpieczeń wymagają szyfrowania danych końcowych przy użyciu kluczy szyfrowania, w tym szyfrowania tymczasowego dysku z załączonym lokalnie, Użyj usługi Azure Disk Encryption. Alternatywnie domyślnie Managed Disks są szyfrowane domyślnie przy użyciu usługi Azure szyfrowanie usługi Storage, gdzie klucze szyfrowania są kluczami zarządzanymi przez firmę Microsoft na platformie Azure. Jeśli spełnia wymagania związane ze zgodnością i zabezpieczeniami, można użyć domyślnego szyfrowania dysków zarządzanych do spełnienia wymagań.|
|Maszyna|30|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie zapory aplikacji sieci Web (WAF), aby zabezpieczyć aplikacje sieci Web. |
|Maszyna|30|Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono rozwiązanie innej firmy do oceny luk w zabezpieczeniach, są stale oceniane pod kątem luk w zabezpieczeniach aplikacji i systemu operacyjnego. Po każdym znalezieniu tych luk w zabezpieczeniach można uzyskać więcej informacji w ramach zalecenia.|
|Maszyna|30|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instalowanie rozwiązania do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|1|Maszyny wirtualne powinny być migrowane do nowych zasobów AzureRM|Użyj Azure Resource Manager dla maszyn wirtualnych, aby zapewnić lepsze zabezpieczenia, takie jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, wdrażanie i zarządzanie oparte na Menedżer zasobówach, dostęp do zarządzanych tożsamości, dostęp do magazynu kluczy dla wpisów tajnych, Uwierzytelnianie oparte na usłudze Azure AD i obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami. |
|Maszyna|30|Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono rozwiązanie innej firmy do oceny luk w zabezpieczeniach, są stale oceniane pod kątem luk w zabezpieczeniach aplikacji i systemu operacyjnego. Po każdym znalezieniu tych luk w zabezpieczeniach można uzyskać więcej informacji w ramach zalecenia.|
|Zestaw skalowania maszyn wirtualnych |4|Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone|Włącz dzienniki i zachowaj je przez maksymalnie rok. Dzięki temu można odtworzyć ślady aktywności do celów dochodzeniowych. Jest to przydatne w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci.|
|Zestaw skalowania maszyn wirtualnych|35|Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować|Koryguj luki w zabezpieczeniach w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |
|Zestaw skalowania maszyn wirtualnych|5|Koryguj błędy kondycji programu Endpoint Protection w zestawach skalowania maszyn wirtualnych|Skoryguj błędy kondycji programu Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|10|Program Endpoint Protection powinien być zainstalowany na maszynach wirtualnych|Zainstaluj rozwiązanie Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |
|Zestaw skalowania maszyn wirtualnych|40|Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Zainstaluj brakujące zabezpieczenia systemu i aktualizacje krytyczne, aby zabezpieczyć zestawy skalowania maszyn wirtualnych z systemami Windows i Linux. |
 





## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:


* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

