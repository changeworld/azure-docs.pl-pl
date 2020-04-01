---
title: Ciągłość działania i odzyskiwanie po awarii
description: Projektowanie strategii w celu ochrony danych, szybkiego odzyskiwania danych po zdarzeniach zakłócających działanie, przywracanie zasobów wymaganych przez krytyczne funkcje biznesowe oraz utrzymywanie ciągłości działania aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437714"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Ciągłość działania i odzyskiwanie po awarii dla aplikacji Azure Logic Apps

Aby zmniejszyć wpływ i skutki nieprzewidywalnych zdarzeń na firmę i klientów, upewnij się, że masz rozwiązanie [ *odzyskiwania po awarii* (DR),](https://en.wikipedia.org/wiki/Disaster_recovery) aby chronić dane, szybko przywrócić zasoby obsługujące krytyczne funkcje biznesowe i kontynuować operacje w celu utrzymania [ *ciągłości działania* (BC).](https://en.wikipedia.org/wiki/Business_continuity_planning) Na przykład zakłócenia mogą obejmować awarie, straty w podstawowej infrastruktury lub składników, takich jak magazyn, sieci lub zasobów obliczeniowych, nieodwracalne błędy aplikacji, a nawet pełne utraty centrum danych. Dzięki przygotowaniu rozwiązania bcdr (continuity business continuity and disaster recovery), przedsiębiorstwo lub organizacja może szybciej reagować na przerwy, planowane lub nieplanowane oraz skracają przestoje klientów.

Ten artykuł zawiera wskazówki i strategie BCDR, które można zastosować podczas tworzenia zautomatyzowanych przepływów pracy przy użyciu [usługi Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Przepływy pracy aplikacji logiki ułatwiają łatwiej integrować i aranżować dane między aplikacjami, usługami w chmurze i systemami lokalnymi, zmniejszając ilość kodu, który musisz zapisać. Planujesz bcdr, upewnij się, że bierzesz pod uwagę nie tylko aplikacje logiki, ale także te zasoby platformy Azure, których używasz z aplikacjami logiki:

* [Połączenia](../connectors/apis-list.md) utworzone z aplikacji logiki do innych aplikacji, usług i systemów. Aby uzyskać więcej informacji, zobacz [Połączenia z zasobami](#resource-connections) w dalszej części tego tematu.

* [Lokalne bramy danych,](../logic-apps/logic-apps-gateway-connection.md) które są zasobami platformy Azure, które są tworzonymi i używanymi w aplikacjach logiki w celu uzyskania dostępu do danych w systemach lokalnych. Każdy zasób bramy reprezentuje oddzielną [instalację bramy danych](../logic-apps/logic-apps-gateway-install.md) na komputerze lokalnym. Aby uzyskać więcej informacji, zobacz [lokalne bramy danych](#on-premises-data-gateways) w dalszej części tego tematu.

* [Konta integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) w których można zdefiniować i przechowywać artefakty używane przez aplikacje logiki w scenariuszach integracji przedsiębiorstwa między przedsiębiorstwami.Integration accounts where you define and store the artifacts that logic apps use for [business-to-business (B2B) enterprise integration](../logic-apps/logic-apps-enterprise-integration-overview.md) scenarios. Na przykład można [skonfigurować odzyskiwanie po awarii między regionami dla kont integracji](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Środowiska usług integracji (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) w których tworzysz aplikacje logiki, które są uruchamiane w wystąpieniu środowiska uruchomieniowego izolowanych aplikacji logiki w sieci wirtualnej platformy Azure. Te aplikacje logiki mogą następnie uzyskać dostęp do zasobów, które są chronione za zaporą w tej sieci wirtualnej.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Lokalizacje podstawowe i dodatkowe

Każda aplikacja logiki musi określić lokalizację, która ma być używana do wdrożenia. Ta lokalizacja jest regionem publicznym w globalnej platformie Azure z wieloma dzierżawcami, na przykład "Zachodnie stany USA", lub środowiskiem usługi integracji (ISE), które zostało wcześniej utworzone i wdrożone w sieci wirtualnej platformy Azure. Uruchamianie aplikacji logiki w usłudze ISE jest podobne do uruchamiania aplikacji logiki w globalnym regionie platformy Azure, co oznacza, że strategia odzyskiwania po awarii może mieć zastosowanie do obu scenariuszy. Jednak ises mają inne zagadnienia, takie jak konfigurowanie dostępu do zasobów, które są dostępne tylko dla ises.

> [!NOTE]
> Jeśli aplikacja logiki działa również z artefaktami B2B, takimi jak partnerzy handlowi, umowy, schematy, mapy i certyfikaty, które są przechowywane na koncie integracji, zarówno konto integracji, jak i aplikacje logiki muszą określać tę samą lokalizację.

Ta strategia odzyskiwania po awarii koncentruje się na konfigurowaniu aplikacji logiki podstawowej do [*pracy awaryjnej*](https://en.wikipedia.org/wiki/Failover) w aplikacji logiki wstrzymania lub tworzenia kopii zapasowych w alternatywnej lokalizacji, w której są również dostępne usługi Azure Logic Apps. W ten sposób, jeśli podstawowy ponosi straty, zakłócenia lub awarie, wtórne może podjąć pracę. Ta strategia wymaga, że aplikacja logiki pomocniczej i zasobów zależnych są już wdrożone i gotowe w lokalizacji alternatywnej.

Jeśli zastosujesz się do dobrych praktyk DevOps, już używasz [szablonów usługi Azure Resource Manager](../azure-resource-manager/management/overview.md) do definiowania i wdrażania aplikacji logiki i ich zasobów zależnych. Szablony Menedżera zasobów umożliwiają użycie definicji pojedynczego wdrożenia, a następnie użycie plików parametrów w celu zapewnienia wartości konfiguracji używanych dla każdego miejsca docelowego wdrożenia. Ta funkcja oznacza, że można wdrożyć tę samą aplikację logiki w różnych środowiskach, na przykład rozwoju, testowania i produkcji. Tę samą aplikację logiki można również wdrożyć w różnych regionach platformy Azure lub w regionach internetowych, które obsługują strategie odzyskiwania po awarii, które używają [sparowanych regionów.](../best-practices-availability-paired-regions.md)

W przypadku strategii pracy awaryjnej aplikacje i lokalizacje logiki muszą spełniać następujące wymagania:

* Pomocnicze wystąpienie aplikacji logiki ma dostęp do tych samych aplikacji, usług i systemów, co wystąpienie aplikacji logiki podstawowej.

* Oba wystąpienia aplikacji logiki mają ten sam typ hosta. Tak więc oba wystąpienia są wdrażane w regionach na globalnej platformie Azure z wieloma dzierżawcami lub oba wystąpienia są wdrażane w środowiskach ISE, które umożliwiają aplikacjom logiki bezpośredni dostęp do zasobów w sieci wirtualnej platformy Azure. Aby uzyskać najlepsze rozwiązania i więcej informacji na temat sparowanych regionów dla BCDR, zobacz [Ciągłość działania i odzyskiwanie po awarii (BCDR): sparowane regiony platformy Azure](../best-practices-availability-paired-regions.md).

  Na przykład zarówno lokalizacje podstawowe, jak i pomocnicze muszą być środowiskami ise, gdy podstawowa aplikacja logiki jest uruchamiana w środowisku ISE i używa [łączników w wersji ISE](../connectors/apis-list.md#ise-connectors), akcji HTTP do wywoływania zasobów w sieci wirtualnej platformy Azure lub obu. W tym scenariuszu aplikacja logiki pomocniczej musi mieć podobną konfigurację w lokalizacji pomocniczej jako podstawowej aplikacji logiki.

  > [!NOTE]
  > W przypadku bardziej zaawansowanych scenariuszy można mieszać zarówno platformę Azure z wieloma dzierżawcami, jak i platformą ISE jako lokalizacje. Upewnij się jednak, że bierzesz pod uwagę i rozumiesz [różnice między uruchamianiem aplikacji logiki w usłudze ISE a platformą Azure z wieloma dzierżawami.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Jeśli używasz ises, [upewnij się, że są one skalowane w poziomie lub mają wystarczającą pojemność](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) do obsługi obciążenia.

#### <a name="example-multi-tenant-azure"></a>Przykład: Platforma Azure z wieloma dzierżawami

W tym przykładzie pokazano wystąpienia aplikacji logiki podstawowej i pomocniczej, które są wdrażane w oddzielnych regionach w globalnej platformy Azure z wieloma dzierżawami w tym scenariuszu. Pojedynczy [szablon Menedżera zasobów](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definiuje zarówno wystąpienia aplikacji logiki, jak i zasoby zależne wymagane przez te aplikacje logiki. Oddzielne pliki parametrów określają wartości konfiguracji, które mają być używane dla każdej lokalizacji wdrożenia:

![Wystąpienia aplikacji logiki podstawowej i pomocniczej w oddzielnych lokalizacjach](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Przykład: Środowisko usługi integracji

W tym przykładzie przedstawiono poprzednie wystąpienia aplikacji logiki podstawowej i pomocniczej, ale wdrożone w oddzielnych programach ISE. Pojedynczy szablon Menedżera zasobów definiuje zarówno wystąpienia aplikacji logiki, zasoby zależne wymagane przez te aplikacje logiki, jak i programy ISE jako lokalizacje wdrażania. Oddzielne pliki parametrów definiują wartości konfiguracji używane do wdrożenia w każdej lokalizacji:

![Aplikacje logiki podstawowej i pomocniczej w różnych lokalizacjach](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Połączenia z zasobami

Usługa Azure Logic Apps udostępnia [wbudowane wyzwalacze i akcje oraz setki zarządzanych łączników,](../connectors/apis-list.md) których aplikacja logiki może używać do pracy z innymi aplikacjami, usługami, systemami i innymi zasobami, takimi jak konta usługi Azure Storage, bazy danych programu SQL Server, konta e-mail usługi Office 365 Outlook itd. Jeśli aplikacja logiki potrzebuje dostępu do tych zasobów, należy utworzyć połączenia, które uwierzytelniają dostęp do tych zasobów. Każde połączenie jest oddzielnym zasobem platformy Azure, który istnieje w określonej lokalizacji i nie może być używany przez zasoby w innych lokalizacjach.

W przypadku strategii odzyskiwania po awarii należy wziąć pod uwagę lokalizacje, w których istnieją zasoby zależne w stosunku do wystąpień aplikacji logiki:

* Wystąpienie podstawowe i zasoby zależne istnieją w różnych lokalizacjach. W takim przypadku wystąpienie pomocnicze może połączyć się z tymi samymi zasobami zależnymi lub punktami końcowymi. Jednak należy utworzyć połączenia specjalnie dla wystąpienia pomocniczego. W ten sposób, jeśli lokalizacja podstawowa stanie się niedostępna, nie ma to wpływu na połączenia pomocnicze.

  Załóżmy na przykład, że aplikacja logiki podstawowej łączy się z usługą zewnętrzną, taką jak Salesforce. Zazwyczaj dostępność i lokalizacja usługi zewnętrznej są niezależne od dostępności aplikacji logiki. W takim przypadku wystąpienie pomocnicze może połączyć się z tą samą usługą, ale powinno mieć własne połączenie.

* Zarówno wystąpienie podstawowe, jak i zasoby zależne istnieją w tej samej lokalizacji. W takim przypadku zasoby zależne powinny mieć kopie zapasowe lub replikowane wersje w innej lokalizacji, aby wystąpienie pomocnicze nadal mogło uzyskiwać dostęp do tych zasobów.

  Załóżmy na przykład, że aplikacja logiki podstawowej łączy się z usługą, która znajduje się w tej samej lokalizacji lub regionie, na przykład usługi Azure SQL Database. Jeśli ten cały region stanie się niedostępny, usługa Azure SQL Database w tym regionie jest również prawdopodobnie niedostępna. W takim przypadku należy pomocnicze wystąpienie do korzystania z replikowanej lub kopii zapasowej bazy danych wraz z oddzielnym połączeniem z tej bazy danych.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Lokalne bramy danych

Jeśli aplikacja logiki jest uruchamiana na platformie Azure z wieloma dzierżawcami i potrzebuje dostępu do zasobów lokalnych, takich jak bazy danych programu SQL Server, należy zainstalować [lokalną bramę danych na](../logic-apps/logic-apps-gateway-install.md) komputerze lokalnym. Następnie można utworzyć zasób bramy danych w witrynie Azure portal, dzięki czemu aplikacja logiki może używać bramy podczas tworzenia połączenia z zasobem.

Zasób bramy danych jest skojarzony z lokalizacją lub regionem platformy Azure, podobnie jak zasób aplikacji logiki. W strategii odzyskiwania po awarii upewnij się, że brama danych pozostaje dostępna dla aplikacji logiki do użycia. Wysoka dostępność bramy można [włączyć,](../logic-apps/logic-apps-gateway-install.md#high-availability) gdy masz wiele instalacji bramy.

> [!NOTE]
> Jeśli aplikacja logiki działa w środowisku usługi integracji (ISE) i używa tylko łączników o wersji ISE dla lokalnych źródeł danych, nie jest potrzebna brama danych, ponieważ łączniki ISE zapewniają bezpośredni dostęp do zasobu lokalnego.
>
> Jeśli łącznik w wersji ISE nie jest dostępny dla żądanego zasobu lokalnego, aplikacja logiki nadal może utworzyć połączenie przy użyciu łącznika innego niż ISE, który działa na globalnej platformie Azure z wieloma dzierżawcami, a nie w środowisku ISE. Jednak to połączenie wymaga lokalnej bramy danych.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Role aktywne-aktywne i aktywne-pasywne

Lokalizacje podstawowe i pomocnicze można skonfigurować, aby wystąpienia aplikacji logiki w tych lokalizacjach mogły odgrywać następujące role:

| Rola pierwotno-pomocnicza | Opis |
|------------------------|-------------|
| *Aktywny aktywny* | Podstawowe i pomocnicze wystąpienia aplikacji logiki w obu lokalizacjach aktywnie obsługują żądania, wykonując jeden z tych wzorców: <p><p>- *Równoważenie obciążenia:* W razie potrzeby oba wystąpienia mogą nasłuchiwać ruchu punktu końcowego i równoważenia obciążenia do każdego wystąpienia. <p>- *Konkurujący konsumenci:* Oba wystąpienia mogą działać jako konkurujący konsumenci, dzięki czemu wystąpienia konkurują o wiadomości z kolejki. Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienie przejmuje obciążenie. |
| *Aktywny-pasywny* | Podstawowe wystąpienie aplikacji logiki aktywnie obsługuje całe obciążenie, podczas gdy wystąpienie pomocnicze jest pasywne (wyłączone lub nieaktywne). Pomocniczy czeka na sygnał, że podstawowy jest niedostępny lub nie działa z powodu zakłóceń lub awarii i przejmuje obciążenie jako aktywne wystąpienie. |
| Połączenie | Niektóre aplikacje logiki odgrywają rolę aktywny-aktywny, podczas gdy inne aplikacje logiki odgrywają rolę aktywny-pasywny. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Aktywne i aktywne przykłady

W tych przykładach pokazano konfigurację aktywny-aktywny, w której oba wystąpienia aplikacji logiki aktywnie obsługują żądania lub wiadomości. Niektóre inne system lub usługa dystrybuuje żądania lub komunikaty między wystąpieniami, na przykład jedną z następujących opcji:

* "Fizyczny" moduł równoważenia obciążenia, taki jak sprzęt, który kieruje ruch

* "Miękki" moduł równoważenia obciążenia, taki jak [Azure Load Balancer](../load-balancer/load-balancer-overview.md) lub [Azure API Management.](../api-management/api-management-key-concepts.md) Za pomocą usługi API Management można określić zasady określające sposób równoważenia obciążenia ruchu przychodzącego. Można też użyć usługi obsługującej śledzenie stanu, na przykład [usługi Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

  Chociaż w tym przykładzie przede wszystkim pokazano moduł równoważenia obciążenia platformy Azure, można użyć opcji, która najlepiej odpowiada potrzebom twojego scenariusza:

  ![Konfiguracja "Aktywny aktywny", która korzysta z modułu równoważenia obciążenia lub usługi stanowej](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Każde wystąpienie aplikacji logiki działa jako konsument i mają oba wystąpienia konkurować o wiadomości z kolejki:

  ![Konfiguracja "Active-Active", która wykorzystuje "konkurujących konsumentów"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Przykłady aktywnej i pasywnej

W tym przykładzie pokazano konfigurację aktywnych pasywnych, gdzie podstawowe wystąpienie aplikacji logiki jest aktywne w jednej lokalizacji, podczas gdy wystąpienie pomocnicze pozostaje nieaktywne w innej lokalizacji. Jeśli podstawowe wystąpi zakłócenie lub błąd, można mieć operator uruchomić skrypt, który aktywuje pomocniczy do podjęcia obciążenia.

![Konfiguracja "Aktywny-pasywny", która wykorzystuje "konkurujących konsumentów"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Połączenie z aktywnym i aktywnym pasywnym

W tym przykładzie pokazano połączonej konfiguracji, gdzie lokalizacja podstawowa ma zarówno aktywnych wystąpień aplikacji logiki, podczas gdy lokalizacja pomocnicza ma aktywnych pasywnych wystąpień aplikacji logiki. Jeśli lokalizacja podstawowa wystąpi zakłócenie lub awaria, aktywna aplikacja logiki w lokalizacji pomocniczej, która już obsługuje częściowe obciążenie, może przejąć całe obciążenie.

* W lokalizacji podstawowej aktywna aplikacja logiki nasłuchuje kolejki usługi Azure Service Bus dla wiadomości, podczas gdy inna aktywna aplikacja logiki sprawdza wiadomości e-mail przy użyciu wyzwalacza sondowania programu Office 365 Outlook.

* W lokalizacji dodatkowej aplikacja aktywnej logiki współpracuje z aplikacją logiki w lokalizacji podstawowej, nasłuchując i konkurując o wiadomości z tej samej kolejki usługi Service Bus. Tymczasem pasywna nieaktywna aplikacja logiki czeka w trybie gotowości, aby sprawdzić, czy wiadomości e-mail, gdy lokalizacja podstawowa staje się niedostępna, ale jest *wyłączona,* aby uniknąć ponownego odczytania wiadomości e-mail.

!["Active-passive" i "active-passive" kombinacja, która wykorzystuje wyzwalacze cyklu](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stan i historia aplikacji logiki

Gdy aplikacja logiki jest wyzwalany i uruchamia się, stan aplikacji jest przechowywany w tej samej lokalizacji, w której aplikacja została uruchomiona i jest niezbywalny do innej lokalizacji. Jeśli wystąpi błąd lub zakłócenie, wszelkie wystąpienia przepływu pracy w toku są porzucane. Po skonfigurowaniu lokalizacji podstawowej i pomocniczej nowe wystąpienia przepływu pracy zaczynają działać w lokalizacji dodatkowej.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Zmniejszanie porzuconych wystąpień w toku

Aby zminimalizować liczbę porzuconych wystąpień przepływu pracy w toku, można wybrać spośród różnych wzorców wiadomości, które można zaimplementować, na przykład:

* [Poprawiono szyku poślizgu routingu](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Ten wzorzec komunikatów przedsiębiorstwa, który dzieli proces biznesowy na mniejsze etapy. Dla każdego etapu skonfigurować aplikację logiki, która obsługuje obciążenia dla tego etapu. Aby komunikować się ze sobą, aplikacje logiki używają asynchronii protokołu obsługi wiadomości, takiego jak kolejki usługi Azure Service Bus lub tematy. Po podzieleniu procesu na mniejsze etapy, można zmniejszyć liczbę procesów biznesowych, które mogą utknąć na wystąpienie aplikacji logiki nie powiodło się. Aby uzyskać bardziej ogólne informacje na temat tego wzorca, zobacz [Wzorce integracji przedsiębiorstwa — posuwanie marszruty](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  W tym przykładzie pokazano wzorzec poślizgu routingu, gdzie każda aplikacja logiki reprezentuje etap i używa kolejki usługi Service Bus do komunikowania się z następnej aplikacji logiki w procesie.

  ![Dzielenie procesu biznesowego na etapy reprezentowane przez aplikacje logiki, które komunikują się ze sobą przy użyciu kolejek usługi Azure Service Bus](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Jeśli zarówno podstawowe, jak i pomocnicze wystąpienia aplikacji logiki wykonaj ten sam wzorzec poślizgu routingu w swoich lokalizacjach, można zaimplementować [wzorzec konkurujących konsumentów, konfigurując](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) [aktywne aktywne role](#roles) dla tych wystąpień.

* [Wzorzec menedżera procesów (brokera)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock bez wzorca limitu czasu](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Dostęp do historii wyzwalania i uruchamiania

Aby uzyskać więcej informacji na temat poprzednich wykonań przepływu pracy aplikacji logiki, możesz przejrzeć wyzwalacz aplikacji i historię uruchamiania. Historia wykonywania historii aplikacji logiki jest przechowywana w tej samej lokalizacji lub regionie, w którym uruchomiono tę aplikację logiki, co oznacza, że nie można migrować tej historii do innej lokalizacji. Jeśli wystąpienie podstawowe działa w sposób fail over do wystąpienia pomocniczego, można uzyskać dostęp tylko do wyzwalacza każdego wystąpienia i uruchamia historię w odpowiednich lokalizacjach, w których te wystąpienia zostały uruchomione. Można jednak uzyskać informacje o lokalizacji niezależnej od lokalizacji na temat historii aplikacji logiki, konfigurując aplikacje logiki do wysyłania zdarzeń diagnostycznych do obszaru roboczego usługi Azure Log Analytics. Następnie można przejrzeć kondycję i historię w aplikacjach logiki, które działają w wielu lokalizacjach.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Wskazówki dotyczące typu wyzwalacza

Typ wyzwalacza używany w aplikacjach logiki określa opcje konfigurowania aplikacji logiki w różnych lokalizacjach w strategii odzyskiwania po awarii. Oto dostępne typy wyzwalaczy, których można używać w aplikacjach logiki:

* [Wyzwalacz cyklu](#recurrence-trigger)
* [Wyzwalacz sondowania](#polling-trigger)
* [Wyzwalacz żądania](#request-trigger)
* [Wyzwalacz elementu webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Wyzwalacz cyklu

Wyzwalacz cyklu jest niezależny od określonej usługi lub punktu końcowego i uruchamia wyłącznie na podstawie określonego **harmonogramu** i żadnych innych kryteriów, na przykład:

* Stała częstotliwość i interwał, takie jak co 10 minut
* Bardziej zaawansowany harmonogram, taki jak ostatni poniedziałek każdego miesiąca o 17:00

Gdy aplikacja logiki rozpoczyna się od wyzwalacza cyklu, należy skonfigurować podstawowe i pomocnicze wystąpienia aplikacji logiki z [rolami aktywnymi i pasywnymi.](#roles) Aby zmniejszyć *cel czasu odzyskiwania* (RTO), który odnosi się do docelowego czasu trwania przywracania procesu biznesowego po zakłóceniu lub awarii, można skonfigurować wystąpienia aplikacji logiki za pomocą kombinacji ról [aktywnych i pasywnych](#roles) i [aktywnych pasywno-aktywnych](#roles). W tej konfiguracji należy podzielić harmonogram na lokalizacje.

Załóżmy na przykład, że masz aplikację logiki, która musi być uruchamiana co 10 minut. Aplikacje logiki i lokalizacje można skonfigurować w taki sposób, aby w przypadku, gdy lokalizacja podstawowa stała się niedostępna, lokalizacja pomocnicza mogła przejąć pracę:

!["Active-passive" i "pasywno-aktywne" połączenie, które wykorzystuje wyzwalacze cyklu](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* W lokalizacji głównej skonfiguruj [role aktywnych i pasywnych](#roles) dla tych aplikacji logiki:

  * Dla *aktywnej* włączonej aplikacji logiki ustaw wyzwalacz cyklu, aby rozpocząć w górnej części godziny i powtarzać co 20 minut, na przykład 9:00 AM, 9:20 AM i tak dalej.

  * W przypadku *pasywnej* wyłączonej aplikacji logiki ustaw wyzwalacz cyklu na ten sam harmonogram, ale zacznij od 10 minut po godzinie i powtarzaj co 20 minut, na przykład 9:10, 9:30 i tak dalej.

* W lokalizacji dodatkowej skonfiguruj [pasywno-aktywne](#roles) dla tych aplikacji logiki:

  * W przypadku *pasywnej* wyłączonej aplikacji logiki ustaw wyzwalacz cyklu na ten sam harmonogram co aktywna aplikacja logiki w lokalizacji podstawowej, która znajduje się w górnej części godziny i powtarzaj co 20 minut, na przykład 9:00 AM, 9:10 AM i tak dalej.

  * Dla *aktywnej* włączonej aplikacji logiki ustaw wyzwalacz cyklu na taki sam harmonogram jak pasywna aplikacja logiki w lokalizacji podstawowej, która ma zaczynać się od 10 minut po godzinie i powtarzać co 20 minut, na przykład 9:10 AM, 9:20 AM i tak dalej.

Teraz, jeśli zdarzenie zakłócające dzieje się w lokalizacji podstawowej, należy aktywować pasywną aplikację logiki w lokalizacji alternatywnej. W ten sposób, jeśli znalezienie błędu wymaga czasu, ta konfiguracja ogranicza liczbę nieodebranych powtórzeń podczas tego opóźnienia.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Wyzwalacz sondowania

Aby regularnie sprawdzać, czy nowe dane do przetwarzania są dostępne z określonej usługi lub punktu końcowego, aplikacja logiki można użyć wyzwalacza *sondowania,* który wielokrotnie wywołuje usługi lub punktu końcowego na podstawie stałego harmonogramu cyklu. Dane, które zapewnia usługa lub punkt końcowy może mieć jeden z tych typów:

* Dane statyczne, które opisują dane, które są zawsze dostępne do odczytu
* Dane nietrwałe, które opisują dane, które nie są już dostępne po przeczytaniu

Aby uniknąć wielokrotnego odczytywania tych samych danych, aplikacja logiki musi pamiętać, które dane zostały wcześniej odczytane przez zachowanie stanu po stronie klienta lub po stronie serwera, usługi lub systemu.

* Aplikacje logiki, które działają ze stanem po stronie klienta używać wyzwalaczy, które mogą utrzymać stan.

  Na przykład wyzwalacz, który odczytuje nową wiadomość ze skrzynki odbiorczej poczty e-mail wymaga, aby wyzwalacz mógł zapamiętać ostatnio przeczytaną wiadomość. W ten sposób wyzwalacz uruchamia aplikację logiki tylko wtedy, gdy nadejdzie następna nieprzeczytana wiadomość.

* Aplikacje logiki, które działają z serwerem, usługą lub stanem po stronie systemu, używają wartości właściwości lub ustawień, które znajdują się po stronie serwera, usługi lub systemu.

  Na przykład wyzwalacz oparty na kwerendzie, który odczytuje wiersz `isRead` z bazy danych, wymaga, aby wiersz miał kolumnę ustawioną na `FALSE`. Za każdym razem, gdy wyzwalacz odczytuje wiersz, `isRead` aplikacja `FALSE` logiki aktualizuje ten wiersz, zmieniając kolumnę z . `TRUE`

  To podejście po stronie serwera działa podobnie w przypadku kolejek usługi Service Bus lub tematów, które mają semantyka kolejkowania, gdzie wyzwalacz może odczytywać i blokować wiadomość, podczas gdy aplikacja logiki przetwarza komunikat. Po zakończeniu przetwarzania aplikacji logiki wyzwalacz usuwa wiadomość z kolejki lub tematu.

Z punktu widzenia odzyskiwania po awarii podczas konfigurowania aplikacji logiki wystąpienia podstawowe i pomocnicze, upewnij się, że konto dla tych zachowań na podstawie tego, czy aplikacja logiki śledzi stan po stronie klienta lub po stronie serwera:

* W przypadku aplikacji logiki, która działa ze stanem po stronie klienta, upewnij się, że aplikacja logiki nie odczytuje tej samej wiadomości więcej niż jeden raz. Tylko jedna lokalizacja może mieć aktywne wystąpienie aplikacji logiki w dowolnym określonym czasie. Upewnij się, że wystąpienie aplikacji logiki w lokalizacji alternatywnej jest nieaktywne lub wyłączone, dopóki wystąpienie podstawowe nie przejdzie w stan failed do lokalizacji alternatywnej.

  Na przykład wyzwalacz programu Outlook usługi Office 365 zachowuje stan po stronie klienta i śledzi sygnaturę czasową ostatnio przeczytanych wiadomości e-mail, aby uniknąć odczytywania duplikatu.

* Dla aplikacji logiki, która współpracuje ze stanem po stronie serwera, można skonfigurować wystąpienia aplikacji logiki do odtwarzania [aktywnych aktywnych ról,](#roles) gdzie działają jako konkurencyjnych konsumentów lub [aktywnych pasywnych ról,](#roles) gdzie alternatywne wystąpienie czeka, aż wystąpienie podstawowe przejdzie w tryb fail on do lokalizacji alternatywnej.

  Na przykład odczytywanie z kolejki komunikatów, takich jak kolejka usługi Azure Service Bus, używa stanu po stronie serwera, ponieważ usługa kolejkowania przechowuje blokady wiadomości, aby uniemożliwić innym klientom odczytywanie tych samych wiadomości.

  > [!NOTE]
  > Jeśli aplikacja logiki musi odczytywać wiadomości w określonej kolejności, na przykład z kolejki usługi Service Bus, można użyć konkurencyjnego wzorca konsumenta, ale tylko w połączeniu z sesjami usługi Service Bus, który jest również znany jako [wzorzec *konwoju sekwencyjnego* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy). W przeciwnym razie należy skonfigurować wystąpienia aplikacji logiki z ról aktywnych pasywnych.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Wyzwalacz żądania

Wyzwalacz **żądania** sprawia, że aplikacja logiki wywoływane z innych aplikacji, usług i systemów i jest zwykle używany do zapewnienia tych funkcji:

* Bezpośredni interfejs API REST dla aplikacji logiki, który mogą wywoływać inne osoby

  Na przykład użyj wyzwalacza żądania, aby uruchomić aplikację logiki, aby inne aplikacje logiki mogły wywołać wyzwalacz przy użyciu akcji **Przepływ pracy wywołanie — aplikacje logiki.**

* Element [webhook](#webhook-trigger) lub mechanizm wywołania zwrotnego dla aplikacji logiki

* Sposób ręcznego uruchamiania operacji użytkownika lub procedur wywoływania aplikacji logiki, na przykład przy użyciu skryptu programu PowerShell, który wykonuje określone zadanie

Z punktu widzenia odzyskiwania po awarii wyzwalacz żądania jest odbiornikiem pasywnym, ponieważ aplikacja logiki nie wykonuje żadnej pracy i czeka, aż inna usługa lub system jawnie wywoła wyzwalacz. Jako pasywny punkt końcowy można skonfigurować wystąpienia podstawowe i pomocnicze w następujący sposób:

* [Aktywny aktywny:](#roles)Oba wystąpienia aktywnie obsługują żądania lub wywołania. Wywołujący lub router równoważy lub dystrybuuje ruch między tymi wystąpieniami.

* [Aktywny pasywny:](#roles)Tylko wystąpienie podstawowe jest aktywne i obsługuje całą pracę, podczas gdy wystąpienie pomocnicze czeka, aż podstawowe wystąpi zakłócenie lub błąd. Wywołujący lub router określa, kiedy wywołać wystąpienie pomocnicze.

Jako zalecana architektura można użyć usługi Azure API Management jako serwera proxy dla aplikacji logiki, które używają wyzwalaczy żądania. Usługa API Management zapewnia [wbudowaną odporność międzyregionalnej oraz możliwość kierowania ruchu przez wiele punktów końcowych.](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Wyzwalacz elementu webhook

Wyzwalacz *elementu webhook* zapewnia możliwość aplikacji logiki do subskrybowania usługi, przekazując *adres URL wywołania zwrotnego* do tej usługi. Aplikacja logiki można następnie nasłuchiwać i czekać na określone zdarzenie, które ma miejsce w tym punkcie końcowym usługi. Gdy zdarzenie się dzieje, usługa wywołuje wyzwalacz elementu webhook przy użyciu adresu URL wywołania zwrotnego, który następnie uruchamia aplikację logiki. Po włączeniu wyzwalacz elementu webhook subskrybuje usługę. Po wyłączeniu wyzwalacz wypisuje się z usługi.

Z punktu widzenia odzyskiwania po awarii skonfiguruj wystąpienia podstawowe i pomocnicze, które używają wyzwalaczy elementu webhook do odtwarzania ról aktywnych i pasywnych, ponieważ tylko jedno wystąpienie powinno odbierać zdarzenia lub wiadomości z subskrybowanego punktu końcowego.

## <a name="assess-primary-instance-health"></a>Ocena kondycji wystąpienia podstawowego

Aby strategia odzyskiwania po awarii działała, rozwiązanie wymaga sposobów wykonywania następujących zadań:

* [Sprawdzanie dostępności wystąpienia podstawowego](#check-primary-availability)
* [Monitorowanie kondycji wystąpienia podstawowego](#monitor-primary-health)
* [Aktywowanie wystąpienia pomocniczego](#activate-secondary)

W tej sekcji opisano jedno rozwiązanie, którego można użyć wprost lub jako podstawę dla własnego projektu. Oto omówienie wizualne wysokiego poziomu dla tego rozwiązania:

![Tworzenie aplikacji logiki watchdog, która monitoruje aplikację logiki sprawdzania kondycji w lokalizacji podstawowej](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Sprawdzanie dostępności wystąpienia podstawowego

Aby ustalić, czy wystąpienie podstawowe jest dostępne, uruchomione i zdolne do pracy, można utworzyć aplikację logiki "health-check", która znajduje się w tej samej lokalizacji co wystąpienie podstawowe. Następnie można wywołać tę aplikację sprawdzania kondycji z alternatywnej lokalizacji. Jeśli aplikacja sprawdzania kondycji pomyślnie odpowiada, podstawowa infrastruktura usługi Azure Logic Apps w tym regionie jest dostępna i działa. Jeśli aplikacja sprawdzania kondycji nie odpowiada, można założyć, że lokalizacja nie jest już w dobrej kondycji.

W przypadku tego zadania należy utworzyć podstawową aplikację logiki sprawdzania kondycji, która wykonuje następujące zadania:

1. Odbiera wywołanie z aplikacji watchdog przy użyciu wyzwalacza żądania.

1. Odpowiedz ze stanem wskazującym, czy aplikacja logiki sprawdzone nadal działa przy użyciu odpowiedzi akcji.

   > [!IMPORTANT]
   > Aplikacja logiki sprawdzania kondycji musi używać response akcji, tak aby aplikacja odpowiada synchronicznie, a nie asynchronicznie.

1. Opcjonalnie, aby dodatkowo określić, czy lokalizacja podstawowa jest w dobrej kondycji, można uwzględnić kondycji innych usług, które współdziałają z aplikacją logiki docelowej w tej lokalizacji. Wystarczy rozwinąć aplikację logiki sprawdzania kondycji, aby ocenić kondycję dla tych innych usług.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Tworzenie aplikacji logiki watchdog

Aby monitorować kondycję wystąpienia podstawowego i wywołać aplikację logiki sprawdzania kondycji, utwórz aplikację logiki "watchdog" w *alternatywnej lokalizacji*. Na przykład można skonfigurować aplikację logiki watchdog tak, aby w przypadku wywołania logiki sprawdzania kondycji nie powiedzie się, watchdog może wysłać alert do zespołu operacyjnego, aby mogli zbadać błąd i dlaczego wystąpienie podstawowe nie odpowiada.

> [!IMPORTANT]
> Upewnij się, że aplikacja logiki watchdog znajduje się w *lokalizacji, która różni się od lokalizacji podstawowej*. Jeśli usługa Aplikacje logiki w lokalizacji podstawowej wystąpią problemy, aplikacja logiki watchdog może nie działać.

W przypadku tego zadania w lokalizacji pomocniczej utwórz aplikację logiki watchdog, która wykonuje następujące zadania:

1. Uruchom na podstawie stałego lub zaplanowanego cyklu przy użyciu wyzwalacza cyklu.

   Można ustawić cykl na wartość poniżej poziomu tolerancji dla celu czasu odzyskiwania (RTO).

1. Wywołanie aplikacji logiki sprawdzania kondycji w lokalizacji podstawowej przy użyciu akcji HTTP, na przykład:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktywowanie wystąpienia pomocniczego

Aby automatycznie aktywować wystąpienie pomocnicze, można utworzyć aplikację logiki, która wywołuje interfejs API zarządzania, taki jak [łącznik usługi Azure Resource Manager,](https://docs.microsoft.com/connectors/arm/) aby aktywować odpowiednie aplikacje logiki w lokalizacji pomocniczej. Możesz rozwinąć aplikację watchdog, aby wywołać tę aplikację logiki aktywacji po wystąpieniu określonej liczby błędów.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Zbieranie danych diagnostycznych

Można skonfigurować rejestrowanie dla aplikacji logiki uruchamia i wysłać wynikowe dane diagnostyczne do usług, takich jak Usługa Azure Storage, Usługi Azure Event Hubs i usługi Azure Log Analytics do dalszej obsługi i przetwarzania.

* Jeśli chcesz użyć tych danych z usługi Azure Log Analytics, możesz udostępnić dane dla lokalizacji podstawowych i pomocniczych, konfigurując **ustawienia diagnostyczne** aplikacji logiki i wysyłając dane do wielu obszarów roboczych usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla usługi Azure Logic Apps.](../logic-apps/monitor-logic-apps-log-analytics.md)

* Jeśli chcesz wysłać dane do usługi Azure Storage lub usługi Azure Event Hubs, możesz udostępnić dane zarówno dla lokalizacji podstawowych, jak i pomocniczych, konfigurując nadmiarowość geograficzną. Więcej informacji można znaleźć w tych artykułach:<p>

  * [Odzyskiwanie po awarii usługi Azure Blob Storage i przeczesywu konta w stanie failover](../storage/common/storage-disaster-recovery-guidance.md)
  * [Usługa Azure Event Hubs geo-disaster recovery](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Następne kroki

* [Omówienie odporności platformy Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Lista kontrolna dotycząca odporności dla określonych usług platformy Azure](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Zarządzanie danymi w zakresie odporności na platformie Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Tworzenie kopii zapasowych i odzyskiwanie po awarii dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Odzyskiwanie po obejmujących cały region zakłóceniach usługi](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Umowy dotyczące poziomu usług firmy Microsoft (SLA) dla usług platformy Azure](https://azure.microsoft.com/support/legal/sla/)
