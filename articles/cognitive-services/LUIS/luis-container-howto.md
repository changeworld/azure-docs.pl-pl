---
title: Kontenerów Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: Kontener usługi LUIS spowoduje załadowanie aplikacji uczonego lub opublikowane w kontenerze platformy docker i zapewnia dostęp do przewidywania zapytania z punktów końcowych interfejsu API kontenera.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dapine
ms.openlocfilehash: 86b23c5f69fd96fe5c5614d99483e1936895ad9e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537096"
---
# <a name="install-and-run-luis-docker-containers"></a>Zainstaluj i uruchom usługi LUIS kontenerów platformy docker
 
Kontener Language Understanding (LUIS) ładuje modelu interpretacji języka przeszkolonych lub opublikowany, znany również jako [aplikacją usługi LUIS](https://www.luis.ai), w kontenerze platformy docker i zapewnia dostęp do przewidywania zapytania z interfejsu API kontenera punkty końcowe. Możesz zbierać dzienniki zapytań z kontenera i przekazać te wstecz do aplikacji interpretacji języka w celu zwiększenia dokładności prognozy aplikacji.

Poniższy klip wideo pokazuje, za pomocą tego kontenera.

[![Pokaz kontener dla usług Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było uruchomić kontener usługi LUIS, musisz mieć następujące czynności: 

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.| 
|Azure `Cognitive Services` zasobów i LUIS [spakowanych aplikacji](luis-how-to-start-new-app.md#export-app-for-containers) pliku |Aby można było używać kontenera, musisz mieć:<br><br>* A _usług Cognitive Services_ zasobów platformy Azure i skojarzone opłaty klucz rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach przeglądu i klucze zasobu i wymagane do uruchomienia kontenera. Musisz dodać `luis/v2.0` routingu do identyfikator URI punktu końcowego, jak pokazano w poniższym przykładzie BILLING_ENDPOINT_URI. <br>* Uczonego opublikowanych aplikacją lub spakowany jako zainstalowanego dane wejściowe do kontenera z jego skojarzony identyfikator aplikacji. Pakowanego pliku można uzyskać z portalu usługi LUIS lub do tworzenia interfejsów API. Jeśli otrzymujesz spakowanych aplikacji usługi LUIS z [Tworzenie interfejsów API](#authoring-apis-for-package-file), należy również swoje _tworzenia klucza_.<br><br>Te wymagania są używane do przekazywania argumentów wiersza polecenia do następujących zmiennych:<br><br>**{AUTHORING_KEY}** : Ten klucz jest używany do pobrania spakowanych aplikacji z usługi LUIS w chmurze, a następnie przekaż dzienniki zapytań do chmury. Format jest `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Ten identyfikator jest używany do wybierz aplikację. Format jest `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}** : Ten klucz jest używany do uruchomienia kontenera. Klucz punktu końcowego można znaleźć w dwóch miejscach. Pierwsza to witryny Azure portal w ramach _usług Cognitive Services_ listy kluczy zasobu. Klucz punktu końcowego jest również dostępna w portalu usługi LUIS na klucze i punktu końcowego strony ustawień. Nie należy używać klucza starter.<br><br>**{BILLING_ENDPOINT}** : Na przykład: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>[Tworzenia klucza i klucza punktu końcowego](luis-boundaries.md#key-limits) różnym przeznaczeniu. Nie należy używać ich zamiennie. |

### <a name="authoring-apis-for-package-file"></a>Tworzenie interfejsów API dla pliku pakietu

Tworzenie interfejsów API dla spakowanych aplikacji:

* [Opublikowany pakiet interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Opublikowane nie należy korzystać tylko skonfigurowanych pod kątem pakietu interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

Ten kontener obsługuje minimalne i zalecane wartości ustawień:

|Kontener| Minimalne | Zalecane | TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|LUIS|1 rdzeń, 2 GB pamięci RAM|1 rdzeń, 4 GB pamięci RAM|20,40|

* Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.
* TPS - transakcji na sekundę

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Użyj [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z `mcr.microsoft.com/azure-cognitive-services/luis` repozytorium:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Użyj [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera.

Aby uzyskać pełny opis dostępnych tagów takich jak `latest` używane w poprzednim poleceniu, zobacz [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) w usłudze Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

![Proces za pomocą kontenera Language Understanding (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Eksportuj pakiet](#export-packaged-app-from-luis) dla kontenera z portalu usługi LUIS lub interfejsów API usługi LUIS.
1. Przenieś plik pakietu do wymaganych **wejściowych** katalogu [komputerze-hoście](#the-host-computer). Nie zmiany nazwy, alter, Zastąp lub zdekompresuj plik pakietu usługi LUIS.
1. [Uruchom kontener](##run-the-container-with-docker-run), za pomocą wymaganych _wejściowych instalacji_ i rozliczeń ustawienia. Więcej [przykłady](luis-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 
1. [Wykonywanie zapytań endpoint prognoz kontenera](#query-the-containers-prediction-endpoint). 
1. Po wykonaniu tych czynności za pomocą kontenera, [importować dzienniki punktu końcowego](#import-the-endpoint-logs-for-active-learning) z danych wyjściowych instalacji w portalu usługi LUIS i [zatrzymać](#stop-the-container) kontenera.
1. Użyj usługi LUIS portal [aktywne uczenie](luis-how-to-review-endpoint-utterances.md) na **Przejrzyj wypowiedzi punktu końcowego** strony, aby udoskonalać aplikację.

Nie można zmienić aplikacji działającej w kontenerze. W kolejności zmiany aplikacji w kontenerze, należy zmodyfikować aplikację tak, przy użyciu usługi LUIS [LUIS](https://www.luis.ai) portalu lub użyj usługi LUIS [Tworzenie interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Następnie szkolenie i/lub opublikować, a następnie Pobierz nowy pakiet i ponownie uruchom kontenera.

Nie można wyeksportować aplikacji usługi LUIS wewnątrz kontenera do usługi LUIS. Można przekazać tylko dzienniki zapytań. 

## <a name="export-packaged-app-from-luis"></a>Eksportowanie spakowanych aplikacji z usługi LUIS

Kontener usługi LUIS wymaga uczonego lub opublikowaną aplikacją usługi LUIS do odpowiadania na kwerendy prognozowania z wypowiedzi użytkowników. Aby uzyskać aplikację usługi LUIS, korzystać uczonego lub opublikowany pakiet interfejsu API. 

Domyślna lokalizacja to `input` podkatalogu w odniesieniu do której uruchomiono `docker run` polecenia.  

Umieść plik pakietu w katalogu i odwołać ten katalog jako danych wejściowych instalacji po uruchomieniu kontenera platformy docker. 

### <a name="package-types"></a>Typy pakietów

Katalog instalacji danych wejściowych może zawierać **produkcji**, **przemieszczania**, i **Trained** wersji aplikacji jednocześnie. Wszystkie pakiety są instalowane. 

|Typ pakietu|Punkt końcowy interfejsu API zapytań|Dostępność zapytań|Format nazwy pliku pakietu|
|--|--|--|--|
|Przeszkoleni|GET, Post|Kontenera|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Przygotowanie|GET, Post|Platformy Azure i kontenerów|`{APPLICATION_ID}_STAGING.gz`|
|Produkcja|GET, Post|Platformy Azure i kontenerów|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Nie zmiany nazwy, alter, Zastąp lub zdekompresować plików pakietu usługi LUIS.

### <a name="packaging-prerequisites"></a>Wymagania wstępne dotyczące tworzenia pakietów

Przed spakowaniem aplikacją usługi LUIS, należy dysponować następującymi elementami:

|Wymagania dotyczące opakowania|Szczegóły|
|--|--|
|Azure _usług Cognitive Services_ wystąpienia zasobu|Obsługiwane regiony obejmują<br><br>Zachodnie stany USA (```westus```)<br>Europa Zachodnia (```westeurope```)<br>Australia Wschodnia (```australiaeast```)|
|Uczony lub opublikowaną aplikacją usługi LUIS|Bez [nieobsługiwany zależności](#unsupported-dependencies). |
|Dostęp do [komputerze-hoście](#the-host-computer)przez system plików |Komputer-host musi zezwalać na [wejściowych instalacji](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Eksportuj pakiet aplikacji z portalu usługi LUIS

Usługa LUIS [portal](https://www.luis.ai) zapewnia możliwość eksportowania pakietu aplikacji uczonego lub opublikowane. 

### <a name="export-published-apps-package-from-luis-portal"></a>Eksportuj pakiet opublikowanej aplikacji z portalu usługi LUIS

Pakiet opublikowanej aplikacji jest dostępny z **Moje aplikacje** stronie listy. 

1. Zaloguj się do usługi LUIS [portal](https://www.luis.ai).
1. Na liście, zaznacz pole wyboru po lewej stronie nazwy aplikacji. 
1. Wybierz **wyeksportować** elementu z kontekstowych narzędzi powyżej listy.
1. Wybierz **eksportu dla kontenera (GZIP)** .
1. Wybierz środowisko z **miejscem produkcyjnym** lub **miejsce przejściowe**.
1. Pakiet jest pobierana z przeglądarki.

![Eksportowanie opublikowany pakiet dla kontenera, z menu eksportowania na stronie aplikacji](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Eksportuj pakiet uczonego aplikacji z portalu usługi LUIS

Pakiet aplikacji uczonego jest dostępne z **wersji** stronie listy. 

1. Zaloguj się do usługi LUIS [portal](https://www.luis.ai).
1. Wybierz aplikację na liście. 
1. Wybierz **Zarządzaj** na pasku nawigacyjnym aplikacji.
1. Wybierz **wersji** na pasku nawigacyjnym po lewej stronie.
1. Zaznacz pole wyboru po lewej stronie nazwy wersji na liście.
1. Wybierz **wyeksportować** elementu z kontekstowych narzędzi powyżej listy.
1. Wybierz **eksportu dla kontenera (GZIP)** .
1. Pakiet jest pobierana z przeglądarki.

![Wyeksportuj pakiet uczonego w kontenerze menu eksportowania na stronie wersji](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Eksportuj pakiet opublikowanej aplikacji z interfejsu API

Użyj następującej metody interfejsu API REST do pakietu z aplikacją usługi LUIS, że masz już [opublikowane](luis-how-to-publish-app.md). Podstawiając odpowiednie wartości dla symboli zastępczych w wywołaniu interfejsu API przy użyciu tabeli poniżej specyfikację protokołu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol zastępczy | Wartość |
|-------------|-------|
|{APPLICATION_ID} | Identyfikator aplikacji opublikowanej aplikacji usługi LUIS. |
|{APPLICATION_ENVIRONMENT} | Środowisko opublikowanej aplikacji usługi LUIS. Użyj jednej z następujących wartości:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Tworzenia klucza konta usługi LUIS do opublikowanej aplikacji usługi LUIS.<br/>Możesz uzyskać klucz tworzenia pakietów administracyjnych z **ustawienia użytkownika** strony w portalu usługi LUIS. |
|{AZURE_REGION} | Odpowiedni region platformy Azure:<br/><br/>```westus``` -Zachodnie stany USA<br/>```westeurope``` -Zachodnia Europa<br/>```australiaeast``` — Australia Wschodnia |

Pobierz pakiet opublikowane, można znaleźć w temacie [w tej dokumentacji interfejsu API][download-published-package]. Pomyślnie pobrano odpowiedzi czy plik pakietu usługi LUIS. Zapisz plik w lokalizacji przechowywania określona dla danych wejściowych instalacji kontenera. 

### <a name="export-trained-apps-package-from-api"></a>Eksportuj pakiet aplikacji uczony z interfejsu API

Użyj następującej metody interfejsu API REST do pakietu aplikacji usługi LUIS, że masz już [uczonego](luis-how-to-train.md). Podstawiając odpowiednie wartości dla symboli zastępczych w wywołaniu interfejsu API przy użyciu tabeli poniżej specyfikację protokołu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Symbol zastępczy | Wartość |
|-------------|-------|
|{APPLICATION_ID} | Identyfikator aplikacji uczonego aplikacji usługi LUIS. |
|{APPLICATION_VERSION} | Wersja aplikacji uczonego aplikacji usługi LUIS. |
|{AUTHORING_KEY} | Tworzenia klucza konta usługi LUIS do opublikowanej aplikacji usługi LUIS.<br/>Możesz uzyskać klucz tworzenia pakietów administracyjnych z **ustawienia użytkownika** strony w portalu usługi LUIS.  |
|{AZURE_REGION} | Odpowiedni region platformy Azure:<br/><br/>```westus``` -Zachodnie stany USA<br/>```westeurope``` -Zachodnia Europa<br/>```australiaeast``` — Australia Wschodnia |

Pobierz pakiet przeszkolonych, można znaleźć w temacie [w tej dokumentacji interfejsu API][download-trained-package]. Pomyślnie pobrano odpowiedzi czy plik pakietu usługi LUIS. Zapisz plik w lokalizacji przechowywania określona dla danych wejściowych instalacji kontenera. 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie do uruchomienia kontenera. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{ENDPOINT_KEY} | Ten klucz jest używany do uruchomienia kontenera. Nie należy używać klucza starter. |
|{BILLING_ENDPOINT} | Rozliczeń wartość punktu końcowego jest dostępna w witrynie Azure portal `Cognitive Services` strona przeglądu. Należy dodać `luis/v2.0` routingu do identyfikator URI punktu końcowego, jak pokazano w poniższym przykładzie: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia. Uruchom polecenie w konsoli programu Windows.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={BILLING_ENDPOINT} ^
ApiKey={ENDPOINT_KEY}
```

* W tym przykładzie użyto katalogu poza `C:` dysku, aby uniknąć konfliktów uprawnień na Windows. Jeśli musisz użyć określonego katalogu jako katalog wejściowy może być konieczne przyznanie platformy docker usługi uprawnienia. 
* Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.
* Jeśli używasz innego systemu operacyjnego na użytek poprawne konsoli/terminal, składnia folderu instalacji i wstawić znak kontynuacji wiersza w systemie. W poniższych przykładach założono konsoli Windows za pomocą znak kontynuacji wiersza `^`. Ponieważ kontener jest system operacyjny Linux, instalacji docelowy używa składni folderu stylu dla systemu Linux.

To polecenie:

* Uruchamia kontener z usługi LUIS obrazu kontenera
* Ładuje aplikacją usługi LUIS z danych wejściowych instalacji na c:\input, znajdujących się na hoście kontenera
* Przydziela dwa rdzenie procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Zapisuje dzienniki, aby dane wyjściowe instalacji na c:\output, znajduje się na hoście kontenera, kontenerów i LUIS
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście. 

Więcej [przykłady](luis-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).
> Wartość ApiKey **klucz** z kluczy i punkty końcowe strony w portalu usługi LUIS i jest również dostępna na platformie Azure `Cognitive Services` strony klucze zasobu.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Interfejsy API obsługiwane przez kontener punktu końcowego

Zarówno w wersji 2 i [V3 (wersja zapoznawcza)](luis-migration-api-v3.md) wersje interfejsu API są dostępne z kontenerem. 

## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

Kontener udostępnia punkt końcowy usługi oparte na protokole REST zapytania prognozowania interfejsów API. Punkty końcowe dla opublikowanych aplikacji (przejściowych lub produkcyjnych) mają _różnych_ trasy niż punktów końcowych dla uczonego aplikacji. 

Użyj hosta, `https://localhost:5000`, dla kontenera interfejsów API. 

|Typ pakietu|Metoda|Trasa|Parametry zapytania|
|--|--|--|--|
|Opublikowane|[Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>& przemieszczania<br>[& timezoneOffset]<br>[& pełne]<br>[& dziennika]<br>|
|Przeszkoleni|GET, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[& timezoneOffset]<br>[& pełne]<br>[& dziennika]|

Skonfiguruj parametry zapytania jak i co to jest zwracany w odpowiedzi na zapytanie:

|Parametr zapytania|Typ|Przeznaczenie|
|--|--|--|
|`q`|string|Wypowiedź użytkownika.|
|`timezoneOffset`|numer|TimezoneOffset umożliwia [Zmień strefę czasową](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) posługują się datetimeV2 wstępnie utworzone jednostki.|
|`verbose`|boolean|Zwraca wszystkie intencje i ich wyniki, gdy ustawiona na wartość true. Wartość domyślna to false, która zwraca górną intencji.|
|`staging`|boolean|Zapytanie zwraca wyniki w środowisku przejściowym, jeśli jest ustawiona na wartość true. |
|`log`|boolean|Rejestruje zapytania, których można korzystać później podczas [aktywne uczenie](luis-how-to-review-endpoint-utterances.md). Domyślna to true.|

### <a name="query-published-app"></a>Opublikowana aplikacja zapytania

Przykład polecenia CURL do wykonywania zapytań kontener dla opublikowanej aplikacji to:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Tworzenie kwerend do **przemieszczania** środowiska, zmiana **przemieszczania** zbadać wartości parametru ciągu na wartość true: 

`staging=true`

### <a name="query-trained-app"></a>Aplikacja uczonego zapytania

Przykład polecenia CURL do wykonywania zapytań kontener dla aplikacji uczonego to: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Nazwa wersji może zawierać maksymalnie 10 znaków i zawiera tylko znaki dozwolone w adresie URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importowanie dzienników punktu końcowego dla aktywne uczenie

Jeśli dane wyjściowe instalacji jest określony dla kontenera usługi LUIS, pliki dziennika zapytań aplikacji są zapisywane w katalogu wyjściowym, gdzie {właściwość INSTANCE_ID} to identyfikator kontenera. Dziennik zapytań aplikacji zawiera zapytania, odpowiedzi i sygnatury czasowe dla każdej prognozy przesłano zapytanie do kontenera usługi LUIS. 

Następującej lokalizacji przedstawia strukturę zagnieżdżonych katalog dla plików dziennika kontenera.
```
/output/luis/{INSTANCE_ID}/
```
 
Wybierz aplikację z portalu usługi LUIS, a następnie wybierz **importować dzienniki do endpoint** przekazywania tych dzienników. 

![Zaimportuj pliki dziennika kontenera, aktywne uczenie](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po przekazaniu dziennika [Przejrzyj punktu końcowego](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) wypowiedzi w portalu usługi LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavit kontejner

Do zamykania w kontenerze, w środowisko wiersza polecenia, na którym działa kontener, naciśnij klawisz **klawisze Ctrl + C**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z danymi wyjściowymi [instalacji](luis-container-configuration.md#mount-settings) i Rejestrowanie włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera. 

## <a name="billing"></a>Rozliczenia

Wysyła kontenera usługi LUIS rozliczeń informacje na platformie Azure, przy użyciu _usług Cognitive Services_ zasobów dla konta systemu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Obsługiwane zależności dla `latest` kontenera

Najnowszy kontener, wydana w 2019 r / / kompilacji, będzie obsługiwać:

* Sprawdzanie pisowni Bing: żądania do punktu końcowego prognozowania zapytania przy użyciu `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` parametry ciągu zapytania. Użyj [sprawdzania pisowni Bing w wersji 7 samouczek](luis-tutorial-bing-spellcheck.md) Aby dowiedzieć się więcej. Jeśli ta funkcja jest używana, kontener wysyła wypowiedź do zasobu sprawdzanie pisowni Bing w wersji 7.
* [Nowe ze wstępnie utworzonych domen](luis-reference-prebuilt-domains.md): te skoncentrowane na enterprise domeny obejmują jednostek, przykład wypowiedzi i wzorce. Rozszerzenie tych domen na własny użytek. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Nieobsługiwane zależności dla `latest` kontenera

Jeśli aplikacją usługi LUIS ma nieobsługiwany zależności, nie będzie mógł [eksportu dla kontenera](#export-packaged-app-from-luis) dopóki nie usuniesz nieobsługiwanych funkcji. Przy próbie eksportu dla kontenera, w portalu usługi LUIS zgłasza nieobsługiwane funkcje, które należy usunąć.

Możesz użyć aplikacji usługi LUIS, jeśli jego **nie obejmuje** żadnego z następujących zależności:

Konfiguracje nieobsługiwanych aplikacji|Szczegóły|
|--|--|
|Nieobsługiwana kontenera kultur| Holenderski (nl-NL)<br>Japoński (ja-JP)<br>Niemiecki jest obsługiwany tylko z [1.0.2 tokenizatora](luis-language-support.md#custom-tokenizer-versions).|
|Nieobsługiwane jednostki dla wszystkich języków|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) wstępnie utworzone jednostki dla wszystkich języków|
|Nieobsługiwane jednostki dla kultury angielski (en US)|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) ze wstępnie utworzonych jednostek|
|Zalewanie mowy|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Analiza tonacji|Zależności zewnętrzne nie są obsługiwane w kontenerze.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów Language Understanding (LUIS). Podsumowanie:

* Language Understanding (LUIS) oferuje jeden kontener systemu Linux platformy Docker udostępnianie punktu końcowego zapytania przewidywań wypowiedzi.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR).
* Obrazy kontenera Uruchom na platformie Docker.
* Za pomocą interfejsu API REST do wykonywania zapytań punktów końcowych kontenera, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta (na przykład obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](luis-container-configuration.md) ustawień konfiguracji
* Zapoznaj się [Rozwiązywanie problemów](troubleshooting.md) Aby rozwiązać problemy związane z działaniem usługi LUIS.
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip