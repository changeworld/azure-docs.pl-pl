---
title: Szyfrowanie zawartości przy użyciu szyfrowania magazynu przy użyciu interfejsu API REST usługi AMS
description: Dowiedz się, jak szyfrowanie zawartości przy użyciu szyfrowania magazynu przy użyciu interfejsów API REST usługi AMS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 30ac6a94142c9b9d987fb3fd32b3483cc6dc130c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64867592"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Szyfrowanie zawartości przy użyciu szyfrowania magazynu 

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/).   > Żadnych nowych funkcji lub funkcje są dodawane do usługi Media Services v2. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)
>   

Zdecydowanie zaleca się szyfrowanie zawartości lokalnie przy użyciu algorytmu AES-256-bitowego, a następnie przekaż go do usługi Azure Storage gdzie są przechowywane, szyfrowane w stanie spoczynku.

Ten artykuł zawiera omówienie usługi AMS szyfrowania magazynu i pokazuje, jak przekazywać zawartość szyfrowany w magazynie:

* Utwórz klucz zawartości.
* Tworzenie zasobu. Ustaw AssetCreationOption StorageEncryption podczas tworzenia elementu zawartości.
  
     Zaszyfrowane zasoby są skojarzone z kluczy zawartości.
* Klucz zawartości można połączyć elementu zawartości.  
* Ustaw parametry związanych z szyfrowaniem na jednostkach AssetFile.

## <a name="considerations"></a>Zagadnienia do rozważenia 

Jeśli użytkownik chce dostarczać zaszyfrowane trwałego magazynu, należy skonfigurować zasady dostarczania elementu zawartości. Zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu zasad dostarczania określony. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services|AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> a Media Services obsługuje obsługi zawartości, bez zabezpieczeń/bez jakiejkolwiek formy szyfrowania, to nie jest to zalecane.

<sup>2</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Omówienie szyfrowania usługi Storage
Zastosowanie szyfrowania magazynu usługi AMS **Ewidencyjne AES** tryb szyfrowania do całego pliku.  Tryb Ewidencyjne AES jest szyfrowania bloku, który szyfruje dowolnej długości danych bez konieczności dopełnienia. Działa przez szyfrowanie przy użyciu algorytmu AES, a następnie umieszczanie XOR dane wyjściowe AES z danymi można zaszyfrować lub odszyfrować bloku licznika.  Blok licznika, używany jest tworzony przez skopiowanie wartości InitializationVector bajtów 0 do 7 wartość licznika i bajtów 8 do 15 wartości licznika są ustawiane na zero. Bloku 16-bajtowy licznik bajtów 8 do 15 (oznacza to, że najmniej znaczące bajtów) są używane podczas przetwarzania proste 64-bitowa liczba całkowita bez znaku jest zwiększany o jeden dla każdego kolejnych bloków danych i są przechowywane w kolejności bajtów w sieci. Jeśli ta liczba całkowita osiągnie maksymalną wartość (0xFFFFFFFFFFFFFFFF), jego inkrementacją Resetuje licznik bloku (w bajtach 8 do 15) bez wpływu na 64 bity licznika (oznacza to, w bajtach 0-7).   Aby zachować bezpieczeństwo kont AES szyfrowania trybu, wartość InitializationVector dla danego identyfikatora klucza dla każdego klucza zawartości jest unikatowy dla każdego pliku i plików jest mniejsza niż 2 ^ 64 bloki o długości.  Ta unikatowa wartość jest zapewnienie, że wartość licznika nigdy nie zostanie ponownie użyty z danym kluczem. Aby uzyskać więcej informacji na temat trybu kont Zobacz [tej strony typu wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (artykuł w witrynie wiki używany jest termin "Identyfikator jednorazowy" zamiast "InitializationVector").

Użyj **szyfrowania magazynu** do zaszyfrowania zawartości lokalnie przy użyciu algorytmu AES-256-bitowego szyfrowania, a następnie przekaż go do usługi Azure Storage gdzie są przechowywane, szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Głównym zastosowaniem szyfrowania magazynu jest, gdy chcesz zabezpieczyć swoje wysokiej jakości multimedialnych plików wejściowych za pomocą silnego szyfrowania magazynowanych na dysku.

W celu dostarczania elementu zawartości zaszyfrowanej magazynu, należy skonfigurować zasady dostarczania elementu zawartości, Media Services wie, jak chcesz dostarczanie zawartości. Zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość za pomocą zasad określony dostarczania (na przykład, AES, stosowanie szyfrowania common encryption lub bez szyfrowania).

## <a name="create-contentkeys-used-for-encryption"></a>Tworzenie kluczy zawartości jest używany do szyfrowania
Zaszyfrowane zasoby są skojarzone z kluczy szyfrowania magazynu. Utwórz klucz zawartości ma być używany do szyfrowania przed utworzeniem plików zasobów. W tej sekcji opisano sposób tworzenia klucza zawartości.

Poniżej przedstawiono ogólne kroki do generowania kluczy zawartości, które skojarzysz z zasobami, które mają być szyfrowane. 

1. Szyfrowanie magazynu losowego generowania 32-bajtowy klucz szyfrowania AES. 
   
    32-bajtowy klucz szyfrowania AES jest klucz zawartości dla swojego elementu zawartości, co oznacza wszystkie pliki skojarzone z tego konieczność użycia tego samego klucza zawartości podczas odszyfrowywania zawartości. 
2. Wywołaj [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) i [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metody, aby uzyskać prawidłowy certyfikat X.509 używany do szyfrowania klucza zawartości.
3. Szyfrowanie klucza zawartości przy użyciu klucza publicznego certyfikatu X.509. 
   
   Media Services .NET SDK używa algorytmu RSA z OAEP podczas wykonywania szyfrowania.  Widać przykład .NET, w [funkcja EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Utwórz wartość sumy kontrolnej obliczane przy użyciu klucza identyfikatora i klucza zawartości. W poniższym przykładzie .NET oblicza sumę kontrolną, używając identyfikatora GUID części kluczowych identyfikator i klucz czyszczenia zawartości.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Utwórz klucz zawartości za pomocą **EncryptedContentKey** (konwertowane na ciąg kodowany w formacie base64) **ProtectionKeyId**, **ProtectionKeyType**,  **ContentKeyType**, i **sumy kontrolnej** wartości otrzymane w poprzednich krokach.

    Dla celów szyfrowania magazynu następujące właściwości powinny być objęte treści żądania.

    Właściwości treści żądania    | Opis
    ---|---
    Identyfikator | Identyfikator ContentKey jest generowany przy użyciu następującego formatu "nb:kid:UUID:\<nowy identyfikator GUID >".
    ContentKeyType | Typ klucza zawartości jest liczba całkowita, która definiuje klucz. Dla formatu szyfrowania magazynu wartość wynosi 1.
    EncryptedContentKey | Utworzymy nową wartość klucza zawartości, która jest wartością 256-bitowego (32-bajtów). Klucz jest zaszyfrowany przy użyciu certyfikatu X.509 szyfrowania magazynu, który możemy pobrać z usługi Microsoft Azure Media Services, wykonując żądanie HTTP GET dla GetProtectionKeyId i metod GetProtectionKey. Na przykład zobacz następujący kod .NET: **EncryptSymmetricKeyData** metody zdefiniowanej [tutaj](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Jest to ochrona identyfikator klucza dla certyfikatu X.509 szyfrowania magazynu, który został użyty do zaszyfrowania naszych klucza zawartości.
    ProtectionKeyType | Jest to typ szyfrowania do ochrony klucza, który został użyty do szyfrowania klucza zawartości. Ta wartość jest StorageEncryption(1) w naszym przykładzie.
    Sumy kontrolnej |Obliczony sumy kontrolnej MD5 dla klucza zawartości. Jest ona obliczana przez szyfrowania identyfikator zawartości przy użyciu klucza zawartości. Przykładowy kod przedstawia sposób obliczania sumy kontrolnej.


### <a name="retrieve-the-protectionkeyid"></a>Pobieranie ProtectionKeyId
Poniższy przykład pokazuje, jak pobrać ProtectionKeyId, odcisk palca certyfikatu, aby uzyskać certyfikat którego należy użyć podczas szyfrowania klucza zawartości. Wykonaj ten krok, aby upewnić się, czy już masz odpowiedni certyfikat na komputerze.

Żądanie:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

Odpowiedź:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Pobieranie ProtectionKey dla ProtectionKeyId
Poniższy przykład pokazuje, jak można pobrać certyfikatu X.509 przy użyciu ProtectionKeyId otrzymany w poprzednim kroku.

Żądanie:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Odpowiedź:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Tworzenie klucza zawartości
Po pobrać certyfikat X.509 i użyć swojego klucza publicznego do szyfrowania klucza zawartości należy utworzyć **ContentKey** jednostki i ustaw odpowiednie wartości jej właściwości.

Jedna z wartości, należy ustawić podczas tworzenia zawartości klucza jest typem. Korzystając z szyfrowania magazynu, wartość powinna być równa "1". 

Poniższy przykład pokazuje, jak utworzyć **ContentKey** z **ContentKeyType** Ustaw szyfrowania magazynu ("1") i **ProtectionKeyType** równa "0", aby wskazać, że Klucz ochrony identyfikator jest odcisk palca certyfikatu X.509.  

Żądanie

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Utworzenie elementu zawartości
Poniższy przykład pokazuje, jak utworzyć element zawartości.

**Żądanie HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Odpowiedź HTTP**

Jeśli to się powiedzie, jest zwracany następującą odpowiedź:

    HTP/1.1 201 Created
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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>Skojarz ContentKey z elementem zawartości
Po utworzeniu ContentKey, skojarzyć ją z elementów zawartości przy użyciu operacji $links, jak pokazano w poniższym przykładzie:

Żądanie:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Odpowiedź:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Utwórz AssetFile
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) jednostki reprezentuje plik wideo lub audio, który jest przechowywany w kontenerze obiektów blob. Plik zasobów jest zawsze skojarzone z elementem zawartości i zasobów może zawierać jeden lub wiele plików zasobów. Zadanie Media Services Encoder kończy się niepowodzeniem, jeśli obiekt pliku zasobów nie jest skojarzony z plikiem cyfrowych w kontenerze obiektów blob.

**AssetFile** wystąpienia i plik multimedialna znajdują się dwa różne obiekty. Wystąpienie AssetFile zawiera metadane dotyczące plików multimedialnych, a plik multimedialny zawiera zawartość multimedialna.

Po przekazaniu pliku multimediów cyfrowych do kontenera obiektów blob, której użyjesz **scalania** żądania HTTP, aby zaktualizować AssetFile przy użyciu informacji o pliku multimedialnego (nie pokazane w tym artykule). 

**Żądanie HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
