---
title: Podczas wywoływania operacji interfejsu API REST usługi Azure Storage, w tym uwierzytelniania | Dokumentacja firmy Microsoft
description: Podczas wywoływania operacji interfejsu API REST usługi Azure Storage, w tym uwierzytelniania
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9f6698eebf184d1df80920b7779512e2fda83a0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729345"
---
# <a name="using-the-azure-storage-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure Storage

W tym artykule dowiesz się, jak używać interfejsów API REST usługi Storage Blob oraz sposób uwierzytelniania połączenia z usługą. Jest ona zapisywana z punktu widzenia osoby, która nie zna o REST i nie wiadomo jak wykonywać wywołanie interfejsu REST, ale jest deweloperem. Firma Microsoft Przejrzyj dokumentację referencyjną wywołanie interfejsu REST i zobacz, jak tłumaczenie rzeczywistego wywołania REST — pola, które go miejsce? Po wiedzę, jak skonfigurować wywołanie interfejsu REST, możesz korzystać z tej wiedzy, aby użyć dowolnego innego magazynu usługi interfejsów API REST.

## <a name="prerequisites"></a>Wymagania wstępne 

Aplikacja wyświetla listę kontenerów w magazynie obiektów blob dla konta magazynu. Aby wypróbować ten kod w tym artykule, potrzebne są następujące elementy: 

* Zainstaluj [programu Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) o następujących obciążeniach:
    - Tworzenie aplikacji na platformie Azure

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto magazynu ogólnego przeznaczenia. Jeśli nie masz jeszcze konta magazynu, zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md).

* W przykładzie w tym artykule pokazano sposób wyświetlenia listy kontenerów na koncie magazynu. Aby wyświetlić dane wyjściowe, należy dodać niektórych kontenerów, aby przed rozpoczęciem korzystania z usługi blob storage na koncie magazynu.

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

Przykładowa aplikacja jest aplikacji konsolowej napisanej w języku C#.

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć rozwiązanie programu Visual Studio, wyszukaj storage-dotnet-rest-api-with-auth folder, otwórz go i kliknij dwukrotnie StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Co to jest REST?

Oznacza, że REST *prezentowania*. Dla określonej definicji, zapoznaj się z [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

Po prostu REST to architektura, służy do wywoływania interfejsów API lub Tworzenie interfejsów API do wywołania. Jest ono niezależne, co się dzieje po obu stronach i jakie inne oprogramowanie jest używana podczas wysyłania lub odbierania pozostałe wywołania. Można napisać aplikację, która działa na komputerze Mac, Windows, Linux, telefon z systemem Android lub tabletu, iPhone, iPod lub witryny sieci web i używać tego samego interfejsu API REST dla wszystkich tych platform. Dane mogą być przekazywane w i/lub się po wywołaniu interfejsu API REST. Interfejs API REST zależy od platformy jest wywoływane — ważne jest informacje przekazywane w żądaniu i danych zawartych w odpowiedzi.

Wiedza, jak przy użyciu architektury REST jest przydatne umiejętności. Zespół pracujący nad produktem Azure często wydaje nowe funkcje. Wiele razy, nowe funkcje są dostępne za pośrednictwem interfejsu REST, ale nie zostały udostępnione za pośrednictwem **wszystkich** bibliotek klienckich magazynu lub interfejsu użytkownika (np. witryna Azure portal). Jeśli chcesz zawsze używać najnowszej i najlepszej, nauki REST jest wymagana. Ponadto jeśli chcesz zapisać biblioteki do interakcji z usługą Azure Storage lub chcesz uzyskać dostęp do usługi Azure Storage przy użyciu języka programowania, którego nie ma zestawu SDK lub magazynu biblioteki klienta, możesz użyć interfejsu API REST.

## <a name="about-the-sample-application"></a>Temat przykładowej aplikacji

Przykładowa aplikacja wyświetla listę kontenerów na koncie magazynu. Po zapoznaniu się, jak informacje przedstawione w dokumentacji interfejsu API REST jest skorelowane do rzeczywistego kodu, inne wywołania REST są łatwiejsze ustalenie. 

Jeśli przyjrzymy się [interfejsu API REST usługi Blob](/rest/api/storageservices/Blob-Service-REST-API), zobaczysz wszystkie operacje można wykonywać na magazynu obiektów blob. Biblioteki klienta magazynu są otok wokół interfejsów API REST — ułatwiają one można uzyskać dostęp do magazynu bez bezpośrednio za pomocą interfejsów API REST. Ale jak wspomniano powyżej, czasami trzeba zamiast biblioteki klienta magazynu za pomocą interfejsu API REST.

## <a name="rest-api-reference-list-containers-api"></a>Dokumentacja interfejsu API REST: Lista kontenerów interfejsu API

Przyjrzyjmy się na stronie dokumentacja interfejsu API REST [ListContainers](/rest/api/storageservices/List-Containers2) operację, aby zrozumieć, niektóre pola pochodzenie w żądaniu i odpowiedzi w następnej sekcji kodu.

**Metoda żądania**: POBIERZ. To polecenie jest metoda HTTP, który został określony jako właściwość obiektu żądania. Inne wartości dla tego zlecenia obejmują HEAD, PUT i DELETE, w zależności od tego, wywoływany jest interfejs API.

**Identyfikator URI żądania**: https://myaccount.blob.core.windows.net/?comp=list  To jest tworzona na podstawie punkt końcowy konta usługi blob storage `http://myaccount.blob.core.windows.net` i ciągu zasobu `/?comp=list`.

[Parametry identyfikatora URI](/rest/api/storageservices/List-Containers2#uri-parameters): Istnieją dodatkowe parametry zapytania używane podczas wywoływania ListContainers. Kilka z tych parametrów są *limitu czasu* wywołania (w sekundach) i *prefiks*, która jest używana do filtrowania.

Inny parametr pomocne jest *maxresults:* Jeśli większej liczbie kontenerów są dostępne od tej wartości, będzie zawierać treści odpowiedzi *NextMarker* element, który wskazuje następnego kontenera do zwrócenia na następnej żądanie. Aby użyć tej funkcji, podaj *NextMarker* wartość jako *znacznika* parametru w identyfikatorze URI po wprowadzeniu następnego żądania. Dzięki tej funkcji jest odpowiednikiem stronicować wyniki. 

Aby korzystać z dodatkowych parametrów, dołącz je do ciągu zasobu o wartości, np. w tym przykładzie:

```
/?comp=list&timeout=60&maxresults=100
```

[Nagłówki żądania](/rest/api/storageservices/List-Containers2#request-headers)**:** Ta sekcja zawiera nagłówki żądania wymaganych i opcjonalnych. Wymagane są trzy nagłówki: *autoryzacji* nagłówka, *x-ms-date* (zawiera czas UTC żądania), a *x-ms-version* (określa wersję pozostałe Interfejs API do użycia). W tym *x-ms klient request-id* w nagłówkach jest opcjonalna — można ustawić wartość dla tego pola do żadnego elementu; są zapisywane do magazynu analizy dzienników, gdy jest włączone rejestrowanie.

[Treść żądania](/rest/api/storageservices/List-Containers2#request-body)**:** Brak treści żądania nie ListContainers. Treść żądania jest używana we wszystkich operacji PUT przy przekazywaniu obiektów blob, a także SetContainerAccessPolicy, co pozwala na wysyłanie na liście XML przechowywane zasady dostępu do zastosowania. Zapisane zasady dostępu zostały omówione w artykule [przy użyciu dostępu współdzielonego Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Kod stanu odpowiedzi](/rest/api/storageservices/List-Containers2#status-code)**:** Informuje żadnych kodów stanu, które trzeba znać. W tym przykładzie kod stanu HTTP 200 to ok. Aby uzyskać pełną listę kodów stanu HTTP, zapoznaj się [definicjami kodów stanu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Aby wyświetlić kody błędów specyficzne dla interfejsów API REST magazynu, zobacz [kody błędów wspólnego interfejsu API REST](/rest/api/storageservices/common-rest-api-error-codes)

[Nagłówki odpowiedzi](/rest/api/storageservices/List-Containers2#response-headers)**:** Obejmują one *typu zawartości*; *x-ms-request-id* (identyfikator żądania przekazałeś, jeśli ma to zastosowanie); *x-ms-version* (wskazuje wersję usługi obiektów Blob używane), a *data* (czas UTC, informuje, jakich czasie wysłano żądanie).

[Treść odpowiedzi](/rest/api/storageservices/List-Containers2#response-body): To pole jest zapewnienie żądanych danych struktury XML. W tym przykładzie odpowiedź jest listę kontenerów i ich właściwości.

## <a name="creating-the-rest-request"></a>Tworzenie żądania REST

Kilka uwag dotyczących przed rozpoczęciem — aby zapewnić bezpieczeństwo podczas uruchamiania w środowisku produkcyjnym, należy zawsze używać protokołu HTTPS zamiast protokołu HTTP. Na potrzeby tego ćwiczenia należy użyć protokołu HTTP, aby można było wyświetlić dane żądań i odpowiedzi. Aby wyświetlić informacje dotyczące żądania i odpowiedzi w rzeczywistych wywołania REST, możesz pobrać [Fiddler](https://www.telerik.com/fiddler) lub podobnej aplikacji. W rozwiązaniu Visual Studio nazwa konta magazynu i klucz są zapisane na stałe w klasie, a metoda ListContainersAsyncREST przekazuje nazwę konta magazynu i klucza konta magazynu do metod, które są używane do tworzenia różnych składników żądania REST . W rzeczywistej aplikacji Nazwa konta magazynu i klucz będzie znajdować się w pliku konfiguracji, zmienne środowiskowe, lub można pobrać z usługi Azure Key Vault.

W naszym przykładowym projekcie kodu na potrzeby tworzenia nagłówek autoryzacji znajduje się w osobnej klasy z rozwiązaniem, że możesz może zająć całą klasę i dodaj go do własnego rozwiązania i używać go "as""to. Kod nagłówka autoryzacji działa w przypadku większości wywołań interfejsu API REST do usługi Azure Storage.

Aby tworzyć żądania, który jest obiekt HttpRequestMessage, przejdź do ListContainersAsyncREST w pliku Program.cs. Procedura tworzenia żądania jest następująca: 

* Utwórz identyfikator URI, który ma być używany do wywoływania usługi. 
* Utwórz obiekt HttpRequestMessage i ustaw ładunku. Ładunek jest pusta dla ListContainersAsyncREST, ponieważ firma Microsoft nie przechodząc w.
* Dodaj nagłówki żądania x-ms-date i x-ms-version.
* Pobrać nagłówka autoryzacji, a następnie dodaj go.

Niektóre podstawowe informacje, które są potrzebne: 

*  Aby uzyskać ListContainers **metoda** jest `GET`. Ta wartość jest ustawiana podczas tworzenia wystąpienia żądania. 
*  **Zasobów** jest część zapytania identyfikatora URI, który wskazuje, które interfejs API jest wywoływana, dlatego wartość `/?comp=list`. Jak wspomniano wcześniej, zasób znajduje się na stronie dokumentacji zawierającej informacje o [ListContainers API](/rest/api/storageservices/List-Containers2).
*  Identyfikator URI jest tworzony przez tworzenie punktu końcowego usługi Blob dla tego konta magazynu i łączenie zasobu. Wartość **identyfikator URI żądania** kończy się on `http://contosorest.blob.core.windows.net/?comp=list`.
*  Aby uzyskać ListContainers **requestBody** ma wartość null wiąże się z funkcjami bez żadnych dodatkowych **nagłówki**.

Różne interfejsy API mogą mieć inne parametry, takie jak przekazywanie *ifMatch*. Przykład, w którym mogą korzystać z ifMatch jest podczas wywoływania PutBlob. W takim przypadku ifMatch jest ustawiona na element eTag i powoduje zaktualizowanie tylko obiektu blob w przypadku element eTag, których udzielasz dopasowuje bieżący element eTag w obiekcie blob. Jeśli ktoś inny był aktualizowany obiekt blob, od pobierania element eTag, nie będzie można zastąpić swoje zmiany. 

Najpierw ustaw `uri` i `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Następnie utwórz wystąpienie żądania, ustawienie metody `GET` i podając identyfikator URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Dodaj nagłówki żądania x-ms-date i x-ms-version. To miejsce w kodzie jest również, gdzie dodać wszelkie dodatkowe nagłówki żądania wymaganych do wywołania. W tym przykładzie Brak dodatkowych nagłówków. Przykład interfejsu API, który przekazuje dodatkowe nagłówki jest SetContainerACL. Dla magazynu obiektów Blob dodaje nagłówek o nazwie "x-ms-obiektów blob — publiczny — dostępu" i wartość dla poziomu dostępu.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Wywołaj metodę, która utworzy nagłówek autoryzacji i dodaj go do nagłówków żądań. Pokazano, jak utworzyć nagłówek autoryzacji w dalszej części tego artykułu. Nazwa metody jest GetAuthorizationHeader, którą można zobaczyć, w tym fragmencie kodu:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

W tym momencie `httpRequestMessage` zawiera żądania REST z nagłówków autoryzacji. 

## <a name="call-the-rest-api-with-the-request"></a>Wywołanie interfejsu API REST wraz z żądaniem

Teraz, gdy żądanie, możesz wywołać SendAsync można wysłać żądania REST. SendAsync wywołuje interfejs API i otrzymuje w odpowiedzi. Sprawdź odpowiedzi StatusCode (to 200 OK), następnie przeanalizować odpowiedzi. W takim przypadku otrzymujesz listę XML kontenerów. Przyjrzyjmy się kod do wywoływania metody GetRESTRequest do utworzenia żądania, wykonaj żądanie, a następnie sprawdź odpowiedzi na liście kontenerów.

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

Po uruchomieniu takich jak penetratora sieciowego [Fiddler](https://www.telerik.com/fiddler) po wywołania, aby SendAsync, można wyświetlić informacje dotyczące żądania i odpowiedzi. Przyjrzyjmy się. Nazwa konta magazynu jest *contosorest*.

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

**Zwrócone nagłówki stan kodu i odpowiedź po wykonaniu:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Treść odpowiedzi (XML):** Dla ListContainers to pokazuje listę kontenerów i ich właściwości.

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

Teraz, gdy już rozumiesz sposób utworzenia żądania, wywoła usługę i przeanalizować wyniki, zobaczmy, jak utworzyć nagłówka autoryzacji. Tworzenie nagłówka jest skomplikowane, ale dobra wiadomość jest taka, gdy kod działa, działa dla wszystkich interfejsów API REST usługi Storage.

## <a name="creating-the-authorization-header"></a>Tworzenie nagłówka autoryzacji

> [!TIP]
> Usługa Azure Storage obsługuje teraz integrację usługi Azure Active Directory (Azure AD) dla kolejek i obiektów blob. Usługi Azure AD oferuje znacznie prostsze środowisko do autoryzowania żądania do usługi Azure Storage. Aby uzyskać więcej informacji na temat korzystania z usługi Azure AD do autoryzowania operacje REST, zobacz [uwierzytelnianie w usłudze Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Omówienie integracji z usługą Azure AD z usługą Azure Storage, zobacz [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](storage-auth-aad.md).

Znajduje się artykuł, który objaśnia, koncepcyjnie (Brak kodu) sposób wykonywania [uwierzytelniania dla usług Azure Storage](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Umożliwia wyodrębnianie określonych tego artykułu, aby dokładnie jest wymagany i wyświetlany jest kod.

Najpierw za pomocą uwierzytelniania klucza wspólnego. Format nagłówka autoryzacji wygląda następująco:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Pole podpisu jest bazujących na skrótach komunikat o kod uwierzytelniania (HMAC) utworzona na podstawie żądania i obliczane przy użyciu algorytm SHA256, a następnie kodowany w formacie Base64. Masz,? (Zawieszanie w miejscu, nie zostały jeszcze słyszeli wyraz *w postaci kanonicznej* jeszcze.)

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

Większość z tych pól są rzadko używane. Dla magazynu obiektów Blob należy określić ZLECENIE, md5, długość zawartości, w postaci kanonicznej nagłówków i zasobów w postaci kanonicznej. Puste innych (ale put w `\n` będzie wówczas traktował są puste).

Jakie są CanonicalizedHeaders i CanonicalizedResource? Dobre pytanie. W rzeczywistości jak działa w postaci kanonicznej średniej? Program Microsoft Word nawet nie rozpoznaje je jako wyraz. Oto, co [Wikipedia mówi o canonicalization](https://en.wikipedia.org/wiki/Canonicalization): *Informatyki kanoniczną (czasami normalizacji lub normalizacji) to proces konwersji danych, który ma więcej niż jedną możliwą reprezentację w formie "standardowy", "normal" lub kanonicznej.* Czytaj w normalnym, oznacza to przejąć kontrolę na liście elementów (takich jak nagłówki w przypadku nagłówków w postaci kanonicznej) i je ustandaryzować w wymaganym formatem. Po prostu Microsoft decyzję formatu i muszą zapewnić zgodność.

Zacznijmy od tych dwóch pól postaci kanonicznej, ponieważ są one wymagane do utworzenia nagłówka autoryzacji.

**Nagłówki w postaci kanonicznej**

Aby utworzyć tę wartość, nagłówki, które zaczynają "x - ms-" i sortować je pobrać, a następnie formatują ciągu `[key:value\n]` wystąpień, połączone w jeden ciąg. W tym przykładzie postaci kanonicznej nagłówki wyglądać następująco: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Poniżej przedstawiono kod używany do tworzenia te dane wyjściowe:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
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

**Zasób w postaci kanonicznej**

Ta część ciągu podpisu reprezentuje konto magazynu, docelowe przez żądanie. Należy pamiętać, że identyfikator URI żądania jest `<http://contosorest.blob.core.windows.net/?comp=list>`, za pomocą rzeczywista nazwa konta (`contosorest` w tym przypadku). W tym przykładzie ta wartość jest zwracana:

```
/contosorest/\ncomp:list
```

W przypadku parametrów zapytania w tym te, jak również. Poniżej przedstawiono kod, który obsługuje także dodatkowe parametry zapytania i parametry zapytania z wieloma wartościami. Należy pamiętać, że tworzysz ten kod w celu pracy dla wszystkich interfejsów API REST, które chcesz uwzględnić wszystkie możliwości, nawet wtedy, gdy metoda ListContainers nie potrzebuje wszystkich z nich.

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

Teraz, gdy w postaci kanonicznej ciągi są ustawione, Przyjrzyjmy się tworzenie nagłówka autoryzacji, sam. Możesz rozpocząć od utworzenia ciąg podpisu wiadomości w formacie StringToSign wcześniej wyświetlane w tym artykule. Takie podejście jest łatwiejsze do wyjaśnienia, za pomocą komentarzy w kodzie, więc w tym miejscu jest, ostatnią metodę, która zwraca nagłówek autoryzacji:

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

Po uruchomieniu tego kodu, wynikowy MessageSignature wygląda następująco:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Oto wartość końcową dla AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader jest ostatni nagłówek umieszczany w nagłówkach żądania przed opublikowaniem odpowiedzi.

Która obejmuje wszystko, czego potrzebujesz, aby dowiedzieć się, wraz z kodem, dozą klasy, która służy do tworzenia żądania ma być używany do wywoływania interfejsów API REST usługi Storage.

## <a name="how-about-another-example"></a>Co myślisz o inny przykład? 

Przyjrzyjmy się jak zmienić kod, aby Wywołaj metodę ListBlobs dla kontenera *1 kontenera*. To jest niemal identyczny kod do wyświetlania listy kontenerów, tylko różnic, identyfikator URI i jak przeanalizować odpowiedzi. 

Jeśli wyświetlasz dokumentację referencyjną [ListBlobs](/rest/api/storageservices/List-Blobs), okaże się, że metoda jest *UZYSKAĆ* i RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

W ListContainersAsyncREST Zmień kod, który ustawia identyfikator URI do interfejsu API dla ListBlobs. Nazwa kontenera jest **1 kontenera**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Następnie gdzie obsługi odpowiedzi, Zmień kod do wyszukiwania dla obiektów blob zamiast kontenerów.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Po uruchomieniu tego przykładu, możesz uzyskać wyniki podobne do następującego:

**Nagłówki w postaci kanonicznej:**

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

Poniżej przedstawiono wartości z [Fiddler](https://www.telerik.com/fiddler):

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

**Zwrócone nagłówki stan kodu i odpowiedź po wykonaniu:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Treść odpowiedzi (XML):** Ta odpowiedź XML zawiera listę obiektów blob i ich właściwości. 

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

W tym artykule przedstawiono sposób wysłać żądanie do interfejsu API REST, aby pobrać listę kontenerów lub listę obiektów blob w kontenerze magazynu obiektów blob. Przedstawiono również sposób tworzenia podpisu autoryzacji dla wywołania interfejsu API REST, jak z niej korzystać w żądaniu REST i jak zbadać odpowiedź.

## <a name="next-steps"></a>Kolejne kroki

* [Interfejs API REST usługi blob](/rest/api/storageservices/blob-service-rest-api)
* [Interfejs API REST usługi plików](/rest/api/storageservices/file-service-rest-api)
* [Interfejs API REST usługi kolejek](/rest/api/storageservices/queue-service-rest-api)
