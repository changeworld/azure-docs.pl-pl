---
title: Ochrona maszyn i aplikacji w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Tym dokumencie przedstawiono zalecenia w usłudze Security Center, które ułatwiają ochronę maszyn wirtualnych i komputerów i aplikacji sieci web i środowisk usługi App Service.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 53a6ae828009db24b56a11b1fdcbea19842191f4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106621"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Ochrona maszyn i aplikacji w usłudze Azure Security Center
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek. Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM) i komputerów, aplikacji, networking, SQL i tożsamościami i dostępem.

W tym artykule opisano zaleceń, które są stosowane do maszyn i aplikacji.

## <a name="monitoring-security-health"></a>Monitorowanie kondycji zabezpieczeń
Możesz monitorować stan zabezpieczeń zasobów na **Security Center — Przegląd** pulpitu nawigacyjnego. **Zasobów** sekcji omówiono liczbę zidentyfikowane problemy i stan zabezpieczeń dla każdego typu zasobu.

Można wyświetlić listę wszystkich problemów, wybierając **zalecenia**. Aby uzyskać więcej informacji na temat stosowania zaleceń, zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md).

Aby uzyskać pełną listę zasobów obliczeniowych i aplikacji usług zalecenia, zobacz [zalecenia](security-center-virtual-machine-recommendations.md).

Aby kontynuować, wybierz **obliczenia i aplikacje** w obszarze **zasobów** lub w menu głównym usługi Security Center.
![Pulpit nawigacyjny Centrum zabezpieczeń][1]

## <a name="monitor-compute-and-app-services"></a>Monitorowanie mocy obliczeniowej i aplikacji usług
W obszarze **Obliczenia** znajdują się cztery karty:

- **Omówienie**: monitorowanie i zalecenia zidentyfikowane przez usługę Security Center.
- **Maszyny wirtualne i komputery**: lista maszyn wirtualnych i komputerów oraz ich bieżący stan zabezpieczeń.
- **Usługi w chmurze**: lista ról internetowych i procesów roboczych monitorowanych przez usługę Security Center.
- **Usługi App services (wersja zapoznawcza)**: Lista środowisk usługi App service i bieżący stan zabezpieczeń każdego z nich.
Aby kontynuować, wybierz **obliczenia i aplikacje** w obszarze **zasobów** lub w menu głównym usługi Security Center.

![Wystąpienia obliczeniowe][2]

Na każdej karcie znajduje się kilka sekcji, a w każdej sekcji można wybrać poszczególne opcje, aby wyświetlić więcej szczegółowych informacji dotyczących zalecanych kroków rozwiązania konkretnego problemu.

### <a name="monitoring-recommendations"></a>Zalecenia dotyczące monitorowania
W tej sekcji przedstawiono całkowitą liczbę maszyn wirtualnych i komputerów, dla których zainicjowano automatyczną aprowizację oraz ich bieżący stan. W tym przykładzie jest jedno zalecenie, **Monitorowanie problemów dotyczących kondycji agenta**. Wybierz to zalecenie.

![Monitorowanie problemów dotyczących kondycji agenta][3]

Zostanie otwarte okno **Monitorowanie problemów dotyczących kondycji agenta**. Zawiera ono listę maszyn wirtualnych i komputerów, których usługa Security Center nie mogła pomyślnie monitorować. Wybierz maszynę wirtualną lub komputer, aby uzyskać szczegółowe informacje. **STAN MONITOROWANIA** zapewnia powód, dla którego usługa Security Center nie mogła realizować monitorowania. Zobacz [Przewodnik rozwiązywania problemów w usłudze Security Center](security-center-troubleshooting-guide.md), aby uzyskać listę wartości, opisów i rozwiązań dla opcji **STAN MONITOROWANIA**.

### Niemonitorowane maszyny wirtualne i komputery <a name="unmonitored-vms-and-computers"></a>
Maszynę Wirtualną lub komputer jest niemonitorowana przez usługę Security Center, jeśli komputer nie jest uruchomiona rozszerzenia Microsoft Monitoring Agent. Komputer może mieć zainstalowanego lokalnego agenta, na przykład OMS bezpośrednie lub agent SCOM. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ ci agenci nie są w pełni obsługiwane w usłudze Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie programu Microsoft Monitoring Agent.

Rozszerzenie można zainstalować na niemonitorowanej maszynie Wirtualnej lub komputerze oprócz już zainstalowanego lokalnego agenta. Obu agentów należy skonfigurować tak samo, łącząc ich z tym samym obszarem roboczym. Dzięki temu usługa Security Center może wchodzić w interakcje z rozszerzeniem programu Microsoft Monitoring Agent i zbierać dane. Zobacz [Włączanie rozszerzenia maszyny wirtualnej](../azure-monitor/learn/quick-collect-azurevm.md), aby uzyskać instrukcje dotyczące sposobu instalowania rozszerzenia programu Microsoft Monitoring Agent.

Zobacz [Monitorowanie problemów dotyczących kondycji agenta](security-center-troubleshooting-guide.md#mon-agent), aby dowiedzieć się więcej na temat powodów, dla których usługa Security Center nie może prawidłowo monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji.

### <a name="recommendations"></a>Zalecenia
Ta sekcja zawiera zestaw zaleceń dotyczących każdej maszyny Wirtualnej i komputera, role sieć web i proces roboczy, Azure App Service Web Apps i Azure App Service Environment, która usługa Security Center monitoruje. W pierwszej kolumnie wyświetlane są zalecenia. Druga kolumna zawiera całkowitą liczbę zasobów, których dotyczy tego zalecenia. Trzecia kolumna pokazuje wagę problemu, co przedstawiono na następującym zrzucie ekranu:

![Zalecenia][4]

Każdy zalecenie obejmuje zestaw akcji, które można wykonać po jej wybraniu. Na przykład w przypadku wybrania **brakujące aktualizacje systemu**, liczba maszyn wirtualnych i komputerów, których brakuje poprawek oraz ważność brakujących aktualizacji pojawi się, jak pokazano na poniższym zrzucie ekranu:

![Zastosuj aktualizacje systemu][5]

**Zastosuj aktualizacje systemu** zawiera podsumowanie krytycznych aktualizacji w postaci wykresu: jeden dla Windows i jeden dla systemu Linux. Druga część zawiera tabelę z następującymi informacjami:

- **NAZWA**: nazwa brakującej aktualizacji.
- **LICZBA MASZYN WIRTUALNYCH I KOMPUTERÓW**: łączna liczba maszyn wirtualnych i komputerów, które nie mają tej aktualizacji.
- **WAŻNOŚĆ aktualizacji**: Opisuje ważność określonego zalecenia:

    - **Krytyczne**: luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (aplikacji, maszyny wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
    - **Ważne**: niekrytyczne lub dodatkowe kroki są wymagane do ukończenia procesu lub wyeliminowania luki w zabezpieczeniach.
    - **Umiarkowany**: luki w zabezpieczeniach, powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale możesz ustawić filtr umożliwiający wyświetlanie zaleceń o niskiej ważności, jeśli chcesz je przeglądać).


- **STAN**: bieżący stan zalecenia:

    - **Otwarte**: nie rozpoczęto jeszcze wykonywania zalecenia.
    - **W toku**: zalecenie jest aktualnie stosowane do danych zasobów, żadna akcja użytkownika nie jest wymagana.
    - **Rozwiązane**: zalecenie zostało już zakończone. (Gdy problem został rozwiązany, wpis jest wyszarzony).

Aby wyświetlić szczegóły zalecenia, kliknij nazwę brakującej aktualizacji na liście.

![Szczegóły rekomendacji][6]

> [!NOTE]
> Zalecenia dotyczące zabezpieczeń w tym miejscu są takie same, jak w obszarze **zalecenia** kafelka. Zobacz [wdrażanie zaleceń dotyczących zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md) uzyskać więcej informacji dotyczących stosowania zaleceń.
>
>

### <a name="vms-and-computers"></a>Maszyny wirtualne i komputery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących maszyn wirtualnych i komputerów. Każda kolumna reprezentuje jeden zestaw zaleceń, jak pokazano na poniższym zrzucie ekranu:

![Rekomendacje dotyczące maszyn wirtualnych i komputerów][7]

Istnieją cztery typy ikon przedstawianych na tej liście:

![Komputer spoza platformy Azure][8] Komputer bez platformy Azure.

![Maszyna wirtualna platformy Azure Resource Manager][9] Maszyna wirtualna platformy Azure Resource Manager.

![Klasyczne maszyny Wirtualnej platformy Azure][10] Klasyczna maszyna wirtualna platformy Azure.

![Maszyny wirtualne wskazane w obszarze roboczym][11] Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które podlegają obszarowi roboczemu w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane z agentem bezpośrednim programu SCOM i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko zidentyfikować maszyny Wirtualnej, a komputer, który wymaga uwagi oraz jakiego typu zalecenie ma do nich. Opcja filtrowania umożliwia również wybrać opcje, które będą wyświetlane na tym ekranie.

![Filtr][12]

W poprzednim przykładzie jedna maszyna wirtualna jest krytyczne zalecenie dotyczące ochrony punktów końcowych. Wybierz maszynę Wirtualną, aby uzyskać więcej informacji na ten temat:

![Krytyczne zalecenie][13]

W tym miejscu wyświetlane szczegóły zabezpieczeń dotyczące maszyny Wirtualnej lub komputera. W dolnej części widoczna jest zalecana akcja i ważność poszczególnych problemów.

### <a name="cloud-services"></a>Usługi w chmurze
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna, jak pokazano na poniższym zrzucie ekranu:

![Usługi w chmurze][14]

W scenariuszu, w którym masz zalecenie (co nie jest w przypadku poprzedniego przykładu) należy wykonać kroki zawarte w zaleceniu, aby zaktualizować wersję systemu operacyjnego. Gdy aktualizacja jest dostępna, zostanie wyświetlony alert (czerwony lub pomarańczowy — w zależności od ważności problemu). Po wybraniu tego alertu WebRole1 (działa system Windows Server z aplikacją sieci web automatycznie wdrożoną w usługach IIS) lub WorkerRole1 (działa system Windows Server z aplikacją sieci web automatycznie wdrożoną w usługach IIS) zostanie wyświetlone więcej szczegółów na temat tego zalecenia, jak pokazano w Poniższy zrzut ekranu:

![WorkerRole1][15]

Aby zobaczyć więcej normatywnych wyjaśnień dotyczących tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **OPIS**.

![Aktualizacja wersji systemu operacyjnego][16]

### <a name="app-services-preview"></a>App Services (wersja zapoznawcza)

> [!NOTE]
> Monitorowanie usługi App Service jest dostępna w wersji zapoznawczej i jest dostępna tylko w warstwie standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
>
>

W obszarze **usług App services**Znajdź listę środowisk App service Environment i wykonywane podsumowanie kondycji opartego na ocenie usługi Security Center.

![Usługi aplikacji][17]

Istnieją trzy typy ikon przedstawianych na tej liście:

![Środowiska usług aplikacji][18] Środowiska usług aplikacji.

![Aplikacja internetowa][19] Aplikacja sieci Web.

![Aplikacja funkcji][24] Aplikacja funkcji.

1. Wybierz aplikację sieci web. Otwiera widok podsumowania z trzema kartami:

  - **Zalecenia dotyczące**: oparte na ocen wykonywane przez usługę Security Center, który uległ awarii.
  - **Przekazano ocen**: Lista ocen wykonywane przez usługę Security Center, które przekazane.
  - **Oceny niedostępne**: Lista ocen, którzy nie uruchomili z powodu błędu lub zalecenie nie jest odpowiednie dla określonej usługi App service

  W obszarze **zalecenia** listę zaleceń dla wybranej aplikacji sieci web i ważność poszczególne zalecenia.

  ![Widok podsumowania][20]

2. Wybierz zalecenie opis zalecenia oraz listę zasoby w złej kondycji, zasoby w dobrej kondycji i nieprzeskanowane zasoby.

  ![Opis rekomendacji][21]

  W obszarze **przekazywane ocen** znajduje się lista oceny zakończone pomyślnie.  Ważność te oceny zawsze ma kolor zielony.

  ![Oceny zakończone pomyślnie][22]

3. Wybierz ocenę przekazany z listy opis oceny, lista zasobów w złej kondycji i w dobrej kondycji i listę nieprzeskanowane zasoby. Ma kartę zasoby w złej kondycji, ale ta lista jest zawsze pusta, ponieważ przekazana oceny.

    ![Zasoby w dobrej kondycji][23]

## <a name="compute-and-app-recommendations"></a>Zalecenia dotyczące obliczeń i aplikacji
|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Maszyna|50|Zainstaluj agenta monitorowania na maszynach|Zainstaluj agenta monitorowania, aby włączyć zbieranie danych, aktualizacje skanowania, skanowania linii bazowej i programem endpoint protection na każdym komputerze.|
|Maszyna|50|Włączanie automatycznej aprowizacji i zbierania danych dla subskrypcji |Włączanie automatycznej aprowizacji i zbierania danych dla maszyn w Twoich subskrypcjach, aby włączyć zbieranie danych aktualizacji skanowania, skanowania linii bazowej i programem endpoint protection na każdej maszynie, dodać do swojej subskrypcji.|
|Maszyna|40|Rozwiąż problemy dotyczące kondycji agenta monitorowania na maszynach|Dla pełnej ochrony usługa Security Center należy rozwiązać problemy dotyczące monitorowania agentów na maszynach zgodnie z instrukcjami w przewodniku rozwiązywania problemów| 
|Maszyna|40|Rozwiąż problemy dotyczące kondycji ochrony punktu końcowego na maszynach|Dla pełnej ochrony usługa Security Center należy rozwiązać problemy dotyczące monitorowania agenta na maszynach, postępując zgodnie z instrukcjami w przewodniku rozwiązywania problemów.|
|Maszyna|40|Rozwiąż problemy dotyczące brakujących danych skanowania na maszynach|Rozwiązywanie problemów z Brak danych skanowania na maszynach wirtualnych i komputerów. Brak danych skanowania na wyniki maszyn brakujących oceny zabezpieczeń, takich jak aktualizacji, skanowania linii bazowej skanowanie i brak Trwa skanowanie rozwiązania ochrony punktu końcowego.|
|Maszyna|40|Zainstaluj aktualizacje systemu na maszynach|Instalowanie brakujących zabezpieczeń systemu i zabezpieczanie maszyn wirtualnych Windows i Linux oraz komputerach krytyczne aktualizacje
|Maszyna|40|Zaktualizuj wersję systemu operacyjnego dla ról usługi w chmurze|Zaktualizuj wersję systemu operacyjnego (OS) dla ról usługi w chmurze do najnowszej wersji dostępnej dla Twojej rodziny systemów operacyjnych.|
|Maszyna|35|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach|Korygowanie luk w zabezpieczeniach w konfiguracji zabezpieczeń na maszynach w taki sposób, aby chronić je przed atakami. |
|Maszyna|35|Korygowanie luk w zabezpieczeniach w konfiguracji zabezpieczeń na kontenerów|Skoryguj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach z zainstalowaną platformą Docker, aby ochronić je przed atakami.|
|Maszyna|25|Włącz adaptacyjne kontrole aplikacji|Włącz aplikację formantami, które aplikacje mogą być uruchamiane na maszynach wirtualnych na platformie Azure. Ułatwi to zabezpiecza maszyny wirtualne przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego w celu analizuje aplikacje uruchomione na każdej maszynie Wirtualnej i pomaga stosowanych umożliwiają reguły za pomocą tej analizy. Ta funkcja upraszcza proces konfigurowania i utrzymywania aplikacji umożliwiają reguły.|
|Maszyna|20|Zainstaluj rozwiązanie do ochrony punktu końcowego na maszynach|Zainstalować rozwiązanie do ochrony punktu końcowego na maszynach wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach.|
|Maszyna|20|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu i zabezpieczyć maszyny przed lukami w zabezpieczeniach.|
|App Service|20|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Tylko ograniczenie dostępu do aplikacji sieci Web przy użyciu protokołu HTTPS.|
|App Service|20|Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS|Tylko ograniczenie dostępu do aplikacji funkcji przy użyciu protokołu HTTPS.|
|Maszyna|15|Zastosuj szyfrowanie dysków na maszynach wirtualnych|Szyfrowanie dysków maszyn wirtualnych przy użyciu usługi Azure Disk Encryption zarówno dla maszyn wirtualnych Windows i Linux. Usługa Azure dysku Encryption (ADE) korzysta z branży standardowych funkcji BitLocker Windows oraz funkcji DM-Crypt systemu Linux w celu zapewnienia szyfrowania dysku systemu operacyjnego i danych, aby pomóc chronić dane i sprostać organizacyjne bezpieczeństwa i zgodności zobowiązań klientów usługi Azure key vault. Gdy Twoje wymagania zgodności i zabezpieczeń wymaga szyfrowania danych typu end to end przy użyciu kluczy szyfrowania, łącznie z szyfrowaniem efemerycznego dysku (tymczasowo podłączonych lokalnie), użyj usługa Azure disk encryption. Alternatywnie domyślnie Managed Disks są magazynowanych domyślnie szyfrowane za pomocą usługi Azure Storage Service Encryption, gdzie klucze szyfrowania są klucze zarządzany przez firmę Microsoft na platformie Azure. Jeśli to nie spełnia wymagania zabezpieczeń i zgodności, na których, można korzystać z szyfrowania dysku zarządzanego domyślne, zgodnie z wymaganiami.|
|(Usługa Service fabric) zasobów obliczeniowych|10|Usługa Azure Active Directory do uwierzytelniania klientów w usłudze Service Fabric|W usłudze Service Fabric, należy wykonać uwierzytelnianie klientów tylko przy użyciu usługi Azure Active Directory.|
|(Konto usługi automation) zasobów obliczeniowych|5| Włącz szyfrowanie konta usługi Automation|W przypadku przechowywania poufnych danych, należy włączyć szyfrowanie zmiennych elementów zawartości konta usługi Automation.|
|App Service|5|Włącz dzienniki diagnostyki w usłudze App service|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Moduł równoważenia obciążenia) zasobów obliczeniowych|5|Włączanie dzienników diagnostycznych w module równoważenia obciążenia|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Wyszukiwanie) zasobów obliczeniowych|5|Włącz dzienniki diagnostyczne usługi Search|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service bus) zasobów obliczeniowych|5|Włącz dzienniki diagnostyki w usłudze Service Bus|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa stream analytics) zasobów obliczeniowych|5|Włącz dzienniki diagnostyki w usłudze Azure Stream Analytics|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|(Usługa Service fabric) zasobów obliczeniowych|5|Włącz dzienniki diagnostyki w usłudze Service Fabric|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasobów (wsadowe)|5|Włączanie dzienników diagnostycznych w ramach kont usługi Batch|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasobów (Centrum zdarzeń)|5|Włącz dzienniki diagnostyki w Centrum zdarzeń|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Obliczenia zasoby (aplikacje Logic Apps)|5|Włącz dzienniki diagnostyki w usłudze Logic Apps|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Maszyna|30|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie umożliwiającymi zainstalowanie zapory aplikacji sieci web w celu zabezpieczenia aplikacji sieci web. |
|Maszyna|30|Korygowanie luk w zabezpieczeniach — przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono luk w zabezpieczeniach 3rd strona rozwiązania do oceny są stale oceniane względem aplikacji i luk w zabezpieczeniach systemu operacyjnego. Zawsze, gdy zostaną znalezione lukami umożliwiającymi takie ataki, są dostępne, aby uzyskać więcej informacji, w ramach zalecenia.|
|(Usługa Service fabric) zasobów obliczeniowych|15|Ustaw właściwość ClusterProtectionLevel EncryptAndSign w usłudze Service Fabric|Usługa Service Fabric udostępnia trzy poziomy ochrony (Brak, logowania i EncryptAndSign) do komunikacji między węzłami, przy użyciu certyfikatu klastra podstawowego.  Ustaw poziom ochrony, aby zapewnić zaszyfrowany i podpisany cyfrowo wszystkie komunikaty węzła do węzła. |
|App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Wyłącz opcję debugowania dla aplikacji sieci Web, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji.|
|App Service|10|Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji|Wyłącz opcję debugowania dla aplikacji funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji.|
|App Service|10|Konfiguruj ograniczenia adresów IP dla aplikacji sieci Web|Definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji. Użyj ograniczeń adresów IP chroni aplikację internetową przed typowymi atakami.|
|App Service|10|Konfiguruj ograniczenia adresów IP dla aplikacji funkcji| Definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji. Użyj ograniczeń adresów IP chroni aplikację funkcji przed typowymi atakami.|
|App Service|10|Nie zezwalaj na wszystkie ("*") zasobów uzyskiwanie dostępu do aplikacji| Nie zezwalaj na zestaw parametru WEBSITE_LOAD_CERTIFICATES na "". Ustawienie dla parametru "oznacza, że wszystkie certyfikaty są ładowane do magazynu certyfikatów osobiste aplikacje sieci web. Może to prowadzić do naruszenia zasady najmniejszych uprawnień, ponieważ jest mało prawdopodobne, że witryna musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania.|
|App Service|5|Gniazda sieci Web powinny być wyłączone dla aplikacji sieci Web|Przejrzyj użycie gniazda sieci Web w aplikacjach sieci web. Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa.|
|App Service|5|Gniazda sieci Web powinny być wyłączone dla aplikacji funkcji|Przejrzyj użycie gniazda sieci Web w obrębie aplikacji funkcji. Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa.|
|App Service|5|Użyj domen niestandardowych dla aplikacji sieci Web|Użyj domen niestandardowych, aby zabezpieczyć aplikację sieci web przed typowymi atakami, takimi jak wyłudzanie informacji i inne ataki związane z usługą DNS.|
|App Service|5|Użyj domen niestandardowych dla aplikacji funkcji|Użyj domen niestandardowych, aby chronić aplikacji funkcji przed typowymi atakami, takimi jak wyłudzanie informacji i inne ataki związane z usługą DNS.|
|Obliczenia zasobów (wsadowe)|1|Konfigurowanie reguł alertów dotyczących metryk na koncie usługi Batch|Konfigurowanie reguł alertów dotyczących metryk na koncie usługi Batch i włączyć metryki puli Usuń zakończenie zdarzenia i puli Usuń Start|
|(Usługa Service bus) zasobów obliczeniowych|1|Usuń wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw usługi Service Bus |Klienci usługi Service Bus nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby było zgodne z co najmniej uprawnień zabezpieczeń modelu powinny możesz utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Obliczenia zasobów (Centrum zdarzeń)|1|Usuń wszystkie reguły autoryzacji, z wyjątkiem RootManageSharedAccessKey z przestrzeni nazw Centrum zdarzeń |Klienci Centrum zdarzeń nie należy używać zasady dostępu na poziomie przestrzeni nazw, która zapewnia dostęp do wszystkich kolejek i tematów w przestrzeni nazw. Aby było zgodne z co najmniej uprawnień zabezpieczeń modelu powinny możesz utworzyć zasady dostępu na poziomie jednostki dla kolejek i tematów, aby zapewnić dostęp do określonej jednostki.|
|Obliczenia zasobów (Centrum zdarzeń)|5|Zdefiniuj reguły autoryzacji na jednostce Centrum zdarzeń|Inspekcja reguły autoryzacji na jednostce Centrum zdarzeń przyznania najmniej uprzywilejowanego dostępu.|
|Maszyna|30|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|Instaluj rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych|
|App Service|20|Mechanizm CORS nie powinien zezwalać na każdy zasób dostęp do aplikacji sieci Web|Zezwala na tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe pochodzenia zasobów między źródłami (cors) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do sieci web aplikacji.|
|App Service|20|Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji| Zezwala na tylko wymagane domen do interakcji z aplikacją funkcji. Krzyżowe origin resource sharing (CORS) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do funkcji aplikacji.|
|Maszyna|15|Dodawanie zapory aplikacji internetowej| Wdróż rozwiązanie umożliwiającymi zainstalowanie zapory aplikacji sieci web w celu zabezpieczenia aplikacji sieci web. |
|App Service|10|Użyj najnowszej obsługiwanej wersji systemu .NET Framework dla aplikacji sieci Web|Użyj najnowszej wersji .NET Framework dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację.|
|App Service|10|Użyj najnowszej obsługiwanej wersji środowiska Java dla aplikacji sieci Web|Użyj najnowszej wersji języka Java dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację.|
|App Service|10|Użyj najnowszej obsługiwanej wersji środowiska PHP dla aplikacji sieci Web|Użyj najnowszej wersji języka PHP do najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację.|
|App Service|10|Użyj najnowszej obsługiwanej wersji środowiska Node.js dla aplikacji sieci Web|Użyj najnowszej wersji środowiska Node.js dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację.|
|App Service|10|Użyj najnowszej obsługiwanej wersji środowiska Python dla aplikacji sieci Web|Użyj najnowszej wersji języka Python dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację.|
|Maszyny wirtualne i komputery|1|Migrowanie maszyn wirtualnych na nowe zasoby usługi AzureRM|Użyj nowego 2 usługi Azure Resource Manager dla usługi maszyny wirtualne zapewnienie wzmocnienia zabezpieczeń, takich jak: silniejsze kontroli dostępu (RBAC), lepiej inspekcji, wdrożenie oparte na ARM i nadzoru, dostęp do zarządzanych tożsamości i dostępu do magazynu kluczy dla kluczy tajnych, Azure Uwierzytelnianie za pomocą usługi AD i obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami. |
|Maszyna|30|Korygowanie luk w zabezpieczeniach — przez rozwiązanie do oceny luk w zabezpieczeniach|Maszyny wirtualne, dla których wdrożono luk w zabezpieczeniach 3rd strona rozwiązania do oceny są stale oceniane względem aplikacji i luk w zabezpieczeniach systemu operacyjnego. Zawsze, gdy zostaną znalezione lukami umożliwiającymi takie ataki, są dostępne, aby uzyskać więcej informacji, w ramach zalecenia.|




## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:


* [Omówienie usługi Azure Security Center zaleceń dotyczących maszyn wirtualnych](security-center-virtual-machine-recommendations.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
