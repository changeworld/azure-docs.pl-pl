---
title: Wypychanie i ciągnięcie artefaktu OCI
description: Wypychanie i ściąganie artefaktów inicjatywy Open Container Initiative (OCI) przy użyciu prywatnego rejestru kontenerów na platformie Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371056"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Wypychanie i ściąganie artefaktu OCI przy użyciu rejestru kontenerów platformy Azure

Za pomocą rejestru kontenerów platformy Azure można przechowywać [artefakty inicjatywy Open Container Initiative (OCI)](container-registry-image-formats.md#oci-artifacts) i zarządzać nimi, a także obrazy kontenerów zgodne z platformą Docker i docker.

Aby zademonstrować tę możliwość, w tym artykule pokazano, jak używać [narzędzia Rejestru OCI jako magazynu (ORAS)](https://github.com/deislabs/oras) do wypychania przykładowego artefaktu — pliku tekstowego — do rejestru kontenerów platformy Azure. Następnie pociągnij artefakt z rejestru. Można zarządzać różnymi artefaktami OCI w rejestrze kontenerów platformy Azure przy użyciu różnych narzędzi wiersza polecenia odpowiednich dla każdego artefaktu.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj [witryny Azure portal](container-registry-get-started-portal.md) lub interfejsu [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Narzędzie ORAS** — pobierz i zainstaluj bieżącą wersję ORAS dla swojego systemu operacyjnego z [repozytorium GitHub](https://github.com/deislabs/oras/releases). Narzędzie jest uwalniane jako skompresowany tarball (plik).`.tar.gz` Wyodrębnij i zainstaluj plik przy użyciu standardowych procedur dla systemu operacyjnego.
* **Jednostki usługi Azure Active Directory (opcjonalnie)** — aby uwierzytelnić się bezpośrednio za pomocą ORAS, należy utworzyć [jednostkę usługi,](container-registry-auth-service-principal.md) aby uzyskać dostęp do rejestru. Upewnij się, że podmiotu usługi jest przypisany rolę, takich jak AcrPush tak, że ma uprawnienia do wypychania i ściągania artefaktów.
* **Interfejsu wiersza polecenia platformy Azure (opcjonalnie)** — aby użyć tożsamości indywidualnej, potrzebujesz lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Zaleca się stosowanie wersji 2.0.71 lub nowszej. Uruchom, `az --version `aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* **Docker (opcjonalnie)** — aby użyć pojedynczej tożsamości, musisz również zainstalować program Docker lokalnie, aby uwierzytelnić się w rejestrze. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Logowanie się do rejestru

W tej sekcji przedstawiono dwa sugerowane przepływy pracy do logowania się do rejestru, w zależności od używanej tożsamości. Wybierz metodę odpowiednią dla danego środowiska.

### <a name="sign-in-with-oras"></a>Zaloguj się za pomocą ORAS

Korzystając z [jednostki usługi](container-registry-auth-service-principal.md) z `oras login` prawami wypychania, uruchom polecenie, aby zalogować się do rejestru przy użyciu identyfikatora aplikacji jednostki usługi i hasła. Określ w pełni kwalifikowaną nazwę rejestru (wszystkie małe litery), w tym przypadku *myregistry.azurecr.io*. Identyfikator aplikacji głównej usługi jest przekazywany w zmiennej `$SP_APP_ID` `$SP_PASSWD`środowiskowej, a hasło w zmiennej .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Aby odczytać hasło ze stdin, użyj `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure

[Zaloguj się](/cli/azure/authenticate-azure-cli) do interfejsu wiersza polecenia platformy Azure za pomocą tożsamości, aby wypychać i pobierać artefakty z rejestru kontenerów.

Następnie użyj polecenia interfejsu wiersza polecenia azure [az acr logowania,](/cli/azure/acr?view=azure-cli-latest#az-acr-login) aby uzyskać dostęp do rejestru. Na przykład, aby uwierzytelnić się w rejestrze o nazwie *myregistry:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`Używa klienta platformy Docker, aby ustawić token `docker.config` usługi Azure Active Directory w pliku. Klient platformy Docker musi być zainstalowany i uruchomiony, aby ukończyć przepływ indywidualnego uwierzytelniania.

## <a name="push-an-artifact"></a>Wypychanie artefaktu

Utwórz plik tekstowy w lokalnym roboczym katalogu roboczym z przykładowym tekstem. Na przykład w powłoce bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Użyj `oras push` polecenia, aby wypchnąć ten plik tekstowy do rejestru. Poniższy przykład wypycha przykładowy `samples/artifact` plik tekstowy do repozytorium. Rejestr jest identyfikowany za pomocą w pełni kwalifikowanej nazwy rejestru *myregistry.azurecr.io* (wszystkie małe litery). Artefakt jest oznaczony `1.0`. Artefakt ma domyślnie niezdefiniowany typ identyfikowany przez ciąg typu `artifact.txt` *nośnika* po nazwach plików . Zobacz [Artefakty OCI](https://github.com/opencontainers/artifacts) dla dodatkowych typów. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Dane wyjściowe dla pomyślnego wypychania jest podobny do następujących:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Aby zarządzać artefaktami w rejestrze, jeśli używasz `az acr` interfejsu wiersza polecenia platformy Azure, uruchom standardowe polecenia do zarządzania obrazami. Na przykład pobierz atrybuty artefaktu za pomocą polecenia [az acr repozytorium show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Dane wyjściowe będą podobne do następujących:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Ciągnienie artefaktu

Uruchom `oras pull` polecenie, aby wyciągnąć artefakt z rejestru.

Najpierw usuń plik tekstowy z lokalnego katalogu roboczego:

```bash
rm artifact.txt
```

Uruchom, `oras pull` aby wyciągnąć artefakt i określ typ nośnika używany do wypychania artefaktu:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Sprawdź, czy ściąganie zakończyło się pomyślnie:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Usuwanie artefaktu (opcjonalnie)

Aby usunąć artefakt z rejestru kontenerów platformy Azure, użyj polecenia [az acr repozytorium delete.][az-acr-repository-delete] Poniższy przykład usuwa przechowywany tam artefakt:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [bibliotece ORAS](https://github.com/deislabs/oras/tree/master/docs), w tym jak skonfigurować manifest dla artefaktu
* Odwiedź repozytorium [artefaktów OCI,](https://github.com/opencontainers/artifacts) aby uzyskać informacje na temat nowych typów artefaktów



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
