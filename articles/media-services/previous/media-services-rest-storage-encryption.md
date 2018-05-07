---
title: "Szyfrowanie zawartości przy użyciu szyfrowania magazynu przy użyciu interfejsu API REST usług AMS"
description: "Dowiedz się, jak szyfrowanie zawartości przy użyciu szyfrowania magazynu przy użyciu interfejsów API REST usługi AMS."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 2fd4c91a8151067c0e9cc9000c158e48cb2cd8a5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Szyfrowanie zawartości przy użyciu szyfrowania magazynu

Zdecydowanie zaleca się do szyfrowania treści lokalnie przy użyciu standardu AES 256-bitowego i przekaż go do magazynu Azure gdzie jest przechowywana szyfrowane, gdy.

Ten artykuł zawiera omówienie usług AMS szyfrowanie magazynu i pokazuje, jak przekazywać zawartość szyfrowany w magazynie:

* Utwórz klucz zawartości.
* Utwórz zasób. Ustaw AssetCreationOption StorageEncryption podczas tworzenia zasobu.
  
     Zaszyfrowane zasoby są skojarzone z kluczy zawartości.
* Połącz klucz zawartości do elementu zawartości.  
* Ustaw parametry związane z szyfrowaniem na jednostkach AssetFile.

## <a name="considerations"></a>Zagadnienia do rozważenia 

Jeśli chcesz dostarczyć zasób zaszyfrowanych magazynu, należy skonfigurować zasady dostarczania elementu zawartości. Przed zawartości mogą być przesyłane strumieniowo, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieni zawartości przy użyciu zasady dostarczania określony. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-rest-configure-asset-delivery-policy.md).

Podczas uzyskiwania dostępu do obiektów w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md). 

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsu API usług AMS, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Omówienie szyfrowania magazynu
Zastosowanie szyfrowania magazynu AMS **Ewidencyjne AES** tryb szyfrowanie całego pliku.  Tryb Ewidencyjne AES jest szyfry blokowe, który można zaszyfrować dane o dowolnej długości bez potrzeby dopełnienia. Działa on tak szyfrując z algorytmu AES, a następnie używać XOR dane wyjściowe AES z danymi można zaszyfrować lub odszyfrować bloku licznika.  Blok licznika używany jest tworzony przez skopiowanie wartości InitializationVector bajtów 0 do 7 wartość licznika i bajtów 8 do 15 wartość licznika jest ustawiana wartość zero. Bloku 16-bajtowych licznik bajtów 8 do 15 (to znaczy najmniej znaczący bajtów) są używane jako proste 64-bitowych unsigned liczba całkowita, która jest zwiększany o jeden dla każdego kolejnych bloków danych przetwarzane i jest przechowywany w sieci kolejności bajtów. Jeśli ta liczba całkowita osiągnie wartość maksymalna (0xFFFFFFFFFFFFFFFF), następnie zwiększenie jego Resetuje licznik bloku (w bajtach 8 do 15) bez wpływu na inne 64-bitowy licznika (to znaczy bajtów 0-7).   Aby zachować bezpieczeństwo szyfrowania tryb Ewidencyjne AES, wartość InitializationVector danym identyfikatorem klucza dla każdego klucza zawartości jest unikatowy dla każdego pliku i plików jest mniejsza niż wartość 2 ^ 64 bloki o długości.  Jest to unikatowa wartość aby upewnić się, że wartość licznika nigdy nie jest ponownie z danym kluczem. Aby uzyskać więcej informacji o trybie kont Zobacz [tej strony typu wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (Artykuł typu wiki używany jest termin "Nonce" zamiast "InitializationVector").

Użyj **szyfrowanie magazynu** do zaszyfrowania zawartości lokalnie przy użyciu standardu AES 256-bitowy szyfrowania i przekaż go do magazynu Azure gdzie jest przechowywana szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a następnie opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest, gdy chcesz zabezpieczyć Twoje wysokiej jakości multimedialnych plików wejściowych z silne szyfrowanie przechowywanych na dysku.

Aby dostarczyć zasób zaszyfrowanych magazynu, należy skonfigurować zasady dostarczania elementu zawartości będzie wówczas traktował Media Services sposób dostarczania zawartości. Przed zawartości mogą być przesyłane strumieniowo, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieni zawartości za pomocą zasad określonym dostarczania (na przykład AES, wspólnego szyfrowania lub bez szyfrowania).

## <a name="create-contentkeys-used-for-encryption"></a>Utwórz ContentKeys używany do szyfrowania
Zaszyfrowane zasoby są skojarzone z kluczy szyfrowania magazynu. Utwórz klucz zawartości do użycia dla szyfrowania przed utworzeniem plików zasobów. Ta sekcja zawiera opis sposobu tworzenia klucza zawartości.

Poniżej przedstawiono ogólne kroki podczas generowania zawartości kluczy, które skojarzysz z zasobów, które mają być szyfrowane. 

1. Do szyfrowania magazynu losowego generowania klucza AES 32 bajtów. 
   
    32-bajtowych klucz AES jest zawartości dla zawartości, co oznacza wszystkie pliki skojarzone z którym zasobów musi używać tego samego klucza zawartości podczas odszyfrowywania. 
2. Wywołanie [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) i [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metod, aby uzyskać poprawny certyfikat X.509 używany do szyfrowania klucza zawartości.
3. Zaszyfrowanie klucza zawartości z kluczem publicznym certyfikatu X.509. 
   
   .NET SDK usługi Media Services używa algorytmu RSA z OAEP podczas operacji szyfrowania.  Widoczny jest przykład .NET w [funkcja EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Utwórz wartość sumy kontrolnej obliczane przy użyciu klucza identyfikator i klucz zawartości. W poniższym przykładzie .NET oblicza sumę kontrolną, używając identyfikatora GUID części klucza identyfikator i klucz czyszczenie zawartości.

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

5. Utwórz klucz zawartości z **EncryptedContentKey** (konwertowana na ciąg kodowany w formacie base64), **ProtectionKeyId**, **ProtectionKeyType**,  **ContentKeyType**, i **sumy kontrolnej** wartości otrzymany w poprzednich krokach.

    Do szyfrowania magazynu powinien znajdować się następujące właściwości, w treści żądania.

    Właściwość treść żądania    | Opis
    ---|---
    Identyfikator | Identyfikator ContentKey jest generowany przy użyciu następującego formatu "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | Typ klucza zawartości jest liczba całkowita, która definiuje klucz. Format szyfrowania magazynu wartość to 1.
    EncryptedContentKey | Możemy utworzyć nowego klucza wartość zawartości, która jest wartością 256-bitowego (32 bajtów). Klucz jest zaszyfrowany przy użyciu certyfikatu X.509 szyfrowania magazynu, który pobrać z usługi Microsoft Azure Media Services, wykonując żądanie HTTP GET dla GetProtectionKeyId i GetProtectionKey metod. Na przykład zobacz następujący kod .NET: **EncryptSymmetricKeyData** metody zdefiniowanej [tutaj](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | To jest identyfikator klucza ochrony dla magazynu certyfikatu X.509 szyfrowania, który został użyty do zaszyfrowania naszych klucz zawartości.
    ProtectionKeyType | Jest to typ szyfrowania dla klucza ochrony, który był używany do szyfrowania klucza zawartości. Ta wartość jest StorageEncryption(1) w naszym przykładzie.
    Sumy kontrolnej |Obliczony sumy kontrolnej MD5 dla klucza zawartości. Jest ona obliczana przez szyfrowanie identyfikatora zawartości przy użyciu klucza zawartości. Przykład kodu pokazuje, jak można obliczyć sumy kontrolnej.


### <a name="retrieve-the-protectionkeyid"></a>Pobrać ProtectionKeyId
Poniższy przykład pokazuje, jak pobrać ProtectionKeyId, odcisk palca certyfikatu, dla certyfikatu, którego należy użyć w przypadku szyfrowania kluczem zawartości. Wykonaj ten krok, aby się upewnić, że już ma odpowiedniego certyfikatu na tym komputerze.

Żądanie:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
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

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Pobrać ProtectionKey ProtectionKeyId
Poniższy przykład pokazuje, jak można pobrać certyfikatu X.509 przy użyciu ProtectionKeyId otrzymanego w poprzednim kroku.

Żądanie:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
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

### <a name="create-the-content-key"></a>Utwórz klucz zawartości
Po pobrać certyfikat X.509 i używać swojego klucza publicznego do szyfrowania klucza zawartości należy utworzyć **ContentKey** jednostki i ustaw jej właściwość odpowiednio wartości.

Jedna z wartości, że należy ustawić podczas tworzenia zawartości klucza jest typem. Podczas korzystania z szyfrowania przestrzeni dyskowej, wartość powinna być równa "1". 

Poniższy przykład przedstawia sposób tworzenia **ContentKey** z **ContentKeyType** ustawić szyfrowania magazynu ("1") i **ProtectionKeyType** ustawioną wartość "0", aby wskazać, że Klucz ochrony identyfikator jest odcisk palca certyfikatu X.509.  

Żądanie

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
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

## <a name="create-an-asset"></a>Utwórz zasób
Poniższy przykład pokazuje, jak utworzyć element zawartości.

**Żądania HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Odpowiedź HTTP**

W przypadku powodzenia następującą odpowiedź jest zwracana:

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

## <a name="associate-the-contentkey-with-an-asset"></a>Skojarz ContentKey z zasobów
Po utworzeniu ContentKey, skojarzyć ją z zawartości przy użyciu operacji $links, jak pokazano w poniższym przykładzie:

Żądanie:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Odpowiedź:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Utwórz AssetFile
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) jednostki reprezentuje plik wideo lub audio, który jest przechowywany w kontenerze obiektów blob. Plik zasobów zawsze jest skojarzony z zasobem i zasobów może zawierać jeden lub wiele plików zasobów. Zadanie Media Encoder usługi nie powiedzie się, jeśli obiekt pliku zasobu nie jest skojarzony z pliku cyfrowego w kontenerze obiektów blob.

**AssetFile** wystąpienia oraz plik multimedialna są dwa różne obiekty. Wystąpienia AssetFile zawiera metadanych dotyczących pliku nośnika, a plik nośnika zawiera zawartość multimedialna.

Po przekazaniu pliku nośnika cyfrowego do kontenera obiektów blob, którego użyjesz **scalania** żądania HTTP w celu zaktualizowania AssetFile informacje o pliku nośnika (niewidoczny w tym artykule). 

**Żądania HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
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
