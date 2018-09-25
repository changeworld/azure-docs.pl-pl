---
title: Łączenie z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps
description: Dostęp do sieci wirtualnych (Vnet) platformy Azure z usługi Azure Logic Apps, środowiska możesz tworzyć prywatnego, dedykowanego i izolowane integracji usługi zachowujących aplikacji logiki i inne zasoby oddzielić z publicznej lub "global" platformy Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b1a75c140376c1e2e2fdfdcd1581978301ab32f1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996472"
---
# <a name="create-isolated-environments-to-access-azure-virtual-networks-vnets-from-azure-logic-apps"></a>Tworzenie środowiska izolowanego dostępu do sieci wirtualnych (Vnet) platformy Azure w usłudze Azure Logic Apps

> [!NOTE]
> Ta funkcja jest w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview).

Dla scenariuszy integracji, w której muszą mieć dostęp do usługi logic apps i kont integracji [sieci wirtualnej platformy Azure (VNET)](../virtual-network/virtual-networks-overview.md), możesz utworzyć [ *środowisko usługi integracji* (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) który stanowi łącze do sieci wirtualnej i wdraża usługę Logic Apps do sieci wirtualnej. Podczas tworzenia aplikacji logiki i kont integracji, możesz wybrać ten ISE jako ich lokalizacji. Usługi logic apps i kont integracji można następnie uzyskać dostęp do zasobów, takich jak maszyny wirtualne (VM), serwery, systemów i usług w Twojej sieci Wirtualnej. 

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Twoje ISE jest prywatnych i izolowanym środowisku, korzystającą z dedykowanych dla magazynu i innych zasobów, które istnieją niezależnie od publicznego lub *globalnego* usługę Logic Apps. Ta separacja pozwala zredukować ich wpływ, jakie innych dzierżaw usługi Azure może mieć na wydajność Twojej aplikacji. 

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Konfigurowanie uprawnień w sieci wirtualnej platformy Azure, umożliwiające wystąpieniem prywatnym Logic Apps mogą uzyskiwać dostęp do sieci wirtualnej.

* Tworzenie środowiska integration service environment (ISE). 

* Tworzenie aplikacji logiki, które mogą być uruchamiane w swoje środowiska ISE. 

* Tworzenie konta integracji aplikacji logiki w swoje środowiska ISE.

Aby uzyskać więcej informacji na temat środowisk usługi integracji, zobacz [dostęp do zasobów usługi Azure Virtual Network (VNET) z izolowanej usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Jeśli nie masz, siecią wirtualną platformy Azure, Dowiedz się, jak [tworzenie sieci wirtualnej platformy Azure](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Podczas tworzenia środowiska nie jest konieczne siecią wirtualną platformy Azure, możesz *tylko* wybierz sieć Wirtualną jako elementów równorzędnych w danym środowisku, podczas tworzenia tego środowiska. 

* Zostać zapewniony bezpośredni dostęp do sieci wirtualnej platformy Azure, aplikacje logiki [skonfigurowanie uprawnień kontroli dostępu opartej na rolach (RBAC)](#vnet-access) dzięki usłudze Logic Apps ma uprawnienia do uzyskiwania dostępu do sieci wirtualnej. 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-up-vnet-permissions"></a>Skonfiguruj uprawnienia sieci Wirtualnej

Podczas tworzenia środowiska usług integracji, siecią wirtualną platformy Azure (VNET) można wybrać jako *równorzędnej* dla danego środowiska. Jednakże, można wykonać tylko ten krok lub *komunikacji równorzędnej*, podczas tworzenia środowiska. Ta relacja umożliwia usługę Logic Apps, bezpośrednio łączyć się z zasobami w tej sieci Wirtualnej i zapewnia dostęp do Twojego środowiska do tych zasobów. 

Aby można było wybrać sieć Wirtualną, możesz skonfigurować uprawnienia kontroli dostępu opartej na rolach (RBAC), w sieci Wirtualnej. Aby wykonać to zadanie, należy przypisać określonych ról w usłudze Azure Logic Apps.

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz sieć Wirtualną. Twojej sieci Wirtualnej menu wybierz kolejno pozycje **kontrola dostępu (IAM)**. 

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
   | **Subskrypcja** | Yes | <*Nazwa w przypadku subskrypcji platformy Azure*> | Subskrypcji platformy Azure do użycia w danym środowisku | 
   | **Grupa zasobów** | Yes | <*Azure-resource nazwa_grupy*> | Grupa zasobów platformy Azure, w którym chcesz utworzyć swoje środowisko |
   | **Lokalizacja** | Yes | <*Region platformy Azure — centrum danych*> | Region centrum danych platformy Azure miejsce do przechowywania informacji o środowisku |
   | **Wirtualna sieć równorzędna** | Nie | <*Nazwa w przypadku sieci Wirtualnej platformy Azure*> | Sieci wirtualnej platformy Azure (VNET), aby skojarzyć ze środowiskiem jako *równorzędnej* , logic apps w tym środowisku mogą uzyskiwać dostęp do sieci wirtualnej. Przed utworzeniem tej relacji, upewnij się, że już [Konfigurowanie kontroli dostępu opartej na rolach w sieci wirtualnej dla usługi Azure Logic Apps](#vnet-access). <p>**Ważne**: mimo że w sieci Wirtualnej nie jest wymagane, możesz wybrać sieć Wirtualną *tylko* po utworzeniu środowiska. | 
   | **Nazwa komunikacji równorzędnej** | Tak, przy użyciu wybranej sieci Wirtualnej | <*Nazwa komunikacji równorzędnej*> | Nazwa do nadania relacji elementu równorzędnego | 
   | **Zakres adresów IP w sieci Wirtualnej** | Tak, przy użyciu wybranej sieci Wirtualnej | <*Zakres adresów IP*> | Zakres adresów IP na potrzeby tworzenia zasobów w danym środowisku. Ten zakres, należy użyć [formatu Bezklasowego routingu międzydomenowego (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), na przykład 10.0.0.1/16 i wymaga przestrzeń adresów klasy B. Zakres nie może istnieć w przestrzeni adresowej dla sieci Wirtualnej wybrany w **wirtualnej sieci równorzędnej** właściwości ani żadnych innych prywatnych adresów IP gdzie połączone sieci równorzędnej za pośrednictwem komunikacji równorzędnej lub bramy. <p><p>**Ważne**: możesz *nie można zmienić* tego zakresu adresów, po utworzeniu środowiska. |
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

* Jeśli wcześniej skonfigurowano usługi ISE z siecią wirtualną platformy Azure jako element równorzędny logic apps w swoje środowiska ISE można uzyskać dostęp do zasobów w tej sieci Wirtualnej. Systemów lokalnych w sieci Wirtualnej, która jest połączona z ISE aplikacje logiki można uzyskać dostęp do tych systemów, przy użyciu dowolnego z tych elementów: 

  * Łącznik platformy ISE dla tego systemu, na przykład SQL Server

  * Akcja HTTP 

  * Łącznik niestandardowy

  Dla systemów lokalnych, które nie znajdują się w sieci Wirtualnej lub nie masz ISE łączniki, można wciąż mogą się łączyć po [konfigurowania i używania lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md).

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
