---
title: Wdrażanie i wywoływanie interfejsów API i interfejsów API REST w sieci web z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wdrażanie i wywoływanie interfejsów API i interfejsów API REST w sieci web dla systemu dotyczących przepływów w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: a9049ba1fbd7d3bdce061d277f6a7a02d9b1e4b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60740408"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Wdrażanie i wywoływanie niestandardowych interfejsów API z przepływów pracy w usłudze Azure Logic Apps

Po zakończeniu [Tworzenie niestandardowych interfejsów API](./logic-apps-create-api-app.md) do użycia w przepływach pracy aplikacji logiki, należy wdrożyć swoje interfejsy API, zanim można je wywoływać. Interfejsy API, co można wdrożyć [aplikacje sieci web](../app-service/overview.md), ale zaleca się wdrożenie interfejsów API jako [aplikacje API apps](../app-service/app-service-web-tutorial-rest-api.md), które ułatwią Ci pracę po tworzenie, hostowanie i używanie interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w Twoich interfejsów API — wystarczy go wdrożyć w aplikacji interfejsu API. Możesz hostować swoje interfejsy API na [usługi Azure App Service](../app-service/overview.md), platformy as-a-service (PaaS) oferty, zapewniająca o wysokim stopniu skalowalności, łatwe hostowanie interfejsu API.

Chociaż możesz wywołać dowolny interfejs API z aplikacji logiki, aby uzyskać najlepsze wyniki, należy dodać [OpenAPI (wcześniej struktury Swagger) metadane](https://swagger.io/specification/) opisujący operacje i parametry interfejsu API. Ten plik OpenAPI pomaga interfejsu API łatwiej integrować i lepszą współpracę z usługą logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Wdrażanie interfejsu API jako aplikację sieci web lub aplikacji interfejsu API

Zanim wywołasz niestandardowego interfejsu API z aplikacji logiki Wdróż interfejs API jako aplikację sieci web lub aplikacji interfejsu API w usłudze Azure App Service. Ponadto aby pliku OpenAPI plik odczytany przez Projektant aplikacji logiki, ustaw właściwości definicji interfejsu API i Włącz [współużytkowanie zasobów między źródłami (cors)](../app-service/overview.md) dla aplikacji sieci web lub aplikacji interfejsu API.

1. W [witryny Azure portal](https://portal.azure.com), Wybieranie aplikacji sieci web lub aplikacji interfejsu API.

2. W menu aplikacji, które otwiera w obszarze **API**, wybierz **definicji interfejsu API**. Ustaw **Lokalizacja definicji interfejsu API** na adres URL pliku swagger.json interfejsu OpenAPI.

   Zazwyczaj adres URL pojawia się w następującym formacie: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link do pliku OpenAPI dla niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. W obszarze **API**, wybierz **CORS**. Ustawianie zasad CORS dla **dozwolone źródła** do **' *'** (Zezwalaj na wszystkie).

   To ustawienie zezwala na żądania z projektanta aplikacji logiki.

   ![Zezwolić na żądania z projektanta aplikacji logiki do niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Aby uzyskać więcej informacji, zobacz [hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Wywoływanie niestandardowego interfejsu API z logiki przepływów pracy aplikacji

Po skonfigurowaniu właściwości definicji interfejsu API i mechanizmu CORS, wyzwalacze i akcje niestandardowego interfejsu API powinny być dostępne dla Ciebie do uwzględnienia w przepływie pracy aplikacji logiki. 

*  Aby wyświetlić witryny, które mają adresy URL interfejsu OpenAPI, możesz przeglądać witryny sieci Web Twojej subskrypcji w Projektancie aplikacji logiki.

*  Aby wyświetlić dostępne akcje i dane wejściowe, wskazując dokument w standardzie OpenAPI, użyj [protokołu HTTP + Swagger akcji](../connectors/connectors-native-http-swagger.md).

*  Do wywołania dowolnego interfejsu API, między innymi interfejsów API, które nie zawiera lub udostępnić dokument w standardzie OpenAPI, zawsze można utworzyć żądania o [Akcja HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md)