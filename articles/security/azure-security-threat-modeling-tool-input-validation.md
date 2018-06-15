---
title: Wprowadź Azure - narzędzie Microsoft Threat modelowania — sprawdzanie poprawności | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: d26d869748283718375e35ae4183eef0e51a96ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778583"
---
# <a name="security-frame-input-validation--mitigations"></a>Ramka zabezpieczeń: Sprawdzanie poprawności danych wejściowych | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Wyłącz XSLT skryptów dla wszystkich przekształceń przy użyciu arkuszy stylów niezaufanych](#disable-xslt)</li><li>[Upewnij się, że każdej strony, która może zawierać zawartość sterowane użytkownika zdecyduje się poza automatyczne wykrywanie MIME](#out-sniffing)</li><li>[Ograniczenia funkcjonalności lub wyłączyć rozpoznawanie jednostek XML](#xml-resolution)</li><li>[Aplikacje przy użyciu pliku http.sys przeprowadzenia weryfikacji zapewniania kanoniczności adresu URL](#app-verification)</li><li>[Upewnij się, że odpowiednie formanty są stosowane podczas akceptowania plików od użytkowników](#controls-users)</li><li>[Upewnij się, że bezpieczny parametry są używane w aplikacji sieci Web dla dostępu do danych](#typesafe)</li><li>[Użyj modelu oddzielne powiązanie klasy lub listy filtrów powiązanie, aby zapobiec MVC masowej przypisania](#binding-mvc)</li><li>[Dane wyjściowe niezaufanych sieci web przed renderowaniem kodowania](#rendering)</li><li>[Sprawdzania poprawności danych wejściowych i filtrowanie na typ ciągu wszystkie właściwości modelu](#typemodel)</li><li>[Ich oczyszczania powinny być stosowane na pola formularza, które akceptują znaków, np., Edytor tekstu sformatowanego](#richtext)</li><li>[Nie należy przypisywać elementy modelu DOM wychwytywanie, które nie mają wbudowane kodowania](#inbuilt-encode)</li><li>[Sprawdź poprawność wszystkich przekierowania aplikacji zostały zamknięte lub wykonywane w sposób bezpieczny](#redirect-safe)</li><li>[Implementowanie sprawdzania poprawności danych wejściowych na wszystkie parametry typu ciąg zaakceptowane przez metody kontrolera](#string-method)</li><li>[Ustawić limitu górnego limitu czasu dla wyrażenia regularnego przetwarzania, aby zapobiec DoS z powodu nieprawidłowych wyrażeń regularnych](#dos-expression)</li><li>[Należy unikać używania Html.Raw w widokami Razor](#html-razor)</li></ul> | 
| **Baza danych** | <ul><li>[Nie używaj zapytań dynamicznych procedury składowane](#stored-proc)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że weryfikacja modelu jest wykonywana na metody interfejsu API sieci Web](#validation-api)</li><li>[Implementowanie sprawdzania poprawności danych wejściowych na wszystkie parametry typu ciąg zaakceptowane przez metody interfejsu API sieci Web](#string-api)</li><li>[Upewnij się, czy bezpieczny parametry są używane w interfejsie API sieci Web dla dostępu do danych](#typesafe-api)</li></ul> | 
| **Dokumentów w usłudze Azure DB** | <ul><li>[Użyj sparametryzowanego zapytania SQL dla bazy danych Azure rozwiązania Cosmos](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Sprawdzanie poprawności danych wejściowych WCF przez powiązanie ze schematem](#schema-binding)</li><li>[Sprawdzanie poprawności wejściowy WCF za pomocą parametru inspektorzy](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Wyłącz XSLT skryptów dla wszystkich przekształceń przy użyciu arkuszy stylów niezaufanych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zabezpieczenia XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [właściwości XsltSettings.EnableScript](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroki** | XSLT obsługuje wykonywanie skryptów wewnątrz przy użyciu arkuszy stylów `<msxml:script>` elementu. Dzięki funkcji niestandardowych do użycia w transformację XSLT. Skrypt jest wykonywany w kontekście tego procesu wykonywania transformacji. Skrypt XSLT musi zostać wyłączone w niezaufanych środowiska, aby zapobiec wykonaniu kodzie niezaufanym. *Jeśli przy użyciu platformy .NET:* XSLT skryptów jest domyślnie wyłączona; jednak pamiętaj, że go nie została jawnie włączona przy użyciu `XsltSettings.EnableScript` właściwości.|

### <a name="example"></a>Przykład 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz przy użyciu programu MSXML 6.0 XSLT skryptów jest domyślnie wyłączona; należy jednak upewnij się, że jej nie została jawnie włączona za pośrednictwem właściwości obiektu XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz MSXML 5 lub poniżej, skryptów XSLT jest domyślnie włączona, więc użytkownik musi jawnie należy ją wyłączyć. Właściwości obiektu XML DOM AllowXsltScript ustawiona wartość false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Upewnij się, że każdej strony, która może zawierać zawartość sterowane użytkownika zdecyduje się poza automatyczne wykrywanie MIME

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Programu IE 8 V części zabezpieczeń - kompleksową ochronę](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Kroki** | <p>Dla każdej strony zawierających zawartość sterowane użytkownika, należy użyć nagłówka HTTP `X-Content-Type-Options:nosniff`. Aby spełnić to wymaganie, można ustawić wymaganego nagłówka przez strony dla tych stron, które mogą zawierać zawartość kontroli użytkownika, lub możesz ustawić globalnie do wszystkich stron w aplikacji.</p><p>Każdy typ pliku dostarczonych przez serwer sieci web ma skojarzoną [typ MIME](http://en.wikipedia.org/wiki/Mime_type) (nazywane również *typu zawartości*), który opisuje rodzaj zawartości (czyli obraz, tekst, aplikacji, itp.)</p><p>Nagłówek X-Content-typu-Options jest nagłówka HTTP, dzięki której deweloperzy mogą określić, że jego zawartość nie powinny być ten sposób MIME. Celem jest ograniczenie liczby ataków wykrywanie MIME tego nagłówka. Dodano obsługę dla tego nagłówka w programie Internet Explorer 8 (programu IE 8)</p><p>Tylko użytkownicy programu Internet Explorer 8 (programu IE 8) będą korzystać z X-Content-typu-Options. Poprzednie wersje programu Internet Explorer aktualnie nie przestrzega nagłówka X-zawartości — typ — opcje</p><p>Program Internet Explorer 8 (lub nowszy) są tylko główne przeglądarki do implementacji funkcji Wypisz wykrywanie MIME. Jeśli inne główne przeglądarki (Firefox, Safari, Chrome) zaimplementować podobne funkcje, tego zalecenia zostanie zaktualizowany obejmują składni dla tych przeglądarek, jak również</p>|

### <a name="example"></a>Przykład
Aby włączyć wymaganego nagłówka globalnie do wszystkich stron w aplikacji, wykonaj jedną z następujących czynności: 

* Dodaj nagłówek w pliku web.config, gdy aplikacja jest obsługiwana przez Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Dodaj nagłówek za pośrednictwem aplikacji globalne\_powstaniem zdarzenia BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementowanie niestandardowego modułu HTTP 

``` 
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
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Można włączyć wymaganego nagłówka tylko dla określonych stron przez dodanie go do poszczególnych odpowiedzi: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Ograniczenia funkcjonalności lub wyłączyć rozpoznawanie jednostek XML

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Rozszerzenia jednostki XML](http://capec.mitre.org/data/definitions/197.html), [ataki odmowy usługi XML i zabezpieczenia](http://msdn.microsoft.com/magazine/ee335713.aspx), [Omówienie zabezpieczeń MSXML](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [najlepsze rozwiązania dotyczące zabezpieczania kodu MSXML](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Odwołanie do protokołu NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [rozpoznawania odwołań zewnętrznych](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroki**| <p>Chociaż nie jest powszechnie używane, jest funkcją XML, który umożliwia działanie analizatora składni XML rozwinąć makra jednostek z wartości zdefiniowanych w dokumencie lub ze źródeł zewnętrznych. Na przykład dokumentu może zdefiniować jednostki "NazwaFirmy" o wartości "Microsoft", tak że zawsze tekst "&companyname;" pojawia się w dokumencie, automatycznie zostaje zastąpiony tekst firmy Microsoft. Lub dokument zdefiniować jednostki "MSFTStock" odwołujący się do zewnętrznej usługi sieci web można pobrać bieżącej wartości zasobu firmy Microsoft.</p><p>Następnie w dowolnym momencie "&MSFTStock;" pojawia się w dokumencie, automatycznie zostaje zastąpiony bieżącego giełdowy. Jednak ta funkcja może być użyte do utworzenia odmowa usługi (DoS) warunków. Osoba atakująca można zagnieżdżać wiele jednostek do tworzenia rozszerzenia wykładniczej bomba XML, który wykorzystuje wszystkie dostępnej pamięci w systemie. </p><p>Alternatywnie on można tworzyć odwołania zewnętrznego, który strumieni wstecz nieskończoną ilość danych lub która po prostu zawiesza się wątek. W związku z tym wszystkie zespoły należy wyłączyć wewnętrznych lub zewnętrznych rozpoznawanie jednostek XML, jeśli ich stosowania nie używać jej lub ręcznie ograniczyć ilość pamięci i czasu, który aplikacja może wykorzystać rozpoznanie jednostki, jeśli jest to bezwzględnie konieczne. Jeśli jednostka rozpoznawania nie jest wymagane przez aplikację, należy go wyłączyć. </p>|

### <a name="example"></a>Przykład
Dla kodu platformy .NET Framework można użyć następujących metod:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Należy pamiętać, że wartość domyślna `ProhibitDtd` w `XmlReaderSettings` ma wartość true, ale w `XmlTextReader` ma wartość false. Jeśli używasz XmlReaderSettings, nie trzeba ustawić ProhibitDtd dla elementu jawnie true, ale jest zalecane dla bezpieczeństwa sake wykonanie. Należy również zauważyć, że domyślnie klasy XmlDocument zezwala na rozpoznawanie jednostek. 

### <a name="example"></a>Przykład
Aby wyłączyć rozpoznawanie jednostek dla XmlDocuments, należy użyć `XmlDocument.Load(XmlReader)` przeciążenia metody obciążenia i ustaw odpowiednie właściwości w argumencie XmlReader i wyłączyć rozpoznawanie, jak pokazano w poniższym kodzie: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Przykład
Jeśli wyłączenie rozpoznawania jednostki nie jest możliwe w dla aplikacji, należy ustawić właściwość XmlReaderSettings.MaxCharactersFromEntities rozsądną wartość zgodnie z potrzebami Twojej aplikacji. To powoduje ograniczenie skutków potencjalnych ataków DoS wykładniczej rozszerzenia. Poniższy kod stanowi przykład tego podejścia: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Przykład
Jeśli chcesz rozwiązać wbudowanego jednostek, ale mają nie trzeba rozwiązać podmioty zewnętrzne, ustaw właściwość XmlReaderSettings.XmlResolver na wartość null. Na przykład: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Należy zauważyć, że w MSXML6, ProhibitDtd dla elementu jest TRUE (wyłączenie przetwarzanie elementu DTD) domyślnie. Kod, OS x firmy Apple dla systemu iOS, istnieją dwie analizatorów składni XML można użyć: NSXMLParser i libXML2. 

## <a id="app-verification"></a>Aplikacje przy użyciu pliku http.sys przeprowadzenia weryfikacji zapewniania kanoniczności adresu URL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Dowolnej aplikacji, która korzysta z pliku http.sys należy przestrzegać następujących wytycznych:</p><ul><li>Ogranicz długość adresu URL do nie więcej niż 16 384 znaków (ASCII lub Unicode). Jest to bezwzględny maksymalna długość adresu URL na podstawie domyślnego ustawienia usług Internet Information Services (IIS) 6. Witryn sieci Web należy dążyć do krótszego niż to, jeśli to możliwe</li><li>Użyj standardowe klasy we/wy pliku .NET Framework (na przykład FileStream), jak te skorzystają z zasadami zgodności w .NET FX</li><li>Jawne tworzenie listy dozwolonych, znane nazw plików</li><li>Jawnie Odrzuć znane typy plików, nie będzie obsługiwać odrzuca UrlScan: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm [l], stm, drukarki, ini, pol, pliki dat</li><li>CATCH następujące wyjątki:<ul><li>System.ArgumentException (dla nazwy urządzenia)</li><li>System.NotSupportedException (dla strumieni danych)</li><li>System.IO.FileNotFoundException (dla nieprawidłowy zmienionym nazwy plików)</li><li>System.IO.DirectoryNotFoundException (dla nieprawidłowy katalogi zmienionym)</li></ul></li><li>*Nie* wyróżnienia do pliku Win32 interfejsów API we/wy. Na nieprawidłowy adres URL bezpiecznie zwróci błąd 400 do użytkownika i rzeczywistych błąd logowania.</li></ul>|

## <a id="controls-users"></a>Upewnij się, że odpowiednie formanty są stosowane podczas akceptowania plików od użytkowników

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Nieograniczonych przekazywania pliku](https://www.owasp.org/index.php/Unrestricted_File_Upload), [tabeli podpisu pliku](http://www.garykessler.net/library/file_sigs.html) |
| **Kroki** | <p>Przekazane pliki stanowi znaczące zagrożenie aplikacji.</p><p>Pierwszym etapem wiele ataków jest uzyskanie kodu, system na ataki. Następnie ataku tylko musi znaleźć sposób, aby otrzymać kod wykonywany. Przy użyciu przekazywania pliku pomaga osoba atakująca zrobić w pierwszym kroku. Skutków przekazywania pliku nieograniczony może różnić się w tym przejęcia całego systemu, system plików przeciążone lub bazy danych, przekazywanie ataków na systemy zaplecza i proste atak skutkujący zmianą zawartości.</p><p>To zależy funkcje aplikacji z przekazanego pliku i szczególnie, gdzie są przechowywane. Brak sprawdzania poprawności po stronie serwera przekazywania plików. Następujące opcje zabezpieczeń powinny być implementowane przekazywanie plików funkcji:</p><ul><li>Sprawdź rozszerzenia plików (powinna być akceptowana prawidłowy zbiór dozwolonego typu plików)</li><li>Maksymalny limit rozmiaru</li><li>Nie można przekazać pliku do webroot; Lokalizacja powinna być katalogu na dysku niesystemowym</li><li>Konwencja nazewnictwa, należy wykonać, zastępuje w taki sposób, że nazwa przesłanego pliku ma niektórych losowości, tak aby zapobiec pliku</li><li>Pliki powinny zostać przeskanowane pod kątem oprogramowania antywirusowego przed zapisaniem na dysku</li><li>Upewnij się, sprawdzania poprawności dla złośliwego znaków nazwy pliku i wszystkie inne metadane (np. ścieżka pliku)</li><li>Podpis format pliku powinna być sprawdzana, aby uniemożliwić użytkownikowi przekazywania masqueraded pliku (np. przekazywanie pliku exe przez zmianę rozszerzeniem txt)</li></ul>| 

### <a name="example"></a>Przykład
Ostatniego punktu dotyczące sprawdzania poprawności podpisu format pliku można znaleźć klasy poniżej, aby uzyskać szczegółowe informacje: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Upewnij się, że bezpieczny parametry są używane w aplikacji sieci Web dla dostępu do danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Jeśli korzystasz z kolekcji parametrów, traktuje SQL dane wejściowe są jako wartość literału, a nie jako kod wykonywalny. Kolekcja parametrów może być używana do wymuszania ograniczeń typu i długości danych wejściowych. Wartości spoza zakresu wyzwolenia Wystąpił wyjątek. Jeśli nie są używane parametry SQL bezpieczny, osoby atakujące można wykonać iniekcji atakom, które są osadzone w niefiltrowane danych wejściowych.</p><p>Użyj parametrów bezpieczne typu podczas tworzenia zapytania SQL, aby uniknąć możliwych ataków iniekcji kodu SQL, które mogą wystąpić przy użyciu niefiltrowane danych wejściowych. Umożliwia bezpieczne parametry typu z procedur składowanych i dynamicznych instrukcji SQL. Parametry są traktowane jako wartości literałów przez bazę danych, a nie kodu wykonywalnego. Parametry są również sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład 
Poniższy kod przedstawia sposób użycia bezpiecznego parametry typu z SqlParameterCollection podczas wywoływania procedury składowanej. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli danych jest niezgodny z typem lub długość zdefiniowaną przez parametr, klasa SqlParameter zgłasza wyjątek. 

## <a id="binding-mvc"></a>Użyj modelu oddzielne powiązanie klasy lub listy filtrów powiązanie, aby zapobiec MVC masowej przypisania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Atrybutów metadanych](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [publiczny klucz zabezpieczeń luki w zabezpieczeniach i środki zaradcze](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [do przypisania masowej na platformie ASP.NET MVC: pełny Przewodnik po](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [wprowadzenie EF przy użyciu MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroki** | <ul><li>**Gdy powinna wyglądać dla zbyt księgowej luk w zabezpieczeniach? -** Zbyt księgowej luk w zabezpieczeniach, może wystąpić miejsce wiązania klasy modeli z danych wprowadzonych przez użytkownika. Struktury, takich jak MVC może reprezentować danych użytkownika w niestandardowej klasy .NET, w tym zwykły stare obiekty CLR (POCOs). MVC automatycznie wypełni te klasy modelu danych z żądaniem, zapewniając wygodny reprezentacja zajmujących się dane wejściowe użytkownika. Te klasy zawierają właściwości, które nie powinny być ustawiane przez użytkownika, aplikacja może być narażony na zbyt księgowej ataków, umożliwiających kontrolki użytkownika danych, które nigdy nie aplikacji przeznaczonych. Podobnie jak wiązania modelu MVC, bazy danych dostęp do technologii, takich jak mapowań obiektu/relacyjnych jak Entity Framework często również obsługa przy użyciu obiektów POCO do reprezentowania danych w bazie danych. Te klasy modelu danych Podaj funkcję w dotyczących danych z bazy danych, tak jak w przypadku MVC przy danych wejściowych użytkownika. Ponieważ zarówno MVC, jak i bazy danych obsługuje modele podobne jak w przypadku obiektów POCO wydaje się ułatwia zarówno w celach ponowne użycie tej samej klasy. Takie rozwiązanie nie powiedzie się zachować separacji i jest jednego obszaru wspólnej, gdzie niezamierzone właściwości są widoczne dla wiązania modelu, umożliwiające publikowanie uwierzytelniając ataków.</li><li>**Dlaczego nie należy używać zajęć niefiltrowane bazy danych modelu jako parametry Moje działania MVC? -** Wiązania modelu MVC ponieważ powiąże niczego w tej klasie. Nawet jeśli dane nie są wyświetlane w widoku, złośliwy użytkownik może wysyłać żądań HTTP przy użyciu tych danych uwzględnione i MVC gladly powiąże go ponieważ akcję tym, że klasa bazy danych kształtu danych, które powinien akceptować dla danych wejściowych użytkownika.</li><li>**Dlaczego należy interesujących kształtu używany do wiązania modelu? -** Wiązania modelu przy użyciu platformy ASP.NET MVC przy użyciu modeli nadmiernych przedstawia aplikacji zbyt księgowej ataków. Publikowanie uwierzytelniając można włączyć osoby atakujące zmienić dane aplikacji poza co Deweloper przeznaczone, takie jak zastępowanie ceny dla elementu lub uprawnień do konta. Aplikacje powinny używać kontraktu wiązanie modeli (lub określoną właściwość dozwolonych filtru list) w celu zapewnienia jawnie określonych akcji dla jakie niezaufanych danych wejściowych umożliwić za pośrednictwem wiązania modelu.</li><li>**Występują modeli oddzielne powiązania właśnie duplikowania kod? -** Nie jest to kwestia separacji. Ponowne użycie bazy danych modeli w metod akcji, są opinie żadnych właściwość (lub podrzędnego), w tej klasy można ustawić przez użytkownika w żądaniu HTTP. Jeśli nie chcesz MVC, aby zrobić to, potrzebujesz listę filtrów lub kształt osobnej klasy do wyświetlenia MVC, jakie dane mogą pochodzić z danych wejściowych zamiast tego użytkownika.</li><li>**Jeśli modele oddzielne powiązania dla danych wejściowych użytkownika, należy ponownie zduplikowane wszystkie moje atrybuty adnotacji danych? -** Niekoniecznie. MetadataTypeAttribute klasy modelu bazy danych służy do odesłania do metadanych dla klasy wiązania modelu. Po prostu należy pamiętać, że typ odwołuje się MetadataTypeAttribute muszą być podzbiorem odwołaniem do typu (może mieć mniej właściwości, ale nie więcej).</li><li>**Przenoszenie danych i z powrotem między modelami wejściowych użytkownika i modele bazy danych jest niewygodny. Wystarczy skopiować na wszystkie właściwości, za pomocą odbicia? -** Tak. Tylko właściwości, które są wyświetlane w modelach powiązania są tymi, które zostały uznane za bezpieczne dla danych wejściowych użytkownika. To nie ma powodu zabezpieczeń, co uniemożliwia ich za pomocą odbicia, aby skopiować wszystkie właściwości, które istnieją wspólne między te dwa modele.</li><li>**Jak [Bind (Wyklucz = "â €¦")]. Czy za pomocą którego zamiast modeli oddzielne powiązania? -** Tej metody nie jest zalecane. Przy użyciu [Bind (Wyklucz = "â €¦")] oznacza, że wszystkie nowe właściwości powiązania domyślnie. Po dodaniu nowej właściwości jest dodatkowy krok, aby Pamiętaj, aby zapewnić bezpieczeństwo rzeczy, a nie o projektu można domyślnie zabezpieczone. W zależności od dewelopera ryzykowne jest sprawdzanie tej listy za każdym razem, gdy właściwość została dodana.</li><li>**Jest [Bind (Dołącz = "â €¦")] przydatne w przypadku operacji edycji? -** Nie. [Powiązanie (Dołącz = "â €¦")] nadaje się tylko do operacji INSERT stylu (Dodawanie nowych danych). Dla operacji aktualizacji stylu (zmiana istniejących danych) Użyj innego podejścia, takie jak mających oddzielne powiązania modele lub przekazywanie jawną listę dozwolonych właściwości UpdateModel lub TryUpdateModel. Dodawanie [Bind (Dołącz = "â €¦")] atrybutu na operację edycji oznacza, że MVC będzie utworzyć wystąpienie obiektu Ustaw tylko właściwości z listy, pozostawiając pozostałe wartości domyślne. W przypadku danych jest trwały, całkowicie zastąpi istniejącej jednostki, resetowanie wartości pominięcia właściwości do wartości domyślnych. Na przykład, jeśli został pominięty IsAdmin [Bind (Dołącz = "â €¦")] atrybutu na operację edycji, każdy użytkownik, którego nazwa została edytować za pomocą tej akcji spowoduje zresetowana do wartości IsAdmin = false (każdy użytkownik edytowanych spowoduje utratę stan administratora). Jeśli chcesz uniemożliwić aktualizacji do niektórych właściwości, użyj jednej z metod powyżej. Należy pamiętać, że niektóre wersje narzędzi MVC wygenerowane klasy kontrolera z [Bind (Dołącz = "â €¦")] edytowanie akcji i oznacza, że usunięcie właściwości z tej listy będą zapobiegać atakom uwierzytelniając publikowanie. Jednak zgodnie z powyższym opisem tego podejścia nie będzie działać zgodnie z założeniami, a zamiast tego spowoduje zresetowanie żadnych danych w przypadku pominięcia właściwości do wartości domyślnych.</li><li>**Dla operacji Create, czy istnieją wszystkie ostrzeżenia przy użyciu [Bind (Dołącz = "â €¦")] zamiast modeli oddzielne powiązania? -** Tak. Takie podejście najpierw nie działa w przypadku scenariuszy edycji konieczności utrzymania dwa podejścia oddzielne łagodzenia wszystkie luki w zabezpieczeniach publikowanie nadmierne. Modele powiązanie drugiej, oddzielnej wymuszania separacji między kształt danych wejściowych użytkownika i kształt trwałości, coś [Bind (Dołącz = "â €¦")] nie działa. Trzecie, należy pamiętać, że [Bind (Dołącz = "â €¦")] może obsługiwać tylko właściwości najwyższego poziomu; Nie można zezwolić tylko części właściwości podrzędnych (na przykład "Details.Name") w atrybucie. Na koniec i prawdopodobnie przede wszystkim za pomocą [powiązania (Dołącz = "â €¦")] dodaje dodatkowy krok, który należy pamiętać, wtedy klasa jest używana do wiązania modelu. Jeśli nowej metody akcji wiąże klasy danych bezpośrednio i zapomni do uwzględnienia [powiązania (Dołącz = "â €¦")] atrybutu, może być narażony na zbyt księgowej ataków, więc [powiązać (Dołącz = "â €¦")] podejście jest domyślnie nieco mniej bezpieczne. Jeśli używasz [Bind (Dołącz = "â €¦")], należy zawsze pamiętaj określić, że za każdym razem, gdy klas danych są wyświetlane jako parametry metody akcji.</li><li>**Dla operacji Create, co sądzisz o wprowadzenie [Bind (Dołącz = "â €¦")] atrybutu w samej klasy modelu? Nie takie podejście uniknąć konieczności Pamiętaj umieszczania atrybutu w każdej metody akcji? -** Ta metoda działa w niektórych przypadkach. Przy użyciu [Bind (Dołącz = "â €¦")] na samego typu modelu (a nie na parametry akcji za pomocą tej klasy), uniknąć konieczności Pamiętaj, aby uwzględnić [powiązać (Dołącz = "â €¦")] atrybutu w każdej metody akcji. Efektywne używanie atrybutu bezpośrednio w klasie tworzy oddzielne powierzchni tej klasy w celach powiązania modelu. Jednak takie podejście umożliwia tylko na jeden kształt powiązanie modelu na model klasy. Jeśli jedna metoda akcji musi umożliwić wiązanie modelu pola (na przykład tylko administrator akcję, która aktualizuje role użytkowników) i innych działań dopuścić wiązania modelu w tym polu do, ta metoda nie będzie działać. Każda klasa może mieć tylko jeden kształt powiązanie modelu; Jeśli różne akcje muszą kształtów powiązania innego modelu, muszą reprezentować te oddzielne kształtów za pomocą klasy powiązanie albo oddzielnym modelu lub różnych [Bind (Dołącz = "â €¦")] atrybutów dla metody akcji.</li><li>**Co to są wiązanie modeli? Są one tak samo, jak przeglądanie modeli -** Są dwa pojęcia pokrewne. Model powiązanie termin odwołuje się do klasy modelu używany na liście parametrów akcji (kształtu przekazywanych do metody akcji z wiązania modelu MVC). Model widoku termin odwołuje się do klasy modelu przekazywanych do widoku z metody akcji. Przy użyciu modelu specyficzne dla widoku jest typowym podejściem przekazywania danych z metody akcji do widoku. Często ten kształt jest również odpowiedniego do wiązania modelu i model widoku termin można odwoływać się sam model używane w obu miejscach. Aby była precyzyjna, ta procedura wspomniana specjalnie wiązanie modeli koncentrujących się na kształt przekazany do akcji, która jest, co jest ważne dla celów masowej przypisania.</li></ul>| 

## <a id="rendering"></a>Dane wyjściowe niezaufanych sieci web przed renderowaniem kodowania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, Web Forms, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Jak zapobiec skryptów w programie ASP.NET krzyżowych](http://msdn.microsoft.com/library/ms998274.aspx), [skryptów krzyżowych](http://cwe.mitre.org/data/definitions/79.html), [arkusza Cheat zapobiegania XSS (skryptów krzyżowych)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Kroki** | Skryptów między witrynami (zwykle skrót XSS) jest celem ataków usług online lub aplikacji/części, który wykorzystuje dane wejściowe z sieci web. Luki w zabezpieczeniach XSS może umożliwić atakującemu można wykonać skryptu na komputerze przez innego użytkownika za pośrednictwem aplikacji sieci web narażone. Złośliwych skryptów może służyć do kradzieży plików cookie, a w przeciwnym razie manipulowanie ofiara komputera przy użyciu języka JavaScript. Walidacja danych wejściowych użytkownika, zapewnienie, że jest poprawnie sformułowany i kodowanie, zanim zostanie wyświetlony na stronie sieci web nie będzie mógł XSS. Sprawdzania poprawności danych wejściowych i wyjściowych kodowanie może odbywać się przy użyciu biblioteki ochrony sieci Web. Dla kodu zarządzanego (C\#, VB.net, itp.), użyj jednej lub więcej odpowiednich metod kodowania z biblioteki ochrony sieci Web (Anti-XSS), w zależności od kontekstu, w którym pobiera dyskowe wyświetlane dane wejściowe użytkownika:| 

### <a name="example"></a>Przykład

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Sprawdzania poprawności danych wejściowych i filtrowanie na typ ciągu wszystkie właściwości modelu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Dodawanie walidacji](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [sprawdzanie poprawności modelu danych w aplikacji MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [wytyczne dla aplikacji ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | <p>Musi zostać zweryfikowany parametrów wejściowych, zanim zostaną użyte w aplikacji aby upewnić się, że aplikacja jest chronione przed danych wprowadzonych przez złośliwego użytkownika. Sprawdź poprawność wartości wejściowe za pomocą wyrażeń regularnych operacji sprawdzania poprawności po stronie serwera ze strategią sprawdzania poprawności listy dozwolonych adresów IP. Unsanitized danych wprowadzonych przez użytkownika / parametry przekazywane do metod może powodować kodu iniekcji luk w zabezpieczeniach.</p><p>Punkty wejścia można także dla aplikacji sieci web, pól formularza, QueryStrings plików cookie, nagłówków HTTP i parametry usługi sieci web.</p><p>Następujące testy sprawdzania poprawności danych wejściowych, należy wykonać podczas wiązania modelu:</p><ul><li>Właściwości modelu powinien być oznaczony za pomocą adnotacji wyrażenia regularnego, Zaakceptuj dozwolonych znaków, a maksymalna dopuszczalna długość</li><li>Metody kontrolera, należy wykonać ModelState ważności</li></ul>|

## <a id="richtext"></a>Ich oczyszczania powinny być stosowane na pola formularza, które akceptują znaków, np., Edytor tekstu sformatowanego

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Kodowanie niebezpiecznych danych wejściowych](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [moduł czyszczący HTML](https://github.com/mganss/HtmlSanitizer) |
| **Kroki** | <p>Zidentyfikuj wszystkie tagi znaczników statycznych, których chcesz użyć. Typowym rozwiązaniem jest ograniczenie formatowania do bezpiecznego elementów HTML, takie jak `<b>` (pogrubienie) i `<i>` (kursywą).</p><p>Przed zapisaniem danych kodowanie HTML go. Dzięki temu wszystkie złośliwy skrypt bezpieczne przez powodowania traktowane jako tekst, a nie jako kod wykonywalny.</p><ol><li>Wyłączono weryfikację żądań ASP.NET, dodając parametr ValidateRequest = "false" dla atrybutu dyrektywy @ Page</li><li>Kodowanie przy użyciu metody HtmlEncode ciąg na wejściu</li><li>Użyj klasy StringBuilder i Wywołaj jej metodę Replace, aby usunąć wybrane kodowanie elementów HTML, które chcesz zezwalać na</li></ol><p>Strona do weryfikacji żądań odwołania wyłącza ASP.NET, ustawiając `ValidateRequest="false"`. Go HTML koduje dane wejściowe i umożliwia selektywne `<b>` i `<i>` Alternatywnie można również użyć biblioteki .NET dla ich oczyszczania HTML.</p><p>HtmlSanitizer to biblioteka .NET do czyszczenia fragmentów kodu HTML i dokumentów z konstrukcji, które mogą prowadzić do atakom XSS. Użyto AngleSharp do analizy, manipulowanie nimi oraz renderowania kodu HTML i CSS. HtmlSanitizer można zainstalować jako pakietu NuGet, a dane wejściowe użytkownika mogą zostać przekazane za pomocą odpowiednich HTML i CSS ich oczyszczania metod po stronie serwera. Należy pamiętać, że ich oczyszczania jako kontrolę zabezpieczeń należy traktować wyłącznie jako ostatnia opcja.</p><p>Sprawdzania poprawności danych wejściowych i wyjściowych kodowanie są traktowane jako lepszą kontrolę zabezpieczeń.</p> |

## <a id="inbuilt-encode"></a>Nie należy przypisywać elementy modelu DOM wychwytywanie, które nie mają wbudowane kodowania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wiele funkcji javascript nie wykonuj kodowaniu. Podczas przypisywania niezaufanych danych wejściowych do elementów modelu DOM za pośrednictwem takie funkcje, może spowodować między wykonaniami skryptu (XSS) w lokacji.| 

### <a name="example"></a>Przykład
Poniżej przedstawiono przykłady niezabezpieczone: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nie używaj `innerHtml`; zamiast tego użyć `innerText`. Podobnie, a nie z `$("#elm").html()`, użyj `$("#elm").text()` 

## <a id="redirect-safe"></a>Sprawdź poprawność wszystkich przekierowania aplikacji zostały zamknięte lub wykonywane w sposób bezpieczny

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Framework autoryzacji OAuth 2.0 - readresatory otwarte](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroki** | <p>Projekt aplikacji wymagających przekierowanie do lokalizacji dostarczone przez użytkownika należy ograniczyć cele możliwe przekierowania do wstępnie zdefiniowaną listą "bezpiecznej" Lokacje lub domeny. Wszystkie przekierowania w aplikacji musi być zamknięty bezpieczne.</p><p>W tym celu:</p><ul><li>Zidentyfikuj wszystkie przekierowania</li><li>Implementuje odpowiednie środki zaradcze dla każdego przekierowania. Odpowiednie środki zaradcze zawierają potwierdzenie przekierowania listy dozwolonych lub użytkownika. Jeśli do witryny sieci web lub usługi z usterki Otwórz przekierowania używa dostawcy tożsamości usługi Facebook/OAuth/OpenID, osoba atakująca może wykradać tokenu logowania użytkownika i personifikacji tego użytkownika. Jest to ryzykiem w przypadku w trybie OAuth, które opisano w specyfikacji RFC 6749 "Protokołu OAuth 2.0 autoryzacji ramach", podobnie sekcji 10.15 "otworzyć przekierowuje", poświadczeń użytkowników mogą zostać złamane przez ukierunkowanego wyłudzania przy użyciu przekierowania Otwórz</li></ul>|

## <a id="string-method"></a>Implementowanie sprawdzania poprawności danych wejściowych na wszystkie parametry typu ciąg zaakceptowane przez metody kontrolera

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Sprawdzanie poprawności modelu danych w aplikacji MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [wytyczne dla aplikacji ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | Dla metod, które wystarczy zaakceptować jako argument typu danych pierwotnych, a nie modeli można wykonać sprawdzania poprawności danych wejściowych przy użyciu wyrażenia regularnego. W tym miejscu Regex.IsMatch powinien być używany z wzorcem prawidłowe wyrażenie regularne. Jeśli dane wejściowe nie odpowiada określonemu wyrażeniu regularnemu, formant nie należy kontynuować, a powinien być wyświetlany odpowiedniego ostrzeżenia dotyczące niepowodzenia weryfikacji.| 

## <a id="dos-expression"></a>Ustawić limitu górnego limitu czasu dla wyrażenia regularnego przetwarzania, aby zapobiec DoS z powodu nieprawidłowych wyrażeń regularnych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, Web Forms, MVC5, MVC6  |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Właściwość DefaultRegexMatchTimeout ](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroki** | Aby "odmowa usługi" przeciwko źle utworzony wyrażeń regularnych, powodujących dużo śledzenie wsteczne ustawić globalne domyślny limit czasu. Jeśli czas przetwarzania trwa dłużej niż górny limit zdefiniowany, go spowoduje zgłoszenie wyjątku limitu czasu. Jeśli niczego nie skonfigurowano limit czasu może być nieskończona.| 

### <a name="example"></a>Przykład
Na przykład następująca konfiguracja zgłosi RegexMatchTimeoutException, jeśli przetwarzanie zajmuje więcej niż 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Należy unikać używania Html.Raw w widokami Razor

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Krok | Stron sieci Web ASP.Net (Razor) wykonaj automatyczne kodowania HTML. Wszystkie ciągi drukowanymi przez nuggets osadzonego kodu (@ bloki) są automatycznie kodowany w formacie HTML. Jednakże, gdy `HtmlHelper.Raw` wywołania metody, zwraca kod znaczników, który nie ma kodowania HTML. Jeśli `Html.Raw()` używana jest metoda pomocnika, jego pomija automatyczną ochronę kodowania, która udostępnia Razor.|

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładowy niezabezpieczonych: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nie używaj `Html.Raw()` chyba, że konieczne jest wyświetlenie znaczników. Ta metoda nie przeprowadza kodowania niejawnie danych wyjściowych. Użyj innych pomocników platformy ASP.NET, np. `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Nie używaj zapytań dynamicznych procedury składowane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Ataku polegającego na iniekcji SQL wykorzystuje luki w zabezpieczeniach w sprawdzania poprawności danych wejściowych do uruchomienia dowolnego polecenia w bazie danych. Może on wystąpić, gdy aplikacja korzysta z danych wejściowych do konstruowania dynamicznych instrukcji SQL, dostęp do bazy danych. Może również wystąpić, jeśli procedury składowane przekazywane ciągi zawierające dane wejściowe użytkownika raw używa Twój kod. Za pomocą ataku polegającego na iniekcji SQL, osoba atakująca może wykonać dowolne polecenia w bazie danych. Wszystkie instrukcje SQL (w tym instrukcje SQL w procedur składowanych) musi sparametryzowana. Sparametryzowanych instrukcji SQL akceptuje znaków, które mają specjalne znaczenie do bazy danych SQL (na przykład pojedynczy cudzysłów) bez problemów, ponieważ są one silnie typizowane. |

### <a name="example"></a>Przykład
Poniżej przedstawiono przykład niezabezpieczonych dynamiczne procedury składowanej: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Przykład
Tej samej procedury składowanej zaimplementowana bezpiecznie jest następujący: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Upewnij się, że weryfikacja modelu jest wykonywana na metody interfejsu API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Weryfikacja modelu w składniku ASP.NET Web API ](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Gdy klient wysyła dane do interfejsu API sieci web, jest to konieczne do sprawdzania poprawności danych, przed wykonaniem jakiegokolwiek przetwarzania. Dla interfejsów API sieci Web ASP.NET, która akceptuje modeli jako dane wejściowe, użyj adnotacji danych w modelach konfigurowania reguł sprawdzania poprawności dla właściwości modelu.|

### <a name="example"></a>Przykład
Poniższy kod ilustruje takie same: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Przykład
W metodzie akcji kontrolerów interfejsu API ważności modelu ma jawnie można sprawdzić, jak pokazano poniżej: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Implementowanie sprawdzania poprawności danych wejściowych na wszystkie parametry typu ciąg zaakceptowane przez metody interfejsu API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, MVC 5, 6 MVC |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Sprawdzanie poprawności modelu danych w aplikacji MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [wytyczne dla aplikacji ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | Dla metod, które wystarczy zaakceptować jako argument typu danych pierwotnych, a nie modeli można wykonać sprawdzania poprawności danych wejściowych przy użyciu wyrażenia regularnego. W tym miejscu Regex.IsMatch powinien być używany z wzorcem prawidłowe wyrażenie regularne. Jeśli dane wejściowe nie odpowiada określonemu wyrażeniu regularnemu, formant nie należy kontynuować, a powinien być wyświetlany odpowiedniego ostrzeżenia dotyczące niepowodzenia weryfikacji.|

## <a id="typesafe-api"></a>Upewnij się, czy bezpieczny parametry są używane w interfejsie API sieci Web dla dostępu do danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Jeśli korzystasz z kolekcji parametrów, traktuje SQL dane wejściowe są jako wartość literału, a nie jako kod wykonywalny. Kolekcja parametrów może być używana do wymuszania ograniczeń typu i długości danych wejściowych. Wartości spoza zakresu wyzwolenia Wystąpił wyjątek. Jeśli nie są używane parametry SQL bezpieczny, osoby atakujące można wykonać iniekcji atakom, które są osadzone w niefiltrowane danych wejściowych.</p><p>Użyj parametrów bezpieczne typu podczas tworzenia zapytania SQL, aby uniknąć możliwych ataków iniekcji kodu SQL, które mogą wystąpić przy użyciu niefiltrowane danych wejściowych. Umożliwia bezpieczne parametry typu z procedur składowanych i dynamicznych instrukcji SQL. Parametry są traktowane jako wartości literałów przez bazę danych, a nie kodu wykonywalnego. Parametry są również sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład
Poniższy kod przedstawia sposób użycia bezpiecznego parametry typu z SqlParameterCollection podczas wywoływania procedury składowanej. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli danych jest niezgodny z typem lub długość zdefiniowaną przez parametr, klasa SqlParameter zgłasza wyjątek. 

## <a id="sql-docdb"></a>Użyj sparametryzowanego zapytania SQL dla bazy danych rozwiązania Cosmos

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dokumentów w usłudze Azure DB | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Anonsowanie parametryzacja SQL w Azure rozwiązania Cosmos bazy danych](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroki** | Mimo że bazy danych rozwiązania Cosmos Azure obsługuje tylko zapytania tylko do odczytu, iniekcja kodu SQL jest nadal możliwe, gdy zapytania są wykonane przez łączenie z danych wejściowych użytkownika. Może się zdarzyć, aby użytkownik mógł uzyskać dostęp do danych, której nie powinien mieć dostęp w ramach tej samej kolekcji przez obsługuje tworzenie zapytań SQL złośliwego. Użyj sparametryzowane zapytania SQL Jeśli zbudowanych zapytania oparte na danych wejściowych użytkownika. |

## <a id="schema-binding"></a>Sprawdzanie poprawności danych wejściowych WCF przez powiązanie ze schematem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroki** | <p>Brak weryfikacji prowadzi do ataków iniekcji innego typu.</p><p>Sprawdzanie poprawności komunikatu reprezentuje jedną linię obrony w ochronie aplikacji WCF. Z tej metody Sprawdź poprawność wiadomości przy użyciu schematów do ochrony przed atakiem przez złośliwego klienta operacji usługi WCF. Sprawdź poprawność wszystkich komunikatów odebranych przez klienta do ochrony klienta przed atakiem przez złośliwe usługi. Sprawdzanie poprawności komunikatu umożliwia sprawdzanie poprawności komunikaty podczas operacji korzystać kontraktów komunikatu lub kontraktów danych, których nie można wykonać przy użyciu sprawdzanie poprawności parametru. Sprawdzanie poprawności komunikatu służy do tworzenia logiki sprawdzania poprawności wewnątrz schematów, a tym samym zapewniając większą elastyczność i zmniejsza czas programowania. Schematy mogą być ponownie używane w różnych aplikacjach wewnątrz organizacji, standardów dotyczących reprezentację danych. Ponadto sprawdzanie poprawności komunikatu umożliwia ochronę operacji, jeśli używają oni bardziej złożone typy danych obejmujących reprezentującej logiki biznesowej.</p><p>Aby wykonać sprawdzanie poprawności komunikatu, najpierw utworzyć schemat, który reprezentuje operacje usługi i typy danych używanych przez te operacje. Następnie można utworzyć klasy .NET, która implementuje inspektora komunikat niestandardowego klienta i niestandardowe dyspozytora komunikatów inspektora sprawdzania poprawności komunikatów wysłany/odebrany z usługi. Następnie zaimplementowaniem zachowania punktu końcowego niestandardowe, aby włączyć sprawdzanie poprawności komunikatu zarówno klient, jak i usługi. Na koniec wdrożenia elementu konfiguracji niestandardowej klasy, który umożliwia uwidocznienie zachowanie rozszerzonej niestandardowe punktu końcowego w pliku konfiguracyjnym usługi lub klienta"</p>|

## <a id="parameters"></a>Sprawdzanie poprawności wejściowy WCF za pomocą parametru inspektorzy

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroki** | <p>Sprawdzanie poprawności danych wejściowych i danych reprezentuje jeden ważne linię obrony w ochronie aplikacji WCF. Należy sprawdzić, czy wszystkie parametry ujawniony w operacji usługi WCF w celu ochrony przed atakiem usługi przez złośliwego klienta. Z drugiej strony należy także sprawdzić, czy wszystkie wartości zwracanych odebranych przez klienta do ochrony klienta przed atakiem przez złośliwe usługi</p><p>Usługi WCF udostępniają punkty rozszerzeń różnych, które pozwalają dostosować zachowanie środowiska uruchomieniowego usługi WCF, tworząc niestandardowe rozszerzenia. Inspektorzy komunikatów i parametrów inspektorzy są dwa mechanizmy rozszerzeń używane do uzyskania większej kontroli nad danymi przekazywanie między klientem a usługą. Należy użyć parametru inspektorzy do sprawdzania poprawności danych wejściowych i użyć inspektorzy komunikatów tylko wtedy, gdy trzeba sprawdzić cały komunikat przepływu i usługi.</p><p>Do wykonywania sprawdzania poprawności danych wejściowych, będzie kompilacji klasę platformy .NET i wdrożyć inspektora parametru niestandardowego w celu sprawdzania poprawności parametrów operacji w usłudze. Następnie będzie implementowany zachowanie niestandardowe punktu końcowego można włączyć weryfikację zarówno klient, jak i usługi. Na koniec będzie implementowany element konfiguracji niestandardowej w klasie, która umożliwia uwidocznienie zachowanie rozszerzonej niestandardowe punktu końcowego w pliku konfiguracyjnym usługi lub klienta</p>|
