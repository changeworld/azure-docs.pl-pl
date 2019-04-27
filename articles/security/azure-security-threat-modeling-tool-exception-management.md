---
title: Wyjątek zarządzania — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: środki zaradcze dotyczące zagrożeń, widoczne w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 7d881454eb857080f1178f228a1f7bec36cae178
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610712"
---
# <a name="security-frame-exception-management--mitigations"></a>Ramka zabezpieczeń: Zarządzanie wyjątkami | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **WCF** | <ul><li>[Usługi WCF — nie uwzględniaj serviceDebug węzła w pliku konfiguracji](#servicedebug)</li><li>[Usługi WCF — nie uwzględniaj węzła serviceMetadata w pliku konfiguracji](#servicemetadata)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że odpowiednie wyjątków odbywa się w Web API platformy ASP.NET](#exception)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Nie ujawniaj informacji zabezpieczeń w komunikatach o błędach](#messages)</li><li>[Implementowanie obsługi strony błędów domyślne](#default)</li><li>[Set, metoda wdrażania na detaliczną w usługach IIS](#deployment)</li><li>[Wyjątki powinna zakończyć się niepowodzeniem bezpiecznie](#fail)</li></ul> |

## <a id="servicedebug"></a>Usługi WCF — nie uwzględniaj serviceDebug węzła w pliku konfiguracji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroki** | Usług Windows Communication Framework (WCF) można skonfigurować tak, aby udostępnić informacje debugowania. Debugowanie nie można używać informacji w środowiskach produkcyjnych. `<serviceDebug>` Tag definiuje, czy funkcja informacji debugowania jest włączona dla usługi WCF. Jeśli includeExceptionDetailInFaults atrybutu jest równa true, informacje o wyjątku z aplikacji będą zwracane do klientów. Osoby atakujące mogą korzystać inne informacje, które będą mogli z debugowania danych wyjściowych do zainstalowania celem ataków w ramach, bazy danych lub innych zasobów używanych przez aplikację. |

### <a name="example"></a>Przykład
Następujący plik konfiguracji zawiera `<serviceDebug>` tag: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Wyłącz informacji o debugowaniu w usłudze. Można to osiągnąć, usuwając `<serviceDebug>` znacznika z pliku konfiguracji aplikacji. 

## <a id="servicemetadata"></a>Usługi WCF — nie uwzględniaj węzła serviceMetadata w pliku konfiguracji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Generic, NET Framework 3 |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroki** | Publicznie ujawnienia informacji na temat usługi może zapewnić osoby atakujące cenne wglądu w jaki sposób może wykorzystać usługę. `<serviceMetadata>` Tag włącza funkcję publikowania metadanych. Metadane usługi mogą zawierać poufne informacje, które nie powinny być dostępne publicznie. Jako minimum tylko użytkownicy zaufany dostęp do metadanych i upewnij się, że niepotrzebnych informacji nie jest uwidaczniana. Jeszcze lepiej całkowicie wyłączyć możliwość Publikowanie metadanych. Bezpieczne konfiguracji usługi WCF nie będzie zawierać `<serviceMetadata>` tagu. |

## <a id="exception"></a>Upewnij się, że odpowiednie wyjątków odbywa się w Web API platformy ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC 5, MVC 6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Obsługa wyjątków w Web API platformy ASP.NET](https://www.asp.net/web-api/overview/error-handling/exception-handling), [Walidacja we wzorcu ASP.NET Web API modelu](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Domyślnie większość nieprzechwyconych wyjątków w interfejsie API sieci Web platformy ASP.NET są tłumaczone na odpowiedź HTTP z kodem stanu `500, Internal Server Error`|

### <a name="example"></a>Przykład
Aby kontrolować kod stanu zwrócony przez interfejs API, `HttpResponseException` może służyć, jak pokazano poniżej: 
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
Dodatkowe kontrolki w odpowiedzi wyjątek `HttpResponseMessage` klasa może być używana, jak pokazano poniżej: 
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
Aby przechwycić nieobsługiwane wyjątki, które nie są typu `HttpResponseException`, można użyć filtry wyjątków. Filtry wyjątków zaimplementować `System.Web.Http.Filters.IExceptionFilter` interfejsu. Najprostszym sposobem pisanie filtra wyjątku jest pochodną `System.Web.Http.Filters.ExceptionFilterAttribute` klasy, a także Przesłoń metodę OnException. 

### <a name="example"></a>Przykład
Oto filtr, który konwertuje `NotImplementedException` wyjątki w kod stanu HTTP `501, Not Implemented`: 
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

Istnieje kilka sposobów, aby zarejestrować filtra wyjątku interfejsu API sieci Web:
- Za akcję
- Kontroler
- Globalnie

### <a name="example"></a>Przykład
Aby zastosować filtr do określonej akcji, Dodaj filtr jako atrybut do akcji: 
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
Aby zastosować filtr do wszystkich akcji na `controller`, Dodaj filtr jako atrybut do `controller` klasy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Przykład
Aby zastosować filtr globalnie do wszystkich kontrolerów składnika Web API, należy dodać wystąpienia filtru w celu `GlobalConfiguration.Configuration.Filters` kolekcji. Zastosuj filtry wyjątków w tej kolekcji do dowolnej akcji kontrolera interfejsu API sieci Web. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Przykład
Do weryfikacji modelu stan modelu może być przekazywany do metody CreateErrorResponse, jak pokazano poniżej: 
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

Sprawdź łącza w sekcji odwołań, aby uzyskać szczegółowe informacje o obsłudze wyjątkowe i sprawdzania poprawności modelu programu ASP.NET Web API 

## <a id="messages"></a>Nie ujawniaj informacji zabezpieczeń w komunikatach o błędach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Ogólne komunikaty o błędach są dostarczane bezpośrednio do użytkownika bez uwzględniania danych poufnych aplikacji. Przykłady danych poufnych:</p><ul><li>Nazwy serwerów</li><li>Parametry połączeń</li><li>Nazwy użytkowników</li><li>Hasła</li><li>Procedur SQL</li><li>Szczegółowe informacje o dynamicznej błędów SQL</li><li>Ślad stosu i wierszy kodu</li><li>Zmiennych przechowywanych w pamięci</li><li>Dysk i folder lokalizacji</li><li>Punkty instalacji aplikacji</li><li>Ustawienia konfiguracji hosta</li><li>Inne szczegóły aplikacja wewnętrzna</li></ul><p>Generują pułapki wszystkich błędów w aplikacji i zapewnianie ogólne komunikaty o błędach, a także włączanie błędy niestandardowe w ramach usług IIS może pomóc zapobiec ujawnieniu informacji. Bazy danych programu SQL Server i .NET obsługi wyjątków, wśród innych obsługi architektury, błędów są szczególnie pełne i niezwykle przydatna funkcja dla złośliwego użytkownika profilowanie aplikacji. Nie bezpośrednio wyświetlać zawartość klasy pochodzącej od klasy wyjątku .NET i upewnij się, że odpowiednie wyjątków, aby nieoczekiwany wyjątek przypadkowo nie jest wywoływane bezpośrednio do użytkownika.</p><ul><li>Podaj ogólne komunikaty o błędach bezpośrednio do użytkownika, który abstrakcji away szczegółowe informacje bezpośrednio w wyjątek/komunikat o błędzie</li><li>Nie wyświetlaj zawartość klasy wyjątków .NET bezpośrednio do użytkownika</li><li>Namierzania wszystkich komunikatów o błędach i jeśli są one powiadamia użytkownika za pośrednictwem ogólny komunikat o błędzie wysyłany do klienta aplikacji</li><li>Nie ujawniaj zawartość klasy wyjątku bezpośrednio do użytkownika, a szczególnie wartość zwrotną z elementu `.ToString()`, lub wartości właściwości wiadomości lub ślad stosu. Bezpiecznie Zaloguj się te informacje i wyświetlić więcej nieszkodliwe komunikat dla użytkownika</li></ul>|

## <a id="default"></a>Implementowanie obsługi strony błędów domyślne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Edytuj okno dialogowe ustawień stron błędów ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroki** | <p>Gdy aplikacja ASP.NET nie powiedzie się i powoduje, że wystąpił wewnętrzny błąd serwera HTTP/1.x 500 lub Konfiguracja funkcji (np. Filtrowanie żądań) uniemożliwia wyświetlenie strony, zostanie wygenerowany komunikat o błędzie. Administratorzy mogą wybrać, informację określającą, czy aplikacja powinna wyświetlić przyjazny komunikat do klienta, szczegółowy komunikat o błędzie do klienta lub szczegółowy komunikat o błędzie tylko localhost. `<customErrors>` Znacznika w pliku web.config ma trzy tryby:</p><ul><li>**Włączone:** Określa, czy błędy niestandardowe są włączone. Jeśli atrybut defaultRedirect nie zostanie określony, użytkownicy widzą błąd ogólny. Błędy niestandardowe są wyświetlane z klientami zdalnymi, jak i do hosta lokalnego</li><li>**Wyłączone:** Określa, że błędy niestandardowe są wyłączone. Szczegóły błędów ASP.NET są wyświetlane z klientami zdalnymi, jak i do hosta lokalnego</li><li>**RemoteOnly:** Określa, że błędy niestandardowe są wyświetlane tylko z klientami zdalnymi oraz że ASP.NET błędy są wyświetlane dla hosta lokalnego. Jest to wartość domyślna</li></ul><p>Otwórz `web.config` plików dla aplikacji/witryny i sprawdź, czy tag ma `<customErrors mode="RemoteOnly" />` lub `<customErrors mode="On" />` zdefiniowane.</p>|

## <a id="deployment"></a>Set, metoda wdrażania na detaliczną w usługach IIS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [wdrożenie elementu (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroki** | <p>`<deployment retail>` Przełącznik jest przeznaczony dla serwerów usług IIS w środowisku produkcyjnym. Ten przełącznik ułatwiają działają najlepiej maksymalną wydajność i najniższe możliwe wycieków informacje zabezpieczeń, wyłączając aplikacji możliwość generowania danych wyjściowych śledzenia na stronie, wyłączenie możliwości, aby wyświetlić szczegółowe komunikaty o błędach do aplikacje Użytkownicy końcowi i wyłączając przełącznik debugowania.</p><p>Często, przełączników i opcje, które są skoncentrowane na deweloperów, takie jak nie powiodło się żądanie śledzenia i debugowania, są włączane podczas opracowywania active. Zalecane jest, czy metoda wdrażania na dowolnym serwerze produkcyjnym można ustawić na detaliczną. Otwórz plik machine.config i upewnij się, że `<deployment retail="true" />` ustawiony na wartość true.</p>|

## <a id="fail"></a>Wyjątki powinna zakończyć się niepowodzeniem bezpiecznie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Bezpiecznie zakończyć się niepowodzeniem](https://www.owasp.org/index.php/Fail_securely) |
| **Kroki** | Aplikacja powinna zakończyć się niepowodzeniem bezpiecznie. Wszystkie metody, która zwraca wartość logiczną, w oparciu o nawiązaniu pewnych decyzji powinien mieć bloku wyjątków dokładnie utworzone. Istnieje wiele błędów logicznych z powodu których pełzania w po bloku wyjątków jest zapisywany zaniedbali. problemy dotyczące zabezpieczeń.|

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
Powyższe metoda zawsze zwraca wartość True, jeśli wystąpi wyjątek. Jeśli użytkownik końcowy podaje nieprawidłowo sformułowany adres URL, który stosuje się do przeglądarki, ale `Uri()` Konstruktor nie, to spowoduje zgłoszenie wyjątku i ofiary nastąpi przekierowanie do adresu URL prawidłowy, ale źle sformułowane. 
