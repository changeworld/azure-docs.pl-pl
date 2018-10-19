---
title: Połączyć się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps za pośrednictwem środowiska usług integracji (ISE)
description: Utwórz środowisku usługi integracji (ISE) aby logic apps i kont integracji mogą uzyskiwać dostęp do sieci wirtualnych platformy Azure, pozostając prywatnych i odizolowane od publicznych lub "global" platformy Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: b4e4e801c3c54b635f2f13b319257018ea544c03
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404124"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Połączyć się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps za pośrednictwem środowiska usług integracji (ISE)

> [!NOTE]
> Ta funkcja jest w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview).

Dla scenariuszy integracji, w której muszą mieć dostęp do usługi logic apps i kont integracji [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), Utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), czyli prywatnych i izolowanym środowisku, używającej dedykowanego magazynu i inne zasoby oddzielone publicznie lub *globalnego* usługę Logic Apps. Ta separacja zmniejsza żadnego wpływu, której innych dzierżaw usługi Azure może występować na wydajność Twojej aplikacji. Ta ISE można połączyć sieci wirtualnej platformy Azure, która wdraża usługę Logic Apps do sieci wirtualnej. Podczas tworzenia konta aplikacji lub Integracja z usługą Logic Apps, wybierz ten ISE ich lokalizacji. Twoje konto aplikacji lub Integracja z usługą Logic Apps mogą uzyskiwać bezpośrednio dostęp do zasobów, takich jak maszyny wirtualne (VM), serwery, systemów i usług w Twojej sieci wirtualnej. 

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Umożliwia skonfigurowanie uprawnień w sieci wirtualnej platformy Azure, aby wystąpieniem prywatnym Logic Apps może uzyskiwać dostęp do sieci wirtualnej.

* Tworzenie środowiska integration service environment (ISE). 

* Tworzenie aplikacji logiki, które mogą być uruchamiane w swoje środowiska ISE. 

* Tworzenie konta integracji aplikacji logiki w swoje środowiska ISE.

Aby uzyskać więcej informacji na temat środowisk usługi integracji, zobacz [dostęp do zasobów sieci wirtualnej platformy Azure z izolowanej usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Jeśli nie masz sieci wirtualnej platformy Azure, Dowiedz się, jak [tworzenie sieci wirtualnej platformy Azure](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Podczas tworzenia środowiska nie potrzebujesz sieci wirtualnej platformy Azure, możesz *tylko* wybierz sieć wirtualną jako elementów równorzędnych w danym środowisku, podczas tworzenia tego środowiska. 

* Zostać zapewniony bezpośredni dostęp do sieci wirtualnej platformy Azure, aplikacje logiki [skonfigurowanie uprawnień kontroli dostępu opartej na rolach (RBAC)](#vnet-access) dzięki usłudze Logic Apps ma uprawnienia do uzyskiwania dostępu do sieci wirtualnej. 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Ustaw uprawnienia do sieci wirtualnej

Podczas tworzenia środowiska usług integracji, można wybrać sieci wirtualnej platformy Azure jako *równorzędnej* dla danego środowiska. Jednakże, można wykonać tylko ten krok lub *komunikacji równorzędnej*, podczas tworzenia środowiska. Ta relacja umożliwia usługę Logic Apps, bezpośrednio łączyć się z zasobami w tej sieci wirtualnej i zapewnia dostęp do Twojego środowiska do tych zasobów. 

Aby można było wybrać sieć wirtualną, możesz skonfigurować uprawnienia kontroli dostępu opartej na rolach (RBAC), w sieci wirtualnej. Aby wykonać to zadanie, należy przypisać określonych ról w usłudze Azure Logic Apps.

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz sieć wirtualną. 

1. Sieci wirtualnej menu wybierz kolejno pozycje **kontrola dostępu (IAM)**. 

1. W obszarze **kontroli dostępu**, wybierz opcję **przypisania roli** Jeśli jeszcze nie wybrano. Na **przypisania roli** narzędzi, wybierz **Dodaj**. 

   ![Dodawanie przypisania roli](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Na **Dodaj uprawnienia** okienku Konfigurowanie każdej roli w tej tabeli dla usługi Azure Logic Apps. Upewnij się, że wybrano **Zapisz** po zakończeniu każdej roli:

   | Rola | Przypisz dostęp do | Wybierz pozycję | 
   |------|------------------|--------|
   | **Współautor sieci** | **Usługa Azure AD użytkownika, grupy lub aplikacji** | Wprowadź **usługi Azure Logic Apps**. Po wyświetleniu listy elementów członkowskich, wybierz tę samą wartość. <p>**Porada**: Jeśli nie możesz znaleźć tę usługę, wprowadź identyfikator aplikacji usługi Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Współautor klasycznej** | **Usługa Azure AD użytkownika, grupy lub aplikacji** | Wprowadź **usługi Azure Logic Apps**. Po wyświetleniu listy elementów członkowskich, wybierz tę samą wartość. <p>**Porada**: Jeśli nie możesz znaleźć tę usługę, wprowadź identyfikator aplikacji usługi Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Na przykład:

   ![Dodawanie uprawnień](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   Aby uzyskać więcej informacji na temat uprawnień roli wymagane w przypadku komunikacji równorzędnej, zobacz [uprawnienia sekcji Tworzenie, zmienianie, lub usunąć komunikację równorzędną sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#permissions). 

Jeśli sieć wirtualna jest połączona za pośrednictwem usługi ExpressRoute platformy Azure, Azure Point-to-Site VPN lub usługi Azure Site-to-Site VPN, przejdź do następnej sekcji, więc można dodać podsieć bramy wymagane. W przeciwnym razie kontynuuj [tworzenia środowiska](#create-environment).

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>Dodaj podsieć bramy dla sieci wirtualnych przy użyciu usługi ExpressRoute lub sieci VPN

Po wykonaniu poprzednich kroków, aby dać środowiska integration service environment (ISE) dostęp do sieci wirtualnej platformy Azure, który jest połączony za pośrednictwem [usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md), [Azure Point-to-Site VPN](../vpn-gateway/point-to-site-about.md), lub [usługi Azure Site-to-Site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), musisz również dodać [ *podsieci bramy* ](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) z siecią wirtualną:

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz sieć wirtualną. Sieci wirtualnej menu wybierz kolejno pozycje **podsieci**, a następnie wybierz **podsieci bramy** > **OK**.

   ![Dodaj podsieć bramy](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. Teraz Utwórz [ *tabeli tras*](../virtual-network/manage-route-table.md), który zostanie skojarzony z podsiecią bramy, została utworzona wcześniej.

   1. W głównym menu platformy Azure, wybierz **Utwórz zasób** > 
    **sieć** > **tabeli tras**.

      ![Tworzenie tabeli tras](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. Podaj informacje dotyczące tabeli tras, takie jak nazwa, Twojej subskrypcji platformy Azure do użycia, grupy zasobów platformy Azure i lokalizacji. Upewnij się, że **propagację trasy protokołu BGP** właściwość jest ustawiona na **włączone**, a następnie wybierz **Utwórz**.

      ![Podawanie szczegółów dotyczących tabeli tras](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. W menu tabeli tras, wybierz pozycję **podsieci**, a następnie wybierz **skojarzyć**. 

      ![Łączenie tabeli tras do podsieci](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. Wybierz **sieć wirtualna**, a następnie wybierz sieć wirtualną.
   
   1. Wybierz **podsieci**, a następnie wybierz pozycję podsieć bramy utworzonej wcześniej.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

1. W przypadku sieci VPN punkt lokacja za wykonaj następujące kroki:

   1. Na platformie Azure Znajdź i zaznacz zasób Brama sieci wirtualnej.

   1. Bramy menu wybierz kolejno pozycje **Point-to-site configuration**. 
   a następnie wybierz **klienta VPN Pobierz** pozwala uzyskać najbardziej aktualną konfiguracji klienta sieci VPN.

      ![Pobierz najnowszego klienta sieci VPN](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

Teraz wszystko z konfiguracją podsieci bramy dla sieci wirtualnych, które używają usługi ExpressRoute, point-to-site VPN lub sieci VPN typu lokacja lokacja. Tworzenie środowiska usługi integracji, należy wykonać kolejne kroki.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Utwórz swoje środowiska ISE

Aby utworzyć środowiska integration service environment (ISE), wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **Utwórz zasób**.

   ![Tworzenie nowego zasobu](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W polu wyszukiwania wprowadź "środowisko usługi integracji" jako filtr.
Wybierz z listy wyników **środowisko usługi integracji (wersja zapoznawcza)**, a następnie wybierz **Utwórz**.

   ![Wybierz pozycję "Środowisko integracji usługi"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Wybierz pozycję "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Podaj te szczegóły dotyczące środowiska:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagane | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Yes | <*Nazwa środowiska*> | Nazwa do nadania środowiska | 
   | **Subskrypcja** | Yes | <*Azure-subscription-name*> | Subskrypcji platformy Azure do użycia w danym środowisku | 
   | **Grupa zasobów** | Yes | <*Azure-resource nazwa_grupy*> | Grupa zasobów platformy Azure, w którym chcesz utworzyć swoje środowisko |
   | **Lokalizacja** | Yes | <*Region platformy Azure — centrum danych*> | Region centrum danych platformy Azure miejsce do przechowywania informacji o środowisku |
   | **Wirtualna sieć równorzędna** | Nie | <*Nazwa w przypadku sieci Wirtualnej platformy Azure*> | Sieć wirtualna platformy Azure, aby skojarzyć ze środowiskiem jako *równorzędnej* aby logic apps w tym środowisku mogą uzyskiwać dostęp do sieci wirtualnej. Przed utworzeniem tej relacji, upewnij się, że już [Konfigurowanie kontroli dostępu opartej na rolach w Twojej sieci wirtualnej dla usługi Azure Logic Apps](#vnet-access). <p>**Ważne**: mimo że sieć wirtualna nie jest wymagane, można wybrać sieć wirtualną *tylko* po utworzeniu środowiska. | 
   | **Nazwa komunikacji równorzędnej** | Tak, przy użyciu wybranej sieci wirtualnej | <*Nazwa komunikacji równorzędnej*> | Nazwa do nadania relacji elementu równorzędnego | 
   | **Zakres adresów IP w sieci Wirtualnej** | Tak, przy użyciu wybranej sieci wirtualnej | <*Zakres adresów IP*> | Zakres adresów IP na potrzeby tworzenia zasobów w danym środowisku. Ten zakres, należy użyć [formatu Bezklasowego routingu międzydomenowego (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), na przykład 10.0.0.1/16 i wymaga przestrzeń adresów klasy B. Zakres nie może istnieć w przestrzeni adresowej dla sieci wirtualnej wybrany w **wirtualnej sieci równorzędnej** właściwości ani żadnych innych prywatnych adresów IP gdzie połączone sieci równorzędnej za pośrednictwem komunikacji równorzędnej lub bramy. <p><p>**Ważne**: możesz *nie można zmienić* tego zakresu adresów, po utworzeniu środowiska. |
   |||||
   
1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 

   Wdrażanie środowiska Start Azure, ale ten proces może potrwać *do dwóch godzin* przed zakończeniem. 
   Aby sprawdzić stan wdrożenia, na pasku narzędzi usługi Azure wybierz ikonę powiadomienia, który zostanie otwarty w okienku powiadomienia.

   ![Sprawdź stan wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Po pomyślnym zakończeniu wdrożenia platformy Azure zostaną wyświetlone to powiadomienie:

   ![Wdrażanie zakończyło się pomyślnie](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Aby wyświetlić swoje środowisko, wybierz opcję **przejdź do zasobu** Jeśli Azure nie automatyczne przejście do środowiska, po zakończeniu wdrożenia.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Tworzenie aplikacji logiki — środowiska ISE

Do tworzenia aplikacji logiki, które używają środowiska integration service environment (ISE), wykonaj kroki zwykle [jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) , ale z te różnice i zagadnienia: 

* Podczas tworzenia aplikacji logiki, **lokalizacji** swoje ISEs w obszarze listy właściwości **środowiska usług integracji** wraz z dostępnych regionów. Wybierz swoje ISE, a nie region, na przykład:

  ![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Możesz użyć tego samego elementy wbudowane, takich jak HTTP, wyzwalacza lub akcji, które działają w tej samej platformy ISE co aplikacja logiki nadrzędnego. Łączniki z usługą **ISE** etykiety również uruchamiać w tej samej platformy ISE jako nadrzędny aplikacji logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety.

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Jeśli wcześniej skonfigurowano usługi ISE z siecią wirtualną platformy Azure jako element równorzędny logic apps w swoje środowiska ISE można uzyskać dostęp do zasobów w tej sieci wirtualnej. Systemów lokalnych w sieci wirtualnej, która jest połączona z ISE aplikacje logiki można uzyskać dostęp do tych systemów, przy użyciu dowolnego z tych elementów: 

  * Łącznik platformy ISE dla tego systemu, na przykład SQL Server

  * Akcja HTTP 

  * Łącznik niestandardowy

  Dla systemów lokalnych, które nie znajdują się w sieci wirtualnej lub nie masz ISE łączniki, można wciąż mogą się łączyć po [konfigurowania i używania lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Utwórz konto integracji — środowiska ISE

Aby użyć konta integracji z usługą logic apps w środowisku usługi integracji (ISE), należy użyć tego konta integracji *tym samym środowisku* co usługa logic apps. Logic apps w środowisku ISE można odwoływać się tylko konta integracji w tym samym środowisku ISE. 

Aby utworzyć konto integracji, która używa środowiska ISE, czynności zwykle w [sposób tworzenia kont integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z wyjątkiem **lokalizacji** właściwość, która teraz wyświetla Twoje ISEs w obszarze  **Środowiska usług integracji** wraz z dostępnych regionów. Wybierz swoje ISE, a nie region, na przykład:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="http://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
