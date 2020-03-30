---
title: Przekazywanie plików do konta usługi Azure Media Services przy użyciu rest | Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać zawartość multimedialną do usługi Media Services, tworząc i przekazując zasoby.
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
ms.openlocfilehash: d5b84a9d216457720e9bd4e17b002d6ab9490f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888596"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Przekazywanie plików na konto usługi Media Services przy użyciu stylu REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [Reszta](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. Encja [Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki podpisów kodowanych (oraz metadane dotyczące tych plików).  Po przesłaniu plików do zasobu zawartość jest bezpiecznie przechowywana w chmurze w celu dalszego przetwarzania i przesyłania strumieniowego. 

W tym samouczku dowiesz się, jak przekazać plik i inne związane z nim działania:

> [!div class="checklist"]
> * Konfigurowanie listonosza dla wszystkich operacji przekazywania
> * Łączenie się z usługą Media Services 
> * Tworzenie zasad dostępu z uprawnieniami do zapisu
> * Tworzenie zasobu
> * Tworzenie lokalizatora sygnatury dostępu Współdzielonego i tworzenie adresu URL przekazywania
> * Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przekazywania
> * Tworzenie metadanych w zasobie dla przesłanego pliku multimedialnego

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.
- [Utwórz konto usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).
- Przejrzyj [omówienie przeglądu interfejsu API usługi Azure Media Services z dostępem do usługi AAD.](media-services-use-aad-auth-to-access-ams-api.md)
- Również w celu uzyskania bardziej informacji Przejrzyj [użyj uwierzytelniania usługi Azure AD, aby uzyskać dostęp do interfejsu API usługi Media Services za pomocą](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad) artykułu REST.
- Skonfiguruj **listonosz** zgodnie z opisem w [temacie Konfiguruj listonosz dla połączeń interfejsu API REST usługi Media Services](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas korzystania z interfejsu API REST usługi Media Services obowiązują następujące zagadnienia:
 
* Podczas uzyskiwania dostępu do jednostek przy użyciu interfejsu MEDIA Services REST API należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md). <br/>Kolekcja Postman używana w tym samouczku zajmuje się ustawianiem wszystkich niezbędnych nagłówków.
* Usługa Media Services używa wartości właściwości IAssetFile.Name podczas tworzenia adresów URL zawartości przesyłanej strumieniowo (na przykład http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters).) Z tego powodu kodowanie procentowe jest niedozwolone. Wartość **Name** właściwości nie może mieć żadnego z następujących [znaków zarezerwowanych do kodowania procentowego:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;@&=+$,/?%#[]". Ponadto może istnieć tylko jeden '.' dla rozszerzenia nazwy pliku.
* Długość nazwy nie powinna być większa niż 260 znaków.
* Istnieje limit maksymalnego rozmiaru pliku do przetwarzania w usłudze Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.

## <a name="set-up-postman"></a>Konfigurowanie narzędzia Postman

Aby uzyskać instrukcje dotyczące konfigurowania listonosza dla tego samouczka, zobacz [Konfigurowanie listonosza](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

1. Dodawanie wartości połączeń do środowiska. 

    Niektóre zmienne, które są częścią [środowiska](postman-environment.md) **MediaServices,** muszą być ustawione ręcznie, zanim będzie można rozpocząć wykonywanie operacji zdefiniowanych w [kolekcji](postman-collection.md).

    Aby uzyskać wartości dla pierwszych pięciu zmiennych, zobacz [Dostęp do interfejsu API usługi Azure Media Services z uwierzytelnianiem usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-import-env.png)
2. Określ wartość zmiennej środowiskowej **MediaFileName.**

    Określ nazwę pliku nośnika, który zamierzasz przekazać. W tym przykładzie mamy zamiar przesłać BigBuckBunny.mp4. 
3. Sprawdź plik **AzureMediaServices.postman_environment.json.** Zobaczysz, że prawie wszystkie operacje w kolekcji wykonać skrypt "test". Skrypty wziąć niektóre wartości zwracane przez odpowiedź i ustawić odpowiednie zmienne środowiskowe.

    Na przykład pierwsza operacja pobiera token dostępu i ustawić go na **accessToken** zmiennej środowiskowej, która jest używana we wszystkich innych operacjach.

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
4. Po lewej stronie okna **Listonosz** kliknij **na 1. Pobierz token AAD Auth** -> **Pobierz token usługi Azure AD dla jednostki usługi.**

    Część adresu URL jest wypełniona zmienną środowiskową **AzureADSTSEndpoint** (wcześniej w samouczku ustawiono wartości zmiennych środowiskowych, które obsługują kolekcję).

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kliknij pozycję **Wyślij**.

    Możesz zobaczyć odpowiedź, która zawiera "access_token". Skrypt "test" przyjmuje tę wartość i ustawia zmienną środowiskową **AccessToken** (jak opisano powyżej). Jeśli zbadasz zmienne środowiskowe, zobaczysz, że ta zmienna zawiera teraz wartość tokenu dostępu (tokenu nośnika), która jest używana w pozostałych operacjach. 

    Jeśli token wygaśnie, ponownie przejdzie przez krok "Pobierz token usługi Azure AD dla jednostki usługi". 

## <a name="create-an-access-policy-with-write-permission"></a>Tworzenie zasad dostępu z uprawnieniami do zapisu

### <a name="overview"></a>Omówienie 

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Przed przekazaniem plików do magazynu obiektów blob ustaw prawa zasad dostępu do zapisu do zasobu. Aby to zrobić, opublikuj żądanie HTTP do zestawu encji AccessPolicies. Zdefiniuj wartość DurationInMinutes podczas tworzenia lub otrzymasz komunikat o błędzie 500 wewnętrzny serwer z powrotem w odpowiedzi. Aby uzyskać więcej informacji na temat AccessPolicies, zobacz [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

1. Wybierz **opcję AccessPolicy** -> **Create AccessPolicy for Upload**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skrypt "test" pobiera Identyfikator AccessPolicy i ustawia odpowiednią zmienną środowiskową.

## <a name="create-an-asset"></a>Tworzenie zasobu

### <a name="overview"></a>Omówienie

[Zasób](https://docs.microsoft.com/rest/api/media/operations/asset) jest kontenerem dla wielu typów lub zestawów obiektów w usłudze Media Services, w tym wideo, audio, obrazów, kolekcji miniatur, ścieżek tekstowych i plików podpisów kodowych. W interfejsie API REST tworzenie zasobu wymaga wysłania żądania POST do usługi Media Services i umieszczenia wszelkich informacji o właściwościach zasobu w treści żądania.

Jedną z właściwości, które można dodać podczas tworzenia zasobu jest **Opcje**. Można określić jedną z następujących opcji szyfrowania: **Brak** (domyślnie, nie jest używane szyfrowanie), **StorageEncrypted** (dla zawartości, która została wstępnie zaszyfrowana za pomocą szyfrowania magazynu po stronie klienta), **CommonEncryptionProtected**lub **EnvelopeEncryptionProtected**. Jeśli masz zaszyfrowany zasób, musisz skonfigurować zasady dostarczania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania zasobów](media-services-rest-configure-asset-delivery-policy.md).

Jeśli zasób jest zaszyfrowany, należy utworzyć **contentkey** i połączyć go z [zasobem,](media-services-rest-create-contentkey.md)jak opisano w następującym artykule: Jak utworzyć contentkey . Po przekazaniu plików do zasobu należy zaktualizować właściwości szyfrowania w jednostce **AssetFile** o wartości dodane podczas szyfrowania **zasobów.** Zrób to za pomocą żądania **MERGE** HTTP. 

W tym przykładzie tworzymy niezaszyfrowany zasób. 

### <a name="create-an-asset"></a>Tworzenie zasobu

1. Wybierz pozycję Zasoby -> **Utwórz** **zasób**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skrypt "test" pobiera identyfikator zasobu i ustawia odpowiednią zmienną środowiskową.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Tworzenie lokalizatora sygnatury dostępu Współdzielonego i tworzenie adresu URL przekazywania

### <a name="overview"></a>Omówienie

Po ustawieniu usługi AccessPolicy i Locator rzeczywisty plik jest przekazytywał do kontenera usługi Azure Blob Storage przy użyciu interfejsów API REST usługi Azure Storage. Należy przekazać pliki jako blokowe obiekty blob. Obiekty BLOB strony nie są obsługiwane przez usługę Azure Media Services.  

Aby uzyskać więcej informacji na temat pracy z obiektami blob magazynu platformy Azure, zobacz [Interfejs API REST usługi obiektów Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Aby otrzymać rzeczywisty adres URL przekazywania, utwórz lokalizator sygnatury dostępu Współdzielonego (pokazany poniżej). Lokalizatory definiują godzinę rozpoczęcia i typ punktu końcowego połączenia dla klientów, którzy chcą uzyskać dostęp do plików w zasobie. Można utworzyć wiele jednostek lokalizatora dla danej pary AccessPolicy i Asset do obsługi różnych żądań i potrzeb klientów. Każdy z tych lokalizatorów używa Wartości StartTime plus DurationInMinutes wartość AccessPolicy do określenia czasu adres URL może być używany. Aby uzyskać więcej informacji, zobacz [Lokalizator](https://docs.microsoft.com/rest/api/media/operations/locator).

Adres URL sygnatury dostępu Współdzielonego ma następujący format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Zagadnienia do rozważenia

Zagadnienia do rozważenia:

* Nie można jednocześnie mieć więcej niż pięciu unikatowych lokalizatorów skojarzonych z danym zasobem. Aby uzyskać więcej informacji, zobacz Lokalizator.
* Jeśli musisz natychmiast przekazać pliki, należy ustawić wartość StartTime na pięć minut przed bieżącym czasem. Jest tak, ponieważ może istnieć pochylenie zegara między komputerem klienckim a usługą Media Services. Ponadto wartość StartTime musi być w następującym formacie DateTime: YYYY-MM-DDTHH:mm:ssZ (na przykład "2014-05-23T17:53:50Z").    
* Może wystąpić opóźnienie 30-40 sekund po utworzeniu lokalizatora, gdy jest on dostępny do użycia.

### <a name="create-a-sas-locator"></a>Tworzenie lokalizatora sygnatury dostępu Współdzielonego

1. Wybierz **pozycję Lokalizator lokalizatora** -> **SAS**.
2. Kliknij pozycję **Wyślij**.

    Skrypt "test" tworzy "Przekaż adres URL" na podstawie określonej nazwy pliku multimedialnego i informacji lokalizatora sygnatury dostępu Współdzielonego i ustawia odpowiednią zmienną środowiskową.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przekazywania

### <a name="overview"></a>Omówienie

Teraz, gdy masz adres URL przekazywania, musisz napisać kod przy użyciu interfejsów API obiektów Blob platformy Azure bezpośrednio do przekazania pliku do kontenera sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Korzystanie z interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [UMIEŚĆ obiekt blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Przekazywanie obiektów blob do magazynu obiektów Blob](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Przekazywanie pliku u listonosza

Na przykład używamy Listonosza do przesłania małego pliku .mp4. Może istnieć limit rozmiaru pliku podczas przesyłania pliku binarnego za pośrednictwem listonosza.

Żądanie przekazywania nie jest częścią kolekcji **AzureMedia.** 

Utwórz i skonfiguruj nowe żądanie:
1. Naciśnij **+** klawisz , aby utworzyć nową kartę żądania.
2. Wybierz pozycję **Put** operation and paste **{{UploadURL}}** w adresie URL.
2. Pozostaw kartę **Autoryzacja** w stanie, w jakim jest (nie należy ustawiać jej na **token na okaziciela).**
3. Na karcie **Nagłówki** określ: **Klucz:**"x-ms-blob-type" i **Value**: "BlockBlob".
2. Na karcie **Treść** kliknij pozycję **plik binarny**.
4. Wybierz plik o nazwie określonej w zmiennej środowiskowej **MediaFileName.**
5. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Tworzenie metadanych w zasobie

Po przekazaniu pliku należy utworzyć metadane w zasobie dla pliku multimedialnego przekazanego do magazynu obiektów blob skojarzonego z zasobem.

1. Wybierz pozycję **AssetFiles** -> **CreateFileInfos**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/media-services-rest-upload-files/postman-create-file-info.png)

Plik powinien zostać przekazany, a jego zestaw metadanych.

## <a name="validate"></a>Walidacja

Aby sprawdzić, czy plik został pomyślnie przekazany, można zbadać [plik zasobów](https://docs.microsoft.com/rest/api/media/operations/assetfile) i porównać rozmiar **pliku contentfile** (lub inne szczegóły) z oczekiwanymi informacjami w nowym zasobie. 

Na przykład następująca operacja **GET** przynosi dane pliku dla pliku zasobu (w przypadku pliku BigBuckBunny.mp4). Kwerenda używa [zmiennych środowiskowych,](postman-environment.md) które zostały ustawione wcześniej.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Odpowiedź będzie zawierać rozmiar, nazwę i inne informacje.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Następne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).

Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania na podstawie pliku trafiającego do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz [ten przykład](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

