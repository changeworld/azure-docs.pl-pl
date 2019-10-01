---
title: Kontenery platformy Docker — LUIS
titleSuffix: Azure Cognitive Services
description: Kontener LUIS ładuje aplikację przeszkolony lub opublikowaną do kontenera Docker i zapewnia dostęp do prognoz zapytania z punktów końcowych interfejsu API kontenera.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: a718a2335ed4928c6cd40ad4dc832eaf82bc6a2b
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695291"
---
# <a name="install-and-run-luis-docker-containers"></a>Instalowanie i uruchamianie kontenerów platformy Docker LUIS
 
Kontener Language Understanding (LUIS) ładuje swój przeszkolony lub opublikowany Language Understanding model. Jako [aplikacja Luis](https://www.luis.ai), kontener platformy Docker zapewnia dostęp do prognoz zapytania z punktów końcowych interfejsu API kontenera. Można zebrać dzienniki zapytań z kontenera i przekazać je z powrotem do aplikacji Language Understanding, aby zwiększyć dokładność przewidywania aplikacji.

Poniższy film wideo demonstruje użycie tego kontenera.

[Demonstracja @no__t 1Container dla Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić kontener LUIS, należy zwrócić uwagę na następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń `docker`.| 
|Azure @no__t — 0 zasobów i LUIS [spakowany plik aplikacji](luis-how-to-start-new-app.md#export-app-for-containers) |Aby można było używać kontenera, musisz mieć:<br><br>* Zasób platformy Azure _Cognitive Services_ i skojarzony klucz rozliczeniowy identyfikator URI punktu końcowego rozliczenia. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera. <br>* Przeszkolone lub opublikowana aplikacja spakowana jako zainstalowano dane wejściowe do kontenera ze skojarzonym IDENTYFIKATORem aplikacji. Spakowany plik można pobrać z portalu LUIS lub interfejsów API tworzenia. Jeśli otrzymujesz spakowaną aplikację LUIS z [interfejsów API tworzenia](#authoring-apis-for-package-file), będziesz również potrzebować _klucza tworzenia_.<br><br>Te wymagania są używane do przekazywania argumentów wiersza polecenia do następujących zmiennych:<br><br>**{AUTHORING_KEY}** : ten klucz służy do uzyskiwania spakowanej aplikacji z usługi Luis w chmurze i przekazywania dzienników zapytań z powrotem do chmury. Format jest `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : ten identyfikator jest używany do wybierania aplikacji. Format jest `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : ten klucz jest używany do uruchamiania kontenera. Klucz punktu końcowego można znaleźć w dwóch miejscach. Pierwszy to Azure Portal na liście kluczy zasobu _Cognitive Services_ . Klucz punktu końcowego jest również dostępny w portalu LUIS na stronie ustawień klucze i punkt końcowy. Nie używaj klucza początkowego.<br><br>**{ENDPOINT_URI}** : punkt końcowy określony na stronie Przegląd.<br><br>[Klucz tworzenia i klucz punktu końcowego](luis-boundaries.md#key-limits) mają różne cele. Nie należy ich używać zamiennie. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Tworzenie interfejsów API dla pliku pakietu

Tworzenie interfejsów API dla spakowanych aplikacji:

* [Interfejs API opublikowanego pakietu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Interfejs API pakietów bez publikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

Ten kontener obsługuje minimalne i zalecane wartości ustawień:

|Wbudowane| Minimalnie | Zalecane | TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|LUIS|1 rdzeń, 2 GB pamięci|1 rdzeń, 4 GB pamięci|20, 40|

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę

Rdzeń i pamięć odpowiadają ustawieniom `--cpus` i `--memory`, które są używane jako część polecenia `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera z `docker pull`

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z repozytorium `mcr.microsoft.com/azure-cognitive-services/luis`:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera.

Pełny opis dostępnych tagów, takich jak `latest` użyty w poprzednim poleceniu, znajduje się w temacie [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) on the Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

![Proces używania kontenera Language Understanding (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Pakiet eksportu](#export-packaged-app-from-luis) dla kontenera z Luis Portal lub interfejsów API Luis.
1. Przenieś plik pakietu do wymaganego katalogu **wejściowego** na [komputerze-hoście](#the-host-computer). Nie należy zmieniać nazwy, zmieniać, zastępować ani dekompresowanie pliku pakietu LUIS.
1. [Uruchom kontener](##run-the-container-with-docker-run)z wymaganymi ustawieniami _instalacji_ i rozliczeń. Więcej [przykładów](luis-container-configuration.md#example-docker-run-commands) polecenia `docker run` jest dostępnych. 
1. [Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint). 
1. Po zakończeniu pracy z kontenerem [zaimportuj dzienniki punktów końcowych](#import-the-endpoint-logs-for-active-learning) z instalacji wyjściowej w portalu Luis i [Zatrzymaj](#stop-the-container) kontener.
1. Aby ulepszyć aplikację, użyj [aktywnej uczenia](luis-how-to-review-endpoint-utterances.md) w portalu Luis na stronie **Przegląd punktu końcowego wyrażenia długości** .

Nie można zmienić aplikacji działającej w kontenerze. W kolejności zmiany aplikacji w kontenerze należy zmienić aplikację w usłudze LUIS przy użyciu portalu [Luis](https://www.luis.ai) lub użyć [interfejsów API tworzenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)Luis. Następnie Wyszkol i/lub Opublikuj, a następnie Pobierz nowy pakiet i ponownie uruchom kontener.

Nie można wyeksportować aplikacji LUIS wewnątrz kontenera z powrotem do usługi LUIS. Można przekazać tylko dzienniki zapytań. 

## <a name="export-packaged-app-from-luis"></a>Eksportuj spakowaną aplikację z LUIS

Kontener LUIS wymaga przeszkolonej lub opublikowanej aplikacji LUIS do odpowiedzi na zapytania predykcyjne wyrażenia długości użytkownika. Aby uzyskać aplikację LUIS, użyj interfejsu API pakietu przeszkolonego lub opublikowanego. 

Domyślną lokalizacją jest podkatalog `input` w odniesieniu do miejsca, w którym jest uruchamiane polecenie `docker run`.  

Umieść plik pakietu w katalogu i odwołuje się do tego katalogu jako instalacji wejściowej podczas uruchamiania kontenera Docker. 

### <a name="package-types"></a>Typy pakietów

Wejściowy katalog instalacji może zawierać jednocześnie **produkcyjne**, **przejściowe**i **przeszkolone** wersje aplikacji. Wszystkie pakiety są zainstalowane. 

|Typ pakietu|Interfejs API punktu końcowego zapytania|Dostępność zapytania|Format nazwy pliku pakietu|
|--|--|--|--|
|Szkole|POBIERZ, OPUBLIKUJ|Tylko kontener|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Przemieszczania|POBIERZ, OPUBLIKUJ|Platforma Azure i kontener|`{APPLICATION_ID}_STAGING.gz`|
|Narzędzi|POBIERZ, OPUBLIKUJ|Platforma Azure i kontener|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Nie zmieniaj nazw, nie zmieniaj, zastępuj ani nie Kompresuj plików pakietu LUIS.

### <a name="packaging-prerequisites"></a>Wymagania wstępne dotyczące pakowania

Przed spakowaniem aplikacji LUIS należy wykonać następujące czynności:

|Wymagania dotyczące pakietów|Szczegóły|
|--|--|
|Wystąpienie zasobów usługi Azure _Cognitive Services_|Obsługiwane regiony obejmują<br><br>Zachodnie stany USA (`westus`)<br>Europa Zachodnia (`westeurope`)<br>Australia Wschodnia (`australiaeast`)|
|Przeszkolone lub opublikowana aplikacja LUIS|Bez [nieobsługiwanych zależności](#unsupported-dependencies). |
|Dostęp do systemu plików [komputera hosta](#the-host-computer) |Komputer hosta musi zezwalać na [instalację wejściową](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Eksportuj pakiet aplikacji z portalu LUIS

[Portal](https://www.luis.ai) Luis umożliwia eksportowanie pakietu przeszkolonej lub opublikowanej aplikacji. 

### <a name="export-published-apps-package-from-luis-portal"></a>Eksportuj pakiet opublikowanej aplikacji z portalu LUIS

Pakiet opublikowanej aplikacji jest dostępny na stronie listy **Moje aplikacje** . 

1. Zaloguj się do [portalu](https://www.luis.ai)Luis.
1. Zaznacz pole wyboru po lewej stronie nazwy aplikacji na liście. 
1. Wybierz element **Eksportuj** z kontekstowego paska narzędzi powyżej listy.
1. Wybierz pozycję **Eksportuj dla kontenera (gzip)** .
1. Wybierz środowisko **miejsca produkcyjnego** lub **miejsca przejściowego**.
1. Pakiet zostanie pobrany z przeglądarki.

![Wyeksportuj opublikowany pakiet dla kontenera z menu Eksportuj strony aplikacji](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Eksportuj pakiet przeszkolonej aplikacji z portalu LUIS

Pakiet przeszkolonej aplikacji jest dostępny na stronie listy **wersji** . 

1. Zaloguj się do [portalu](https://www.luis.ai)Luis.
1. Wybierz aplikację z listy. 
1. Wybierz pozycję **Zarządzaj** na pasku nawigacyjnym aplikacji.
1. Wybierz pozycję **wersje** na lewym pasku nawigacyjnym.
1. Zaznacz pole wyboru po lewej stronie nazwy wersji na liście.
1. Wybierz element **Eksportuj** z kontekstowego paska narzędzi powyżej listy.
1. Wybierz pozycję **Eksportuj dla kontenera (gzip)** .
1. Pakiet zostanie pobrany z przeglądarki.

![Eksportuj przeszkolony pakiet dla kontenera z menu Eksportuj strony wersje](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Eksportuj pakiet opublikowanej aplikacji z interfejsu API

Użyj następującej metody interfejsu API REST, aby spakować aplikację LUIS, która została już [opublikowana](luis-how-to-publish-app.md). Podstawianie własnych odpowiednich wartości dla symboli zastępczych w wywołaniu interfejsu API przy użyciu tabeli poniżej specyfikacji protokołu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol | Wartość |
|-------------|-------|
| **{APPLICATION_ID}** | Identyfikator aplikacji opublikowanej aplikacji LUIS. |
| **{APPLICATION_ENVIRONMENT}** | Środowisko opublikowanej aplikacji LUIS. Użyj jednej z następujących wartości:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Klucz tworzenia konta LUIS dla opublikowanej aplikacji LUIS.<br/>Swój klucz tworzenia można pobrać ze strony **ustawień użytkownika** w portalu Luis. |
| **{AZURE_REGION}** | Odpowiedni region świadczenia usługi Azure:<br/><br/>`westus` — zachodnie stany USA<br/>`westeurope` — Europa Zachodnia<br/>`australiaeast` — Australia Wschodnia |

Aby pobrać opublikowany pakiet, zapoznaj się z [dokumentacją interfejsu API w tym miejscu][download-published-package]. Jeśli pobrano pomyślnie, odpowiedź to plik pakietu LUIS. Zapisz plik w lokalizacji magazynu określonej dla instalacji wejściowej kontenera. 

### <a name="export-trained-apps-package-from-api"></a>Eksportuj pakiet przeszkolonej aplikacji z interfejsu API

Użyj następującej metody interfejsu API REST, aby spakować aplikację LUIS, która została już [przeszkolone](luis-how-to-train.md). Podstawianie własnych odpowiednich wartości dla symboli zastępczych w wywołaniu interfejsu API przy użyciu tabeli poniżej specyfikacji protokołu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol | Wartość |
|-------------|-------|
| **{APPLICATION_ID}** | Identyfikator aplikacji wyszkolonej aplikacji LUIS. |
| **{APPLICATION_VERSION}** | Wersja aplikacji z przeszkolonej aplikacji LUIS. |
| **{AUTHORING_KEY}** | Klucz tworzenia konta LUIS dla opublikowanej aplikacji LUIS.<br/>Swój klucz tworzenia można pobrać ze strony **ustawień użytkownika** w portalu Luis. |
| **{AZURE_REGION}** | Odpowiedni region świadczenia usługi Azure:<br/><br/>`westus` — zachodnie stany USA<br/>`westeurope` — Europa Zachodnia<br/>`australiaeast` — Australia Wschodnia |

Aby pobrać przeszkolony pakiet, zapoznaj się z [dokumentacją interfejsu API w tym miejscu][download-trained-package]. Jeśli pobrano pomyślnie, odpowiedź to plik pakietu LUIS. Zapisz plik w lokalizacji magazynu określonej dla instalacji wejściowej kontenera. 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener z `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat pobierania wartości `{ENDPOINT_URI}` i `{API_KEY}`.

[Przykłady](luis-container-configuration.md#example-docker-run-commands) `docker run` polecenia są dostępne.

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

* W tym przykładzie używa się katalogu poza dyskiem `C:`, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalogu wejściowego, może być konieczne przyznanie uprawnienia usługi Docker. 
* Nie zmieniaj kolejności argumentów, chyba że znasz kontenery Docker.
* W przypadku korzystania z innego systemu operacyjnego należy użyć właściwej konsoli/terminalu, składni folderu dla instalacji i znaku kontynuacji wiersza dla systemu. W poniższych przykładach przyjęto założenie, że konsola systemu Windows ma znak kontynuacji wiersza `^`. Ponieważ kontener jest systemem operacyjnym Linux, instalacja docelowa używa składni folderu w stylu systemu Linux.

To polecenie:

* Uruchamia kontener z obrazu kontenera LUIS
* Ładuje aplikację LUIS z instalacji wejściowej w lokalizacji *C:\input*, która znajduje się na hoście kontenera
* Przydziela dwa rdzenie procesora CPU i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Zapisuje dzienniki kontenerów i LUIS na potrzeby instalacji wyjściowej w *C:\output*, znajdującej się na hoście kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

Więcej [przykładów](luis-container-configuration.md#example-docker-run-commands) polecenia `docker run` jest dostępnych. 

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing` i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).
> Wartość ApiKey jest **kluczem** ze strony **zasobów platformy Azure** w portalu Luis i jest również dostępna na stronie klucze zasobów platformy Azure `Cognitive Services`.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Interfejsy API punktu końcowego obsługiwane przez kontener

Dla tego kontenera są dostępne zarówno wersje 2, jak i [v3 (wersja zapoznawcza)](luis-migration-api-v3.md) interfejsu API. 

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

Kontener udostępnia interfejsy API punktu końcowego przewidywania zapytań. Punkty końcowe dla opublikowanych aplikacji (przemieszczania lub produkcji) mają _różną_ trasę niż punkty końcowe dla przeszkolonych aplikacji. 

Użyj hosta `http://localhost:5000` w przypadku interfejsów API kontenerów. 

|Typ pakietu|Metoda|Szlak|Parametry zapytania|
|--|--|--|--|
|Publikacj|[Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Opublikuj](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q = {q}<br>& przemieszczanie<br>[& timezoneOffset]<br>[& verbose]<br>[Dziennik &]<br>|
|Szkole|POBIERZ, OPUBLIKUJ|/luis/v2.0/apps/{appId}/versions/{versionId}?|q = {q}<br>[& timezoneOffset]<br>[& verbose]<br>[Dziennik &]|

Parametry zapytania konfigurują sposób i wartość zwracaną w odpowiedzi na zapytanie:

|Parametr zapytania|Typ|Cel|
|--|--|--|
|`q`|string|Wypowiedź użytkownika.|
|`timezoneOffset`|liczba|TimezoneOffset umożliwia [zmianę strefy czasowej](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) używanej przez wstępnie utworzoną jednostkę datetimeV2.|
|`verbose`|wartość logiczna|Zwraca wszystkie intencje i ich wyniki po ustawieniu na wartość true. Wartość domyślna to false, która zwraca tylko najwyższy cel.|
|`staging`|wartość logiczna|Zwraca zapytanie z wyników środowiska przejściowego, jeśli ma wartość true. |
|`log`|wartość logiczna|Rejestruje zapytania, które mogą być używane później w przypadku [aktywnej nauki](luis-how-to-review-endpoint-utterances.md). Wartość domyślna to true.|

### <a name="query-published-app"></a>Zbadaj opublikowaną aplikację

Przykładowe polecenie ZWINIĘCIE dla kwerendy kontenera dla opublikowanej aplikacji to:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Aby wykonać zapytania w środowisku **przejściowym** , Zmień wartość parametru ciągu zapytania **przemieszczania** na true: 

`staging=true`

### <a name="query-trained-app"></a>Zbadaj przeszkolone aplikacje

Przykładowe polecenie ZWINIĘCIE dla kwerendy kontenera dla przeszkolonej aplikacji to: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Nazwa wersji ma maksymalnie 10 znaków i zawiera tylko znaki dozwolone w adresie URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importowanie dzienników punktów końcowych dla usługi Active Learning

Jeśli dla kontenera LUIS określono instalację wyjściową, pliki dziennika zapytania aplikacji są zapisywane w katalogu wyjściowym, gdzie `{INSTANCE_ID}` to identyfikator kontenera. Dziennik zapytań aplikacji zawiera zapytanie, odpowiedź i sygnatury czasowe dla każdego zapytania predykcyjnego przesłanego do kontenera LUIS. 

W następującej lokalizacji przedstawiono zagnieżdżoną strukturę katalogów dla plików dziennika kontenera.
```
/output/luis/{INSTANCE_ID}/
```
 
W portalu LUIS wybierz aplikację, a następnie wybierz pozycję **Importuj dzienniki punktów końcowych** , aby przekazać te dzienniki. 

![Importowanie plików dziennika kontenera do usługi Active Learning](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po przekazaniu dziennika [zapoznaj się z punktem końcowym](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) wyrażenia długości w portalu Luis.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymaj kontener

Aby zamknąć kontener, w środowisku wiersza polecenia, w którym jest uruchomiony kontener, naciśnij **klawisze CTRL + C**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](luis-container-configuration.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Godzin

Kontener LUIS wysyła informacje o rozliczeniach do platformy Azure przy użyciu zasobu _Cognitive Services_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Obsługiwane zależności dla kontenera `latest`

Najnowszy kontener, wydanego o 2019 Build, będzie obsługiwał:

* [Nowe prebudowane domeny](luis-reference-prebuilt-domains.md): te domeny ukierunkowane na przedsiębiorstwa obejmują jednostki, przykładowe wyrażenia długości i wzorce. Rozwiń te domeny do własnych potrzeb. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Nieobsługiwane zależności dla kontenera `latest`

Aby [wyeksportować kontener](#export-packaged-app-from-luis), należy usunąć nieobsługiwane zależności z aplikacji Luis. Podczas próby eksportowania dla kontenera Portal LUIS zgłasza te Nieobsługiwane funkcje, które należy usunąć.

Aplikacji LUIS można użyć, jeśli **nie zawiera** ona żadnych z następujących zależności:

Nieobsługiwane konfiguracje aplikacji|Szczegóły|
|--|--|
|Nieobsługiwane kultury kontenerów| Holenderski (NL-NL)<br>Japoński (ja-JP)<br>Język niemiecki jest obsługiwany tylko w przypadku [1.0.2 tokenizatora](luis-language-support.md#custom-tokenizer-versions).|
|Nieobsługiwane jednostki dla wszystkich kultur|Wstępnie utworzona jednostka [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) dla wszystkich kultur|
|Nieobsługiwane jednostki dla kultury angielskiej (EN-US)|Wstępnie skompilowane jednostki [GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2)|
|Napełnianiu mowy|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Analiza tonacji|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Sprawdzanie pisowni Bing|Zależności zewnętrzne nie są obsługiwane w kontenerze.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów Language Understanding (LUIS). Podsumowanie:

* Language Understanding (LUIS) udostępnia jeden kontener systemu Linux dla platformy Docker, który udostępnia przewidywania zapytań punktu końcowego wyrażenia długości.
* Obrazy kontenerów są pobierane z Container Registry firmy Microsoft (MCR).
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Za pomocą interfejsu API REST można badać punkty końcowe kontenera, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](luis-container-configuration.md) dla ustawień konfiguracji
* [Rozwiązywanie](troubleshooting.md) problemów związanych z działaniem funkcji Luis można znaleźć w rozwiązaniu.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip