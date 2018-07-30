---
title: Importowanie aplikacji usługi Azure Functions jako interfejsu API w witrynie Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management do importowania aplikacji usługi Azure Functions jako interfejsu API.
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
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239056"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Importowanie aplikacji usługi Azure Functions jako interfejsu API

W tym artykule przedstawiono sposób importowania aplikacji usługi Azure Functions jako interfejsu API. Artykuł przedstawia również sposób testowania interfejsu API usługi Azure API Management.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie aplikacji usługi Functions jako interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj procedury przedstawione w przewodniku Szybki start [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Upewnij się, że Twoja subskrypcja zawiera aplikację usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Create a Functions App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) (Tworzenie aplikacji usługi Functions).
+ [Utwórz definicję interfejsu OpenAPI](../azure-functions/functions-openapi-definition.md) aplikacji usługi Azure Functions.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Importowanie i publikowanie interfejsu API zaplecza

1. W obszarze **API MANAGEMENT** wybierz pozycję **Interfejsy API**.
2. Z listy **Dodaj nowy interfejs API** wybierz pozycję **Aplikacja usługi Functions**.

    ![Aplikacja usługi Functions](./media/import-function-app-as-api/function-app-api.png)
3. Wybierz pozycję **Przeglądaj**, aby wyświetlić listę aplikacji usługi Functions w ramach subskrypcji.
4. Wybierz aplikację. Usługa API Management znajduje program Swagger skojarzony z wybraną aplikacją, a następnie pobiera go i importuje. 
5. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi API Management. Sufiks musi być unikatowy w tym wystąpieniu usługi API Management.
6. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu **Nieograniczony**. Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Interfejs API można dodać do produktu podczas tworzenia interfejsu API lub później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania deweloper otrzymuje klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi API Management, jesteś administratorem. Domyślnie administratorzy mają subskrypcję każdego produktu.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    * **Starter (początkowy)**
    * **Unlimited (nieograniczony)**   
7. Wybierz pozycję **Utwórz**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Wprowadzanie kluczy usługi Azure Functions w usłudze Azure API Management

Jeśli zaimportowane funkcje Azure Functions są chronione za pomocą kluczy, usługa API Management automatycznie tworzy *nazwane wartości* dla kluczy. Usługa API Management nie wprowadza wpisów tajnych. Wykonaj następujące kroki dla każdego wpisu:  

1. W wystąpieniu usługi API Management wybierz kartę **Nazwane wartości**.
2. Wybierz wpis, a następnie wybierz pozycję **Pokaż wartość** na pasku bocznym.

    ![Nazwane wartości](./media/import-function-app-as-api/apim-named-values.png)

3. Jeśli tekst wyświetlany w polu **Wartość** jest podobna do **kodu \<nazwy usługi Azure Functions\>**, przejdź do pozycji **Aplikacje usługi Functions**, a następnie przejdź do pozycji **Functions**.
4. Wybierz pozycję **Zarządzaj**, a następnie skopiuj odpowiedni klucz na podstawie metody uwierzytelniania aplikacji.

    ![Aplikacje usługi Functions — kopiowanie kluczy](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Wklej klucz w polu **Wartość**, a następnie wybierz przycisk **Zapisz**.

    ![Aplikacje usługi Functions — wklejanie wartości klucza](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Testowanie nowego interfejsu API usługi API Management w witrynie Azure Portal

Operacje można wywoływać bezpośrednio w witrynie Azure Portal. Witryna Azure Portal to wygodny sposób wyświetlania i testowania operacji interfejsu API.  

1. Wybierz interfejs API utworzony w poprzedniej sekcji.
2. Wybierz kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to **Ocp-Apim-Subscription-Key** dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie API Management, oznacza to, że już jesteś administratorem, więc klucz zostanie uzupełniony automatycznie. 
4. Wybierz pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

## <a name="call-operation"></a>Wywoływanie operacji z portalu dla deweloperów

Operacje można również wywoływać z portalu dla deweloperów w celu przetestowania interfejsów API. 

1. Wybierz interfejs API utworzony w kroku [Importowanie i publikowanie interfejsu API zaplecza](#create-api).
2. Wybierz pozycję **Portal dla deweloperów**.

    Zostanie otwarta witryna portalu dla deweloperów.
3. Wybierz utworzony **interfejs API**.
4. Wybierz operację, którą chcesz przetestować.
5. Wybierz pozycję **Wypróbuj**.
6. Wybierz pozycję **Wyślij**.
    
    Po wywołaniu operacji portal dla deweloperów wyświetla **stan odpowiedzi**, **nagłówki odpowiedzi** oraz wszelką **zawartość odpowiedzi**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)