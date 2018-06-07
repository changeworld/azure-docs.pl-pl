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
ms.openlocfilehash: 03d785898398cb0bcd7b43e8d7feab705bce4b34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598474"
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy
## <a name="about-this-list"></a>Ta lista — informacje
Podczas importowania interfejsu API, może spotkać się z pewnymi ograniczeniami lub zidentyfikować problemy, które należy rozwiązać, aby można było pomyślnie zaimportować. Ten artykuł dokumentów, uporządkowane według format importu interfejsu API.

## <a name="open-api"> </a>OpenAPI/Swagger
W przypadku otrzymania Importowanie dokumentu OpenAPI błędy, upewnij się, zweryfikowaniu jej — przy użyciu narzędzia Projektant w portalu Azure (projekt - Front End — OpenAPI specyfikacji Edytor) lub z innych firm narzędzia takie jak <a href="http://www.swagger.io">edytora programu Swagger</a>.

* Obsługiwane jest tylko format JSON OpenAPI.
* Parametry wymagane przez zarówno ścieżka i kwerenda muszą mieć unikatowe nazwy. (W OpenAPI nazwę parametru tylko musi być unikatowa w ramach danej lokalizacji, np. ścieżka, zapytania, nagłówka.  Jednak w usłudze API Management zezwolimy operacje można Suma rozłączna — zarówno ścieżka i kwerenda parametrami (które OpenAPI nie obsługuje). W związku z tym wymagamy nazwy parametrów, aby być unikatowe w obrębie całej szablon adresu URL.)
* Schematy odwoływać się przy użyciu **$ref** właściwości nie może zawierać inne **$ref** właściwości.
* **$ref** wskaźniki nie mogą odwoływać się do plików zewnętrznych.
* **x-ms ścieżki** i **serwerach x** są obsługiwane tylko rozszerzenia.
* Niestandardowe rozszerzenia zostaną zignorowane podczas importowania i są nie zapisano lub zachowane eksportu.

> [!IMPORTANT]
> Zobacz ten [dokument](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/), aby uzyskać ważne informacje i wskazówki związane z importowaniem interfejsu OpenAPI.

## <a name="wsdl"> </a>WSDL
Pliki WSDL są używane do generowania interfejsów API przekazywanego SOAP lub służyć jako zaplecza interfejsu API SOAP-REST.
* **Powiązania SOAP** — są obsługiwane tylko SOAP powiązania stylu "dokumentu" i "literal" kodowania. Nie jest obsługiwane dla styl "rpc" lub kodowania protokołu SOAP.
* **WSDL: import** -tego atrybutu nie jest obsługiwana. Klienci, należy scalić polecenie importuje jeden dokument.
* **Komunikaty z wielu części** -komunikatów tego typu nie są obsługiwane.
* **WCF wsHttpBinding** -usług SOAP utworzone za pomocą programu Windows Communication Foundation należy użyć klasy basicHttpBinding — wsHttpBinding nie jest obsługiwane.
* **MTOM** — usługi przy użyciu mechanizmu MTOM <em>może</em> pracy. W tej chwili nie jest oferowany oficjalnego wsparcia.
* **Rekursja** — typy, które są zdefiniowane cyklicznie (na przykład odwoływać się do siebie tablicę) nie są obsługiwane przez APIM.

## <a name="wadl"> </a>WADL
Obecnie nie istnieją żadne znane problemy importu WADL.
