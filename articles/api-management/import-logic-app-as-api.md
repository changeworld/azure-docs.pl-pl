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
ms.openlocfilehash: 4077187fe04e3be914a6f7fba84c03df1b79d06a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74108397"
---
# <a name="import-a-logic-app-as-an-api"></a>Importowanie aplikacji logiki jako interfejsu API

W tym artykule pokazano, jak zaimportować aplikację logiki jako interfejs API i przetestować zaimportowany interfejs API.

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

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **Aplikacja logiki** z listy **Dodaj nowy interfejs API**.

    ![Aplikacja logiki](./media/import-logic-app-as-api/logic-app-api.png)

3. Naciśnij **przycisk Przeglądaj,** aby wyświetlić listę aplikacji logiki z wyzwalaczem HTTP w ramach subskrypcji. (Należy zauważyć, że aplikacje logiki bez wyzwalacza HTTP nie pojawią się na liście).
4. Wybierz aplikację. Usługa API Management wyszukuje swagger skojarzony z wybraną aplikacją, pobiera ją i importuje.
5. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi API Management. Musi być unikatowa w tym wystąpieniu zarządzania interfejsem API.
6. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „_Nieograniczony_”. Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie zarządzania interfejsami API, jesteś już administratorem, więc domyślnie subskrybujesz każdy produkt.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    - **Starter (początkowy)**
    - **Unlimited (nieograniczony)**

7. Wybierz **pozycję Utwórz**.

## <a name="test-the-api-in-the-azure-portal"></a>Testowanie interfejsu API w witrynie Azure Portal

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie API Management, oznacza to, że już jesteś administratorem, więc klucz zostanie uzupełniony automatycznie.

4. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Każda aplikacja logiki ma operację **manual-invoke**. Jeśli interfejs API ma składać się z wielu aplikacji logiki, należy zmienić nazwę funkcji, aby zapobiec konfliktowi.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
