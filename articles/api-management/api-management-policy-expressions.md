---
title: Wyrażenia zasad API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat wyrażeń zasad w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: fa5e84ba62896969458b84cf014e2b35ee869df7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072167"
---
# <a name="api-management-policy-expressions"></a>Wyrażenia zasad API Management
W tym artykule omówiono składnię wyrażeń C# zasad to 7. Każde wyrażenie ma dostęp do niejawnie podanej zmiennej [kontekstowej](api-management-policy-expressions.md#ContextVariables) i dozwolone podzbiór typów .NET Framework. [](api-management-policy-expressions.md#CLRTypes)

Informacje dodatkowe:

- Zobacz, jak dostarczać informacje kontekstu do usługi wewnętrznej bazy danych. Aby podać te informacje, użyj [Ustawienia ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) i [Ustaw zasady nagłówka HTTP](api-management-transformation-policies.md#SetHTTPheader) .
- Zapoznaj się z tematem jak używać zasad [tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) do wstępnego autoryzowania dostępu do operacji na podstawie oświadczeń tokenów.
- Zobacz jak używać śladu [inspektora interfejsu API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) , aby zobaczyć, jak oceniane są zasady i wyniki tych ocen.
- Zobacz jak używać wyrażeń z zasadami [Pobierz z pamięci](api-management-caching-policies.md#GetFromCache) podręcznej i [Zapisz do pamięci](api-management-caching-policies.md#StoreToCache) podręcznej, aby skonfigurować buforowanie odpowiedzi API Management. Ustaw czas trwania, który jest zgodny z buforowaniem odpowiedzi usługi wewnętrznej bazy danych określonej przez `Cache-Control` dyrektywę usługi kopii zapasowej.
- Zobacz, jak wykonywać filtrowanie zawartości. Usuń elementy danych z odpowiedzi otrzymanej z zaplecza przy użyciu [przepływu sterowania](api-management-advanced-policies.md#choose) i [Ustaw](api-management-transformation-policies.md#SetBody) zasady dotyczące treści.
- Aby pobrać instrukcje zasad, zobacz repozytorium usługi [API Management — przykłady/zasady](https://github.com/Azure/api-management-samples/tree/master/policies) usługi GitHub.


## <a name="Syntax"></a>Obowiązuje
Wyrażenia pojedynczej instrukcji są ujęte `@(expression)`w `expression` , gdzie jest poprawnie sformułowaną C# instrukcją expression.

Wyrażenia wieloinstrukcji są ujęte w `@{expression}`. Wszystkie ścieżki kodu w wyrażeniach wieloinstrukcji muszą kończyć się `return` instrukcją.

## <a name="PolicyExpressionsExamples"></a>Pokazują

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

## <a name="PolicyExpressionsUsage"></a>Wykorzystywani
Wyrażenia mogą być używane jako wartości atrybutów lub wartości tekstowe w dowolnych [zasadach](api-management-policies.md) API Management (chyba że odwołanie do zasad określono inaczej).

> [!IMPORTANT]
> W przypadku używania wyrażeń zasad istnieje tylko ograniczona weryfikacja wyrażeń zasad, gdy zasady są zdefiniowane. Wyrażenia są wykonywane przez bramę w czasie wykonywania, wszystkie wyjątki generowane przez wyrażenia zasad powodują błąd czasu wykonania.

## <a name="CLRTypes"></a>Typy .NET Framework dozwolone w wyrażeniach zasad
Poniższa tabela zawiera listę typów .NET Framework i ich elementów członkowskich, które są dozwolone w wyrażeniach zasad.

|Type|Obsługiwane elementy członkowskie|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Wszyscy|
|Newtonsoft.Json.JsonConvert|Serializacjaobject, deserializacjaobject|
|Newtonsoft.Json.Linq.Extensions|Wszyscy|
|Newtonsoft.Json.Linq.JArray|Wszyscy|
|Newtonsoft.Json.Linq.JConstructor|Wszyscy|
|Newtonsoft.Json.Linq.JContainer|Wszyscy|
|Newtonsoft.Json.Linq.JObject|Wszyscy|
|Newtonsoft.Json.Linq.JProperty|Wszyscy|
|Newtonsoft.Json.Linq.JRaw|Wszyscy|
|Newtonsoft.Json.Linq.JToken|Wszyscy|
|Newtonsoft.Json.Linq.JTokenType|Wszyscy|
|Newtonsoft.Json.Linq.JValue|Wszyscy|
|System. Array|Wszyscy|
|System.BitConverter|Wszyscy|
|System.Boolean|Wszyscy|
|System.Byte|Wszyscy|
|System.Char|Wszyscy|
|System. Collections. Generic. dictionary < TKey, TValue >|Wszyscy|
|System.Collections.Generic.HashSet\<T>|Wszyscy|
|System.Collections.Generic.ICollection\<T>|Wszyscy|
|System. Collections. Generic. IDictionary < TKey, TValue >|Wszyscy|
|System.Collections.Generic.IEnumerable\<T>|Wszyscy|
|System.Collections.Generic.IEnumerator\<T>|Wszyscy|
|System.Collections.Generic.IList\<T>|Wszyscy|
|System.Collections.Generic.IReadOnlyCollection\<T>|Wszyscy|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Wszyscy|
|System.Collections.Generic.ISet\<T>|Wszyscy|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Wszyscy|
|System.Collections.Generic.List\<T>|Wszyscy|
|System.Collections.Generic.Queue\<T>|Wszyscy|
|System.Collections.Generic.Stack\<T>|Wszyscy|
|System.Convert|Wszyscy|
|System.DateTime|(Konstruktor), Add, addDays, addgodz. addmilisekunds, addmiesiącach, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, dzieńroku, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekundy, minute, MinValue, month, Now , Analizuj, drugi, Odejmij, Ticks, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Wszyscy|
|System.Decimal|Wszyscy|
|System.Double|Wszyscy|
|System.Exception|Wszyscy|
|System.Guid|Wszyscy|
|System.Int16|Wszyscy|
|System.Int32|Wszyscy|
|System.Int64|Wszyscy|
|System.IO.StringReader|Wszyscy|
|System.IO.StringWriter|Wszyscy|
|System.Linq.Enumerable|Wszyscy|
|System.Math|Wszyscy|
|System.MidpointRounding|Wszyscy|
|System .NET. WebUtility|Wszyscy|
|System.Nullable|Wszyscy|
|System.Random|Wszyscy|
|System.SByte|Wszyscy|
|System.Security.Cryptography.AsymmetricAlgorithm|Wszyscy|
|System.Security.Cryptography.CipherMode|Wszyscy|
|System.Security.Cryptography.HashAlgorithm|Wszyscy|
|System.Security.Cryptography.HashAlgorithmName|Wszyscy|
|System.Security.Cryptography.HMAC|Wszyscy|
|System.Security.Cryptography.HMACMD5|Wszyscy|
|System.Security.Cryptography.HMACSHA1|Wszyscy|
|System.Security.Cryptography.HMACSHA256|Wszyscy|
|System.Security.Cryptography.HMACSHA384|Wszyscy|
|System.Security.Cryptography.HMACSHA512|Wszyscy|
|System.Security.Cryptography.KeyedHashAlgorithm|Wszyscy|
|System.Security.Cryptography.MD5|Wszyscy|
|System.Security.Cryptography.Oid|Wszyscy|
|System.Security.Cryptography.PaddingMode|Wszyscy|
|System.Security.Cryptography.RNGCryptoServiceProvider|Wszyscy|
|System.Security.Cryptography.RSA|Wszyscy|
|System.Security.Cryptography.RSAEncryptionPadding|Wszyscy|
|System.Security.Cryptography.RSASignaturePadding|Wszyscy|
|System.Security.Cryptography.SHA1|Wszyscy|
|System.Security.Cryptography.SHA1Managed|Wszyscy|
|System.Security.Cryptography.SHA256|Wszyscy|
|System.Security.Cryptography.SHA256Managed|Wszyscy|
|System.Security.Cryptography.SHA384|Wszyscy|
|System.Security.Cryptography.SHA384Managed|Wszyscy|
|System.Security.Cryptography.SHA512|Wszyscy|
|System.Security.Cryptography.SHA512Managed|Wszyscy|
|System.Security.Cryptography.SymmetricAlgorithm|Wszyscy|
|System.Security.Cryptography.X509Certificates.PublicKey|Wszyscy|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Wszyscy|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name|
|System.Security.Cryptography.X509Certificates.X509Certificate|Wszyscy|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Wszyscy|
|System.Security.Cryptography.X509Certificates.X509ContentType|Wszyscy|
|System.Security.Cryptography.X509Certificates.X509NameType|Wszyscy|
|System.Single|Wszyscy|
|System.String|Wszyscy|
|System.StringComparer|Wszyscy|
|System.StringComparison|Wszyscy|
|System.StringSplitOptions|Wszyscy|
|System.Text.Encoding|Wszyscy|
|System.Text.RegularExpressions.Capture|Indeks, długość, wartość|
|System.Text.RegularExpressions.CaptureCollection|Liczba, element|
|System.Text.RegularExpressions.Group|Przechwytywanie, sukces|
|System.Text.RegularExpressions.GroupCollection|Liczba, element|
|System.Text.RegularExpressions.Match|Puste, grupy, wynik|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, Match, dopasowań, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Wszyscy|
|System.Text.StringBuilder|Wszyscy|
|System.TimeSpan|Wszyscy|
|System.TimeZone|Wszyscy|
|System.TimeZoneInfo.AdjustmentRule|Wszyscy|
|System.TimeZoneInfo.TransitionTime|Wszyscy|
|System.TimeZoneInfo|Wszyscy|
|System.Tuple|Wszyscy|
|System.UInt16|Wszyscy|
|System.UInt32|Wszyscy|
|System.UInt64|Wszyscy|
|System.Uri|Wszyscy|
|System.UriPartial|Wszyscy|
|System.Xml.Linq.Extensions|Wszyscy|
|System.Xml.Linq.XAttribute|Wszyscy|
|System.Xml.Linq.XCData|Wszyscy|
|System.Xml.Linq.XComment|Wszyscy|
|System.Xml.Linq.XContainer|Wszyscy|
|System.Xml.Linq.XDeclaration|Wszyscy|
|System.Xml.Linq.XDocument|Wszystkie, z wyjątkiem: Ładowanie|
|System.Xml.Linq.XDocumentType|Wszyscy|
|System.Xml.Linq.XElement|Wszyscy|
|System.Xml.Linq.XName|Wszyscy|
|System.Xml.Linq.XNamespace|Wszyscy|
|System.Xml.Linq.XNode|Wszyscy|
|System.Xml.Linq.XNodeDocumentOrderComparer|Wszyscy|
|System.Xml.Linq.XNodeEqualityComparer|Wszyscy|
|System.Xml.Linq.XObject|Wszyscy|
|System.Xml.Linq.XProcessingInstruction|Wszyscy|
|System.Xml.Linq.XText|Wszyscy|
|System.Xml.XmlNodeType|Wszyscy|

## <a name="ContextVariables"></a>Zmienna kontekstowa
Zmienna o nazwie `context` jest niejawnie dostępna w każdym [wyrażeniu](api-management-policy-expressions.md#Syntax)zasad. Jego członkowie zawierają informacje dotyczące programu `\request`. Wszystkie elementy członkowskie `context` są tylko do odczytu.

|Zmienna kontekstowa|Dozwolone metody, właściwości i wartości parametrów|
|----------------------|-------------------------------------------------------|
|context|[Interfejs API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Wdrożenie](#ref-context-deployment)<br /><br /> Który upłynął Przedział czasu w czasie między wartością sygnatury czasowej a bieżącym czasem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operacja](#ref-context-operation)<br /><br /> [Iloczyn](#ref-context-product)<br /><br /> [Żądanie](#ref-context-request)<br /><br /> RequestId: Identyfikator GUID — unikatowy identyfikator żądania<br /><br /> [Odpowiedź](#ref-context-response)<br /><br /> [Subskrypcja](#ref-context-subscription)<br /><br /> Znacznik czasu Data i godzina odebrania żądania<br /><br /> Śledzenie: bool-wskazuje, czy śledzenie jest włączone, czy wyłączone <br /><br /> [Użytkownicy](#ref-context-user)<br /><br /> [Zmienne](#ref-context-variables): IReadOnlyDictionary < ciąg, obiekt ><br /><br /> void Trace (Message: String)|
|<a id="ref-context-api"></a>Context. Interfejsu API|ID: ciąg<br /><br /> IsCurrentRevision: bool<br /><br />  Name: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Poprawka: ciąg<br /><br /> ServiceUrl [IUrl](#ref-iurl)<br /><br /> Wersja: ciąg |
|<a id="ref-context-deployment"></a>Context. Mieszczeniu|Region: ciąg<br /><br /> ServiceName: ciąg<br /><br /> Przystawki IReadOnlyDictionary < ciąg X509Certificate2 >|
|<a id="ref-context-lasterror"></a>context.LastError|Źródło: ciąg<br /><br /> Przyczyna: ciąg<br /><br /> Komunikat: ciąg<br /><br /> Zakres: ciąg<br /><br /> Sekcja: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> PolicyId: ciąg<br /><br /> Aby uzyskać więcej informacji o kontekście. LastError, zobacz [Obsługa błędów](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Context. Operacje|ID: ciąg<br /><br /> Metoda: ciąg<br /><br /> Name: ciąg<br /><br /> UrlTemplate: ciąg|
|<a id="ref-context-product"></a>Context. Iloczyn|Programowania Interfejs IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Grupowania Interfejs IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: ciąg<br /><br /> Name: ciąg<br /><br /> State: Wyliczenie ProductState {NotPublished, opublikowano}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>Context. Żądając|Jednostce [IMessageBody](#ref-imessagebody) lub `null` Jeśli żądanie nie ma treści.<br /><br /> Certyfikatu System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Nagłówki](#ref-context-request-headers): IReadOnlyDictionary < ciąg, ciąg [] ><br /><br /> IpAddress: ciąg<br /><br /> MatchedParameters: IReadOnlyDictionary < ciąg, ciąg ><br /><br /> Metoda: ciąg<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adres URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontekst ciągu. Request. Headers. GetValueOrDefault (headerName: String, DefaultValue: String)|headerName: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości nagłówka żądania oddzielone przecinkami `defaultValue` lub jeśli nagłówek nie zostanie znaleziony.|
|<a id="ref-context-response"></a>Context. Reakcji|Jednostce [IMessageBody](#ref-imessagebody)<br /><br /> [Nagłówki](#ref-context-response-headers): IReadOnlyDictionary < ciąg, ciąg [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: ciąg|
|<a id="ref-context-response-headers"></a>kontekst ciągu. Response. Headers. GetValueOrDefault (headerName: String, DefaultValue: String)|headerName: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości nagłówka odpowiedzi rozdzielane przecinkami `defaultValue` lub nie można znaleźć nagłówka.|
|<a id="ref-context-subscription"></a>Context. Ramach|CreatedTime: DateTime<br /><br /> EndDate Datę?<br /><br /> ID: ciąg<br /><br /> Klucz: ciąg<br /><br /> Name: ciąg<br /><br /> PrimaryKey: ciąg<br /><br /> SecondaryKey: ciąg<br /><br /> StartDate Datę?|
|<a id="ref-context-user"></a>Context. Użytkownicy|Adres e-mail: ciąg<br /><br /> FirstName: ciąg<br /><br /> Grupowania Interfejs IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: ciąg<br /><br /> Osob Interfejs IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: ciąg<br /><br /> Uwaga: ciąg<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: ciąg<br /><br /> Name: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Protokołów Ciąg < IEnumerable\><br /><br /> ServiceUrl [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: ciąg<br /><br /> Name: ciąg|
|<a id="ref-imessagebody"></a>IMessageBody|Jako < T\>(preserveContent: bool = false): Gdzie T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Metody `context.Request.Body.As<T>` `T`i `context.Response.Body.As<T>` są używane do odczytywania treści żądania i odpowiedzi w określonym typie. Domyślnie metoda używa oryginalnego strumienia treści wiadomości i renderuje go po powrocie. Aby uniknąć tego, że metoda operuje na kopii strumienia treści, ustaw `preserveContent` parametr na. `true` Przejdź [tutaj](api-management-transformation-policies.md#SetBody) , aby zobaczyć przykład.|
|<a id="ref-iurl"></a>IUrl|Host: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Port: int<br /><br /> [Zapytanie](#ref-iurl-query): IReadOnlyDictionary < ciąg, ciąg [] ><br /><br /> QueryString: ciąg<br /><br /> Schemat: ciąg|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: ciąg<br /><br /> Dostawca: ciąg|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Nagłówek: ciąg<br /><br /> Zapytanie: ciąg|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String, DefaultValue: String)|queryParameterName: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości parametrów zapytania oddzielone przecinkami `defaultValue` lub jeśli nie znaleziono parametru.|
|<a id="ref-context-variables"></a>Kontekst T. Zmienne. GetValueOrDefault < T\>(VariableName: String, DefaultValue: T)|VariableName: ciąg<br /><br /> DefaultValue T<br /><br /> Zwraca Rzutowanie wartości zmiennej na `T` typ `defaultValue` lub jeśli zmienna nie zostanie znaleziona.<br /><br /> Ta metoda zgłasza wyjątek, jeśli określony typ nie jest zgodny z rzeczywistym typem zwracanej zmiennej.|
|BasicAuthCredentials AsBasic (dane wejściowe: ten ciąg)|dane wejściowe: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość nagłówka żądanie autoryzacji uwierzytelniania podstawowego protokołu HTTP, metoda zwraca obiekt typu `BasicAuthCredentials`; w przeciwnym razie metoda zwraca wartość null.|
|bool TryParseBasic (dane wejściowe: ten ciąg, wynik: out BasicAuthCredentials)|dane wejściowe: ciąg<br /><br /> wynik: BasicAuthCredentials out<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość autoryzacji uwierzytelniania podstawowego http w nagłówku żądania, metoda zwraca `true` i parametr wynik zawiera wartość typu `BasicAuthCredentials`; w przeciwnym razie metoda zwraca `false`.|
|BasicAuthCredentials|Hasło: ciąg<br /><br /> UserId: ciąg|
|AsJwt JWT (dane wejściowe: ten ciąg)|dane wejściowe: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca obiekt typu `Jwt`; w przeciwnym razie metoda zwraca. `null`|
|bool TryParseJwt (dane wejściowe: ten ciąg, wynik: out JWT)|dane wejściowe: ciąg<br /><br /> wynik: out JWT<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca `true` i parametr wynik zawiera wartość typu `Jwt`; w przeciwnym razie metoda zwraca `false`.|
|Jwt|Algorytm: ciąg<br /><br /> Publiczn Ciąg < IEnumerable\><br /><br /> Oświadczeń IReadOnlyDictionary < ciąg, ciąg [] ><br /><br /> ExpirationTime Datę?<br /><br /> ID: ciąg<br /><br /> Wystawca: ciąg<br /><br /> IssuedAt: Datę?<br /><br /> NotBefore Datę?<br /><br /> Podmiot: ciąg<br /><br /> Typ: ciąg|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimname: ciąg<br /><br /> DefaultValue: ciąg<br /><br /> Zwraca wartości w postaci oddzielone przecinkami lub `defaultValue` nie można znaleźć nagłówka.|
|Byte [] Szyfruj (dane wejściowe: ten bajt [], alg: ciąg, klucz: Byte [], IV: Byte [])|dane wejściowe — tekst do zaszyfrowania<br /><br />alg — Nazwa algorytmu szyfrowania symetrycznego<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zaszyfrowany tekst.|
|Byte [] Szyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm)|dane wejściowe — tekst do zaszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />Zwraca zaszyfrowany tekst.|
|Byte [] Szyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm, klucz: Byte [], IV: Byte [])|dane wejściowe — tekst do zaszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zaszyfrowany tekst.|
|Byte [] Odszyfruj (dane wejściowe: ten bajt [], alg: ciąg, klucz: Byte [], IV: Byte [])|Tekst wejściowy-szyfr do odszyfrowania<br /><br />alg — Nazwa algorytmu szyfrowania symetrycznego<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zwykły tekst.|
|Byte [] Odszyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm)|Tekst wejściowy-szyfr do odszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />Zwraca zwykły tekst.|
|Byte [] Odszyfruj (dane wejściowe: ten bajt [], alg: System. Security. Cryptography. SymmetricAlgorithm, klucz: Byte [], IV: Byte [])|Tekst wejściowy-szyfr do odszyfrowania<br /><br />alg — algorytm szyfrowania<br /><br />klucz — klucz szyfrowania<br /><br />wektor inicjalizacji IV<br /><br />Zwraca zwykły tekst.|
|bool VerifyNoRevocation (dane wejściowe: this system. Security. Cryptography. x509. X509Certificate2)|Wykonuje weryfikację łańcucha X. 509 bez sprawdzania stanu odwołania do certyfikatu.<br /><br />Obiekt certyfikatu wejściowego<br /><br />Zwraca `true` czy Walidacja zakończyła się pomyślnie; `false` jeśli sprawdzanie poprawności zakończy się niepowodzeniem.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
