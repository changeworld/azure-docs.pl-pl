---
title: Rozwiązania na potrzeby kontenerów platformy Docker
titleSuffix: Azure Cognitive Services
description: Użyj tych przepisów kontenera, aby utworzyć Cognitive kontenerów usług, które mogą być ponownie używane. Kontenery mogą być wbudowane przy użyciu niektórych lub wszystkich ustawień konfiguracji, tak, aby nie są one potrzebne podczas uruchamiania kontenera. Gdy masz ta nowa warstwa kontenera (za pomocą ustawienia) i należy przetestować ją lokalnie, można przechowywać kontenera w rejestrze kontenerów. Po uruchomieniu kontenera musi jedynie te ustawienia, które nie są obecnie przechowywane w kontenerze.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 37fa972d52c564c4d61e5923c2b3dc48bde9d2ee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445802"
---
# <a name="create-containers-for-reuse"></a>Tworzenie kontenerów do ponownego wykorzystania

Użyj tych przepisów kontenera, aby utworzyć Cognitive kontenerów usług, które mogą być ponownie używane. Kontenery mogą być wbudowane przy użyciu niektórych lub wszystkich ustawień konfiguracji, aby były one _nie_ potrzebny po uruchomieniu kontenera.

Gdy masz ta nowa warstwa kontenera (za pomocą ustawienia) i należy przetestować ją lokalnie, można przechowywać kontenera w rejestrze kontenerów. Po uruchomieniu kontenera musi jedynie te ustawienia, które nie są obecnie przechowywane w kontenerze. Kontener rejestru prywatnego miejsce konfiguracji przekazania tych ustawień w.

## <a name="docker-run-syntax"></a>Uruchom składni platformy docker

Wszelkie `docker run` przykładów w tym dokumencie przyjęto założenie, za pomocą konsoli Windows `^` znak kontynuacji wiersza. Należy wziąć pod uwagę następujące czynności na własny użytek:

* Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.
* Jeśli używasz systemu operacyjnego innego niż Windows lub konsoli inne niż Windows, konsoli, na użytek poprawne konsoli/terminal, składnia folderu instaluje i wstawić znak kontynuacji wiersza dla konsoli i systemu.  Ponieważ kontener usług Cognitive Services jest system operacyjny Linux, instalacji docelowy używa składni folderu stylu dla systemu Linux.
* `docker run` Przykłady korzystają z katalogu, wyłącz `c:` dysku, aby uniknąć konfliktów uprawnień na Windows. Jeśli musisz użyć określonego katalogu jako katalog wejściowy może być konieczne przyznanie platformy docker usługi uprawnienia.

## <a name="store-no-configuration-settings-in-image"></a>Store żadnych ustawień konfiguracji w obrazie

Przykład `docker run` polecenia dla każdej z tych usług nie należy przechowywać wszystkie ustawienia konfiguracji w kontenerze. Po uruchomieniu kontenera przy użyciu konsoli lub rejestru usługi muszą podawać tych ustawień konfiguracji. Kontener rejestru prywatnego miejsce konfiguracji przekazania tych ustawień w.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Ponowne użycie przepisu: przechowywać wszystkie ustawienia konfiguracji za pomocą kontenera

Aby można było przechowywać wszystkie ustawienia konfiguracji, należy utworzyć `Dockerfile` przy użyciu tych ustawień.

Problemy w przypadku tej metody:

* Nowy kontener ma oddzielne nazwę oraz tag z oryginalnym kontenerze.
* Aby zmienić te ustawienia, trzeba będzie zmienić wartości pliku Dockerfile, ponowne tworzenie obrazu i ponownie opublikuj do rejestru.
* Jeśli ktoś uzyskuje dostęp do usługi container registry lub hosta lokalnego, mogą uruchomić kontener i punktów końcowych usług Cognitive Services.
* Jeśli usługi Cognitive Service nie wymaga wprowadzania instaluje, nie dodawaj `COPY` wierszy z pliku Dockerfile.

Tworzenie pliku Dockerfile i ściąganie z istniejącego kontenera usług Cognitive Services, którego chcesz użyć, a następnie użyj poleceń platformy docker w pliku Dockerfile można ustawiać ani ściągnąć informacje o kontenerze potrzebuje.

W tym przykładzie:

* Ustawia rozliczeń punktu końcowego, `{BILLING_ENDPOINT}` z hosta kluczu środowiska za pomocą `ENV`.
* Ustawia rozliczeń klucz interfejsu API, `{ENDPOINT_KEY}` z hosta kluczu środowiska za pomocą "ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Ponowne użycie przepisu: Zapisz ustawienia rozliczeń za pomocą kontenera

Ten przykład przedstawia sposób tworzenia kontenera tonacji Analiza tekstu z pliku Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Kompilowanie i uruchamianie kontenera [lokalnie](#how-to-use-container-on-your-local-host) lub z poziomu usługi [prywatnego rejestru kontenera](#how-to-add-container-to-private-registry) zgodnie z potrzebami.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Ponowne użycie przepisu: przechowywanie rozliczenia i ustawienia za pomocą kontenera instalacji

W tym przykładzie pokazano, jak używać rozumienia języka, zapisywanie Pomoc dotycząca rozliczeń i modeli z pliku Dockerfile.

* Kopie pliku modelu Language Understanding (LUIS) z hosta na plik przy użyciu systemu `COPY`.
* Kontener usługi LUIS obsługuje więcej niż jednego modelu. Jeśli wszystkie modele są przechowywane w tym samym folderze, wszystkie potrzebna jest jedna `COPY` instrukcji.
* Uruchom plik platformy docker z elementem nadrzędnym względnych katalog wejściowy modelu. W poniższym przykładzie, należy uruchomić `docker build` i `docker run` poleceń z elementem nadrzędnym względną `/input`. Pierwszy `/input` na `COPY` polecenie jest katalog komputera hosta. Drugi `/input` jest katalogiem kontenera.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Kompilowanie i uruchamianie kontenera [lokalnie](#how-to-use-container-on-your-local-host) lub z poziomu usługi [prywatnego rejestru kontenera](#how-to-add-container-to-private-registry) zgodnie z potrzebami.

## <a name="how-to-use-container-on-your-local-host"></a>Jak używać kontenerów na hoście lokalnym

Aby utworzyć plik platformy Docker, Zastąp `<your-image-name>` z nową nazwą obrazu, wpisz:

```console
docker build -t <your-image-name> .
```

Uruchom obraz i usunąć go po zatrzymaniu kontenera (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Sposób dodawania kontenera do rejestru prywatnego

Wykonaj następujące kroki, aby użyć pliku Dockerfile i umieścić nowy obraz w prywatnego rejestru kontenera.  

1. Utwórz `Dockerfile` z tekstem z przepisem ponownego użycia. Element `Dockerfile` nie ma rozszerzenia.

1. Zastąp wartości w nawiasy kątowe własnymi wartościami.

1. Tworzenie pliku obrazu, w wierszu polecenia lub terminalu, przy użyciu następującego polecenia. Zastąp wartości w nawiasy kątowe `<>`z własnej nazwy kontenera i tagu.  

    Opcji tagu `-t`, jest sposób, aby dodać informacje zostały zmienione przez dla kontenera. Na przykład nazwą kontenera o `modified-LUIS` wskazuje, oryginalnym kontenerze zostały warstwie. Nazwa tagu `with-billing-and-model` wskazuje, jak kontenera Language Understanding (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Zaloguj się do wiersza polecenia platformy Azure z poziomu konsoli. To polecenie otworzy w przeglądarce i wymaga uwierzytelnienia. Po uwierzytelnieniu można Zamknij przeglądarkę i kontynuować pracę w konsoli.

    ```azure-cli
    az login
    ```

1. Zaloguj się do rejestru prywatnego przy użyciu wiersza polecenia platformy Azure z poziomu konsoli.

    Zastąp wartości w nawiasy kątowe `<my-registry>`, własną nazwą rejestru.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Możesz też zarejestrować się przy użyciu platformy docker logowania po przypisaniu jednostki usługi.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Tag kontenera z lokalizacji rejestru prywatnego. Zastąp wartości w nawiasy kątowe `<my-registry>`, własną nazwą rejestru. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Jeśli nie używasz nazwę tagu `latest` jest domyślna.

1. Wypchnij nowy obraz do prywatnego rejestru kontenera. Podczas przeglądania prywatnego rejestru kontenera nazwę kontenera, następujące polecenia interfejsu wiersza polecenia będzie nazwa repozytorium.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Kolejne kroki

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