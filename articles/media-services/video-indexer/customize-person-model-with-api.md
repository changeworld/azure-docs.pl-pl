---
title: Dostosowywanie modelu osoby — Azure za pomocą interfejsu API indeksatora wideo
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model osoby przy użyciu interfejsu API indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360504"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Dostosuj model osoba, za pomocą interfejsu API indeksatora wideo

Usługa Video Indexer obsługuje wykrywanie twarzy oraz rozpoznawanie osobistości zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około jeden milion twarze na podstawie źródła najczęściej żądanych danych, takich jak ZAUFANI Wikipedia i górnym specjaliści LinkedIn. Wykryto twarzy, które nie są rozpoznawane przez funkcję rozpoznawania osobistości; jednak są pozostawione bez nazwy. Po wideo możesz przekazać do indeksatora wideo i uzyskuj wyniki z powrotem, może Przejdź wstecz i nazwij twarzy, które nie zostały rozpoznane. Gdy etykiety twarz o nazwie twarzy i nazwy poproś o dodanie Cię do swojego konta osoby modelu. Indeksator wideo następnie rozpozna twarzach przyszłych filmów wideo i ostatnich filmów wideo.

Interfejs API indeksatora wideo służy do edytowania twarzy, które zostały wykryte w filmach wideo, zgodnie z opisem w tym temacie. Umożliwia także Video Indexer witryny sieci Web, zgodnie z opisem w [modelu dostosować osoby za pomocą witryny sieci Web Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Zarządzanie wielu modeli osoby 

Usługa Video Indexer obsługuje wiele modeli osoby na jednym koncie. Ta funkcja jest obecnie dostępna wyłącznie za pośrednictwem interfejsów API indeksatora wideo.

Jeśli Twoje konto jest przeznaczony dla różnych scenariuszy przypadków użycia, można utworzyć wiele modeli osoby na jednym koncie. Na przykład jeśli zawartość jest powiązany z dyscyplin sportowych, następnie utwórz oddzielne modelu osoby dla każdego rodzaju sportu (football przeciwna, soccer, itp.). 

Po utworzeniu modelu, można użyć go, podając identyfikator modelu określonego modelu osoby podczas przekazywania indeksowania lub Indeksowanie filmu wideo. Szkolenie nowej twarzy film aktualizuje określonego modelu niestandardowego, który film wideo został skojarzony z.

Każde konto ma ograniczenie do 50 modeli osoby. Jeśli nie potrzebujesz obsługi wielu modelu osoby, nie należy przypisywać osoby identyfikator modelu do swojego wideo podczas przekazywania indeksowania lub ponowne indeksowanie. W takim przypadku Video Indexer używa domyślnego niestandardowego modelu osoby na Twoim koncie.

## <a name="create-a-new-person-model"></a>Utwórz nowy model osoby

Utwórz nowy model osoby na określonym koncie. 

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametry żądania 

|**Name (Nazwa)**|**Type**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|name|string|Yes|Nazwa modelu osoby|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera nazwy i Identyfikatora modelu wygenerowanego osoby modelu, utworzony zgodnie z formatu w poniższym przykładzie.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Następnie należy użyć **identyfikator** wartość **personModelId** parametru podczas [przekazywania filmu wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [indeksowanie wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Usuń model osoby

Usuń niestandardowy model osoby z określonego konta. 

Po pomyślnym usunięciu modelu osoby indeks bieżącej filmów wideo, które były używane usunięto modelu pozostanie niezmieniona, do momentu ich indeksowania. Podczas ponownego indeksowania, powierzchnie, które zostały nazwane w modelu usuniętego nie zostanie rozpoznana przez indeksator wideo w bieżącym filmów wideo, które są indeksowane, za pomocą modelu; Jednak te powierzchnie nadal zostanie wykryty. Bieżący filmów wideo, które są indeksowane, za pomocą modelu usuniętego będą teraz korzystały z Twojego konta domyślnego osoby modelu. Twarze na nagraniach usunięto modelu są również wymienione w modelu domyślnego konta, będą nadal być rozpoznawany w filmach wideo te powierzchnie.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parametry żądania

|**Name (Nazwa)**|**Type**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|id|string|Yes|Identyfikator modelu osoby (generowany podczas tworzenia modelu osoby)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Brak zawartości zwracane, gdy model osoby został pomyślnie usunięty.

## <a name="get-all-person-models"></a>Wszystkie modele osoby

Pobierz wszystkie modele osoby na określonym koncie. 

### <a name="request-call"></a>Wywołanie żądania

Jest to żądanie GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parametry żądania

|**Name (Nazwa)**|**Type**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich modeli osoby na koncie (w tym domyślny model osoby na określonym koncie), a każdy z ich nazwy i identyfikatory następującego formatu w poniższym przykładzie.

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

Można wybrać model, który chcesz użyć dla filmu wideo za pomocą **identyfikator** wartość modelu osoby **personModelId** parametru podczas [przekazywania filmu wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [indeksowanie wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizacja twarzy

To polecenie umożliwia aktualizowanie twarzy w wideo nazwą, używając Identyfikatora wideo i identyfikator powierzchni. Następnie aktualizuje model osoby, do którego film wideo został skojarzony z po przekazaniu indeksowania lub ponowne indeksowanie. Jeśli przypisano bez modelu osoby, aktualizuje konta domyślnego osoby modelu. 

Gdy tak się stanie, rozpoznaje wystąpień tego samego twarzy w innych bieżącego filmów wideo, które współużytkują ten sam model osoby. Rozpoznawanie twarzy w Twojej bieżącej wideo może trochę potrwać zaczęły obowiązywać, ponieważ jest to proces usługi batch.

Możesz zaktualizować krój Video Indexer, który został rozpoznany jako osobistości z nową nazwą. Nowa nazwa nadana ma pierwszeństwo rozpoznawania osobistości wbudowanych.

### <a name="request-call"></a>Wywołanie żądania

Jest to żądanie POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parametry żądania

|**Name (Nazwa)**|**Type**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|videoId|string|Yes|Identyfikator filmu wideo, w którym pojawia się twarz, którą chcesz zaktualizować. To jest tworzony, gdy wideo zostanie przekazany i indeksowane.|
|faceId|liczba całkowita|Yes|Identyfikator twarzy, które zostaną zaktualizowane. Indeks wideo można uzyskać faceId|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|
|name|string|Yes|Nowa nazwa do rozpoznawania twarzy, za pomocą aktualizacji.|

Nazwy są unikatowe dla modeli osoby, więc jeśli nadasz dwa różne twarze na tej samej osoby modelu takie same **nazwa** wartość parametru i Video Indexer widoków powierzchnie jako ta sama osoba i zbieżna dla wartości, ich po ponownego poindeksowania danych filmu wideo. 

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Brak zawartości zwracane, gdy twarz został pomyślnie zaktualizowany.

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model osoby, witryny sieci Web indeksatora wideo](customize-person-model-with-website.md)
