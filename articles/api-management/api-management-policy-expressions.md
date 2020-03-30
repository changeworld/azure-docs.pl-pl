---
title: Wyrażenia zasad usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o wyrażeniach zasad w usłudze Azure API Management.
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
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244006"
---
# <a name="api-management-policy-expressions"></a>Wyrażenia zasad zarządzania interfejsami API
W tym artykule omówiono składnię wyrażeń zasad w języku C# 7. Każde wyrażenie ma dostęp do niejawnie dostarczonej zmiennej [kontekstowej](api-management-policy-expressions.md#ContextVariables) i dozwolonego [podzbioru](api-management-policy-expressions.md#CLRTypes) typów .NET Framework.

Więcej informacji:

- Zobacz, jak dostarczać informacje kontekstowe do usługi wewnętrznej bazy danych. Użyj [parametru Ustaw ciąg zapytania](api-management-transformation-policies.md#SetQueryStringParameter) i Ustaw zasady [nagłówka HTTP,](api-management-transformation-policies.md#SetHTTPheader) aby podać te informacje.
- Zobacz, jak używać [zasad Sprawdzania poprawności JWT](api-management-access-restriction-policies.md#ValidateJWT) do pre-autoryzowania dostępu do operacji na podstawie oświadczeń tokenu.
- Zobacz, jak używać śledzenia [Inspektora interfejsu API,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) aby zobaczyć, jak zasady są oceniane i wyniki tych ocen.
- Zobacz, jak używać wyrażeń z [zasadami Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) i [Magazynuj do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) w celu skonfigurowania buforowania odpowiedzi usługi API Management. Ustaw czas trwania, który odpowiada buforowania odpowiedzi usługi wewnętrznej bazy danych, zgodnie z dyrektywą usługi `Cache-Control` kopii zapasowej.
- Zobacz, jak przeprowadzić filtrowanie zawartości. Usuń elementy danych z odpowiedzi otrzymanej z wewnętrznej bazy danych przy użyciu [control przepływu](api-management-advanced-policies.md#choose) i ustaw zasady [treści.](api-management-transformation-policies.md#SetBody)
- Aby pobrać instrukcje zasad, zobacz [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub repo.


## <a name="syntax"></a><a name="Syntax"></a>Składni
Wyrażenia pojedynczej instrukcji są `@(expression)`ujęte w , gdzie `expression` jest dobrze sformułowaną instrukcją wyrażenia C#.

Wyrażenia wielu instrukcji są ujęte w `@{expression}`pliku . Wszystkie ścieżki kodu w wyrażeniach wielu instrukcji `return` musi kończyć się instrukcją.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Przykłady

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Użycia
Wyrażenia mogą być używane jako wartości atrybutów lub wartości tekstowe w dowolnych [zasadach](api-management-policies.md) zarządzania interfejsami API (chyba że odwołanie do zasad określa inaczej).

> [!IMPORTANT]
> Podczas korzystania z wyrażeń zasad, istnieje tylko ograniczona weryfikacja wyrażeń zasad, gdy zasady są zdefiniowane. Wyrażenia są wykonywane przez bramę w czasie wykonywania, wszelkie wyjątki generowane przez wyrażenia zasad powodują błąd środowiska uruchomieniowego.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>Typy programu .NET Framework dozwolone w wyrażeniach zasad
W poniższej tabeli wymieniono typy programu .NET Framework i ich elementy członkowskie, które są dozwolone w wyrażeniach zasad.

|Typ|Wspierani członkowie|
|--------------|-----------------------|
|Newtonsoft.Json.Formatowanie|Wszystkie|
|Newtonsoft.Json.JsonKonwert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Rozszerzenia|Wszystkie|
|Newtonsoft.Json.Linq.JArray|Wszystkie|
|Newtonsoft.Json.Linq.JKonstruktor|Wszystkie|
|Newtonsoft.Json.Linq.JKontainer|Wszystkie|
|Newtonsoft.Json.Linq.JObject|Wszystkie|
|Newtonsoft.Json.Linq.JProperty|Wszystkie|
|Newtonsoft.Json.Linq.JRaw|Wszystkie|
|Newtonsoft.Json.Linq.JToken|Wszystkie|
|Newtonsoft.Json.Linq.JTokenType|Wszystkie|
|Newtonsoft.Json.Linq.JValue|Wszystkie|
|System.array|Wszystkie|
|System.BitConverter|Wszystkie|
|System.Boolean|Wszystkie|
|System.Bajt|Wszystkie|
|System.char|Wszystkie|
|System.Collections.Generic.Dictionary<TKey, TValue>|Wszystkie|
|System.Collections.Generic.HashSet\<T>|Wszystkie|
|System.Collections.Generic.ICollection\<T>|Wszystkie|
|System.Collections.Generic.IDictionary<TKey, TValue>|Wszystkie|
|System.Collections.Generic.IWliczenia\<T>|Wszystkie|
|System.Collections.Generic.IEnumerator\<T>|Wszystkie|
|System.Collections.Generic.IList\<T>|Wszystkie|
|System.Collections.Generic.IReadOnlyCollection\<T>|Wszystkie|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Wszystkie|
|System.Collections.Generic.ISet\<T>|Wszystkie|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Wszystkie|
|System.Collections.Generic.List\<T>|Wszystkie|
|System.Collections.Generic.Queue\<T>|Wszystkie|
|System.Collections.Generic.Stack\<T>|Wszystkie|
|System.Convert|Wszystkie|
|System.datetime|(Konstruktor), Dodaj, DodajDzień, Dodaj Godziny, Dodaj Milenijne, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, DayOfWeek, DayOfYear, DaysInMonMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now , Parse, Second, Odejmij, Kleszcze, TimeOfDay, Dzisiaj, ToString, UtcNow, Rok|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Wszystkie|
|System.decimal|Wszystkie|
|System.double|Wszystkie|
|System.exception|Wszystkie|
|System.guid|Wszystkie|
|System.Int16|Wszystkie|
|System.Int32|Wszystkie|
|System.Int64|Wszystkie|
|System.IO.StringReader|Wszystkie|
|System.IO.StringWriter|Wszystkie|
|System.Linq.Wyliczalny|Wszystkie|
|System.math|Wszystkie|
|System.MidpointRounding|Wszystkie|
|System.Net.WebUtility|Wszystkie|
|System.nullable|Wszystkie|
|System.Losowo|Wszystkie|
|System.SByte|Wszystkie|
|System.Security.Cryptography.AsymmetricAlgorithm|Wszystkie|
|System.Security.Cryptography.CipherMode|Wszystkie|
|System.Security.Cryptography.HashAlgorithm|Wszystkie|
|System.Security.Cryptography.HashAlgorithmName|Wszystkie|
|System.Security.Cryptography.HMAC|Wszystkie|
|System.Security.Cryptography.HMACMD5|Wszystkie|
|System.Security.Cryptography.HMACSHA1|Wszystkie|
|System.Security.Cryptography.HMACSHA256|Wszystkie|
|System.Security.Cryptography.HMACSHA384|Wszystkie|
|System.Security.Cryptography.HMACSHA512|Wszystkie|
|System.Security.Cryptography.KeyedHashAlgorithm|Wszystkie|
|System.Security.Cryptography.MD5|Wszystkie|
|System.Security.Cryptography.Oid|Wszystkie|
|System.Security.Cryptography.PaddingMode|Wszystkie|
|System.Security.Cryptography.RNGCryptoServiceProvider|Wszystkie|
|System.Security.Cryptography.RSA|Wszystkie|
|System.Security.Cryptography.RSAEncryptionPadding|Wszystkie|
|System.Security.Cryptography.RSASignaturePadding|Wszystkie|
|System.Security.Cryptography.SHA1|Wszystkie|
|System.Security.Cryptography.SHA1Zarządzane|Wszystkie|
|System.Security.Cryptography.SHA256|Wszystkie|
|System.Security.Cryptography.SHA256Zarządzane|Wszystkie|
|System.Security.Cryptography.SHA384|Wszystkie|
|System.Security.Cryptography.SHA384Zarządzane|Wszystkie|
|System.Security.Cryptography.SHA512|Wszystkie|
|System.Security.Cryptography.SHA512Zarządzane|Wszystkie|
|System.Security.Cryptography.SymmetricAlgorithm|Wszystkie|
|System.Security.Cryptography.X509Certificates.PublicKey|Wszystkie|
|System.Security.Cryptography.X509Certaty.RSACertificateExtensions|Wszystkie|
|System.Security.Cryptography.X509Certyfikaty.X500DistinguishedName|Nazwa|
|System.Security.Cryptography.X509Certyfikaty.X509Certyfikat|Wszystkie|
|System.Security.Cryptography.X509Certyfikaty.X509Certificate2|Wszystkie|
|System.Security.Cryptography.X509Certificates.X509ContentType|Wszystkie|
|System.Security.Cryptography.X509Certyfikaty.X509NameType|Wszystkie|
|System.Single|Wszystkie|
|System.string|Wszystkie|
|Układ systemowy.StringComparer|Wszystkie|
|System.StringComparison|Wszystkie|
|System.StringSplitOptions|Wszystkie|
|System.Text.Encoding|Wszystkie|
|System.Text.RegularExpressions.Capture|Indeks, Długość, Wartość|
|System.Text.RegularExpressions.CaptureCollection|Liczba, pozycja|
|System.Text.RegularExpressions.Group|Przechwytuje, Sukces|
|System.Text.RegularExpressions.GroupKolekcja|Liczba, pozycja|
|System.Text.RegularExpressions.Match|Puste, Grupy, Wynik|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, Match, Mecze, Zamień, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Wszystkie|
|System.text.stringbuilder|Wszystkie|
|System.timespan|Wszystkie|
|System.TimeZone|Wszystkie|
|System.TimeZoneInfo.AdjustmentRule|Wszystkie|
|System.TimeZoneInfo.TransitionTime|Wszystkie|
|System.TimeZoneInfo|Wszystkie|
|System.Tutka|Wszystkie|
|System.UInt16|Wszystkie|
|System.UInt32|Wszystkie|
|System.UInt64|Wszystkie|
|System.uri|Wszystkie|
|System.UriPartial|Wszystkie|
|System.Xml.Linq.Rozszerzenia|Wszystkie|
|Atrybut System.Xml.Linq.XAttribute|Wszystkie|
|System.Xml.Linq.XCData|Wszystkie|
|System.Xml.Linq.XKomentuj|Wszystkie|
|System.Xml.Linq.XKontainer|Wszystkie|
|System.Xml.Linq.XDekadlacyjny|Wszystkie|
|System.Xml.Linq.XDocument|Wszystkie, z wyjątkiem: Load|
|System.Xml.Linq.XDocumentType|Wszystkie|
|System.Xml.Linq.XElement|Wszystkie|
|System.Xml.Linq.XName|Wszystkie|
|Obszar System.Xml.Linq.XNamespace|Wszystkie|
|System.Xml.Linq.XNode|Wszystkie|
|Układ systemowy.Xml.Linq.XNodeDocumentOrderComparer|Wszystkie|
|System.Xml.Linq.XNodeEqualityKomor|Wszystkie|
|System.Xml.Linq.XObject|Wszystkie|
|System.Xml.Linq.XProcessingInstrukcja|Wszystkie|
|System.Xml.Linq.XText|Wszystkie|
|Typ system.Xml.XmlNodeType|Wszystkie|

## <a name="context-variable"></a><a name="ContextVariables"></a>Zmienna kontekstowa
Zmienna `context` o nazwie jest niejawnie dostępna w każdym [wyrażeniu](api-management-policy-expressions.md#Syntax)zasad . Jej członkowie dostarczają informacji istotnych `\request`dla . Wszyscy członkowie `context` są tylko do odczytu.

|Zmienna kontekstowa|Dozwolone metody, właściwości i wartości parametrów|
|----------------------|-------------------------------------------------------|
|kontekst|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [wdrażania](#ref-context-deployment)<br /><br /> Data upływu: TimeSpan - przedział czasu między wartością sygnatury czasowej a bieżącym czasem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operacji](#ref-context-operation)<br /><br /> [Product (Produkt)](#ref-context-product)<br /><br /> [Żądanie](#ref-context-request)<br /><br /> Identyfikator żądania: Guid — unikatowy identyfikator żądania<br /><br /> [Odpowiedzi](#ref-context-response)<br /><br /> [Subskrypcja](#ref-context-subscription)<br /><br /> Sygnatura czasowa: DateTime - punkt w czasie, kiedy żądanie zostało odebrane<br /><br /> Śledzenie: bool - wskazuje, czy śledzenie jest włączone lub wyłączone <br /><br /> [Użytkownik](#ref-context-user)<br /><br /> [Zmienne](#ref-context-variables): IReadOnlyDictionary<string,><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>Kontekście. Api|Identyfikator: ciąg znaków<br /><br /> IsCurrentRevision: bool<br /><br />  Nazwa: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Wersja: ciąg<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Wersja: ciąg znaków |
|<a id="ref-context-deployment"></a>Kontekście. Wdrażania|Region: ciąg znaków<br /><br /> Nazwa usługi: ciąg<br /><br /> Certyfikaty: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Kontekście. LastError ( LastError )|Źródło: ciąg<br /><br /> Powód: ciąg<br /><br /> Komunikat: ciąg znaków<br /><br /> Zakres: ciąg znaków<br /><br /> Sekcja: ciąg znaków<br /><br /> Ścieżka: ciąg<br /><br /> PolicyId: ciąg<br /><br /> Aby uzyskać więcej informacji na temat kontekstu. LastError, zobacz [Obsługa błędów](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Kontekście. Operacji|Identyfikator: ciąg znaków<br /><br /> Metoda: ciąg znaków<br /><br /> Nazwa: ciąg<br /><br /> UrlTemplate: ciąg|
|<a id="ref-context-product"></a>Kontekście. Produktu|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> Wymagana homologacja: bool<br /><br /> Grupy: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Identyfikator: ciąg znaków<br /><br /> Nazwa: ciąg<br /><br /> Stan: wyliczenia Stan produktu {Niepublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> Wymagana subskrypcja: bool|
|<a id="ref-context-request"></a>Kontekście. Żądanie|Treść: [IMessageBody](#ref-imessagebody) lub `null` jeśli wniosek nie ma ciała.<br /><br /> Certyfikat: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Nagłówki](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: ciąg<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Metoda: ciąg znaków<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adres URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontekstu ciągu. Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: ciąg<br /><br /> defaultValue: ciąg<br /><br /> Zwraca wartości nagłówka żądania rozdzielone przecinkami lub `defaultValue` jeśli nagłówek nie zostanie znaleziony.|
|<a id="ref-context-response"></a>Kontekście. Odpowiedzi|Ciało: [IMessageBody](#ref-imessagebody)<br /><br /> [Nagłówki](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> Kod statusu: int<br /><br /> StatusReason: ciąg|
|<a id="ref-context-response-headers"></a>kontekstu ciągu. Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: ciąg<br /><br /> defaultValue: ciąg<br /><br /> Zwraca wartości nagłówka odpowiedzi rozdzielone przecinkami lub `defaultValue` jeśli nagłówek nie zostanie znaleziony.|
|<a id="ref-context-subscription"></a>Kontekście. Subskrypcji|CreatedTime: DateTime<br /><br /> Data końcowa: DateTime?<br /><br /> Identyfikator: ciąg znaków<br /><br /> Klawisz: ciąg znaków<br /><br /> Nazwa: ciąg<br /><br /> PrimaryKey: ciąg<br /><br /> SecondaryKey: ciąg<br /><br /> Data rozpoczęcia: DateTime?|
|<a id="ref-context-user"></a>Kontekście. Użytkownika|Wiadomość e-mail: ciąg znaków<br /><br /> Imię: ciąg<br /><br /> Grupy: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Identyfikator: ciąg znaków<br /><br /> Tożsamości: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Nazwisko: ciąg znaków<br /><br /> Uwaga: ciąg znaków<br /><br /> Data rejestracji: DateTime|
|<a id="ref-iapi"></a>IApi (włas i a)|Identyfikator: ciąg znaków<br /><br /> Nazwa: ciąg<br /><br /> Ścieżka: ciąg<br /><br /> Protokoły: Ciąg<ienumerable\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames SubscriptionKeyParameterNames SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames) SubscriptionKey|
|<a id="ref-igroup"></a>Grupa IGroup|Identyfikator: ciąg znaków<br /><br /> Nazwa: ciąg|
|<a id="ref-imessagebody"></a>IMessageBody|Jak<T\>(preserveContent: bool = false): Gdzie T: ciąg, bajt[], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Metody `context.Request.Body.As<T>` `context.Response.Body.As<T>` i metody są używane do odczytywania żądań i odpowiedzi treści wiadomości w określonym typie `T`. Domyślnie metoda używa oryginalnego strumienia treści wiadomości i renderuje go niedostępny po powrocie. Aby tego uniknąć, ponieważ metoda działa na kopii strumienia `true`treści, należy ustawić parametr na `preserveContent` . Przejdź [tutaj,](api-management-transformation-policies.md#SetBody) aby zobaczyć przykład.|
|<a id="ref-iurl"></a>IUrl (właso.|Host: ciąg znaków<br /><br /> Ścieżka: ciąg<br /><br /> Port: int<br /><br /> [Kwerenda](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> QueryString: ciąg<br /><br /> Schemat: ciąg znaków|
|<a id="ref-iuseridentity"></a>IUserIdentity (IUserIdentity)|Identyfikator: ciąg znaków<br /><br /> Dostawca: ciąg znaków|
|<a id="ref-isubscriptionkeyparameternames"></a>Nazwy iSubscriptionKeyParameterNames|Nagłówek: ciąg znaków<br /><br /> Zapytanie: ciąg|
|<a id="ref-iurl-query"></a>ciąg IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: ciąg<br /><br /> defaultValue: ciąg<br /><br /> Zwraca wartości parametrów kwerendy `defaultValue` rozdzielone przecinkami lub jeśli parametr nie zostanie znaleziony.|
|<a id="ref-context-variables"></a>T kontekstu. Zmienne.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: ciąg<br /><br /> defaultValue: T<br /><br /> Zwraca wartość zmienną `T` `defaultValue` rzutową do typu lub jeśli zmienna nie zostanie znaleziona.<br /><br /> Ta metoda zgłasza wyjątek, jeśli określony typ nie jest zgodny z rzeczywistym typem zwracanej zmiennej.|
|BasicAuthCredentials AsBasic(dane wejściowe: ten ciąg)|wejście: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość nagłówka żądania autoryzacji `BasicAuthCredentials`uwierzytelniania podstawowego HTTP, metoda zwraca obiekt typu; w przeciwnym razie metoda zwraca wartość null.|
|bool TryParseBasic(wejście: ten ciąg, wynik: obecnie BasicAuthCredentials)|wejście: ciąg<br /><br /> wynik: obecnie BasicAuthCredentials<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość autoryzacji uwierzytelniania `true` podstawowego HTTP w nagłówku żądania, metoda zwraca, a parametr wynikowy zawiera wartość typu; `BasicAuthCredentials` w przeciwnym `false`razie metoda zwraca .|
|BasicAuthCredentials (Podstawowe Programy Podstawowych)|Hasło: ciąg znaków<br /><br /> Identyfikator użytkownika: ciąg|
|Jwt AsJwt(dane wejściowe: ten ciąg)|wejście: ciąg<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu JWT, metoda zwraca obiekt typu; `Jwt` w przeciwnym `null`razie metoda zwraca .|
|bool TryParseJwt(wejście: ten ciąg, wynik: obecnie Jwt)|wejście: ciąg<br /><br /> wynik: obecnie Jwt<br /><br /> Jeśli parametr wejściowy zawiera prawidłową wartość tokenu `true` JWT, metoda zwraca, a parametr wynikowy zawiera wartość typu; `Jwt` w przeciwnym `false`razie metoda zwraca .|
|Jwt|Algorytm: ciąg<br /><br /> Publiczność: IEnumerable<string\><br /><br /> Oświadczenia: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Identyfikator: ciąg znaków<br /><br /> Wystawca: ciąg znaków<br /><br /> IssuedAt: DateTime?<br /><br /> Nieprzed: DateTime?<br /><br /> Temat: ciąg<br /><br /> Typ: ciąg znaków|
|ciąg Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: ciąg<br /><br /> defaultValue: ciąg<br /><br /> Zwraca wartości oświadczeń rozdzielonych `defaultValue` przecinkami lub jeśli nagłówek nie zostanie znaleziony.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - zwykły tekst do zaszyfrowania<br /><br />alg - nazwa symetrycznego algorytmu szyfrowania<br /><br />klucz - klucz szyfrowania<br /><br />iv - wektor inicjowania<br /><br />Zwraca zaszyfrowany tekst zwykły.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - zwykły tekst do zaszyfrowania<br /><br />alg - algorytm szyfrowania<br /><br />Zwraca zaszyfrowany tekst zwykły.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - zwykły tekst do zaszyfrowania<br /><br />alg - algorytm szyfrowania<br /><br />klucz - klucz szyfrowania<br /><br />iv - wektor inicjowania<br /><br />Zwraca zaszyfrowany tekst zwykły.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - tekst cypher do odszyfrowania<br /><br />alg - nazwa symetrycznego algorytmu szyfrowania<br /><br />klucz - klucz szyfrowania<br /><br />iv - wektor inicjowania<br /><br />Zwraca zwykły tekst.|
|byte[] Odszyfruj(dane wejściowe: ten bajt[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - tekst cypher do odszyfrowania<br /><br />alg - algorytm szyfrowania<br /><br />Zwraca zwykły tekst.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - tekst cypher do odszyfrowania<br /><br />alg - algorytm szyfrowania<br /><br />klucz - klucz szyfrowania<br /><br />iv - wektor inicjowania<br /><br />Zwraca zwykły tekst.|
|bool VerifyNoRevocation(wejście: ten System.Security.Cryptography.X509Certificates.X509Certificate2)|Wykonuje sprawdzanie poprawności łańcucha X.509 bez sprawdzania stanu odwołania certyfikatu.<br /><br />input - obiekt certyfikatu<br /><br />Zwraca, `true` jeśli sprawdzanie poprawności powiedzie się; `false` jeśli sprawdzanie poprawności nie powiedzie się.|


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
+ [Przekształcanie interfejsów API](transform-api.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
