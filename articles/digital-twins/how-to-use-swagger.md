---
title: Zrozumienia sposobu używania Azure cyfrowego Twins Swagger | Dokumentacja firmy Microsoft
description: Jak używać Azure cyfrowego Twins Swagger
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 3bc365c204ab75a2f136c3e26c4b598b25f66114
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093493"
---
# <a name="how-to-use-azure-digital-twins-swagger"></a>Jak używać Azure cyfrowego Twins Swagger

Każde elastycznie wystąpienie Twins cyfrowych platformy Azure zawiera swój własny automatycznie wygenerowaną dokumentację referencyjną struktury Swagger.

[Struktury swagger](https://swagger.io/) (lub [OpenAPI](https://www.openapis.org/)) łączy w sobie złożone informacje interfejsu API do zasobów odwołanie interakcyjne i niezależny od języka. W szczególności struktury Swagger zawiera materiały referencyjne krytycznych, o których ładunki JSON, metody HTTP i punkty końcowe służące do wykonywania operacji dotyczących interfejsu API.

## <a name="swagger-summary"></a>Podsumowanie swagger

Struktury swagger zawiera interakcyjne podsumowanie Twojego interfejsu API, w tym:

* Informacje dotyczące interfejsu API i Model obiektów.
* Punkty końcowe interfejsu API REST, określając wymagane, ładunki żądania, nagłówki, parametrów, kontekstu ścieżki i metody HTTP.
* Testowanie funkcji interfejsu API.
* Informacje o odpowiedzi przykład sprawdzania poprawności i Potwierdzanie odpowiedzi HTTP.
* Informacje o kodzie błędu.

Struktury swagger, dlatego jest wygodne narzędzie na potrzeby programowania i testowania wywołania interfejsu API zarządzania.

> [!TIP]
> Odwołanie Swagger rzucić okiem znajduje się do zademonstrowania funkcji interfejsu API zestawu.
> Jest ona hostowana na [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Można uzyskać dostęp do swoich potrzeb, wygenerowany, dokumentacji struktury Swagger interfejsu API zarządzania, na:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourInstanceName* | Nazwa wystąpienia usługi Azure cyfrowego bliźniaczych reprezentacji |
| *yourLocation* | Który region serwer wystąpienie usługi jest hostowana na |

## <a name="reference-material"></a>Materiały referencyjne

Materiały referencyjne automatycznie generowanych wyjaśnia krytyczne pojęć i modele obiektów.

Krótkie podsumowanie Opisuje interfejs API:

![Top struktury swagger][1]

Modele obiektów interfejsu API Core są również wyszczególnione:

![Modele struktury swagger][2]

Możesz kliknąć na każdej liście Model obiektów dla bardziej szczegółowe podsumowanie kluczowych atrybutów:

![Model struktury swagger][3]

Wygenerowany modele obiektów programu Swagger są wygodne wyświetlić wszystkie dostępne Azure cyfrowego bliźniaczych reprezentacji [obiektów i interfejsów API](./concepts-objectmodel-spatialgraph.md). Jest cennym zasobem dla deweloperów do użycia podczas tworzenia rozwiązań na Twins cyfrowych platformy Azure.

## <a name="endpoint-summary"></a>Podsumowanie punktu końcowego

Struktury swagger zawiera również szczegółowe omówienie wszystkich punktów końcowych, które tworzą interfejs API.

Każdy punkt końcowy, wymienionych zawiera także wymagane żądanie informacji takich jak:

* Wymagane parametry.
* Typy danych wymaganego parametru.
* Metoda HTTP, aby uzyskać dostęp do zasobu.

![Punkty końcowe programu swagger][4]

Każdy zasób, można kliknąć, aby zobaczyć bardziej szczegółowym omówieniem.

## <a name="using-swagger-to-test-endpoints"></a>Aby przetestować punkty końcowe korzystające z programu Swagger

Jednym z zaawansowane funkcje, które zapewnia struktury Swagger jest możliwość **wypróbuj działanie rozwiązania** lub testów punkt końcowy interfejsu API bezpośrednio w ramach dokumentacji interfejsu użytkownika.

Po kliknięciu przycisku do określonego punktu końcowego, zobaczysz **wypróbuj działanie rozwiązania** przycisku:

![Spróbuj struktury swagger][5]

Rozwijanie tej sekcji powoduje wyświetlenie pól wejściowych dla wszystkich wymaganych i opcjonalnych parametrów. Wprowadź odpowiednie wartości, a następnie kliknij przycisk **Execute**:

![Nastąpiła struktury swagger][6]

Po wykonaniu testu, można sprawdzić poprawność danych odpowiedzi.

## <a name="swagger-response-data"></a>Dane odpowiedzi programu swagger

Każdy punkt końcowy, wymienionych obejmuje również dane treści odpowiedzi weryfikowanie programowania i testów. Te przykłady kodów żądany stan i JSON pomyślnych żądań HTTP.

![Odpowiedź struktury swagger][7]

Przykłady obejmują również kody błędów, aby ułatwić debugowanie lub ulepszenia niepowodzenie testów.

## <a name="swagger-oauth-20-authorization"></a>Struktury swagger autoryzacji OAuth 2.0

Aby przetestować interaktywnie żądania w odniesieniu do zasobów interfejsu API chronionego przez OAuth 2.0, zobacz temat [oficjalną dokumentacją](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o modele obiektów Twins cyfrowych platformy Azure i wykres analizy przestrzennej, przeczytaj [w tym artykule](./concepts-objectmodel-spatialgraph.md).

Aby dowiedzieć się, jak można uwierzytelnić przy użyciu interfejsu API zarządzania, przeczytaj [uwierzytelniania za pomocą interfejsów API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
