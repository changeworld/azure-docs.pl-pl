---
title: Ograniczenia i znane problemy podczas importowania interfejsu API usługi Azure API Management | Dokumentacja firmy Microsoft
description: Szczegółowe informacje o znanych problemów i ograniczeń dotyczących importowania do usługi Azure API Management przy użyciu otwartych interfejsów API, WSDL lub WADL formatów.
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
ms.openlocfilehash: a9f4a4ed4a8771f32a4d66aed2457a43abb92a63
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285881"
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy
## <a name="about-this-list"></a>Ta lista — informacje
Podczas importowania interfejsu API, może spotkać się z pewnymi ograniczeniami lub identyfikowaniu problemów, które muszą zostać usunięty, zanim będzie można pomyślnie zaimportować. Ten artykuł dokumentów, te, uporządkowane według format importu interfejsu API.

## <a name="open-api"> </a>Plik OpenAPI/Swagger
Jeśli otrzymujesz błędy importowania dokument OpenAPI, upewnij się, sprawdzeniu poprawności jej — przy użyciu narzędzia Projektant w witrynie Azure portal (Edytor specyfikacja interfejsu OpenAPI projekt - Front End —,), lub ze stroną trzecią narzędzia takie jak <a href="http://editor.swagger.io">edytora programu Swagger</a>.

* Jest obsługiwany tylko format JSON dla interfejsu OpenAPI.
* Wymagane parametry zarówno ścieżka i kwerenda muszą mieć unikatowe nazwy. (W standardzie OpenAPI nazwę parametru tylko musi być unikatowa w lokalizacji, np. ścieżka, zapytanie, nagłówek.  Jednak w usłudze API Management zezwalamy na operacje, aby być suma rozłączna przez parametry zarówno ścieżka i kwerenda, (które nie obsługuje interfejsu OpenAPI). W związku z tym firma Microsoft wymaga nazwy parametrów, aby była unikatowa w ramach całego szablonu adresu URL.)
* Schematy przywoływane przy użyciu **$ref** właściwości nie mogą zawierać inne **$ref** właściwości.
* **$ref** wskaźniki nie można utworzyć zewnętrzne pliki odwołań.
* **x-ms ścieżki** i **serwerach x** są obsługiwane tylko rozszerzenia.
* Niestandardowe rozszerzenia są ignorowane podczas importowania i są nie zapisano lub zachowywane na potrzeby eksportu.

> [!IMPORTANT]
> Zobacz ten [dokument](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/), aby uzyskać ważne informacje i wskazówki związane z importowaniem interfejsu OpenAPI.

## <a name="wsdl"> </a>WSDL
Pliki WSDL są używane do generowania interfejsy API SOAP przekazywane lub służyć jako zaplecza interfejsu API protokołu SOAP do REST.
* **Powiązania protokołu SOAP** — obsługiwane są tylko protokołu SOAP powiązania styl "dokument" i "literal" kodowania. Nie jest obsługiwane dla styl "rpc" lub kodowaniem SOAP.
* **WSDL** — ten atrybut nie jest obsługiwany. Klienci powinno się scalać operacji importu do jednego dokumentu.
* **Komunikaty, w których wiele części** -komunikatów tego typu nie są obsługiwane.
* **WCF wsHttpBinding** — usługi protokołu SOAP utworzone za pomocą programu Windows Communication Foundation, należy użyć basicHttpBinding — wsHttpBinding nie jest obsługiwana.
* **MTOM** — usług przy użyciu MTOM <em>może</em> pracy. Oficjalna Obsługa nie jest dostępna w tej chwili.
* **Rekursja** — typy, które są zdefiniowane rekursywnie (na przykład, zobacz tablicę samodzielnie) nie są obsługiwane przez usługi APIM.

## <a name="wadl"> </a>WADL
Obecnie nie istnieją żadne znane problemy importu WADL.
