---
title: Szyfrowanie zawartości za pomocą szyfrowania pamięci masowej przy użyciu interfejsu API AMS REST
description: Dowiedz się, jak szyfrować zawartość za pomocą szyfrowania magazynu przy użyciu interfejsów API AMS REST.
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
ms.openlocfilehash: 2a5ef1837375cc395a871f9a9860fa8bde572a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773597"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Szyfrowanie zawartości za pomocą szyfrowania pamięci masowej 

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > Do usługi Media Services w wersji 2 nie są dodawane żadne nowe funkcje ani funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)
>   

Zdecydowanie zaleca się szyfrowanie zawartości lokalnie przy użyciu szyfrowania AES-256 bit, a następnie przekazywanie jej do usługi Azure Storage, gdzie jest przechowywane w stanie spoczynku.

Ten artykuł zawiera omówienie szyfrowania magazynu AMS i pokazuje, jak przekazać zaszyfrowaną zawartość magazynu:

* Utwórz klucz zawartości.
* Utwórz zasób. Podczas tworzenia zasobu ustaw zawrostowość AssetCreationOption na StorageEncryption.
  
     Zaszyfrowane zasoby są skojarzone z kluczami zawartości.
* Połącz klucz zawartości z zasobem.  
* Ustaw parametry związane z szyfrowaniem na encjach AssetFile.

## <a name="considerations"></a>Zagadnienia do rozważenia 

Jeśli chcesz dostarczyć zaszyfrowany zasób magazynu, musisz skonfigurować zasady dostarczania zasobu. Przed przesyłaniem strumieniowego zasobu serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-rest-configure-asset-delivery-policy.md).

Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usług multimedialnych|Szyfrowanie AES-256, klucz zarządzany przez media services|Obsługiwane<sup>(1)</sup>|Nie obsługiwane<sup>(2)</sup>|
|[Szyfrowanie usługi przechowywania danych w spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> Chociaż usługi Media Services obsługują obsługę zawartości w postaci przejrzystej/bez szyfrowania, nie jest to zalecane.

<sup>2</sup> W programie Media Services w wersji 3 szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w celu zapewnienia zgodności z powrotem, gdy zasoby zostały utworzone za pomocą usługi Media Services w wersji 2. Oznacza to, że wersja 3 współpracuje z istniejącymi zaszyfrowanymi zasobami magazynu, ale nie pozwala na tworzenie nowych.

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

## <a name="storage-encryption-overview"></a>Omówienie szyfrowania magazynu
Szyfrowanie magazynu AMS stosuje szyfrowanie w trybie **AES-CTR** do całego pliku.  Tryb AES-CTR to szyfr blokowy, który może szyfrować dane o dowolnej długości bez konieczności dopełniania. Działa poprzez szyfrowanie bloku licznika za pomocą algorytmu AES, a następnie XOR-ing danych wyjściowych AES z danymi do szyfrowania lub odszyfrowywania.  Używany blok licznika jest konstruowany przez skopiowanie wartości InitializationVector do bajtów 0 do 7 wartości licznika i bajty 8 do 15 wartości licznika są ustawione na zero. Z 16-bajtowego bloku licznika bajty od 8 do 15 (czyli najmniej znaczące bajty) są używane jako prosta 64-bitowa niepodpisana liczba całkowita, która jest zwiększana o jeden dla każdego kolejnego bloku przetwarzanych danych i jest przechowywana w kolejności bajtów sieciowych. Jeśli ta liczba całkowita osiągnie wartość maksymalną (0xFFFFFFFFFFFFFFFFFFFFFFFFFFFF), a następnie zwiększa ją resetuje licznik bloku do zera (bajty 8 do 15) bez wpływu na pozostałe 64 bity licznika (czyli bajty od 0 do 7).   W celu utrzymania bezpieczeństwa szyfrowania w trybie AES-CTR wartość InitializationVector dla danego identyfikatora klucza dla każdego klucza zawartości jest unikatowa dla każdego pliku, a pliki mają długość mniejszą niż 2^64 bloki.  Ta unikatowa wartość ma na celu zapewnienie, że wartość licznika nigdy nie jest ponownie z danym kluczem. Aby uzyskać więcej informacji na temat trybu CTR, zobacz [tę stronę wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (artykuł wiki używa terminu "Nonce" zamiast "InitializationVector").

Szyfrowanie magazynu umożliwia **szyfrowanie** wyczyszczonych zawartości lokalnie przy użyciu szyfrowania AES-256 bit, a następnie przekazywanie jej do usługi Azure Storage, gdzie jest przechowywane w stanie spoczynku. Zasoby chronione szyfrowaniem magazynu są automatycznie niezaszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem i opcjonalnie ponownie szyfrowane przed przekazaniem z powrotem jako nowego zasobu wyjściowego. Podstawowym przypadkiem użycia szyfrowania magazynu jest, gdy chcesz zabezpieczyć wysokiej jakości pliki multimedialne wejściowe z silnym szyfrowaniem w spoczynku na dysku.

Aby dostarczyć zaszyfrowany zasób magazynu, należy skonfigurować zasady dostarczania zasobu, aby program Media Services wiedział, jak chcesz dostarczać zawartość. Przed przesyłaniem strumieniowego zasobu serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania (na przykład AES, szyfrowania wspólnego lub braku szyfrowania).

## <a name="create-contentkeys-used-for-encryption"></a>Tworzenie kluczy zawartości używanych do szyfrowania
Zaszyfrowane zasoby są skojarzone z kluczami szyfrowania magazynu. Utwórz klucz zawartości, który ma być używany do szyfrowania przed utworzeniem plików zasobów. W tej sekcji opisano sposób tworzenia klucza zawartości.

Poniżej przedstawiono ogólne kroki generowania kluczy zawartości skojarzonych z zasobami, które mają być szyfrowane. 

1. W przypadku szyfrowania magazynu losowo wygeneruj 32-bajtowy klucz AES. 
   
    32-bajtowy klucz AES jest kluczem zawartości zasobu, co oznacza, że wszystkie pliki skojarzone z tym zasobem muszą używać tego samego klucza zawartości podczas odszyfrowywania. 
2. Wywołanie [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) i [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metody, aby uzyskać poprawny certyfikat X.509, który musi być używany do szyfrowania klucza zawartości.
3. Zaszyfruj klucz zawartości za pomocą klucza publicznego certyfikatu X.509. 
   
   Media Services .NET SDK używa RSA z OAEP podczas wykonywania szyfrowania.  Przykład platformy .NET można zobaczyć w [funkcji EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Utwórz wartość sumy kontrolnej obliczoną przy użyciu identyfikatora klucza i klucza zawartości. Poniższy przykład .NET oblicza sumę kontrolną przy użyciu części GUID identyfikatora klucza i wyczyść klucz zawartości.

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

5. Utwórz klucz Zawartości z wartościami **EncryptedContentKey** (przekonwertowanym na ciąg zakodowany w bazie 64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**i **Suma kontrolna** otrzymana w poprzednich krokach.

    W przypadku szyfrowania magazynu w treści żądania należy uwzględnić następujące właściwości.

    Właściwość treści żądania    | Opis
    ---|---
    Identyfikator | Identyfikator ContentKey jest generowany przy użyciu następującego formatu "nb:kid:UUID:\<NEW GUID>".
    Typ klucza zawartości | Typ klucza zawartości jest całkowitej liczby, która definiuje klucz. W przypadku formatu szyfrowania magazynu wartość wynosi 1.
    EncryptedContentKey (Klucz szyfrowania) | Tworzymy nową wartość klucza zawartości, która jest wartością 256-bitową (32 bajtów). Klucz jest szyfrowany przy użyciu certyfikatu szyfrowania magazynu X.509, który pobieramy z usług Microsoft Azure Media Services, wykonując żądanie HTTP GET dla metod GetProtectionKeyId i GetProtectionKey. Na przykład zobacz następujący kod .NET: **EncryptSymmetricKeyData** metoda zdefiniowana [w tym miejscu](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    Identyfikator klucza ochrony | Jest to identyfikator klucza ochrony dla certyfikatu X.509 szyfrowania magazynu, który został użyty do zaszyfrowania naszego klucza zawartości.
    Typ klucza ochronnego | Jest to typ szyfrowania klucza ochrony, który został użyty do zaszyfrowania klucza zawartości. Ta wartość jest StorageEncryption(1) dla naszego przykładu.
    Suma kontrolna |Md5 obliczona suma kontrolna dla klucza zawartości. Jest obliczany przez szyfrowanie identyfikatora zawartości za pomocą klucza zawartości. Przykładowy kod pokazuje, jak obliczyć sumę kontrolną.


### <a name="retrieve-the-protectionkeyid"></a>Pobieranie identyfikatora ProtectionKeyId
W poniższym przykładzie pokazano, jak pobrać ProtectionKeyId, odcisk palca certyfikatu, dla certyfikatu, który należy użyć podczas szyfrowania klucza zawartości. Wykonaj ten krok, aby upewnić się, że masz już odpowiedni certyfikat na komputerze.

Żądanie:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Pobieranie klucza protectionkey dla identyfikatora ProtectionKeyId
W poniższym przykładzie pokazano, jak pobrać certyfikat X.509 przy użyciu identyfikatora ProtectionKeyId otrzymanego w poprzednim kroku.

Żądanie:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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
Po pobraniu certyfikatu X.509 i użyciu jego klucza publicznego do szyfrowania klucza zawartości, należy utworzyć encję **ContentKey** i odpowiednio ustawić jego wartości właściwości.

Jedną z wartości, które należy ustawić podczas tworzenia klucza zawartości jest typ. Podczas korzystania z szyfrowania magazynu wartość powinna być ustawiona na "1". 

W poniższym przykładzie pokazano, jak utworzyć **ContentKey** z **contentkeytype** zestaw szyfrowania magazynu ("1") i **ProtectionKeyType** ustawiona na "0", aby wskazać, że identyfikator klucza ochrony jest odcisk palca certyfikatu X.509.  

Żądanie

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

## <a name="create-an-asset"></a>Tworzenie zasobu
W poniższym przykładzie pokazano, jak utworzyć zasób.

**Żądanie HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Odpowiedź HTTP**

W przypadku powodzenia zwracana jest następująca odpowiedź:

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

## <a name="associate-the-contentkey-with-an-asset"></a>Kojarzenie contentkeya z zasobem
Po utworzeniu ContentKey skojarzyć go z zasobem przy użyciu operacji $links, jak pokazano w poniższym przykładzie:

Żądanie:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Odpowiedź:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Tworzenie pliku zasobów
[Encja AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) reprezentuje plik wideo lub audio przechowywany w kontenerze obiektów blob. Plik zasobu jest zawsze skojarzony z zasobem, a zasób może zawierać jeden lub wiele plików zasobów. Zadanie kodera usługi Media Services kończy się niepowodzeniem, jeśli obiekt pliku zasobu nie jest skojarzony z plikiem cyfrowym w kontenerze obiektów blob.

**AssetFile wystąpienie** i rzeczywisty plik multimedialny są dwa odrębne obiekty. AssetFile wystąpienie zawiera metadane dotyczące pliku multimedialnego, podczas gdy plik multimedialny zawiera rzeczywistą zawartość multimedialną.

Po przekazaniu pliku multimediów cyfrowych do kontenera obiektów blob użyjesz żądania **MERGE** HTTP, aby zaktualizować plik AssetFile z informacjami o pliku multimedialnym (nie pokazano w tym artykule). 

**Żądanie HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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
