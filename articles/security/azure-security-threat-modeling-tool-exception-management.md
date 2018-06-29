---
title: Azure Management - narzędzie Microsoft Threat modelowania — wyjątek | Dokumentacja firmy Microsoft
description: środki zaradcze w przypadku zagrożeń widoczne w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 3fae9390b41d12361b820e2c37601283b37bc302
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031716"
---
# <a name="security-frame-exception-management--mitigations"></a>Ramka zabezpieczeń: Zarządzanie wyjątkami | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **WCF** | <ul><li>[Usługi WCF - nie zawiera węzła serviceDebug w pliku konfiguracji](#servicedebug)</li><li>[Usługi WCF - nie zawiera węzła serviceMetadata w pliku konfiguracji](#servicemetadata)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że odpowiednich wyjątków odbywa się w interfejsie API sieci Web ASP.NET ](#exception)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Nie ujawniaj szczegóły zabezpieczeń w komunikatach o błędach ](#messages)</li><li>[Implementowanie obsługi strony błędów domyślne ](#default)</li><li>[Ustaw metody wdrażania na wersję handlową w usługach IIS](#deployment)</li><li>[Wyjątki powinna zakończyć się niepowodzeniem bezpiecznie](#fail)</li></ul> |

## <a id="servicedebug"></a>Usługi WCF - nie zawiera węzła serviceDebug w pliku konfiguracji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroki** | Usług Windows Communication Framework (WCF) można skonfigurować w celu ujawnienie informacji debugowania. Debugowanie informacji nie powinna być używana w środowisku produkcyjnym. `<serviceDebug>` Tagów Określa, czy dla usługi WCF jest włączona funkcja informacji debugowania. Jeśli includeExceptionDetailInFaults atrybut ma ustawioną wartość true, informacje o wyjątku z aplikacji, zostanie zwrócony do klientów. Osoby atakujące mogą korzystać z dodatkowych informacji, które będą mogli z debugowania dane wyjściowe można zainstalować ataków ukierunkowanych na platformę, bazy danych lub inne zasoby używane przez aplikację. |

### <a name="example"></a>Przykład
Następujący plik konfiguracji zawiera `<serviceDebug>` tagu: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Wyłącz informacji o debugowaniu w usłudze. Można to zrobić przez usunięcie `<serviceDebug>` tagu z pliku konfiguracji aplikacji. 

## <a id="servicemetadata"></a>Usługi WCF - nie zawiera węzła serviceMetadata w pliku konfiguracji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Ogólny, NET Framework 3 |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroki** | Publicznie udostępnianie informacji na temat usługi umożliwia atakującym cenny wgląd w jaki sposób może wykorzystać usługę. `<serviceMetadata>` Tag włącza funkcję publikowania metadanych. Metadane usługi mogą zawierać poufne informacje, które nie powinny być publicznie dostępne. Co najmniej Zezwalaj tylko przez zaufanych użytkowników dostępu do metadanych i upewnij się, że niepotrzebnych informacji nie jest uwidaczniana. Jeszcze lepiej całkowicie wyłączyć publikowanie metadanych. Bezpieczne konfiguracji usługi WCF nie będą zawierać `<serviceMetadata>` tagu. |

## <a id="exception"></a>Upewnij się, że odpowiednich wyjątków odbywa się w interfejsie API sieci Web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC 5, MVC 6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Obsługa wyjątków w ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling), [modelu weryfikacji w składniku ASP.NET Web API](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Domyślnie większość nieprzechwyconych wyjątków w interfejsie API sieci Web platformy ASP.NET są przetłumaczyć z kodem stanu odpowiedzi HTTP `500, Internal Server Error`|

### <a name="example"></a>Przykład
Aby kontrolować kod stanu zwrócony przez interfejs API, `HttpResponseException` można w sposób przedstawiony poniżej: 
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
Dla dalszego formantu w odpowiedzi wyjątek `HttpResponseMessage` klasa może być używana, jak pokazano poniżej: 
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
Aby przechwycić nieobsługiwane wyjątki, które nie są typu `HttpResponseException`, filtry wyjątków mogą być używane. Filtry wyjątków zaimplementować `System.Web.Http.Filters.IExceptionFilter` interfejsu. Najprostszym sposobem pisanie filtra wyjątku jest pochodzić z `System.Web.Http.Filters.ExceptionFilterAttribute` klasy i przesłonić metodę OnException. 

### <a name="example"></a>Przykład
Oto filtr, który konwertuje `NotImplementedException` wyjątków w kodzie stanu HTTP `501, Not Implemented`: 
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

Istnieje kilka sposobów, aby zarejestrować filtru wyjątków interfejsu API sieci Web:
- Przez akcję
- Kontroler
- Globalny

### <a name="example"></a>Przykład
Aby zastosować filtr do określonej akcji, należy dodać filtr jako atrybut do akcji: 
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
Aby zastosować filtr do wszystkich akcji w `controller`, Dodaj filtr jako atrybut do `controller` klasy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Przykład
Aby zastosować filtr globalnie do wszystkich kontrolerów interfejsu API sieci Web, należy dodać wystąpienia filtr, aby `GlobalConfiguration.Configuration.Filters` kolekcji. Zastosuj filtry wyjątków w tej kolekcji do dowolnej akcji kontrolera interfejsu API sieci Web. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Przykład
Do weryfikacji modelu stan modelu mogą zostać przekazane do metody CreateErrorResponse, jak pokazano poniżej: 
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

Sprawdź łącza w sekcji odwołań, aby uzyskać więcej informacji o obsługę wyjątkowych i sprawdzania poprawności modelu w interfejsie API sieci Web ASP.Net 

## <a id="messages"></a>Nie ujawniaj szczegóły zabezpieczeń w komunikatach o błędach

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Ogólne komunikaty o błędach są dostarczane bezpośrednio do użytkownika bez włącznie z danymi poufnej aplikacji. Przykłady danych poufnych:</p><ul><li>Nazwy serwerów</li><li>Parametry połączeń</li><li>Nazwy użytkowników</li><li>Hasła</li><li>Procedury SQL</li><li>Szczegóły błędów SQL dynamiczne</li><li>Ślad stosu i wiersze kodu</li><li>Zmiennych przechowywanych w pamięci</li><li>Dysk i folder lokalizacji</li><li>Punkty instalacji aplikacji</li><li>Ustawienia konfiguracji hosta</li><li>Inne szczegóły aplikacja wewnętrzna</li></ul><p>Generują pułapki wszystkich błędów w aplikacji i zapewnianie ogólne komunikaty o błędach, a także włączenie błędy niestandardowe w ramach usług IIS pomaga zapobiegać ujawnienie informacji. Baza danych SQL Server i .NET obsługi wyjątków, między inny błąd obsługi architektury, są szczególnie pełne i bardzo przydatne złośliwy użytkownik profilowania aplikacji. Nie bezpośrednio wyświetlić zawartość klasy pochodzącej od klasy wyjątków .NET i upewnij się, czy masz odpowiednie wyjątków, dzięki czemu wystąpił nieoczekiwany wyjątek przypadkowo nie jest wywoływane bezpośrednio do użytkownika.</p><ul><li>Podaj ogólne komunikaty o błędach bezpośrednio do użytkownika, który abstrakcyjnej nieobecności szczegółowe znaleziono bezpośrednio wyjątek/komunikatu o błędzie</li><li>Nie wyświetlaj zawartość klasy wyjątków .NET bezpośrednio do użytkownika</li><li>Pułapki wszystkie komunikaty o błędach i w razie potrzeby powiadamia użytkownika za pośrednictwem ogólny komunikat o błędzie wysyłany do aplikacji klienta</li><li>Nie ujawniaj treść klasy wyjątku bezpośrednio do użytkownika, szczególnie wartość zwrotną z elementu `.ToString()`, lub wartości właściwości komunikatu lub ślad stosu. Bezpiecznego logowania te informacje i wyświetli komunikat więcej nieszkodliwe dla użytkownika</li></ul>|

## <a id="default"></a>Implementowanie obsługi strony błędów domyślne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Edytuj okno dialogowe ustawień stron błędów ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroki** | <p>Aplikacja ASP.NET nie powiedzie się i powoduje, że wystąpił wewnętrzny błąd serwera HTTP/1.x 500 lub Konfiguracja funkcji, (na przykład Filtrowanie żądań) zapobiega wyświetlaniu strony, zostanie wygenerowany komunikat o błędzie. Administratorzy mogą wybrać, czy aplikacja powinien być wyświetlany przyjazny komunikat klienta, szczegółowy komunikat o błędzie do klienta lub szczegółowy komunikat o błędzie tylko localhost. <customErrors> Znacznika w pliku web.config ma trzy tryby:</p><ul><li>**Na:** Określa, czy błędy niestandardowe są włączone. Jeśli zostanie określony bez atrybutu defaultRedirect, użytkownicy widzą błąd ogólny. Błędy niestandardowe są wyświetlane z klientami zdalnymi, jak i dla hosta lokalnego</li><li>**OFF:** Określa, czy błędy niestandardowe są wyłączone. Szczegóły błędów platformy ASP.NET są wyświetlane z klientami zdalnymi, jak i dla hosta lokalnego</li><li>**RemoteOnly:** Określa, czy błędy niestandardowe są wyświetlane tylko klienci zdalni i czy błędy programu ASP.NET są widoczne dla hosta lokalnego. Jest to wartość domyślna</li></ul><p>Otwórz `web.config` dla witryny/aplikacji i upewnij się, że znacznik ma `<customErrors mode="RemoteOnly" />` lub `<customErrors mode="On" />` zdefiniowane.</p>|

## <a id="deployment"></a>Ustaw metody wdrażania na wersję handlową w usługach IIS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [wdrożenie — Element (schemat ustawień programu ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroki** | <p>`<deployment retail>` Przełącznik jest przeznaczony dla systemów produkcyjnych serwerów usług IIS. Ta opcja służy do aplikacje z najlepszą wydajność i najmniejsze wycieków informacji zabezpieczeń przez wyłączenie aplikacji możliwość generowania danych wyjściowych śledzenia na stronie wyłączenie możliwość wyświetlania szczegółowych komunikatów o błędach do Użytkownicy końcowi i wyłączanie przełącznika debugowania.</p><p>Często, przełączniki i opcje, które są ukierunkowane developer, takie jak nie powiodło się żądanie śledzenia i debugowania, są włączane podczas opracowywania active. Zaleca się, że metoda wdrażania na dowolnym serwerze produkcyjnym można ustawić na wersję handlową. Otwórz plik machine.config i upewnij się, że `<deployment retail="true" />` pozostaje ustawioną wartość true.</p>|

## <a id="fail"></a>Wyjątki powinna zakończyć się niepowodzeniem bezpiecznie

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Niepowodzenie bezpiecznego](https://www.owasp.org/index.php/Fail_securely) |
| **Kroki** | Aplikacja powinna zakończyć się niepowodzeniem bezpiecznie. Dowolnej metody, która zwraca wartość logiczną, oparte na następuje pewnych decyzji, powinien mieć dokładnie utworzyć bloku wyjątków. Istnieje wiele błędów logicznych ze względu na których pełzanie w po bloku wyjątków są zapisywane zaniedbali. problemy dotyczące zabezpieczeń.|

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
Powyżej metoda zawsze zwraca wartość True, jeśli wystąpi wyjątek. Jeśli użytkownik końcowy zawiera źle sformułowany adres URL, który uwzględnia przeglądarki, ale `Uri()` Konstruktor nie, to spowoduje zgłoszenie wyjątku i ofiary nastąpi przekierowanie do adresu URL prawidłowe, ale nieprawidłowo sformułowany. 