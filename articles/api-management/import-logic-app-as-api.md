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
ms.devlang: na
ms.topic: tutorial
origin.date: 11/22/2017
ms.author: v-yiso
ms.date: 02/04/2019
ms.openlocfilehash: 76a509c1cb9277ac72f99ec9ebfc239bfd71390c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234659"
---
# <a name="import-a-logic-app-as-an-api"></a>Importowanie aplikacji logiki jako interfejsu API

W tym artykule przedstawiono sposób importowania aplikacji logiki jako interfejsu API. Artykuł przedstawia również sposób testowania interfejsu API usługi APIM.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie aplikacji logiki jako interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
* Upewnij się, że Twoja subskrypcja obejmuje aplikację logiki, która uwidacznia punkt końcowy HTTP. Aby uzyskać więcej informacji, zobacz [Trigger workflows with HTTP endpoints (Wyzwalanie przepływów pracy za pomocą punktów końcowych HTTP)](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **Aplikacja logiki** z listy **Dodaj nowy interfejs API**.

    ![Aplikacja logiki](./media/import-logic-app-as-api/logic-app-api.png)
3. Naciśnij pozycję **Przeglądaj**, aby wyświetlić listę wywoływalnych aplikacji logiki w ramach subskrypcji.
4. Wybierz aplikację. Usługa APIM znajduje program Swagger skojarzony z wybraną aplikacją, a następnie pobiera go i importuje. 
5. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi APIM. Musi ona być unikatowa w tym wystąpieniu usługi APIM.
6. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „*Nieograniczony*”.  Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    * **Starter (początkowy)**
    * **Unlimited (nieograniczony)**   
7. Wybierz pozycję **Utwórz**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testowanie nowego interfejsu API usługi APIM w witrynie Azure Portal

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.  

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc klucz zostanie uzupełniony automatycznie. 
1. Kliknij pozycję **Wyślij**.

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
> Każda aplikacja logiki ma operację **manual-invoke**. Jeśli interfejs API ma składać się z wielu aplikacji logiki, należy zmienić nazwę funkcji, aby zapobiec konfliktowi.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)