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
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: 57803ec9889cb6a19dae6d6d1070d8381577aff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77468392"
---
# <a name="import-an-openapi-specification"></a>Importowanie specyfikacji interfejsu OpenAPI

W tym artykule pokazano, jak zaimportować interfejs API zaplecza „Specyfikacja interfejsu OpenAPI” znajdujący się pod adresem https://conferenceapi.azurewebsites.net?format=json. Ten interfejs API zaplecza jest zapewniany przez firmę Microsoft i hostowany na platformie Azure. Artykuł przedstawia również sposób testowania interfejsu API usługi APIM.

> [!IMPORTANT]
> Zobacz ten [dokument](https://azure.microsoft.com/blog/announcing-the-preview-of-openapi-specification-v3-support-in-azure-api-management/), aby uzyskać ważne informacje i wskazówki związane z importowaniem interfejsu OpenAPI.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie interfejsu API zaplecza „Specyfikacja interfejsu OpenAPI”
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **Specyfikacja interfejsu OpenAPI** z listy **Dodawanie nowego interfejsu API**.

    ![Specyfikacja OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Wprowadź odpowiednie ustawienia. Podczas tworzenia możesz ustawić wszystkie wartości interfejsu API. Alternatywnie możesz ustawić niektóre z nich później, przechodząc na kartę **Ustawienia**. <br/> Jeśli naciśniesz klawisz **Tab** niektóre (lub wszystkie) pola zostaną wypełnione z użyciem informacji z określonej usługi zaplecza.

    ![Tworzenie interfejsu API](./media/api-management-get-started/create-api.png)

    |Ustawienie|Wartość|Opis|
    |---|---|---|
    |**Specyfikacja interfejsu OpenAPI**|https://conferenceapi.azurewebsites.net?format=json|Zawiera odwołanie do usługi implementującej interfejs API. Usługa API Management przekazuje żądania na ten adres.|
    |**Nazwa wyświetlana**|*Wersja demonstracyjna interfejsu API Conference*|W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON. <br/>Ta nazwa jest wyświetlana w portalu dla deweloperów.|
    |**Nazwa**|*demo-conference-api*|Zapewnia unikatową nazwę interfejsu API. <br/>W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON.|
    |**Opis**|Wprowadź opcjonalny opis interfejsu API.|W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON.|
    |**Sufiks adresu URL interfejsu API**|*conference*|Sufiks jest dołączany do podstawowego adresu URL usługi API Management. W usłudze API Management interfejsy API są rozróżniane na podstawie sufiksów, dlatego sufiksy poszczególnych interfejsów API dla danego wydawcy muszą być unikatowe.|
    |**Schemat adresu URL**|*Protokół HTTPS*|Określa, których protokołów można używać w celu uzyskania dostępu do interfejsu API. |
    |**Produkty**|*Unlimited (nieograniczony)*| Opublikuj interfejs API przez skojarzenie go z produktem. Opcjonalnie możesz dodać ten nowy interfejs API do produktu, wpisując nazwę produktu. Ten krok możesz powtórzyć wiele razy, aby dodać interfejs API do wielu produktów.<br/>Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.<br/> Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe: **Starter** i **Unlimited**. |

4. Wybierz **pozycję Utwórz**.

> [!NOTE]
> Ograniczenia importu interfejsu API są udokumentowane w [innym artykule](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testowanie nowego interfejsu API w witrynie Azure portal

![Mapa testowania interfejsu API](./media/api-management-get-started/01-import-first-api-01.png)

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.

1. Wybierz interfejs API utworzony w poprzednim kroku (z karty **Interfejsy API**).
2. Naciśnij kartę **Test**.
3. Kliknij pozycję **GetSpeakers**. Na stronie są wyświetlane pola parametrów zapytania (w tym przypadku nie ma żadnych parametrów) i nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Klucz jest uzupełniany automatycznie.
4. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
