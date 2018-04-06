---
title: Ograniczenia i znane problemy podczas importowania interfejsu API usługi Azure API Management | Dokumentacja firmy Microsoft
description: Szczegóły znane problemy i ograniczenia dotyczące importowania do usługi Azure API Management przy użyciu interfejsu API otwarty, WSDL lub WADL formatów.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: ab4bc4024248675c6325159b5507add1274addc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy
## <a name="about-this-list"></a>Ta lista — informacje
Podczas importowania interfejsu API, może spotkać się z pewnymi ograniczeniami lub zidentyfikować problemy, które należy rozwiązać, aby można było pomyślnie zaimportować. Ten artykuł dokumentów, uporządkowane według format importu interfejsu API.

## <a name="open-api"> </a>Interfejs API otwarty/Swagger
W przypadku otrzymania błędy importowania dokument otwarty interfejs API, upewnij się, zweryfikowaniu jej — przy użyciu narzędzia Projektant w portalu Azure (projekt - Front End — Otwórz interfejs API specyfikacji Edytor), lub z innych firm narzędzia takie jak <a href="http://www.swagger.io">edytora programu Swagger</a>.

* Obsługiwane jest tylko format JSON OpenAPI.
* Schematy odwoływać się przy użyciu **$ref** właściwości nie może zawierać inne **$ref** właściwości.
* **$ref** wskaźniki nie mogą odwoływać się do plików zewnętrznych.
* **x-ms ścieżki** i **serwerach x** są obsługiwane tylko rozszerzenia.
* Niestandardowe rozszerzenia zostaną zignorowane podczas importowania i są nie zapisano lub zachowane eksportu.

> [!IMPORTANT]
> Zobacz to [dokumentu](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/) ważne informacje i wskazówki związane z OpenAPI importu.

## <a name="wsdl"> </a>WSDL
Pliki WSDL są używane do generowania interfejsów API przekazywanego SOAP lub służyć jako zaplecza interfejsu API SOAP-REST.

* **WSDL: import** — obecnie APIM nie obsługuje interfejsów API przy użyciu tego atrybutu. Klienci, należy scalić importowanych elementów w jednym dokumencie.
* **Komunikaty z wielu części** , APIM nie obsługuje obecnie komunikatów tego typu.
* **WCF wsHttpBinding** usług SOAP utworzone za pomocą programu Windows Communication Foundation należy użyć klasy basicHttpBinding — wsHttpBinding nie jest obsługiwane.
* **MTOM** usług przy użyciu mechanizmu MTOM <em>może</em> pracy. W tej chwili nie jest oferowany oficjalnego wsparcia.
* **Rekursja** typy, które są zdefiniowane cyklicznie (na przykład odwoływać się do siebie tablicę) nie są obsługiwane przez APIM.

## <a name="wadl"> </a>WADL
Obecnie nie istnieją żadne znane problemy importu WADL.
