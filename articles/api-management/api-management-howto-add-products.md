---
title: Jak tworzyć i publikować produkt w usłudze Azure API Management
description: Dowiedz się, jak tworzyć i publikować produkty w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 4f5097f8555d0345add7de8a0f648190bd4e1e00
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "41920881"
---
# <a name="create-and-publish-a-product"></a>Tworzenie i publikowanie produktu  

W usłudze Azure API Management produkt zawiera co najmniej jeden interfejs API oraz limit przydziału użycia i warunki użytkowania. Po opublikowaniu produktu deweloperzy mogą go zasubskrybować i zacząć korzystać z jego interfejsów API.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i publikowanie produktu
> * Dodawanie interfejsu API do produktu

![dodawanie produktu](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Tworzenie i publikowanie produktu

1. Kliknij pozycję **Produkty** w menu po lewej stronie, aby wyświetlić stronę **Produkty**.
2. Kliknij pozycję **+ Dodaj**.

    ![dodany produkt](media/api-management-howto-add-products/add-product.png)

    Podczas dodawania produktu należy podać następujące informacje: 

    | Name (Nazwa)                     | Opis                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nazwa wyświetlana             | Nazwa, która ma być wyświetlana w **portalu deweloperów**.                                                                                                                                                                                                                                                        |
    | Name (Nazwa)                     | Opisowa nazwa produktu.                                                                                                                                                                                                                                                                                      |
    | Opis              | W polu **Opis** możliwe jest podanie szczegółów dotyczących produktu, takich jak jego przeznaczenie, interfejsy API, do których zapewnia dostęp, oraz innych przydatnych informacji.                                                                                                                                               |
    | Stan                    | Naciśnij pozycję **Opublikowano**, jeśli chcesz opublikować produkt. Aby możliwe było wywołanie interfejsów API w produkcie, produkt musi zostać najpierw opublikowany. Domyślnie nowe produkty są nieopublikowane i widoczne tylko dla użytkowników w grupie **Administratorzy**.                                                                                      |
    | Wymaga subskrypcji    | Zaznacz pole wyboru **Wymaga subskrypcji**, jeśli do używania produktu wymagane jest, aby użytkownik przeprowadził subskrypcję.                                                                                                                                                                                                                                   |
    | Wymaga zatwierdzenia        | Zaznacz pole wyboru **Wymagaj zatwierdzenia**, jeśli chcesz, aby administrator przeglądał i akceptował lub odrzucał próby subskrypcji tego produktu. Jeśli to pole wyboru nie jest zaznaczone, próby subskrypcji będą zatwierdzane automatycznie.                                                                                                                         |
    | Limit liczby subskrypcji | Aby ograniczyć liczby wielu równoczesnych subskrypcji, wprowadź limit subskrypcji.                                                                                                                                                                                                                                |
    | Postanowienia prawne              | Możesz uwzględnić warunki użytkowania produktu, które jego subskrybenci muszą zaakceptować, aby z niego korzystać.                                                                                                                                                                                                             |
    | Interfejsy API                     | Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. <br/> Podczas tworzenia produktu możesz dodać istniejący interfejs API. Interfejs API możesz dodać do produktu później — z poziomu strony **Ustawienia** produktów lub podczas jego tworzenia. |

3. Kliknij pozycję **Utwórz**, aby utworzyć nowy produkt.

### <a name="add-more-configurations"></a>Dodawanie dodatkowych konfiguracji

Możesz kontynuować konfigurowanie produktu po zapisaniu go, wybierając kartę **Ustawienia**. 

Wyświetl lub dodaj subskrybentów produktu z poziomu karty **Subskrypcje**.

Ustaw widoczność produktu dla deweloperów lub gości z poziomu karty **Kontrola dostępu**.

## <a name="add-apis"> </a>Dodawanie interfejsów API do produktu

Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Podczas tworzenia produktu możesz dodać istniejący interfejs API. Interfejs API możesz dodać do produktu później — z poziomu strony **Ustawienia** produktów lub podczas jego tworzenia.

Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.

### <a name="add-an-api-to-an-existing-product"></a>Dodawanie interfejsu API do istniejącego produktu

![dodawanie interfejsu API produktu](media/api-management-howto-add-products/add-product-api.png)

1. Z poziomu karty **Produkty** wybierz produkt.
2. Przejdź do karty **Interfejsy API**.
3. Kliknij pozycję **+ Dodaj**.
4. Wybierz interfejs API, a następnie kliknij pozycję **Wybierz**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie i publikowanie produktu
> * Dodawanie interfejsu API do produktu

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Tworzenie pustego interfejsu API i testowanie odpowiedzi interfejsu API](mock-api-responses.md)
