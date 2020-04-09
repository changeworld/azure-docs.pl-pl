---
title: Przepisy na pojemniki platformy Docker
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć, testować i przechowywać kontenery z niektórymi lub wszystkimi ustawieniami konfiguracji do wdrażania i ponownego użycia.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875082"
---
# <a name="create-containers-for-reuse"></a>Tworzenie kontenerów do ponownego użycia

Użyj tych receptur kontenerów do tworzenia kontenerów usług Cognitive Services, które mogą być ponownie użyte. Kontenery mogą być budowane przy niektórych lub wszystkich ustawieniach konfiguracji, tak aby _nie_ były potrzebne po uruchomieniu kontenera.

Po tej nowej warstwie kontenera (z ustawieniami) i przetestowaniu go lokalnie, można przechowywać kontener w rejestrze kontenerów. Po uruchomieniu kontenera będzie potrzebował tylko tych ustawień, które nie są obecnie przechowywane w kontenerze. Kontener rejestru prywatnego zapewnia miejsce konfiguracji do przekazania tych ustawień.

## <a name="docker-run-syntax"></a>Składnia uruchamiania platformy Docker

Wszelkie `docker run` przykłady w tym dokumencie `^` zakładają, że konsola systemu Windows ze znakiem kontynuacji wiersza. Należy wziąć pod uwagę następujące kwestie na własny użytek:

* Nie należy zmieniać kolejności argumentów, chyba że są bardzo zaznajomieni z kontenerów docker.
* Jeśli używasz systemu operacyjnego innego niż Windows lub konsoli innej niż konsola Systemu Windows, użyj odpowiedniej konsoli/terminala, składni folderów dla instalacji i znaku kontynuacji linii dla konsoli i systemu.  Ponieważ kontener usług Cognitive Services jest systemem operacyjnym Linux, instalacja docelowa używa składni folderów w stylu linuksa.
* `docker run`przykłady użyć katalogu poza `c:` dyskiem, aby uniknąć konfliktów uprawnień w systemie Windows. Jeśli musisz użyć określonego katalogu jako katalogu wejściowego, może być konieczne udzielenie uprawnienia usługi docker.

## <a name="store-no-configuration-settings-in-image"></a>Przechowywanie ustawień konfiguracji na obrazie

Przykładowe `docker run` polecenia dla każdej usługi nie przechowują żadnych ustawień konfiguracji w kontenerze. Po uruchomieniu kontenera z konsoli lub usługi rejestru te ustawienia konfiguracji muszą być przekazywać. Kontener rejestru prywatnego zapewnia miejsce konfiguracji do przekazania tych ustawień.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Ponowne użycie przepisu: przechowuj wszystkie ustawienia konfiguracji za pomocą kontenera

Aby zapisać wszystkie ustawienia konfiguracji, `Dockerfile` utwórz z tymi ustawieniami.

Problemy z tym podejściem:

* Nowy kontener ma osobną nazwę i tag z oryginalnego kontenera.
* Aby zmienić te ustawienia, należy zmienić wartości pliku dockerfile, odbudować obraz i ponownie opublikować w rejestrze.
* Jeśli ktoś uzyska dostęp do rejestru kontenerów lub lokalnego hosta, może uruchomić kontener i użyć punktów końcowych usług Cognitive Services.
* Jeśli usługa Cognitive Service nie wymaga instalacji wejściowych, nie dodawaj `COPY` wierszy do pliku Dockerfile.

Utwórz plik Dockerfile, pobierając z istniejącego kontenera usług Cognitive Services, którego chcesz użyć, a następnie użyj poleceń platformy docker w pliku Dockerfile, aby ustawić lub wyciągnąć informacje potrzebne kontenerowi.

W tym przykładzie:

* Ustawia punkt końcowy rozliczeń `{BILLING_ENDPOINT}` z klucza środowiska `ENV`hosta przy użyciu programu .
* Ustawia klucz api rozliczeń `{ENDPOINT_KEY}` z klucza środowiska hosta przy użyciu env.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Ponowne użycie przepisu: ustawienia rozliczeń sklepu z kontenerem

W tym przykładzie pokazano, jak utworzyć kontener tonacji analizy tekstu z pliku Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

W razie potrzeby skompiluj i uruchom kontener [lokalnie](#how-to-use-container-on-your-local-host) lub z [prywatnego kontenera rejestru.](#how-to-add-container-to-private-registry)

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Ponowne użycie przepisu: ustawienia rozliczeń i instalacji sklepu z kontenerem

W tym przykładzie pokazano, jak używać rozumienia języka, zapisywanie rozliczeń i modeli z Dockerfile.

* Kopiuje plik modelu rozumienia języka (LUIS) z `COPY`systemu plików hosta za pomocą pliku .
* Kontener usługi LUIS obsługuje więcej niż jeden model. Jeśli wszystkie modele są przechowywane w tym `COPY` samym folderze, wszystkie potrzebne są jedno instrukcja.
* Uruchom plik docker z względnego nadrzędnego katalogu wejściowego modelu. W poniższym przykładzie `docker build` `docker run` uruchom polecenia i polecenia `/input`z względnego rodzica . Pierwszym `/input` z `COPY` nich jest katalog komputera-hosta. Drugi `/input` to katalog kontenera.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

W razie potrzeby skompiluj i uruchom kontener [lokalnie](#how-to-use-container-on-your-local-host) lub z [prywatnego kontenera rejestru.](#how-to-add-container-to-private-registry)

## <a name="how-to-use-container-on-your-local-host"></a>Jak używać kontenera na lokalnym hoście

Aby utworzyć plik platformy `<your-image-name>` Docker, zastąp nową nazwą obrazu, a następnie użyj:

```console
docker build -t <your-image-name> .
```

Aby uruchomić obraz i usunąć go po`--rm`zatrzymaniu kontenera ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Jak dodać kontener do rejestru prywatnego

Wykonaj następujące kroki, aby użyć pliku Dockerfile i umieścić nowy obraz w rejestrze kontenerów prywatnych.  

1. Utwórz `Dockerfile` z tekstem z przepisu ponownego użycia. A `Dockerfile` nie ma rozszerzenia.

1. Zastąp dowolne wartości w nawiasach kątowych własnymi wartościami.

1. Tworzenie pliku do obrazu w wierszu polecenia lub terminalu, za pomocą następującego polecenia. Zastąp wartości w `<>`nawiasach kątowych, własną nazwą kontenera i znacznikiem.  

    Opcja tagu `-t`, to sposób na dodanie informacji o tym, co zostało zmienione dla kontenera. Na przykład nazwa kontenera `modified-LUIS` wskazuje, że oryginalny kontener został warstwowy. Nazwa tagu `with-billing-and-model` wskazuje, jak zmodyfikowano kontener rozumienia języka (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure z konsoli. To polecenie otwiera przeglądarkę i wymaga uwierzytelnienia. Po uwierzytelnieniu można zamknąć przeglądarkę i kontynuować pracę w konsoli.

    ```azurecli
    az login
    ```

1. Zaloguj się do rejestru prywatnego za pomocą interfejsu wiersza polecenia platformy Azure z konsoli.

    Zastąp wartości w `<my-registry>`nawiasach kątowych własną nazwą rejestru.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Można również zalogować się za pomocą logowania docker, jeśli są przypisane jednostki usługi.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Oznaczanie kontenera lokalizacją rejestru prywatnego. Zastąp wartości w `<my-registry>`nawiasach kątowych własną nazwą rejestru. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Jeśli nie używasz nazwy tagu, `latest` jest implikowane.

1. Wypchnij nowy obraz do rejestru kontenerów prywatnych. Podczas wyświetlania rejestru kontenerów prywatnych nazwa kontenera używana w następującym poleceniu interfejsu wiersza polecenia będzie nazwą repozytorium.

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