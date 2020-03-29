---
title: Wywoływanie operacji interfejsu API REST z autoryzacją klucza udostępnionego
titleSuffix: Azure Storage
description: Użyj interfejsu API REST usługi Azure Storage, aby złożyć żądanie magazynu obiektów Blob przy użyciu autoryzacji klucza udostępnionego.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5c6125b850062450516e7fc0b19c2e0d5d6f577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916068"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Wywoływanie operacji interfejsu API REST z autoryzacją klucza udostępnionego

W tym artykule pokazano, jak wywołać interfejsy API REST usługi Azure Storage, w tym sposób tworzenia nagłówka autoryzacji. Jest napisany z punktu widzenia dewelopera, który nic nie wie o REST i nie ma pojęcia, jak wykonać połączenie REST. Po zapoznaniu się z instrukcjami wywoływania operacji REST można wykorzystać tę wiedzę do użycia innych operacji REST usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Przykładowa aplikacja wyświetla listę kontenerów obiektów blob dla konta magazynu. Aby wypróbować kod w tym artykule, potrzebujesz następujących elementów:

- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) z obciążeniem **dewelopera platformy Azure.**

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- Konto magazynu ogólnego przeznaczenia. Jeśli nie masz jeszcze konta magazynu, zobacz [Tworzenie konta magazynu](storage-account-create.md).

- W przykładzie w tym artykule pokazano, jak wyświetlić listę kontenerów na koncie magazynu. Aby wyświetlić dane wyjściowe, dodaj niektóre kontenery do magazynu obiektów blob na koncie magazynu przed rozpoczęciem.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja jest aplikacją konsoli napisaną w języku C#.

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć rozwiązanie programu Visual Studio, poszukaj folderu storage-dotnet-rest-api-with-auth, otwórz go i kliknij dwukrotnie na StorageRestApiAuth.sln.

## <a name="about-rest"></a>Informacje o REST

REST oznacza *reprezentacyjny transfer państwa*. Aby uzyskać konkretną definicję, zapoznaj się z [Wikipedią](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST to architektura umożliwiająca interakcję z usługą za pośrednictwem protokołu internetowego, takiego jak HTTP/HTTPS. REST jest niezależny od oprogramowania uruchomionego na serwerze lub kliencie. Interfejs API REST można wywołać z dowolnej platformy obsługującej protokół HTTP/HTTPS. Możesz napisać aplikację, która działa na komputerze Mac, Windows, Linux, telefonie lub tablecie z systemem Android, telefonie iPhone, iPodzie lub witrynie sieci Web i używać tego samego interfejsu API REST dla wszystkich tych platform.

Wywołanie interfejsu API REST składa się z żądania, które jest dokonywane przez klienta i odpowiedzi, która jest zwracana przez usługę. W żądaniu należy wysłać adres URL z informacjami o operacji, którą chcesz wywołać, zasobu do działania, wszelkich parametrów kwerendy i nagłówków oraz w zależności od operacji, która została wywołana, ładunku danych. Odpowiedź z usługi zawiera kod stanu, zestaw nagłówków odpowiedzi i w zależności od operacji, która została wywołana, ładunek danych.

## <a name="about-the-sample-application"></a>Informacje o przykładowej aplikacji

Przykładowa aplikacja wyświetla listę kontenerów na koncie magazynu. Po zrozumieniu, jak informacje w dokumentacji interfejsu API REST koreluje z rzeczywistym kodem, inne wywołania REST są łatwiejsze do ustalenia.

Jeśli spojrzeć na [interfejs API REST usługi obiektów blob](/rest/api/storageservices/Blob-Service-REST-API), zobaczysz wszystkie operacje, które można wykonać na magazynie obiektów blob. Biblioteki klienta magazynu są otoki wokół interfejsów API REST — ułatwiają one dostęp do magazynu bez konieczności bezpośredniego korzystania z interfejsów API REST. Ale jak wspomniano powyżej, czasami chcesz użyć interfejsu API REST zamiast biblioteki klienta magazynu.

## <a name="list-containers-operation"></a>Operacja Kontenery listy

Przejrzyj odwołanie do [operacji ListContainers.](/rest/api/storageservices/List-Containers2) Te informacje pomogą Ci zrozumieć, skąd pochodzą niektóre pola w żądaniu i odpowiedzi.

**Metoda żądania:** GET. Ten zlecenie jest metodą HTTP określoną jako właściwość obiektu żądania. Inne wartości dla tego zlecenia to HEAD, PUT i DELETE, w zależności od wywoływania interfejsu API.

**Żądanie URI**: `https://myaccount.blob.core.windows.net/?comp=list`.Identyfikator URI żądania jest tworzony z punktu `http://myaccount.blob.core.windows.net` końcowego `/?comp=list`konta magazynu obiektów blob i ciągu zasobu .

[Parametry URI:](/rest/api/storageservices/List-Containers2#uri-parameters)Istnieją dodatkowe parametry zapytania, których można użyć podczas wywoływania listcontainers. Kilka z tych parametrów *to limit czasu* dla wywołania (w sekundach) i *prefiks*, który jest używany do filtrowania.

Innym przydatnym parametrem jest *maxresults:* jeśli więcej kontenerów są dostępne niż ta wartość, treść odpowiedzi będzie zawierać *NextMarker* element, który wskazuje następny kontener do zwrócenia w następnym żądaniu. Aby użyć tej funkcji, należy podać wartość *NextMarker* jako parametr *znacznika* w identyfikatorze URI podczas składania następnego żądania. Podczas korzystania z tej funkcji, jest analogiczne do stronicowania za pośrednictwem wyników.

Aby użyć dodatkowych parametrów, dołącz je do ciągu zasobu o wartości, na przykład:

```
/?comp=list&timeout=60&maxresults=100
```

[Nagłówki żądań:](/rest/api/storageservices/List-Containers2#request-headers)**:** Ta sekcja zawiera listę wymaganych i opcjonalnych nagłówków żądań. Wymagane są trzy nagłówki: nagłówek *autoryzacji,* *x-ms-date* (zawiera czas UTC dla żądania) i *x-ms-version* (określa wersję interfejsu API REST do użycia). Uwzględnienie *x-ms-client-request-id* w nagłówkach jest opcjonalne — wartość tego pola można ustawić na cokolwiek; jest zapisywany w dziennikach analizy magazynu, gdy rejestrowanie jest włączone.

[Treść żądania:](/rest/api/storageservices/List-Containers2#request-body)**:** Nie ma treści żądania dla ListContainers. Treść żądania jest używana we wszystkich operacjach PUT podczas przekazywania obiektów blob, a także SetContainerAccessPolicy, która umożliwia wysyłanie listy XML przechowywanych zasad dostępu do zastosowania. Zasady dostępu przechowywane zostały omówione w artykule [Korzystanie z sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

[Kod stanu odpowiedzi:](/rest/api/storageservices/List-Containers2#status-code)**:** Informuje o wszelkich kodach stanu, które musisz znać. W tym przykładzie kod stanu HTTP 200 jest ok. Aby uzyskać pełną listę kodów stanu HTTP, zapoznaj się z [definicjami kodu stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Aby wyświetlić kody błędów specyficzne dla interfejsów API REST magazynu, zobacz [Typowe kody błędów interfejsu API REST](/rest/api/storageservices/common-rest-api-error-codes)

[Nagłówki odpowiedzi:](/rest/api/storageservices/List-Containers2#response-headers)**:** Obejmują one *typ zawartości;* *x-ms-request-id*, czyli identyfikator żądania, który został przekazany; *x-ms-version*, który wskazuje wersję używanej usługi blob; oraz *Datę*, która jest w UTC i informuje, o której godzinie wniosek został złożony.

[Treść odpowiedzi:](/rest/api/storageservices/List-Containers2#response-body)To pole jest strukturą XML zawierającą żądane dane. W tym przykładzie odpowiedź jest lista kontenerów i ich właściwości.

## <a name="creating-the-rest-request"></a>Tworzenie żądania REST

Aby uzyskać bezpieczeństwo podczas pracy w produkcji, należy zawsze używać protokołu HTTPS, a nie http. Na potrzeby tego ćwiczenia należy użyć protokołu HTTP, aby wyświetlić dane żądania i odpowiedzi. Aby wyświetlić informacje o żądaniu i odpowiedzi w rzeczywistych wywołań REST, można pobrać [Fiddler](https://www.telerik.com/fiddler) lub podobnej aplikacji. W rozwiązaniu programu Visual Studio nazwa konta magazynu i klucz są zakodowane na stałe w klasie. ListContainersAsyncREST Metoda przekazuje nazwę konta magazynu i klucz konta magazynu do metod, które są używane do tworzenia różnych składników żądania REST. W aplikacji w świecie rzeczywistym nazwa konta magazynu i klucz będą znajdować się w pliku konfiguracji, zmienne środowiskowe lub być pobierane z usługi Azure Key Vault.

W naszym przykładowym projekcie kod do tworzenia nagłówka autoryzacji znajduje się w osobnej klasie. Chodzi o to, że można wziąć całą klasę i dodać go do własnego rozwiązania i używać go "jak jest". Kod nagłówka autoryzacji działa dla większości wywołań interfejsu API REST do usługi Azure Storage.

Aby utworzyć żądanie, który jest Obiekt HttpRequestMessage, przejdź do ListContainersAsyncREST w Program.cs. Kroki tworzenia żądania są następujące:

- Utwórz identyfikator URI, który ma być używany do wywoływania usługi.
- Utwórz obiekt HttpRequestMessage i ustaw ładunek. Ładunek jest null dla ListContainersAsyncREST, ponieważ nie przekazujemy niczego.
- Dodaj nagłówki żądań dla x-ms-date i x-ms-version.
- Pobierz nagłówek autoryzacji i dodaj go.

Kilka podstawowych informacji, których potrzebujesz:

- W przypadku listcontainers `GET` **metoda** jest . Ta wartość jest ustawiana podczas tworzenia wystąpienia żądania.
- **Zasób** jest częścią kwerendy identyfikatora URI, która wskazuje, `/?comp=list`który interfejs API jest wywoływany, więc wartość jest . Jak wspomniano wcześniej, zasób znajduje się na stronie dokumentacji odwołania, która pokazuje informacje o [Interfejsie API ListContainers](/rest/api/storageservices/List-Containers2).
- Identyfikator URI jest konstruowany przez utworzenie punktu końcowego usługi obiektów Blob dla tego konta magazynu i łączenie zasobu. Wartość **identyfikatora URI** żądania `http://contosorest.blob.core.windows.net/?comp=list`kończy się .
- W przypadku listcontainers **requestBody** ma wartość null i nie ma żadnych dodatkowych **nagłówków**.

Różne interfejsy API mogą mieć inne parametry do przekazania, takie jak *ifMatch*. Przykład, gdzie można użyć ifMatch jest podczas wywoływania PutBlob. W takim przypadku można ustawić ifMatch do eTag i aktualizuje tylko obiekt blob, jeśli eTag podać pasuje do bieżącego eTag na obiekt blob. Jeśli ktoś inny zaktualizował obiekt blob od czasu pobrania eTag, ich zmiana nie zostanie zastąpiona.

Najpierw ustaw `uri` i `payload`.

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Następnie wystąpienia żądania, ustawienie metody `GET` i dostarczanie identyfikatora URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Dodaj nagłówki żądań `x-ms-date` `x-ms-version`dla i . To miejsce w kodzie jest również, gdzie można dodać wszelkie dodatkowe nagłówki żądania wymagane dla wywołania. W tym przykładzie nie ma żadnych dodatkowych nagłówków. Przykładem interfejsu API, który przekazuje w dodatkowych nagłówków jest Set Container ACL operacji. To wywołanie interfejsu API dodaje nagłówek o nazwie "x-ms-blob-public-access" i wartość dla poziomu dostępu.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Wywołanie metody, która tworzy nagłówek autoryzacji i dodać go do nagłówków żądań. Zobaczysz, jak utworzyć nagłówek autoryzacji w dalszej części artykułu. Nazwa metody jest GetAuthorizationHeader, który można zobaczyć w tym fragment kodu:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

W tym `httpRequestMessage` momencie zawiera żądanie REST wraz z nagłówkami autoryzacji.

## <a name="send-the-request"></a>Wysyłanie żądania

Teraz, gdy zostały skonstruowane żądanie, można wywołać SendAsync metody, aby wysłać go do usługi Azure Storage. Sprawdź, czy wartość kodu stanu odpowiedzi wynosi 200, co oznacza, że operacja powiodła się. Następnie przesiemać odpowiedzi. W takim przypadku otrzymasz listę kontenerów XML. Przyjrzyjmy się kod do wywoływania GetRESTRequest metody, aby utworzyć żądanie, wykonać żądanie, a następnie zbadać odpowiedź dla listy kontenerów.

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

Jeśli uruchomisz sniffer sieci, takich jak [Fiddler](https://www.telerik.com/fiddler) podczas wykonywania połączenia z SendAsync, można zobaczyć informacje o żądaniu i odpowiedzi. Zobaczmy. Nazwa konta magazynu to *contosorest*.

**Żądanie:**

```
GET /?comp=list HTTP/1.1
```

**Nagłówki żądań:**

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

**Treść odpowiedzi (XML):** Dla listy kontenerów operacji, pokazuje listę kontenerów i ich właściwości.

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

Teraz, gdy zrozumiesz, jak utworzyć żądanie, wywołać usługę i przeanalizować wyniki, zobaczmy, jak utworzyć nagłówek autoryzacji. Tworzenie tego nagłówka jest skomplikowane, ale dobrą wiadomością jest to, że gdy masz kod działa, działa dla wszystkich interfejsów API REST usługi magazynu.

## <a name="creating-the-authorization-header"></a>Tworzenie nagłówka autoryzacji

> [!TIP]
> Usługa Azure Storage obsługuje teraz integrację usługi Azure Active Directory (Azure AD) dla obiektów blob i kolejek. Usługa Azure AD oferuje znacznie prostsze środowisko autoryzowania żądania do usługi Azure Storage. Aby uzyskać więcej informacji na temat używania usługi Azure AD do autoryzowania operacji REST, zobacz [Autoryzuj z usługą Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory). Aby zapoznać się z omówieniem integracji usługi Azure AD z usługą Azure Storage, zobacz [Uwierzytelnij dostęp do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md).

Istnieje artykuł, który wyjaśnia koncepcyjnie (bez kodu) jak [autoryzować żądania do usługi Azure Storage.](/rest/api/storageservices/authorize-requests-to-azure-storage)

Let's destylacji tego artykułu do dokładnie jest potrzebne i pokazać kod.

Najpierw użyj autoryzacji klucza udostępnionego. Format nagłówka autoryzacji wygląda następująco:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole podpisu jest kodem uwierzytelniania wiadomości opartym na skrótach (HMAC) utworzonym na podstawie żądania i obliczonym przy użyciu algorytmu SHA256, a następnie zakodowanym przy użyciu kodowania Base64. Masz to? (Hang tam, nawet nie słyszałem słowo *kanoniczne* jeszcze.)

Ten fragment kodu pokazuje format ciągu podpisu klucza udostępnionego:

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

Większość z tych pól jest rzadko używana. W przypadku magazynu obiektów Blob należy określić czasownik, md5, długość zawartości, kanonicznie nagłówki i zasób kanonizowany. Możesz pozostawić pozostałe puste (ale `\n` umieścić w tak, że wie, że są puste).

Co to są CanonicalizedHeaders i CanonicalizedResource? Dobre pytanie. W rzeczywistości, co oznacza kanoniczne? Program Microsoft Word nawet nie rozpoznaje go jako słowa. Oto, co [Wikipedia mówi o kanonizacji:](https://en.wikipedia.org/wiki/Canonicalization)W *informatyce kanonizacja (czasami standaryzacja lub normalizacja) jest procesem konwersji danych, który ma więcej niż jedną możliwą reprezentację w "standardową", "normalną" lub kanoniczną formę.* W normalnej mowie oznacza to, że należy wziąć listę elementów (takich jak nagłówki w przypadku kanonicznie nagłówków) i ustandaryzować je do wymaganego formatu. Zasadniczo Microsoft zdecydował się na format i musisz go dopasować.

Zacznijmy od tych dwóch kanonicznych pól, ponieważ są one wymagane do utworzenia nagłówka Autoryzacja.

### <a name="canonicalized-headers"></a>Kanonicznie zakaulizowane nagłówki

Aby utworzyć tę wartość, pobierz nagłówki, które zaczynają się od "x-ms-" i `[key:value\n]` posortuj je, a następnie sformatować je w ciąg wystąpień, połączony w jeden ciąg. W tym przykładzie kanoniczne nagłówki wyglądają następująco:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Oto kod używany do tworzenia tego wyjścia:

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

### <a name="canonicalized-resource"></a>Zasób kanonizowany

Ta część ciągu podpisu reprezentuje konto magazynu, na które ma być skierowane żądanie. Należy pamiętać, że `<http://contosorest.blob.core.windows.net/?comp=list>`identyfikator URI żądania`contosorest` jest , z rzeczywistą nazwą konta (w tym przypadku). W tym przykładzie jest to zwracane:

```
/contosorest/\ncomp:list
```

Jeśli masz parametry kwerendy, w tym przykładzie zawiera te parametry, jak również. Oto kod, który obsługuje również dodatkowe parametry kwerendy i parametry kwerendy z wieloma wartościami. Pamiętaj, że tworzenie tego kodu do pracy dla wszystkich interfejsów API REST. Chcesz uwzględnić wszystkie możliwości, nawet jeśli ListContainers metoda nie potrzebuje wszystkich z nich.

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
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Teraz, gdy kanoniczne ciągi są ustawione, przyjrzyjmy się, jak utworzyć sam nagłówek autoryzacji. Możesz rozpocząć od utworzenia ciągu podpisu wiadomości w formacie StringToSign wcześniej wyświetlane w tym artykule. Ta koncepcja jest łatwiejsza do wyjaśnienia przy użyciu komentarzy w kodzie, więc tutaj jest, ostateczna metoda, która zwraca nagłówek autoryzacji:

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
        storageAccountName + ":" + signature);
    return authHV;
}
```

Po uruchomieniu tego kodu wynikowy MessageSignature wygląda następująco w tym przykładzie:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Oto ostateczna wartość AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader jest ostatnim nagłówkiem umieszczonym w nagłówkach żądań przed zaksięgowaniem odpowiedzi.

Obejmuje wszystko, co musisz wiedzieć, aby połączyć klasę, z którą można utworzyć żądanie wywołania interfejsów API REST usług magazynu.

## <a name="example-list-blobs"></a>Przykład: Lista obiektów blob

Przyjrzyjmy się, jak zmienić kod, aby wywołać operację Lista obiektów blob dla *kontenera kontenera-1*. Ten kod jest prawie identyczny z kodem dla kontenerów aukcji, tylko różnice są identyfikator URI i jak przeanalizować odpowiedź.

Jeśli spojrzeć na dokumentację referencyjną dla [ListBlobs](/rest/api/storageservices/List-Blobs), okaże się, że metoda jest *GET* i RequestURI jest:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

W ListContainersAsyncREST zmień kod, który ustawia identyfikator URI do interfejsu API dla ListBlobs. Nazwa kontenera to **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Następnie, gdzie obsługiwać odpowiedzi, należy zmienić kod, aby wyszukać obiekty BLOB zamiast kontenerów.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Po uruchomieniu tego przykładu, otrzymasz wyniki, takie jak następujące:

**Kanonicznie nagłówki:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Zasób kanonizowany:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Podpis wiadomości:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Nagłówek autoryzacji:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Następujące wartości pochodzą z [Fiddler:](https://www.telerik.com/fiddler)

**Żądanie:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Nagłówki żądań:**

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

**Treść odpowiedzi (XML):** Ta odpowiedź XML pokazuje listę obiektów blob i ich właściwości.

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

W tym artykule dowiesz się, jak złożyć żądanie do interfejsu API REST magazynu obiektów blob. Za pomocą żądania można pobrać listę kontenerów lub listę obiektów blob w kontenerze. Dowiedzialiśmy się, jak utworzyć podpis autoryzacji dla wywołania interfejsu API REST i jak go używać w żądaniu REST. Na koniec nauczyłeś się, jak zbadać odpowiedź.

## <a name="next-steps"></a>Następne kroki

- [Interfejs API REST usługi Blob Service](/rest/api/storageservices/blob-service-rest-api)
- [Interfejs API REST usługi File (Plik)](/rest/api/storageservices/file-service-rest-api)
- [Interfejs API REST usługi Queue (Kolejka)](/rest/api/storageservices/queue-service-rest-api)
- [Interfejs API REST usługi Table (Tabela)](/rest/api/storageservices/table-service-rest-api)
