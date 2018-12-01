---
title: Połączyć się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps za pośrednictwem środowiska usług integracji (ISE)
description: Tworzenie środowiska usługi integracji (ISE) aby logic apps i kont integracji mogą uzyskiwać dostęp do sieci wirtualnych platformy Azure (Vnet), pozostając prywatnych i odizolowane od publicznych lub "global" Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/29/2018
ms.openlocfilehash: 798b50887bcfdf5b4298c37beb1b9eea8f9abdda
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682201"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Połączyć się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps za pośrednictwem środowiska usług integracji (ISE)

> [!NOTE]
> Ta funkcja jest w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview).

W scenariuszach, w której muszą mieć dostęp do usługi logic apps i kont integracji [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), Utwórz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE jest prywatne i izolowanym środowisku, używającej dedykowanego magazynu i inne zasoby utrzymywane oddzielić od publicznego lub *globalnego* usługę Logic Apps. Ta separacja zmniejsza żadnego wpływu, której innych dzierżaw usługi Azure może występować na wydajność Twojej aplikacji. Twoje ISE jest *wprowadzony* w sieci wirtualnej platformy Azure, który następnie wdraża usługę Logic Apps do sieci wirtualnej. Podczas tworzenia konta aplikacji lub Integracja z usługą Logic Apps, wybierz ten ISE ich lokalizacji. Twoje konto aplikacji lub Integracja z usługą Logic Apps mogą uzyskiwać bezpośrednio dostęp do zasobów, takich jak maszyny wirtualne (VM), serwery, systemów i usług w Twojej sieci wirtualnej. 

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

W tym artykule przedstawiono sposób wykonania następujących zadań:

* Umożliwia skonfigurowanie uprawnień w sieci wirtualnej platformy Azure, aby wystąpieniem prywatnym Logic Apps może uzyskiwać dostęp do sieci wirtualnej.

* Tworzenie środowiska integration service environment (ISE). 

* Tworzenie aplikacji logiki, które mogą być uruchamiane w swoje środowiska ISE. 

* Tworzenie konta integracji aplikacji logiki w swoje środowiska ISE.

Aby uzyskać więcej informacji na temat środowisk usługi integracji, zobacz [dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* [Sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli nie masz sieci wirtualnej, Dowiedz się, jak [tworzenie sieci wirtualnej platformy Azure](../virtual-network/quick-create-portal.md). 

* Zostać zapewniony bezpośredni dostęp do sieci wirtualnej platformy Azure, aplikacje logiki [skonfigurowanie uprawnień kontroli dostępu opartej na rolach (RBAC)](#vnet-access) dzięki usłudze Logic Apps ma uprawnienia do uzyskiwania dostępu do sieci wirtualnej. 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Ustaw uprawnienia do sieci wirtualnej

Podczas tworzenia środowiska usług integracji (ISE) wybierzesz siecią wirtualną platformy Azure, gdzie możesz *wstrzyknąć* środowiska. Jednak aby można było wybrać sieć wirtualną wstrzykiwania środowiska, można ustawić uprawnień kontroli dostępu opartej na rolach (RBAC) w sieci wirtualnej. Aby skonfigurować uprawnienia, Przypisz te określone role w usłudze Azure Logic Apps:

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz sieć wirtualną. 

1. Sieci wirtualnej menu wybierz kolejno pozycje **kontrola dostępu (IAM)**. 

1. W obszarze **kontrola dostępu (IAM)**, wybierz **Dodaj**. 

   ![Dodawanie ról](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Na **Dodaj przypisanie roli** okienku Konfigurowanie każdej roli dla usługi Azure Logic Apps, zgodnie z opisem w tabeli, w tym kroku. Upewnij się, że wybrano **Zapisz** po zakończeniu każdej roli.

   ![Dodaj przypisanie roli](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   | Rola | Przypisz dostęp do | Wybierz pozycję | 
   |------|------------------|--------|
   | **Współautor sieci** | **Usługa Azure AD użytkownika, grupy lub aplikacji** | Wprowadź **usługi Azure Logic Apps**. Po wyświetleniu listy elementów członkowskich, wybierz tę samą wartość. <p>**Porada**: Jeśli nie możesz znaleźć tę usługę, wprowadź identyfikator aplikacji usługi Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Współautor klasycznej** | **Usługa Azure AD użytkownika, grupy lub aplikacji** | Wprowadź **usługi Azure Logic Apps**. Po wyświetleniu listy elementów członkowskich, wybierz tę samą wartość. <p>**Porada**: Jeśli nie możesz znaleźć tę usługę, wprowadź identyfikator aplikacji usługi Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

Aby uzyskać więcej informacji, zobacz [uprawnienia dostępu do sieci wirtualnej](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Utwórz swoje środowiska ISE

Aby utworzyć środowiska integration service environment (ISE), wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **Utwórz zasób**.

   ![Tworzenie nowego zasobu](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. W polu wyszukiwania wprowadź "środowisko usługi integracji" jako filtr.
Wybierz z listy wyników **środowisko usługi integracji (wersja zapoznawcza)**, a następnie wybierz **Utwórz**.

   ![Wybierz pozycję "Środowisko integracji usługi"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Wybierz pozycję "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Podaj te szczegóły dla danego środowiska, a następnie wybierz **przeglądanie + tworzenie**, na przykład:

   ![Podaj szczegóły środowiska](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Właściwość | Wymagane | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Yes | <*Azure-subscription-name*> | Subskrypcji platformy Azure do użycia w danym środowisku | 
   | **Grupa zasobów** | Yes | <*Azure-resource nazwa_grupy*> | Grupa zasobów platformy Azure, w którym chcesz utworzyć swoje środowisko |
   | **Nazwa środowiska usługi integracji** | Yes | <*Nazwa środowiska*> | Nazwa do nadania środowiska | 
   | **Lokalizacja** | Yes | <*Region platformy Azure — centrum danych*> | Region centrum danych platformy Azure miejsca wdrożenia środowiska | 
   | **Dyspozycyjność** | Yes | 0, 1, 2, 3 | Liczba jednostek przetwarzania do użycia dla tego zasobu środowiska ISE | 
   | **Sieć wirtualna** | Yes | <*Azure — — nazwa sieci wirtualnej —*> | Azure sieci wirtualnej, której chcesz wstawić środowiska, dzięki czemu aplikacje logiki w tym środowisku mają dostęp do sieci wirtualnej. Jeśli nie masz sieci, można utworzyć jeden tutaj. <p>**Ważne**: możesz *tylko* wykonywać takie działanie, podczas tworzenia usługi ISE. Jednakże przed utworzeniem tej relacji, upewnij się, że już [Konfigurowanie kontroli dostępu opartej na rolach w Twojej sieci wirtualnej dla usługi Azure Logic Apps](#vnet-access). | 
   | **Podsieci** | Yes | <*Zakres adresów IP*> | ISE wymaga czterech *pusty* podsieci, które nie mają delegowania do dowolnej usługi i są używane do tworzenia zasobów w danym środowisku. Każda podsieć musi spełniać następujące kryteria: <p>— Wykorzystanie [formatu Bezklasowego routingu międzydomenowego (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). <br>— Wymagają przestrzeń adresów klasy B. <br>-Ma nazwę, która nie zaczyna się od numeru lub łącznik. <br>-Zawiera `/27`, na przykład w każdej podsieci, w tym miejscu określa zakres 32-bitowy adres: `10.0.0.0/27`, `10.0.0.32/27`, `10.0.0.64/27`, i `10.0.0.96/27`. <br>— Nie może istnieć w tym samym zakresie adresów dla wybranej sieci wirtualnej ani żadnych innych prywatnych adresów IP gdzie sieć wirtualna jest połączona. <br>-Musi być pusta. <p><p>**Ważne**: możesz *nie można zmienić* te zakresy adresów IP po utworzeniu środowiska. |
   |||||

1. Po Azure weryfikuje pomyślnie informacji ISE, wybierz **Utwórz**, na przykład:

   ![Po pomyślnej weryfikacji wybierz polecenie "Utwórz"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Wdrażanie środowiska Start Azure, ale ten proces może potrwać *do dwóch godzin* przed zakończeniem. 
   Aby sprawdzić stan wdrożenia, na pasku narzędzi usługi Azure wybierz ikonę powiadomienia, który zostanie otwarty w okienku powiadomienia.

   ![Sprawdź stan wdrożenia](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Po pomyślnym zakończeniu wdrożenia platformy Azure zostaną wyświetlone to powiadomienie:

   ![Wdrażanie zakończyło się pomyślnie](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Aby wyświetlić swoje środowisko, wybierz opcję **przejdź do zasobu** Jeśli Azure nie automatyczne przejście do środowiska, po zakończeniu wdrożenia.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Tworzenie aplikacji logiki — środowiska ISE

Aby tworzyć aplikacje logiki, które używają środowiska integration service environment (ISE), wykonaj kroki opisane w [jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) jednak z następującymi ograniczeniami: 

* Podczas tworzenia aplikacji logiki wybierz swoje ISE, a nie region platformy Azure, z **lokalizacji** listy z **środowiska usług integracji** sekcji, na przykład:

  ![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Można użyć tych samych wbudowanych wyzwalaczy i akcji, takich jak HTTP, które działają w tej samej platformy ISE jako aplikację logiki. Łączniki z usługą **ISE** również etykietę uruchomienia w tym samym środowisku ISE jako aplikację logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety.

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Po należy wstrzyknąć Twojego środowiska ISE w sieci wirtualnej platformy Azure, logic apps w swoje środowiska ISE można uzyskać dostęp do zasobów w tej sieci wirtualnej. Systemów lokalnych w sieci wirtualnej, która jest połączona z ISE aplikacje logiki można uzyskać dostęp do tych systemów, przy użyciu dowolnego z tych elementów: 

  * Łącznik platformy ISE dla tego systemu, na przykład SQL Server
  * Akcja HTTP 
  * Łącznik niestandardowy

  Dla systemów lokalnych, które nie znajdują się w sieci wirtualnej lub nie masz łączniki ISE, najpierw [konfigurowania i używania lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Utwórz konto integracji — środowiska ISE

Aby użyć konta integracji z usługą logic apps w środowisku usługi integracji (ISE), należy użyć tego konta integracji *tym samym środowisku* co usługa logic apps. Logic apps w środowisku ISE można odwoływać się tylko konta integracji w tym samym środowisku ISE. 

Aby utworzyć konto integracji, która używa środowiska ISE, czynności zwykle w [sposób tworzenia kont integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z wyjątkiem **lokalizacji** właściwość, która teraz wyświetla Twoje ISEs w obszarze  **Środowiska usług integracji** wraz z dostępnych regionów. Wybierz swoje ISE, a nie region, na przykład:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="https://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
