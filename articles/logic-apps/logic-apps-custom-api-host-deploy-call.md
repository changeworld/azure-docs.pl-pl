---
title: Wdrażanie i wywoływanie interfejsów API sieci web & interfejsów API REST z aplikacji Azure Logic Apps
description: Wdrażanie i wywoływanie interfejsów API sieci web & interfejsów API REST dla przepływów pracy integracji systemu w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790755"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Wdrażanie i wywoływanie niestandardowych interfejsów API z przepływów pracy w aplikacjach logiki azure

Po [utworzeniu niestandardowych interfejsów API](./logic-apps-create-api-app.md) do użycia w przepływach pracy aplikacji logiki, należy wdrożyć interfejsy API, zanim będzie można je wywołać. Interfejsy API można wdrażać jako [aplikacje sieci web,](../app-service/overview.md)ale należy rozważyć wdrożenie interfejsów API jako [aplikacji interfejsu API,](../app-service/app-service-web-tutorial-rest-api.md)które ułatwiają wykonywanie zadań podczas tworzenia, hostowania i korzystania z interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w interfejsach API — wystarczy wdrożyć kod do aplikacji interfejsu API. Interfejsy API można hostować w [usłudze Azure App Service](../app-service/overview.md)— ofercie platformy jako usługi (PaaS), która zapewnia wysoce skalowalny, łatwy hosting interfejsu API.

Chociaż można wywołać dowolny interfejs API z aplikacji logiki, aby uzyskać najlepsze środowisko, dodaj [metadane OpenAPI (wcześniej Swagger),](https://swagger.io/specification/) który opisuje operacje i parametry interfejsu API. Ten plik OpenAPI ułatwia integrację interfejsu API i lepiej współpracuje z aplikacjami logiki.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Wdrażanie interfejsu API jako aplikacji sieci Web lub aplikacji interfejsu API

Zanim będzie można wywołać niestandardowy interfejs API z aplikacji logiki, wdrożyć interfejs API jako aplikację sieci web lub aplikację interfejsu API do usługi Azure App Service. Ponadto, aby plik OpenAPI był czytelny dla projektanta aplikacji logiki, ustaw właściwości definicji interfejsu API i włącz [udostępnianie zasobów między źródłami (CORS)](../app-service/overview.md) dla aplikacji sieci web lub aplikacji interfejsu API.

1. W [witrynie Azure portal](https://portal.azure.com)wybierz aplikację sieci Web lub aplikację interfejsu API.

2. W menu aplikacji, które się otworzy, w obszarze **API**wybierz pozycję **Definicja interfejsu API**. Ustaw **lokalizację definicji interfejsu API** na adres URL pliku OpenAPI swagger.json.

   Zazwyczaj adres URL pojawia się w tym formacie:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Łącze do pliku OpenAPI dla niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. W obszarze **API**wybierz pozycję **CORS**. Ustaw zasadę CORS dla **dozwolonych źródeł na** **"*"** (zezwalaj na wszystkie).

   To ustawienie zezwala na żądania z projektanta aplikacji logiki.

   ![Zezwalaj na żądania od projektanta aplikacji logiki do niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Aby uzyskać więcej informacji, zobacz [Hostuj interfejs API RESTful z usługą CORS w usłudze Azure App Service.](../app-service/app-service-web-tutorial-rest-api.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Wywoływanie niestandardowego interfejsu API z przepływów pracy aplikacji logiki

Po skonfigurowaniu właściwości definicji interfejsu API i mechanizmu CORS wyzwalacze i akcje niestandardowego interfejsu API powinny być dostępne do uwzględnienia w przepływie pracy aplikacji logiki. 

*  Aby wyświetlić witryny sieci Web z adresami URL OpenAPI, możesz przeglądać witryny sieci Web subskrypcji w Projektancie aplikacji logiki.

*  Aby wyświetlić dostępne akcje i dane wejściowe, wskazując dokument OpenAPI, użyj [akcji HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Aby wywołać dowolny interfejs API, w tym interfejsy API, które nie mają lub udostępniają dokument OpenAPI, zawsze można utworzyć żądanie za pomocą [akcji HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Następne kroki

* [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md)