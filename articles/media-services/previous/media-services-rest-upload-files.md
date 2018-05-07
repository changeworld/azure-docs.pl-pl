---
title: "Przekazywanie plików do konta usługi Azure Media Services przy użyciu REST | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak pobrać zawartości nośnika do usługi Media Services przez tworzenie i przekazywanie zasoby."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: juliako
ms.openlocfilehash: 4ba6fdcec8d71326b02d71dbad429be8c2052171
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2018
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Przekazywanie plików do konta usługi Media Services za pomocą usługi REST
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. [Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) może zawierać wideo, audio, obrazy, kolekcje miniatur, tekst ścieżek i napisów plików (oraz metadane dotyczące tych plików.)  Po przekazaniu plików do zawartości, zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego. 

Z tego samouczka dowiesz się sposób przekazywania plików i innych operacji:

> [!div class="checklist"]
> * Ustawienia dla wszystkich operacji przekazywania Postman
> * Łączenie się z usługą Media Services 
> * Tworzenie zasad dostępu z uprawnieniami do zapisu
> * Utwórz zasób
> * Tworzenie lokalizatora SAS i Utwórz adres URL przesyłania
> * Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przesyłania
> * Utwórz metadanych w zasobu dla pliku nośnika, który został przekazany

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Tworzenie konta usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
- Przegląd [podczas uzyskiwania dostępu do usługi Azure Media usług interfejsu API za pomocą omówienie uwierzytelniania usługi AAD](media-services-use-aad-auth-to-access-ams-api.md) artykułu.
- Konfiguruj **Postman** zgodnie z opisem w [wymaga skonfigurowania Postman interfejsu API REST usługi Media](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Następujące kwestie przy użyciu interfejsu API REST usługi Media:
 
* Podczas uzyskiwania dostępu do jednostek przy użyciu interfejsu API REST usług nośnika, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md). <br/>Kolekcja Postman używane w tym samouczku odpowiada on za ustawienie wszystkie niezbędne nagłówki.
* Usługa Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresy URL przesyłania strumieniowego zawartości (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tego powodu kodowania procent jest niedozwolone. Wartość **nazwa** właściwość nie może mieć następujące [procent kodowanie zarezerwowanych znaków](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Ponadto może istnieć tylko jeden "." dla rozszerzenia nazwy pliku.
* Długość nazwy nie może być większa niż 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [to](media-services-quotas-and-limitations.md) artykułu, aby uzyskać szczegółowe informacje dotyczące limitu rozmiaru pliku.

## <a name="set-up-postman"></a>Konfigurowanie Postman

Aby uzyskać instrukcje dotyczące sposobu konfigurowania Postman, w tym samouczku, zobacz [skonfigurować Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

1. Dodawanie wartości połączenia dla danego środowiska. 

    Niektóre zmienne, które są częścią **MediaServices** [środowiska](postman-environment.md) należy ręcznie ustawić przed rozpoczęciem wykonywania operacji zdefiniowanej w [kolekcji](postman-collection.md).

    Można pobrać wartości pierwsze pięć zmiennych, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-env.png)
2. Określ wartość dla **MediaFileName** zmiennej środowiskowej.

    Określ nazwę pliku nośnika, który zamierzasz przekazać. W tym przykładzie zamierzamy przekazać BigBuckBunny.mp4. 
3. Sprawdź **AzureMediaServices.postman_environment.json** pliku. Zobaczysz, że prawie wszystkie operacje w kolekcji do wykonywania skryptu "test". Skrypty podjęcia niektórych wartości zwracane przez odpowiedź, a następnie ustaw zmienne środowiskowe odpowiednie.

    Na przykład pierwszej operacji pobiera token dostępu i ustaw go na **AccessToken** zmiennej środowiskowej, która jest używana w innych operacji.

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
4. Po lewej stronie **Postman** kliknij na **1. Pobierz token usługi AAD MFA** -> **uzyskać Azure AD tokenu dla jednostki usługi**.

    Część adresu URL jest wypełniony **AzureADSTSEndpoint** zmiennej środowiskowej (wartość można ustawić we wcześniejszej części tego samouczka).
    
5. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postment-get-token.png)

    Widać odpowiedzi, który zawiera "' access_token '". Skrypt "test" przyjmuje tę wartość i ustawia **AccessToken** zmiennej środowiskowej (jak opisano powyżej). Sprawdź zmienne środowiskowe, pojawi się czy ta zmienna uwzględnia teraz wartość token (token elementu nośnego) dostępu, która jest używana w pozostałej części operacji. 

    Jeśli token jest ważny przez przejść przez "Pobierz Azure AD Token dla usługi podmiotu zabezpieczeń" krok ponownie. 

## <a name="create-an-access-policy-with-write-permission"></a>Tworzenie zasad dostępu z uprawnieniami do zapisu

### <a name="overview"></a>Przegląd 

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Przed przekazaniem żadnych plików do magazynu obiektów blob, należy ustawić dostęp zasad prawa do zapisu do elementu zawartości. W tym celu po żądania HTTP AccessPolicies zestawem jednostek. Zdefiniuj wartość DurationInMinutes po utworzeniu lub pojawi się komunikat o błędzie 500 wewnętrzny serwer w odpowiedzi. Aby uzyskać więcej informacji o AccessPolicies, zobacz [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

1. Wybierz **AccessPolicy** -> **utworzyć AccessPolicy przekazywania**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skrypt "test" identyfikator AccessPolicy pobiera i ustawia zmienną środowiskową odpowiednie.

## <a name="create-an-asset"></a>Utwórz zasób

### <a name="overview"></a>Przegląd

[Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) to kontener dla wielu typów lub zestawów obiektów w usłudze Media Services: wideo, audio, obrazy, kolekcje miniatur, ścieżek tekstu i pliki napisów. W interfejsie API REST tworzenie zasobu wymaga wysłanie żądania POST do usługi Media Services i umieszczenie wszelkie informacje o zawartości w treści żądania.

Jedna z właściwości, które można dodać podczas tworzenia zasobu jest **opcje**. Można określić jedną z następujących opcji szyfrowania: **Brak** (domyślnie bez szyfrowania jest używany), **StorageEncrypted** (dla zawartości, które zostały zaszyfrowane wstępnie przy użyciu szyfrowania magazynu po stronie klienta), **CommonEncryptionProtected**, lub **EnvelopeEncryptionProtected**. Jeśli masz zasób zaszyfrowane, należy skonfigurować zasady dostarczania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasady dostarczania zasobów](media-services-rest-configure-asset-delivery-policy.md).

Jeśli zawartości jest zaszyfrowany, należy utworzyć **ContentKey** i łącza do zawartości zgodnie z opisem w artykule: [tworzenie ContentKey](media-services-rest-create-contentkey.md). Po przekazaniu plików do zawartości, należy zaktualizować właściwości szyfrowania na **AssetFile** jednostki o wartości uzyskano podczas **zasobów** szyfrowania. To zrobić za pomocą **scalania** żądania HTTP. 

W tym przykładzie tworzymy zasób niezaszyfrowane. 

### <a name="create-an-asset"></a>Utwórz zasób

1. Wybierz **zasoby** -> **utworzenia zasobu**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skrypt "test" pobiera identyfikator zasobu i ustawia zmienną środowiskową odpowiednie.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Tworzenie lokalizatora SAS i utworzyć adres URL przekazywania

### <a name="overview"></a>Przegląd

Po utworzeniu AccessPolicy, a także ustawić rzeczywisty plik jest przekazywany do kontenera magazynu obiektów Blob Azure przy użyciu interfejsów API REST usługi Magazyn Azure. Należy przekazać pliki jako blokowych obiektów blob. Stronicowe obiekty BLOB nie są obsługiwane przez usługi Azure Media Services.  

Aby uzyskać więcej informacji na temat pracy z obiektami blob magazynu Azure, zobacz [interfejsu API REST usługi Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Aby uzyskać adres URL przesyłania rzeczywistej, tworzenie lokalizatora SAS (pokazana poniżej). Lokalizatory zdefiniuj czas rozpoczęcia i typ punktu końcowego połączenia dla klientów, którzy mają dostęp do plików w zasób. Możesz utworzyć wiele jednostek lokalizatora dla danego pary AccessPolicy i zasobów do obsługi żądań klientów różnych i potrzeb. Wartość StartTime oraz wartość DurationInMinutes AccessPolicy każdego z tych lokalizatorów używa do określenia czasu, można użyć adresu URL. Aby uzyskać więcej informacji, zobacz [lokalizatora](https://docs.microsoft.com/rest/api/media/operations/locator).

Adres URL SAS ma następujący format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Zagadnienia do rozważenia

Zagadnienia do rozważenia:

* Nie może mieć więcej niż pięć lokalizatorów unikatowy skojarzone z danym zawartości w tym samym czasie. Aby uzyskać więcej informacji zobacz lokalizatora.
* Jeśli chcesz przekazać pliki natychmiast, należy ustawić wartość StartTime do pięciu minut przed bieżącym czasem. Jest to spowodowane mogą istnieć zegara pochylenia między komputer klienta i usługi Media Services. Ponadto wartość StartTime musi być w następującym formacie daty/godziny: RRRR-MM-ddtgg (na przykład "2014-05-23T17:53:50Z").    
* Może być 30 40 sekund opóźnienia po utworzeniu lokalizatora, gdy jest ona dostępna do użycia.

### <a name="create-a-sas-locator"></a>Tworzenie lokalizatora SAS

1. Wybierz **lokalizatora** -> **tworzenie lokalizatora SAS**.
2. Kliknij pozycję **Wyślij**.

    Skrypt "test" tworzy "Przekazywanie adresu URL" na podstawie podanej nazwy pliku nośnika, a informacje lokalizatora SAS i ustawia zmienną środowiskową odpowiednie.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przesyłania

### <a name="overview"></a>Przegląd

Teraz, gdy masz adresu URL przesyłania, należy napisać kodu za pomocą interfejsów API usługi Azure Blob bezpośrednio, aby przesłać plik do kontenera sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Użycie magazynu Azure, interfejsu API REST](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [UMIESZCZANIE obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Przekaż obiekty BLOB do magazynu obiektów Blob](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Przekaż plik z Postman

Na przykład używamy Postman można przekazać pliku mały plik MP4. Być może na przekazywanie danych binarnych za pośrednictwem Postman limit rozmiaru pliku.

Żądanie przekazania nie jest częścią **AzureMedia** kolekcji. 

Utwórz i skonfiguruj nowe żądanie:
1. Naciśnij klawisz  **+** , aby utworzyć nową kartę żądania.
2. Wybierz **PUT** operacji i Wklej **{{UploadURL}}** w adresie URL.
2. Pozostaw **autoryzacji** karcie, ponieważ jest (nie należy ustawiać na **tokenów Bearer**).
3. W **nagłówki** karcie, określ: **klucza**: "x-ms-blob-type" i **wartość**: "BlockBlob".
2. W **treści** , kliknij pozycję **binarne**.
4. Wybierz plik o nazwie określonej w **MediaFileName** zmiennej środowiskowej.
5. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Utwórz metadanych w elemencie zawartości

Po przekazaniu pliku, należy utworzyć metadanych w zasobu dla pliku nośnika, który został przekazany do magazynu obiektów blob skojarzony z zawartości.

1. Wybierz **AssetFiles** -> **CreateFileInfos**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-file-info.png)

Można przekazać pliku, i ustaw jej metadane.

## <a name="validate"></a>Walidacja

Aby zweryfikować, że plik został przekazany pomyślnie, należy zbadać [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) i porównaj **ContentFileSize** (lub inne szczegóły) można oczekiwać w nowych zasobów. 

Na przykład następująca **UZYSKAĆ** operacji łączy dane pliku dla pliku zasobów (w lub w przypadku pliku BigBuckBunny.mp4). Zapytanie używa [zmiennych środowiskowych](postman-environment.md) wcześniej skonfigurowane.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Odpowiedź zawiera rozmiar, nazwę i inne informacje.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Kolejne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

