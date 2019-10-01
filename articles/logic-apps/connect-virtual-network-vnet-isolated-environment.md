---
title: Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps za pośrednictwem środowiska usługi integracji (ISE)
description: Utwórz środowisko usługi integracji (ISE), dzięki czemu Aplikacje logiki i konta integracji mogą uzyskiwać dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych), a jednocześnie mieć charakter prywatny i odizolowany od publicznego lub "globalnego" platformy Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 15e1f1c4c8757ca55ec27659a4ca11b1729aebc2
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701949"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps przy użyciu środowiska usługi integracji (ISE)

W przypadku scenariuszy, w których aplikacje logiki i konta integracji potrzebują dostępu do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), Utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE to środowisko prywatne i izolowane korzystające z dedykowanego magazynu i innych zasobów, które są oddzielone od publicznej lub "globalnej" Logic Apps usługi. Ta separacja również zmniejsza wpływ innych dzierżawców platformy Azure na wydajność aplikacji.

Po utworzeniu ISE platforma Azure wprowadza tę ISE do sieci wirtualnej platformy Azure *, która następnie* wdraża usługę Logic Apps w sieci wirtualnej. Podczas tworzenia aplikacji logiki lub konta integracji wybierz swój ISE jako lokalizację. Aplikacja logiki lub konto integracji może następnie bezpośrednio uzyskać dostęp do zasobów, takich jak maszyny wirtualne, serwery, systemy i usługi, w sieci wirtualnej.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> W przypadku aplikacji logiki i kont integracji, które współpracują ze sobą w ISE, oba muszą używać tego *samego ISE* jako lokalizacji.

ISE zwiększono limity czasu trwania przebiegu, przechowywanie magazynu, przepływność, żądania HTTP i limity czasu odpowiedzi, rozmiary komunikatów i żądania łączników niestandardowych. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md). Aby dowiedzieć się więcej na temat ISEs, zobacz [dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Upewnij się, że wszystkie niezbędne porty w sieci wirtualnej są otwarte, aby ruch może poruszać się przez ISE w obrębie podsieci w tej sieci wirtualnej.

* Utwórz ISE.

* Dodaj dodatkową pojemność do ISE.

> [!IMPORTANT]
> Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki, które działają w ISE, korzystają z planu cenowego innego niż plan cenowy oparty na zużyciu. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Sieć wirtualna platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli nie masz sieci wirtualnej, Dowiedz się, jak [utworzyć sieć wirtualną platformy Azure](../virtual-network/quick-create-portal.md). 

  * Sieć wirtualna musi mieć cztery *puste* podsieci do tworzenia i wdrażania zasobów w ISE. Można utworzyć te podsieci z wyprzedzeniem lub poczekać, aż utworzysz ISE, w którym można tworzyć podsieci w tym samym czasie. Dowiedz się więcej o [wymaganiach podsieci](#create-subnet).

  * Nazwy podsieci muszą zaczynać się od znaku alfabetycznego lub podkreślenia i nie mogą używać następujących znaków: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 

  * Upewnij się, że sieć wirtualna [udostępnia te porty](#ports) , aby ISE działała prawidłowo i pozostaje dostępna.

  * Jeśli używasz [ExpressRoute](../expressroute/expressroute-introduction.md), który zapewnia połączenie prywatne z usługami w chmurze firmy Microsoft, musisz [utworzyć tabelę tras](../virtual-network/manage-route-table.md) , która ma następującą trasę i połączyć tę tabelę z każdą podsiecią używaną przez ISE:

    **Nazwa**: <*nazwę trasy*><br>
    **Prefiks adresu**: 0.0.0.0/0<br>
    **Następny przeskok**: Internet

* Jeśli chcesz użyć niestandardowych serwerów DNS dla sieci wirtualnej platformy Azure, [Skonfiguruj te serwery, wykonując następujące czynności](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) przed wdrożeniem ISE w sieci wirtualnej. W przeciwnym razie przy każdej zmianie serwera DNS trzeba również ponownie uruchomić ISE.

  > [!IMPORTANT]
  > W przypadku zmiany ustawień serwera DNS po utworzeniu ISE upewnij się, że ponownie uruchomiono ISE. Aby uzyskać więcej informacji na temat zarządzania ustawieniami serwera DNS, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="ports"></a>

## <a name="check-network-ports"></a>Sprawdzanie portów sieciowych

Gdy używasz ISE z siecią wirtualną platformy Azure, typowy problem z instalacją ma co najmniej jeden blokowany port. Łączniki używane do tworzenia połączeń między ISE i systemem docelowym mogą również mieć własne wymagania dotyczące portów. Jeśli na przykład komunikujesz się z systemem FTP przy użyciu łącznika FTP, upewnij się, że port używany w systemie FTP jest dostępny, na przykład, port 21 do wysyłania poleceń. Aby upewnić się, że ISE pozostaje dostępna i może być poprawnie działała, Otwórz porty określone przez poniższą tabelę. W przeciwnym razie, jeśli którykolwiek z wymaganych portów jest niedostępny, ISE przestanie działać.

> [!IMPORTANT]
> Porty źródłowe są tymczasowe, dlatego upewnij się, że zostały ustawione na `*` dla wszystkich reguł.
> W przypadku komunikacji wewnętrznej wewnątrz podsieci ISE wymaga otwarcia wszystkich portów w tych podsieciach.

* W przypadku utworzenia nowej sieci wirtualnej i podsieci bez ograniczeń nie trzeba konfigurować [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/security-overview.md#network-security-groups) w sieci wirtualnej, aby kontrolować ruch w różnych podsieciach.

* W istniejącej sieci wirtualnej można *Opcjonalnie* skonfigurować sieciowych grup zabezpieczeń przez [Filtrowanie ruchu sieciowego między podsieciami](../virtual-network/tutorial-filter-network-traffic.md). W przypadku wybrania tej trasy w sieci wirtualnej, w której chcesz skonfigurować sieciowych grup zabezpieczeń, upewnij się, że otwarto porty określone w poniższej tabeli. W przypadku używania [reguł zabezpieczeń sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules)wymagane są zarówno protokoły TCP, jak i UDP.

* Jeśli masz już istniejące sieciowych grup zabezpieczeń lub zapory w sieci wirtualnej, upewnij się, że zostały otwarte porty określone przez poniższą tabelę. W przypadku używania [reguł zabezpieczeń sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md#security-rules)wymagane są zarówno protokoły TCP, jak i UDP.

Poniżej przedstawiono tabelę opisującą porty w sieci wirtualnej używane przez ISE oraz miejsce, w którym te porty są używane. [Tagi usługi Menedżer zasobów](../virtual-network/security-overview.md#service-tags) reprezentuje grupę prefiksów adresów IP, która pomaga zminimalizować złożoność podczas tworzenia reguł zabezpieczeń.

| Przeznaczenie | Kierunek | Porty docelowe | Tag usługi źródłowej | Docelowy tag usługi | Uwagi |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Komunikacja z Azure Logic Apps | Wychodzące | 80, 443 | VirtualNetwork | Internet | Port zależy od usługi zewnętrznej, z którą komunikuje się usługa Logic Apps |
| Usługa Active Directory systemu Azure | Wychodzące | 80, 443 | VirtualNetwork | Usługi azureactivedirectory | |
| Zależność usługi Azure Storage | Wychodzące | 80, 443 | VirtualNetwork | Usługa Storage | |
| Komunikacja między sieciami | Przychodzące & wychodzące | 80, 443 | VirtualNetwork | VirtualNetwork | Do komunikacji między podsieciami |
| Komunikacja do Azure Logic Apps | Przychodzące | 443 | Punkty końcowe dostępu wewnętrznego: <br>VirtualNetwork <p><p>Punkty końcowe dostępu zewnętrznego: <br>Internet <p><p>**Uwaga**: te punkty końcowe odnoszą się do ustawienia punktu końcowego, który został [wybrany podczas tworzenia ISE](#create-environment). Aby uzyskać więcej informacji, zobacz [dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | Adres IP komputera lub usługi, który wywołuje każdy wyzwalacz żądania lub element webhook, który istnieje w aplikacji logiki. Zamknięcie lub zablokowanie tego portu uniemożliwia wywołania HTTP aplikacjom logiki z wyzwalaczami żądań. |
| Historia uruchamiania aplikacji logiki | Przychodzące | 443 | Punkty końcowe dostępu wewnętrznego: <br>VirtualNetwork <p><p>Punkty końcowe dostępu zewnętrznego: <br>Internet <p><p>**Uwaga**: te punkty końcowe odnoszą się do ustawienia punktu końcowego, który został [wybrany podczas tworzenia ISE](#create-environment). Aby uzyskać więcej informacji, zobacz [dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | Adres IP komputera, z którego ma zostać wyświetlona historia uruchamiania aplikacji logiki. Mimo że zamknięcie lub zablokowanie tego portu nie uniemożliwia wyświetlenia historii uruchamiania, nie można wyświetlić danych wejściowych i wyjściowych dla każdego kroku w tej historii uruchamiania. |
| Zarządzanie połączeniami | Wychodzące | 443 | VirtualNetwork  | Internet | |
| Publikowanie dzienników diagnostycznych & metryki | Wychodzące | 443 | VirtualNetwork  | AzureMonitor | |
| Komunikacja z Traffic Manager platformy Azure | Przychodzące | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps Designer — właściwości dynamiczne | Przychodzące | 454 | Internet | VirtualNetwork | Żądania pochodzą z Logic Apps [adresów IP ruchu przychodzącego punktu końcowego dostępu w tym regionie](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Zależność zarządzania App Service | Przychodzące | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Wdrożenie łącznika | Przychodzące | 454 | AzureConnectors | VirtualNetwork | Niezbędne do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje, że wdrożenia ISE kończą się niepowodzeniem i uniemożliwiają aktualizacje lub poprawki łącznika. |
| Wdrożenie zasad łącznika | Przychodzące | 3443 | Internet | VirtualNetwork | Niezbędne do wdrażania i aktualizowania łączników. Zamknięcie lub zablokowanie tego portu powoduje, że wdrożenia ISE kończą się niepowodzeniem i uniemożliwiają aktualizacje lub poprawki łącznika. |
| Zależność SQL platformy Azure | Wychodzące | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Wychodzące | 1886 | VirtualNetwork | AzureMonitor | Aby opublikować stan kondycji Resource Health |
| Punkt końcowy zarządzania API Management | Przychodzące | 3443 | APIManagement | VirtualNetwork | |
| Zależność od dziennika do zasad usługi Event Hub i agenta monitorowania | Wychodzące | 5672 | VirtualNetwork | EventHub | |
| Dostęp do pamięci podręcznej platformy Azure dla wystąpień Redis między wystąpieniami roli | Przychodzące <br>Wychodzące | 6379-6383 | VirtualNetwork | VirtualNetwork | Ponadto, aby ISE współpracował z usługą Azure cache for Redis, należy otworzyć te [porty wychodzące i przychodzące opisane w pamięci podręcznej platformy Azure dla Redis często zadawane pytania](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Przychodzące | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Tworzenie ISE

Aby utworzyć środowisko usługi integracji (ISE), wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)w głównym menu platformy Azure wybierz pozycję **Utwórz zasób**.
W polu wyszukiwania wprowadź "środowisko usługi integracji" jako filtr.

   ![Utwórz nowy zasób](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W okienku tworzenia środowisko usługi integracji wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Podaj te szczegóły dla danego środowiska, a następnie wybierz pozycję **Przegląd + Utwórz**, na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagane | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Azure-subscription-name*> | Subskrypcja platformy Azure do użycia w danym środowisku |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group-name*> | Grupa zasobów platformy Azure, w której ma zostać utworzone środowisko |
   | **Nazwa środowiska usługi integracji** | Tak | <*Environment-name*> | Nazwa ISE, która może zawierać tylko litery, cyfry, łączniki (`-`), podkreślenia (`_`) i kropki (`.`). |
   | **Lokalizacja** | Tak | <*Azure-Datacenter-region*> | Region centrum danych platformy Azure, w którym ma zostać wdrożone środowisko |
   | **SKU** | Tak | **Premium** lub **Developer (bez umowy SLA)** | Jednostka SKU ISE do utworzenia i użycia. Aby uzyskać różnice między tymi jednostkami SKU, zobacz [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Ważne**: Ta opcja jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić. |
   | **Dodatkowa pojemność** | Premium: <br>Tak <p><p>Developer: <br>Nie dotyczy | Premium: <br>od 0 do 10 <p><p>Developer: <br>Nie dotyczy | Liczba dodatkowych jednostek przetwarzania, które mają być używane dla tego zasobu ISE. Aby dodać pojemność po utworzeniu, zobacz [Dodawanie pojemności ISE](#add-capacity). |
   | **Punkt końcowy dostępu** | Tak | **Wewnętrzne** lub **zewnętrzne** | Typ punktów końcowych dostępu do użycia w ISE, które określają, czy wyzwalacze żądania lub elementu webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej. Typ punktu końcowego ma także wpływ na dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki. Aby uzyskać więcej informacji, zobacz [dostęp do punktu końcowego](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Ważne**: Ta opcja jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić. |
   | **Sieć wirtualna** | Tak | <*Azure-Virtual-Network-name*> | Sieć wirtualna platformy Azure, w której chcesz wstrzyknąć środowisko, aby aplikacje logiki w tym środowisku mogły uzyskiwać dostęp do sieci wirtualnej. Jeśli nie masz sieci, [najpierw Utwórz sieć wirtualną platformy Azure](../virtual-network/quick-create-portal.md). <p>**Ważne**: to iniekcja można wykonać *tylko* po utworzeniu ISE. |
   | **Podsieci** | Tak | <*podsieci-Resource-list*> | ISE wymaga czterech *pustych* podsieci do tworzenia i wdrażania zasobów w środowisku. Aby utworzyć każdą podsieć, [wykonaj kroki opisane w tej tabeli](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Utwórz podsieć**

   Aby tworzyć i wdrażać zasoby w danym środowisku, ISE muszą mieć cztery *puste* podsieci, które nie są delegowane do żadnej usługi. *Nie* można zmienić tych adresów podsieci po utworzeniu środowiska.
   
   > [!IMPORTANT]
   > 
   > Nazwy podsieci muszą zaczynać się od litery lub znaku podkreślenia (bez cyfr) i nie mogą używać następujących znaków: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.
   
   Ponadto każda podsieć musi spełniać następujące wymagania:

   * Używa [formatu routingu bezklasowego (cidr)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) i przestrzeni adresowej klasy B.

   * Używa co najmniej `/27` w przestrzeni adresowej, ponieważ każda podsieć musi mieć *co najmniej* 32 *adresów.* Na przykład:

     * `10.0.0.0/27` ma 32 adresów, ponieważ 2<sup>(32-27)</sup> wynosi 2<sup>5</sup> lub 32.

     * `10.0.0.0/24` ma 256 adresów, ponieważ 2<sup>(32-24)</sup> to 2<sup>8</sup> lub 256.

     * `10.0.0.0/28` ma tylko 16 adresów i jest za mały, ponieważ 2<sup>(32-28)</sup> wynosi 2<sup>4</sup> lub 16.

     Aby dowiedzieć się więcej o obliczaniu adresów, zobacz [bloki protokołu IPv4 w protokole CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Jeśli używasz [ExpressRoute](../expressroute/expressroute-introduction.md), musisz [utworzyć tabelę tras](../virtual-network/manage-route-table.md) , która ma następującą trasę i połączyć tę tabelę z każdą podsiecią używaną przez ISE:

     **Nazwa**: <*nazwę trasy*><br>
     **Prefiks adresu**: 0.0.0.0/0<br>
     **Następny przeskok**: Internet

   1. Na liście **podsieci** wybierz kolejno pozycje **Zarządzaj konfiguracja podsieci**.

      ![Zarządzanie konfiguracją podsieci](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. W okienku **podsieci** wybierz **podsieć**.

      ![Dodawanie podsieci](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. W okienku **Dodaj podsieć** podaj te informacje.

      * **Name**: Nazwa podsieci
      * **Zakres adresów (blok CIDR)** : zakres podsieci w sieci wirtualnej i w formacie CIDR

      ![Dodawanie szczegółów podsieci](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   1. Powtórz te kroki dla trzech więcej podsieci.

      > [!NOTE]
      > Jeśli podsieci, które próbujesz utworzyć, są nieprawidłowe, Azure Portal pokazuje komunikat, ale nie blokuje postępu.

   Aby uzyskać więcej informacji na temat tworzenia podsieci, zobacz [Dodawanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

1. Po pomyślnym sprawdzeniu przez platformę Azure informacji o ISE wybierz pozycję **Utwórz**, na przykład:

   ![Po pomyślnej weryfikacji wybierz pozycję "Utwórz".](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Platforma Azure rozpocznie wdrażanie środowiska, ale ten proces *może* trwać do dwóch godzin przed zakończeniem. Aby sprawdzić stan wdrożenia, na pasku narzędzi platformy Azure wybierz ikonę powiadomienia, która spowoduje otwarcie okienka powiadomienia.

   ![Sprawdź stan wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Jeśli wdrożenie zakończyło się pomyślnie, na platformie Azure zostanie wyświetlone następujące powiadomienie:

   ![Wdrożenie powiodło się](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   W przeciwnym razie postępuj zgodnie z Azure Portal instrukcjami dotyczącymi rozwiązywania problemów z wdrażaniem.

   > [!NOTE]
   > Jeśli wdrożenie nie powiedzie się lub usuniesz ISE, platforma Azure może upłynąć do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być konieczne odczekanie przed ponownym użyciem tych podsieci w innym ISE.
   >
   > Po usunięciu sieci wirtualnej platforma Azure zazwyczaj zajmie maksymalnie dwie godziny przed zwolnieniem podsieci, ale ta operacja może trwać dłużej. 
   > Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
   > Zobacz [usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Aby wyświetlić swoje środowisko, wybierz pozycję **Przejdź do zasobu** , jeśli platforma Azure nie przejdzie automatycznie do środowiska po zakończeniu wdrażania.

1. Aby sprawdzić kondycję sieci ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Aby rozpocząć tworzenie aplikacji logiki i innych artefaktów w ISE, zobacz [Dodawanie artefaktów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Dodawanie pojemności ISE

Jednostka bazowa ISE Premium ma stałą pojemność, więc jeśli potrzebujesz większej przepływności, możesz dodać więcej jednostek skalowania podczas tworzenia lub później. Możesz automatycznie skalować na podstawie metryk wydajności lub w oparciu o kilka dodatkowych jednostek przetwarzania. W przypadku wybrania opcji Skalowanie automatyczne na podstawie metryk można wybrać różne kryteria i określić warunki progowe spełniające te kryteria. Jednostka SKU dla deweloperów nie obejmuje możliwości dodawania jednostek skalowania.

1. W Azure Portal Znajdź swój ISE.

1. Aby sprawdzić metryki użycia i wydajności dla ISE, w menu głównym ISE wybierz pozycję **Przegląd**.

   ![Wyświetl użycie dla ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Aby skonfigurować Skalowanie automatyczne, w obszarze **Ustawienia**wybierz pozycję **Skaluj w poziomie**. Na karcie **Konfiguracja** wybierz pozycję **Włącz automatyczne skalowanie**.

   ![Włącz Skalowanie automatyczne](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. W obszarze **Nazwa ustawienia automatycznego skalowania**Podaj nazwę ustawienia.

1. W sekcji **domyślnej** wybierz pozycję **skalowanie na podstawie metryki** lub **skali do określonej liczby wystąpień**.

   * Jeśli wybierzesz opcję oparty na wystąpieniu, wprowadź liczbę jednostek przetwarzania z przedziału od 0 do 10 włącznie.

   * W przypadku wybrania opcji opartych na pomiarach wykonaj następujące czynności:

     1. W sekcji **reguły** wybierz pozycję **Dodaj regułę**.

     1. W okienku **reguła skalowania** Skonfiguruj swoje kryteria i akcję, które mają być wykonywane po wyzwoleniu reguły.

     1. Gdy skończysz, wybierz pozycję **Dodaj**.

1. Po zakończeniu korzystania z ustawień automatycznego skalowania Zapisz zmiany.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie artefaktów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Sprawdź kondycję sieci dla środowisk usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informacje o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
