---
title: Podczas wywoływania operacji interfejsu API REST usług magazynu Azure, włącznie z uwierzytelnianiem | Dokumentacja firmy Microsoft
description: Podczas wywoływania operacji interfejsu API REST usług magazynu Azure, włącznie z uwierzytelnianiem
services: storage
author: tamram
manager: twooley
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: tamram
ms.openlocfilehash: 6009ebd18eb089b21c98d6f7d9f49044a8d96098
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650455"
---
# <a name="using-the-azure-storage-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Storage

W tym artykule przedstawiono sposób użycia interfejsów API REST usługi Magazyn obiektów Blob oraz sposób uwierzytelniania połączenia z usługą. Jest ona zapisywana z punktu widzenia osoby, która nie zna o REST i nie wiadomo jak wykonywać wywołanie interfejsu REST, ale jest dewelopera. Możemy znaleźć w dokumentacji dla wywołania REST i zobacz, jak do rzeczywistego wywołania REST — umożliwiło pola, które go where? Po dowiedzieć, jak skonfigurować wywołanie interfejsu REST, można wykorzystać wiedzy pod kątem używania dowolnych innych magazynów usługi interfejsów API REST.

## <a name="prerequisites"></a>Wymagania wstępne 

Aplikacja Wyświetla kontenery w magazynie obiektów blob dla konta magazynu. Wypróbowanie kodu w tym artykule, potrzebne są następujące elementy: 

* Zainstaluj [programu Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) z obciążeniem, następujące:
    - Tworzenie aplikacji na platformie Azure

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto magazynu ogólnego przeznaczenia. Jeśli nie masz jeszcze konta magazynu, zobacz [Utwórz konto magazynu](storage-quickstart-create-account.md).

* W przykładzie w tym artykule przedstawiono sposób do listy kontenerów na koncie magazynu. Aby wyświetlić dane wyjściowe, należy dodać niektóre kontenerów do magazynu w ramach konta magazynu obiektów blob, przed rozpoczęciem.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja jest aplikacji konsoli napisanych w języku C#.

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć rozwiązanie programu Visual Studio, wyszukaj storage-dotnet-rest-api-with-auth folder, otwórz go, a następnie kliknij dwukrotnie na StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Co to jest REST?

Oznacza, że REST *representational transferze*. Dla określonej definicji, zapoznaj się z [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

Zasadniczo REST jest architekturę służy do wywoływania interfejsów API lub wprowadzeniem do wywołania interfejsów API. Nie zależy od działania wykonywane na każdej stronie, a tym, jakie inne oprogramowanie jest używany przy wysyłaniu lub odbieraniu pozostałe wywołania. Można napisać aplikację, która działa na komputerze Mac, Windows, Linux, telefonów z systemem Android lub tabletu, iPhone, iPod lub witryny sieci web i używać tego samego interfejsu API REST dla wszystkich tych platform. Dane mogą być przekazywane w i/lub wychodzących po wywołaniu interfejsu API REST. Interfejs API REST nie zależy od platformy jest nazywana — ważne jest przekazane w żądaniu informacje i dane zawarte w odpowiedzi.

Za pomocą REST jest przydatne umiejętności. Zespół produktu Azure często udostępnia nowe funkcje. Wiele razy, nowe funkcje są dostępne za pośrednictwem interfejsu REST, ale nie ma jeszcze udostępniane za pośrednictwem **wszystkie** biblioteki klienta magazynu lub interfejsu użytkownika (na przykład Azure portal). Zawsze należy używać najnowszej i najlepszej uczenia REST jest wymagane. Ponadto jeśli chcesz zapisać biblioteki do interakcji z usługą Azure Storage lub chcesz uzyskać dostępu do usługi Azure Storage z języka programowania, który nie ma zestawu SDK lub magazynu biblioteki klienta, można użyć interfejsu API REST.

## <a name="about-the-sample-application"></a>O przykładowej aplikacji

Przykładowa aplikacja Wyświetla kontenery na koncie magazynu. Po zrozumieniu sposobu informacji w dokumentacji interfejsu API REST są powiązane z rzeczywistego kodu, inne wywołania REST są łatwiejsze do ustalenia. 

Jeśli przyjrzymy się [interfejsu API REST usługi Blob](/rest/api/storageservices/fileservices/Blob-Service-REST-API), zobacz wszystkie operacje można wykonywać na magazynu obiektów blob. Biblioteki klienta magazynu są otoki wokół interfejsów API REST-one ułatwiają możesz uzyskać dostęp do magazynu bez bezpośrednio za pomocą interfejsów API REST. Ale jak wspomniano powyżej, czasami chce zamiast biblioteki klienta magazynu za pomocą interfejsu API REST.

## <a name="rest-api-reference-list-containers-api"></a>Dokumentacja interfejsu API REST: Lista kontenery API

Przyjrzyjmy się w dokumentacji interfejsu API REST dla strony [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) operację, aby zrozumieć, niektóre pola skąd żądania i odpowiedzi w następnej sekcji kod.

**Metoda żądania**: GET. To polecenie jest metodę HTTP, który został określony jako właściwość obiektu żądania. Inne wartości dla tego zlecenia to HEAD, PUT i DELETE, w zależności od wywołujesz interfejs API.

**Identyfikator URI żądania**: https://myaccount.blob.core.windows.net/?comp=list to jest tworzona na podstawie końcowego konta magazynu obiektów blob `http://myaccount.blob.core.windows.net` i ciągu zasobu `/?comp=list`.

[Parametry identyfikatora URI](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): istnieją dodatkowe kryteria wyszukiwania można używać podczas wywoływania ListContainers. Kilka te parametry są *limitu czasu* dla wywołania (w sekundach) i *prefiks*, które jest używane do filtrowania.

Inny parametr pomocne jest *maxresults:* Jeśli dostępnych kontenery więcej niż ta wartość będzie zawierać treść odpowiedzi *NextMarker* element, który wskazuje następnego kontenera, aby powrócić na następnej żądanie. Aby użyć tej funkcji, należy podać *NextMarker* wartość jako *znacznika* parametru w identyfikatorze URI po wprowadzeniu następnego żądania. Ta funkcja jest odpowiednikiem stronicowania wyników. 

Aby korzystać z dodatkowych parametrów, dołącz je do ciągu zasobu o wartości, tak jak ten przykład:

```
/?comp=list&timeout=60&maxresults=100
```

[Nagłówki żądań](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** ta sekcja zawiera nagłówki żądań wymaganych i opcjonalnych. Wymagane są trzy nagłówki: *autoryzacji* nagłówka, *x-ms-date* (zawiera czas UTC żądania), i *x-ms-version* (określa wersję REST Interfejs API do użycia). W tym *x-ms-client żądania id* w nagłówkach jest opcjonalne — można ustawić wartości dla tego pola wartość; po włączeniu rejestrowania są zapisywane w dziennikach analityka magazynu.

[Treść żądania](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** brak treści żądania dla ListContainers. Treść żądania jest używana we wszystkich operacjach PUT podczas przekazywania obiektów blob, a także SetContainerAccessPolicy, dzięki czemu można wysyłać na liście XML zasad dostępu przechowywane do zastosowania. Zasady przechowywane dostępu opisano w artykule [przy użyciu dostępu sygnatur dostępu Współdzielonego](storage-dotnet-shared-access-signature-part-1.md).

[Kod stanu odpowiedzi](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** Tells żadnych kodów stanu, trzeba znać. W tym przykładzie kodu stanu HTTP 200 jest prawidłowy. Aby uzyskać pełną listę kodów stanu HTTP, zapoznaj się [definicje kod stanu](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Aby wyświetlić kody błędów specyficzne dla interfejsów API REST magazynu, zobacz [kody błędów wspólnego interfejsu API REST](/rest/api/storageservices/common-rest-api-error-codes)

[Nagłówki odpowiedzi](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** do nich *typu zawartości*; *x-ms-request-id* (identyfikator żądania zostanie przekazany, jeśli ma to zastosowanie); *x-ms-version* (wskazuje wersję usługi obiektów Blob używane) i *data* (UTC, informuje, jakie czasu wysłano żądanie).

[Treść odpowiedzi](/rest/api/storageservices/fileservices/List-Containers2#response-body): to pole jest struktury XML udostępnia żądanych danych. W tym przykładzie odpowiedzi znajduje się lista kontenery i ich właściwości.

## <a name="creating-the-rest-request"></a>Tworzenie żądania REST

Kilka notatek przed rozpoczęciem — zabezpieczeń podczas uruchamiania w środowisku produkcyjnym, należy zawsze używać protokołu HTTPS zamiast protokołu HTTP. Na potrzeby tego ćwiczenia należy użyć protokołu HTTP, aby wyświetlić dane żądań i odpowiedzi. Aby wyświetlić informacje dotyczące żądania i odpowiedzi w rzeczywistych wywołań REST, możesz pobrać [Fiddler](http://www.telerik.com/fiddler) lub podobnej aplikacji. W rozwiązaniu Visual Studio nazwy konta magazynu i klucz są zapisane na stałe w klasie, a metoda ListContainersAsyncREST przekazuje nazwę konta magazynu i klucza konta magazynu do metody, które są używane do tworzenia różnych składników żądania REST . W przypadku aplikacji rzeczywistych nazwy konta magazynu i klucz będzie znajdować się w pliku konfiguracji, zmienne środowiskowe, lub można pobrać z magazynu kluczy Azure.

W naszym przykładowy projekt kodu na potrzeby tworzenia nagłówek autoryzacji znajduje się w osobnej klasy z rozwiązaniem, że może zająć całe klasy i dodaj go do własnych rozwiązań i używać go "jak"jest. Kod nagłówek autoryzacji działa w przypadku większości wywołań interfejsu API REST usługi Azure Storage.

Aby utworzyć żądania, która jest obiekt HttpRequestMessage, przejdź do ListContainersAsyncREST w pliku Program.cs. Kroki tworzenia żądania są: 

* Utwórz identyfikator URI do zastosowania w przypadku wywoływania usługi. 
* Utwórz obiekt HttpRequestMessage i ustaw ładunku. Ładunek ma wartość null dla ListContainersAsyncREST, ponieważ firma Microsoft nie przechodząc żadnego z elementów.
* Dodaj nagłówki żądania x-ms-date i x-ms-version.
* Pobierz nagłówek autoryzacji i dodaj go.

Kilku podstawowych informacji, które są potrzebne: 

*  Dla ListContainers **metody** jest `GET`. Ta wartość jest ustawiana podczas tworzenia wystąpienia żądania. 
*  **Zasobów** jest część zapytania identyfikatora URI, który wskazuje, które interfejs API jest wywoływana, dlatego wartość `/?comp=list`. Jak wspomniano wcześniej, zasób znajduje się na stronę dokumentacji odwołania, który zawiera informacje o [ListContainers API](/rest/api/storageservices/fileservices/List-Containers2).
*  Identyfikator URI jest tworzony przez tworzenie punkt końcowy usługi Blob dla tego konta magazynu i łączenie zasobów. Wartość **identyfikatora URI żądania** kończy się on `http://contosorest.blob.core.windows.net/?comp=list`.
*  Dla ListContainers **requestBody** ma wartość null i nie ma żadnych dodatkowych **nagłówki**.

Różne interfejsów API może mieć inne parametry do przekazania w takich jak *ifMatch*. Przykładem, z których można użyć ifMatch jest podczas wywoływania metody PutBlob. W takim przypadku wartość ifMatch tag eTag i powoduje zaktualizowanie tylko obiektu blob w Jeśli eTag, podane przez użytkownika spełnia bieżący element eTag w obiekcie blob. Jeśli ktoś inny zaktualizował obiektu blob od momentu pobierania tag eTag, nie można zastąpić swoje zmiany. 

Najpierw należy ustawić `uri` i `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Następnie wystąpienia żądania, ustawienie metody `GET` i identyfikator URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Dodaj nagłówki żądania x-ms-date i x-ms-version. To miejsce w kodzie jest także umieszczane wszelkie nagłówki żądania dodatkowych wymaganych do wywołania. W tym przykładzie są żadnych dodatkowych nagłówków. Przykładowy interfejs API, który przekazuje w dodatkowych nagłówków to SetContainerACL. Dla magazynu obiektów Blob dodaje nagłówek o nazwie "x-ms-blob--dostępu publicznego" i wartość dla poziomu dostępu.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Wywołaj metodę, która tworzy nagłówek autoryzacji i dodaj go do nagłówki żądania. Pojawi się tworzenie nagłówka autoryzacji w dalszej części tego artykułu. Nazwa metody jest GetAuthorizationHeader, które widać w poniższym przykładzie:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

W tym momencie `httpRequestMessage` zawiera nagłówki autoryzacji ukończone żądania REST. 

## <a name="call-the-rest-api-with-the-request"></a>Wywołanie interfejsu API REST z żądania

Teraz, gdy masz żądania można wywołać SendAsync można wysłać żądania REST. SendAsync wywołanie interfejsu API i ponownie pobiera odpowiedź. Sprawdź odpowiedzi StatusCode (jest 200 OK), następnie przeanalizować odpowiedzi. W takim przypadku otrzymasz listy XML kontenerów. Oto kod dla wywołania metody GetRESTRequest do utworzenia żądania, wykonać żądania, a następnie sprawdź odpowiedzi dla listy kontenerów.

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

Jeśli takie jak uruchamianie penetratora sieciowego [Fiddler](https://www.telerik.com/fiddler) podczas wywołania do SendAsync, zawiera informacje żądania i odpowiedzi. Spójrzmy. Nazwa konta magazynu jest *contosorest*.

**Żądanie:**

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

**Stan kodu odpowiedzi nagłówki i zwrócona po wykonaniu:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Treść odpowiedzi (XML):** ListContainers dla pokazuje listę kontenerów i ich właściwości.

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

Teraz, że rozumiesz, jak utworzyć żądanie, wywołania tej usługi i przeanalizować wyniki, zobaczmy, jak utworzyć nagłówek autoryzacji. Tworzenie nagłówka jest skomplikowane, ale szczęście jest, że po utworzeniu kodu, praca, działa dla wszystkich interfejsów API REST usług magazynu.

## <a name="creating-the-authorization-header"></a>Tworzenie nagłówek autoryzacji

> [!TIP]
> Usługa Azure Storage obsługuje teraz integracji usługi Azure Active Directory (Azure AD) dla usług obiektów Blob i kolejek (wersja zapoznawcza). Usługi Azure AD, oferuje znacznie prostsze środowisko autoryzacji żądania do usługi Azure Storage. Aby uzyskać więcej informacji na używanie programu Azure AD do autoryzacji operacje REST, zobacz [uwierzytelniania w usłudze Azure Active Directory (wersja zapoznawcza)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Omówienie integracji z usługą Azure AD z usługą Azure Storage, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).

Brak artykuł opisujący koncepcyjnie (Brak kodu) jak wykonać [uwierzytelniania dla usług magazynu Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Załóżmy przetwarzania tego artykułu, aby dokładnie są potrzebne i wyświetlany jest kod.

Najpierw użyj uwierzytelniania klucza wspólnego. Format nagłówka autoryzacji wygląda następująco:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole podpisu jest Hash-based wiadomości kod uwierzytelniania (HMAC) utworzone na podstawie żądania i obliczane przy użyciu algorytmu SHA256, a następnie kodowany w formacie Base64. Otrzymano, który? (Zawieszenie w nim, nie wiesz nawet wyraz *w postaci kanonicznej* jeszcze.)

Następujący fragment kodu przedstawia format ciągu podpis klucza wspólnego:

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

Większość tych pól są rzadko używane. Dla magazynu obiektów Blob należy określić ZLECENIE, md5, długość zawartości, w postaci kanonicznej nagłówki i zasobów w postaci kanonicznej. Puste innych (ale put w `\n` który będzie wówczas traktował są puste).

Jakie są CanonicalizedHeaders i CanonicalizedResource? Dobrym pytanie. W rzeczywistości co w postaci kanonicznej średniej? Program Microsoft Word nawet nie rozpoznaje ją jako wyraz. Oto, co [Wikipedia informujący o zapewniania kanoniczności](http://en.wikipedia.org/wiki/Canonicalization): *informatyki, zapewniania kanoniczności (czasami normalizacji lub normalizacji) jest to proces konwertowania danych, które ma kilka możliwości reprezentacja w formie "standardowy", "normal" lub kanonicznej.* W mowy normalny, oznacza to przejąć na liście elementów (takich jak nagłówki w przypadku nagłówków w postaci kanonicznej) i normalizacji je na wymagany format. Zasadniczo Microsoft decyzję o formacie i konieczne jest zgodny.

Zacznijmy tych dwóch pól postaci kanonicznej, ponieważ są one wymagane do utworzenia nagłówek autoryzacji.

**Nagłówki w postaci kanonicznej**

Aby utworzyć tę wartość, pobrać nagłówki, które rozpoczynać się od "x - ms-" i sortowanie je, a następnie formatują ciąg `[key:value\n]` wystąpień połączone w jeden ciąg. Na przykład postaci kanonicznej nagłówki wyglądać następująco: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Oto kod pozwala utworzyć te dane wyjściowe:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Postaci kanonicznej zasobów**

Ta część ciągu podpisu reprezentuje objęci żądanie konta magazynu. Należy pamiętać, że identyfikator URI żądania jest `<http://contosorest.blob.core.windows.net/?comp=list>`, nazwą konta rzeczywiste (`contosorest` w takim przypadku). W tym przykładzie ten błąd jest zwracany:

```
/contosorest/\ncomp:list
```

Jeśli parametry zapytania, w tym te, jak również. Oto kod, który również obsługuje dodatkowe parametry i parametry zapytania z wieloma wartościami. Należy pamiętać, że tworzysz ten kod do działania dla wszystkich interfejsów API REST, więc chcesz uwzględnić wszystkie możliwości, nawet wtedy, gdy metoda ListContainers nie potrzebuje wszystkich z nich.

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

Teraz, gdy zostaną skonfigurowane w postaci kanonicznej ciągów, Przyjrzyjmy się tworzenie nagłówek autoryzacji. Rozpoczyna się od utworzenia ciąg podpisu wiadomości w formacie StringToSign wyświetlane wcześniej w tym artykule. Ta koncepcja jest łatwiejsze wyjaśnić, przy użyciu komentarze w kodzie, dlatego w tym miejscu jest, końcowego metodę, która zwraca nagłówek autoryzacji:

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

Po uruchomieniu tego kodu MessageSignature wynikowy wygląda następująco:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Oto końcowej dla AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader jest ostatni nagłówka, umieszczone w nagłówkach żądania przed opublikowaniem odpowiedzi.

Który obejmuje wszystko, co należy wiedzieć, wraz z kodem, aby opracować klasy, która umożliwia utworzenie żądanie można użyć do wywołania interfejsów API REST usług magazynu.

## <a name="how-about-another-example"></a>Jak inny przykład? 

Zobaczmy, jak zmienić kod, aby wywołać ListBlobs kontenera *kontenera 1*. To jest niemal identyczny z kodem do wyświetlania listy kontenerów, tylko różnice identyfikator URI i jak przeanalizować odpowiedzi. 

W dokumentacji referencyjnej dla [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), okaże się, że metoda jest *UZYSKAĆ* i RequestURI jest:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

W ListContainersAsyncREST Zmień kod, który ustawia identyfikator URI do interfejsu API dla ListBlobs. Nazwa kontenera jest **kontenera 1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Następnie którym obsługiwać odpowiedzi, Zmień kod, aby wyszukać obiekty BLOB zamiast kontenerów.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Po uruchomieniu tego przykładu, można uzyskać wyniki podobne do poniższych:

**Nagłówki postaci kanonicznej:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Zasób w postaci kanonicznej:**

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

Poniżej przedstawiono wartości z [Fiddler](http://www.telerik.com/fiddler):

**Żądanie:**

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

**Stan kodu odpowiedzi nagłówki i zwrócona po wykonaniu:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Treść odpowiedzi (XML):** odpowiedzi XML ten przedstawia listę obiektów blob i ich właściwości. 

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

W tym artykule przedstawiono sposób przesyłania żądania do magazynu obiektów blob interfejsu API REST, aby pobrać listę kontenery lub listę obiektów blob w kontenerze. Przedstawiono również sposób tworzenia podpisu autoryzacji dla wywołania interfejsu API REST, jak z niego korzystać w żądaniu REST i jak zbadać odpowiedzi.

## <a name="next-steps"></a>Kolejne kroki

* [Interfejs API REST usługi blob](/rest/api/storageservices/blob-service-rest-api)
* [Interfejs API REST usługi plików](/rest/api/storageservices/file-service-rest-api)
* [Interfejs API REST usługi kolejki](/rest/api/storageservices/queue-service-rest-api)