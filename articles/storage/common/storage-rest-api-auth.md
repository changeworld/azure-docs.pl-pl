---
title: Wywoływanie operacji interfejsu API REST usług Azure Storage, w tym uwierzytelniania | Microsoft Docs
description: Wywoływanie operacji interfejsu API REST usług Azure Storage, łącznie z uwierzytelnianiem
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2149bfb68697129680c45f15c6cce359863fbc59
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989938"
---
# <a name="using-the-azure-storage-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Storage

W tym artykule pokazano, jak używać interfejsów API REST usługi Blob Storage i jak uwierzytelniać wywołanie usługi. Jest ona zapisywana z punktu widzenia dewelopera, który wie, że niczego nie dotyczy, i nie ma pomysłu, jak wykonać wywołanie REST. Zapoznaj się z dokumentacją referencyjną dla wywołania REST i zobacz, jak przetłumaczyć ją na rzeczywiste wywołanie REST — które pola są gotowe? Po zapoznaniu się ze sposobem konfigurowania wywołania REST możesz skorzystać z tej wiedzy, aby użyć dowolnego interfejsu API REST usługi Storage.

## <a name="prerequisites"></a>Wymagania wstępne 

Aplikacja zawiera listę kontenerów w usłudze BLOB Storage dla konta magazynu. Aby wypróbować kod w tym artykule, potrzebne są następujące elementy: 

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) z następującym obciążeniem:
    - Programowanie na platformie Azure

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto magazynu ogólnego przeznaczenia. Jeśli jeszcze nie masz konta magazynu, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

* W przykładzie w tym artykule pokazano, jak wyświetlić listę kontenerów na koncie magazynu. Aby wyświetlić dane wyjściowe, przed rozpoczęciem Dodaj kontenery do magazynu obiektów BLOB na koncie magazynu.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja jest aplikacją konsolową, która C#jest zapisywana.

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć rozwiązanie Visual Studio, poszukaj folderu Storage-dotnet-REST-API-with-auth, otwórz go, a następnie kliknij dwukrotnie plik StorageRestApiAuth. sln. 

## <a name="what-is-rest"></a>Co to jest REST?

REST oznacza *przeniesienie stanu reprezentacji*. Aby zapoznać się z określoną definicją, zapoznaj się z tematem [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

W istocie, REST to architektura, której można użyć podczas wywoływania interfejsów API lub udostępniania interfejsów API do wywoływania. Jest on niezależny od tego, co się dzieje po obu stronach i jakie inne oprogramowanie jest używane podczas wysyłania lub otrzymywania wywołań REST. Można napisać aplikację działającą na komputerach Mac, Windows, Linux, telefonie z systemem Android lub tablecie, telefonie iPhone, iPod lub witrynie sieci Web i korzystać z tego samego interfejsu API REST dla wszystkich tych platform. Dane można przekazywać i/lub wywoływać, gdy wywoływany jest interfejs API REST. Interfejs API REST nie ma wpływu na to, jaką platformę nazywamy — ważne są informacje przekazywane w żądaniu oraz dane podane w odpowiedzi.

Znajomość korzystania z REST jest przydatną umiejętnością. Zespół produktu Azure często zwalnia nowe funkcje. Wiele razy nowe funkcje są dostępne za pomocą interfejsu REST. Czasami jednak funkcje te nie zostały nawiązane przez **wszystkie** biblioteki klienta magazynu ani interfejs użytkownika (na przykład Azure Portal). Jeśli zawsze chcesz używać najnowszych i największych, musisz postanowić się, że jest to wymagane. Ponadto, jeśli chcesz napisać własną bibliotekę do współpracy z usługą Azure Storage lub chcesz uzyskać dostęp do usługi Azure Storage przy użyciu języka programowania bez zestawu SDK lub biblioteki klienta magazynu, możesz użyć interfejsu API REST.

## <a name="about-the-sample-application"></a>Informacje o aplikacji przykładowej

Przykładowa aplikacja zawiera listę kontenerów na koncie magazynu. Po zrozumieniu sposobu, w jaki informacje w dokumentacji interfejsu API REST są skorelowane z rzeczywistym kodem, inne wywołania REST są łatwiejsze do ustalenia. 

Jeśli szukasz [interfejsu API REST usługi BLOB Service](/rest/api/storageservices/Blob-Service-REST-API), zobaczysz wszystkie operacje, które można wykonać w usłudze BLOB Storage. Biblioteki klienta usługi Storage są otokami otaczającymi interfejsy API REST — ułatwiają dostęp do magazynu bez bezpośredniego używania interfejsów API REST. Jednak jak wspomniano powyżej, Czasami chcesz użyć interfejsu API REST zamiast biblioteki klienta usługi Storage.

## <a name="rest-api-reference-list-containers-api"></a>Dokumentacja interfejsu API REST: Utwórz listę interfejsów API kontenerów

Przyjrzyjmy się stronie w dokumentacji interfejsu API REST dla operacji [ListContainers](/rest/api/storageservices/List-Containers2) . Te informacje ułatwią zrozumienie, w jaki sposób niektóre pola pochodzą z żądania i odpowiedzi.

**Metoda żądania**: POBIERZ. To zlecenie jest metodą HTTP określoną jako właściwość obiektu żądania. Inne wartości tego zlecenia obejmują nagłówek, PUT i DELETE, w zależności od wywoływanego interfejsu API.

**Identyfikator URI żądania**: https://myaccount.blob.core.windows.net/?comp=list Jest to tworzone na podstawie punktu końcowego `http://myaccount.blob.core.windows.net` konta usługi BLOB Storage i ciągu `/?comp=list` zasobu.

[Parametry identyfikatora URI](/rest/api/storageservices/List-Containers2#uri-parameters): Istnieją dodatkowe parametry zapytania, których można użyć podczas wywoływania ListContainers. Kilka z tych parametrów jest *limitem czasu* dla wywołania (w sekundach) i *prefiksu*, który jest używany do filtrowania.

Innym przydatnym parametrem jest *MaxResults:* Jeśli więcej kontenerów jest dostępnych niż ta wartość, treść odpowiedzi będzie zawierać element *NextMarker* , który wskazuje następny kontener do zwrócenia przy następnym żądaniu. Aby użyć tej funkcji, należy podać wartość *NextMarker* jako parametr *znacznika* w identyfikatorze URI podczas następnego żądania. Korzystając z tej funkcji, jest on analogiczny do stronicowania za pośrednictwem wyników. 

Aby użyć dodatkowych parametrów, Dołącz je do ciągu zasobu z wartością, jak w poniższym przykładzie:

```
/?comp=list&timeout=60&maxresults=100
```

[Nagłówki żądań](/rest/api/storageservices/List-Containers2#request-headers) **:** Ta sekcja zawiera listę wymaganych i opcjonalnych nagłówków żądań. Wymagane są trzy z nagłówków: nagłówek *autoryzacji* , *x-MS-Date* (zawiera czas UTC dla żądania) i *x-MS-Version* (określa wersję interfejsu API REST do użycia). Włączenie opcji *x-MS-Client-Request-ID* w nagłówkach jest opcjonalne — można ustawić wartość dla tego pola na dowolne. jest on zapisywana w dziennikach analizy magazynu, gdy rejestrowanie jest włączone.

[Treść żądania](/rest/api/storageservices/List-Containers2#request-body) **:** Brak treści żądania dla ListContainers. Treść żądania jest używana dla wszystkich operacji PUT podczas przekazywania obiektów blob, a także SetContainerAccessPolicy, co umożliwia wysyłanie danych z listy XML przechowywanych zasad dostępu do zastosowania. Przechowywane zasady dostępu zostały omówione w artykule [przy użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

[Kod stanu odpowiedzi](/rest/api/storageservices/List-Containers2#status-code) **:** Zawiera informacje o kodach stanu, które należy znać. W tym przykładzie kod stanu HTTP 200 jest prawidłowy. Aby uzyskać pełną listę kodów stanu HTTP, sprawdź [definicje kodu stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Aby wyświetlić kody błędów charakterystyczne dla interfejsów API REST magazynu, zobacz [Common Error API Storage Codes](/rest/api/storageservices/common-rest-api-error-codes)

[Nagłówki odpowiedzi](/rest/api/storageservices/List-Containers2#response-headers) **:** Obejmują one *Typ zawartości*; *x-MS-Request-ID*, który jest identyfikatorem żądania, który został przesłany; *x-MS-Version*, która wskazuje wersję używanej BLOB Service; i *Data*, która jest w formacie UTC i informuje o czasie realizacji żądania.

[Treść odpowiedzi](/rest/api/storageservices/List-Containers2#response-body): To pole jest strukturą XML dostarczającą żądane dane. W tym przykładzie odpowiedzią jest lista kontenerów i ich właściwości.

## <a name="creating-the-rest-request"></a>Tworzenie żądania REST

Kilka notatek przed rozpoczęciem — w celu zapewnienia bezpieczeństwa podczas działania w środowisku produkcyjnym zawsze używaj protokołu HTTPS zamiast protokołu HTTP. Na potrzeby tego ćwiczenia należy użyć protokołu HTTP, aby można było wyświetlić dane żądania i odpowiedzi. Aby wyświetlić informacje o żądaniu i odpowiedzi w rzeczywistych wywołaniach REST, można pobrać [programu Fiddler](https://www.telerik.com/fiddler) lub podobną aplikację. W rozwiązaniu programu Visual Studio nazwa i klucz konta magazynu są stałe w klasie. Metoda ListContainersAsyncREST przekazuje nazwę konta magazynu i klucz konta magazynu do metod, które są używane do tworzenia różnych składników żądania REST. W świecie rzeczywistym nazwa i klucz konta magazynu znajdują się w pliku konfiguracji, zmiennych środowiskowych lub do pobrania z Azure Key Vault.

W naszym przykładowym projekcie kod służący do tworzenia nagłówka autoryzacji znajduje się w osobnej klasie. Pomysłem jest to, że można przyjąć całą klasę i dodać ją do własnego rozwiązania i użyć jej "w takiej postaci, w jakiej jest". Kod nagłówka autoryzacji działa w przypadku większości wywołań interfejsu API REST do usługi Azure Storage.

Aby skompilować żądanie, które jest obiektem HttpRequestMessage, przejdź do ListContainersAsyncREST w Program.cs. Poniżej przedstawiono procedurę tworzenia żądania: 

* Utwórz identyfikator URI, który ma być używany do wywoływania usługi. 
* Utwórz obiekt HttpRequestMessage i ustaw ładunek. Ładunek ma wartość null dla ListContainersAsyncREST, ponieważ nie są przekazywane żadne elementy w.
* Dodaj nagłówki żądania dla x-MS-date i x-MS-Version.
* Pobierz nagłówek autoryzacji i dodaj go.

Wymagane są pewne podstawowe informacje: 

*  Dla ListContainers **Metoda** jest `GET`. Ta wartość jest ustawiana podczas tworzenia wystąpienia żądania. 
*  **Zasób** jest częścią zapytania identyfikatora URI wskazującej, który interfejs API jest wywoływany, więc wartość to `/?comp=list`. Jak wspomniano wcześniej, zasób znajduje się na stronie dokumentacji referencyjnej, która zawiera informacje o [interfejsie API ListContainers](/rest/api/storageservices/List-Containers2).
*  Identyfikator URI jest konstruowany przez utworzenie punktu końcowego Blob service dla tego konta magazynu i połączenie zasobu. Wartość **identyfikatora URI żądania** jest `http://contosorest.blob.core.windows.net/?comp=list`zakończona.
*  Dla ListContainers, **elemencie requestbody** ma wartość null i nie ma żadnych dodatkowych **nagłówków**.

Inne interfejsy API mogą mieć inne parametry do przekazania, takie jak *ifMatch*. Przykładem, gdzie można używać ifMatch, jest wywołanie PutBlob. W takim przypadku należy ustawić ifMatch na eTag i tylko wtedy, gdy obiekt eTag jest zgodny z bieżącym eTag w obiekcie blob. Jeśli ktoś inny zaktualizował obiekt BLOB od momentu pobrania elementu eTag, ich zmiana nie zostanie zastąpiona. 

Najpierw ustaw `uri` `payload`i. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Następnie Utwórz wystąpienie żądania, ustawiając metodę na `GET` i dostarczając identyfikator URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Dodaj nagłówki żądania dla x-MS-date i x-MS-Version. W tym miejscu w kodzie jest również dodawane dodatkowe nagłówki żądania wymagane dla wywołania. W tym przykładzie nie ma dodatkowych nagłówków. Przykładem interfejsu API, który przekazuje w dodatkowych nagłówkach, jest SetContainerACL. W przypadku usługi BLOB Storage dodaje nagłówek o nazwie "x-MS-BLOB-Public-Access" i wartość dla poziomu dostępu.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Wywołaj metodę, która tworzy nagłówek autoryzacji, i Dodaj ją do nagłówków żądania. Zobaczysz, jak utworzyć nagłówek autoryzacji w dalszej części artykułu. Nazwa metody to GetAuthorizationHeader, którą można zobaczyć w tym fragmencie kodu:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

W tym momencie program `httpRequestMessage` zawiera wszystkie żądania REST z nagłówkami autoryzacji. 

## <a name="call-the-rest-api-with-the-request"></a>Wywoływanie interfejsu API REST przy użyciu żądania

Teraz, gdy masz żądanie, możesz wywołać SendAsync w celu wysłania żądania REST. SendAsync wywołuje interfejs API i pobiera odpowiedź z powrotem. Zapoznaj się z odpowiedzią StatusCode (200 OK), a następnie Przeanalizuj odpowiedź. W tym przypadku otrzymujesz listę XML kontenerów. Przyjrzyjmy się kodowi do wywoływania metody GetRESTRequest w celu utworzenia żądania, wykonania żądania, a następnie sprawdzenia odpowiedzi na listę kontenerów.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Jeśli uruchamiasz szperacz sieciowy, taki jak [programu Fiddler](https://www.telerik.com/fiddler) podczas wywołania SendAsync, zobaczysz informacje dotyczące żądania i odpowiedzi. Przyjrzyjmy się. Nazwa konta magazynu to *contosorest*.

**Żądając**

```
GET /?comp=list HTTP/1.1
```

**Nagłówki żądania:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Kod stanu i nagłówki odpowiedzi zwrócone po wykonaniu:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Treść odpowiedzi (XML):** W przypadku ListContainers zostanie wyświetlona lista kontenerów i ich właściwości.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Teraz, gdy zrozumiesz, jak utworzyć żądanie, wywołać usługę i przeanalizować wyniki, zobaczmy, jak utworzyć nagłówek autoryzacji. Tworzenie tego nagłówka jest skomplikowane, ale dobrą nowością jest to, że gdy kod działa prawidłowo, działa on w przypadku wszystkich interfejsów API REST usługi Storage.

## <a name="creating-the-authorization-header"></a>Tworzenie nagłówka autoryzacji

> [!TIP]
> Usługa Azure Storage obsługuje teraz integrację Azure Active Directory (Azure AD) dla obiektów blob i kolejek. Usługa Azure AD oferuje znacznie prostsze środowisko do autoryzowania żądania do usługi Azure Storage. Aby uzyskać więcej informacji o korzystaniu z usługi Azure AD do autoryzacji operacji REST, zobacz [uwierzytelnianie za pomocą Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Aby zapoznać się z omówieniem integracji usługi Azure AD z usługą Azure Storage, zobacz temat [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](storage-auth-aad.md).

Istnieje artykuł objaśniający koncepcję koncepcyjnie (bez kodu), w jaki sposób przeprowadzać [uwierzytelnianie dla usług Azure Storage](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Poinformujmy o tym, że artykuł jest odpowiednio widoczny i Pokaż kod.

Najpierw Użyj uwierzytelniania klucza współużytkowanego. Format nagłówka autoryzacji wygląda następująco:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole podpisu jest kod uwierzytelniania wiadomości oparte na skrótach (HMAC) utworzonym na podstawie żądania i obliczane przy użyciu algorytmu SHA256, a następnie kodowane przy użyciu kodowania base64. Czy masz? (W tym miejscu nie zostało jeszcze rozwiązane słowo *kanoniczne* ).

Ten fragment kodu przedstawia format ciągu sygnatury klucza współdzielonego:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

Większość z tych pól jest rzadko używana. W przypadku usługi BLOB Storage należy określić CZASOWNIK, MD5, długość zawartości, nagłówki kanoniczne i zasób kanoniczny. Pozostałe wartości można pozostawić puste (ale umieścić je w tym `\n` obszarze, tak że są puste).

Co to są CanonicalizedHeaders i CanonicalizedResource? Dobre pytanie. Co oznacza znaczenie w postaci kanonicznej? Program Microsoft Word nie rozpoznaje go nawet jako wyrazu. Oto [Informacje o kanonizacji](https://en.wikipedia.org/wiki/Canonicalization)w witrynie Wikipedia: *W nauce komputerowej, kanonizacja (czasami normalizacja lub normalizacja) to proces konwersji danych, które mają więcej niż jedną możliwą reprezentację w postaci "standardowy", "normalny" lub "kanoniczny".* W normalnych przypadkach oznacza to, że należy zastosować listę elementów (takich jak nagłówki w przypadku nagłówków kanonicznych) i przeprowadzić ich standaryzację w wymaganym formacie. Zasadniczo firma Microsoft zdecydowała się na format i należy je dopasować.

Zacznijmy od tych dwóch pól kanonicznych, ponieważ są one wymagane do utworzenia nagłówka autoryzacji.

**Nagłówki kanoniczne**

Aby utworzyć tę wartość, Pobierz nagłówki, które zaczynają się od "x-ms-" i posortuj je, a następnie sformatuj je `[key:value\n]` do ciągu wystąpień, połączone w jeden ciąg. Na potrzeby tego przykładu kanoniczne nagłówki wyglądają następująco: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Oto kod używany do tworzenia tych danych wyjściowych:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

**Zasób kanoniczny**

Ta część ciągu podpisu reprezentuje konto magazynu wskazywane przez żądanie. Należy pamiętać, że identyfikator URI `<http://contosorest.blob.core.windows.net/?comp=list>`żądania ma rzeczywistą nazwę konta (`contosorest` w tym przypadku). W tym przykładzie jest zwracany:

```
/contosorest/\ncomp:list
```

Jeśli masz parametry zapytania, ten przykład obejmuje również te parametry. Oto kod, który obsługuje także dodatkowe parametry zapytania i parametry zapytania z wieloma wartościami. Pamiętaj, że tworzysz ten kod, aby działał dla wszystkich interfejsów API REST. Chcesz uwzględnić wszystkie możliwości, nawet jeśli metoda ListContainers nie potrzebuje wszystkich z nich.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Teraz, gdy są ustawiane ciągi kanoniczne, przyjrzyjmy się sposobom tworzenia nagłówka autoryzacji. Zacznij od utworzenia ciągu komunikatu w formacie StringToSign, który został wcześniej wyświetlony w tym artykule. Pojęcie to jest łatwiejsze do wyjaśnienia przy użyciu komentarzy w kodzie, więc jest to Ostatnia metoda zwracająca nagłówek autoryzacji:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Po uruchomieniu tego kodu wynikowy MessageSignature wygląda następująco:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Oto końcowa wartość dla AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader jest ostatnim nagłówkiem umieszczonym w nagłówkach żądania przed opublikowaniem odpowiedzi.

Zawiera wszystkie informacje potrzebne do utworzenia klasy, za pomocą której można utworzyć żądanie wywołania interfejsów API REST usług Storage.

## <a name="how-about-another-example"></a>Jak działa inny przykład? 

Przyjrzyjmy się sposobom zmiany kodu w celu wywołania ListBlobs *kontenera-1*. Ten kod jest niemal identyczny z kodem dla kontenerów list, jedyne różnice w identyfikatorze URI i sposób analizowania odpowiedzi. 

Jeśli przeszukasz dokumentację referencyjną [ListBlobs](/rest/api/storageservices/List-Blobs), znajdziesz metodę *Get* i RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

W ListContainersAsyncREST Zmień kod, który ustawia identyfikator URI interfejsu API dla ListBlobs. Nazwa kontenera to **Container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Następnie w miejscu obsługi odpowiedzi Zmień kod, aby wyszukać obiekty blob zamiast kontenerów.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Po uruchomieniu tego przykładu uzyskasz wyniki podobne do następujących:

**Nagłówki kanoniczne:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Zasób kanoniczny:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Następujące wartości pochodzą z [programu Fiddler](https://www.telerik.com/fiddler):

**Żądając**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Nagłówki żądania:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Kod stanu i nagłówki odpowiedzi zwrócone po wykonaniu:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Treść odpowiedzi (XML):** Ta odpowiedź XML przedstawia listę obiektów blob i ich właściwości. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono sposób tworzenia żądania do interfejsu API REST usługi BLOB Storage. Za pomocą żądania można pobrać listę kontenerów lub listę obiektów BLOB w kontenerze. Wiesz już, jak utworzyć podpis autoryzacji dla wywołania interfejsu API REST i jak używać go w żądaniu REST. Na koniec zapoznaj się z badaniem odpowiedzi.

## <a name="next-steps"></a>Następne kroki

* [Interfejs API REST usługi BLOB Service](/rest/api/storageservices/blob-service-rest-api)
* [Interfejs API REST usługi plików](/rest/api/storageservices/file-service-rest-api)
* [Interfejs API REST usługi kolejkowania](/rest/api/storageservices/queue-service-rest-api)
