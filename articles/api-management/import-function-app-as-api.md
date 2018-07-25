---
title: Importowanie aplikacji funkcji jako interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management (APIM) do importowania aplikacji funkcji jako interfejsu API.
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
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090932"
---
# <a name="import-a-function-app-as-an-api"></a>Importowanie aplikacji funkcji jako interfejsu API

W tym artykule przedstawiono sposób importowania aplikacji funkcji jako interfejsu API. Artykuł przedstawia również sposób testowania interfejsu API usługi APIM.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie aplikacji funkcji jako interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
+ Upewnij się, że Twoja subskrypcja zawiera aplikację funkcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Create a Function App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) (Tworzenie aplikacji funkcji).
+ [Utwórz definicję interfejsu OpenAPI](../azure-functions/functions-openapi-definition.md) aplikacji funkcji platformy Azure

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **Aplikacja funkcji** z listy **Dodaj nowy interfejs API**.

    ![Aplikacja funkcji](./media/import-function-app-as-api/function-app-api.png)
3. Naciśnij pozycję **Przeglądaj**, aby wyświetlić listę aplikacji funkcji w ramach subskrypcji.
4. Wybierz aplikację. Usługa APIM znajduje program Swagger skojarzony z wybraną aplikacją, a następnie pobiera go i importuje. 
5. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi APIM. Musi ona być unikatowa w tym wystąpieniu usługi APIM.
6. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „*Nieograniczony*”.  Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    * **Starter (początkowy)**
    * **Unlimited (nieograniczony)**   
7. Wybierz pozycję **Utwórz**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Wprowadzanie kluczy usługi Azure Functions w usłudze Azure API Management

Jeśli zaimportowane funkcje Azure Functions są chronione za pomocą kluczy, usługa Azure API Management automatycznie tworzy dla nich **nazwane wartości**, ale nie wprowadza wpisów tajnych. W przypadku każdego wpisu należy wykonać poniższe czynności.  

1. Przejdź do karty **Nazwane wartości** w wystąpieniu usługi API Management.
2. Kliknij wpis, a następnie naciśnij przycisk **Pokaż wartość** na pasku bocznym.

    ![Nazwane wartości](./media/import-function-app-as-api/apim-named-values.png)

3. Jeśli zawartość to *code for {nazwa funkcji platformy Azure}*, przejdź do zaimportowanej aplikacji funkcji platformy Azure, a następnie przejdź do funkcji platformy Azure.
4. Przejdź do sekcji **Zarządzaj** wybranej funkcji platformy Azure i skopiuj klucz odpowiedni do metody uwierzytelniania funkcji platformy Azure.

    ![Aplikacja funkcji](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Wklej klucz w polu tekstowym w obszarze **Nazwane wartości** i kliknij przycisk **Zapisz**.

    ![Aplikacja funkcji](./media/import-function-app-as-api/apim-named-values-2.png)

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

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)