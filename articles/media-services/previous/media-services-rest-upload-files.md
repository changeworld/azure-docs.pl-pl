---
title: Przekazywanie plików na konto usługi Azure Media Services przy użyciu usługi REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać zawartości multimedialnej do usługi Media Services, tworząc i przekazując zasoby.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f63087d107b9db30e2af6273afde7f51f1c72404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817658"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Przekazywanie plików na konto usługi Media Services przy użyciu usługi REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. [Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) jednostki może zawierać wideo, audio, obrazy, kolekcje miniatur, tekst śledzi i napisów plików (i metadane dotyczące tych plików.)  Po przekazaniu plików do elementu zawartości, zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego. 

W tym samouczku dowiesz się, jak przekazać plik i innych operacji skojarzonych z nią:

> [!div class="checklist"]
> * Konfigurowanie narzędzia Postman dla wszystkich operacji przekazywania
> * Łączenie się z usługą Media Services 
> * Tworzenie zasad dostępu z uprawnieniami do zapisu
> * Utworzenie elementu zawartości
> * Tworzenie lokalizatora SAS następuje i Utwórz adres URL przesyłania
> * Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przesyłania
> * Utworzenie metadanych w elemencie zawartości dla pliku nośnika, który został przekazany

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure portal](media-services-portal-create-account.md).
- Przegląd [uzyskiwania dostępu do usługi Azure Media Services interfejsu API za pomocą omówienie uwierzytelniania usługi AAD](media-services-use-aad-auth-to-access-ams-api.md) artykułu.
- Konfiguruj **Postman** zgodnie z opisem w [wywołuje Konfigurowanie narzędzia Postman dla interfejsu API REST usługi Media Services](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Korzystając z interfejsu API REST usługi Media Services, obowiązują następujące zastrzeżenia:
 
* Podczas uzyskiwania dostępu do jednostek przy użyciu interfejsu API REST usługi Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md). <br/>Odpowiada kolekcji Postman, w tym samouczku używane ustawienie wszystkie niezbędne nagłówki.
* Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent nie jest dozwolone. Wartość **nazwa** właściwość nie może zawierać żadnych z następujących [procent kodowanie — zastrzeżone znaki](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * "();: @& = + $, /? [] % #". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.
* Długość nazwy nie powinna być dłuższa niż 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Aby uzyskać instrukcje dotyczące sposobu konfigurowania Postman na potrzeby tego samouczka, zobacz [Konfigurowanie narzędzia Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

1. Dodaj wartości połączenia dla danego środowiska. 

    Niektóre zmienne, które są częścią **MediaServices** [środowiska](postman-environment.md) należy skonfigurować ręcznie przed rozpoczęciem wykonywania operacji, które zdefiniowano w [kolekcji](postman-collection.md).

    Aby uzyskać wartości dla pierwszych pięć zmiennych, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-env.png)
2. Określ wartość **MediaFileName** zmiennej środowiskowej.

    Określ nazwę pliku nośnika, który zamierzasz przekazać. W tym przykładzie użyjemy do przekazania BigBuckBunny.mp4. 
3. Sprawdź **AzureMediaServices.postman_environment.json** pliku. Zobaczysz, że prawie wszystkich operacji w kolekcji wykonać skrypt "test". Skrypty wykonać niektóre wartości zwracane przez odpowiedź, a następnie ustawić odpowiednie zmienne środowiskowe.

    Na przykład pierwsza operacja pobiera token dostępu i ustaw ją na **AccessToken** zmienną środowiskową, która jest używana w innych operacji.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. W lewej części **Postman** okna, kliknij pozycję **1. Uzyskiwanie tokenu uwierzytelniania usługi AAD** -> **Get usługi Azure AD Token dla jednostki usługi**.

    Część adresu URL jest wypełniany **AzureADSTSEndpoint** zmienna środowiskowa (we wcześniejszej części tego samouczka można ustawić wartości zmiennych środowiskowych, które obsługują kolekcji).

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kliknij pozycję **Wyślij**.

    Możesz zobaczyć odpowiedź, która zawiera "access_token". Skrypt "test" przyjmuje tę wartość i ustawia **AccessToken** zmienna środowiskowa (zgodnie z opisem powyżej). Możesz zbadać zmienne środowiskowe, zobaczysz że ta zmienna zawiera teraz wartość token (token elementu nośnego) dostępu, która jest używana w pozostałej części operacji. 

    Jeśli token jest ważny przejść przez krok "Pobierz usługi Azure AD Token dla usługi głównej" ponownie. 

## <a name="create-an-access-policy-with-write-permission"></a>Tworzenie zasad dostępu z uprawnieniami do zapisu

### <a name="overview"></a>Omówienie 

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Przed przekazaniem żadnych plików do magazynu obiektów blob, należy ustawić dostępu zasady prawa do zapisu do elementu zawartości. Aby to zrobić, OPUBLIKUJ żądanie HTTP AccessPolicies zestawem jednostek. Zdefiniuj wartość DurationInMinutes po utworzeniu lub pojawi się komunikat 500 Błąd wewnętrzny serwera w odpowiedzi. Aby uzyskać więcej informacji na temat AccessPolicies, zobacz [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

1. Wybierz **AccessPolicy** -> **tworzenie AccessPolicy w celu przekazania**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skrypt "test" pobiera identyfikator AccessPolicy i ustawia zmienną środowiskową odpowiednie.

## <a name="create-an-asset"></a>Utworzenie elementu zawartości

### <a name="overview"></a>Omówienie

[Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) jest kontenerem dla wielu typów lub zestawów obiektów w usłudze Media Services, w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów. W interfejsie API REST tworzenia zasobu wymaga wysłanie żądania POST do usługi Media Services oraz umieszczanie właściwości informacji o elementów zawartości w treści żądania.

Jedna z właściwości, które można dodać podczas tworzenia zasobu jest **opcje**. Można określić jedną z następujących opcji szyfrowania: **Brak** (ustawienie domyślne, bez szyfrowania jest używany), **StorageEncrypted** (w przypadku zawartości, który został wstępnie zaszyfrowane przy użyciu szyfrowania magazynu po stronie klienta), **CommonEncryptionProtected**, lub  **EnvelopeEncryptionProtected**. W przypadku elementu zawartości zaszyfrowanej należy skonfigurować zasady dostarczania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

Jeśli element zawartości jest zaszyfrowany, należy utworzyć **ContentKey** i link do elementu zawartości zgodnie z opisem w następującym artykule: [Jak utworzyć ContentKey](media-services-rest-create-contentkey.md). Po przekazaniu plików do niego, musisz zaktualizować właściwości szyfrowania na **AssetFile** jednostki o wartości stało się podczas **zasobów** szyfrowania. Zrobić to za pomocą **scalania** żądania HTTP. 

W tym przykładzie tworzymy niezaszyfrowanych zasobów. 

### <a name="create-an-asset"></a>Utworzenie elementu zawartości

1. Wybierz **zasoby** -> **utworzyć zasób**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skrypt "test" pobiera identyfikator elementu zawartości i ustawia zmienną środowiskową odpowiednie.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Tworzenie lokalizatora SAS następuje i utworzyć adres URL przekazywania

### <a name="overview"></a>Omówienie

Po utworzeniu AccessPolicy i lokalizatora zestawu rzeczywisty plik zostanie przekazany do kontenera usługi Azure Blob Storage przy użyciu interfejsów API REST usługi Azure Storage. Należy przekazać pliki jako blokowe obiekty BLOB. Stronicowe obiekty BLOB nie są obsługiwane przez usługę Azure Media Services.  

Aby uzyskać więcej informacji na temat pracy z obiektami blob usługi Azure storage, zobacz [interfejsu API REST usługi Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Aby otrzymywać z adresem URL rzeczywistej przekazywania, należy utworzyć lokalizatora SAS następuje (pokazana poniżej). Lokalizatory definiują czas rozpoczęcia i typu punktu końcowego połączenia dla klientów, którzy chcą uzyskać dostęp do plików w zasobie. Możesz utworzyć wiele jednostek lokalizatora dla danego pary AccessPolicy i zasobów do obsługi żądań różnych klientów i potrzeb. Wartość StartTime plus wartość DurationInMinutes AccessPolicy każdego z tych lokalizatorów używa do określenia czasu, można użyć adresu URL. Aby uzyskać więcej informacji, zobacz [lokalizatora](https://docs.microsoft.com/rest/api/media/operations/locator).

Adres URL SAS ma następujący format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Zagadnienia do rozważenia

Zagadnienia do rozważenia:

* Nie może mieć więcej niż pięć unikatowe Lokalizatory skojarzone z danym elementem zawartości w tym samym czasie. Aby uzyskać więcej informacji zobacz lokalizatora.
* Jeśli zachodzi potrzeba natychmiast przekazać pliki, należy ustawić wartość StartTime do pięciu minut przed bieżącym czasem. Jest to spowodowane mogą istnieć zegara niesymetryczność między komputerem klienckim i Media Services. Ponadto wartość StartTime musi być w następującym formacie daty/godziny: RRRR-MM-ddtgg (na przykład "2014-05-23T17:53:50Z").    
* Może to być sekundy 30 – 40 opóźnienie po utworzeniu lokalizatora, gdy będzie ona dostępna do użytku.

### <a name="create-a-sas-locator"></a>Tworzenie lokalizatora sygnatury dostępu Współdzielonego

1. Wybierz **lokalizatora** -> **utworzenie lokalizatora sygnatury dostępu Współdzielonego**.
2. Kliknij pozycję **Wyślij**.

    Skrypt "test" tworzy "Przekazywanie adresu URL" na podstawie podanej nazwy pliku multimediów i informacji lokalizatora sygnatury dostępu Współdzielonego i ustawia zmienną środowiskową odpowiednie.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przesyłania

### <a name="overview"></a>Omówienie

Teraz, gdy masz adres URL przesyłania, należy napisać kodu za pomocą interfejsów API usługi Azure Blob bezpośrednio do przekazania pliku do kontenera sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Za pomocą interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [WSTAWIENIE obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Przekazywanie obiektów blob do magazynu obiektów Blob](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Przekaż plik za pomocą narzędzia Postman

Na przykład używamy narzędzia Postman do przekazania plików MP4 małe. Być może na przekazywanie danych binarnych za pomocą narzędzia Postman limit rozmiaru pliku.

Żądanie przekazania nie jest częścią **AzureMedia** kolekcji. 

Tworzenie i konfigurowanie nowego żądania:
1. Naciśnij klawisz **+**, aby utworzyć nową kartę żądanie.
2. Wybierz **umieścić** operacji i Wklej **{{UploadURL}}** w adresie URL.
2. Pozostaw **autoryzacji** karcie, ponieważ jest (nie należy ustawiać na **tokenu elementu nośnego**).
3. W **nagłówki** karcie, określ: **Klucz**: "x-ms-blob-type" i **wartość**: "BlockBlob".
2. W **treści** kliknij pozycję **binarne**.
4. Wybierz plik o nazwie określonej w **MediaFileName** zmiennej środowiskowej.
5. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Utworzenie metadanych w zasobie

Po przekazaniu pliku należy utworzyć metadanych w elemencie zawartości dla pliku nośnika, który został przekazany do magazynu obiektów blob skojarzony element zawartości.

1. Wybierz **AssetFiles** -> **CreateFileInfos**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-file-info.png)

Można przekazać pliku, a następnie ustaw jego metadanych.

## <a name="validate"></a>Walidacja

Aby sprawdzić, czy plik został przekazany pomyślnie, możesz chcieć zbadać [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) i porównaj **ContentFileSize** (lub inne szczegóły) będzie oczekiwać w nowy zasób. 

Na przykład następująca **UZYSKAĆ** operacji udostępnia dane pliku dla pliku zasobów (w lub w przypadku pliku BigBuckBunny.mp4). Zapytanie jest za pomocą [zmienne środowiskowe](postman-environment.md) ustawionej wcześniej.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Odpowiedź zawiera rozmiar, nazwę i inne informacje.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Kolejne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

