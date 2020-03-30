---
title: Jak korzystać z dokumentacji swaggera referencyjnego — Azure Digital Twins | Dokumenty firmy Microsoft
description: Opis sposobu korzystania z dokumentacji referencyjnej Azure Digital Twins Swagger.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023297"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Dokumentacja referencyjna usługi Azure Digital Twins Swagger

Każde aprowizowane wystąpienie usługi Azure Digital Twins zawiera własną automatycznie generowaną dokumentację referencyjną Swagger.

[Swagger](https://swagger.io/)lub [OpenAPI](https://www.openapis.org/)łączy złożone informacje o interfejsie API w interaktywny i niezależny od języka zasób referencyjny. Swagger zawiera krytyczne materiały referencyjne, o których ładunek JSON, metody HTTP i określonych punktów końcowych do użycia do wykonywania operacji w interfejsie API.

## <a name="swagger-summary"></a>Podsumowanie swaggera

Swagger udostępnia interaktywne podsumowanie interfejsu API, które obejmuje:

* Informacje o interfejsie API i modelu obiektu.
* Punkty końcowe interfejsu API REST, które określają wymagane ładunki żądań, nagłówki, parametry, ścieżki kontekstowe i metody HTTP.
* Testowanie funkcji interfejsu API.
* Przykładowe informacje o odpowiedziach używane do sprawdzania poprawności i potwierdzania odpowiedzi HTTP.
* Informacje o kodzie błędu.

Swagger to wygodne narzędzie ułatwiające tworzenie i testowanie wywołań wykonanych w interfejsach API zarządzania cyfrowymi urządzeniami twins azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Materiał referencyjny

Automatycznie generowany materiał referencyjny Swagger dostarcza szybki przegląd ważnych pojęć, dostępnych punktów końcowych interfejsu API zarządzania i opis każdego modelu obiektu, aby pomóc w opracowywaniu i testowaniu.

Zwięzłe podsumowanie opisuje interfejs API.

[![Podsumowanie swagger i informacje ogólne API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Modele obiektów interfejsu API zarządzania są również wymienione.

[![Modele Swagger wymienione na dole interfejsu użytkownika Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Można wybrać każdy model obiektu na liście, aby uzyskać bardziej szczegółowe podsumowanie kluczowych atrybutów.

[![Modele Swagger rozszerzone, aby odczytać zawartość modeli](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Wygenerowane modele obiektów Swagger są wygodne do odczytania wszystkich dostępnych [obiektów i interfejsów API](./concepts-objectmodel-spatialgraph.md)usługi Azure Digital Twins. Deweloperzy mogą używać tego zasobu podczas tworzenia rozwiązań na platformie Azure Digital Twins.

## <a name="endpoint-summary"></a>Podsumowanie punktu końcowego

Swagger zapewnia również dokładny przegląd wszystkich punktów końcowych, które tworzą interfejsy API zarządzania.

Każdy wymieniony punkt końcowy zawiera również wymagane informacje o żądaniu, takie jak:

* Wymagane parametry.
* Wymagane typy danych parametrów.
* HTTP, aby uzyskać dostęp do zasobu.

[![Punkty końcowe swagger wyświetlane w interfejsie użytkownika swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Wybierz każdy zasób, aby wyświetlić ich dodatkową zawartość, aby uzyskać bardziej szczegółowy przegląd.

## <a name="use-swagger-to-test-endpoints"></a>Użyj Swagger do testowania punktów końcowych

Jedną z zaawansowanych funkcji Swagger zapewnia jest możliwość testowania punktu końcowego interfejsu API bezpośrednio za pośrednictwem interfejsu użytkownika dokumentacji.

Po wybraniu określonego punktu końcowego zostanie wyświetlony przycisk **Wypróbuj.**

[![Przycisk Swagger Wypróbuj](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Rozwiń tę sekcję, aby wyświetlić pola wejściowe dla każdego wymaganego i opcjonalnego parametru. Wprowadź poprawne wartości i wybierz pozycję **Wykonaj**.

[![Swagger Wypróbuj przykład wyniku](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Po wykonaniu testu można sprawdzić poprawność danych odpowiedzi.

## <a name="swagger-response-data"></a>Dane odpowiedzi Swaggera

Każdy wymieniony punkt końcowy zawiera również dane treści odpowiedzi, aby sprawdzić poprawność rozwoju i testów. Przykłady te obejmują kody stanu i JSON dla pomyślnych żądań HTTP.

[![Przykład odpowiedzi Swagger JSON](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Przykłady obejmują również kody błędów, aby pomóc debugowania lub poprawy testów nie po awarii.

## <a name="swagger-oauth-20-authorization"></a>Autoryzacja SwaggerAuth 2.0

> [!NOTE]
> * Podmiot zabezpieczeń użytkownika, który utworzył zasób usługi Azure Digital Twins, będzie miał przypisanie roli Administratora przestrzeni i będzie mógł tworzyć dodatkowe przypisania ról dla innych użytkowników. Ci użytkownicy i ich role mogą być autoryzowane do wywoływania interfejsów API.

1. Wykonaj kroki opisane w [przewodniku Szybki start,](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) aby utworzyć i skonfigurować aplikację usługi Azure Active Directory. Alternatywnie można ponownie użyć istniejącej rejestracji aplikacji.

1. Dodaj następujący **identyfikator URI przekierowania** do rejestracji aplikacji usługi Azure Active Directory:

    [![Zarejestruj adres URL przekierowania Swaggera w UAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nazwa  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Adres URL dokumentacji interfejsu API interfejsu API zarządzania znaleziony w portalu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Zaznacz pole wyboru **Niejawne** > **tokeny programu Access,** aby zezwolić na użycie niejawnego przepływu dotacji OAuth 2.0. Wybierz **pozycję Konfiguruj**, a następnie **zapisz**.

1. Skopiuj **identyfikator klienta** aplikacji usługi Azure Active Directory.

Po zakończeniu rejestracji usługi Azure Active Directory:

1. Wybierz przycisk **Autoryzuj** na stronie swagger.

    [![Wybierz przycisk Autoryzuj waggera](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Wklej identyfikator aplikacji do pola **client_id.**

    [![Pole client_id łupu](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Następnie zostaniesz przekierowany do następującego modalnego sukcesu.

    [![Swagger przekierowanie modalne](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Aby dowiedzieć się więcej o interaktywnym testowaniu żądań chronionych przez OAuth 2.0, przeczytaj [oficjalną dokumentację](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o modelach obiektów usługi Azure Digital Twins i wykresie analizy przestrzennej, przeczytaj artykuł [Opis modeli obiektów usługi Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

- Aby dowiedzieć się, jak uwierzytelnić się za pomocą interfejsu API zarządzania, przeczytaj [artykuł Uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).
