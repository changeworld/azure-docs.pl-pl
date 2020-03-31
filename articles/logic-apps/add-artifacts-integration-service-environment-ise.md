---
title: Dodawanie zasobów do środowisk usług integracyjnych
description: Dodawanie aplikacji logiki, kont integracji, łączników niestandardowych i łączników zarządzanych do środowiska usługi integracji (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164880"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Dodawanie zasobów do środowiska usługi integracji (ISE) w usłudze Azure Logic Apps

Po utworzeniu [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)dodaj zasoby, takie jak aplikacje logiki, konta integracji i łączniki, aby mogły uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Na przykład zarządzane łączniki ISE, które stają się dostępne po utworzeniu ise nie są automatycznie wyświetlane w Logic App Designer. Przed użyciem tych łączników ISE należy ręcznie [dodać i wdrożyć te łączniki do systemu ISE, tak aby](#add-ise-connectors-environment) były wyświetlane w projektancie aplikacji logiki.

> [!IMPORTANT]
> W przypadku aplikacji logiki i kont integracji do współpracy w ise, oba muszą używać *tego samego ise* jako ich lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Ise, który został utworzony w celu uruchomienia aplikacji logiki. Jeśli nie masz ise, [należy najpierw utworzyć ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Aby utworzyć, dodać lub zaktualizować zasoby, które są wdrażane w firmie ISE, należy przypisać rolę właściciela lub współautora na tej ise lub masz uprawnienia dziedziczone za pośrednictwem subskrypcji platformy Azure lub grupy zasobów platformy Azure skojarzonej z ISE. Dla osób, które nie mają uprawnień właściciela, współautora lub dziedziczone, można przypisać rolę współautora środowiska usługi integracji lub środowiska środowiska integracji roli dewelopera. Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC), zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure?](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Tworzenie aplikacji logiki

Aby utworzyć aplikacje logiki, które są uruchamiane w środowisku usługi integracji (ISE), wykonaj następujące kroki:

1. Znajdź i otwórz ise, jeśli nie jest jeszcze otwarty. Z menu ISE w obszarze **Ustawienia**wybierz pozycję **Aplikacje** > logiki**Dodaj**.

   ![Dodawanie nowej aplikacji logiki do ise](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Podaj informacje o aplikacji logiki, którą chcesz utworzyć, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa aplikacji logiki do utworzenia |
   | **Subskrypcja** | Tak | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | Tak | Nazwa grupy zasobów platformy Azure (nowej lub istniejącej) do użycia |
   | **Lokalizacja** | Tak | W obszarze **Środowiska usług integracji**wybierz środowisko ISE do użycia, jeśli nie jest jeszcze zaznaczone. <p><p> **Ważne:** Aby korzystać z aplikacji logiki z kontem integracji, oba muszą używać tego samego ISE. |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Kontynuuj [tworzenie aplikacji logiki w zwykły sposób](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Aby uzyskać różnice w działaniu wyzwalaczy i akcji oraz sposobu ich etykietowania podczas korzystania z usługi ISE w porównaniu z usługą aplikacji logiki z wieloma dzierżawami, zobacz [Izolowane i wielodostępne w przeglądzie ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Aby zarządzać aplikacjami logiki i połączeniami interfejsu API w środowisku ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Tworzenie kont integracji

Na podstawie jednostki [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) wybranej podczas tworzenia program ISE zawiera określone użycie konta integracji bez dodatkowych kosztów. Aplikacje logiki, które istnieją w środowisku usługi integracji (ISE) można odwoływać się tylko kont integracji, które istnieją w tym samym środowisku ISE. Tak, aby konto integracji do pracy z aplikacjami logiki w środowisku ISE, zarówno konta integracji i aplikacji logiki musi używać *tego samego środowiska,* co ich lokalizacji. Aby uzyskać więcej informacji na temat kont integracji i ise, zobacz [Konta integracji z ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Aby utworzyć konto integracji korzystające z narzędzia ISE, wykonaj następujące kroki:

1. Znajdź i otwórz ise, jeśli nie jest jeszcze otwarty. Z menu ISE w obszarze **Ustawienia**wybierz pozycję **Konta** > integracji**Dodaj**.

   ![Dodawanie nowego konta integracji do ise](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Podaj informacje o aplikacji logiki, którą chcesz utworzyć, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa konta integracji, które chcesz utworzyć |
   | **Subskrypcja** | Tak | Nazwa subskrypcji platformy Azure, której chcesz użyć |
   | **Grupa zasobów** | Tak | Nazwa grupy zasobów platformy Azure (nowej lub istniejącej) do użycia |
   | **Warstwa cenowa** | Tak | Warstwa cenowa używana dla konta integracji |
   | **Lokalizacja** | Tak | W obszarze **Środowiska usług integracji**wybierz tę samą środowisko ISE, której używają aplikacje logiki, jeśli nie została już wybrana. <p><p> **Ważne:** Aby korzystać z konta integracji z aplikacjami logiki, oba muszą używać tego samego ISE. |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. [Połącz aplikację logiki z kontem integracji w zwykły sposób](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Kontynuuj, dodając zasoby do swojego konta integracyjnego, takie jak [partnerzy handlowi](../logic-apps/logic-apps-enterprise-integration-partners.md) i [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Aby zarządzać kontami integracji w środowisku ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Dodawanie łączników ISE

Łączniki zarządzane przez firmę Microsoft, które stają się dostępne po utworzeniu ise nie są automatycznie wyświetlane w selektorze łączników w logice Projektanta aplikacji. Przed użyciem tych łączników ISE należy ręcznie dodać i wdrożyć te łączniki do systemu ISE, tak aby były wyświetlane w projektancie aplikacji logiki.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Zarządzane łączniki**. Na pasku narzędzi wybierz pozycję **Dodaj**.

   ![Wyświetlanie łączników zarządzanych](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. W okienku **Dodawanie nowego zarządzanego łącznika** otwórz listę **Znajdź łącznik.** Wybierz łącznik ISE, którego chcesz użyć, ale nie jest jeszcze wdrożony w użynie sieci ISE. Wybierz **pozycję Utwórz**.

   ![Wybierz łącznik ISE, który chcesz wdrożyć w ise](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Tylko łączniki ISE, które kwalifikują się, ale nie zostały jeszcze wdrożone w ise są dostępne do wyboru. Łączniki, które są już wdrożone w użymie są wyświetlane jako niedostępne do wyboru.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Tworzenie łączników niestandardowych

Aby użyć łączników niestandardowych w ise, należy utworzyć te łączniki niestandardowe bezpośrednio wewnątrz ise.

1. Znajdź i otwórz ise, jeśli nie jest jeszcze otwarty. Z menu ISE w obszarze **Ustawienia**wybierz pozycję **Niestandardowe łączniki** > **Dodaj**.

   ![Tworzenie łącznika niestandardowego](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Podaj nazwę, subskrypcję platformy Azure i grupę zasobów platformy Azure (nową lub istniejącą) do użycia dla łącznika niestandardowego.

1. Z listy **Lokalizacja** w sekcji **Środowiska usług integracji** wybierz tę samą środowisko ISE, z której korzystają aplikacje logiki, a następnie wybierz pozycję **Utwórz**, na przykład:

   ![Wybierz środowisko usługi integracji](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Wybierz nowy łącznik niestandardowy, a następnie wybierz pozycję **Edytuj**, na przykład:

   ![Zaznaczanie i edytowanie łącznika niestandardowego](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Kontynuuj, tworząc łącznik w zwykły sposób z [definicji OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) lub [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Aby zarządzać łącznikami niestandardowymi w środowisku ISE, zobacz [Zarządzanie środowiskiem usługi integracji](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md)
