---
title: Importowanie i publikowanie pierwszego interfejsu API w usłudze Azure API Management | Microsoft Docs
description: Dowiedz się, jak zaimportować i opublikować pierwszy interfejs API przy użyciu usługi API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: bae762b4603b2f5f80447a16671fed4e37e62b95
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108544"
---
# <a name="import-and-publish-your-first-api"></a>Importowanie i publikowanie pierwszego interfejsu API 

Ten samouczek pokazuje, jak zaimportować interfejs API zaplecza „Specyfikacja OpenAPI” znajdujący się w https://conferenceapi.azurewebsites.net?format=json. Ten interfejs API zaplecza jest zapewniany przez firmę Microsoft i hostowany na platformie Azure. 

Po zaimportowaniu interfejsu API zaplecza do usługi API Management (APIM), interfejs API APIM staje się fasadą dla interfejsu API zaplecza. Podczas importowania interfejsu API zaplecza źródłowy interfejs API i interfejs API APIM są identyczne. Usługa APIM umożliwia dostosowywanie fasady zgodnie z potrzebami bez ingerencji w interfejs API zaplecza. Aby uzyskać więcej informacji, zobacz [Przekształcanie i ochrona interfejsu API](transform-api.md). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie pierwszego interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

![Nowy interfejs API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

Ta sekcja przedstawia sposób importowania i publikowania interfejsu API zaplecza specyfikacji OpenAPI.
 
1. Wybierz **Interfejsy API** w obszarze **ZARZĄDZANIE INTERFEJSAMI API**.
2. Wybierz z listy pozycję **Specyfikacja interfejsu OpenAPI** i kliknij przycisk **Pełna** w okienku wyskakującym.

    ![Tworzenie interfejsu API](./media/api-management-get-started/create-api.png)

    Wartości interfejsu API można ustawić podczas tworzenia lub później, przechodząc do karty **Ustawienia** . Czerwona gwiazdka obok pola wskazuje, że pole jest wymagane.

    Użyj wartości z poniższej tabeli, aby utworzyć pierwszy interfejs API.

    | Ustawienie                   | Wartość                                              | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Specyfikacja interfejsu OpenAPI** | https://conferenceapi.azurewebsites.net?format=json | Zawiera odwołanie do usługi implementującej interfejs API. Usługa API Management przekazuje żądania na ten adres.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Nazwa wyświetlana**          | *Wersja demonstracyjna interfejsu API Conference*                              | W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON. <br/>Ta nazwa jest wyświetlana w portalu dla deweloperów.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Nazwa**                  | *demo-conference-api*                              | Zapewnia unikatową nazwę interfejsu API. <br/>W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Opis**           | Wprowadź opcjonalny opis interfejsu API.        | W przypadku naciśnięcia klawisza Tab po wprowadzeniu adresu URL usługi usługa APIM wypełni to pole w oparciu o zawartość pliku JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **Schemat adresu URL**            | *HTTPS*                                            | Określa, których protokołów można używać w celu uzyskania dostępu do interfejsu API.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **Sufiks adresu URL interfejsu API**        | *conference*                                       | Sufiks jest dołączany do podstawowego adresu URL usługi API Management. W usłudze API Management interfejsy API są rozróżniane na podstawie sufiksów, dlatego sufiksy poszczególnych interfejsów API dla danego wydawcy muszą być unikatowe.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Produkty**              | *Unlimited (nieograniczony)*                                        | Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API w produkcie i zaoferować je deweloperom za pośrednictwem portalu deweloperów. <br/>Publikowanie interfejsu API odbywa się poprzez skojarzenie interfejsu API z produktem (w tym przypadku *Unlimited*). Aby dodać ten nowy interfejs API do produktu, wpisz nazwę produktu (możesz też zrobić to później na stronie **Ustawienia**). Ten krok można powtórzyć wiele razy, aby dodać interfejs API do wielu produktów.<br/>Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. <br/> Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc posiada subskrypcję każdego produktu.<br/> Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe: **Starter** i **Unlimited**. |
    | **Tagi**                  |                                                    | Tagi do organizowania interfejsów API. Tagi mogą służyć do wyszukiwania, grupowania lub filtrowania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Czy chcesz utworzyć wersję tego interfejsu API?**     |                                                    | Aby uzyskać więcej informacji o przechowywaniu wersji, zobacz [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Aby opublikować interfejs API, musisz skojarzyć go z produktem. Możesz to zrobić na stronie **Ustawienia**.

3. Wybierz pozycję **Utwórz**.

> [!TIP]
> Jeśli występują problemy z zaimportowaniem Twojej definicji interfejsu API [wyświetl listę znanych problemów i ograniczeń](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Przetestuj nowy interfejs API w Azure Portal

![Mapa testowania interfejsu API](./media/api-management-get-started/01-import-first-api-01.png)

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.

1. Wybierz interfejs API utworzony w poprzednim kroku (z karty **Interfejsy API**).
2. Naciśnij kartę **Test**.
3. Kliknij pozycję **GetSpeakers**. Na stronie są wyświetlane pola parametrów zapytania (w tym przypadku nie ma żadnych parametrów) i nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Klucz jest uzupełniany automatycznie.
4. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

## <a name="next-steps"> </a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie pierwszego interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie produktu](api-management-howto-add-products.md)
