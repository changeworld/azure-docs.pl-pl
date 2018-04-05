---
title: Metadane OpenAPI w usługi Azure Functions | Dokumentacja firmy Microsoft
description: Omówienie obsługi OpenAPI w funkcji platformy Azure
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Obsługa metadanych OpenAPI 2.0 w funkcjach Azure (wersja zapoznawcza)
OpenAPI 2.0 (dawniej Swagger) Obsługa metadanych w usługi Azure Functions to funkcja podglądu służy do zapisania OpenAPI 2.0 definicja aplikacji funkcji. Następnie można obsługiwać tego pliku przy użyciu funkcji aplikacji.

[Metadane OpenAPI](http://swagger.io/) umożliwia funkcję, która obsługuje interfejs API REST do użycia przez wiele różnych innego oprogramowania. Oprogramowanie zawiera oferty firmy Microsoft, takich jak rozwiązania PowerApps i [funkcji aplikacji interfejsu API usługi Azure App Service](../app-service/app-service-web-overview.md), narzędzia dla deweloperów innych firm, takich jak [Postman](https://www.getpostman.com/docs/importing_swagger), i [wiele pakietów więcej](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Firma Microsoft zaleca, począwszy od [Wprowadzenie — samouczek](./functions-api-definition-getting-started.md) , a następnie zwracanie do tego dokumentu, aby dowiedzieć się więcej na temat określonych funkcji.

## <a name="enable"></a>Włącz obsługę definicji OpenAPI
Można skonfigurować wszystkie ustawienia OpenAPI na **definicji interfejsu API** strony aplikacji funkcji **funkcji platformy**.

Aby włączyć Generowanie hostowanej definicji OpenAPI i definicji szybkiego startu, ustaw **źródła definicji interfejsu API** do **— funkcja (wersja zapoznawcza)**. **Zewnętrzny adres URL** umożliwia z funkcji, należy użyć definicji OpenAPI, która ma hostowaną w innym miejscu.

## <a name="generate-definition"></a>Generowanie szkielet Swagger z metadanych funkcji składowanej
Szablon może pomóc rozpocząć pisanie pierwszego definicję OpenAPI. Funkcja szablonu definicji tworzy rozrzedzonych definicji OpenAPI przy użyciu wszystkich metadanych w pliku function.json dla każdej funkcji wyzwalacza HTTP. Musisz podać więcej informacji na temat interfejsu API z [specyfikacji OpenAPI](http://swagger.io/specification/), takie jak szablony żądań i odpowiedzi.

Aby uzyskać instrukcje, zobacz [Wprowadzenie — samouczek](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Dostępne szablony

|Name (Nazwa)| Opis |
|:-----|:-----|
|Wygenerowany definicji|Definicja OpenAPI z maksymalną ilość informacji, które można wywnioskować na podstawie metadanych istniejących funkcji.|

### <a name="quickstart-details"></a>Metadane uwzględnione w wygenerowanym definicji

Poniższa tabela reprezentuje ustawienia portalu Azure i odpowiadające im dane w function.json jest zamapowana do wygenerowanego szkielet struktury Swagger.

|Swagger.json|Portal UI|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|**Ustawienia aplikacji funkcji** > **ustawienia aplikacji usługi** > **omówienie** > **adresu URL**|*Nie istnieje*
|[Ścieżki](http://swagger.io/specification/#paths-object-29)|**Integracja** > **metod HTTP wybrane**|Powiązania: trasy
|[Ścieżka elementu](http://swagger.io/specification/#path-item-object-32)|**Integracja** > **szablon trasy**|Powiązania: metody
|[Bezpieczeństwo](http://swagger.io/specification/#security-scheme-object-112)|**Keys**|*Nie istnieje*|
|operationID *|**Trasy + dozwolone zlecenia**|Trasy + dozwolonych poleceń|

\*Identyfikator operacji jest wymagany tylko w przypadku integracji z PowerApps i przepływów.
> [!NOTE]
> Rozszerzenie x-ms Podsumowanie zawiera nazwę wyświetlaną w aplikacji logiki, PowerApps i przepływów.
>
> Aby dowiedzieć się więcej, zobacz [Dostosowywanie definicji struktury Swagger Twoje rozwiązanie PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Za pomocą elementu konfiguracji / definicji interfejsu API zestawu

 Należy włączyć definicji interfejsu API hosting w portalu, przed włączeniem kontroli źródła zmodyfikować definicję interfejsu API z kontroli źródła. Wykonaj te instrukcje:

1. Przejdź do **definicji interfejsu API (wersja zapoznawcza)** w ustawieniach funkcji aplikacji.
  1. Ustaw **źródła definicji interfejsu API** do **funkcja**.
  1. Kliknij przycisk **API generowania definicji szablonu** , a następnie **zapisać** do tworzenia definicji szablonu modyfikowania później.
  1. Należy pamiętać, Twój adres URL definicji interfejsu API i klucz.
1. [Konfigurowanie ciągłej integracji/ciągłego wdrażania (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Modyfikowanie swagger.json w kontroli źródła na \site\wwwroot\.azurefunctions\swagger\swagger.json.

Teraz, zmiany swagger.json w repozytorium, są obsługiwane przez aplikację funkcji w interfejsie API adres URL definicji i klucz zanotowaną w kroku 1.c.

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie — samouczek](functions-api-definition-getting-started.md). Spróbuj nasze wskazówki, aby zobaczyć definicji OpenAPI w akcji.
* [Azure repozytorium GitHub funkcji](https://github.com/Azure/Azure-Functions/). Zapoznaj się z repozytorium funkcji, aby przesłać nam swoją opinię na Podgląd pomocy technicznej definicji interfejsu API. Tworzenie problem GitHub dla wszystkich danych, które chcesz wyświetlić zaktualizowane.
* [Dokumentacja dla deweloperów usługi Azure funkcji](functions-reference.md). Więcej informacji na temat kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
