---
title: Importowanie specyfikacji interfejsu OpenAPI przy użyciu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak zaimportować specyfikację interfejsu OpenAPI za pomocą usługi API Management.
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
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: b1602af59cd46b4bbcefa6f1df3cd84c0d342b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236470"
---
# <a name="import-an-openapi-specification"></a>Importowanie specyfikacji interfejsu OpenAPI

W tym artykule pokazano, jak zaimportować interfejs API zaplecza „Specyfikacja interfejsu OpenAPI” znajdujący się pod adresem https://conferenceapi.azurewebsites.net?format=json. Ten interfejs API zaplecza jest zapewniany przez firmę Microsoft i hostowany na platformie Azure. Artykuł przedstawia również sposób testowania interfejsu API usługi APIM.

> [!IMPORTANT]
> Zobacz ten [dokument](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/), aby uzyskać ważne informacje i wskazówki związane z importowaniem interfejsu OpenAPI.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie interfejsu API zaplecza „Specyfikacja interfejsu OpenAPI”
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **Specyfikacja interfejsu OpenAPI** z listy **Dodawanie nowego interfejsu API**.
    ![Specyfikacja interfejsu OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Wprowadź odpowiednie ustawienia. Podczas tworzenia możesz ustawić wszystkie wartości interfejsu API. Alternatywnie możesz ustawić niektóre z nich później, przechodząc na kartę **Ustawienia**. <br/> Jeśli naciśniesz klawisz **Tab** niektóre (lub wszystkie) pola zostaną wypełnione z użyciem informacji z określonej usługi zaplecza.

    ![Tworzenie interfejsu API](./media/api-management-get-started/create-api.png)

    |Ustawienie|Wartość|Opis|
    |---|---|---|
    |**Specyfikacja interfejsu OpenAPI**|https://conferenceapi.azurewebsites.net?format=json|Zawiera odwołanie do usługi implementującej interfejs API. Usługa API Management przekazuje żądania na ten adres.|
    |**Nazwa wyświetlana**|*Wersja demonstracyjna interfejsu API Conference*|W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON. <br/>Ta nazwa jest wyświetlana w portalu dla deweloperów.|
    |**Nazwa**|*demo-conference-api*|Zapewnia unikatową nazwę interfejsu API. <br/>W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON.|
    |**Opis**|Wprowadź opcjonalny opis interfejsu API.|W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON.|
    |**Sufiks adresu URL interfejsu API**|*conference*|Sufiks jest dołączany do podstawowego adresu URL usługi API Management. W usłudze API Management interfejsy API są rozróżniane na podstawie sufiksów, dlatego sufiksy poszczególnych interfejsów API dla danego wydawcy muszą być unikatowe.|
    |**Schemat adresu URL**|*HTTPS*|Określa, których protokołów można używać w celu uzyskania dostępu do interfejsu API. |
    |**Produkty**|*Unlimited (nieograniczony)*| Opublikuj interfejs API przez skojarzenie go z produktem. Opcjonalnie możesz dodać ten nowy interfejs API do produktu, wpisując nazwę produktu. Ten krok możesz powtórzyć wiele razy, aby dodać interfejs API do wielu produktów.<br/>Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.<br/> Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe: **początkowy** i **bez ograniczeń**. |

4. Wybierz pozycję **Utwórz**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testowanie nowego interfejsu API usługi APIM w witrynie Azure Portal

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.

![Testowanie interfejsu API](./media/api-management-get-started/01-import-first-api-01.png)

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Kliknij pozycję **GetSpeakers**.

    Strona wyświetla pola parametrów zapytania, ale w tym przypadku nie ma żadnych parametrów. Ponadto strona wyświetla pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc klucz zostanie uzupełniony automatycznie.
4. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

## <a name="call-operation"> </a>Wywoływanie operacji z portalu dla deweloperów

Operacje mogą być również wywoływane z **portalu dla deweloperów** w celu przetestowania interfejsów API.

1. Wybierz interfejs API utworzony w kroku „Importowanie i publikowanie interfejsu API zaplecza”.
2. Naciśnij pozycję **Portal dla deweloperów**.

    ![Testowanie w portalu dla deweloperów](./media/api-management-get-started/developer-portal.png)

    Zostanie otwarta witryna „Portal dla deweloperów”.
3. Wybierz pozycję **Interfejs API**.
4. Wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**.
5. Kliknij pozycję **GetSpeakers**.

    Strona wyświetla pola parametrów zapytania, ale w tym przypadku nie ma żadnych parametrów. Ponadto strona wyświetla pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc klucz zostanie uzupełniony automatycznie.
6. Naciśnij pozycję **Wypróbuj**.
7. Kliknij pozycję **Wyślij**.

    Po wywołaniu operacji portal dla deweloperów wyświetla **stan odpowiedzi**, **nagłówki odpowiedzi** oraz wszelką **zawartość odpowiedzi**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
