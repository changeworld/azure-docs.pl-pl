---
title: Dodaj artefakty do środowiska usługi integracji — Azure Logic Apps
description: Dodawanie aplikacji logiki, kont integracji i łączników niestandardowych do środowiska usługi integracji (ISE) w celu uzyskania dostępu do sieci wirtualnych platformy Azure (sieci wirtualnych)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 266c942adb62b187d39d2eac4a47455482bfadaa
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680526"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Dodaj artefakty do środowiska usługi integracji (ISE) w Azure Logic Apps

Po utworzeniu [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Dodaj artefakty, takie jak aplikacje logiki, konta integracji i łączniki, aby mogły uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* ISE, który został utworzony w celu uruchomienia aplikacji logiki. Jeśli nie masz elementu ISE, [najpierw utwórz ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Tworzenie aplikacji logiki

Aby skompilować Aplikacje logiki, które działają w środowisku usługi integracji (ISE), wykonaj następujące kroki:

1. Znajdź i Otwórz ISE, jeśli nie jest jeszcze otwarty. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Aplikacje logiki**  > **Dodaj**.

   ![Dodaj nową aplikację logiki do ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   — lub —

   W głównym menu platformy Azure wybierz pozycję **Utwórz zasób** > **integracja** > **aplikacji logiki**.

1. Podaj nazwę, subskrypcję platformy Azure i grupę zasobów platformy Azure (nową lub istniejącą) do użycia w aplikacji logiki.

1. Z listy **Lokalizacja** w sekcji **środowiska usługi integracji** wybierz swój ISE, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Jeśli chcesz używać aplikacji logiki z kontem integracji, te aplikacje logiki i konto integracji muszą używać tego samego ISE.

1. Kontynuuj [Tworzenie aplikacji logiki w zwykły sposób](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   W przypadku różnic między działami wyzwalaczami i działaniami oraz sposobie ich etykietowania w przypadku używania ISE w porównaniu z globalną usługą Logic Apps, zobacz [izolowany a globalny w przeglądzie ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Aby zarządzać aplikacjami logiki i połączeniami interfejsu API w ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Tworzenie kont integracji

W oparciu o [ISEą jednostkę SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) wybraną podczas tworzenia ISE obejmuje specyficzne użycie konta integracji bez dodatkowych kosztów. Aplikacje logiki, które istnieją w środowisku usługi integracji (ISE), mogą odwoływać się tylko do kont integracji, które istnieją w tym samym ISE. Aby konto integracji działało z usługą Logic Apps w ISE, zarówno konto integracji, jak i Aplikacje logiki muszą używać tego *samego środowiska* , co ich lokalizacja. Aby uzyskać więcej informacji na temat kont integracji i ISEs, zobacz [konta integracji z ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Aby utworzyć konto integracji korzystające z ISE, wykonaj następujące kroki:

1. Znajdź i Otwórz ISE, jeśli nie jest jeszcze otwarty. W menu ISE w obszarze **Ustawienia**wybierz pozycję **konta integracji**  > **Dodaj**.

   ![Dodaj nowe konto integracji do ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   — lub —

   W głównym menu platformy Azure wybierz pozycję **Utwórz zasób**  > **integracji**  > **konto integracji**.

1. Podaj nazwę, subskrypcję platformy Azure, grupę zasobów platformy Azure (nową lub istniejącą) i warstwę cenową, która ma być używana dla konta integracji.

1. Z listy **Lokalizacja** w sekcji **środowiska usługi integracji** wybierz tę samą ISEę, która jest używana przez aplikacje logiki, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Połącz aplikację logiki z kontem integracji w zwykły sposób](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Kontynuuj dodawanie artefaktów do konta integracji, takiego jak [partnerzy handlowi](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Aby zarządzać kontami integracji w ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Dodawanie łączników ISE

Możesz dodać łączniki zarządzane przez firmę Microsoft, które są dostępne do użycia w ISE, ale nie są wdrożone w ISE.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Łączniki zarządzane**. Na pasku narzędzi wybierz pozycję **Dodaj**.

   ![Wyświetlanie łączników zarządzanych](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. W okienku **Dodaj nowe łączniki zarządzane** Otwórz listę **Znajdź łącznik** . Jeśli potrzebny łącznik jest dostępny, wybierz ten łącznik, a następnie wybierz pozycję **Utwórz**.

   Na liście są wyświetlane tylko te łączniki, które są uprawnione, ale nie są wdrażane w ISE. Łączniki, które są już wdrożone w ISE, są niedostępne do wyboru.

   ![Wybierz kwalifikujący się łącznik](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Tworzenie łączników niestandardowych

Aby używać łączników niestandardowych w ISE, Utwórz te łączniki niestandardowe bezpośrednio wewnątrz ISE.

1. Znajdź i Otwórz ISE, jeśli nie jest jeszcze otwarty. Z menu ISE w obszarze **Ustawienia**wybierz pozycję **łączniki niestandardowe**  > **Dodaj**.

   ![Tworzenie łącznika niestandardowego](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Podaj nazwę, subskrypcję platformy Azure i grupę zasobów platformy Azure (nową lub istniejącą) do użycia w łączniku niestandardowym.

1. Z listy **Lokalizacja** w sekcji **środowiska usługi integracji** wybierz tę samą ISEę, w której używane są aplikacje logiki, a następnie wybierz pozycję **Utwórz**, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Wybierz nowy łącznik niestandardowy, a następnie wybierz pozycję **Edytuj**, na przykład:

   ![Wybieranie i edytowanie łącznika niestandardowego](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Kontynuuj, tworząc łącznik w zwykły sposób z [definicji openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) lub [protokołu SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Aby zarządzać łącznikami niestandardowymi w ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzaj środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md)
