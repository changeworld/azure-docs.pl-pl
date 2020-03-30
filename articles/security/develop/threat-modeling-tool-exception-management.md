---
title: Zarządzanie wyjątkami — narzędzie Microsoft Threat Modeling Tool — Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728074"
---
# <a name="security-frame-exception-management--mitigations"></a>Ramka zabezpieczeń: Zarządzanie wyjątkami | Czynniki 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- Nie dołączaj węzła serviceDebug w pliku konfiguracyjnym](#servicedebug)</li><li>[WCF- Nie dołączaj węzła serviceMetadata w pliku konfiguracyjnym](#servicemetadata)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że prawidłowa obsługa wyjątków odbywa się w ASP.NET interfejsie API sieci Web](#exception)</li></ul> |
| **Aplikacja internetowa** | <ul><li>[Nie ujawniaj szczegółów zabezpieczeń w komunikatach o błędach](#messages)</li><li>[Implementowanie strony domyślnej obsługi błędów](#default)</li><li>[Ustawianie metody wdrażania na sprzedaż detaliczną w usługach IIS](#deployment)</li><li>[Wyjątki powinny zakończyć się niepowodzeniem](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- Nie dołączaj węzła serviceDebug w pliku konfiguracyjnym

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroki** | Usługi Windows Communication Framework (WCF) można skonfigurować w celu udostępnienia informacji debugowania. Informacje debugowania nie powinny być używane w środowiskach produkcyjnych. Tag `<serviceDebug>` określa, czy funkcja informacji debugowania jest włączona dla usługi WCF. Jeśli atrybut includeExceptionDetailInFaults jest ustawiona na true, informacje o wyjątku z aplikacji zostaną zwrócone do klientów. Osoby atakujące mogą wykorzystać dodatkowe informacje uzyskane z debugowania danych wyjściowych do instalowania ataków ukierunkowanych na platformę, bazę danych lub inne zasoby używane przez aplikację. |

### <a name="example"></a>Przykład
Następujący plik `<serviceDebug>` konfiguracyjny zawiera znacznik: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Wyłącz informacje debugowania w usłudze. Można to osiągnąć, usuwając `<serviceDebug>` znacznik z pliku konfiguracyjnego aplikacji. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- Nie dołączaj węzła serviceMetadata w pliku konfiguracyjnym

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Ogólny, NET Framework 3 |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroki** | Publiczne ujawnianie informacji o usłudze może zapewnić atakującym cenny wgląd w sposób, w jaki mogą wykorzystać tę usługę. Tag `<serviceMetadata>` włącza funkcję publikowania metadanych. Metadane usługi mogą zawierać poufne informacje, które nie powinny być publicznie dostępne. Co najmniej zezwalaj zaufanym użytkownikom na dostęp do metadanych i upewnij się, że niepotrzebne informacje nie są udostępniane. Jeszcze lepiej, całkowicie wyłączyć możliwość publikowania metadanych. Bezpieczna konfiguracja WCF `<serviceMetadata>` nie będzie zawierać tagu. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Upewnij się, że prawidłowa obsługa wyjątków odbywa się w ASP.NET interfejsie API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 5, MVC 6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Obsługa wyjątków w ASP.NET interfejsu API sieci Web,](https://www.asp.net/web-api/overview/error-handling/exception-handling) [sprawdzanie poprawności modelu w ASP.NET interfejsie API sieci Web](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Domyślnie większość nieprzechośniętych wyjątków w ASP.NET interfejsie API sieci Web jest tłumaczona na odpowiedź HTTP z kodem stanu`500, Internal Server Error`|

### <a name="example"></a>Przykład
Aby kontrolować kod stanu zwracany `HttpResponseException` przez interfejs API, można użyć, jak pokazano poniżej: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Przykład
Aby uzyskać dalszą kontrolę `HttpResponseMessage` nad odpowiedzią na wyjątek, klasa może być używana w sposób pokazany poniżej: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Aby złapać nieobsługiwały wyjątki, które nie są typu, `HttpResponseException`można użyć filtrów wyjątków. Filtry wyjątków `System.Web.Http.Filters.IExceptionFilter` implementują interfejs. Najprostszym sposobem, aby napisać filtr wyjątku `System.Web.Http.Filters.ExceptionFilterAttribute` jest pochodzić z klasy i zastąpić OnException metody. 

### <a name="example"></a>Przykład
Oto filtr, który `NotImplementedException` konwertuje wyjątki `501, Not Implemented`na kod stanu HTTP: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Istnieje kilka sposobów rejestrowania filtru wyjątków interfejsu API sieci Web:
- Przez działanie
- Według sterownika
- Globalnie

### <a name="example"></a>Przykład
Aby zastosować filtr do określonej akcji, dodaj filtr jako atrybut do akcji: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Przykład
Aby zastosować filtr do wszystkich akcji `controller`w programie , dodaj filtr `controller` jako atrybut do klasy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Przykład
Aby zastosować filtr globalnie do wszystkich kontrolerów interfejsu API `GlobalConfiguration.Configuration.Filters` sieci Web, dodaj wystąpienie filtru do kolekcji. Filtry wyjątków w tej kolekcji dotyczą dowolnej akcji kontrolera interfejsu API sieci Web. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Przykład
W przypadku sprawdzania poprawności modelu stan modelu można przekazać do metody CreateErrorResponse, jak pokazano poniżej: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Sprawdź łącza w sekcji referencje, aby uzyskać dodatkowe informacje na temat wyjątkowej obsługi i sprawdzania poprawności modelu w ASP.NET web API 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Nie ujawniaj szczegółów zabezpieczeń w komunikatach o błędach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Ogólne komunikaty o błędach są dostarczane bezpośrednio do użytkownika bez uwzględnienia poufnych danych aplikacji. Przykłady poufnych danych obejmują:</p><ul><li>Nazwy serwerów</li><li>Parametry połączeń</li><li>Nazwy użytkownika</li><li>Hasła</li><li>Procedury SQL</li><li>Szczegóły dynamicznych błędów SQL</li><li>Śledzenie stosu i wiersze kodu</li><li>Zmienne przechowywane w pamięci</li><li>Lokalizacje dysków i folderów</li><li>Punkty instalacji aplikacji</li><li>Ustawienia konfiguracji hosta</li><li>Inne szczegóły dotyczące aplikacji wewnętrznej</li></ul><p>Zalewkowanie wszystkich błędów w aplikacji i dostarczanie ogólnych komunikatów o błędach, a także włączanie błędów niestandardowych w ramach usług IIS pomoże zapobiec ujawnieniu informacji. Sql Server bazy danych i .NET obsługi wyjątków, wśród innych architektur obsługi błędów, są szczególnie pełne i bardzo przydatne do złośliwego użytkownika profilowania aplikacji. Nie należy bezpośrednio wyświetlać zawartość klasy pochodzące z .NET Exception klasy i upewnij się, że masz prawidłowe obsługi wyjątków, tak aby nieoczekiwany wyjątek nie jest przypadkowo wywoływane bezpośrednio do użytkownika.</p><ul><li>Dostarczanie ogólnych komunikatów o błędach bezpośrednio do użytkownika, które abstrakcyjne od określonych szczegółów znalezionych bezpośrednio w komunikacie o wyjątku/błędzie</li><li>Nie należy wyświetlać zawartości klasy wyjątku .NET bezpośrednio użytkownikowi</li><li>Zalewka wszystkich komunikatów o błędach i w razie potrzeby poinformować użytkownika za pośrednictwem ogólnego komunikatu o błędzie wysłanego do klienta aplikacji</li><li>Nie należy udostępniać zawartość Exception klasy bezpośrednio do użytkownika, `.ToString()`zwłaszcza zwraca wartość z , lub wartości Message lub StackTrace właściwości. Bezpiecznie rejestruj te informacje i wyświetlaj użytkownikowi bardziej nieszkodliwy komunikat</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementowanie strony domyślnej obsługi błędów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Okno dialogowe Edytowanie ustawień stron błędów ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroki** | <p>Gdy aplikacja ASP.NET ulegnie awarii i spowoduje błąd serwera wewnętrznego HTTP/1.x 500 lub konfiguracja funkcji (taka jak filtrowanie żądań) uniemożliwia wyświetlenie strony, zostanie wygenerowany komunikat o błędzie. Administratorzy mogą wybrać, czy aplikacja powinna wyświetlać przyjazny komunikat do klienta, szczegółowy komunikat o błędzie do klienta lub szczegółowy komunikat o błędzie tylko do localhost. Tag `<customErrors>` w web.config ma trzy tryby:</p><ul><li>**W dniu:** Określa, że błędy niestandardowe są włączone. Jeśli nie określono atrybutu defaultRedirect, użytkownicy zobaczą błąd ogólny. Błędy niestandardowe są wyświetlane klientom zdalnym i hostowi lokalnemu</li><li>**Wył.:** Określa, że błędy niestandardowe są wyłączone. Szczegółowe błędy ASP.NET są wyświetlane klientom zdalnym i lokalnemu hostowi</li><li>**RemoteOnly:** Określa, że błędy niestandardowe są wyświetlane tylko klientom zdalnym i że ASP.NET błędy są wyświetlane hostowi lokalnemu. Jest to wartość domyślna</li></ul><p>Otwórz `web.config` plik dla aplikacji/witryny i upewnij się, `<customErrors mode="On" />` że tag został zdefiniowany. `<customErrors mode="RemoteOnly" />`</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Ustawianie metody wdrażania na sprzedaż detaliczną w usługach IIS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Element wdrażania (schemat ustawień ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroki** | <p>Przełącznik `<deployment retail>` jest przeznaczony do użytku przez produkcyjne serwery IIS. Ten przełącznik służy do pomocy aplikacjom w uruchamianiu z najlepszą możliwą wydajnością i jak najmniejszymi możliwymi wyciekami informacji o zabezpieczeniach, wyłączając możliwość generowania danych wyjściowych śledzenia na stronie, wyłączając możliwość wyświetlania szczegółowych komunikatów o błędach użytkowników końcowych i wyłączenie przełącznika debugowania.</p><p>Często razy przełączniki i opcje, które są skoncentrowane na deweloperze, takie jak śledzenie żądań nie powiodło się i debugowanie, są włączone podczas aktywnego rozwoju. Zaleca się, aby metoda wdrażania na dowolnym serwerze produkcyjnym była ustawiona na sprzedaż detaliczną. otwórz plik machine.config i `<deployment retail="true" />` upewnij się, że pozostaje ustawiony na true.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Wyjątki powinny zakończyć się niepowodzeniem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Bezpieczna porażka](https://www.owasp.org/index.php/Fail_securely) |
| **Kroki** | Aplikacja powinna zakończyć się niepowodzeniem. Każda metoda, która zwraca wartość logiczną, na podstawie której podjęto pewną decyzję, powinna mieć blok wyjątków starannie utworzony. Istnieje wiele błędów logicznych, z powodu których problemy z zabezpieczeniami pełzają, gdy blok wyjątku jest napisany niedbale.|

### <a name="example"></a>Przykład
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Powyższa metoda zawsze zwróci True, jeśli wystąpi jakiś wyjątek. Jeśli użytkownik końcowy udostępnia nieprawidłowo sformułowany adres URL, `Uri()` który jest przestrzegany przez przeglądarkę, ale konstruktor nie, spowoduje to zgłosić wyjątek, a ofiara zostanie przejmiony prawidłowym, ale zniekształcony adres URL. 
