---
title: Zarządzanie środowiskami usługi integracji w Azure Logic Apps
description: Sprawdź kondycję sieci i Zarządzaj aplikacjami logiki, połączeniami, łącznikami niestandardowymi i kontami integracji w środowisku usługi integracji (ISE) dla Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517464"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Zarządzanie środowiskiem usługi integracji (ISE) w Azure Logic Apps

Aby sprawdzić kondycję sieci dla [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i zarządzać aplikacjami logiki, połączeniami, kontami integracji i łącznikami niestandardowymi, które istnieją w ISE, wykonaj kroki opisane w tym temacie.

## <a name="view-your-ise"></a>Wyświetlanie ISE

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

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

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Aplikacje logiki**.

   ![Znajdowanie aplikacji logiki](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Aby usunąć aplikacje logiki z usługi ISE, gdy nie są już potrzebne, wybierz te aplikacje logiki, a następnie wybierz pozycję **Usuń**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Zarządzanie połączeniami interfejsu API

1. Aby wyświetlić połączenia interfejsu API, które zostały utworzone przez aplikacje logiki uruchomione w ISE, w menu ISE w obszarze **Ustawienia**wybierz pozycję **połączenia interfejsu API**.

   ![Znajdowanie połączeń interfejsu API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Aby usunąć połączenia z usługi ISE, gdy nie są już potrzebne, wybierz te połączenia, a następnie wybierz pozycję **Usuń**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Zarządzanie łącznikami niestandardowymi

1. Aby wyświetlić łączniki niestandardowe, które zostały utworzone w ISE, w menu ISE w obszarze **Ustawienia**wybierz pozycję **Łączniki niestandardowe**.

   ![Znajdowanie łączników niestandardowych](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Aby usunąć łączniki niestandardowe z ISE, gdy nie są już potrzebne, zaznacz te łączniki, a następnie wybierz pozycję **Usuń**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Zarządzanie kontami integracji

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **konta integracji**.

   ![Znajdowanie kont integracji](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Aby usunąć konta integracji z usługi ISE, gdy nie są już potrzebne, wybierz te konta integracji, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [nawiązać połączenie z sieciami wirtualnymi platformy Azure z izolowanych aplikacji logiki](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
