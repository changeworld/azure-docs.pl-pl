---
title: Korzystanie z interfejsu API Video Indexer, aby dostosować model osoby — Azure
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model osoby za pomocą interfejsu API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 44f97e3d9af9daac8d62ae42be76bd73dedbd453
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838267"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Dostosowywanie modelu osoby za pomocą interfejsu API Video Indexer

Video Indexer obsługuje wykrywanie kroju i rozpoznawanie osobistości dla zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około 1 000 000 twarzy na podstawie często zażądanego źródła danych, takiego jak IMDB, Wikipedia i najpopularniejszych wpływów serwisu LinkedIn. Wykryto twarze, które nie są rozpoznawane przez funkcję rozpoznawania osobistości. są one jednak pozostawiane bez nazwy. Po przekazaniu wideo do Video Indexer i powrocie z powrotem możesz wrócić i nazwać powierzchnie, które nie zostały rozpoznane. Po oznaczeniu kroju z nazwą zostanie dodana do modelu osoby Twojego konta. Następnie Video Indexer rozpoznają Tę miarę w przyszłych wideo i poprzednich wideo.

Za pomocą interfejsu API Video Indexer można edytować powierzchnie wykryte w wideo, zgodnie z opisem w tym temacie. Możesz również użyć witryny sieci Web Video Indexer, zgodnie z opisem w temacie [Dostosowywanie modelu osoby za pomocą witryny sieci web video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Zarządzanie wieloma modelami osób 

Video Indexer obsługuje wiele modeli osób na konto. Ta funkcja jest obecnie dostępna tylko za pomocą interfejsów API Video Indexer.

Jeśli Twoje konto uwzględnia różne scenariusze przypadków użycia, można utworzyć wiele modeli osób dla każdego konta. Na przykład jeśli zawartość jest powiązana z sportem, można utworzyć osobny model dla każdej sportka (piłka, Basketball, piłka itp.). 

Po utworzeniu modelu można go użyć, dostarczając Identyfikator modelu określonego modelu osoby podczas przekazywania/indeksowania lub ponownego indeksowania wideo. Uczenie nowej kroju filmu wideo aktualizuje określony model niestandardowy, z którym zostało skojarzone wideo.

Każde konto ma limit 50 modeli osób. Jeśli nie potrzebujesz pomocy technicznej modelu wielu osób, nie przypisuj identyfikatora modelu osoby do wideo podczas przekazywania/indeksowania lub ponownego indeksowania. W takim przypadku Video Indexer używa domyślnego modelu osoby niestandardowej na Twoim koncie.

## <a name="create-a-new-person-model"></a>Utwórz nowy model osoby

Utwórz nowy model osoby na określonym koncie. 

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Poniżej znajduje się żądanie w zadaniu.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|ciąg|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|ciąg|Tak|Unikatowy identyfikator globalny dla konta|
|name|ciąg|Tak|Nazwa modelu osoby|
|accessToken|ciąg|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera nazwę i wygenerowany identyfikator modelu osoby, który właśnie został utworzony, zgodnie z poniższym formatem.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Następnie należy użyć wartości **identyfikatora** dla parametru **personModelId** podczas [przekazywania wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub ponownego [indeksowania wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Usuń model osoby

Usuń niestandardowy model osoby z określonego konta. 

Po pomyślnym usunięciu modelu osoby indeks bieżących filmów wideo, przy użyciu którego usunięto model, pozostanie bez zmian do momentu ponownego indeksowania. Podczas ponownego indeksowania powierzchnie, które zostały nazwane w usuniętym modelu, nie będą rozpoznawane przez Video Indexer w bieżących filmach wideo, które były indeksowane przy użyciu tego modelu; Jednak te powierzchnie nadal będą wykrywane. Bieżące filmy wideo, które były indeksowane przy użyciu usuniętego modelu, będą teraz używały domyślnego modelu osoby Twojego konta. Jeśli twarze z usuniętego modelu są również nazwane w domyślnym modelu konta, te powierzchnie będą nadal rozpoznawane w filmach wideo.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Poniżej znajduje się żądanie w zadaniu.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|ciąg|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|ciąg|Tak|Unikatowy identyfikator globalny dla konta|
|id|ciąg|Tak|Identyfikator modelu osoby (wygenerowany podczas tworzenia modelu osoby)|
|accessToken|ciąg|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Nie ma żadnej zwróconej zawartości, gdy model osoby został usunięty pomyślnie.

## <a name="get-all-person-models"></a>Pobierz wszystkie modele osób

Pobierz wszystkie modele osób na określonym koncie. 

### <a name="request-call"></a>Żądanie wywołania

Jest to żądanie GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Poniżej znajduje się żądanie w zadaniu.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|ciąg|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|ciąg|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|ciąg|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich modeli osób w Twoim koncie (w tym domyślny model osoby na określonym koncie) oraz każdej z nazw i identyfikatorów, które są zgodne z poniższym formatem przykładu.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Można wybrać model, który ma być używany dla filmu wideo, przy użyciu wartości **identyfikatora** modelu osoby dla parametru **personModelId** podczas [przekazywania wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub ponownego [indeksowania wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizowanie kroju

To polecenie umożliwia zaktualizowanie kroju obrazu w filmie wideo przy użyciu nazwy z IDENTYFIKATORem wideo i IDENTYFIKATORem kroju. Następnie aktualizuje on model osoby, z którym zostało skojarzone wideo podczas przekazywania/indeksowania lub ponownego indeksowania. Jeśli żaden model osoby nie został przypisany, aktualizuje domyślny model osoby dla konta. 

Gdy tak się stanie, rozpoznaje wystąpienia tej samej klasy z innymi bieżącymi filmami wideo, które współużytkują ten sam model osoby. Rozpoznawanie twarz w innych bieżących filmach wideo może zająć trochę czasu, ponieważ jest to proces wsadowy.

Możesz zaktualizować miarę, która Video Indexer rozpoznawana jako osobistości z nową nazwą. Nowa nazwa, która zostanie nadana, będzie mieć pierwszeństwo przed wbudowanym rozpoznawaniem osobistości.

### <a name="request-call"></a>Żądanie wywołania

Jest to żądanie POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Poniżej znajduje się żądanie w zadaniu.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|ciąg|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|ciąg|Tak|Unikatowy identyfikator globalny dla konta|
|videoId|ciąg|Tak|Identyfikator filmu wideo, w którym pojawia się Strona, która ma zostać zaktualizowana. Ta wartość jest tworzona podczas przekazywania i indeksowania wideo.|
|faceId|liczba całkowita|Tak|Identyfikator dla kroju, który zostanie zaktualizowany. Możesz uzyskać faceId z indeksu wideo|
|accessToken|ciąg|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|
|name|ciąg|Tak|Nowa nazwa, za pomocą której ma zostać zaktualizowana wartość.|

Nazwy są unikatowe dla modeli osób, dlatego w przypadku udostępnienia dwóch różnych twarzy w tym samym modelu osoby o tej samej wartości parametru **nazwa** Video Indexer wyświetlenia twarzy jako ta sama osoba i nastąpi ich zbieżność po ponownym indeksie wideo. 

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Po pomyślnym zaktualizowaniu kroju nie jest zwracana zawartość.

## <a name="next-steps"></a>Następne kroki

[Dostosuj model osoby za pomocą witryny sieci Web Video Indexer](customize-person-model-with-website.md)
