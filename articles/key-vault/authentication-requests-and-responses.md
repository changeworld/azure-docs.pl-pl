---
title: Uwierzytelnianie, żądania i odpowiedzi
description: Uwierzytelniania AD za pomocą usługi Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ca486768cf56059328801b1b4b1036bb8aeece8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201350"
---
# <a name="authentication-requests-and-responses"></a>Uwierzytelnianie, żądania i odpowiedzi

Usługa Azure Key Vault obsługuje żądania w formacie JSON i odpowiedzi. Żądania do usługi Azure Key Vault są kierowane na prawidłowy adres URL magazynu Key platformy Azure za pośrednictwem protokołu HTTPS z niektóre parametry adresu URL i JSON zakodowane treści żądania i odpowiedzi.

Ten temat zawiera szczegółowe informacje o usłudze Azure Key Vault. Aby uzyskać ogólne informacje na temat korzystania z interfejsów REST platformy Azure, w tym uwierzytelniania/autoryzacji i uzyskiwanie tokenu dostępu, zobacz [dokumentacja interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>Adres URL żądania  
 Operacje zarządzania kluczami, użyj HTTP DELETE, GET, poprawki, PUT i POST protokołu HTTP i operacje kryptograficzne dla istniejących obiektów kluczy użyć żądania HTTP POST. Klienci, którzy nie obsługują określonych zleceń HTTP możesz skorzystać z żądania HTTP POST używania nagłówek X-HTTP-ŻĄDANIA, aby określić zamierzone zlecenie; żądań, które normalnie nie wymagają treść powinna zawierać pustą treść, korzystając z POST protokołu HTTP, na przykład gdy używanie żądania POST, zamiast usuwania.  

 Aby pracować z obiektami w usłudze Azure Key Vault, są następujące adresy URL:  

- Aby utworzyć klucz o nazwie TESTKEY w użycie usługi Key Vault — `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Aby ZAIMPORTOWAĆ wartość dla kucza zwanego IMPORTEDKEY do użycia usługi Key Vault — `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Aby UZYSKAĆ klucz tajny o nazwie MYSECRET w użycie usługi Key Vault — `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Do PODPISANIA szyfrowane za pomocą klucza o nazwie TESTKEY w użycie usługi Key Vault — `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Urząd dla żądania do usługi Key Vault jest zawsze w następujący sposób,  `https://{keyvault-name}.vault.azure.net/`  

  Klucze zawsze są przechowywane w ścieżce /keys, wpisów tajnych zawsze są przechowywane w ścieżce /secrets.  

## <a name="api-version"></a>Wersja interfejsu API  
 Usługi Azure Key Vault obsługuje wersji protokołu do zapewniania zgodności z klientami niższego poziomu, mimo że nie wszystkie funkcje będą dostępne dla tych klientów. Klienci muszą używać `api-version` parametr ciągu, aby określić wersję protokołu, które obsługują, ponieważ nie jest domyślnie zapytania.  

 Wersji protokołu w usłudze Azure Key Vault, wykonaj daty przy użyciu {YYYY} schematu numerowania. {MM}. Format {DD}.  

## <a name="request-body"></a>Treść żądania  
 Zgodnie z specyfikacją protokołu HTTP operacje GET nie mogą zawierać treści żądania, a operacje POST i PUT, musi mieć treści żądania. Treść w ramach operacji usuwania jest opcjonalna w protokole HTTP.  

 Jeśli nie określono inaczej w opisie działania, typ zawartości treści żądania musi być application/json i musi zawierać Zserializowany zgodność obiektu JSON do typu zawartości.  

 Jeśli nie określono inaczej w opisie działania, nagłówek żądania Accept musi zawierać typ nośnika application/json.  

## <a name="response-body"></a>Treść odpowiedzi  
 Jeśli nie określono inaczej w opisie działania, typ zawartości treści odpowiedzi zarówno udane, jak i nieudane operacje zostaną application/json i zawiera szczegółowe informacje o błędzie.  

## <a name="using-http-post"></a>Za pomocą żądania HTTP POST  
 Niektórzy klienci nie można używać niektórych zleceń HTTP, takich jak PATCH lub DELETE. Usługa Azure Key Vault obsługuje żądania HTTP POST jako alternatywę, dla tych klientów, pod warunkiem, że klient dołącza również nagłówek "X-HTTP-METHOD" zlecenia określonych oryginalnego HTTP. Należy zauważyć, obsługa żądania HTTP POST dla każdego interfejsu API, zdefiniowana w tym dokumencie.  

## <a name="error-responses"></a>Odpowiedzi na błędy  
 Obsługa błędów użyje kodów stanu HTTP. Typowe wyniki są:  

- 2xx — Powodzenie: Używane do normalnego działania. Treść odpowiedzi zawiera oczekiwany wynik  

- 3xx — przekierowania: 304 "Nie jest modyfikowany" mogą być zwrócone do spełnienia warunkowego GET. Inne kody 3xx może służyć w przyszłości do wskazania zmiany DNS i ścieżkę.  

- 4xx — Błąd klienta: Używany w żądaniach, brakujące klucze, błędy składniowe, nieprawidłowe parametry, błędy uwierzytelniania, itd. Treść odpowiedzi zawiera wyjaśnienie szczegółowy komunikat o błędzie.  

- 5xx — Błąd serwera: Używany do błędy wewnętrzne serwera. Treść odpowiedzi będzie zawierać informacje o błędzie podsumowania.  

  System jest przeznaczona do pracy związany z serwera proxy lub zapory. W związku z tym klient może zostać wyświetlony innych kodach błędów.  

  Usługa Azure Key Vault również zwraca informacje o błędzie w treści odpowiedzi w przypadku wystąpienia problemu. Treść odpowiedzi jest w formacie JSON i ma postać:  

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
 Wszystkie żądania do usługi Azure Key Vault musi zostać uwierzytelnione. Usługa Azure Key Vault obsługuje tokenów dostępu usługi Azure Active Directory, które mogą zostać uzyskane za pomocą protokołu OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Aby uzyskać więcej informacji na temat rejestrowania aplikacji i uwierzytelniania do użycia usługi Azure Key Vault, zobacz [zarejestrować aplikację kliencką w usłudze Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Tokeny dostępu muszą być wysyłane do usługi przy użyciu nagłówka autoryzacji HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Gdy token dostępu nie jest podany, lub gdy token nie jest akceptowana przez usługę, błędu HTTP 401 zostaną zwrócone do klienta i będzie zawierać nagłówka WWW-Authenticate, na przykład:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametry nagłówka WWW-Authenticate są następujące:  

-   Autoryzacja: Adres usługi autoryzacji OAuth2, która może być używana w celu uzyskania tokenu dostępu dla żądania.  

-   Zasób: Nazwa zasobu do użycia w żądaniu autoryzacji.  

## <a name="see-also"></a>Zobacz też  
 [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
