---
title: Zarządzanie środowiskami usługi integracji w Azure Logic Apps
description: Sprawdź kondycję sieci i Zarządzaj aplikacjami logiki, połączeniami, łącznikami niestandardowymi i kontami integracji w środowisku usługi integracji (ISE) dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358788"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Zarządzanie środowiskiem usługi integracji (ISE) w Azure Logic Apps

Aby sprawdzić kondycję sieci dla [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i zarządzać aplikacjami logiki, połączeniami, kontami integracji i łącznikami istniejącymi w ISE, wykonaj kroki opisane w tym temacie. Aby dodać te artefakty do ISE, zobacz [Dodawanie artefaktów do środowiska usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Wyświetlanie ISE

1. Zaloguj się do [Azure portal](https://portal.azure.com).

1. W polu wyszukiwania portalu wprowadź "środowiska usługi integracji", a następnie wybierz pozycję **środowiska usługi integracji**.

   ![Znajdź środowiska usługi integracji](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Z listy wyników wybierz środowisko usługi integracji.

   ![Wybierz środowisko usługi integracji](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Przejdź do następnych sekcji, aby znaleźć aplikacje logiki, połączenia, łączniki lub konta integracji w ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Sprawdź kondycję sieci

W menu ISE w obszarze **Ustawienia**wybierz pozycję **kondycja sieci**. W tym okienku jest wyświetlany stan kondycji podsieci i zależności wychodzące z innych usług.

![Sprawdź kondycję sieci](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Zarządzanie aplikacjami logiki

Aplikacje logiki można wyświetlać i zarządzać nimi, które znajdują się w ISE.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Aplikacje logiki**.

   ![Wyświetlanie aplikacji logiki](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Aby usunąć aplikacje logiki, które nie są już potrzebne w ISE, wybierz te aplikacje logiki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Zarządzanie połączeniami interfejsu API

Można wyświetlać i zarządzać połączeniami utworzonymi przez aplikacje logiki działające w ISE.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **połączenia interfejsu API**.

   ![Wyświetlanie połączeń interfejsu API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Aby usunąć połączenia, które nie są już potrzebne w ISE, wybierz te połączenia, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Zarządzanie łącznikami ISE

Można wyświetlać łączniki interfejsu API wdrożone w ISE i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Łączniki zarządzane**.

   ![Wyświetlanie łączników zarządzanych](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Aby usunąć łączniki, które nie mają być dostępne w ISE, wybierz te łączniki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Zarządzanie łącznikami niestandardowymi

Można wyświetlać łączniki niestandardowe wdrożone w ISE i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Łączniki niestandardowe**.

   ![Znajdowanie łączników niestandardowych](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Aby usunąć łączniki niestandardowe, które nie są już potrzebne w ISE, wybierz te łączniki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Zarządzanie kontami integracji

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **konta integracji**.

   ![Znajdowanie kont integracji](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Aby usunąć konta integracji z usługi ISE, gdy nie są już potrzebne, wybierz te konta integracji, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [nawiązać połączenie z sieciami wirtualnymi platformy Azure z izolowanych aplikacji logiki](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
