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
ms.date: 12/03/2018
ms.openlocfilehash: 8ad4c356c5826532b94721bc4d9071179e8bd93a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846698"
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

1. W obszarze **kontrola dostępu (IAM)**, wybierz **Dodaj przypisanie roli**. 

   ![Dodawanie ról](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Na **Dodaj przypisanie roli** okienko, Dodaj wymagane role w usłudze Azure Logic Apps, zgodnie z opisem. 

   1. W obszarze **roli**, wybierz opcję **Współautor sieci**. 
   
   1. W obszarze **Przypisz dostęp do**, wybierz opcję **użytkownika usługi Azure AD, grupa lub aplikacja**.

   1. W obszarze **wybierz**, wprowadź **usługi Azure Logic Apps**. 

   1. Po wyświetleniu listy elementów członkowskich, wybierz **usługi Azure Logic Apps**. 

      > [!TIP]
      > Jeśli nie możesz znaleźć tę usługę, wprowadź identyfikator aplikacji usługi Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` 
   
   1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   Na przykład:

   ![Dodaj przypisanie roli](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

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
   | **Podsieci** | Yes | <*Zakres adresów IP*> | ISE wymaga czterech *pusty* podsieci. Te podsieci są undelegated do dowolnej usługi i są używane do tworzenia zasobów w danym środowisku. Możesz *nie można zmienić* te zakresy adresów IP po utworzeniu środowiska. <p><p>Do tworzenia każdej podsieci [wykonaj czynności opisane w tej tabeli](#create-subnet). Każda podsieć musi spełniać następujące kryteria: <p>— Nie może istnieć w tym samym zakresie adresów dla wybranej sieci wirtualnej ani żadnych innych prywatnych adresów IP gdzie sieć wirtualna jest połączona. <br>-Używa nazwy, która nie zaczyna się od numeru lub łącznik. <br>— Wykorzystanie [formatu Bezklasowego routingu międzydomenowego (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). <br>— Wymagają przestrzeń adresów klasy B. <br>-Zawiera `/27`. Na przykład w każdej podsieci, w tym miejscu określa zakres 32-bitowy adres: `10.0.0.0/27`, `10.0.0.32/27`, `10.0.0.64/27`, i `10.0.0.96/27`. <br>-Musi być pusta. |
   |||||

   <a name="create-subnet"></a>

   **Tworzenie podsieci**

   1. W obszarze **podsieci** wybierz **konfigurację podsieci Zarządzaj**.

      ![Zarządzanie konfiguracją podsieci](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Na **podsieci** okienku wybierz **podsieci**.

      ![Dodawanie podsieci](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Na **Dodaj podsieć** okienku, podaj te informacje.

      * **Nazwa**: Nazwa podsieci
      * **Zakres adresów (blok CIDR)**: zakres swoje podsieci w sieci wirtualnej i w formacie CIDR

      ![Dodaj szczegóły podsieci](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   1. Powtórz te czynności dla trzech więcej podsieci.

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

* Podczas tworzenia aplikacji logiki, w obszarze **lokalizacji** właściwości, wybierz swoje ISE z **środowiska usług integracji** sekcji, na przykład:

  ![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Można użyć tych samych wbudowanych wyzwalaczy i akcji, takich jak HTTP, które działają w tej samej platformy ISE jako aplikację logiki. Łączniki z usługą **ISE** również etykietę uruchomienia w tym samym środowisku ISE jako aplikację logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety.

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Po należy wstrzyknąć Twojego środowiska ISE w sieci wirtualnej platformy Azure, logic apps w swoje środowiska ISE można uzyskać dostęp do zasobów w tej sieci wirtualnej. Dla systemów lokalnych, które są podłączone do sieci wirtualnej należy wstrzyknąć środowiska ISE do tej sieci, dzięki czemu aplikacje logiki można uzyskać dostęp do tych systemów przy użyciu dowolnego z tych elementów: 

  * Łącznik platformy ISE dla tego systemu, na przykład SQL Server
  
  * Akcja HTTP 
  
  * Łącznik niestandardowy

  Dla systemów lokalnych, które nie znajdują się w sieci wirtualnej lub nie masz łączniki ISE, najpierw [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Utwórz konto integracji — środowiska ISE

Aby użyć konta integracji z usługą logic apps w środowisku usługi integracji (ISE), należy użyć tego konta integracji *tym samym środowisku* co usługa logic apps. Logic apps w środowisku ISE można odwoływać się tylko konta integracji w tym samym środowisku ISE. 

Aby utworzyć konto integracji, która używa środowiska ISE, wykonaj kroki opisane w [sposób tworzenia kont integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z wyjątkiem **lokalizacji** właściwość gdzie **środowiska usług integracji**  pojawi się w sekcji. Zamiast tego wybierz swoje ISE, a nie region, na przykład:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="https://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
