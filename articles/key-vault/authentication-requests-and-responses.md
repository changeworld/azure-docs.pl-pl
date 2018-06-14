---
title: Uwierzytelnianie, żądań i odpowiedzi
description: Uwierzytelnianie usługi AD dotyczącego elementu za pomocą usługi Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 94080fb124478a4b8e196e341c335ca32321ecdf
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012133"
---
# <a name="authentication-requests-and-responses"></a>Uwierzytelnianie, żądań i odpowiedzi

Usługa Azure Key Vault obsługuje formatu JSON żądania i odpowiedzi. Żądania do usługi Azure Key Vault są skierowane do prawidłowy adres URL magazynu kluczy Azure przy użyciu protokołu HTTPS z niektórych parametrów adresu URL i JSON kodowany w treści żądania i odpowiedzi.

W tym temacie opisano charakterystyki dla usługi Azure Key Vault. Aby uzyskać ogólne informacje na temat używania interfejsów Azure REST, w tym uwierzytelniania/autoryzacji i sposobie uzyskania tokenu dostępu, zobacz [dokumentacja interfejsu API REST Azure](https://docs.microsoft.com/rest/api/).

## <a name="request-url"></a>Adres URL żądania  
 Operacje zarządzania kluczami Użyj HTTP DELETE, GET, poprawki, PUT i HTTP POST i HTTP POST użyj operacji kryptograficznych dla istniejących obiektów klucza. Klienci, którzy nie obsługują określonych zleceń HTTP także użyć POST protokołu HTTP za pomocą nagłówek ŻĄDANIA X-HTTP, aby określić zamierzone zlecenie; żądania, które normalnie nie wymagają treść powinna zawierać pustej treści, gdy przy użyciu metody POST protokołu HTTP, na przykład za pomocą POST zamiast usuwania.  

 Aby pracować z obiektami w usłudze Azure Key Vault, są następujące adresy URL:  

-   Aby utworzyć klucz o nazwie TESTKEY używany magazyn kluczy- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

-   Aby ZAIMPORTOWAĆ klucz o nazwie IMPORTEDKEY do użycia usługi Key Vault- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

-   Aby UZYSKAĆ klucz tajny o nazwie MYSECRET używany magazyn kluczy- `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

-   Aby ZAREJESTROWAĆ ona skrót przy użyciu klucza o nazwie TESTKEY używany magazyn kluczy- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

 Jako źródło dla żądania usługi Key Vault jest zawsze następujący,  `https://{keyvault-name}.vault.azure.net/`  

 Klucze zawsze są przechowywane w ścieżce /keys, kluczy tajnych zawsze są przechowywane w ścieżce /secrets.  

## <a name="api-version"></a>Wersja interfejsu API  
 Usługa Magazyn kluczy Azure obsługuje wersji protokołu ze względu na zgodność z klientów niskiego poziomu, mimo że nie wszystkie funkcje będą dostępne dla tych klientów. Klienci muszą używać `api-version` parametr ciągu do określania wersji protokołu, które obsługują one, ponieważ nie jest domyślnie zapytania.  

 Wersji protokołu usługi Azure Key Vault wykonaj datę numerowanie schematu za pomocą {YYYY}. {MM}. Format {DD}.  

## <a name="request-body"></a>Treść żądania  
 Zgodnie ze specyfikacją HTTP operacje GET nie mogą mieć treści żądania, a operacje POST i PUT, musi mieć treści żądania. Treść w operacji usuwania jest opcjonalne w protokole HTTP.  

 O ile nie zaznaczono inaczej w opisie działania, typ zawartości w treści żądania musi być application/json i musi zawierać serializacji zgodność obiekt JSON do typu zawartości.  

 Jeśli nie podano inaczej w opisie działania, nagłówek Accept żądania musi zawierać typ nośnika application/json.  

## <a name="response-body"></a>Treść odpowiedzi  
 Jeśli nie podano inaczej w opisie działania, typ zawartości treści odpowiedzi operacji pomyślnie i niepomyślnie będzie application/json i zawiera szczegółowe informacje o błędzie.  

## <a name="using-http-post"></a>Przy użyciu metody POST protokołu HTTP  
 Niektórzy klienci nie można używać niektórych zleceń HTTP, takie jak poprawki lub DELETE. Usługa Azure Key Vault obsługuje POST protokołu HTTP zamiast, dla tych klientów, pod warunkiem, że klienta obejmuje również nagłówka "X-HTTP-METHOD" zlecenia określonych oryginalnego HTTP. Obsługa HTTP POST jest odnotowany dla każdego interfejsu API zdefiniowane w tym dokumencie.  

## <a name="error-responses"></a>Błąd odpowiedzi  
 Obsługa błędów użyje kody stanu HTTP. Typowe wyniki są:  

-   2xx — Powodzenie: używany do normalnego działania. Treść odpowiedzi będzie zawierać oczekiwany wynik  

-   3xx — Przekierowanie: 304 "niezmodyfikowane" mogą być zwrócone do zrealizowania warunkowe operacje GET. Pozostałe kody 3xx można w przyszłości wskazująca zmiany DNS i ścieżkę.  

-   4xx — Błąd klienta: używana dla błędów w żądaniach, Brak kluczy, błędy składni, nieprawidłowe parametry, błędy uwierzytelniania, itp. Treść odpowiedzi zawiera wyjaśnienie szczegółowe informacje o błędzie.  

-   5xx — Błąd serwera: używany dla błędy wewnętrzne serwera. Treść odpowiedzi będzie zawierać informacje o błędzie podsumowania.  

 System jest przeznaczona do pracy związany z serwera proxy lub zapory. Dlatego klient może zostać wyświetlony innych kodów błędów.  

 Usługa Azure Key Vault również zwraca informacje o błędzie w treści odpowiedzi, jeśli występuje problem. Treść odpowiedzi jest w formacie JSON i ma postać:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Authentication  
 Wszystkie żądania do usługi Azure klucz magazynu musi zostać uwierzytelniony. Usługa Azure Key Vault obsługuje tokenów dostępu usługi Azure Active Directory, które mogą być uzyskane przy użyciu protokołu OAuth2 [[RFC6749](http://tools.ietf.org/html/rfc6749)]. 
 
 Aby uzyskać więcej informacji dotyczących rejestracji aplikacji i uwierzytelniania do użycia usługi Azure Key Vault, zobacz [zarejestrować aplikację klienta z usługą Azure AD](https://docs.microsoft.com/rest/api/index#register-your-client-application-with-azure-ad).
 
 Tokeny dostępu muszą być wysyłane do usługi przy użyciu nagłówka HTTP autoryzacji:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Jeśli nie podano tokenu dostępu, lub tokenu nie jest akceptowane przez usługę, błąd HTTP 401 zostanie zwrócona do klienta i będzie zawierać nagłówka WWW-Authenticate, na przykład:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry nagłówka WWW-Authenticate są:  

-   autoryzacji: adres usługi autoryzacji OAuth2, który może zostać użyty do uzyskania tokenu dostępu dla żądania.  

-   zasobu: Nazwa zasobu do użycia w żądania autoryzacji.  

## <a name="see-also"></a>Zobacz też  
 [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
