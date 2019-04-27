---
title: Wprowadzenie do dostarczania zawartości na żądanie przy użyciu usługi REST | Dokumentacja firmy Microsoft
description: Ten samouczek przeprowadzi Cię przez kroki wdrażania aplikacji dostarczania zawartości na żądanie przy użyciu usługi Azure Media Services przy użyciu interfejsu API REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: fea9bae9fadc20622a6ca3d2e08db9cd3a92c800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639439"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Wprowadzenie do dostarczania zawartości na żądanie przy użyciu usługi REST  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki wdrażania aplikacji do dostarczania zawartości wideo na żądanie (VoD) przy użyciu interfejsów API REST usługi Azure Media Services (AMS).

Samouczek przedstawia podstawowy przepływ pracy usług Media Services oraz najczęściej występujące obiekty i zadania programowania wymagane w celu projektowania usług Media Services. Po ukończeniu tego samouczka możesz się strumieniowo lub pobrać progresywnie przykładowy plik nośnika, który przekazany, zakodowany i pobrane.

Na poniższym obrazie przedstawiono niektóre z najczęściej używanych obiektów podczas tworzenia aplikacji VoD w modelu Media Services OData.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Wymagania wstępne
Następujące wymagania wstępne są wymagane, aby rozpocząć tworzenie aplikacji za pomocą usługi Media Services za pomocą interfejsów API REST.

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* Wiedzą, jak tworzyć aplikacje za pomocą interfejsu API REST usługi Media Services. Aby uzyskać więcej informacji, zobacz [omówienie interfejsu API REST usługi Media Services](media-services-rest-how-to-use.md).
* Aplikacja, który można wysłać żądań i odpowiedzi HTTP. W tym samouczku [Fiddler](https://www.telerik.com/download/fiddler).

Następujące zadania są wyświetlane w tym przewodniku Szybki Start.

1. Uruchamianie punktów końcowych przesyłania strumieniowego (przy użyciu witryny Azure Portal).
2. Połączyć konto usługi Media Services za pomocą interfejsu API REST.
3. Tworzenie nowego elementu zawartości i przekazywanie pliku wideo za pomocą interfejsu API REST.
4. Kodowanie pliku źródłowego do zestawu plików MP4 o adaptacyjnej szybkości transmisji bitów przy użyciu interfejsu API REST.
5. Publikowanie elementu zawartości i uzyskiwanie przesyłania strumieniowego i pobierania progresywnego adresy URL przy użyciu interfejsu API REST.
6. Odtwarzanie zawartości.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze używasz tych samych dni / dostęp do uprawnień, na przykład zasad lokalizatorów, które powinny pozostać w miejscu przez długi czas (nieprzekazywane zasady). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Aby uzyskać szczegółowe informacje na temat jednostek REST usługi AMS używane w tym artykule, zobacz [dokumentacja interfejsu API REST usługi multimediów Azure](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Zobacz też [pojęcia dotyczące usługi Azure Media Services](media-services-concepts.md).

>[!NOTE]
>Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Uruchamianie punktów końcowych przesyłania strumieniowego przy użyciu witryny Azure Portal

Podczas pracy z usługą Azure Media Services, jednym z najbardziej typowych scenariuszy jest dostarczanie obrazu wideo za pośrednictwem przesyłania strumieniowego o adaptacyjnej szybkości transmisji bitów. Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać kodowaną zawartość plików MP4 z adaptacyjną szybkością transmisji bitów w formatach przesyłania strumieniowego obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) w odpowiednim czasie bez konieczności przechowywania wersji wstępnie utworzonych pakietów poszczególnych formatów przesyłania strumieniowego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Aby uruchomić punkt końcowy przesyłania strumieniowego, wykonaj następujące czynności:

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).
2. W oknie Ustawienia kliknij pozycję Punkty końcowe przesyłania strumieniowego.
3. Kliknij domyślny punkt końcowy przesyłania strumieniowego.

    Zostanie wyświetlone okno SZCZEGÓŁY DOMYŚLNEGO PUNKTU KOŃCOWEGO PRZESYŁANIA STRUMIENIOWEGO.

4. Kliknij ikonę Uruchom.
5. Kliknij przycisk Zapisz, aby zapisać zmiany.

## <a id="connect"></a>Połączenie z kontem usługi Media Services za pomocą interfejsu API REST

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Tworzenie nowego elementu zawartości i przekazywanie pliku wideo za pomocą interfejsu API REST

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. **Zasobów** jednostki może zawierać wideo, audio, obrazy, kolekcje miniatur, tekst śledzi i napisów plików (i metadane dotyczące tych plików.)  Po przekazaniu plików do elementu zawartości, zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

Jedna z wartości, które należy podać podczas tworzenia zasobu jest opcji tworzenia elementów zawartości. **Opcje** właściwość jest wartością wyliczenia, która w tym artykule opisano opcje szyfrowania, które można utworzyć element zawartości za pomocą. Nieprawidłowa wartość jest jedną z wartości z listy poniżej, a nie kombinacji wartości z tej listy:

* **Brak** = **0** — bez szyfrowania jest używany. Przy użyciu tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.
    Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji.
* **StorageEncrypted** = **1** — zawartości lokalnie przy użyciu algorytmu AES-256-bitowego szyfruje i przekazuje je do usługi Azure Storage gdzie są przechowywane, szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
* **CommonEncryptionProtected** = **2** — Użyj tej opcji, jeśli przekazujesz zawartość, która jest już zaszyfrowana i chroniona za pomocą wspólnego szyfrowania lub technologii PlayReady DRM (na przykład, Smooth Streaming chronione przy użyciu technologii PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** — Użyj tej opcji, jeśli przekazujesz HLS zaszyfrowanych z użyciem standardu AES. Należy zakodowane pliki i szyfrowane przez Transform Manager.

### <a name="create-an-asset"></a>Utworzenie elementu zawartości
Element zawartości jest kontenerem dla wielu typów lub zestawów obiektów w usłudze Media Services, w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów. W interfejsie API REST tworzenia zasobu wymaga wysłanie żądania POST do usługi Media Services oraz umieszczanie właściwości informacji o elementów zawartości w treści żądania.

Poniższy przykład pokazuje, jak utworzyć element zawartości.

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następujące czynności:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Utwórz AssetFile
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) jednostki reprezentuje plik wideo lub audio, który jest przechowywany w kontenerze obiektów blob. Plik zasobów jest zawsze skojarzone z elementem zawartości i zasobów może zawierać jeden lub wiele AssetFiles. Zadanie Media Services Encoder kończy się niepowodzeniem, jeśli obiekt pliku zasobów nie jest skojarzony z plikiem cyfrowych w kontenerze obiektów blob.

Po przekazaniu pliku multimediów cyfrowych do kontenera obiektów blob, użyj **scalania** żądania HTTP, aby zaktualizować AssetFile z informacjami o pliku multimedialnego (jak pokazano w dalszej części tematu).

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Odpowiedź HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Tworzenie AccessPolicy z uprawnieniami do zapisu
Przed przekazaniem żadnych plików do magazynu obiektów blob, należy ustawić dostępu zasady prawa do zapisu do elementu zawartości. Aby to zrobić, OPUBLIKUJ żądanie HTTP AccessPolicies zestawem jednostek. Zdefiniuj wartość DurationInMinutes po utworzeniu lub pojawi się komunikat 500 Błąd wewnętrzny serwera w odpowiedzi. Aby uzyskać więcej informacji na temat AccessPolicies, zobacz [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

Poniższy przykład pokazuje, jak utworzyć AccessPolicy:

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Pobierz adres URL przesyłania

Aby otrzymywać z adresem URL rzeczywistej przekazywania, należy utworzyć Lokalizator sygnatury dostępu Współdzielonego. Lokalizatory definiują czas rozpoczęcia i typu punktu końcowego połączenia dla klientów, którzy chcą uzyskać dostęp do plików w zasobie. Możesz utworzyć wiele jednostek lokalizatora dla danego pary AccessPolicy i zasobów do obsługi żądań różnych klientów i potrzeb. Wartość StartTime plus wartość DurationInMinutes AccessPolicy każdego z tych lokalizatorów używa do określenia czasu, można użyć adresu URL. Aby uzyskać więcej informacji, zobacz [lokalizatora](https://docs.microsoft.com/rest/api/media/operations/locator).

Adres URL SAS ma następujący format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Zagadnienia do rozważenia:

* Nie może mieć więcej niż pięć unikatowe Lokalizatory skojarzone z danym elementem zawartości w tym samym czasie. 
* Jeśli zachodzi potrzeba natychmiast przekazać pliki, należy ustawić wartość StartTime do pięciu minut przed bieżącym czasem. Jest to spowodowane mogą istnieć zegara niesymetryczność między komputerem klienckim i Media Services. Ponadto wartość StartTime musi być w następującym formacie daty/godziny: RRRR-MM-ddtgg (na przykład "2014-05-23T17:53:50Z").    
* Może to być sekundy 30 – 40 opóźnienie po utworzeniu lokalizatora, gdy będzie ona dostępna do użytku. Ten problem dotyczy zarówno [adresu URL sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) oraz Lokalizatory pochodzenia.

Poniższy przykład pokazuje, jak utworzyć Lokalizator adresu URL sygnatury dostępu Współdzielonego, zgodnie z definicją we właściwości typu w treści żądania ("1" dla lokalizatora sygnatury dostępu Współdzielonego) i "2" dla lokalizatora origin na żądanie. **Ścieżki** właściwości zwróconej zawiera adres URL, do którego należy użyć, aby przesłać plik.

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Przekaż plik do kontenera magazynu obiektów blob
Po utworzeniu AccessPolicy i lokalizatora zestawu rzeczywisty plik zostanie przekazany do kontenera magazynu obiektów blob platformy Azure przy użyciu interfejsów API REST usługi Azure Storage. Należy przekazać pliki jako blokowe obiekty BLOB. Stronicowe obiekty BLOB nie są obsługiwane przez usługę Azure Media Services.  

> [!NOTE]
> Należy dodać nazwę pliku dla pliku, który chcesz przekazać do Lokalizator **ścieżki** wartość odebrana w poprzedniej sekcji. Na przykład `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Aby uzyskać więcej informacji na temat pracy z obiektami blob usługi Azure storage, zobacz [interfejsu API REST usługi Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Aktualizacja AssetFile
Teraz, gdy już przesłano pliku, należy zaktualizować informacje o FileAsset rozmiar (i inne). Na przykład:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następujące czynności:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Usuń lokalizator i AccessPolicy
**Żądanie HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następujące czynności:

    HTTP/1.1 204 No Content
    ...

**Żądanie HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następujące czynności:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów

Po wprowadzane, które mogą być zakodowane zasoby do usługi Media Services, media, transmultipleksacji znakiem wodnym i tak dalej przed dostarczeniem do klientów. Te działania są zaplanowane i uruchamiane w wielu wystąpieniach ról w tle, aby zapewnić wysoką wydajność oraz dostępność. Te działania są nazywane zadaniami, a każde zadanie składa się z niepodzielnych podzadań, które wykonują rzeczywistą pracę w pliku zasobów (Aby uzyskać więcej informacji, zobacz [zadania](https://docs.microsoft.com/rest/api/media/operations/job), [zadań](https://docs.microsoft.com/rest/api/media/operations/task) opisy).

Jak wspomniano wcześniej, podczas pracy za pomocą usługi Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie adaptacyjną szybkością transmisji bitów, przesyłanie strumieniowe do klientów. Usługa Media Services, korzystając z funkcji dynamicznego tworzenia pakietów, może utworzyć pakiet zestawu plików MP4 z adaptacyjną szybkością transmisji bitów w jednym z następujących formatów: HTTP na żywo przesyłania strumieniowego (HLS), Smooth Streaming i MPEG DASH.

W poniższej sekcji pokazano, jak utworzyć zadanie, które zawiera jedno zadanie kodowania. Zadanie określa transkodowanie pliku mezzanine do zestawu z każdego pliku MP4 z adaptacyjną szybkością transmisji bitów przy użyciu **Media Encoder Standard**. W sekcji przedstawiono również sposób monitorowania zadania postęp przetwarzania. Po zakończeniu zadania będzie możliwe do utworzenia lokalizatorów, które są niezbędne do uzyskiwania dostępu do zasobów.

### <a name="get-a-media-processor"></a>Pobierz procesor multimediów
W usłudze Media Services procesor multimediów jest składnikiem, który obsługuje zadanie przetwarzania specyficznego, takich jak kodowania, konwersji formatów, zawartości multimedialnej szyfrowania lub odszyfrowywania. Dla zadania kodowania, przedstawione w tym samouczku są użyjemy Media Encoder Standard.

Poniższy kod żądania identyfikator kodera.

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpowiedź HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Tworzenie zadania
Każde zadanie może mieć jedno lub więcej zadań, w zależności od rodzaju przetwarzania, które chcesz osiągnąć. Za pomocą interfejsu API REST można utworzyć zadań i ich powiązane zadania, w jeden z dwóch sposobów: Zadania mogą być zdefiniowano w tekście za pomocą właściwości nawigacji zadania na jednostkach zadania lub przetwarzanie wsadowe OData. Zestaw SDK usług Media Services korzysta z przetwarzania wsadowego. Aby zwiększyć czytelność, przykładowe kody w tym artykule, zadania są zdefiniowano w tekście. Aby uzyskać informacji na temat przetwarzania wsadowego, zobacz [przetwarzanie wsadowe Open Data Protocol (OData)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Poniższy przykład pokazuje, jak utworzyć i opublikuj zadania przy użyciu jednego, ustawionych przez zadanie do zakodowania filmu w określonym rozwiązania i jakości. Poniższa sekcja dokumentacji zawiera listę wszystkich [zadań wstępne](https://msdn.microsoft.com/library/mt269960) obsługiwane przez procesora Media Encoder Standard.  

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Istnieje kilka ważnych rzeczy, należy pamiętać, we wszystkich żądaniach zadania:

* Taskbody — właściwości należy użyć literał XML, aby zdefiniować liczbę dane wejściowe lub wyjściowe zasoby, które są używane przez zadanie. Ten artykuł zadań zawiera definicji schematu XML dla pliku XML.
* W definicji taskbody — wartość każdego wewnętrzny `<inputAsset>` i `<outputAsset>` musi być ustawiona jako JobInputAsset(value) lub JobOutputAsset(value).
* Zadanie może mieć wielu zasobów danych wyjściowych. Jeden JobOutputAsset(x) należy używać tylko jeden raz jako dane wyjściowe zadania w zadaniu.
* Możesz określić JobInputAsset lub JobOutputAsset jako wejściowego elementu zadania.
* Zadania muszą nie tworzą cyklu.
* Parametr wartości, który jest przekazywany do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu dla zasobu. Rzeczywiste zasoby są zdefiniowane w InputMediaAssets i OutputMediaAssets właściwości nawigacji na definicję jednostki zadania.

> [!NOTE]
> Ponieważ usługa Media Services jest oparta na protokole OData v3, poszczególnych zasobów w kolekcji właściwości nawigacji InputMediaAssets i OutputMediaAssets są wywoływane za pośrednictwem "__metadata: identyfikator uri" pary nazwa wartość.
>
>

* InputMediaAssets jest mapowany na jeden lub więcej zasobów, które zostały utworzone w usłudze Media Services. OutputMediaAssets są tworzone przez system. Nie odwołują się do istniejącego zasobu.
* OutputMediaAssets może być nazwane za pomocą atrybutu assetName. Jeśli ten atrybut nie jest obecny, a następnie nazywa OutputMediaAsset niezależnie od tekst wewnętrzny wartość `<outputAsset>` element jest sufiksem Nazwa zadania wartość lub wartość identyfikatora zadania (w przypadku, w którym nie zdefiniowano właściwości Name). Na przykład jeśli ustawisz wartość assetName do "Przykładowy", następnie właściwość OutputMediaAsset Name będzie miał ustawienie "Przykładowy". Jednak jeśli nie ustawiona wartość assetName, ale ustawiona na nazwę zadania, aby "NewJob", nazwa OutputMediaAsset będzie "_NewJob JobOutputAsset (wartość)".

    Poniższy przykład pokazuje, jak ustawić atrybutu assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Aby włączyć tworzenie łańcuchów zadań:

  * Zadanie musi mieć co najmniej dwa zadania
  * Musi istnieć co najmniej jedno zadanie, którego dane wejściowe są dane wyjściowe inne zadanie w ramach zadania.

Aby uzyskać więcej informacji, zobacz [Tworzenie zadania kodowania za pomocą interfejsu API REST usługi Media Services](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitoruj postęp przetwarzania
Można pobrać stanu zadania za pomocą właściwości stanu, jak pokazano w poniższym przykładzie:

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Anulowanie zadania
Usługa Media Services umożliwia anulowanie działające zadania za pomocą funkcji CancelJob. To wywołanie zwraca kod błędu 400, jeśli zostanie podjęta próba anulowania zadania, gdy jego stan jest anulowane, anulowanie, błąd lub zostało zakończone.

Poniższy przykład pokazuje sposób wywoływania CancelJob.

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Jeśli to się powiedzie, kod 204 odpowiedzi jest zwracany z treści wiadomości.

> [!NOTE]
> Należy zakodować adres URL zadania o identyfikatorze (zwykle nb:jid:UUID: wartość somevalue) podczas przekazywania go jako parametr do CancelJob.
>
>

### <a name="get-the-output-asset"></a>Pobieranie elementu zawartości wyjściowej
Poniższy kod pokazuje, jak żądania elementu zawartości wyjściowej identyfikatora.

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpowiedź HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Publikowanie elementu zawartości i uzyskiwanie przesyłania strumieniowego i pobierania progresywnego adresy URL przy użyciu interfejsu API REST

Aby przesłać strumieniowo lub pobrać element zawartości, należy go najpierw opublikować, tworząc lokalizator. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin używane do strumieniowego przesyłania plików multimedialnych (na przykład w formacie MPEG DASH, HLS i Smooth Streaming) oraz lokalizatory sygnatury dostępu współdzielonego (SAS) używane do pobierania plików multimedialnych. 

Po utworzeniu lokalizatorów można tworzyć adresy URL, które są używane do przesyłania strumieniowego lub pobierania plików.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Adres URL dla protokołu Smooth Streaming ma następujący format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Adres URL dla protokołu HLS ma następujący format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Adres URL dla protokołu MPEG DASH ma następujący format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Adres URL SAS używany do pobierania plików ma następujący format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

W tej sekcji pokazano, jak wykonywać następujące zadania niezbędne do "Publikuj" Twoje zasoby.  

* Tworzenie AccessPolicy z uprawnieniem do odczytu
* Tworzenie adresu URL sygnatury dostępu Współdzielonego dla pobierania zawartości
* Tworzenie źródłowy adres URL do strumieniowego przesyłania zawartości

### <a name="creating-the-accesspolicy-with-read-permission"></a>Tworzenie AccessPolicy z uprawnieniem do odczytu
Przed pobraniem pliku przesyłanie strumieniowe zawartości multimediów, zdefiniuj AccessPolicy z uprawnieniami do odczytu i Utwórz odpowiednie jednostki lokalizatora, który określa typ mechanizm dostarczania, który chcesz włączyć dla klientów. Aby uzyskać więcej informacji na temat dostępnych właściwości, zobacz [właściwości jednostki AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Poniższy przykład pokazuje, jak określić AccessPolicy dla uprawnień do odczytu dla danego elementu zawartości.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Jeśli to się powiedzie, zwracany jest kod 201 sukces, opisujące jednostkę AccessPolicy, który został utworzony. Następnie należy użyć identyfikatora AccessPolicy wraz z Identyfikator trwały zasób, który zawiera plik, którego chcesz dostarczyć (na przykład elementu zawartości wyjściowej), aby utworzyć jednostkę lokalizatora.

> [!NOTE]
> Ten podstawowy przepływ pracy jest taka sama jak przekazywania pliku podczas wprowadzania elementu zawartości (jak zostało omówione we wcześniejszej części tego tematu). Ponadto takie jak przekazywanie plików, jeśli użytkownik (lub klientów) muszą natychmiast uzyskać dostęp do plików, ustawić wartość StartTime do pięciu minut przed bieżącym czasem. Ta akcja jest konieczne, ponieważ może istnieć zegara niesymetryczność między klientem a Media Services. Wartość StartTime musi być w następującym formacie daty/godziny: RRRR-MM-ddtgg (na przykład "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Tworzenie adresu URL sygnatury dostępu Współdzielonego dla pobierania zawartości
Poniższy kod pokazuje, jak można pobrać adresu URL, który może służyć do pobrania plik multimedialny utworzony i przekazany wcześniej. AccessPolicy zapoznał zestaw uprawnień, a ścieżka lokalizatora odwołuje się do pobierania adresu URL sygnatury dostępu Współdzielonego.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Zwrócony **ścieżki** właściwość zawiera adres URL sygnatury dostępu Współdzielonego.

> [!NOTE]
> Jeśli pobierasz zawartość szyfrowany w magazynie, użytkownik musi ręcznie je odszyfrować przed renderowaniem go lub umożliwia MediaProcessor odszyfrowywania magazynu w ramach zadania przetwarzania danych wyjściowych przetworzone pliki bez zabezpieczeń na OutputAsset, a następnie Pobierz z tego zasobu. Aby uzyskać więcej informacji na temat przetwarzania Zobacz Tworzenie zadania kodowania za pomocą interfejsu API REST usługi Media Services. Ponadto nie można zaktualizować lokalizatorów adresu URL sygnatury dostępu Współdzielonego, po ich utworzeniu. Na przykład nie można ponownie użyć tego samego lokalizatora ze zaktualizowaną wartość StartTime. Jest to ze względu na sposób tworzenia adresów URL sygnatury dostępu Współdzielonego. Jeśli chcesz uzyskać dostęp do zasobu do pobrania, po Lokalizator wygasł, należy utworzyć nową grupę za pomocą nowego StartTime.
>
>

### <a name="download-files"></a>Pobieranie plików
Po utworzeniu AccessPolicy i lokalizatora zestawu, możesz pobrać pliki przy użyciu interfejsów API REST usługi Azure Storage.  

> [!NOTE]
> Należy dodać nazwę pliku dla pliku chcesz pobrać Lokalizator **ścieżki** wartość odebrana w poprzedniej sekcji. Na przykład: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Aby uzyskać więcej informacji na temat pracy z obiektami blob usługi Azure storage, zobacz [interfejsu API REST usługi Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

W wyniku zadania kodowania opisane wcześniej (kodowanie na zestaw MP4 o adaptacyjnej) masz plików MP4 z wieloma, które można pobrać progresywnie. Na przykład:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Tworzenie adresu URL przesyłania strumieniowego do strumieniowego przesyłania zawartości
Poniższy kod pokazuje, jak utworzyć Lokalizator przesyłania strumieniowego adresu URL:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Aby przesyłać strumieniowo Smooth Streaming źródłowy adres URL w odtwarzaczu przesyłania strumieniowego multimediów, można dołączyć ścieżkę właściwości o nazwie Smooth Streaming manifest pliku, a następnie "/ manifestu".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Aby przesyłanie strumieniowe HLS, Dołącz (format = m3u8-aapl) po "/ manifestu".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Do przesyłania strumieniowego MPEG DASH, Dołącz (format = mpd-time-csf) po "/ manifestu".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Odtwarzanie zawartości
Do przesyłania strumieniowego zawartości wideo użyj [odtwarzacza usługi Azure Media Services](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

Aby przetestować pobieranie progresywne, wklej adres URL do przeglądarki (na przykład programu Internet Explorer, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Następne kroki: Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
