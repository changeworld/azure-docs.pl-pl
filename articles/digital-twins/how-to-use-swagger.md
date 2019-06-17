---
title: Opis sposobu użycia odwołania Twins cyfrowego Azure struktury Swagger | Dokumentacja firmy Microsoft
description: Zrozumienia sposobu używania Azure cyfrowego Twins Swagger dokumentację referencyjną.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: c402b82b91b02f8d9619c851d09c689fd103c9fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116432"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Dokumentacja platformy Azure cyfrowego Twins struktury Swagger

Każde elastycznie wystąpienie Twins cyfrowych platformy Azure zawiera swój własny automatycznie wygenerowaną dokumentację referencyjną struktury Swagger.

[Struktury swagger](https://swagger.io/), lub [OpenAPI](https://www.openapis.org/), łączy w sobie złożone informacje interfejsu API do zasobów odwołanie interakcyjne i niezależny od języka. Struktury swagger zawiera materiały referencyjne krytycznych, o których ładunki JSON, metody HTTP i punkty końcowe służące do wykonywania operacji dotyczących interfejsu API.

## <a name="swagger-summary"></a>Podsumowanie swagger

Struktury swagger zawiera podsumowanie interaktywnego interfejsu API, który zawiera:

* Interfejs API i obiekt informacji o modelu.
* Punkty końcowe interfejsu API REST, określających, ładunki żądania wymagany, nagłówki, parametrów, kontekstu ścieżki i metody HTTP.
* Testowanie funkcji interfejsu API.
* Przykładowe informacje o odpowiedzi używany do sprawdzania poprawności i Potwierdź odpowiedzi HTTP.
* Informacje o kodzie błędu.

Struktury swagger jest wygodne narzędzie na potrzeby programowania i testowania wywołań do cyfrowego Twins zarządzania interfejsów API usługi Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Materiały referencyjne

Automatycznie generowanych materiały referencyjne dla struktury Swagger zawiera krótkie podsumowanie dotyczące ważnych pojęć, dostępnych punktów końcowych interfejsu API zarządzania oraz opis każdego modelu obiektu ułatwiające projektowanie i testowanie.

Krótkie podsumowanie Opisuje interfejs API.

[![Top struktury swagger](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Modele obiektów interfejsu API zarządzania są także wyświetlane.

[![Modele struktury swagger](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

Możesz wybrać każdego modelu obiektów uwzględnione na liście, aby uzyskać bardziej szczegółowe podsumowanie kluczowych atrybutów.

[![Model struktury swagger](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

Wygenerowany modele obiektów struktury Swagger są wygodne wyświetlić wszystkie dostępne Azure cyfrowego bliźniaczych reprezentacji [obiektów i interfejsów API](./concepts-objectmodel-spatialgraph.md). Deweloperzy mogą używać tego zasobu, podczas ich tworzenia rozwiązań na Twins cyfrowych platformy Azure.

## <a name="endpoint-summary"></a>Podsumowanie punktu końcowego

Struktury swagger zawiera również szczegółowe omówienie wszystkich punktów końcowych, które tworzą interfejsy API Management.

Każdy punkt końcowy, wymienionych obejmowały żądanie wymaganych informacji, takich jak:

* Wymagane parametry.
* Typy danych wymaganego parametru.
* Metoda HTTP, aby uzyskać dostęp do zasobu.

[![Punkty końcowe programu swagger](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

Aby wyświetlić bardziej szczegółowym omówieniem, wybierz poszczególne zasoby.

## <a name="use-swagger-to-test-endpoints"></a>Użyj platformy Swagger, aby przetestować punktów końcowych

Jednym z zaawansowane funkcje, jakie zapewnia struktury Swagger jest możliwość testowania punkt końcowy interfejsu API bezpośrednio w ramach dokumentacji interfejsu użytkownika.

Po wybraniu określonego punktu końcowego, zobacz **wypróbuj działanie rozwiązania**.

[![Spróbuj struktury swagger](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

Rozwiń tę sekcję, aby wyświetlić pola wejściowe dla wszystkich wymaganych i opcjonalnych parametrów. Wprowadź prawidłowe wartości, a następnie wybierz pozycję **Execute**.

[![Nastąpiła struktury swagger](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

Po wykonaniu testu, można sprawdzić poprawność danych odpowiedzi.

## <a name="swagger-response-data"></a>Dane odpowiedzi programu swagger

Każdy punkt końcowy, wymienionych obejmuje również dane treści odpowiedzi weryfikowanie programowania i testów. Te przykłady obejmują kodów stanu i chcesz zobaczyć, czy liczba pomyślnych żądań HTTP w formacie JSON.

[![Odpowiedź struktury swagger](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

Przykłady obejmują również kody błędów, aby ułatwić debugowanie lub ulepszenia niepowodzenie testów.

## <a name="swagger-oauth-20-authorization"></a>Struktury swagger autoryzacji OAuth 2.0

Aby dowiedzieć się więcej o testowaniu interaktywnie żądania chronione przez OAuth 2.0, zobacz [oficjalną dokumentacją](https://swagger.io/docs/specification/authentication/oauth2/).

> [!NOTE]
> Nazwy głównej użytkownika, który utworzono zasób Twins cyfrowych platformy Azure będzie mieć przypisanie roli administratora miejsca i będzie można utworzyć przypisania roli dodatkowe dla innych użytkowników.

1. Postępuj zgodnie z instrukcjami w [ten przewodnik Szybki Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) do tworzenia aplikacji usługi Azure AD typu ***aplikacji sieci Web / interfejs API***. Lub możesz ponownie użyć istniejącej rejestracji aplikacji.

2. Dodaj następujący adres url odpowiedzi do rejestracji aplikacji:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name (Nazwa)  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Adres URL dokumentacji interfejsu API REST zarządzania znalezione w portalu  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Przyznaj uprawnienia aplikacji dostęp do reprezentacji urządzeń cyfrowych platformy Azure. W obszarze **wymagane uprawnienia**, wprowadź `Azure Digital Twins` i wybierz **delegowane uprawnienia**. Następnie wybierz pozycję **Udziel uprawnień**.

    ![Rejestracje aplikacji w usłudze Azure AD, Dodaj interfejs api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Konfigurowanie manifestu aplikacji, aby zezwolić na niejawny przepływ OAuth 2.0. Wybierz **manifestu** otworzyć manifest aplikacji dla aplikacji. Ustaw *oauth2AllowImplicitFlow* do `true`.

    ![Usługa Azure AD niejawny przepływ](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Skopiuj identyfikator aplikacji usługi Azure AD.

Po zakończeniu rejestracji usługi Azure Active Directory:

6. Wybierz **Autoryzuj** przycisk na stronie struktury swagger.

    [![Wybierz Swagger autoryzować przycisku](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

7. Wklej identyfikator aplikacji do **client_id** pola.

    [![Pole client_id struktury swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

8. Następnie nastąpi przekierowanie do następujących modalne sukcesu.

    [![Modalne przekierowania struktury swagger](media/how-to-use-swagger/swagger_auth_redirect.PNG)](media/how-to-use-swagger/swagger_auth_redirect.PNG#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o modele obiektów Twins cyfrowych platformy Azure i wykres analizy przestrzennej, przeczytaj [zrozumieć Azure cyfrowego bliźniaczych reprezentacji obiektów modeli](./concepts-objectmodel-spatialgraph.md).

- Aby dowiedzieć się, jak można uwierzytelnić przy użyciu interfejsu API zarządzania, przeczytaj [Uwierzytelnij za pomocą interfejsów API](./security-authenticating-apis.md).