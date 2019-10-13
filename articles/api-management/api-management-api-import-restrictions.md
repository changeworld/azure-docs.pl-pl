---
title: Ograniczenia i znane problemy dotyczące importowania usługi Azure API Management API | Microsoft Docs
description: Szczegóły znanych problemów i ograniczeń dotyczących importowania do usługi Azure API Management przy użyciu formatów Open API, WSDL lub WADL.
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
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 0bf52b45bc315874c58be50bccbffa49090d554d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294346"
---
# <a name="api-import-restrictions-and-known-issues"></a>Ograniczenia importu interfejsu API i znane problemy

## <a name="about-this-list"></a>Informacje o tej liście

Podczas importowania interfejsu API mogą występować pewne ograniczenia lub zidentyfikować problemy, które należy skorygować przed pomyślnym zaimportowaniem. W tym artykule opisano te dokumenty uporządkowane według formatu importu interfejsu API.

## <a name="open-api"> </a>Openapi/Swagger

Jeśli otrzymujesz błędy podczas importowania dokumentu OpenAPI, upewnij się, że został on wcześniej sprawdzony. Można to zrobić przy użyciu narzędzia Projektant w Azure Portal (Edytor specyfikacji OpenAPI frontonu) lub za pomocą narzędzi innych firm, takich jak <a href="https://editor.swagger.io">Edytor Swagger</a>.

### <a name="open-api-general"> </a>Ogólne

-   Wymagane parametry w ścieżce i zapytaniu muszą mieć unikatowe nazwy. (W OpenAPI Nazwa parametru musi być unikatowa w obrębie lokalizacji, na przykład Path, Query, header. Jednakże w API Management zezwalamy na rozróżnienie operacji przy użyciu parametrów Path i Query (które nie są obsługiwane przez OpenAPI). Dlatego wymagamy, aby nazwy parametrów były unikatowe w ramach całego szablonu adresu URL.
-   wskaźniki **\$ref** nie mogą odwoływać się do plików zewnętrznych.
-   **ścieżki x-MS** i **x-Server** są jedynymi obsługiwanymi rozszerzeniami.
-   Rozszerzenia niestandardowe są ignorowane podczas importowania i nie są zapisywane ani zachowywane do eksportu.
-   **Rekursja** — API Management nie obsługuje definicji zdefiniowanych cyklicznie (na przykład schematów odnoszących się do siebie).
-   Adres URL pliku źródłowego (jeśli jest dostępny) jest stosowany do względnych adresów URL serwera.
-   Definicje zabezpieczeń zostały zignorowane.
-   Wbudowane definicje schematu dla operacji interfejsu API nie są obsługiwane. Definicje schematu są zdefiniowane w zakresie interfejsu API i można do nich odwoływać się w zakresach żądań lub odpowiedzi operacji interfejsu API.

### <a name="open-api-v2"> </a>Openapi wersja 2

-   Obsługiwany jest tylko format JSON.

### <a name="open-api-v3"> </a>Openapi wersja 3

-   Jeśli określono wiele **serwerów** , API Management spróbuje wybrać pierwszy adres URL https. Jeśli nie ma żadnych adresów URL HTTPs — pierwszy adres URL protokołu HTTP. Jeśli nie ma adresów URL protokołu HTTP, adres URL serwera będzie pusty.
-   **Przykłady** nie są obsługiwane, ale **przykład** to.
-   **Wieloczęściowe/formularz — dane** nie są obsługiwane.

## <a name="wsdl"> </a>Język WSDL

Pliki WSDL są używane do tworzenia protokołu SOAP przekazującego i interfejsów API protokołu SOAP-to-REST.

-   **Powiązania SOAP** — obsługiwane są tylko powiązania SOAP ze stylem "Document" i "literal". Nie ma obsługi stylu "RPC" ani kodowania SOAP.
-   **WSDL: import** — ten atrybut nie jest obsługiwany. Klienci powinni scalić Importy do jednego dokumentu.
-   **Komunikaty z wieloma częściami** — te typy komunikatów nie są obsługiwane.
-   **WCF WSHttpBinding** — usługa SOAP utworzona przy użyciu Windows Communication Foundation powinna używać BasicHttpBinding-WSHttpBinding nie jest obsługiwana.
-   **MTOM** — usługi korzystające z mechanizmu MTOM <em>mogą</em> funkcjonować. Oficjalne wsparcie nie jest w tej chwili oferowane.
-   **Rekursja** — typy, które są zdefiniowane cyklicznie (na przykład odnoszą się do tablicy samej) nie są obsługiwane przez APIM.
-   **Wiele przestrzeni nazw** — w schemacie można używać wielu przestrzeni nazw, ale tylko docelowa przestrzeń nazw może służyć do definiowania części komunikatów. Przestrzenie nazw inne niż element docelowy, które są używane do definiowania innych elementów wejściowych lub wyjściowych, nie są zachowywane. Mimo że ten dokument WSDL można zaimportować, w obszarze Eksportuj wszystkie części wiadomości będzie miał docelowy obszar nazw WSDL.
-   **Tablice** — transformacja protokołu SOAP do REST obsługuje tylko opakowane tablice przedstawione w poniższym przykładzie:

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

Obecnie nie ma żadnych znanych problemów z importem WADL.
