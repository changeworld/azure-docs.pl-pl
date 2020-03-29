---
title: Wprowadzenie do dostarczania zawartości na żądanie przy użyciu REST | Dokumenty firmy Microsoft
description: W tym samouczku otrzymasz od kroków implementowania aplikacji dostarczania zawartości na żądanie za pomocą usługi Azure Media Services przy użyciu interfejsu API REST.
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
ms.openlocfilehash: 8989acc6d21a3c53be9d97c74ed7fbf03ba54819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773680"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Wprowadzenie do dostarczania zawartości na żądanie przy użyciu restu  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Ten przewodnik Szybki start przeprowadzi Cię przez kroki implementacji aplikacji dostarczania zawartości wideo na żądanie (VoD) przy użyciu interfejsów API REST usługi Azure Media Services (AMS).

Samouczek przedstawia podstawowy przepływ pracy usług Media Services oraz najczęściej występujące obiekty i zadania programowania wymagane w celu projektowania usług Media Services. Po zakończeniu samouczka można przesyłać strumieniowo lub stopniowo pobierać przykładowy plik multimedialny, który został przesłany, zakodowany i pobrany.

Na poniższym obrazie przedstawiono niektóre z najczęściej używanych obiektów podczas tworzenia aplikacji VoD w modelu Media Services OData.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Wymagania wstępne
Następujące wymagania wstępne są wymagane do rozpoczęcia tworzenia z usługą Media Services za pomocą interfejsów API REST.

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* Zrozumienie sposobu tworzenia za pomocą interfejsu API REST usługi Media Services. Aby uzyskać więcej informacji, zobacz [omówienie interfejsu API rest usługi Media Services](media-services-rest-how-to-use.md).
* Wybranej aplikacji, która może wysyłać żądania i odpowiedzi HTTP. Ten poradnik używa [Fiddler](https://www.telerik.com/download/fiddler).

W tym przewodniku Szybki start przedstawiono następujące zadania.

1. Uruchamianie punktów końcowych przesyłania strumieniowego (przy użyciu witryny Azure Portal).
2. Połącz się z kontem usługi Media Services za pomocą interfejsu REST API.
3. Utwórz nowy zasób i przekaż plik wideo za pomocą interfejsu API REST.
4. Zakoduj plik źródłowy do zestawu adaptacyjnych plików MP4 o adaptacyjnej szybkości transmisji bitów za pomocą interfejsu API REST.
5. Publikuj zasób i uzyskaj strumieniowe i progresywne adresy URL pobierania za pomocą interfejsu API REST.
6. Odtwarzanie zawartości.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni / uprawnień dostępu, na przykład zasad dla lokalizatorów, które mają pozostać w miejscu przez długi czas (zasady nieprześledania). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Aby uzyskać szczegółowe informacje na temat encji AMS REST używanych w tym artykule, zobacz [odwołanie interfejsu API REST usługi Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Zobacz też [pojęcia dotyczące usługi Azure Media Services](media-services-concepts.md).

>[!NOTE]
>Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Uruchamianie punktów końcowych przesyłania strumieniowego przy użyciu witryny Azure Portal

Podczas pracy z usługą Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie wideo za pośrednictwem adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów. Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać kodowaną zawartość plików MP4 z adaptacyjną szybkością transmisji bitów w formatach przesyłania strumieniowego obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) w odpowiednim czasie bez konieczności przechowywania wersji wstępnie utworzonych pakietów poszczególnych formatów przesyłania strumieniowego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Aby uruchomić punkt końcowy przesyłania strumieniowego, wykonaj następujące czynności:

1. Zaloguj się w [witrynie Azure portal](https://portal.azure.com/).
2. W oknie Ustawienia kliknij pozycję Punkty końcowe przesyłania strumieniowego.
3. Kliknij domyślny punkt końcowy przesyłania strumieniowego.

    Zostanie wyświetlone okno SZCZEGÓŁY DOMYŚLNEGO PUNKTU KOŃCOWEGO PRZESYŁANIA STRUMIENIOWEGO.

4. Kliknij ikonę Uruchom.
5. Kliknij przycisk Zapisz, aby zapisać zmiany.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Łączenie się z kontem usługi Media Services za pomocą interfejsu REST API

Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Tworzenie nowego zasobu i przekazywanie pliku wideo za pomocą interfejsu REST API

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. Encja **Zasobów** może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki podpisów kodowanych (oraz metadane dotyczące tych plików).  Po przesłaniu plików do zasobu zawartość jest bezpiecznie przechowywana w chmurze w celu dalszego przetwarzania i przesyłania strumieniowego.

Jedną z wartości, które należy podać podczas tworzenia zasobu, są opcje tworzenia zasobów. **Właściwość Options** jest wartością wyliczenia opisującą opcje szyfrowania, za pomocą których można utworzyć zasób. Prawidłowa wartość jest jedną z wartości z poniższej listy, a nie kombinacją wartości z tej listy:

* **Brak** = **0** — nie jest używane szyfrowanie. Podczas korzystania z tej opcji zawartość nie jest chroniona podczas przesyłania lub przechowywania w magazynie.
    Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji.
* **StorageEncrypted** = **1** — szyfruje wyczyść zawartość lokalnie przy użyciu szyfrowania AES-256 bit, a następnie przekazuje ją do usługi Azure Storage, gdzie jest przechowywany zaszyfrowany w spoczynku. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
* **CommonEncryptionProtected** = **2** - Użyj tej opcji, jeśli przesyłasz zawartość, która została już zaszyfrowana i chroniona za pomocą wspólnego szyfrowania lub PlayReady DRM (na przykład Smooth Streaming chronione PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** — użyj tej opcji, jeśli przesyłasz HLS zaszyfrowane za pomocą AES. Pliki muszą być zakodowane i zaszyfrowane przez Menedżera transformacji.

### <a name="create-an-asset"></a>Tworzenie zasobu
Zasób jest kontenerem dla wielu typów lub zestawów obiektów w usłudze Media Services, w tym wideo, audio, obrazów, kolekcji miniatur, ścieżek tekstowych i plików podpisów kodowych. W interfejsie API REST tworzenie zasobu wymaga wysłania żądania POST do usługi Media Services i umieszczenia wszelkich informacji o właściwościach zasobu w treści żądania.

W poniższym przykładzie pokazano, jak utworzyć zasób.

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Odpowiedź HTTP**

W przypadku powodzenia zwracane są następujące elementy:

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

### <a name="create-an-assetfile"></a>Tworzenie pliku zasobów
[Encja AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) reprezentuje plik wideo lub audio przechowywany w kontenerze obiektów blob. Plik zasobu jest zawsze skojarzony z zasobem, a zasób może zawierać jeden lub wiele plików assetfiles. Zadanie kodera usługi Media Services kończy się niepowodzeniem, jeśli obiekt pliku zasobu nie jest skojarzony z plikiem cyfrowym w kontenerze obiektów blob.

Po przekazaniu pliku multimediów cyfrowych do kontenera obiektów blob żądanie **KORESPONDENCJI SERYJNEJ** HTTP służy do aktualizowania pliku AssetFile z informacjami o pliku multimedialnym (jak pokazano w dalszej części tematu).

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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


### <a name="creating-the-accesspolicy-with-write-permission"></a>Tworzenie polityki dostępu z uprawnieniami do zapisu
Przed przekazaniem plików do magazynu obiektów blob ustaw prawa zasad dostępu do zapisu do zasobu. Aby to zrobić, opublikuj żądanie HTTP do zestawu encji AccessPolicies. Zdefiniuj wartość DurationInMinutes podczas tworzenia lub otrzymasz komunikat o błędzie 500 wewnętrzny serwer z powrotem w odpowiedzi. Aby uzyskać więcej informacji na temat AccessPolicies, zobacz [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

W poniższym przykładzie pokazano, jak utworzyć AccessPolicy:

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Odpowiedź HTTP**

W przypadku powodzenia zwracana jest następująca odpowiedź:

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

### <a name="get-the-upload-url"></a>Pobierz adres URL przekazywania

Aby otrzymać rzeczywisty adres URL przekazywania, utwórz lokalizator sygnatury dostępu Współdzielonego. Lokalizatory definiują godzinę rozpoczęcia i typ punktu końcowego połączenia dla klientów, którzy chcą uzyskać dostęp do plików w zasobie. Można utworzyć wiele jednostek lokalizatora dla danej pary AccessPolicy i Asset do obsługi różnych żądań i potrzeb klientów. Każdy z tych lokalizatorów używa Wartości StartTime plus DurationInMinutes wartość AccessPolicy do określenia czasu adres URL może być używany. Aby uzyskać więcej informacji, zobacz [Lokalizator](https://docs.microsoft.com/rest/api/media/operations/locator).

Adres URL sygnatury dostępu Współdzielonego ma następujący format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Zagadnienia do rozważenia:

* Nie można jednocześnie mieć więcej niż pięciu unikatowych lokalizatorów skojarzonych z danym zasobem. 
* Jeśli musisz natychmiast przekazać pliki, należy ustawić wartość StartTime na pięć minut przed bieżącym czasem. Jest tak, ponieważ może istnieć pochylenie zegara między komputerem klienckim a usługą Media Services. Ponadto wartość StartTime musi być w następującym formacie DateTime: YYYY-MM-DDTHH:mm:ssZ (na przykład "2014-05-23T17:53:50Z").    
* Może wystąpić opóźnienie 30-40 sekund po utworzeniu lokalizatora, gdy jest on dostępny do użycia. Ten problem dotyczy zarówno [adresów URL sygnatury dostępu Współdzielonego,](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) jak i lokalizatorów pochodzenia.

W poniższym przykładzie pokazano, jak utworzyć lokalizator adresów URL sygnatury dostępu Współdzielonego, zgodnie z definicją przez Właściwość Type w treści żądania ("1" dla lokalizatora sygnatury dostępu Współdzielonego i "2" dla lokalizatora pochodzenia na żądanie). Właściwość **Path** zwracana zawiera adres URL, którego należy użyć do przekazania pliku.

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Odpowiedź HTTP**

W przypadku powodzenia zwracana jest następująca odpowiedź:

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

### <a name="upload-a-file-into-a-blob-storage-container"></a>Przekazywanie pliku do kontenera magazynu obiektów blob
Po ustawieniu accesspolicy i lokalizatora rzeczywisty plik jest przekazytywał do kontenera magazynu obiektów blob platformy Azure przy użyciu interfejsów API REST usługi Azure Storage. Należy przekazać pliki jako blokowe obiekty blob. Obiekty BLOB strony nie są obsługiwane przez usługę Azure Media Services.  

> [!NOTE]
> Należy dodać nazwę pliku, który chcesz przekazać, do wartości **Ścieżka** lokalizatora otrzymanej w poprzedniej sekcji. Na przykład `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Aby uzyskać więcej informacji na temat pracy z obiektami blob magazynu platformy Azure, zobacz [Interfejs API REST usługi obiektów Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Aktualizowanie pliku zasobów
Po przesłaniu pliku zaktualizuj informacje o rozmiarze (i innych) rozmiarze zestawu plików. Przykład:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Odpowiedź HTTP**

W przypadku powodzenia zwracane są następujące elementy:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Usuwanie lokalizatora i dostępuPolicy
**Żądanie HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**Odpowiedź HTTP**

W przypadku powodzenia zwracane są następujące elementy:

    HTTP/1.1 204 No Content
    ...

**Żądanie HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

**Odpowiedź HTTP**

W przypadku powodzenia zwracane są następujące elementy:

    HTTP/1.1 204 No Content
    ...

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów

Po spożyciu zasobów do usługi Media Services nośniki mogą być kodowane, transmuxed, znak wodny i tak dalej, zanim zostaną dostarczone do klientów. Te działania są zaplanowane i uruchamiane w wielu wystąpieniach ról w tle, aby zapewnić wysoką wydajność oraz dostępność. Działania te są nazywane zadania i każde zadanie składa się z zadań atomowych, które wykonują rzeczywistą pracę w pliku zasobu (aby uzyskać więcej informacji, zobacz [Zadania](https://docs.microsoft.com/rest/api/media/operations/job), Opisy [zadań).](https://docs.microsoft.com/rest/api/media/operations/task)

Jak wspomniano wcześniej, podczas pracy z usługą Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów do klientów. Usługi Media Services mogą dynamicznie pakować zestaw adaptacyjnych plików MP4 o adaptacyjnej szybkości transmisji bitów w jeden z następujących formatów: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

W poniższej sekcji pokazano, jak utworzyć zadanie, które zawiera jedno zadanie kodowania. Zadanie określa transkodowanie pliku antresoli do zestawu adaptacyjnych plików MP4 o adaptacyjnej szybkości transmisji bitów przy użyciu **programu Media Encoder Standard**. W sekcji pokazano również, jak monitorować postęp przetwarzania zadań. Po zakończeniu zadania można utworzyć lokalizatory, które są potrzebne do uzyskania dostępu do zasobów.

### <a name="get-a-media-processor"></a>Pobierz procesor multimediów
W umywoiń programie Media Services procesor multimediów jest składnikiem obsługującym określone zadanie przetwarzania, takie jak kodowanie, konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. W przypadku zadania kodowania pokazanego w tym samouczku użyjemy standardu Kodera multimediów.

Poniższy kod żąda identyfikatora kodera.

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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
Każde zadanie może mieć jedno lub więcej zadań w zależności od typu przetwarzania, które chcesz wykonać. Za pośrednictwem interfejsu API REST można tworzyć zadania i związane z nimi zadania na jeden z dwóch sposobów: Zadania można zdefiniować w linii za pomocą właściwości Nawigacji zadania w encjach zadania lub za pośrednictwem przetwarzania wsadowego OData. SDK usługi Media Services używa przetwarzania wsadowego. Jednak dla czytelności przykładów kodu w tym artykule zadania są zdefiniowane w linii. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

W poniższym przykładzie pokazano, jak utworzyć i opublikować zadanie z jednym zestawem zadań do kodowania wideo w określonej rozdzielczości i jakości. Poniższa sekcja dokumentacji zawiera listę wszystkich [ustawień predefiniowanych zadań obsługiwanych](https://msdn.microsoft.com/library/mt269960) przez procesor Media Encoder Standard.  

**Żądanie HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

W przypadku powodzenia zwracana jest następująca odpowiedź:

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


Istnieje kilka ważnych rzeczy, na które należy zwrócić uwagę w każdym żądaniu zadania:

* Właściwości TaskBody MUSI używać dosłownego XML do definiowania liczby danych wejściowych lub zasobów wyjściowych, które są używane przez zadanie. Artykuł Zadanie zawiera definicję schematu XML dla pliku XML.
* W definicji TaskBody każda `<inputAsset>` wartość `<outputAsset>` wewnętrzna dla i musi być ustawiona jako JobInputAsset(value) lub JobOutputAsset(value).
* Zadanie może mieć wiele zasobów wyjściowych. Jeden zestaw JobOutputAsset(x) może być użyty tylko raz jako dane wyjściowe zadania w zadaniu.
* Zasób wejściowy zadania można określić jako zasób wejściowy zadania.
* Zadania nie mogą tworzyć cyklu.
* Parametr wartości przekazywalny do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu zasobu. Rzeczywiste zasoby są zdefiniowane w InputMediaAssets i OutputMediaAssets właściwości nawigacji w definicji encji zadania.

> [!NOTE]
> Ponieważ usługi Media Services jest zbudowany na OData v3, poszczególnych zasobów w InputMediaAssets i OutputMediaAssets kolekcji właściwości nawigacji odwołuje się do pary "__metadata : uri" nazwa-wartość pary.
>
>

* InputMediaAssets jest mapowane na jeden lub więcej zasobów utworzonych w udziale usługi Media Services. Zestawy OutputMediaAs są tworzone przez system. Nie odwołują się one do istniejącego zasobu.
* Zestawy OutputMediaAssets można nazwać przy użyciu atrybutu assetName. Jeśli ten atrybut nie jest obecny, a następnie nazwa OutputMediaAsset jest `<outputAsset>` niezależnie od wewnętrznej wartości tekstowej elementu jest z sufiksem albo wartość nazwa zadania lub wartość identyfikatora zadania (w przypadku, gdy Name właściwość nie jest zdefiniowana). Na przykład jeśli ustawisz wartość assetName na "Przykład", właściwość OutputMediaAsset Name zostanie ustawiona na "Przykład". Jeśli jednak nie ustawiono wartości dla assetName, ale ustawiono nazwę zadania na "NewJob", a następnie OutputMediaAsset Nazwa będzie "JobOutputAsset(value)_NewJob".

    W poniższym przykładzie pokazano, jak ustawić atrybut assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Aby włączyć tworzenie łańcucha zadań:

  * Zadanie musi mieć co najmniej dwa zadania
  * Musi istnieć co najmniej jedno zadanie, którego dane wejściowe jest dane wyjściowe innego zadania w zadaniu.

Aby uzyskać więcej informacji, zobacz [Tworzenie zadania kodowania za pomocą interfejsu API REST usługi Media Services](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitorowanie postępu przetwarzania
Stan zadania można pobrać za pomocą właściwości State, jak pokazano w poniższym przykładzie:

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Odpowiedź HTTP**

W przypadku powodzenia zwracana jest następująca odpowiedź:

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
Usługi Media Services umożliwia anulowanie uruchamiania zadań za pomocą funkcji CancelJob. To wywołanie zwraca kod błędu 400, jeśli spróbujesz anulować zadanie, gdy jego stan zostanie anulowany, anuluje, błąd lub zakończone.

W poniższym przykładzie pokazano, jak wywołać CancelJob.

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Jeśli się powiedzie, kod odpowiedzi 204 jest zwracany bez treści wiadomości.

> [!NOTE]
> Identyfikator zadania należy zakodować w adresie URL (zwykle nb:jid:UUID: somevalue) podczas przekazywania go jako parametru canceljob.
>
>

### <a name="get-the-output-asset"></a>Pobierz zasób wyjściowy
Poniższy kod pokazuje, jak zażądać identyfikatora zasobu wyjściowego.

**Żądanie HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publikuj zasób i uzyskaj strumieniowe i progresywne adresy URL pobierania za pomocą interfejsu REST API

Aby przesłać strumieniowo lub pobrać element zawartości, należy go najpierw opublikować, tworząc lokalizator. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin używane do strumieniowego przesyłania plików multimedialnych (na przykład w formacie MPEG DASH, HLS i Smooth Streaming) oraz lokalizatory sygnatury dostępu współdzielonego (SAS) używane do pobierania plików multimedialnych. 

Po utworzeniu lokalizatorów można utworzyć adresy URL używane do przesyłania strumieniowego lub pobierania plików.

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

W tej sekcji pokazano, jak wykonać następujące zadania niezbędne do "opublikowania" zasobów.  

* Tworzenie polityki dostępu z uprawnieniami do odczytu
* Tworzenie adresu URL sygnatury dostępu Współdzielonego do pobierania zawartości
* Tworzenie początkowego adresu URL zawartości przesyłanej strumieniowo

### <a name="creating-the-accesspolicy-with-read-permission"></a>Tworzenie polityki dostępu z uprawnieniami do odczytu
Przed pobraniem lub przesyłaniem strumieniowym dowolnej zawartości multimedialnej należy najpierw zdefiniować aplikację AccessPolicy z uprawnieniami do odczytu i utworzyć odpowiednią encję lokalizatora, która określa typ mechanizmu dostarczania, który ma zostać włączyny dla klientów. Aby uzyskać więcej informacji na temat dostępnych właściwości, zobacz [Właściwości jednostki AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

W poniższym przykładzie pokazano, jak określić AccessPolicy dla uprawnień do odczytu dla danego zasobu.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Jeśli się powiedzie, zwracany jest kod sukcesu 201 opisujący utworzoną jednostkę AccessPolicy. Następnie należy użyć identyfikatora zasady dostępu wraz z identyfikatorem zasobu zawierającego plik, który chcesz dostarczyć (na przykład zasób wyjściowy), aby utworzyć encję Lokalizatora.

> [!NOTE]
> Ten podstawowy przepływ pracy jest taki sam jak przekazywanie pliku podczas pozyskiwania zasobu (jak zostało omówione wcześniej w tym temacie). Ponadto, na przykład przekazywanie plików, jeśli ty (lub twoi klienci) musisz natychmiast uzyskać dostęp do plików, ustaw wartość StartTime na pięć minut przed bieżącym czasem. Ta akcja jest konieczna, ponieważ może istnieć pochylenie zegara między klientem a usługą Media Services. Wartość StartTime musi być w następującym formacie DateTime: YYYY-MM-DDTHH:mm:ssZ (na przykład "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Tworzenie adresu URL sygnatury dostępu Współdzielonego do pobierania zawartości
Poniższy kod pokazuje, jak uzyskać adres URL, który może służyć do pobierania pliku multimedialnego utworzonego i przekazanego wcześniej. AccessPolicy ma ustawione uprawnienia do odczytu, a ścieżka lokalizatora odwołuje się do adresu URL pobierania sygnatury dostępu współdzielonego.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

W przypadku powodzenia zwracana jest następująca odpowiedź:

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

Właściwość zwracana **ścieżka** zawiera adres URL sygnatury dostępu Współdzielonego.

> [!NOTE]
> W przypadku pobierania zaszyfrowanej zawartości magazynu należy ręcznie odszyfrować ją przed jej renderowaniem lub użyć nośnika szyfrowania magazynu w zadaniu przetwarzania do wyprowadzenia przetworzonych plików w systemie clear do zestawu outputAsset, a następnie pobrać z tego zasobu. Aby uzyskać więcej informacji na temat przetwarzania, zobacz Tworzenie zadania kodowania za pomocą interfejsu API REST usługi Media Services. Ponadto lokalizatory adresów URL sygnatury dostępu Współdzielonego nie mogą być aktualizowane po ich utworzeniu. Na przykład nie można ponownie użyć tego samego lokalizatora ze zaktualizowaną wartością StartTime. Wynika to ze sposobu tworzenia adresów URL SYGNATURY DOSTĘPU Współdzielonego. Jeśli chcesz uzyskać dostęp do zasobu do pobrania po wygaśnięciu lokalizatora, należy utworzyć nowy z nowym StartTime.
>
>

### <a name="download-files"></a>Pobieranie plików
Po uzyskaniu zestawu AccessPolicy i Locator można pobrać pliki przy użyciu interfejsów API REST usługi Azure Storage.  

> [!NOTE]
> Należy dodać nazwę pliku, który chcesz pobrać, do wartości **Ścieżka** lokalizatora otrzymanej w poprzedniej sekcji. Na przykład: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Aby uzyskać więcej informacji na temat pracy z obiektami blob magazynu platformy Azure, zobacz [Interfejs API REST usługi obiektów Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

W wyniku zadania kodowania, które zostało wykonane wcześniej (kodowanie do adaptacyjnego zestawu MP4), masz wiele plików MP4, które można stopniowo pobierać. Przykład:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Tworzenie adresu URL przesyłania strumieniowego do przesyłania strumieniowego zawartości
Poniższy kod pokazuje, jak utworzyć lokalizator adresów URL przesyłania strumieniowego:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

W przypadku powodzenia zwracana jest następująca odpowiedź:

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

Aby przesyłać strumieniowo adres URL pochodzenia płynnego przesyłania strumieniowego w odtwarzaczu multimedialnym przesyłania strumieniowego, należy dołączyć właściwość Path o nazwie pliku manifestu Płynne przesyłanie strumieniowe, a następnie "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Aby przesyłać strumieniowo HLS, dołącz (format=m3u8-aapl) po "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Aby przesyłać strumieniowo MPEG DASH, dołącz (format=mpd-time-csf) po "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a name="play-your-content"></a><a id="play"></a>Odtwarzanie zawartości
Do przesyłania strumieniowego zawartości wideo użyj [odtwarzacza usługi Azure Media Services](https://aka.ms/azuremediaplayer).

Aby przetestować pobieranie progresywne, wklej adres URL do przeglądarki (np.

## <a name="next-steps-media-services-learning-paths"></a>Następne kroki: ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
