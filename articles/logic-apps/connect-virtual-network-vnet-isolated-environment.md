---
title: Łączenie się z sieciami wirtualnymi platformy Azure za pomocą środowiska ISE
description: Tworzenie środowiska usługi integracji (ISE), które może uzyskiwać dostęp do sieci wirtualnych platformy Azure (VNETs) z usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 6683c1b78b0e7ecba162026708c83843e2c08180
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478889"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Łączenie się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps przy użyciu środowiska usługi integracji (ISE)

W scenariuszach, w których aplikacje logiki i konta integracji potrzebują dostępu do [sieci wirtualnej platformy Azure,](../virtual-network/virtual-networks-overview.md)utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Środowisko ISE jest izolowanym środowiskiem, które używa dedykowanego magazynu i innych zasobów, które są oddzielone od "globalnej" usługi aplikacji logiki z wieloma dzierżawami. Ta separacja zmniejsza również wpływ innych dzierżaw platformy Azure na wydajność aplikacji. Ise zapewnia również własne statyczne adresy IP. Te adresy IP są oddzielone od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, usługi wielu dzierżawców.

Podczas tworzenia środowiska ISE platforma Azure *wstrzykuje* środowisko ISE do sieci wirtualnej platformy Azure, która następnie wdraża usługę Aplikacje logiki w sieci wirtualnej. Podczas tworzenia aplikacji logiki lub konta integracji wybierz ise jako ich lokalizacji. Aplikacja logiki lub konto integracji można następnie bezpośrednio uzyskać dostęp do zasobów, takich jak maszyny wirtualne (maszyny wirtualne), serwery, systemy i usługi, w sieci wirtualnej.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> W przypadku aplikacji logiki i kont integracji do współpracy w ise, oba muszą używać *tego samego ise* jako ich lokalizacji.

Interfejs ISE zwiększył limity czasu trwania uruchomienia, przechowywania magazynu, przepływności, limitów czasu żądania i odpowiedzi HTTP, rozmiarów wiadomości i niestandardowych żądań łączników. Aby uzyskać więcej informacji, zobacz [Limity i konfiguracja usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Aby dowiedzieć się więcej o środowiskach ISE, zobacz [Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

W tym artykule pokazano, jak wykonać te zadania przy użyciu witryny Azure portal:

* Włącz dostęp do swojego ISE.
* Stwórz ise.
* Dodaj dodatkową pojemność do ise.

Można również utworzyć środowisku ISE przy użyciu [przykładowego szablonu szybkiego startu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) lub przy użyciu interfejsu API REST aplikacji logiki, w tym konfigurowania kluczy zarządzanych przez klienta:

* [Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API REST aplikacji logiki](../logic-apps/create-integration-service-environment-rest-api.md)
* [Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania danych w stanie spoczynku dla ise](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki uruchamiane w interfejsie ISE używają planu cenowego innego niż plan cenowy oparty na zużyciu. Aby dowiedzieć się, jak działają ceny i rozliczenia dla firm ISE, zobacz [model cenowy Aplikacji logiki](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](../logic-apps/logic-apps-pricing.md).

* [Sieć wirtualna platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli nie masz sieci wirtualnej, dowiedz się, jak [utworzyć sieć wirtualną platformy Azure.](../virtual-network/quick-create-portal.md)

  * Sieć wirtualna musi mieć cztery *puste* podsieci do tworzenia i wdrażania zasobów w środowisku ISE. Każda podsieć obsługuje inny składnik aplikacji logiki, który jest używany w użyno. Można utworzyć te podsieci z wyprzedzeniem lub poczekać, aż utworzysz ise, gdzie można tworzyć podsieci w tym samym czasie. Dowiedz się więcej o [wymaganiach dotyczących podsieci](#create-subnet).

  * Nazwy podsieci muszą zaczynać się od znaku alfabetycznego lub podkreślenia `&` `\\`i `?` `/`nie mogą używać następujących znaków: `<`, `>`, `%`, , , , . 
  
  * Jeśli chcesz wdrożyć środowiska ISE za pomocą szablonu usługi Azure Resource Manager, najpierw upewnij się, że jeden pusty podsieci do microsoft.Logic/integrationServiceEnvironment. Nie trzeba wykonywać tej delegacji podczas wdrażania za pośrednictwem witryny Azure portal.

  * Upewnij się, że sieć [wirtualna umożliwia dostęp do środowiska ISE,](#enable-access) dzięki czemu środowisko ISE może działać poprawnie i pozostać dostępne.

  * Jeśli używasz [usługi ExpressRoute](../expressroute/expressroute-introduction.md), która zapewnia prywatne połączenie z usługami w chmurze firmy Microsoft, które jest ułatwione przez dostawcę łączności, należy [utworzyć tabelę tras,](../virtual-network/manage-route-table.md) która ma następującą trasę i połączyć tę tabelę z każdą podsiecią, która jest używana przez ise:

    **Nazwa**: <*nazwa trasy*><br>
    **Prefiks adresu:** 0.0.0.0/0<br>
    **Następny przeskok**: Internet

* Jeśli chcesz używać niestandardowych serwerów DNS dla sieci wirtualnej platformy Azure, [skonfiguruj te serwery, wykonując następujące kroki](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) przed wdrożeniem środowiska ISE w sieci wirtualnej. Aby uzyskać więcej informacji na temat zarządzania ustawieniami serwera DNS, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Jeśli zmienisz ustawienia serwera DNS lub serwera DNS, musisz ponownie uruchomić środowiska ISE, aby środowiska ISE mogły je odebrać. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie ise](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Włączanie dostępu dla środowiska ISE

Podczas korzystania z środowiska ISE z siecią wirtualną platformy Azure, typowy problem z konfiguracją jest posiadanie jednego lub więcej zablokowanych portów. Łączniki używane do tworzenia połączeń między systemami ISE i docelowymi mogą mieć również własne wymagania dotyczące portów. Na przykład w przypadku komunikowania się z systemem FTP przy użyciu łącznika FTP port używany w systemie FTP musi być dostępny, na przykład port 21 do wysyłania poleceń.

Aby upewnić się, że środowisko ISE jest dostępne i że aplikacje logiki w środowisku ISE mogą komunikować się z każdą podsiecią w sieci wirtualnej, [otwórz porty opisane w tej tabeli dla każdej podsieci](#network-ports-for-ise). Jeśli wymagane porty są niedostępne, ise nie będzie działać poprawnie.

* Jeśli masz wiele wystąpień środowiska ISE, które wymagają dostępu do innych punktów końcowych, które mają ograniczenia adresów IP, [wdrożyć zaporę platformy Azure](../firewall/overview.md) lub [wirtualne urządzenie sieciowe](../virtual-network/virtual-networks-overview.md#filter-network-traffic) w sieci wirtualnej i trasy ruchu wychodzącego za pośrednictwem tej zapory lub urządzenia wirtualnego sieci. Następnie można [skonfigurować pojedynczy, wychodzący, publiczny, statyczny i przewidywalny adres IP,](connect-virtual-network-vnet-set-up-single-ip-address.md) którego wszystkie wystąpienia środowiska ISE w sieci wirtualnej mogą używać do komunikowania się z systemami docelowymi. W ten sposób nie trzeba konfigurowania dodatkowych otworów zapory w tych systemach docelowych dla każdej ise.

   > [!NOTE]
   > Tej metody można użyć dla pojedynczego ise, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Należy wziąć pod uwagę, czy dodatkowe koszty dla zapory lub urządzenia sieci wirtualnej mają sens dla twojego scenariusza. Dowiedz się więcej o [cenach zapory platformy Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Jeśli utworzono nową sieć wirtualną platformy Azure i podsieci bez żadnych ograniczeń, nie trzeba konfigurowania [grup zabezpieczeń sieciowych](../virtual-network/security-overview.md#network-security-groups) w sieci wirtualnej do kontrolowania ruchu między podsieciami.

* W istniejącej sieci wirtualnej można *opcjonalnie* skonfigurować sieciowe sieciowe, [filtrując ruch sieciowy między podsieciami](../virtual-network/tutorial-filter-network-traffic.md). Jeśli chcesz przejść tą trasą lub jeśli już używasz sieciowych grup zabezpieczeń, upewnij się, że [otwierasz porty w tej tabeli w](#network-ports-for-ise) sieci wirtualnej, w której masz sieciowe sieciowe sieciowe lub chcesz skonfigurować sieciowe sieciowe.

  > [!NOTE]
  > Jeśli używasz [reguł zabezpieczeń sieciowej grupy](../virtual-network/security-overview.md#security-rules)zabezpieczeń, należy użyć zarówno protokołów TCP, *jak* i UDP. Reguły zabezpieczeń sieciowej sieciowej określają porty, które należy otworzyć dla adresów IP, które wymagają dostępu do tych portów. Upewnij się, że wszystkie zapory, routery lub inne elementy, które istnieją między tymi punktami końcowymi, również zachować te porty dostępne dla tych adresów IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porty sieciowe używane przez ise

W tej tabeli opisano porty w sieci wirtualnej platformy Azure, których używa środowisko ISE i gdzie te porty są używane. Aby zmniejszyć złożoność podczas tworzenia reguł zabezpieczeń, [tagi usługi](../virtual-network/service-tags-overview.md) w tabeli reprezentują grupy prefiksów adresów IP dla określonej usługi platformy Azure.

> [!IMPORTANT]
> Porty źródłowe są ulotne, więc upewnij `*` się, że ustawisz je dla wszystkich reguł. Jeśli zaznaczono, wewnętrzne ISE i zewnętrzne ISE odnoszą się do [punktu końcowego wybranego przy tworzeniu ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Aby uzyskać więcej informacji, zobacz [Dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Przeznaczenie | Kierunek | Porty docelowe | Tag usługi źródłowej | Docelowy tag usługi | Uwagi |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Komunikacja intersubnet w sieci wirtualnej | Przychodzące & wychodzące | * | Przestrzeń adresowa sieci wirtualnej, która ma podsieci ISE | Przestrzeń adresowa sieci wirtualnej, która ma podsieci ISE | Wymagane do przepływu ruchu *między* podsieciami w sieci wirtualnej. <p><p>**Ważne:** Aby ruch przepływał między *komponentami* w każdej podsieci, upewnij się, że wszystkie porty w każdej podsieci. |
| Komunikacja z aplikacją logiki | Przychodzący | 443 | Wewnętrzne ISE: <br>VirtualNetwork <p><p>Zewnętrzna ise: <br>Internet <br>(patrz **kolumna Notatki)** | VirtualNetwork | Zamiast używać tagu usługi **internetowej,** można określić źródłowy adres IP komputera lub usługi, która wywołuje wyzwalacze żądań lub elementy webhook w aplikacji logiki. <p><p>**Ważne:** Zamknięcie lub zablokowanie tego portu uniemożliwia wywołania HTTP do aplikacji logicznych, które mają wyzwalacze żądań. |
| Historia uruchamiania aplikacji logiki | Przychodzący | 443 | Wewnętrzne ISE: <br>VirtualNetwork <p><p>Zewnętrzna ise: <br>Internet <br>(patrz **kolumna Notatki)** | VirtualNetwork | Zamiast używać tagu usługi **internetowej,** można określić źródłowy adres IP komputera lub usługi, z którego ma być można wyświetlić historię uruchamiania aplikacji logiki. <p><p>**Ważne:** Chociaż zamknięcie lub zablokowanie tego portu nie uniemożliwia wyświetlania historii uruchamiania, nie można wyświetlić danych wejściowych i wyjściowych dla każdego kroku w historii przebiegu. |
| Logic Apps Designer - właściwości dynamiczne | Przychodzący | 454 | LogicAppsManagement (Logiczne aplikacje) | VirtualNetwork | Żądania pochodzą z aplikacji logiki dostępu do [przychodzących](../logic-apps/logic-apps-limits-and-config.md#inbound) adresów IP punktu końcowego dla tego regionu. |
| Wdrażanie łącznika | Przychodzący | 454 | Połączenia azureowe | VirtualNetwork | Wymagane do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje niepowodzenie wdrożeń środowiska ISE i uniemożliwia aktualizacje lub poprawki łącznika. |
| Sprawdzanie kondycji sieci | Przychodzący | 454 | Aplikacje Logiczne | VirtualNetwork | Żądania pochodzą z punktu końcowego dostępu aplikacji logiki dla [przychodzących](../logic-apps/logic-apps-limits-and-config.md#inbound) i [wychodzących](../logic-apps/logic-apps-limits-and-config.md#outbound) adresów IP dla tego regionu. |
| Zależność zarządzania usługą app service | Przychodzący | 454, 455 | AppServiceManagement (Usługi w aplikacjach) | VirtualNetwork | |
| Komunikacja z usługi Azure Traffic Manager | Przychodzący | Wewnętrzne ISE: 454 <p><p>Zewnętrzna ise: 443 | AzureTrafficManager | VirtualNetwork | |
| Zarządzanie interfejsami API — punkt końcowy zarządzania | Przychodzący | 3443 | APIManagement (ApiManagement) | VirtualNetwork | |
| Wdrażanie zasad łącznika | Przychodzący | 3443 | APIManagement (ApiManagement) | VirtualNetwork | Wymagane do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje niepowodzenie wdrożeń środowiska ISE i uniemożliwia aktualizacje lub poprawki łącznika. |
| Komunikacja z aplikacji logiki | Wychodzący | 80, 443 | VirtualNetwork | Zmienia się w zależności od miejsca docelowego | Punkty końcowe dla usługi zewnętrznej, z którymi aplikacja logiki musi komunikować się. |
| Usługa Azure Active Directory | Wychodzący | 80, 443 | VirtualNetwork | Usługa AzureActiveDirectory | |
| Zarządzanie połączeniami | Wychodzący | 443 | VirtualNetwork  | AppService | |
| Publikowanie dzienników diagnostycznych & metryki | Wychodzący | 443 | VirtualNetwork  | Usługa AzureMonitor | |
| Zależność usługi Azure Storage | Wychodzący | 80, 443, 445 | VirtualNetwork | Magazyn | |
| Zależność SQL platformy Azure | Wychodzący | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Wychodzący | 1886 | VirtualNetwork | Usługa AzureMonitor | Wymagane do publikowania stanu kondycji w kondycji zasobów |
| Zależność od zasad i agenta monitorowania usługi Log to Event Hub | Wychodzący | 5672 | VirtualNetwork | EventHub | |
| Dostęp do pamięci podręcznej platformy Azure dla wystąpień Redis między wystąpieniami ról | Przychodzący <br>Wychodzący | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Ponadto, aby program ISE działał z usługą Azure Cache for Redis, należy otworzyć te [porty wychodzące i przychodzące opisane w często zadawanych pytaniach dotyczących usługi Azure Cache for Redis.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Tworzenie własnego środowiska ISE

1. W [witrynie Azure portal](https://portal.azure.com)w głównym `integration service environments` polu wyszukiwania platformy Azure wprowadź jako filtr i wybierz pozycję **Środowiska usług integracji**.

   ![Znajdź i wybierz "Środowiska usług integracyjnych"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W okienku **Środowiska usług integracji** wybierz pozycję **Dodaj**.

   ![Znajdź i wybierz "Środowiska usług integracyjnych"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Podaj te szczegóły dla swojego środowiska, a następnie wybierz **pozycję Przejrzyj + utwórz,** na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Subskrypcja platformy Azure do użycia w twoim środowisku |
   | **Grupa zasobów** | Tak | <*Nazwa-grupa zasobów platformy Azure*> | Nowa lub istniejąca grupa zasobów platformy Azure, w której chcesz utworzyć środowisko |
   | **Nazwa środowiska usługi integracji** | Tak | <*nazwa środowiska*> | Nazwa ISE, która może zawierać tylko litery,`-`cyfry,`_`łączniki (`.`), podkreślenia ( ) i kropki ( ). |
   | **Lokalizacja** | Tak | <*Region centrum danych platformy Azure*> | Region centrum danych platformy Azure, w którym można wdrożyć środowisko |
   | **Numer jednostki magazynowej** | Tak | **Premium** lub **Deweloper (bez umowy SLA)** | Jednostka SKU ISE do tworzenia i używania. Aby uzyskać różnice między tymi jednostkami SKU, zobacz [jednostki SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Ważne:** Ta opcja jest dostępna tylko w momencie tworzenia ISE i nie można jej później zmienić. |
   | **Dodatkowa pojemność** | Premium: <br>Tak <p><p>Deweloper: <br>Nie dotyczy | Premium: <br>Od 0 do 10 <p><p>Deweloper: <br>Nie dotyczy | Liczba dodatkowych jednostek przetwarzania do użycia dla tego zasobu ISE. Aby dodać pojemność po utworzeniu, zobacz [Dodawanie pojemności ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Punkt końcowy dostępu** | Tak | **Wewnętrzna** lub **zewnętrzna** | Typ punktów końcowych dostępu do użycia w programie ISE. Te punkty końcowe określają, czy wyzwalacze żądania lub elementu webhook w aplikacjach logiki w środowisku ISE mogą odbierać połączenia spoza sieci wirtualnej. <p><p>Wybór ma również wpływ na sposób, w jaki można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych w aplikacji logiki działa historię. Aby uzyskać więcej informacji, zobacz [dostęp do punktu końcowego ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Ważne:** Ta opcja jest dostępna tylko w momencie tworzenia ISE i nie można jej później zmienić. |
   | **Sieć wirtualna** | Tak | <*Nazwa-sieć azure-wirtualna*> | Sieć wirtualna platformy Azure, w której chcesz wstrzyknąć środowisko, aby aplikacje logiki w tym środowisku mogły uzyskiwać dostęp do sieci wirtualnej. Jeśli nie masz sieci, [najpierw utwórz sieć wirtualną platformy Azure.](../virtual-network/quick-create-portal.md) <p><p>**Ważne:** Wstrzyknięcie to można wykonać *tylko* podczas tworzenia ise. |
   | **Podsieci** | Tak | <*podsieci-resource-list*> | Środowisko ISE wymaga czterech *pustych* podsieci do tworzenia i wdrażania zasobów w twoim środowisku. Aby utworzyć każdą [podsieć, wykonaj czynności opisane w tej tabeli](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Tworzenie podsieci**

   Aby utworzyć i wdrożyć zasoby w danym środowisku, środowisko ISE potrzebuje czterech *pustych* podsieci, które nie są delegowane do żadnej usługi. Każda podsieć obsługuje inny składnik aplikacji logiki, który jest używany w użyno. Nie *można* zmienić tych adresów podsieci po utworzeniu środowiska. Każda podsieć musi spełniać następujące wymagania:

   * Ma nazwę, która zaczyna się od znaku alfabetycznego lub podkreślenia (bez `<` `>`liczb) i nie używa tych znaków: `%`, , , `&`, `\\`, `?`, . `/`

   * Używa [formatu CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) i przestrzeni adresowej klasy B.

   * Używa co najmniej `/27` w przestrzeni adresowej, ponieważ każda podsieć wymaga *co najmniej*32 adresów . Przykład:

     * `10.0.0.0/28`ma tylko 16 adresów i jest za mały, ponieważ 2<sup>(32-28)</sup> to 2<sup>4</sup> lub 16.

     * `10.0.0.0/27`ma 32 adresy, ponieważ 2<sup>(32-27)</sup> to 2<sup>5</sup> lub 32.

     * `10.0.0.0/24`ma 256 adresów, ponieważ 2<sup>(32-24)</sup> to 2<sup>8</sup> lub 256. Jednak więcej adresów nie zapewnia żadnych dodatkowych korzyści.

     Aby dowiedzieć się więcej o obliczaniu adresów, zobacz [Bloki CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Jeśli używasz [usługi ExpressRoute,](../expressroute/expressroute-introduction.md)musisz [utworzyć tabelę marszruty,](../virtual-network/manage-route-table.md) która ma następującą trasę i połączyć tę tabelę z każdą podsiecią używaną przez ise:

     **Nazwa**: <*nazwa trasy*><br>
     **Prefiks adresu:** 0.0.0.0/0<br>
     **Następny przeskok**: Internet

   1. Na liście **Podsieci** wybierz pozycję **Zarządzaj konfiguracją podsieci**.

      ![Zarządzanie konfiguracją podsieci](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. W okienku **Podsieci** wybierz pozycję **Podsieć**.

      ![Dodawanie czterech pustych podsieci](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. W okienku **Dodawanie podsieci** podsieć podaj te informacje.

      * **Nazwa**: Nazwa podsieci
      * **Zakres adresów (blok CIDR)**: Zakres podsieci w sieci wirtualnej i w formacie CIDR

      ![Dodawanie szczegółów podsieci](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Gdy skończysz, wybierz opcję **OK**.

   1. Powtórz te kroki dla trzech kolejnych podsieci.

      > [!NOTE]
      > Jeśli podsieci, które próbujesz utworzyć, nie są prawidłowe, portal Azure zawiera komunikat, ale nie blokuje postępu.

   Aby uzyskać więcej informacji na temat tworzenia podsieci, zobacz [Dodawanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

1. Po pomyślnym sprawdzeniu poprawności informacji o sztucznej platformie ISE wybierz pozycję **Utwórz**, na przykład:

   ![Po pomyślnym sprawd o walidacji wybierz "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Platforma Azure rozpoczyna wdrażanie środowiska, co zwykle trwa w ciągu dwóch godzin. Od czasu do czasu wdrożenie może potrwać do czterech godzin. Aby sprawdzić stan wdrożenia, na pasku narzędzi platformy Azure wybierz ikonę powiadomień, która otwiera okienko powiadomień.

   ![Sprawdzanie stanu wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Jeśli wdrożenie zakończy się pomyślnie, platforma Azure wyświetla to powiadomienie:

   ![Wdrożenie zakończyło się pomyślnie](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   W przeciwnym razie postępuj zgodnie z instrukcjami portalu Azure do rozwiązywania problemów z wdrażaniem.

   > [!NOTE]
   > Jeśli wdrożenie zakończy się niepowodzeniem lub usuniesz ise, platforma Azure może potrwać do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być trzeba poczekać przed ponownym użyciem tych podsieci w innym ise.
   >
   > Jeśli usuniesz sieć wirtualną, platforma Azure zwykle trwa do dwóch godzin przed zwolnieniem podsieci, ale ta operacja może potrwać dłużej. 
   > Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
   > Zobacz [Usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Aby wyświetlić środowisko, wybierz **pozycję Przejdź do zasobu,** jeśli platforma Azure nie przejdzie automatycznie do środowiska po zakończeniu wdrażania.

1. Aby sprawdzić kondycję sieci dla środowiska ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Aby rozpocząć tworzenie aplikacji logiki i innych artefaktów w środowisku ISE, zobacz [Dodawanie zasobów do środowisk usług integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Zarządzane łączniki ISE, które stają się dostępne po utworzeniu ise nie są automatycznie wyświetlane w selektorze łączników w logice App Designer. Przed użyciem tych łączników ISE należy ręcznie [dodać te łączniki do ise,](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) tak aby były wyświetlane w logice App Designer.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usług integracyjnych](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Dowiedz się więcej o [wirtualnej sieci platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
