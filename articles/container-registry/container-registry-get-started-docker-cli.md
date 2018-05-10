---
title: Obraz Docker wypychania do prywatnego rejestru Azure
description: Wypychanie i ściąganie obrazów platformy Docker do prywatnego rejestru kontenerów na platformie Azure za pomocą interfejsu wiersza polecenia platformy Docker
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d729a45b28ad02a652c265974d46fe1aaf752198
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Wypchnij swój pierwszy obraz do prywatnego rejestru kontenerów platformy Docker za pomocą interfejsu wiersza polecenia platformy Docker

Rejestr kontenera platformy Azure przechowuje prywatne obrazy kontenerów platformy [Docker](http://hub.docker.com) i zarządza nimi podobnie, jak [koncentrator platformy Docker](https://hub.docker.com/) przechowuje publiczne obrazy platformy Docker. Można użyć [interfejsu wiersza polecenia Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) dla [logowania](https://docs.docker.com/engine/reference/commandline/login/), [wypychania](https://docs.docker.com/engine/reference/commandline/push/), [ściągania](https://docs.docker.com/engine/reference/commandline/pull/)i inne operacje dotyczące Twojej kontenera rejestr.

W poniższych krokach, możesz pobrać oficjalne [obrazu Nginx](https://store.docker.com/images/nginx) z publicznego rejestru Centrum Docker, znacznika rejestru prywatnej kontenera platformy Azure, wypchnąć go do rejestru i pobierania jej z rejestru.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj witryny [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure w wersji 2.0](container-registry-get-started-azure-cli.md).
* **Interfejs wiersza polecenia docker** — Konfigurowanie lokalnego komputera jako hosta Docker i dostęp do poleceń interfejsu wiersza polecenia Docker, zainstaluj [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Zaloguj się do rejestru

Brak [kilka sposobów uwierzytelniania](container-registry-authentication.md) do rejestru Kontener prywatny. Jest to zalecana metoda podczas pracy w wierszu polecenia przy użyciu polecenia interfejsu wiersza polecenia Azure [logowania acr az](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Na przykład, aby zalogować się do rejestru o nazwie *myregistry*:

```azurecli
az acr login --name myregistry
```

Możesz także zalogować się przy użyciu [logowania docker](https://docs.docker.com/engine/reference/commandline/login/). Poniższy przykład przekazuje identyfikator i hasło [nazwy głównej usługi](../active-directory/active-directory-application-objects.md) Azure Active Directory. Na przykład może być [przypisane nazwy głównej usługi](container-registry-authentication.md#service-principal) do rejestru scenariusz automatyzacji.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Oba polecenia zwracają `Login Succeeded` po ukończeniu. Jeśli używasz `docker login`, może być również wyświetlany ostrzeżenie o zabezpieczeniach rekomendowania stosowania `--password-stdin` parametru. Użycie tego parametru wykracza poza zakres tego artykułu, jednak zalecamy zastosowanie tego najlepszego rozwiązania. Aby uzyskać więcej informacji, zobacz [logowania docker](https://docs.docker.com/engine/reference/commandline/login/) poleceń.

> [!TIP]
> Zawsze Określ nazwę FQDN rejestru (tylko małe litery) korzystając `docker login` i gdy oznaczenia obrazów do wypychania do rejestru. W przykładach w niniejszym artykule jest w pełni kwalifikowana nazwa *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Ściąganie oficjalnego obrazu Nginx

Po pierwsze ściągnięcia publicznego obrazu Nginx na komputerze lokalnym.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Uruchamianie kontenera w środowisku lokalnym

Wykonaj następujące [docker Uruchom](https://docs.docker.com/engine/reference/run/) polecenie, aby uruchomić wystąpienie lokalne kontener Nginx interakcyjne (`-it`) na porcie 8080. `--rm` Argument określa, że kontener powinna zostać usunięta, jeśli zostanie zatrzymane.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Przejdź do [ http://localhost:8080 ](http://localhost:8080) Aby wyświetlić domyślnej strony sieci web obsługiwane przez Nginx w kontenerze uruchomione. Powinny pojawić się Strona podobny do następującego:

![Kontener Nginx na komputerze lokalnym](./media/container-registry-get-started-docker-cli/nginx.png)

Ponieważ uruchomiona kontenera interakcyjnie z `-it`, widać serwera Nginx danych wyjściowych w wierszu polecenia po przejdź do niego w przeglądarce.

Aby zatrzymać i usunąć kontenera, naciśnij klawisz `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Tworzenie aliasu obrazu

Użyj [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) utworzyć alias obrazu z w pełni kwalifikowana ścieżka do rejestru. W tym przykładzie jest określana przestrzeń nazw `samples`, aby uniknąć zaśmiecania katalogu głównego rejestru.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Aby uzyskać więcej informacji na temat znakowanie z przestrzeniami nazw, zobacz [przestrzenie nazw repozytorium](container-registry-best-practices.md#repository-namespaces) sekcji [najlepsze rozwiązania dotyczące rejestru kontenera Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Wypychanie obrazu do rejestru

Teraz, oznaczone obrazu z w pełni kwalifikowana ścieżka do rejestru prywatne, może wypchnąć go do rejestru z [wypychania docker](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Ściąganie obrazu z rejestru

Użyj [ściągania docker](https://docs.docker.com/engine/reference/commandline/pull/) polecenia do pobierania obrazu z rejestru:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Kontener Nginx Start

Użyj [docker Uruchom](https://docs.docker.com/engine/reference/run/) polecenie do uruchomienia obrazu, które zostały już pobierane z rejestru:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Przejdź do [ http://localhost:8080 ](http://localhost:8080) Aby wyświetlić uruchomionych kontenera.

Aby zatrzymać i usunąć kontenera, naciśnij klawisz `Control` + `C`.

## <a name="remove-the-image-optional"></a>Usuń obraz (opcjonalnie)

Jeśli obraz Nginx nie są już potrzebne, można usunąć je lokalnie z [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) polecenia.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Aby usunąć obrazy z rejestru kontenera platformy Azure, możesz użyć polecenia interfejsu wiersza polecenia Azure [az acr repozytorium delete](/cli/azure/acr/repository#az_acr_repository_delete). Na przykład następujące polecenie usuwa manifest odwołuje się tag, wszystkie dane skojarzone warstwy i wszystkie inne tagi odwołujące się do manifestu.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy znasz już podstawy wszystko jest gotowe rozpocząć korzystanie z rejestru! Wdrażanie obrazów kontenera z rejestru na:

* [Usługa Azure Kubernetes (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Wystąpień kontenera platformy Azure](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
