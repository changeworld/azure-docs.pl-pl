---
title: Wdrażanie i wywoływanie interfejsów API i interfejsów API REST w sieci web z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wdrażanie i wywoływania składnika web API i interfejsów API REST dla systemu przepływów pracy integracji w programie Azure Logic Apps
keywords: interfejsy API, interfejsy API REST, łączniki, przepływy pracy, integracji systemu w sieci Web, uwierzytelniania
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: c7a240bf5b7ed5e7780b90f438d2e336ee79f0b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Wdrażanie i wywoływania niestandardowych interfejsów API z logiki przepływów pracy aplikacji

Po [Tworzenie niestandardowych interfejsów API](./logic-apps-create-api-app.md) do użycia w przepływach pracy aplikacji logiki, należy wdrożyć swoje interfejsy API przed wywołaniem je. Można wdrożyć swoje interfejsy API jako [sieci web apps](../app-service/app-service-web-overview.md), ale warto wdrożyć swoje interfejsy API jako [aplikacje interfejsu API](../app-service/app-service-web-tutorial-rest-api.md), które ułatwią Ci pracę podczas kompilacji, hosta i korzystanie z interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w swoje interfejsy API — wystarczy go wdrożyć w aplikacji interfejsu API. Swoje interfejsy API mogą być hostowane na [usłudze Azure App Service](../app-service/app-service-web-overview.md), platformy jako — usługa (PaaS) oferta zapewnia wysoce skalowalną, prosty hosting interfejsu API.

Mimo że jakiegokolwiek interfejsu API można wywołać z aplikacji logiki, aby uzyskać najlepsze wyniki, Dodaj [OpenAPI (wcześniej Swagger) metadanych](http://swagger.io/specification/) opisujący Twój interfejs API operacji i parametry. Ten plik OpenAPI pomaga interfejsu API integrują się łatwiejsze i lepiej współpracuje z aplikacji logiki.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Wdrażanie aplikacji sieci web lub aplikacji interfejsu API interfejsu API

Przed niestandardowego interfejsu API można wywołać z aplikacji logiki, należy wdrożyć jako aplikacji sieci web lub aplikacji interfejsu API interfejsu API w usłudze Azure App Service. Ponadto aby Twoje OpenAPI pliku do odczytu przez projektanta aplikacji logiki, ustaw właściwości definicji interfejsu API i Włącz [współużytkowanie zasobów między źródłami (CORS) do udostępniania](../app-service/app-service-web-overview.md) dla aplikacji sieci web lub aplikacji interfejsu API.

1. W [portalu Azure](https://portal.azure.com), wybierz aplikację sieci web lub aplikacji interfejsu API.

2. W menu aplikacji, który zostanie otwarty, w obszarze **interfejsu API**, wybierz **definicji interfejsu API**. Ustaw **lokalizacji definicji interfejsu API** na adres URL pliku swagger.json OpenAPI.

   Adres URL pojawia się zwykle w następującym formacie: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Łącze do pliku OpenAPI do niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. W obszarze **interfejsu API**, wybierz **CORS**. Ustawienie zasad CORS dla **dozwolone źródła** do **' *'** (Zezwalaj na wszystkie).

   Ustawienie to pozwala żądania przy użyciu projektanta aplikacji logiki.

   ![Zezwala na żądania z projektanta aplikacji logiki do niestandardowego interfejsu API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Aby uzyskać więcej informacji, zobacz [hosta interfejsu API RESTful z CORS w usłudze Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Wywoływanie niestandardowego interfejsu API z logiki przepływów pracy aplikacji

Po skonfigurowaniu właściwości definicji interfejsu API i mechanizmu CORS wyzwalacze i akcje niestandardowego interfejsu API powinny być dostępne dla Ciebie do uwzględnienia w przepływie pracy aplikacji logiki. 

*  Aby wyświetlić OpenAPI URL witryny sieci Web, możesz wyszukać subskrypcji witryny sieci Web w Projektancie aplikacji logiki.

*  Aby wyświetlić dostępne akcje i dane wejściowe, wskazując dokument OpenAPI, użyj [HTTP + Swagger akcji](../connectors/connectors-native-http-swagger.md).

*  Aby wywołać jakiegokolwiek interfejsu API, łącznie z interfejsów API, które nie mają lub udostępnić dokument OpenAPI zawsze można utworzyć żądania z [akcji HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Kolejne kroki

* [Łącznik niestandardowy — omówienie](../logic-apps/custom-connector-overview.md)