---
title: Przepisy dotyczące kontenerów platformy Docker
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć, testować i przechowywać kontenery przy użyciu niektórych lub wszystkich ustawień konfiguracji do wdrożenia i ponownego użycia.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717207"
---
# <a name="create-containers-for-reuse"></a>Tworzenie kontenerów do ponownego użycia

Te przepisy kontenera dotyczą tworzenia kontenerów Cognitive Services, które mogą być ponownie używane. Kontenery można kompilować przy użyciu niektórych lub wszystkich ustawień konfiguracji, aby _nie_ były one używane po rozpoczęciu kontenera.

Po utworzeniu nowej warstwy kontenera (z ustawieniami) i przetestowaniu jej lokalnie można przechowywać kontener w rejestrze kontenerów. Po uruchomieniu kontenera będą potrzebne tylko te ustawienia, które nie są obecnie przechowywane w kontenerze. Kontener rejestru prywatnego zawiera przestrzeń konfiguracyjną do przekazania tych ustawień w programie.

## <a name="docker-run-syntax"></a>Składnia uruchomienia platformy Docker

Wszystkie `docker run` przykłady w tym dokumencie zakładają konsolę systemu Windows z znakiem kontynuacji wiersza `^`. Do własnego użytku należy wziąć pod uwagę następujące kwestie:

* Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.
* Jeśli używasz systemu operacyjnego innego niż Windows lub konsoli innej niż konsola systemu Windows, użyj prawidłowej konsoli/terminalu, składni folderu dla instalacji i znaku kontynuacji wiersza dla konsoli i systemu.  Ponieważ kontener Cognitive Services jest systemem operacyjnym Linux, instalacja docelowa używa składni folderu w stylu systemu Linux.
* `docker run` przykłady użyj katalogu poza dyskiem `c:`, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalog wejściowy może być konieczne przyznanie platformy docker usługi uprawnienia.

## <a name="store-no-configuration-settings-in-image"></a>Nie przechowuj ustawień konfiguracji w obrazie

Przykład `docker run` polecenia dla każdej usługi nie przechowują żadnych ustawień konfiguracyjnych w kontenerze. Po uruchomieniu kontenera z poziomu konsoli lub usługi rejestru te ustawienia konfiguracji muszą zostać przekazane. Kontener rejestru prywatnego zawiera przestrzeń konfiguracyjną do przekazania tych ustawień w programie.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Ponownie Użyj przepisu: Przechowuj wszystkie ustawienia konfiguracji przy użyciu kontenera

Aby można było przechowywać wszystkie ustawienia konfiguracji, należy utworzyć `Dockerfile` przy użyciu tych ustawień.

Problemy z tym podejściem:

* Nowy kontener ma oddzielną nazwę i tag od oryginalnego kontenera.
* Aby zmienić te ustawienia, należy zmienić wartości pliku dockerfile, odbudować obraz i ponownie opublikować w rejestrze.
* Jeśli ktoś uzyska dostęp do rejestru kontenerów lub hosta lokalnego, może uruchomić kontener i użyć punktów końcowych Cognitive Services.
* Jeśli usługa poznawcze nie wymaga instalacji wejściowych, nie dodawaj `COPY` wierszy do pliku dockerfile.

Utwórz pliku dockerfile, pobierając z istniejącego kontenera Cognitive Services, którego chcesz użyć, a następnie Użyj poleceń platformy Docker w pliku dockerfile, aby ustawić lub ściągnąć informacje wymagane przez kontener.

Ten przykład:

* Ustawia punkt końcowy rozliczeń, `{BILLING_ENDPOINT}` z klucza środowiska hosta przy użyciu `ENV`.
* Ustawia klucz interfejsu API rozliczeń, `{ENDPOINT_KEY}` z klucza środowiska hosta przy użyciu funkcji ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Ponownie Użyj przepisu: Zapisz ustawienia rozliczeń za pomocą kontenera

Ten przykład pokazuje, jak utworzyć kontener tonacji "analiza tekstu" z pliku dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Kompiluj i uruchamiaj kontener [lokalnie](#how-to-use-container-on-your-local-host) lub z [kontenera prywatnego rejestru](#how-to-add-container-to-private-registry) , zgodnie z potrzebami.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Ponownie Użyj przepisu: Zapisz ustawienia rozliczeń i instalacji za pomocą kontenera

Ten przykład pokazuje, jak używać Language Understanding, zapisywania rozliczeń i modeli z pliku dockerfile.

* Kopiuje plik modelu Language Understanding (LUIS) z systemu plików hosta przy użyciu `COPY`.
* Kontener LUIS obsługuje więcej niż jeden model. Jeśli wszystkie modele są przechowywane w tym samym folderze, należy wykonać jedną `COPY` instrukcji.
* Uruchom plik platformy Docker z względnego elementu nadrzędnego katalogu wejściowego modelu. W poniższym przykładzie Uruchom `docker build` i `docker run` polecenia z względnej klasy nadrzędnej `/input`. Pierwszy `/input` w poleceniu `COPY` jest katalogiem komputera hosta. Drugim `/input` jest katalog kontenera.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Kompiluj i uruchamiaj kontener [lokalnie](#how-to-use-container-on-your-local-host) lub z [kontenera prywatnego rejestru](#how-to-add-container-to-private-registry) , zgodnie z potrzebami.

## <a name="how-to-use-container-on-your-local-host"></a>Jak używać kontenera na hoście lokalnym

Aby skompilować plik platformy Docker, Zastąp `<your-image-name>` nową nazwą obrazu, a następnie użyj:

```console
docker build -t <your-image-name> .
```

Aby uruchomić obraz i usunąć go po zatrzymaniu kontenera (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Jak dodać kontener do rejestru prywatnego

Wykonaj następujące kroki, aby użyć pliku dockerfile i umieścić nowy obraz w rejestrze kontenera prywatnego.  

1. Utwórz `Dockerfile` z tekstem używanym do ponownego użycia przepisu. `Dockerfile` nie ma rozszerzenia.

1. Zamień wszystkie wartości w nawiasach kątowych na własne wartości.

1. Skompiluj plik do obrazu w wierszu polecenia lub terminalu przy użyciu poniższego polecenia. Zastąp wartości w nawiasach kątowych, `<>`z własną nazwą kontenera i tagiem.  

    Opcja tagu `-t`jest sposobem dodawania informacji o zmianach w kontenerze. Na przykład nazwa kontenera `modified-LUIS` wskazuje, że oryginalny kontener został warstwowy. Nazwa tagu `with-billing-and-model` wskazuje sposób modyfikacji kontenera Language Understanding (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure z poziomu konsoli programu. To polecenie otwiera przeglądarkę i wymaga uwierzytelnienia. Po uwierzytelnieniu można zamknąć przeglądarkę i kontynuować pracę w konsoli programu.

    ```azurecli
    az login
    ```

1. Zaloguj się do prywatnego rejestru przy użyciu interfejsu wiersza polecenia platformy Azure z konsoli programu.

    Zastąp wartości w nawiasach kątowych, `<my-registry>`przy użyciu własnej nazwy rejestru.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Możesz również zalogować się przy użyciu logowania platformy Docker, jeśli przypisano nazwę główną usługi.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Oznacz kontener przy użyciu lokalizacji w rejestrze prywatnym. Zastąp wartości w nawiasach kątowych, `<my-registry>`przy użyciu własnej nazwy rejestru. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Jeśli nie używasz nazwy tagu, `latest` jest implikowana.

1. Wypchnij nowy obraz do prywatnego rejestru kontenerów. Po wyświetleniu prywatnego rejestru kontenerów nazwa kontenera użyta w poniższym poleceniu interfejsu wiersza polecenia będzie nazwą repozytorium.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i używanie wystąpienia kontenera platformy Azure](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->