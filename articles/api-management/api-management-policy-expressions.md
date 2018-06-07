---
title: Wyrażenie zasad interfejsu API zarządzania platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wyrażeń zasad w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 5943357bc421bbae0caef7f0acd7aa3364813826
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597522"
---
# <a name="api-management-policy-expressions"></a>Wyrażenie zasad interfejsu API zarządzania
W tym artykule opisano zasady składnia wyrażeń jest C# 7. Każde wyrażenie ma dostęp do udostępnionego niejawnie [kontekstu](api-management-policy-expressions.md#ContextVariables) zmienną i dozwolonych [podzestawu](api-management-policy-expressions.md#CLRTypes) typów .NET Framework.  

Więcej informacji:

- Informacje o Podaj informacje o kontekście do usługi zaplecza. Użyj [ustawić parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) i [nagłówka HTTP ustawić](api-management-transformation-policies.md#SetHTTPheader) zasad, aby podać te informacje.
- Jak używać [JWT do zweryfikowania](api-management-access-restriction-policies.md#ValidateJWT) zasad do wstępnie autoryzacji dostępu do operacji na podstawie tokenu oświadczeń.   
- Jak używać [inspektora interfejsu API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) śledzenia, aby zobaczyć, jak są analizowane zasady i wyniki tych oceny.  
- Jak używać wyrażenia z [pobrać z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) i [magazynu pamięci podręcznej](api-management-caching-policies.md#StoreToCache) zasad można skonfigurować buforowanie odpowiedzi interfejsu API zarządzania. Ustaw czas trwania, który odpowiada buforowanie odpowiedzi usługi zaplecza określone przez usługę kopii `Cache-Control` dyrektywy.  
- Zobacz, jak wykonać filtrowanie zawartości. Usuń elementy danych z odpowiedzi otrzymanych z wewnętrznej bazy danych przy użyciu [sterowania przepływem](api-management-advanced-policies.md#choose) i [ustawić treść](api-management-transformation-policies.md#SetBody) zasad. 
- Aby pobrać deklaracji zasad, zobacz [interfejsu api zarządzania — przykłady/zasadami](https://github.com/Azure/api-management-samples/tree/master/policies) repozytorium github.  
  
  
##  <a name="Syntax"></a> Składnia  
 Pojedyncza instrukcja wyrażenia są ujęte w `@(expression)`, gdzie `expression` jest poprawnie sformułowanym instrukcji wyrażenia języka C#.  
  
 Wyrażenia instrukcji wielu są ujęte w `@{expression}`. Wszystkie ścieżki kodu w wielu instrukcji wyrażenia musi być zakończona odpowiadającą `return` instrukcji.  
  
##  <a name="PolicyExpressionsExamples"></a> Przykłady  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Użycie  
 Wyrażenia mogą być używane jako wartości atrybutu lub tekst w żadnym interfejsu API zarządzania [zasady](api-management-policies.md) (chyba że informacje o zasadach Określa, w przeciwnym razie).  
  
> [!IMPORTANT]
>  Użycie wyrażenia zasad, jest tylko ograniczony weryfikacji wyrażenia zasad po zdefiniowaniu zasad. Wyrażenia są wykonywane przez bramę w czasie wykonywania, wszelkie wyjątki generowane przez zasady wyrażenia spowodować błąd w czasie wykonywania.  
  
##  <a name="CLRTypes"></a> Typy .NET framework dozwolone w wyrażeniach zasad  
 Poniższa tabela zawiera listę typów .NET Framework i ich elementy członkowskie, które są dozwolone w wyrażeniach zasad.  
  
|Typ CLR|Obsługiwane metody|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JArray|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JConstructor|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JContainer|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JObject|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JProperty|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JRaw|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JToken|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JTokenType|Obsługiwane są wszystkie metody|  
|Newtonsoft.Json.Linq.JValue|Obsługiwane są wszystkie metody|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Wszyscy|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Wszyscy|  
|System.Collections.Generic.ISet<TKey, TValue>|Wszyscy|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Klucz-wartość|  
|System.Collections.Generic.List<TKey, TValue>|Wszyscy|  
|System.Collections.Generic.Queue<TKey, TValue>|Wszyscy|  
|System.Collections.Generic.Stack<TKey, TValue>|Wszyscy|  
|System.Convert|Wszyscy|  
|System.DateTime|Wszyscy|  
|System.DateTimeKind|Czas UTC|  
|System.DateTimeOffset|Wszyscy|  
|System.Decimal|Wszyscy|  
|System.Double|Wszyscy|  
|System.Guid|Wszyscy|  
|System.IEnumerable<T\>|Wszyscy|  
|System.IEnumerator<T\>|Wszyscy|  
|System.Int16|Wszyscy|  
|System.Int32|Wszyscy|  
|System.Int64|Wszyscy|  
|System.Linq.Enumerable<T\>|Obsługiwane są wszystkie metody|  
|System.Math|Wszyscy|  
|System.MidpointRounding|Wszyscy|  
|System.Nullable<T\>|Wszyscy|  
|System.Random|Wszyscy|  
|System.SByte|Wszyscy|  
|System.Security.Cryptography. HMACSHA384|Wszyscy|  
|System.Security.Cryptography. HMACSHA512|Wszyscy|  
|System.Security.Cryptography.HashAlgorithm|Wszyscy|  
|System.Security.Cryptography.HMAC|Wszyscy|  
|System.Security.Cryptography.HMACMD5|Wszyscy|  
|System.Security.Cryptography.HMACSHA1|Wszyscy|  
|System.Security.Cryptography.HMACSHA256|Wszyscy|  
|System.Security.Cryptography.KeyedHashAlgorithm|Wszyscy|  
|System.Security.Cryptography.MD5|Wszyscy|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Wszyscy|  
|System.Security.Cryptography.SHA1|Wszyscy|  
|System.Security.Cryptography.SHA1Managed|Wszyscy|  
|System.Security.Cryptography.SHA256|Wszyscy|  
|System.Security.Cryptography.SHA256Managed|Wszyscy|  
|System.Security.Cryptography.SHA384|Wszyscy|  
|System.Security.Cryptography.SHA384Managed|Wszyscy|  
|System.Security.Cryptography.SHA512|Wszyscy|  
|System.Security.Cryptography.SHA512Managed|Wszyscy|  
|System.Single|Wszyscy|  
|System.String|Wszyscy|  
|System.StringSplitOptions|Wszyscy|  
|System.Text.Encoding|Wszyscy|  
|System.Text.RegularExpressions.Capture|Wartość indeksu, długość,|  
|System.Text.RegularExpressions.CaptureCollection|Liczba elementów|  
|System.Text.RegularExpressions.Group|Przechwytywanie, Powodzenie|  
|System.Text.RegularExpressions.GroupCollection|Liczba elementów|  
|System.Text.RegularExpressions.Match|Puste, grup, wynik|  
|System.Text.RegularExpressions.Regex|(Konstruktor) IsMatch, zgodne, zgodne, Zastąp|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Wszyscy|  
|System.Tuple|Wszyscy|  
|System.UInt16|Wszyscy|  
|System.UInt32|Wszyscy|  
|System.UInt64|Wszyscy|  
|System.Uri|Wszyscy|  
|System.Xml.Linq.Extensions|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XAttribute|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XCData|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XComment|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XContainer|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XDeclaration|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XDocument|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XDocumentType|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XElement|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XName|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XNamespace|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XNode|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XNodeEqualityComparer|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XObject|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XProcessingInstruction|Obsługiwane są wszystkie metody|  
|System.Xml.Linq.XText|Obsługiwane są wszystkie metody|  
|System.Xml.XmlNodeType|Wszyscy|  
  
##  <a name="ContextVariables"></a> Zmienna kontekstu  
 Zmiennej o nazwie `context` jest niejawnie dostępne we wszystkich zasadach [wyrażenia](api-management-policy-expressions.md#Syntax). Jej elementów członkowskich zawierają informacje dotyczące `\request`. Wszystkie `context` elementy członkowskie są tylko do odczytu.  
  
|Zmienna kontekstu|Dozwolone metody, właściwości i wartości parametrów|  
|----------------------|-------------------------------------------------------|  
|Kontekst|Interfejs API: IApi<br /><br /> Wdrożenie<br /><br /> Czas: TimeSpan — interwał między wartość sygnatury czasowej i bieżący czas<br /><br /> LastError<br /><br /> Operacja<br /><br /> Product (Produkt)<br /><br /> Żądanie<br /><br /> Identyfikator żądania: Żądanie Unikatowy identyfikator - Guid<br /><br /> Odpowiedź<br /><br /> Subskrypcja<br /><br /> Sygnatury czasowej: Data i godzina - punkt w czasie, gdy otrzymano żądanie<br /><br /> Śledzenie: bool — wskazuje, czy śledzenie jest włączanie/wyłączanie <br /><br /> Użytkownik<br /><br /> Zmienne: IReadOnlyDictionary < string, object ><br /><br /> void Trace(message: string)|  
|context.Api|Identyfikator: ciąg<br /><br /> IsCurrentRevision: wartość logiczna<br /><br />  Nazwa: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Poprawka: ciąg<br /><br /> ServiceUrl: IUrl<br /><br /> Wersja: ciąg |  
|context.Deployment|Region: ciąg<br /><br /> ServiceName: ciąg<br /><br /> Certyfikaty: IReadOnlyDictionary < ciąg, X509Certificate2 >|  
|kontekst. LastError|Źródło: ciąg<br /><br /> Przyczyna: ciąg<br /><br /> Komunikat: ciąg<br /><br /> Zakres: ciąg<br /><br /> Sekcja: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> PolicyId: ciąg<br /><br /> Aby uzyskać więcej informacji o kontekście. LastError, zobacz [obsługi błędów](api-management-error-handling-policies.md).|  
|kontekst. Operacja|Identyfikator: ciąg<br /><br /> Metoda: ciąg<br /><br /> Nazwa: ciąg<br /><br /> UrlTemplate: ciąg|  
|context.Product|Interfejsy API: IEnumerable < IApi\><br /><br /> ApprovalRequired: wartość logiczna<br /><br /> Grupy: IEnumerable < IGroup\><br /><br /> Identyfikator: ciąg<br /><br /> Nazwa: ciąg<br /><br /> Stan: wyliczenia ProductState {NotPublished, opublikowaną}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: wartość logiczna|  
|kontekst. Żądanie|Treść: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Nagłówki: IReadOnlyDictionary < string, string [] ><br /><br /> Adres IP: ciąg<br /><br /> MatchedParameters: IReadOnlyDictionary < ciąg, ciąg ><br /><br /> Metoda: ciąg<br /><br /> OriginalUrl:IUrl<br /><br /> Adres URL: IUrl|  
|kontekst ciągu. Request.Headers.GetValueOrDefault (headerName: ciąg, wartość domyślna: ciąg)|headerName: ciąg<br /><br /> Wartość domyślna: ciąg<br /><br /> Zwraca wartości nagłówka żądania przecinkami lub `defaultValue` Jeżeli nie znaleziono nagłówka.|  
|kontekst. Odpowiedź|Treść: IMessageBody<br /><br /> Nagłówki: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: ciąg|  
|kontekst ciągu. Response.Headers.GetValueOrDefault (headerName: ciąg, wartość domyślna: ciąg)|headerName: ciąg<br /><br /> Wartość domyślna: ciąg<br /><br /> Zwraca wartości nagłówka odpowiedzi przecinkami lub `defaultValue` Jeżeli nie znaleziono nagłówka.|  
|kontekst. Subskrypcji|CreatedTime: daty i godziny<br /><br /> EndDate: DateTime?<br /><br /> Identyfikator: ciąg<br /><br /> Klucz: ciąg<br /><br /> Nazwa: ciąg<br /><br /> PrimaryKey: ciąg<br /><br /> Klucz pomocniczy: ciąg<br /><br /> Datą rozpoczęcia: DateTime?|  
|context.User|Wiadomości e-mail: ciąg<br /><br /> Imię: ciąg<br /><br /> Grupy: IEnumerable < IGroup\><br /><br /> Identyfikator: ciąg<br /><br /> Tożsamości: IEnumerable < IUserIdentity\><br /><br /> Nazwisko: ciąg<br /><br /> Uwaga: ciąg<br /><br /> RegistrationDate: daty i godziny|  
|IApi|Identyfikator: ciąg<br /><br /> Nazwa: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Protokoły: IEnumerable < ciąg\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Identyfikator: ciąg<br /><br /> Nazwa: ciąg|  
|IMessageBody|Jako < T\>(preserveContent: bool = false): gdy T: ciągu JObject, JToken, JArray, XNode klasy XElement, XDocument<br /><br /> `context.Request.Body.As<T>` i `context.Response.Body.As<T>` metody są używane do odczytu treści wiadomości w określonym typie żądań i odpowiedzi `T`. Domyślnie metoda korzysta z oryginalnego strumień treści komunikatu i okaże się niedostępne po zwraca. Aby tego uniknąć, ponieważ działają na kopii w strumieniu treści metody, należy ustawić `preserveContent` parametr `true`. Przejdź [tutaj](api-management-transformation-policies.md#SetBody) na przykład.|  
|IUrl|Host: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Port: int<br /><br /> Zapytań: IReadOnlyDictionary < string, string [] ><br /><br /> Ciąg zapytania: ciąg<br /><br /> Schemat: ciąg|  
|IUserIdentity|Identyfikator: ciąg<br /><br /> Dostawca: ciąg|  
|ISubscriptionKeyParameterNames|Nagłówek: ciąg<br /><br /> Zapytania: ciąg|  
|ciąg IUrl.Query.GetValueOrDefault (queryParameterName: ciąg, wartość domyślna: ciąg)|queryParameterName: ciąg<br /><br /> Wartość domyślna: ciąg<br /><br /> Zwraca wartości parametrów zapytania przecinkami lub `defaultValue` Jeśli parametr nie zostanie znaleziony.|  
|Kontekst T. Variables.GetValueOrDefault < T\>(nazwa_zmiennej: ciąg, wartość domyślna: T)|nazwa_zmiennej: ciąg<br /><br /> Wartość domyślna: T<br /><br /> Zwraca wartość zmiennej rzutowany na typ `T` lub `defaultValue` Jeśli zmienna nie zostanie znaleziony.<br /><br /> Ta metoda zgłasza wyjątek, jeśli określony typ nie jest zgodny z rzeczywisty typ zwrócony zmiennej.|  
|BasicAuthCredentials AsBasic(input: this string)|wprowadzania: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość nagłówka żądania uwierzytelnienia podstawowego uwierzytelniania HTTP, metoda zwraca obiekt typu `BasicAuthCredentials`; w przeciwnym razie metoda zwraca wartość null.|  
|wartość logiczna TryParseBasic (dane wejściowe: ten ciąg, wynik: limit BasicAuthCredentials)|wprowadzania: ciąg<br /><br /> wynik: limit BasicAuthCredentials<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość autoryzacji HTTP uwierzytelnianie podstawowe w nagłówku żądania metoda zwraca `true` i parametr wynik zawiera wartości typu `BasicAuthCredentials`; w przeciwnym razie metoda zwraca `false`.|  
|BasicAuthCredentials|Hasło: ciąg<br /><br /> Nazwa użytkownika: ciąg|  
|Jwt AsJwt(input: this string)|wprowadzania: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca obiekt typu `Jwt`; w przeciwnym razie metoda zwraca `null`.|  
|bool TryParseJwt (dane wejściowe: ten ciąg, wynik: limit Jwt)|wprowadzania: ciąg<br /><br /> wynik: limit Jwt<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca `true` i parametr wynik zawiera wartości typu `Jwt`; w przeciwnym razie metoda zwraca `false`.|  
|Jwt|Algorytm: ciąg<br /><br /> Grupy odbiorców: IEnumerable < ciąg\><br /><br /> Oświadczenia: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> Identyfikator: ciąg<br /><br /> Wystawca: ciąg<br /><br /> Nie wcześniej niż: DateTime?<br /><br /> Podmiot: ciąg<br /><br /> Typ: ciąg|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: ciąg<br /><br /> Wartość domyślna: ciąg<br /><br /> Zwraca przecinkami wartości oświadczeń lub `defaultValue` Jeżeli nie znaleziono nagłówka.|
|byte [] Szyfruj (wejściowych: tego typu byte [], alg: ciąg, klucz: byte [], iv:byte[])|dane wejściowe — zwykły tekst do zaszyfrowania<br /><br />alg — Nazwa algorytmu szyfrowania symetrycznego<br /><br />klucz — klucz szyfrowania<br /><br />IV - wektor inicjowania<br /><br />Zwraca szyfrowane w postaci zwykłego tekstu.|
|byte [] Szyfruj (wejściowych: tego typu byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|dane wejściowe — zwykły tekst do zaszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />Zwraca szyfrowane w postaci zwykłego tekstu.|
|byte [] Szyfruj (wejściowych: tego typu byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, klucz: byte [], iv:byte[])|dane wejściowe — zwykły tekst do zaszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />klucz — klucz szyfrowania<br /><br />IV - wektor inicjowania<br /><br />Zwraca szyfrowane w postaci zwykłego tekstu.|
|byte [] odszyfrowywania (wejściowych: tego typu byte [], alg: ciąg, klucz: byte [], iv:byte[])|dane wejściowe - tekst szyfrowania do odszyfrowania<br /><br />alg — Nazwa algorytmu szyfrowania symetrycznego<br /><br />klucz — klucz szyfrowania<br /><br />IV - wektor inicjowania<br /><br />Zwraca w postaci zwykłego tekstu.|
|byte [] odszyfrowywania (wejściowych: tego typu byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|dane wejściowe - tekst szyfrowania do odszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />Zwraca w postaci zwykłego tekstu.|
|byte [] odszyfrowywania (wejściowych: tego typu byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, klucz: byte [], iv:byte[])|Tekst wejściowy - wejściowych - szyfrowania do odszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />klucz — klucz szyfrowania<br /><br />IV - wektor inicjowania<br /><br />Zwraca w postaci zwykłego tekstu.|


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
