---
title: Ograniczenia i szczegóły obsługi formatów INTERFEJSU API
titleSuffix: Azure API Management
description: Szczegółowe informacje o znanych problemach i ograniczeniach dotyczących formatów Open API, WSDL i WADL w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513375"
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy

## <a name="about-this-list"></a>O tej liście

Podczas importowania interfejsu API, można natknąć się na pewne ograniczenia lub zidentyfikować problemy, które muszą zostać naprawione, zanim będzie można pomyślnie wykonać import. W tym artykule dokumentuje te ograniczenia, uporządkowane według formatu importu interfejsu API. Opisano w nim również sposób działania eksportu OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Ograniczenia importu OpenAPI/Swagger

Jeśli podczas importowania dokumentu OpenAPI są nawoływanie błędów, upewnij się, że został on wcześniej zweryfikowany. Można to zrobić za pomocą projektanta w witrynie Azure portal (Projekt — Front End — Edytor specyfikacji OpenAPI) lub za pomocą narzędzia innej firmy, takiego jak <a href="https://editor.swagger.io">Edytor Swagger.</a>

### <a name="general"></a><a name="open-api-general"> </a>Ogólne

-   Wymagane parametry zarówno ścieżki, jak i kwerendy muszą mieć unikatowe nazwy. (W OpenAPI nazwa parametru musi być unikatowa tylko w obrębie lokalizacji, na przykład ścieżka, kwerenda, nagłówek. Jednak w zarządzania interfejsami API zezwalamy na operacje, które mają być dyskryminowane zarówno przez parametry ścieżki, jak i zapytania (które OpenAPI nie obsługuje). Dlatego wymagamy, aby nazwy parametrów były unikatowe w całym szablonie adresu URL).
-   `\$ref`wskaźniki nie mogą odwoływać się do plików zewnętrznych.
-   `x-ms-paths`i `x-servers` są jedynymi obsługiwanymi rozszerzeniami.
-   Rozszerzenia niestandardowe są ignorowane podczas importowania i nie są zapisywane ani zachowywane do eksportu.
-   `Recursion`- Zarządzanie interfejsami API nie obsługuje definicji zdefiniowanych rekursywnie (na przykład schematów odnoszących się do siebie).
-   Adres URL pliku źródłowego (jeśli jest dostępny) jest stosowany do względnych adresów URL serwera.
-   Definicje zabezpieczeń są ignorowane.
-   Definicje schematów wbudowanych dla operacji interfejsu API nie są obsługiwane. Definicje schematów są zdefiniowane w zakresie interfejsu API i można się do których odwoływać w zakresach żądań operacji interfejsu API lub odpowiedzi.
-   Zdefiniowany parametr adresu URL musi być częścią szablonu adresu URL.
-   `Produces`słowo kluczowe, które opisuje typy MIME zwracane przez interfejs API, nie jest obsługiwane. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI w wersji 2

-   Obsługiwany jest tylko format JSON.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI w wersji 3

-   Jeśli `servers` wiele z nich jest określonych, zarządzanie interfejsami API spróbuje wybrać pierwszy adres URL HTTPs. Jeśli nie ma żadnych adresów URL HTTPs - pierwszy adres URL HTTP. Jeśli nie ma żadnych adresów URL HTTP - adres URL serwera będzie pusty.
-   `Examples`nie jest obsługiwany, `example` ale jest.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mechanizmy importowania, aktualizowania i eksportowania OpenAPI

### <a name="add-new-api-via-openapi-import"></a>Dodawanie nowego interfejsu API za pomocą importu OpenAPI

Dla każdej operacji znalezionej w dokumencie OpenAPI zostanie utworzona nowa operacja `operationId` z `summary` nazwą zasobu platformy Azure i nazwą wyświetlaną ustawioną odpowiednio na i odpowiednio. `operationId`normalizuje się zgodnie z zasadami opisanymi poniżej. `summary`wartość jest importowana w stanie, a jej długość jest ograniczona do 300 znaków.

Jeśli `operationId` nie jest określony (który jest, nie obecny `null`lub pusty), wartość nazwy zasobu platformy Azure zostanie `get-foo`wygenerowana przez połączenie metody HTTP i szablonu ścieżki, na przykład .

Jeśli `summary` nie jest określony (czyli nie `null`obecny lub `display name` pusty), `operationId`wartość zostanie ustawiona na . Jeśli `operationId` nie zostanie określona, wartość nazwy wyświetlanej zostanie wygenerowana przez `Get - /foo`połączenie metody HTTP i szablonu ścieżki, na przykład .

### <a name="update-an-existing-api-via-openapi-import"></a>Aktualizowanie istniejącego interfejsu API za pomocą importu OpenAPI

Podczas importowania istniejący interfejs API jest zmieniany w celu dopasowania interfejsu API opisanego w dokumencie OpenAPI. Każda operacja w dokumencie OpenAPI jest dopasowywalany do istniejącej operacji, porównując jej `operationId` wartość z nazwą zasobu platformy Azure istniejącej operacji.

Jeśli zostanie znalezione dopasowanie, właściwości istniejącej operacji zostaną zaktualizowane "w miejscu".

Jeśli dopasowanie nie zostanie znalezione, zostanie utworzona nowa operacja przy użyciu reguł opisanych w powyższej sekcji. Dla każdej nowej operacji import będzie próbował skopiować zasady z istniejącej operacji przy użyciu tej samej metody HTTP i szablonu ścieżki.

Wszystkie istniejące niedopasowane operacje zostaną usunięte.

Aby import był bardziej przewidywalny, postępuj zgodnie z tymi wytycznymi:

- Upewnij się, `operationId` że należy określić właściwość dla każdej operacji.
- Powstrzymaj `operationId` się od zmiany po pierwszym imporcie.
- Nigdy `operationId` nie zmieniaj i http metody lub szablonu ścieżki w tym samym czasie.

### <a name="export-api-as-openapi"></a>Eksportowanie interfejsu API jako openapi

Dla każdej operacji jej nazwa zasobu platformy `operationId`Azure zostanie wyeksportowana `summary`jako nazwa wyświetlana, a nazwa wyświetlana zostanie wyeksportowana jako .
Reguły normalizacji dla operationId

- Konwersja na małe litery.
- Na przykład zastąp każdą sekwencję znaków niealfanowych pojedynczą `get-foo-bar-buzz-quix-`kreską. `GET-/foo/{bar}?buzz={quix}`
- Na przykład kreski przycinane `get-foo-bar-buzz-quix-` po obu stronach staną się`get-foo-bar-buzz-quix`
- Obcinanie, aby zmieścić 76 znaków, cztery znaki mniej niż maksymalny limit dla nazwy zasobu.
- Użyj pozostałych czterech znaków dla sufiksu deduplikacji, `-1, -2, ..., -999`jeśli to konieczne, w postaci .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Pliki WSDL są używane do tworzenia interfejsów API protokołu SOAP i SOAP-to-REST.

-   **Powiązania PROTOKOŁU SOAP** — obsługiwane są tylko powiązania PROTOKOŁU SOAP kodowania "document" i "literal". Nie ma obsługi stylu "rpc" lub kodowania PROTOKOŁU SOAP.
-   **WSDL:Import —** ten atrybut nie jest obsługiwany. Klienci powinni scalić import w jeden dokument.
-   **Wiadomości z wieloma częściami** — te typy wiadomości nie są obsługiwane.
-   **WCF wsHttpBinding** — usługi SOAP utworzone za pomocą programu Windows Communication Foundation powinny używać podstawowych funkcjiHttpBinding — wsHttpBinding nie jest obsługiwany.
-   **MTOM** - Usługi korzystające z MTOM <em>mogą</em> działać. Oficjalne wsparcie nie jest obecnie oferowane.
-   **Rekursja** — typy, które są zdefiniowane rekursywnie (na przykład odnoszą się do tablicy siebie) nie są obsługiwane przez APIM.
-   **Wiele obszarów nazw** — wiele obszarów nazw może być używanych w schemacie, ale tylko docelowy obszar nazw może być używany do definiowania części wiadomości. Przestrzenie nazw inne niż miejsce docelowe, które są używane do definiowania innych elementów wejściowych lub wyjściowych, nie są zachowywane. Chociaż taki dokument WSDL można zaimportować, podczas eksportowania wszystkie części wiadomości będą miały docelową przestrzeń nazw WSDL.
-   **Tablice** — transformacja SOAP-to-REST obsługuje tylko opakowane tablice pokazane w poniższym przykładzie:

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

## <a name="wadl"></a><a name="wadl"> </a>WADL

Obecnie nie są znane problemy z importem WADL.
