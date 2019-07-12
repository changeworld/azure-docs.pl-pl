---
title: Metadanych interfejsu OpenAPI w usłudze Azure Functions | Dokumentacja firmy Microsoft
description: Omówienie obsługi interfejsu OpenAPI w usłudze Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 1bc53fadfb622794703deaff560c9083ca6a1a10
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656832"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Obsługa metadanych interfejsu OpenAPI 2.0 w usłudze Azure Functions (wersja zapoznawcza)
Standard OpenAPI 2.0 (dawniej Swagger) Obsługa metadanych w usłudze Azure Functions jest funkcją w wersji zapoznawczej, która służy do pisania definicji interfejsu OpenAPI 2.0 w aplikacji funkcji. Ten plik może być następnie hostować przy użyciu aplikacji funkcji.

> [!IMPORTANT]
> Funkcja interfejsu OpenAPI w wersji zapoznawczej jest obecnie dostępna tylko w środowisku uruchomieniowym w wersji 1.x. Informacje na temat tworzenia aplikacji funkcji w wersji 1.x [można znaleźć tutaj](./functions-versions.md#creating-1x-apps).

[Metadanych interfejsu OpenAPI](https://swagger.io/) umożliwia funkcji, który jest hostem interfejsu API REST do użycia przez cały szereg innych programów. Oprogramowanie zawiera oferty firmy Microsoft, takich jak usługi PowerApps i [funkcji API Apps w usłudze Azure App Service](../app-service/overview.md), narzędzi deweloperskich innych firm, takich jak [Postman](https://www.getpostman.com/docs/importing_swagger), i [wiele innych pakietów](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Zalecamy rozpoczęcie od [Wprowadzenie — samouczek](./functions-api-definition-getting-started.md) a następnie powrotu do tego dokumentu, aby dowiedzieć się więcej na temat określonych funkcji.

## <a name="enable"></a>Włącz obsługę definicji interfejsu OpenAPI
Można skonfigurować wszystkie ustawienia interfejsu OpenAPI na **definicji interfejsu API** strony w aplikacji funkcji **funkcje platformy**.

> [!NOTE]
> Funkcja definicji interfejsu API nie jest obecnie obsługiwana dla środowiska uruchomieniowego w wersji beta.

Aby umożliwić wygenerowanie dyrektyw hostowanej definicji interfejsu OpenAPI i definicję przewodnika Szybki Start, należy ustawić **źródło definicji interfejsu API** do **— funkcja (wersja zapoznawcza)** . **Zewnętrzny adres URL** umożliwia funkcję, aby użyć definicji interfejsu OpenAPI, która ma hostowaną w innym miejscu.

## <a name="generate-definition"></a>Generuj szkielet struktury Swagger z metadanych funkcji
Szablon może pomóc rozpocząć pisanie pierwszego definicja interfejsu OpenAPI. Funkcja szablonu definicji tworzy rozrzedzoną definicję interfejsu OpenAPI za pomocą wszystkie metadane w pliku function.json dla poszczególnych funkcji wyzwalacza HTTP. Musisz podać więcej informacji na temat interfejsu API z [specyfikacji interfejsu OpenAPI](https://swagger.io/specification/), takie jak szablony żądań i odpowiedzi.

Aby uzyskać instrukcje krok po kroku, zobacz [Wprowadzenie — samouczek](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Dostępne szablony

|Name (Nazwa)| Opis |
|:-----|:-----|
|Wygenerowaną definicję|Definicji interfejsu OpenAPI z maksymalną ilością informacji, które można wywnioskować na podstawie istniejących metadanych funkcji.|

### <a name="quickstart-details"></a>Metadane uwzględnione w wygenerowanym definicji

Poniższa tabela reprezentuje ustawienia portalu Azure i odpowiadające im dane w function.json, ponieważ jest on zamapowany do wygenerowany szkielet struktury Swagger.

|Swagger.json|Portal UI|Function.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Ustawienia aplikacji funkcji** > **ustawień usługi App Service** > **Przegląd** > **adresu URL**|*Nie istnieje*
|[Ścieżki](https://swagger.io/specification/#paths-object-29)|**Integracja** > **wybrane metody HTTP**|Powiązania: Trasa
|[Ścieżka elementu](https://swagger.io/specification/#path-item-object-32)|**Integracja** > **szablon trasy**|Powiązania: Metody
|[Zabezpieczenia](https://swagger.io/specification/#security-scheme-object-112)|**klucze**|*Nie istnieje*|
|operationID *|**Trasy + dozwolone czasowniki**|Trasy i dozwolonych poleceń|

\*Identyfikator operacji jest wymagany tylko w celu integracji z usługami PowerApps i Flow.
> [!NOTE]
> Rozszerzenia x-ms-summary zawiera nazwę wyświetlaną w aplikacji logiki, PowerApps i Flow.
>
> Aby dowiedzieć się więcej, zobacz [Dostosowywanie definicji Swagger w usłudze PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Użyj ciągłej integracji/ciągłego wdrażania, aby ustawić definicji interfejsu API

 Należy włączyć definicji interfejsu API hostingu w portalu, przed włączeniem kontroli źródła zmodyfikować definicję interfejsu API z poziomu kontroli źródła. Wykonaj te instrukcje:

1. Przejdź do **definicji interfejsu API (wersja zapoznawcza)** w ustawieniach aplikacji funkcji.
   1. Ustaw **źródło definicji interfejsu API** do **funkcja**.
   1. Kliknij przycisk **szablon definicji interfejsu API z Generowanie** i następnie **Zapisz** do tworzenia definicji szablonu do modyfikowania później.
   1. Należy pamiętać, adres URL definicji interfejsu API i klucz.
1. [Konfigurowanie ciągłej integracji/ciągłego wdrażania (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Modyfikowanie swagger.json w kontroli źródła pod \site\wwwroot\.azurefunctions\swagger\swagger.json.

Teraz, zmiany swagger.json w repozytorium są obsługiwane przez aplikację funkcji w interfejsie API adres URL definicji i klucz, który zanotowaną w kroku 1.c.

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie — samouczek](functions-api-definition-getting-started.md). Wypróbuj nasze wskazówki, aby zobaczyć definicję interfejsu OpenAPI w działaniu.
* [Repozytorium github usługi funkcje Azure](https://github.com/Azure/Azure-Functions/). Zapoznaj się z repozytorium funkcji, aby przesłać nam swoją opinię w definicji interfejsu API pomocy technicznej (wersja zapoznawcza). Należy problem w usłudze GitHub dla wszystkich elementów, które chcesz zobaczyć zaktualizowane.
* [Dokumentacja dla deweloperów usługi Azure Functions](functions-reference.md). Więcej informacji na temat kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
