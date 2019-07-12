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
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: af550d3cdf359fc79b3cc2c799e531e5ec491c4e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613628"
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy

## <a name="about-this-list"></a>Ta lista — informacje

Podczas importowania interfejsu API, może spotkać się z pewnymi ograniczeniami lub identyfikowaniu problemów, które muszą zostać usunięty, zanim będzie można pomyślnie zaimportować. Ten artykuł dokumentów, te, uporządkowane według format importu interfejsu API.

## <a name="open-api"> </a>OpenAPI/Swagger

Jeśli otrzymujesz błędy importowania dokument OpenAPI, upewnij się, że zweryfikowaniu go wcześniej. Możesz to zrobić przy użyciu narzędzia Projektant w witrynie Azure portal (projekt - Front End — Edytor specyfikacji interfejsu OpenAPI) lub za pomocą narzędzia innej firmy takie jak <a href="https://editor.swagger.io">edytora programu Swagger</a>.

### <a name="open-api-general"> </a>Ogólne

-   Wymagane parametry zarówno ścieżka i kwerenda muszą mieć unikatowe nazwy. (W standardzie OpenAPI nazwę parametru tylko musi być unikatowa w lokalizacji, na przykład ścieżka, zapytanie, nagłówek. Jednak w usłudze API Management zezwalamy na operacje, aby być suma rozłączna przez parametry zarówno ścieżka i kwerenda, (które nie obsługuje interfejsu OpenAPI). That's Dlaczego firma Microsoft wymaga nazwy parametrów, aby była unikatowa w ramach całego szablonu adresu URL.)
-   **\$REF** wskaźniki nie można utworzyć zewnętrzne pliki odwołań.
-   **x-ms ścieżki** i **serwerach x** są obsługiwane tylko rozszerzenia.
-   Niestandardowe rozszerzenia są ignorowane podczas importowania i nie są zapisywane lub zachowywane na potrzeby eksportu.
-   **Rekursja** — API Management nie obsługuje rekursywnie definicji (na przykład, schematy odwołujące się do siebie).
-   Adres URL pliku źródłowego (jeśli jest dostępny) są stosowane do serwera względnych adresów URL.

### <a name="open-api-v2"> </a>Plik OpenAPI w wersji 2

-   Jest obsługiwany tylko format JSON.

### <a name="open-api-v3"> </a>W wersji 3 interfejsu OpenAPI

-   Jeśli wiele **serwerów** podano podejmie próbę wybierz pierwszy adres HTTPs URL usługi API Management. Jeśli nie ma żadnych adresów URL HTTPs — pierwszy adres URL protokołu HTTP. Jeśli nie ma żadnych adresów URL HTTP — adres URL serwera jest pusta.
-   **Przykłady** nie jest obsługiwane, ale **przykład** jest.
-   **Multipart/formularza data** nie jest obsługiwane.

> [!IMPORTANT]
> Zobacz ten [dokument](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/), aby uzyskać ważne informacje i wskazówki związane z importowaniem interfejsu OpenAPI.

## <a name="wsdl"> </a>WSDL

Pliki WSDL są używane do tworzenia przekazywanie protokołu SOAP i interfejsy API protokołu SOAP do stałej.

-   **Powiązania protokołu SOAP** — obsługiwane są tylko protokołu SOAP powiązania styl "dokument" i "literal" kodowania. Nie jest obsługiwane dla styl "rpc" lub kodowaniem SOAP.
-   **WSDL** — ten atrybut nie jest obsługiwane. Klienci powinno się scalać operacji importu do jednego dokumentu.
-   **Komunikaty, w których wiele części** -komunikatów tego typu nie są obsługiwane.
-   **WCF wsHttpBinding** — usługi protokołu SOAP utworzone za pomocą programu Windows Communication Foundation, należy użyć basicHttpBinding — wsHttpBinding nie jest obsługiwane.
-   **MTOM** — usług przy użyciu MTOM <em>może</em> pracy. Oficjalna Obsługa nie jest oferowana w tej chwili.
-   **Rekursja** — typy, które są zdefiniowane rekursywnie (na przykład, zobacz tablicę samodzielnie) nie są obsługiwane przez usługi APIM.
-   **Wiele przestrzeni nazw** — wiele przestrzeni nazw mogą być używane w schemacie, ale tylko docelowej przestrzeni nazw może służyć do definiowania części wiadomości. Przestrzenie nazw innej niż docelowa, które są używane do definiowania inne elementy wejściowe i wyjściowe nie są zachowywane. Mimo że można zaimportować dokumentu WSDL na eksport wszystkich części wiadomości są docelowy obszar nazw WSDL.
-   **Tablice** — obsługuje tylko zawinięty tablic przekształcania protokołu SOAP do REST, pokazano w poniższym przykładzie:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"> </a>WADL

Obecnie nie istnieją żadne znane problemy importu WADL.
