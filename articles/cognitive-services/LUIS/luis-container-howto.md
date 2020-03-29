---
title: Kontenery platformy Docker — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Kontener usługi LUIS ładuje uszkoloną lub opublikowaną aplikację do kontenera platformy docker i zapewnia dostęp do prognoz kwerend z punktów końcowych interfejsu API kontenera.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: dapine
ms.openlocfilehash: 308a474970db54022e5351fdf349d9572fbafb0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219930"
---
# <a name="install-and-run-luis-docker-containers"></a>Instalowanie i uruchamianie kontenerów platformy dokowanych usługi LUIS
 
Kontener rozumienia języka (LUIS) ładuje przeszkolony lub opublikowany model rozumienia języka. Jako [aplikacja usługi LUIS](https://www.luis.ai)kontener docker zapewnia dostęp do prognoz kwerend z punktów końcowych interfejsu API kontenera. Można zbierać dzienniki zapytań z kontenera i przekazać je z powrotem do aplikacji Opis języka, aby poprawić dokładność przewidywania aplikacji.

Poniższy film pokazuje przy użyciu tego kontenera.

[![Demonstracja kontenerów dla usług Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić kontener usługi LUIS, należy zwrócić uwagę na następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker jest zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Powinieneś mieć podstawową wiedzę na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.| 
|Plik `Cognitive Services` aplikacji [zaopatrzonych](luis-how-to-start-new-app.md) w zasoby platformy Azure i usługi LUIS |Aby korzystać z kontenera, musisz mieć:<br><br>* _Zasób_ platformy Azure usług Cognitive Services i skojarzony klucz rozliczeniowy identyfikatora URI punktu końcowego rozliczeń. Obie wartości są dostępne na stronach Przegląd i Klucze dla zasobu i są wymagane do uruchomienia kontenera. <br>* Przeszkolona lub opublikowana aplikacja zapakowana jako zamontowane dane wejściowe do kontenera z skojarzonym identyfikatorem aplikacji. Spakowany plik można uzyskać z portalu usługi LUIS lub interfejsów API tworzenia. Jeśli otrzymujesz spakowane aplikacje usługi LUIS z [interfejsów API tworzenia,](#authoring-apis-for-package-file)potrzebny jest również _klucz tworzenia_.<br><br>Te wymagania są używane do przekazywania argumentów wiersza polecenia do następujących zmiennych:<br><br>**{AUTHORING_KEY}**: Ten klucz jest używany do uzyskania spakowane aplikacji z usługi LUIS w chmurze i przekazać dzienniki kwerendy z powrotem do chmury. Format to `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: Ten identyfikator służy do wybierania aplikacji. Format to `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}**: Ten klucz jest używany do uruchamiania kontenera. Klucz punktu końcowego można znaleźć w dwóch miejscach. Pierwszym z nich jest portal Azure w ramach listy kluczy zasobu _usług Cognitive Services._ Klucz punktu końcowego jest również dostępny w portalu usługi LUIS na stronie Ustawienia kluczy i punktu końcowego. Nie używaj przycisku startowego.<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie Przegląd.<br><br>[Klucz tworzenia i klucz punktu końcowego](luis-boundaries.md#key-limits) mają różne cele. Nie używaj ich zamiennie. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Tworzenie interfejsów API dla pliku pakietu

Tworzenie interfejsów API dla spakowanych aplikacji:

* [Opublikowany interfejs API pakietu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nieopublikowany, przeszkolony interfejs API pakietu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

Ten kontener obsługuje minimalne i zalecane wartości dla ustawień:

|Kontener| Minimalne | Zalecane | Tps<br>(Minimum, Maksimum)|
|-----------|---------|-------------|--|
|LUIS|1 rdzeń, 2 GB pamięci|1 rdzeń, 4 GB pamięci|20, 40|

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub więcej.
* TPS - transakcje na sekundę

Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz `mcr.microsoft.com/azure-cognitive-services/luis` kontenera z repozytorium:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Aby uzyskać pełny opis dostępnych tagów, takich jak `latest` używane w poprzednim poleceniu, zobacz usługa [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) w centrum platformy Docker.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak korzystać z pojemnika

Gdy kontener znajduje się na [komputerze-hoście,](#the-host-computer)użyj następującego procesu do pracy z kontenerem.

![Proces korzystania z kontenera rozumienia języka (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Eksportuj pakiet](#export-packaged-app-from-luis) dla kontenera z portalu usługi LUIS lub interfejsów API usługi LUIS.
1. Przenieś plik pakietu do wymaganego katalogu **wejściowego** na [komputerze-hoście](#the-host-computer). Nie należy zmieniać nazwy, zmieniać, zastępować ani dekompresować pliku pakietu usługi LUIS.
1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami _instalacji wejściowej_ i rozliczeń. Dostępnych jest więcej `docker run` [przykładów](luis-container-configuration.md#example-docker-run-commands) polecenia. 
1. [Wykonywanie zapytań o punkt końcowy prognozowania kontenera](#query-the-containers-prediction-endpoint). 
1. Po zakończeniu z kontenerem, [zaimportować dzienniki punktu końcowego](#import-the-endpoint-logs-for-active-learning) z instalacji wyjściowej w portalu usługi LUIS i [zatrzymać](#stop-the-container) kontenera.
1. Użyj [aktywnego uczenia portalu](luis-how-to-review-endpoint-utterances.md) usługi LUIS na stronie **Przejrzyj wypowiedzi punktów końcowych,** aby ulepszyć aplikację.

Nie można zmienić aplikacji uruchomionej w kontenerze. Aby zmienić aplikację w kontenerze, należy zmienić aplikację w usłudze LUIS za pomocą portalu [usługi LUIS](https://www.luis.ai) lub użyć [interfejsów API tworzenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)usługi LUIS . Następnie trenuj i/lub publikuj, a następnie pobierz nowy pakiet i uruchom kontener ponownie.

Nie można wyeksportować aplikacji usługi LUIS wewnątrz kontenera z powrotem do usługi LUIS. Można przekazać tylko dzienniki zapytań. 

## <a name="export-packaged-app-from-luis"></a>Eksportowanie spakowanej aplikacji z usługi LUIS

Kontener usługi LUIS wymaga uczonej lub opublikowanej aplikacji usługi LUIS, aby odpowiedzieć na zapytania przewidywania wypowiedzi użytkowników. Aby uzyskać aplikację usługi LUIS, użyj interfejsu API uczony lub opublikowany pakiet. 

Domyślną lokalizacją `input` jest podkatalog w `docker run` odniesieniu do miejsca uruchomienia polecenia.  

Umieść plik pakietu w katalogu i odwołaj się do tego katalogu jako instalacji wejściowej po uruchomieniu kontenera platformy docker. 

### <a name="package-types"></a>Typy pakietów

Katalog instalacji wejściowej może zawierać modele **produkcyjne,** **przejściowe**i **wersjonowane** aplikacji jednocześnie. Wszystkie pakiety są zamontowane.

|Typ pakietu|Interfejs API punktu końcowego kwerendy|Dostępność kwerendy|Format nazwy pliku pakietu|
|--|--|--|--|
|Wersji|POBIERZ, OPUBLIKUJ|Tylko kontener|`{APP_ID}_v{APP_VERSION}.gz`|
|Przygotowanie|POBIERZ, OPUBLIKUJ|Platforma Azure i kontener|`{APP_ID}_STAGING.gz`|
|Produkcja|POBIERZ, OPUBLIKUJ|Platforma Azure i kontener|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Nie należy zmieniać nazwy, zmieniać, zastępować ani dekompresować plików pakietu usługi LUIS.

### <a name="packaging-prerequisites"></a>Wymagania wstępne w zakresie pakowania

Przed pakowaniem aplikacji usługi LUIS należy mieć następujące właściwości:

|Wymagania dotyczące opakowań|Szczegóły|
|--|--|
|Wystąpienie zasobu usługi Azure _Cognitive Services_|Obsługiwane regiony obejmują<br><br>Zachodnie stany`westus`USA ( )<br>Europa Zachodnia (`westeurope`)<br>Australia Wschodnia (`australiaeast`)|
|Wyszkolona lub opublikowana aplikacja usługi LUIS|Bez [nieobsługiwało zależności][unsupported-dependencies]. |
|Dostęp do systemu plików [komputera-hosta](#the-host-computer) |Komputer-host musi umożliwiać [montaż wejściowy](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Eksportowanie pakietu aplikacji z portalu usługi LUIS

Portal [usługi](https://www.luis.ai) LUIS umożliwia eksportowanie pakietu uczonej lub opublikowanej aplikacji.

### <a name="export-published-apps-package-from-luis-portal"></a>Eksportowanie pakietu opublikowanej aplikacji z portalu usługi LUIS

Pakiet opublikowanej aplikacji jest dostępny na stronie listy **Moje aplikacje.** 

1. Zaloguj się do [portalu](https://www.luis.ai)usługi LUIS .
1. Zaznacz pole wyboru po lewej stronie nazwy aplikacji na liście. 
1. Wybierz element **Eksportuj** z kontekstowego paska narzędzi nad listą.
1. Wybierz **opcję Eksportuj dla kontenera (GZIP).**
1. Wybierz środowisko **gniazda produkcyjnego** lub **miejsca przemieszczania**.
1. Pakiet jest pobierany z przeglądarki.

![Eksportowanie opublikowanego pakietu dla kontenera z menu Eksportuj strony aplikacji](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Eksportowanie pakietu aplikacji z wersji z portalu usługi LUIS

Pakiet aplikacji wersjona jest dostępny na stronie listy **Wersje.**

1. Zaloguj się do [portalu](https://www.luis.ai)usługi LUIS .
1. Wybierz aplikację na liście. 
1. Wybierz **pozycję Zarządzaj** na pasku nawigacyjnym aplikacji.
1. Wybierz **pozycję Wersje** na lewym pasku nawigacyjnym.
1. Zaznacz pole wyboru po lewej stronie nazwy wersji na liście.
1. Wybierz element **Eksportuj** z kontekstowego paska narzędzi nad listą.
1. Wybierz **opcję Eksportuj dla kontenera (GZIP).**
1. Pakiet jest pobierany z przeglądarki.

![Eksportowanie uczonego pakietu dla kontenera z menu Eksportuj strony Wersje](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Eksportowanie pakietu opublikowanej aplikacji z interfejsu API

Użyj następującej metody interfejsu API REST, aby spakować aplikację usługi LUIS, która została już [opublikowana.](luis-how-to-publish-app.md) Zastępując własne odpowiednie wartości dla symboli zastępczych w wywołaniu interfejsu API, korzystając z tabeli poniżej specyfikacji HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol zastępczy | Wartość |
|-------------|-------|
| **{APP_ID}** | Identyfikator aplikacji opublikowanej aplikacji usługi LUIS. |
| **{SLOT_NAME}** | Środowisko opublikowanej aplikacji usługi LUIS. Użyj jednej z następujących wartości:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Klucz tworzenia konta usługi LUIS dla opublikowanej aplikacji usługi LUIS.<br/>Klucz autora można uzyskać ze strony **Ustawienia użytkownika** w portalu usługi LUIS. |
| **{AZURE_REGION}** | Odpowiedni region platformy Azure:<br/><br/>`westus`- Zachodnie stany USA<br/>`westeurope`- Europa Zachodnia<br/>`australiaeast`- Australia Wschodnia |

Aby pobrać opublikowany pakiet, zapoznaj się z [dokumentacją interfejsu API tutaj][download-published-package]. Jeśli pobrano pomyślnie, odpowiedź jest plik pakietu usługi LUIS. Zapisz plik w lokalizacji magazynu określonej dla instalacji wejściowej kontenera. 

### <a name="export-versioned-apps-package-from-api"></a>Eksportowanie pakietu aplikacji wersjona z interfejsu API

Użyj następującej metody interfejsu API REST, aby spakować aplikację usługi LUIS, która została już [przeszkolona.](luis-how-to-train.md) Zastępując własne odpowiednie wartości dla symboli zastępczych w wywołaniu interfejsu API, korzystając z tabeli poniżej specyfikacji HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol zastępczy | Wartość |
|-------------|-------|
| **{APP_ID}** | Identyfikator aplikacji przeszkolonej aplikacji usługi LUIS. |
| **{APP_VERSION}** | Wersja aplikacji uczonej aplikacji usługi LUIS. |
| **{AUTHORING_KEY}** | Klucz tworzenia konta usługi LUIS dla opublikowanej aplikacji usługi LUIS.<br/>Klucz autora można uzyskać ze strony **Ustawienia użytkownika** w portalu usługi LUIS. |
| **{AZURE_REGION}** | Odpowiedni region platformy Azure:<br/><br/>`westus`- Zachodnie stany USA<br/>`westeurope`- Europa Zachodnia<br/>`australiaeast`- Australia Wschodnia |

Aby pobrać pakiet wersjonacyjny, zapoznaj się z [dokumentacją interfejsu API tutaj][download-versioned-package]. Jeśli pobrano pomyślnie, odpowiedź jest plik pakietu usługi LUIS. Zapisz plik w lokalizacji magazynu określonej dla instalacji wejściowej kontenera. 

## <a name="run-the-container-with-docker-run"></a>Uruchom pojemnik za pomocą`docker run`

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Szczegółowe informacje na temat sposobu uzyskania wartości `{ENDPOINT_URI}` `{API_KEY}` można znaleźć w przypadku [zbierania wymaganych parametrów.](#gathering-required-parameters)

[Dostępne](luis-container-configuration.md#example-docker-run-commands) są `docker run` przykłady polecenia.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* W tym przykładzie użyto `C:` katalogu poza dyskiem, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalogu wejściowego, może być konieczne udzielenie uprawnienia usługi docker. 
* Nie należy zmieniać kolejności argumentów, chyba że są zaznajomieni z kontenerów docker.
* Jeśli używasz innego systemu operacyjnego, użyj poprawnej konsoli/terminala, składni folderu dla instalacji i znaku kontynuacji linii dla systemu. W tych przykładach przyjęto założenie, `^`że konsola systemu Windows ma znak kontynuacji wiersza . Ponieważ kontener jest systemem operacyjnym Linux, instalacja docelowa używa składni folderów w stylu linuksa.

To polecenie:

* Uruchamia kontener z obrazu kontenera usługi LUIS
* Ładowanie aplikacji usługi LUIS z instalacji wejściowej w *c:\input*, znajdujące się na hoście kontenera
* Przydziela dwa rdzenie procesora i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Zapisuje kontener i dzienniki usługi LUIS do montażu wyjściowego w *c:\output*, znajdujące się na hoście kontenera
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

Dostępnych jest więcej `docker run` [przykładów](luis-container-configuration.md#example-docker-run-commands) polecenia. 

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).
> Wartość ApiKey jest **kluczem** ze strony **Zasoby platformy Azure** w portalu `Cognitive Services` usługi LUIS i jest również dostępna na stronie klucze zasobów platformy Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Interfejsy API punktu końcowego obsługiwane przez kontener

Wersje interfejsu API w wersji V2 i [V3](luis-migration-api-v3.md) są dostępne w kontenerze. 

## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

Kontener zawiera interfejsy API punktu końcowego przewidywania zapytań oparte na rest. Punkty końcowe dla opublikowanych (przemieszczania lub produkcji) aplikacje mają _inną_ trasę niż punkty końcowe dla aplikacji wersjonawanych.

Użyj hosta, `http://localhost:5000`dla interfejsów API kontenera.

# <a name="v3-prediction-endpoint"></a>[Punkt końcowy przewidywania V3](#tab/v3)

|Typ pakietu|Czasownik HTTP|Trasa|Parametry zapytania|
|--|--|--|--|
|Opublikowany|POBIERZ, OPUBLIKUJ|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Wersji|POBIERZ, OPUBLIKUJ|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Parametry kwerendy skonfigurować sposób i co jest zwracane w odpowiedzi na kwerendę:

|Parametr kwerendy|Typ|Przeznaczenie|
|--|--|--|
|`query`|ciąg|Wypowiedź użytkownika.|
|`verbose`|wartość logiczna|Wartość logiczna wskazująca, czy zwrócić wszystkie metadane dla przewidywanych modeli. Wartość domyślna to false.|
|`log`|wartość logiczna|Rejestruje zapytania, które mogą być później używane do [aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md). Wartość domyślna to false.|
|`show-all-intents`|wartość logiczna|Wartość logiczna wskazująca, czy zwrócić wszystkie intencje lub tylko intencje punktacji. Wartość domyślna to false.|

# <a name="v2-prediction-endpoint"></a>[Punkt końcowy przewidywania wersji V2](#tab/v2)

|Typ pakietu|Czasownik HTTP|Trasa|Parametry zapytania|
|--|--|--|--|
|Opublikowany|[POBIERZ](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [OPUBLIKUJ](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Wersji|POBIERZ, OPUBLIKUJ|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Parametry kwerendy skonfigurować sposób i co jest zwracane w odpowiedzi na kwerendę:

|Parametr kwerendy|Typ|Przeznaczenie|
|--|--|--|
|`q`|ciąg|Wypowiedź użytkownika.|
|`timezoneOffset`|numer|TimezoneOffset umożliwia [zmianę strefy czasowej](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) używanej przez wstępnie utworzony datetimeV2 jednostki.|
|`verbose`|wartość logiczna|Zwraca wszystkie intencje i ich wyniki, gdy ustawiono wartość true. Wartość domyślna jest false, która zwraca tylko górnej intencji.|
|`staging`|wartość logiczna|Zwraca kwerendę z wyników środowiska przejściowego, jeśli ustawiono wartość true. |
|`log`|wartość logiczna|Rejestruje zapytania, które mogą być później używane do [aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md). Wartość domyślna to „true”.|

***

### <a name="query-the-luis-app"></a>Zapytanie do aplikacji usługi LUIS

Przykładowe polecenie CURL do wykonywania zapytań o kontener dla opublikowanej aplikacji to:

# <a name="v3-prediction-endpoint"></a>[Punkt końcowy przewidywania V3](#tab/v3)

Aby zbadać model w gnieździe, użyj następującego interfejsu API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Aby wysyłać zapytania do środowiska `production` `staging` **przejściowego,** zastąp w trasie:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Aby zbadać wersję wersjonawki modelu, użyj następującego interfejsu API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Punkt końcowy przewidywania wersji V2](#tab/v2)

Aby zbadać model w gnieździe, użyj następującego interfejsu API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Aby wysyłć zapytania do środowiska **przemieszczania,** zmień wartość parametru ciągu kwerendy **przemieszczania** na true: 

`staging=true`

Aby zbadać wersję wersjonawki modelu, użyj następującego interfejsu API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Nazwa wersji ma maksymalnie 10 znaków i zawiera tylko znaki dozwolone w adresie URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importowanie dzienników punktów końcowych do aktywnego uczenia się

Jeśli dla kontenera usługi LUIS określono instalację wyjściową, pliki dziennika `{INSTANCE_ID}` zapytań aplikacji są zapisywane w katalogu wyjściowym, gdzie jest identyfikator kontenera. Dziennik zapytań aplikacji zawiera kwerendę, odpowiedź i sygnatury czasowe dla każdej kwerendy przewidywania przesłane do kontenera usługi LUIS. 

W poniższej lokalizacji przedstawiono strukturę zagnieżdżonego katalogu dla plików dziennika kontenera.
```
/output/luis/{INSTANCE_ID}/
```
 
W portalu usługi LUIS wybierz aplikację, a następnie wybierz **pozycję Importuj dzienniki punktów końcowych,** aby przekazać te dzienniki. 

![Importowanie plików dziennika kontenera do aktywnego uczenia się](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po przekazaniu dziennika [przejrzyj](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) wypowiedzi punktów końcowych w portalu usługi LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zamknąć kontener, w środowisku wiersza polecenia, w którym kontener jest uruchomiony, naciśnij **klawisze Ctrl+C**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomisz kontener z [włączoną instalacją](luis-container-configuration.md#mount-settings) wyjściową i rejestrowaniem, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które występują podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontener usługi LUIS wysyła informacje rozliczeniowe do platformy Azure przy użyciu zasobu _usług Cognitive Services_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](luis-container-configuration.md).

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do pobierania, instalowania i uruchamiania kontenerów rozumienia języka (LUIS). Podsumowanie:

* Zrozumienie języka (LUIS) zapewnia jeden kontener systemu Linux dla platformy Docker zapewniając prognozy zapytań punktu końcowego wypowiedzi.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR).
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Za pomocą interfejsu API REST można wysyłać zapytania do punktów końcowych kontenera, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje rozliczeniowe.

> [!IMPORTANT]
> Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [Konfigurowanie kontenerów](luis-container-configuration.md) pod kątem ustawień konfiguracji.
* Zobacz [ograniczenia kontenera usługi LUIS,](luis-container-limitations.md) aby uzyskać znane ograniczenia możliwości.
* Zapoznaj się [z rozwiązywaniem problemów](troubleshooting.md) związanych z funkcją usługi LUIS.
* Użyj większej liczby [kontenerów usług Cognitive Services](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
