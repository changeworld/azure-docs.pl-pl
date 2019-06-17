---
title: Dane wejściowe weryfikacji — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 0803ade7613480621a0cd87f9944ee5f55bf432c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586309"
---
# <a name="security-frame-input-validation--mitigations"></a>Ramka zabezpieczeń: Dane wejściowe weryfikacji | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Wyłącz funkcje tworzenia skryptów wszystkie przekształcenia przy użyciu arkuszy stylów niezaufanych XSLT](#disable-xslt)</li><li>[Upewnij się, że każdej strony zawierające zawartość musi użytkownika oznacza brak zgody na automatyczne wykrywanie MIME](#out-sniffing)</li><li>[Wzmacniania ochrony lub wyłączanie rozpoznawania jednostki XML](#xml-resolution)</li><li>[Aplikacje korzystające z http.sys przeprowadzenie weryfikacji canonicalization adresu URL](#app-verification)</li><li>[Upewnij się, że odpowiednie formanty są stosowane podczas akceptowania plików od użytkowników](#controls-users)</li><li>[Upewnij się, że bezpieczny parametry są używane w aplikacji sieci Web uzyskać dostęp do danych](#typesafe)</li><li>[Użyj klasy powiązanie osobnymi plikami modelu lub listy powiązania filtru, aby zapobiec MVC przydziału pamięci masowej luk w zabezpieczeniach](#binding-mvc)</li><li>[Kodowanie wyjścia niezaufanych sieci web przed renderowaniem](#rendering)</li><li>[Wykonywanie walidacji danych wejściowych i filtrowanie typu string wszystkie właściwości modelu](#typemodel)</li><li>[Narzędzie oczyszczania powinny być stosowane na pola formularza, które akceptują znaków, np., Edytor tekstu sformatowanego](#richtext)</li><li>[Nie przypisuj elementów DOM do ujścia, które nie mają wbudowane kodowania](#inbuilt-encode)</li><li>[Sprawdź, czy wszystkie przekierowania w aplikacji zostaną zamknięte lub wykonywane w sposób bezpieczny](#redirect-safe)</li><li>[Implementowanie walidacji danych wejściowych na wszystkich parametrów typu ciąg zaakceptowane przez metody kontrolera](#string-method)</li><li>[Ustawić limitu górnego limitu czasu dla wyrażenia regularnego, przetwarzania, aby zapobiec DoS ze względu na nieprawidłowy wyrażeń regularnych](#dos-expression)</li><li>[Unikaj używania Html.Raw w widokami Razor](#html-razor)</li></ul> | 
| **Baza danych** | <ul><li>[W procedurach składowanych nie należy używać zapytań dynamicznych](#stored-proc)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że weryfikacja modelu odbywa się na metody interfejsu API sieci Web](#validation-api)</li><li>[Implementowanie walidacji danych wejściowych na wszystkich parametrów typu ciąg zaakceptowane przez metody interfejsu API sieci Web](#string-api)</li><li>[Upewnij się, że bezpieczny są używane parametry w internetowego interfejsu API dostępu do danych](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Użyj sparametryzowane zapytania SQL usługi Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Weryfikacja danych wejściowych WCF przez powiązanie ze schematem](#schema-binding)</li><li>[Weryfikacja wprowadzania WCF za pomocą parametru inspektorzy](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Wyłącz funkcje tworzenia skryptów wszystkie przekształcenia przy użyciu arkuszy stylów niezaufanych XSLT

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [XSLT Security](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript Property](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroki** | XSLT obsługuje wykonywanie skryptów w arkusze stylów, za pomocą `<msxml:script>` elementu. Dzięki temu funkcje niestandardowe, które ma być używany w transformacji XSLT. Skrypt jest wykonywany w kontekście tego procesu wykonywania transformacji. XSLT, skrypt musi zostać wyłączone w niezaufanych środowiska, aby zapobiec wykonaniu niezaufanego kodu. *Jeśli przy użyciu platformy .NET:* XSLT, obsługa skryptów jest domyślnie wyłączona; jednak należy upewnić się, że go nie została jawnie włączona przy użyciu `XsltSettings.EnableScript` właściwości.|

### <a name="example"></a>Przykład 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz programu MSXML 6.0 XSLT, obsługa skryptów jest domyślnie wyłączona; należy jednak upewnić się, że jej nie została jawnie włączona za pomocą właściwości obiektu modelu DOM języka XML AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Przykład
Jeśli używasz programu MSXML 5 lub poniżej, XSLT, obsługa skryptów jest domyślnie włączone, więc użytkownik musi jawnie ją wyłączyć. Ustaw właściwość obiektu modelu DOM języka XML AllowXsltScript na wartość false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Upewnij się, że każdej strony zawierające zawartość musi użytkownika oznacza brak zgody na automatyczne wykrywanie MIME

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [IE8 Zabezpieczeń części V - kompleksową ochronę](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Kroki** | <p>Dla każdej strony mogą zawierać treści, którymi można sterować użytkownika, należy użyć nagłówka HTTP `X-Content-Type-Options:nosniff`. Aby spełnić to wymaganie, można ustawić wymaganego nagłówka strony, strony dla tych stron, które mogą zawierać zawartość użytkownika musi lub można ustawić w globalnie dla wszystkich stron w aplikacji.</p><p>Każdy typ pliku dostarczane przez serwer sieci web ma skojarzoną [typ MIME](https://en.wikipedia.org/wiki/Mime_type) (nazywane również *typu zawartości*), który opisuje rodzaj zawartości (oznacza to, obrazów, tekstu, aplikacji itp.)</p><p>Nagłówek X-zawartości-typ-Options jest nagłówek HTTP, która umożliwia deweloperom określić, że jego zawartość nie powinny być ten MIME. Ten nagłówek zaprojektowano w celu zmniejszenia wykrywanie MIME ataków. Dodano obsługę dla tego pliku nagłówkowego w programie Internet Explorer 8 (IE8)</p><p>Tylko użytkownicy programu Internet Explorer 8 (IE8) będą mogli korzystać z X-zawartości-typ-Options. Poprzednie wersje programu Internet Explorer nie respektują obecnie nagłówek X-zawartości — typ-Options</p><p>Program Internet Explorer 8 (lub nowszy) są tylko ważniejszymi przeglądarkami, umożliwiającą implementację funkcji rezygnacji wykrywanie MIME. Jeśli innej największej przeglądarki (Firefox i Safari, przeglądarka Chrome) zaimplementować podobne funkcje, tego zalecenia zostanie zaktualizowana w celu składnia dla tych przeglądarek, które również zawierać</p>|

### <a name="example"></a>Przykład
Aby włączyć wymagany nagłówek globalnie dla wszystkich stron w aplikacji, wykonaj jedną z następujących czynności: 

* Dodaj nagłówek w pliku web.config, jeśli aplikacja jest obsługiwana przez Internetowe usługi informacyjne (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Dodaj nagłówek za pośrednictwem aplikacji globalnej\_BeginRequest 

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

* Należy włączyć wymaganego nagłówka tylko do konkretnych stron, dodając ją do poszczególnych odpowiedzi: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Wzmacniania ochrony lub wyłączanie rozpoznawania jednostki XML

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Rozszerzenie jednostki XML](https://capec.mitre.org/data/definitions/197.html), [ataki XML "odmowa usługi" i mechanizmów obronnych](https://msdn.microsoft.com/magazine/ee335713.aspx), [Omówienie zabezpieczeń programu MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [najlepsze rozwiązania dotyczące zabezpieczania kodu MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Odwołanie do protokołu NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [rozpoznawania odwołań zewnętrznych](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroki**| <p>Chociaż nie jest powszechnie używany, to funkcja języka XML, który umożliwia analizatora XML rozwinąć makra jednostki z wartości zdefiniowanych w obrębie samego dokumentu lub ze źródeł zewnętrznych. Na przykład, dokument może zdefiniować jednostkę "NazwaFirmy" o wartości "Microsoft", więc, każdym razem, gdy tekst "&companyname;" pojawia się w dokumencie, jest automatycznie zastępowany tekstem firmy Microsoft. Lub dokumentu może zdefiniować jednostkę "MSFTStock", który odwołuje się do zewnętrznej usługi internetowej można pobrać bieżącej wartości zasobu firmy Microsoft.</p><p>Następnie w dowolnym momencie "&MSFTStock;" pojawia się w dokumencie, jest automatycznie zastępowany przy użyciu bieżącego najniższej ceny akcji. Jednak ta funkcja mogą być użyte do utworzenia typu odmowa usługi (DoS) warunków. Osoba atakująca można zagnieżdżać wiele jednostek, aby utworzyć bomba XML rozszerzenia wykładniczego, który wykorzystuje całą dostępną pamięć, w systemie. </p><p>Alternatywnie on można utworzyć odwołania zewnętrznego, który przesyła strumieniowo z powrotem nieskończonej ilości danych lub który po prostu zawiesza się wątek. Co w efekcie wszystkie zespoły należy wyłączyć wewnętrznych lub zewnętrznych rozpoznawania jednostki XML, jeśli swoich aplikacji nie używać go, lub ręcznie ograniczyć ilość pamięci i czasu, który ją mogą wykorzystywać do rozpoznawania jednostek, jeśli jest to jest to absolutnie konieczne. Rozpoznawanie jednostek nie jest wymagane przez aplikację, następnie je wyłączyć. </p>|

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
Należy pamiętać, że wartość domyślna `ProhibitDtd` w `XmlReaderSettings` ma wartość true, ale w `XmlTextReader` ma wartość false. Jeśli używasz XmlReaderSettings nie należy ustawić ProhibitDtd dla elementu wartość true, jawnie, ale jest zalecane dla sake bezpieczeństwa możesz zrobić. Należy również zauważyć, że klasy XmlDocument umożliwia rozpoznawanie jednostek domyślnie. 

### <a name="example"></a>Przykład
Aby wyłączyć rozpoznawania jednostek dla XmlDocuments, użyj `XmlDocument.Load(XmlReader)` przeciążenia metody obciążenia i ustaw odpowiednie właściwości w argumencie XmlReader wyłączyć rozdzielczości, jak pokazano w poniższym kodzie: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Przykład
Jeśli wyłączenie rozpoznawania jednostek nie jest możliwe dla aplikacji, należy ustawić właściwość XmlReaderSettings.MaxCharactersFromEntities rozsądną wartość zgodnie z potrzebami Twojej aplikacji. To powoduje ograniczenie wpływu potencjalnych ataków DoS wykładniczego rozszerzenia. Poniższy kod stanowi przykład tej metody: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Przykład
Jeśli potrzebujesz rozwiązać wbudowanej jednostki, ale czy nie trzeba rozwiązać podmioty zewnętrzne, ustaw właściwość XmlReaderSettings.XmlResolver na wartość null. Na przykład: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Należy pamiętać, że w program MSXML6, ProhibitDtd dla elementu ma wartość true (wyłączanie przetwarzanie elementu DTD) domyślnie. Kod, OSX firmy Apple dla systemu iOS istnieją dwa parsery XML, których można użyć: NSXMLParser i libXML2. 

## <a id="app-verification"></a>Aplikacje korzystające z http.sys przeprowadzenie weryfikacji canonicalization adresu URL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Dowolne aplikacje korzystające z http.sys należy przestrzegać następujących wytycznych:</p><ul><li>Ogranicz długość adresu URL do nie więcej niż 16 384 znaków (ASCII lub Unicode). Jest to bezwzględny maksymalna długość adresu URL na podstawie domyślnego ustawienia usług Internet Information Services (IIS) 6. Witryny sieci Web należy dążyć do krótszego niż to, jeśli jest to możliwe</li><li>Użyj standardowych klas we/wy pliku .NET Framework (na przykład FileStream), jak te będą zalet reguły canonicalization w FX platformy .NET</li><li>Jawne tworzenie listy dozwolonych, znanych nazw plików</li><li>Jawnie Odrzuć znane typy plików, nie będzie obsługiwać odrzutów UrlScan: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm [g], stm, drukarki, ini, pol, pliki dat</li><li>CATCH następujące wyjątki:<ul><li>System.ArgumentException (dla nazwy urządzenia)</li><li>System.NotSupportedException (w przypadku strumieni danych)</li><li>System.IO.FileNotFoundException (w przypadku nieprawidłowe nazwy plików o zmienionym znaczeniu)</li><li>System.IO.DirectoryNotFoundException (w przypadku nieprawidłowy o zmienionym znaczeniu katalogów)</li></ul></li><li>*Nie* wyróżnienia do pliku systemu Win32 API we/wy. Na nieprawidłowy adres URL bez problemu zmieniała zwracany błąd 400 użytkownikowi, a rzeczywiste błąd logowania.</li></ul>|

## <a id="controls-users"></a>Upewnij się, że odpowiednie formanty są stosowane podczas akceptowania plików od użytkowników

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Nieograniczone przekazywanie pliku](https://www.owasp.org/index.php/Unrestricted_File_Upload), [pliku podpisu tabeli](https://www.garykessler.net/library/file_sigs.html) |
| **Kroki** | <p>Przekazane pliki stanowią znaczne ryzyko do aplikacji.</p><p>Pierwszym etapem wiele ataków jest uzyskanie kodu służącego do systemu, aby na ataki. Następnie ataku tylko musi znaleźć sposobem uzyskania wykonywanego kodu. Przy użyciu przekazywanie pliku pomaga osoba atakująca wykonać pierwszy krok. Tym przejęcia całego systemu, system plików przeciążona lub bazy danych, przekazywanie ataków systemów zaplecza i proste wystarcza mogą się różnić skutków przekazywania plików bez ograniczeń.</p><p>Zależy to aplikacja wykonuje z przekazanego pliku, a szczególnie, gdzie są przechowywane. Brak sprawdzania poprawności po stronie serwera przekazywania plików. Następujące opcje zabezpieczeń powinny być zrealizowane funkcji przekazywania plików:</p><ul><li>Sprawdzanie rozszerzenie pliku (powinna być akceptowana prawidłowy zestaw dozwolonego typu plików)</li><li>Maksymalny limit rozmiaru</li><li>Nie można przekazać pliku do webroot; Lokalizacja powinna być katalogu na dysku niesystemowym</li><li>Konwencja nazewnictwa należy stosować, zastępuje w taki sposób, że nazwa przekazany plik ma pewne losowości, aby zapobiec pliku</li><li>Pliki powinny być przeskanowane dla oprogramowania antywirusowego przed zapisem na dysku</li><li>Upewnij się, sprawdzania poprawności dla złośliwych znaków nazwy pliku i wszelkie inne metadane (np. ścieżka pliku)</li><li>Podpis format pliku powinna być sprawdzana, aby uniemożliwić użytkownikowi przekazywania pliku masqueraded (np. Trwa przekazywanie pliku exe, zmieniając rozszerzenie txt)</li></ul>| 

### <a name="example"></a>Przykład
Ostatni punkt dotyczące weryfikacji podpisu format pliku można znaleźć klasy poniżej, aby uzyskać szczegółowe informacje: 

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

## <a id="typesafe"></a>Upewnij się, że bezpieczny parametry są używane w aplikacji sieci Web uzyskać dostęp do danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Jeśli używasz kolekcję parametrów, traktuje SQL dane wejściowe są jako wartości literału, a nie jako kod wykonywalny. Kolekcja parametrów może być używana do wymuszania ograniczenia typu i długości danych wejściowych. Wartości spoza zakresu wywołanie wyjątku. Jeśli parametry SQL bezpiecznego typu nie są używane, atakujący może być można wykonać na wstrzyknięciu kodu, które są osadzone w niefiltrowane danych wejściowych.</p><p>Użyj bezpiecznych parametrów typu, podczas tworzenia zapytania SQL, aby uniknąć możliwych wstrzyknięciu kodu SQL, które mogą wystąpić z nieprzefiltrowanymi danymi wejściowymi. Bezpieczne parametrów typu można użyć z procedur składowanych i dynamicznych instrukcji SQL. Parametry są traktowane jako wartości literału przez bazę danych, a nie kodu wykonywalnego. Parametry również są sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład 
Poniższy kod pokazuje, jak za pomocą bezpiecznych parametrów typu SqlParameterCollection podczas wywoływania procedury składowanej. 

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
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli dane nie jest zgodny z typem lub długość zdefiniowaną przez parametr, klasa parametr SqlParameter zgłasza wyjątek. 

## <a id="binding-mvc"></a>Użyj klasy powiązanie osobnymi plikami modelu lub listy powiązania filtru, aby zapobiec MVC przydziału pamięci masowej luk w zabezpieczeniach

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Atrybuty metadanych na](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [publiczny klucz zabezpieczeń luk w zabezpieczeniach i środki zaradcze](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [pełny Przewodnik po masa przypisania we wzorcu ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [wprowadzenie do struktury jednostek przy użyciu platformy MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroki** | <ul><li>**Kiedy powinna wyglądać dla polegającymi luk w zabezpieczeniach? -** Polegającymi luk w zabezpieczeniach może wystąpić, dowolnym miejscu, powiąż klasy modelu z danych wejściowych użytkownika. Środowisk, takich jak MVC może reprezentować dane użytkowników w niestandardowych klas platformy .NET, w tym zwykłych starych obiektów CLR (POCOs). MVC automatycznie wypełni te klasy modelu przy użyciu danych z żądania, podając reprezentację wygodne do radzenia sobie z danymi wejściowymi użytkownika. Gdy te klasy zawierają właściwości, które nie powinny być ustawiane przez użytkownika, aplikacja może być narażony na polegającymi ataków, które umożliwiają kontrolę użytkownika danych, które nigdy nie aplikacji przeznaczonych. Wiązanie modelu MVC, np. bazy danych dostęp do technologii, takich jak obiekt/relacyjnych liczby maperów, takich jak Entity Framework często również pomocy technicznej przy użyciu obiektów POCO do reprezentowania danych w bazie danych. Te klasy modelu danych zawierają ten sam wygodnie korzystać w pracy z bazy danych, jak MVC w radzenia sobie z danymi wejściowymi użytkownika. Ponieważ zarówno MVC, jak i bazy danych obsługuje podobne modeli, na przykład obiektów POCO wygląda na to łatwe do ponownego użycia tych samych klas, zarówno w celach. Tej praktyką nie powiedzie się zachować separacji zagadnień i jest jeden obszar wspólnego, gdzie niezamierzone właściwości są udostępniane z wiązaniem modelu, umożliwiające publikowanie nadmiernego ataków.</li><li>**Dlaczego nie należy użyć Moje klasy modelu niefiltrowane bazy danych jako parametry do Moje działania MVC? -** Wiązania modelu MVC ponieważ powiąże niczego w tej klasie. Nawet jeśli dane nie są wyświetlane w widoku, złośliwy użytkownik może wysyłać żądania HTTP z tymi danymi uwzględnione i MVC zostanie gladly powiązać go, ponieważ akcji o tym, czy klasa bazy danych kształtu danych, dla których należy zaakceptować na dane wejściowe użytkownika.</li><li>**Dlaczego należy interesujące kształtu używany do wiązania modelu? -** Wiązania modelu przy użyciu platformy ASP.NET MVC z modelami nadmiernych udostępnia atakami polegającymi aplikacji. Nadmiernego ogłaszania może umożliwić osobom atakującym zmienianie danych aplikacji poza co Deweloper przeznaczona, takie jak zastępowanie ceny dla elementu lub uprawnienia zabezpieczeń dla konta. Aplikacje powinny używać określonych akcji powiązań modeli (lub listy filtrów z określoną właściwością dozwolone) w celu zapewnienia jawnego kontrakt dla jakich niezaufane dane wejściowe umożliwić za pomocą wiązania modelu.</li><li>**Występują modeli oddzielne powiązania, po prostu duplikowania kodu? -** Nie jest to kwestia separacji zagadnień. Ponowne użycie modeli bazy danych za pomocą metod akcji są informujący o tym wszelkie właściwość (lub podrzędnej), w tym klasy można ustawić przez użytkownika w żądaniu HTTP. Jeśli to nie chcemy MVC zrobić, potrzebny listę filtrów lub kształtu osobnej klasy, aby pokazać MVC, jakie dane mogą pochodzić z zamiast tego dane wejściowe użytkownika.</li><li>**Jeśli mam modeli oddzielne powiązania dla danych wejściowych użytkownika, czy muszę zduplikowane wszystkie moje atrybuty adnotacji danych? -** Niekoniecznie. MetadataTypeAttribute klasy modelu bazy danych służy do łącza do metadanych na klasy wiązania modelu. Po prostu należy pamiętać, że typ odwołuje się MetadataTypeAttribute muszą być podzbiorem odwołujący się typu (może mieć mniejszej liczby właściwości, ale nie więcej).</li><li>**Przenoszenie danych i z powrotem między modelami danych wejściowych użytkownika i modele baz danych jest niewygodna. Czy mogę po prostu skopiować za pośrednictwem wszystkich właściwości, za pomocą odbicia? -** Tak. Jedyne właściwości, które pojawiają się w modelach powiązania są tymi, które mają określone jako bezpieczne dla danych wejściowych użytkownika. To nie ma powodu zabezpieczeń, który uniemożliwia przy użyciu odbicia do skopiowania wszystkich właściwości, które istnieją we wspólnych między tymi dwoma modelami.</li><li>**Co [powiązania (Wyklucz = "WinMgmt...")]. Można używać, zamiast modeli oddzielne powiązania? -** To podejście nie jest zalecane. Przy użyciu [powiązania (Wyklucz = "WinMgmt...")] oznacza, że wszystkie nowej właściwości możliwej do wiązania domyślnie. Po dodaniu nowej właściwości jest dodatkowego kroku, aby pamiętać o bezpieczeństwo rzeczy, a nie masz projekt jest domyślnie bezpieczny. W zależności od dewelopera sprawdzania tej listy, za każdym razem, gdy właściwość jest dodawany jest ryzykowne.</li><li>**Jest [powiązania (Include = "WinMgmt...")] przydatne w przypadku operacji edycji? -** Nie. [Powiązanie (Include = "WinMgmt...")] są przeznaczone tylko dla operacji WSTAWIANIA stylu (dodanie nowych danych). Dla operacji aktualizacji stylu (zmiana istniejących danych) Użyj innego sposobu, takich jak mających oddzielne powiązania modeli lub przekazując jawną listę dozwolonych właściwości do UpdateModel lub TryUpdateModel. Dodawanie [powiązania (Include = "â...")] atrybutu w operacji edycji oznacza, że MVC będą tworzone jest wystąpienie obiektu Ustaw tylko właściwości z listy, pozostawiając wszystkie pozostałe wartości domyślne. Gdy dane są utrwalane, całkowicie zastąpi istniejącą jednostkę resetowanie wartości pominięty właściwości do wartości domyślnych. Na przykład, jeśli został pominięty IsAdmin [powiązania (Include = "â...")] atrybutu w operacji edycji, każdy użytkownik, którego nazwa był edytowany za pomocą tej akcji spowoduje przywrócenie IsAdmin = false (każdy użytkownik edytowanych spowoduje utratę stan administratora). Jeśli chcesz zapobiec aktualizacji do określonych właściwości, należy użyć jednej z metod powyżej. Należy pamiętać, że niektóre wersje narzędzia MVC wygenerowane klasy kontrolera przy użyciu [powiązania (Include = "â...")] edytować akcje i oznacza, że usunięcie właściwości z tej listy będą zapobiegać atakom nadmiernego ogłaszania. Jednak zgodnie z powyższym opisem takie podejście nie działa zgodnie z oczekiwaniami i zamiast tego spowoduje zresetowanie wszystkie dane w pominięty właściwości do wartości domyślnych.</li><li>**Dla operacji tworzenia, czy istnieją wszystkie ostrzeżenia przy użyciu [powiązania (Include = "WinMgmt...")] zamiast modeli oddzielne powiązania? -** Tak. Najpierw to podejście nie działa w przypadku scenariuszy edycji konieczności utrzymywania dwa oddzielne podejścia łagodzenia wszystkich nadmiernego ogłaszania luk w zabezpieczeniach. Modele powiązania drugiej, oddzielnej wymuszania separacji między kształtu używany w danych wejściowych użytkownika i kształtu używany na potrzeby stanu trwałego, coś [powiązania (Include = "WinMgmt...")] nie działa. Po trzecie, należy pamiętać, że [powiązania (Include = "WinMgmt...")] może obsługiwać tylko właściwości najwyższego poziomu; Nie można zezwolić tylko części właściwości podrzędnych (na przykład "Details.Name") w atrybucie. Na koniec i prawdopodobnie najważniejsze, przy użyciu [powiązania (Include = "WinMgmt...")] dodanie dodatkowego kroku należy pamiętać, wtedy klasa jest używana do wiązania modelu. Jeśli nowe metody akcji wiąże klasy danych bezpośrednio i zapomni obejmujący [powiązania (Include = "â...")] atrybutu, może być narażony na polegającymi ataków, więc [powiązania (Include = "â...")] podejście jest nieco mniej bezpieczna opcja domyślnie. Jeśli używasz [powiązania (Include = "WinMgmt...")], należy zawsze pamiętaj, aby określić go za każdym razem, gdy klasy Twoje dane są wyświetlane jako parametry metody akcji.</li><li>**Operacje tworzenia, co sądzisz o umieszczenie [powiązania (Include = "WinMgmt...")] atrybutu w samej klasy modelu? Nie podejście to uniknąć konieczności do zapamiętania, umieszczenie atrybutu w każdej metody akcji? -** To podejście sprawdza się w niektórych przypadkach. Przy użyciu [powiązania (Include = "WinMgmt...")] na typ modelu, sama (a nie na parametry akcji za pomocą tej klasy), uniknąć konieczności Pamiętaj, aby uwzględnić [powiązania (Include = "WinMgmt...")] atrybutu w każdej metody akcji. Za pomocą atrybutu bezpośrednio w klasie skutecznie tworzy oddzielny obszar powierzchni tej klasy do celów powiązania modelu. Jednak takie podejście umożliwia tylko na jeden kształt powiązanie modelu na model klasy. Jeśli jedna metoda akcji musi umożliwić wiązanie modelu pola (na przykład tylko administrator akcję, która aktualizuje ról użytkowników), a inne akcje muszą być Zapobiegaj powiązaniu modelu tego pola, to podejście nie będzie działać. Każda klasa może mieć tylko jeden kształt powiązanie modelu; Jeśli różne akcje muszą kształty powiązania innego modelu, muszą oni reprezentują te rozdzielone kształty przy użyciu obu klas powiązania osobnymi plikami modelu lub rozdzielić [powiązania (Include = "WinMgmt...")] atrybutów na metody akcji.</li><li>**Co to są wiązanie modeli? Są one tak samo jak modeli widoków -** Są dwa pojęcia pokrewne. Powiązanie modelu termin odnosi się do klasy modelu używany na liście parametrów akcji (kształt przekazywanych do metody akcji z wiązania modelu MVC). Model widoku termin odnosi się do klasy modelu przekazywanych do widoku z metody akcji. Przy użyciu modelu specyficzne dla widoku jest powszechnym podejściem do przekazywania danych z metodą akcji do widoku. Często ten kształt jest także odpowiednia dla wiązania modelu i model widoku termin może służyć do odwoływania się tego samego modelu, które są używane w obu miejscach. Ściślej mówiąc, ta procedura opowiada, w szczególności wiązanie modeli, koncentrując się na kształcie przekazywany do akcji, która jest istotnymi dla celów przydziału pamięci masowej.</li></ul>| 

## <a id="rendering"></a>Kodowanie wyjścia niezaufanych sieci web przed renderowaniem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, Web Forms, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Jak zapobiec skryptów między witrynami w programie ASP.NET:](https://msdn.microsoft.com/library/ms998274.aspx), [skryptów między witrynami](https://cwe.mitre.org/data/definitions/79.html), [arkusz porad zapobiegania XSS (skryptów krzyżowych)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Kroki** | Skryptów między witrynami (zwykle skrót XSS) jest wektor ataku dla usług online albo dowolny aplikacji/składnik, który wykorzystuje dane wejściowe z sieci web. XSS luk w zabezpieczeniach może umożliwić atakującemu uruchomienia skryptu na komputerze przez innego użytkownika za pośrednictwem aplikacji sieci web na ataki. Złośliwe skrypty może służyć do kradzieży plików cookie, a w przeciwnym razie manipulowanie ofiary w maszynie za pomocą języka JavaScript. Walidacja danych wejściowych użytkownika, zapewnienie, że jest poprawnie sformułowany i kodowanie, zanim zostanie wyświetlony na stronie sieci web nie będzie mógł XSS. Walidacja danych wejściowych i danych wyjściowych kodowania może odbywać się przy użyciu biblioteki ochrony sieci Web. Dla kodu zarządzanego (C\#, VB.NET, itp.), użyj jednego lub więcej odpowiednie metody kodowania z biblioteki ochrony sieci Web (Anti-XSS), w zależności od kontekstu, w którym pobiera dyskowe widoczne dane wejściowe użytkownika:| 

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

## <a id="typemodel"></a>Wykonywanie walidacji danych wejściowych i filtrowanie typu string wszystkie właściwości modelu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Dodawanie sprawdzania poprawności](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [sprawdzanie poprawności modelu danych w aplikacji MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [wytyczne dla aplikacji platformy ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | <p>Przed użyciem ich w aplikacji, aby upewnić się, że aplikacja jest chronione są względem danych wprowadzonych przez złośliwego użytkownika, należy sprawdzić poprawność wszystkich parametrów wejściowych. Sprawdź poprawność wartości wejściowych za pomocą wyrażeń regularnych walidacji po stronie serwera za pomocą strategii sprawdzania poprawności listy dozwolonych adresów. Dane wejściowe użytkownika unsanitized / parametry przekazane do metody może spowodować, że kod iniekcją.</p><p>Dla aplikacji sieci web punkty wejścia mogą również obejmować pól formularza, ciągami zapytań, pliki cookie, nagłówków HTTP i parametry usługi sieci web.</p><p>Następujące testy sprawdzania poprawności danych wejściowych, należy wykonać podczas wiązania modelu:</p><ul><li>Właściwości modelu powinna być oznaczona przy użyciu adnotacji wyrażenia regularnego, do których można zgłaszać dozwolonych znaków i maksymalną dopuszczalną długość</li><li>Metody kontrolera, należy wykonać ModelState ważności</li></ul>|

## <a id="richtext"></a>Narzędzie oczyszczania powinny być stosowane na pola formularza, które akceptują znaków, np., Edytor tekstu sformatowanego

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Kodowanie niebezpieczny](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [moduł czyszczący HTML](https://github.com/mganss/HtmlSanitizer) |
| **Kroki** | <p>Zidentyfikuj wszystkie statyczne znaczniki, które chcesz użyć. Powszechną praktyką jest, aby ograniczyć formatowanie do bezpiecznego elementów HTML, takich jak `<b>` (pogrubiony) i `<i>` (kursywa).</p><p>Przed zapisaniem danych kodowanie HTML go. Dzięki temu dowolny złośliwy skrypt bezpieczne, powodując go do obsłużenia jako tekst, a nie jako kod wykonywalny.</p><ol><li>Wyłączono weryfikację żądań ASP.NET, dodając parametr ValidateRequest = "false" atrybutu \@ Page — dyrektywa</li><li>Kodowanie ciąg wejściowy za pomocą metody HtmlEncode</li><li>Użyj klasy StringBuilder i wywołać jej metodę Zastąp, aby wybiórczo usunąć kodowanie elementów kodu HTML, które chcesz zezwolić</li></ol><p>Strona do weryfikacji żądań ASP.NET wyłącza odwołania, ustawiając `ValidateRequest="false"`. Jego HTML koduje dane wejściowe i zezwala na selektywne `<b>` i `<i>` również bibliotekę .NET dla sanityzacji HTML można również użyć.</p><p>HtmlSanitizer to biblioteka .NET do czyszczenia fragmenty kodu HTML i dokumenty z konstrukcji, które mogą prowadzić do atakom XSS. Używa AngleSharp analizowanie, manipulowanie i renderowania kodu HTML i CSS. HtmlSanitizer można zainstalować jako pakiet NuGet, a dane wejściowe użytkownika mogą być przekazywane za pośrednictwem odpowiednich HTML i CSS oczyszczania metod, jeśli ma to zastosowanie, po stronie serwera. Należy pamiętać, że oczyszczania jako kontrola zabezpieczeń należy traktować tylko jako ostatnia opcja.</p><p>Walidacja danych wejściowych i danych wyjściowych kodowania są traktowane jako lepsze zabezpieczenia.</p> |

## <a id="inbuilt-encode"></a>Nie przypisuj elementów DOM do ujścia, które nie mają wbudowane kodowania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wiele funkcji języka javascript nie kodowania domyślnie. Podczas przypisywania niezaufane dane wejściowe do elementów modelu DOM przy użyciu takich funkcji, może spowodować wielu lokacji wykonań skryptu (XSS).| 

### <a name="example"></a>Przykład
Poniżej przedstawiono przykłady niezabezpieczone: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nie używaj `innerHtml`; zamiast tego użyć `innerText`. Podobnie, zamiast z `$("#elm").html()`, użyj `$("#elm").text()` 

## <a id="redirect-safe"></a>Sprawdź, czy wszystkie przekierowania w aplikacji zostaną zamknięte lub wykonywane w sposób bezpieczny

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Framework autoryzacji OAuth 2.0 — otwórz readresatory](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroki** | <p>Projekt aplikacji wymagających przekierowanie do lokalizacji, dostarczone przez użytkownika muszą ograniczyć cele możliwe przekierowania do wstępnie zdefiniowanej listy "bezpieczne" Lokacje lub domeny. Przekierowuje wszystkie w aplikacji musi być zamknięty bezpieczny.</p><p>W tym celu:</p><ul><li>Zidentyfikuj wszystkie przekierowania</li><li>Implementowanie odpowiednie środki zaradcze dla każdego przekierowania. Odpowiednie środki zaradcze zawierają potwierdzenie przekierowania listy dozwolonych lub użytkownika. Jeśli witryny sieci web lub usługi z luk w zabezpieczeniach otwarte przekierowywanie korzysta z dostawców tożsamości uwierzytelniania serwisu Facebook/OAuth/OpenID, osoba atakująca może wykradać tokenu logowania użytkownika i personifikacji tego użytkownika. Jest to ryzykiem, gdy przy użyciu protokołu OAuth, która jest opisane w dokumencie RFC 6749 "protokołu OAuth 2.0 autoryzacji program Framework", sekcji 10.15 "Otwórz przekierowuje" Podobnie, poświadczeń użytkownika mogą zostać złamane spear phishing ataków przy użyciu przekierowania Otwórz</li></ul>|

## <a id="string-method"></a>Implementowanie walidacji danych wejściowych na wszystkich parametrów typu ciąg zaakceptowane przez metody kontrolera

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Sprawdzanie poprawności modelu danych w aplikacji MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [wytyczne dla aplikacji platformy ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | W przypadku metod, które akceptują tylko typem danych pierwotnych, a nie modeli jako argument powinna być podejmowana walidacji danych wejściowych za pomocą wyrażenia regularnego. W tym miejscu regex.ismatch — powinien być używany z wzorcem prawidłowe wyrażenie regularne. Jeśli dane wejściowe nie odpowiada określonemu wyrażeniu regularnemu, formant nie należy kontynuować, a powinien być wyświetlany odpowiedniego ostrzeżenia dotyczące niepowodzenia weryfikacji.| 

## <a id="dos-expression"></a>Ustawić limitu górnego limitu czasu dla wyrażenia regularnego, przetwarzania, aby zapobiec DoS ze względu na nieprawidłowy wyrażeń regularnych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, Web Forms, MVC5, MVC6  |
| **Atrybuty**              | ND  |
| **Odwołania**              | [DefaultRegexMatchTimeout Property](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroki** | Aby atakom typu odmowa usługi przed źle utworzony wyrażeń regularnych, które powodują mnóstwo wycofywania, ustawić globalne domyślny limit czasu. Jeśli czas przetwarzania trwa dłużej niż górny limit zdefiniowany, zgłasza wyjątek limitu czasu. Niczego nie skonfigurowano limit czasu będzie mieć nieograniczony.| 

### <a name="example"></a>Przykład
Na przykład następująca konfiguracja zgłosi RegexMatchTimeoutException, jeśli przetwarzanie zajmuje więcej niż 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Unikaj używania Html.Raw w widokami Razor

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| Krok | Stron sieci Web ASP.NET (Razor) kodowanie automatyczne HTML. Wszystkie ciągi drukowanymi przez nuggets osadzony kod (@ bloki) są automatycznie kodowany w formacie HTML. Jednak gdy `HtmlHelper.Raw` metoda jest wywoływana, zwraca kod znaczników, który nie ma kodowania HTML. Jeśli `Html.Raw()` używana jest metoda pomocnika, jego pomija automatyczną ochronę kodowania, która zapewnia Razor.|

### <a name="example"></a>Przykład
Poniżej znajduje się przykład niezabezpieczone: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nie używaj `Html.Raw()` o ile nie trzeba wyświetlić znaczników. Ta metoda nie powoduje wykonania danych wyjściowych kodowania niejawnie. Użyj innych pomocników platformy ASP.NET, np. `@Html.DisplayFor()` 

## <a id="stored-proc"></a>W procedurach składowanych nie należy używać zapytań dynamicznych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Ataku polegającego na iniekcji SQL wykorzystuje luki w zabezpieczeniach w walidacji danych wejściowych do uruchamiania dowolnego polecenia w bazie danych. Może wystąpić, gdy Twoja aplikacja używa danych wejściowych do konstruowania dynamicznych instrukcji SQL dostęp do bazy danych. Może również wystąpić, jeśli kod używa procedur składowanych, które są przekazywane ciągów, które zawierają dane wejściowe użytkownika raw. Przy użyciu ataku polegającego na iniekcji SQL, osoba atakująca może wykonać dowolne polecenia w bazie danych. Wszystkie instrukcje SQL (w tym instrukcje SQL w procedurach składowanych) muszą być sparametryzowane. Sparametryzowanych instrukcji SQL będzie akceptować znaki, które mają specjalne znaczenie do bazy danych SQL (na przykład pojedynczy cudzysłów) bez problemów, ponieważ są one silnie typizowane. |

### <a name="example"></a>Przykład
Poniżej przedstawiono przykład niezabezpieczone dynamiczne procedury składowanej: 

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
Tej samej procedury składowanej zaimplementowane bezpieczne jest następujący: 
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

## <a id="validation-api"></a>Upewnij się, że weryfikacja modelu odbywa się na metody interfejsu API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Weryfikacja modelu we wzorcu ASP.NET Web API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Gdy klient wysyła dane do internetowego interfejsu API, jest wymagane, aby sprawdzić poprawność danych przed wykonaniem jakiegokolwiek przetwarzania. Dla interfejsów API sieci Web platformy ASP.NET, która akceptuje modele jako dane wejściowe, korzystanie z adnotacji danych modeli do konfigurowania reguł sprawdzania poprawności dla właściwości modelu.|

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
W przypadku metody akcji kontrolera interfejsu API ważności modelu ma jawnie można sprawdzić, jak pokazano poniżej: 

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

## <a id="string-api"></a>Implementowanie walidacji danych wejściowych na wszystkich parametrów typu ciąg zaakceptowane przez metody interfejsu API sieci Web

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, MVC 5, MVC 6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Sprawdzanie poprawności modelu danych w aplikacji MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [wytyczne dla aplikacji platformy ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroki** | W przypadku metod, które akceptują tylko typem danych pierwotnych, a nie modeli jako argument powinna być podejmowana walidacji danych wejściowych za pomocą wyrażenia regularnego. W tym miejscu regex.ismatch — powinien być używany z wzorcem prawidłowe wyrażenie regularne. Jeśli dane wejściowe nie odpowiada określonemu wyrażeniu regularnemu, formant nie należy kontynuować, a powinien być wyświetlany odpowiedniego ostrzeżenia dotyczące niepowodzenia weryfikacji.|

## <a id="typesafe-api"></a>Upewnij się, że bezpieczny są używane parametry w internetowego interfejsu API dostępu do danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Jeśli używasz kolekcję parametrów, traktuje SQL dane wejściowe są jako wartości literału, a nie jako kod wykonywalny. Kolekcja parametrów może być używana do wymuszania ograniczenia typu i długości danych wejściowych. Wartości spoza zakresu wywołanie wyjątku. Jeśli parametry SQL bezpiecznego typu nie są używane, atakujący może być można wykonać na wstrzyknięciu kodu, które są osadzone w niefiltrowane danych wejściowych.</p><p>Użyj bezpiecznych parametrów typu, podczas tworzenia zapytania SQL, aby uniknąć możliwych wstrzyknięciu kodu SQL, które mogą wystąpić z nieprzefiltrowanymi danymi wejściowymi. Bezpieczne parametrów typu można użyć z procedur składowanych i dynamicznych instrukcji SQL. Parametry są traktowane jako wartości literału przez bazę danych, a nie kodu wykonywalnego. Parametry również są sprawdzane pod kątem typu i długości.</p>|

### <a name="example"></a>Przykład
Poniższy kod pokazuje, jak za pomocą bezpiecznych parametrów typu SqlParameterCollection podczas wywoływania procedury składowanej. 

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
W poprzednim przykładzie kodu wartość wejściowa nie może być dłuższa niż 11 znaków. Jeśli dane nie jest zgodny z typem lub długość zdefiniowaną przez parametr, klasa parametr SqlParameter zgłasza wyjątek. 

## <a id="sql-docdb"></a>Użyj sparametryzowane zapytania SQL usługi Cosmos DB

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Document DB | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Przedstawiamy parametryzacji SQL w usłudze Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroki** | Mimo że usługę Azure Cosmos DB obsługuje tylko zapytania tylko do odczytu, wstrzyknięcie kodu SQL jest nadal możliwe, gdy zapytania są tworzone przez łączenie z danymi wejściowymi użytkownika. Może być możliwe dla użytkownika w celu uzyskania dostępu do danych, której nie powinien mieć dostęp w ramach tej samej kolekcji przy tworzeniu złośliwych zapytań SQL. Użyj sparametryzowane zapytania SQL Jeśli zapytania są konstruowane na podstawie danych wejściowych użytkownika. |

## <a id="schema-binding"></a>Weryfikacja danych wejściowych WCF przez powiązanie ze schematem

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroki** | <p>Brak weryfikacji prowadzi do innego typu ataki przez iniekcję kodu.</p><p>Sprawdzanie poprawności komunikatu reprezentuje jedną linię obrony w ochronie aplikacji WCF. W przypadku tej metody Sprawdź poprawność wiadomości do ochrony operacji usługi WCF z ataku przez złośliwe klienta przy użyciu schematów. Sprawdź poprawność wszystkich komunikatów odebranych przez klienta do ochrony klienta przed ataku przez złośliwe usługi. Komunikat sprawdzania poprawności sprawia, że można sprawdzać poprawność wiadomości, podczas operacji używanie kontraktów komunikatu lub kontraktów danych, których nie można wykonać przy użyciu poprawność parametrów. Sprawdzanie poprawności komunikatu służy do tworzenia logiki weryfikacji wewnątrz schematów, a tym samym co zapewnia większą elastyczność i zmniejsza czas opracowywania. Schematy mogą być ponownie używane dla różnych aplikacji w organizacji, standardów dotyczących reprezentacji danych. Ponadto komunikat sprawdzania poprawności umożliwia ochronę operacji, jeśli używają oni bardziej złożone typy danych obejmujące reprezentującej logiki biznesowej.</p><p>Aby wykonać sprawdzanie poprawności komunikatu, najpierw utworzyć schemat, który reprezentuje operacji usługi i typy danych używanych przez te operacje. Następnie Utwórz klasę .NET, która implementuje Inspektor komunikatu niestandardowego klienta i niestandardowych dyspozytorów komunikatów Inspektor sprawdzania poprawności komunikatów wysłać/odebrać z usługi. Następnie możesz zaimplementować zachowania niestandardowego punktu końcowego można włączyć weryfikację komunikat zarówno klient, jak i usługi. Na koniec implementuje niestandardowy element konfiguracji w klasie, która pozwala na udostępnianie zachowanie rozszerzonej niestandardowego punktu końcowego w pliku konfiguracyjnym usługi lub klienta"</p>|

## <a id="parameters"></a>Weryfikacja wprowadzania WCF za pomocą parametru inspektorzy

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroki** | <p>Sprawdzanie poprawności danych wejściowych i danych reprezentuje jeden ważny linią obrony w ochronie aplikacji WCF. Należy sprawdzić, czy wszystkie parametry ujawnione w operacji usługi WCF do ochrony usługi od ataku przez złośliwe klienta. Z drugiej strony należy także sprawdzić, czy wszystkich wartości zwracanych odebranych przez klienta do ochrony klienta przed ataku przez złośliwe usługi</p><p>Usługi WCF udostępniają punkty rozszerzeń różnych, które pozwalają dostosować zachowanie środowiska uruchomieniowego WCF, tworząc niestandardowe rozszerzenia. Inspektorzy komunikatów i inspektorzy parametru są dwa mechanizmy rozszerzania pozwala uzyskać większą kontrolę nad danymi przekazywanie między klientem a usługą. Użytkownik powinien używać inspektorzy parametr dla walidacji danych wejściowych i inspektorzy komunikatów tylko wtedy, gdy trzeba sprawdzić cały komunikat przepływających do i z usługi.</p><p>Aby przeprowadzić sprawdzenie poprawności danych wejściowych, utworzysz klasa platformy .NET i wdrożyć Inspektor parametru niestandardowego w celu sprawdzają poprawność parametrów na operacje w usłudze. Następnie wdroży zachowanie niestandardowego punktu końcowego można włączyć weryfikację zarówno klient, jak i usługi. Na koniec wdroży niestandardowy element konfiguracji w klasie, która pozwala na udostępnianie zachowanie rozszerzonej niestandardowego punktu końcowego w pliku konfiguracyjnym usługi lub klienta</p>|
