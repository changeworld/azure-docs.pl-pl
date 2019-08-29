---
title: Importowanie aplikacji logiki jako interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management (APIM) do importowania aplikacji logiki jako interfejsu API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: apimpm
ms.openlocfilehash: 57965b0326171f37d33aeae02080c8887c3acf23
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072075"
---
# <a name="import-a-logic-app-as-an-api"></a>Importowanie aplikacji logiki jako interfejsu API

W tym artykule przedstawiono sposób importowania aplikacji logiki jako interfejsu API i testowania zaimportowanego interfejsu API.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> -   Importowanie aplikacji logiki jako interfejsu API
> -   Testowanie interfejsu API w witrynie Azure Portal
> -   Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

-   Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
-   Upewnij się, że Twoja subskrypcja obejmuje aplikację logiki, która uwidacznia punkt końcowy HTTP. Aby uzyskać więcej informacji, zobacz [Trigger workflows with HTTP endpoints (Wyzwalanie przepływów pracy za pomocą punktów końcowych HTTP)](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **Aplikacja logiki** z listy **Dodaj nowy interfejs API**.

    ![Aplikacja logiki](./media/import-logic-app-as-api/logic-app-api.png)

3. Naciśnij przycisk **Przeglądaj** , aby wyświetlić listę Logic Apps z wyzwalaczem http w ramach subskrypcji. (Należy zauważyć, że Logic Apps bez wyzwalacza HTTP nie będą wyświetlane na liście).
4. Wybierz aplikację. API Management znajduje strukturę Swagger skojarzoną z wybraną aplikacją, pobiera ją i importuje.
5. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi API Management. Musi być unikatowa w tym wystąpieniu API Management.
6. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „_Nieograniczony_”. Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie API Management, jesteś administratorem, więc domyślnie subskrybujesz każdy produkt.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    - **Starter (początkowy)**
    - **Unlimited (nieograniczony)**

7. Wybierz pozycję **Utwórz**.

## <a name="test-the-imported-api-in-the-azure-portal"></a>Przetestuj zaimportowany interfejs API w Azure Portal

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie API Management, oznacza to, że już jesteś administratorem, więc klucz zostanie uzupełniony automatycznie.

4. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

## <a name="call-operation"> </a>Wywoływanie operacji z portalu dla deweloperów

Operacje mogą być również wywoływane z **portalu dla deweloperów** w celu przetestowania interfejsów API.

1. Wybierz interfejs API utworzony w kroku „Importowanie i publikowanie interfejsu API zaplecza”.
2. Naciśnij pozycję **Portal dla deweloperów**.

    Zostanie otwarta witryna „Portal dla deweloperów”.

3. Wybierz utworzony **interfejs API**.
4. Kliknij operację, którą chcesz przetestować.
5. Naciśnij pozycję **Wypróbuj**.
6. Kliknij pozycję **Wyślij**.

    Po wywołaniu operacji portal dla deweloperów wyświetla **stan odpowiedzi**, **nagłówki odpowiedzi** oraz wszelką **zawartość odpowiedzi**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Każda aplikacja logiki ma operację **manual-invoke**. Jeśli interfejs API ma składać się z wielu aplikacji logiki, należy zmienić nazwę funkcji, aby zapobiec konfliktowi.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
