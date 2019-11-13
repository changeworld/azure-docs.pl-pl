---
title: Jak korzystać z dokumentacji struktury Swagger w usłudze Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Informacje dotyczące korzystania z dokumentacji programu Azure Digital bliźniaczych reprezentacji Swagger.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: ccea63e8edee739ce6743d7638b4e5300ad07f8f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009879"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Dokumentacja usługi Azure Digital bliźniaczych reprezentacji Swagger

Każde zainicjowane wystąpienie usługi Azure Digital bliźniaczych reprezentacji zawiera własną, automatycznie wygenerowaną dokumentację programu Swagger.

Struktura [Swagger](https://swagger.io/)lub [openapi](https://www.openapis.org/)umożliwia łączenie złożonych informacji interfejsu API z zasobami referencyjnymi w języku interaktywnym i niezależny od. Struktura Swagger zawiera krytyczne materiały referencyjne na temat tego, które ładunki JSON, metody HTTP i określone punkty końcowe służą do wykonywania operacji na interfejsie API.

## <a name="swagger-summary"></a>Podsumowanie struktury Swagger

Struktura Swagger zawiera interaktywne podsumowanie interfejsu API, w tym:

* Interfejs API i informacje o modelu obiektu.
* Punkty końcowe interfejsu API REST, które określają wymagane ładunki żądań, nagłówki, parametry, ścieżki kontekstu i metody HTTP.
* Testowanie funkcji interfejsu API.
* Przykładowe informacje dotyczące odpowiedzi służące do weryfikowania i potwierdzania odpowiedzi HTTP.
* Informacje o kodzie błędu.

Struktura Swagger to wygodne narzędzie pomocne przy tworzeniu i testowaniu wywołań interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Materiały referencyjne

Automatycznie wygenerowany materiał referencyjny struktury Swagger zawiera krótkie omówienie ważnych pojęć, dostępnych punktów końcowych interfejsu API zarządzania i opis każdego modelu obiektów, aby pomóc w tworzeniu i testowaniu.

Zwięzłe podsumowanie zawiera opis interfejsu API.

[![Swagger — Góra](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Są również wyświetlane modele obiektów interfejsu API zarządzania.

[![modele Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Możesz wybrać każdy z wymienionych modeli obiektów, aby uzyskać bardziej szczegółowy opis atrybutów klucza.

[model struktury Swagger ![](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Wygenerowane modele obiektów Swagger są wygodne do wyświetlania wszystkich dostępnych [obiektów i interfejsów API](./concepts-objectmodel-spatialgraph.md)usługi Azure Digital bliźniaczych reprezentacji. Deweloperzy mogą korzystać z tego zasobu podczas tworzenia rozwiązań na platformie Azure Digital bliźniaczych reprezentacji.

## <a name="endpoint-summary"></a>Podsumowanie punktu końcowego

Struktura Swagger zawiera również szczegółowe omówienie wszystkich punktów końcowych, które tworzą interfejsy API zarządzania.

Każdy z wymienionych punktów końcowych zawiera również wymagane informacje o żądaniu, takie jak:

* Wymagane parametry.
* Wymagany typ danych parametru.
* Metoda HTTP uzyskiwania dostępu do zasobu.

[![punkty końcowe struktury Swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Aby zapoznać się z bardziej szczegółowym omówieniem, wybierz każdy zasób.

## <a name="use-swagger-to-test-endpoints"></a>Użyj struktury Swagger do testowania punktów końcowych

Jedną z zaawansowanych funkcji struktury Swagger zapewnia możliwość przetestowania punktu końcowego interfejsu API bezpośrednio za pomocą interfejsu użytkownika dokumentacji.

Po wybraniu określonego punktu końcowego zobaczysz **go**.

[![strukturę Swagger](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Rozwiń tę sekcję, aby wyświetlić pola wejściowe dla każdego wymaganego i opcjonalnego parametru. Wprowadź prawidłowe wartości, a następnie wybierz pozycję **Wykonaj**.

[próba ![struktury Swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Po wykonaniu testu można sprawdzić poprawność danych odpowiedzi.

## <a name="swagger-response-data"></a>Dane odpowiedzi struktury Swagger

Każdy z wymienionych punktów końcowych zawiera również dane treści odpowiedzi w celu zweryfikowania rozwoju i testów. Te przykłady obejmują kody stanu i kod JSON, które mają być wyświetlane w przypadku pomyślnych żądań HTTP.

[![odpowiedzi struktury Swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Przykłady zawierają również kody błędów ułatwiające debugowanie lub usprawnienie testów zakończonych niepowodzeniem.

## <a name="swagger-oauth-20-authorization"></a>Autoryzacja OAuth 2,0 programu Swagger

> [!NOTE]
> * Podmiot zabezpieczeń, który utworzył zasób usługi Azure Digital bliźniaczych reprezentacji, będzie miał przypisanie roli administratora miejsca i będzie mógł tworzyć dodatkowe przypisania ról dla innych użytkowników. Takie Użytkownicy i ich role mogą być autoryzowane do wywoływania interfejsów API.

1. Wykonaj kroki opisane w [tym przewodniku szybki start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) , aby utworzyć i skonfigurować aplikację usługi Azure AD. Alternatywnie możesz ponownie użyć istniejącej rejestracji aplikacji.

1. Dodaj następujący adres URL odpowiedzi do rejestracji aplikacji:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nazwa  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Adres URL dokumentacji interfejsu API REST zarządzania znajduje się w portalu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Skopiuj identyfikator aplikacji usługi Azure AD.

Po zakończeniu rejestracji Azure Active Directory:

1. Wybierz przycisk **Autoryzuj** na stronie struktury Swagger.

    [![wybierz przycisk Autoryzuj autoryzację](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Wklej identyfikator aplikacji do pola **client_id** .

    [![Swagger client_id pole](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Następnie nastąpi przekierowanie do poniższego modalnego sukcesu.

    [modalne przekierowanie ![Swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Aby dowiedzieć się więcej na temat interakcyjnych żądań testowania chronionych przez uwierzytelnianie OAuth 2,0, zobacz [oficjalną dokumentację](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat modeli obiektów Digital bliźniaczych reprezentacji i grafu analiz przestrzennych, przeczytaj artykuł [Omówienie modeli obiektów Digital bliźniaczych reprezentacji platformy Azure](./concepts-objectmodel-spatialgraph.md).

- Aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API zarządzania, przeczytaj artykuł [uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).