---
title: Zarządzanie środowiskami usług integracji w usłudze Azure Logic Apps
description: Sprawdzanie kondycji sieci i zarządzanie aplikacjami logicznymi, połączeniami, łącznikami niestandardowymi i kontami integracji w środowisku usługi integracji (ISE) dla usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284202"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Zarządzanie środowiskiem usługi integracji (ISE) w usłudze Azure Logic Apps

W tym artykule pokazano, jak wykonywać zadania zarządzania dla [środowiska usługi integracji (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)na przykład:

* Zarządzanie zasobami, takimi jak aplikacje logiki, połączenia, konta integracji i łączniki w użyranie sieciOWEJ.
* Sprawdź kondycję sieci ISE.
* Dodaj pojemność, uruchom ponownie ise lub usuń ise, wykonaj kroki opisane w tym temacie. Aby dodać te artefakty do środowiska ISE, zobacz [Dodawanie artefaktów do środowiska usługi integracji.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

## <a name="view-your-ise"></a>Wyświetlanie ise

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W polu wyszukiwania portalu wprowadź "środowiska usług integracji", a następnie wybierz pozycję **Środowiska usług integracji**.

   ![Znajdowanie środowisk usług integracyjnych](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Z listy wyników wybierz środowisko usługi integracji.

   ![Wybierz środowisko usługi integracji](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Przejdź do następnych sekcji, aby znaleźć aplikacje logiki, połączenia, łączniki lub konta integracji w ise.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Sprawdzanie kondycji sieci

W menu ISE w obszarze **Ustawienia**wybierz pozycję **Kondycja sieci**. W tym okienku jest wyświetlany stan kondycji podsieci i zależności wychodzące od innych usług.

![Sprawdzanie kondycji sieci](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Zarządzanie aplikacjami logiki

Można wyświetlać aplikacje logiki, które znajdują się w twoim dysku ISE i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Aplikacje logiki**.

   ![Wyświetlanie aplikacji logiki](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Aby usunąć aplikacje logiki, które nie są już potrzebne w użymie, wybierz te aplikacje logiki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **Tak**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Zarządzanie połączeniami interfejsu API

Można wyświetlać połączenia utworzone przez aplikacje logiki uruchomione w użymie i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Połączenia interfejsu API**.

   ![Wyświetlanie połączeń interfejsu API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Aby usunąć połączenia, które nie są już potrzebne w użymie, zaznacz te połączenia, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **Tak**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Zarządzanie złączami ISE

Można wyświetlać łączniki interfejsu API i zarządzać nimi, które są wdrażane w interfejsie ISE.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Zarządzane łączniki**.

   ![Wyświetlanie łączników zarządzanych](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Aby usunąć łączniki, które nie mają być dostępne w użymie, zaznacz te łączniki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **Tak**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Zarządzanie łącznikami niestandardowymi

Można wyświetlać łączniki niestandardowe wdrożone w użymie i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Łączniki niestandardowe**.

   ![Znajdowanie łączników niestandardowych](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Aby usunąć łączniki niestandardowe, które nie są już potrzebne w użymie, zaznacz te łączniki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **Tak**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Zarządzanie kontami integracji

1. W menu ISE w obszarze **Ustawienia**wybierz pozycję **Konta integracji**.

   ![Znajdowanie kont integracyjnych](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Aby usunąć konta integracji z ise, gdy nie są już potrzebne, wybierz te konta integracji, a następnie wybierz pozycję **Usuń**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Dodawanie pojemności środowiska ISE

Jednostka bazowa Premium ISE ma stałą pojemność, więc jeśli potrzebujesz większej przepustowości, możesz dodać więcej jednostek skalowania, zarówno podczas tworzenia, jak i później. Jednostka SKU dewelopera nie zawiera możliwości dodawania jednostek skalowania.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do witryny ISE.

1. Aby przejrzeć dane dotyczące użycia i wydajności dla ise, w menu ISE wybierz **przegląd**.

   ![Wyświetlanie użycia dla ise](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. W obszarze **Ustawienia**wybierz pozycję **Skaluj w poziomie**. W okienku **Konfigurowanie** wybierz jedną z następujących opcji:

   * [**Skala ręczna:**](#manual-scale)Skaluj na podstawie liczby jednostek przetwarzania, których chcesz użyć.
   * [**Niestandardowe skalowanie automatyczne:**](#custom-autoscale)Skaluj na podstawie metryk wydajności, wybierając spośród różnych kryteriów i określając warunki progowe dla spełnienia tych kryteriów.

   ![Wybierz odpowiedni typ skalowania](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Skala ręczna

1. Po **wybraniu opcji Ręczne skalowanie**, dla **dodatkowej pojemności**wybierz liczbę jednostek skalowania, których chcesz użyć.

   ![Wybierz odpowiedni typ skalowania](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Niestandardowe skalowanie automatyczne

1. Po **wybraniu niestandardowego skalowania automatycznego**, dla **nazwy ustawienia skalowania automatycznego**, podaj nazwę ustawienia i opcjonalnie wybierz grupę zasobów platformy Azure, do której należy to ustawienie.

   ![Podaj nazwę ustawienia skalowania automatycznego i wybierz grupę zasobów](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. W przypadku warunku **Domyślne** wybierz opcję **Skaluj na podstawie metryki** lub **Skaluj do określonej liczby wystąpień**.

   * Jeśli wybierzesz oparte na wystąpieniu, wprowadź numer dla jednostek przetwarzania, który jest wartością od 0 do 10.

   * Jeśli wybierzesz oparte na metrykach, wykonaj następujące kroki:

     1. W sekcji **Reguły** wybierz pozycję **Dodaj regułę**.

     1. W okienku **reguły skalowania** skonfiguruj kryteria i akcję, które należy podjąć podczas wyzwalania reguły.

     1. Dla **limitów instancji,** określ następujące wartości:

        * **Minimum**: Minimalna liczba jednostek przetwórczych do użycia
        * **Maksymalna**: Maksymalna liczba jednostek przetwarzania do użycia
        * **Domyślnie:** Jeśli podczas odczytywania metryk zasobów występują jakiekolwiek problemy, a bieżąca pojemność jest niższa niż domyślna pojemność, skalowanie automatyczne jest skalowane w poziomie do domyślnej liczby jednostek przetwarzania. Jeśli jednak bieżąca pojemność przekracza domyślną pojemność, skalowanie automatyczne nie jest skalowane.

1. Aby dodać inny warunek, wybierz **pozycję Dodaj warunek skali**.

1. Po zakończeniu pracy z ustawieniami skalowania automatycznego zapisz zmiany.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Ponowne uruchamianie ise

Jeśli zmienisz ustawienia serwera DNS lub serwera DNS, musisz ponownie uruchomić środowiska ISE, aby środowiska ISE mogły je odebrać. Ponowne uruchomienie usługi SKU w wersji Premium nie powoduje przestojów spowodowanych nadmiarowością i składnikami, które są ponownie uruchamiane po jednym naraz podczas recyklingu. Jednak ise ise sku dewelopera występuje przestojów, ponieważ nie istnieje nadmiarowość. Aby uzyskać więcej informacji, zobacz [jednostki SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do witryny ISE.

1. W menu ISE wybierz polecenie **Przegląd**. Na pasku narzędzi Przegląd **uruchom ponownie**plik .

   ![Uruchom ponownie środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Usuwanie środowiska ISE

Przed usunięciem środowiska ISE, który nie jest już potrzebny, lub grupy zasobów platformy Azure, która zawiera środowisko ISE, sprawdź, czy nie masz żadnych zasad lub blokad w grupie zasobów platformy Azure, która zawiera te zasoby lub w sieci wirtualnej platformy Azure, ponieważ te elementy mogą blokować usuwanie.

Po usunięciu środowiska ISE może być poczekaj do 9 godzin przed próbą usunięcia sieci wirtualnej lub podsieci platformy Azure.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usług integracyjnych](../logic-apps/add-artifacts-integration-service-environment-ise.md)