---
title: Zainstalować woluminu gitRepo usługi Azure Container Instances
description: Dowiedz się, jak można zainstalować woluminu gitRepo, aby sklonować repozytorium Git do wystąpienia kontenera
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: 70593bffbf30b3a0c0978e56c2af1a856a22f2ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563023"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Zainstalować woluminu gitRepo w usłudze Azure Container Instances

Dowiedz się, jak zainstalować *gitRepo* woluminu, aby sklonować repozytorium Git do wystąpienia kontenera.

> [!NOTE]
> Instalowanie *gitRepo* wolumin jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>wolumin gitRepo

*GitRepo* wolumin instaluje katalogu i klony określonego repozytorium Git do niej podczas uruchamiania kontenera. Za pomocą *gitRepo* woluminu w usługi container instances, można uniknąć, dodając kod, aby to zrobić w swoich aplikacjach.

Podczas instalowania *gitRepo* woluminu, można ustawić trzy właściwości, aby skonfigurować woluminu:

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| `repository` | Yes | Pełny adres URL, w tym `http://` lub `https://`, repozytorium Git do sklonowania.|
| `directory` | Nie | Katalog, w którym można sklonować repozytorium. Ścieżka musi nie zawierały lub rozpoczynały się "`..`".  Jeśli określisz "`.`", repozytorium został sklonowany w katalogu woluminu. W przeciwnym razie repozytorium Git został sklonowany w podkatalogu o podanej nazwie w katalogu woluminu. |
| `revision` | Nie | Skrót zatwierdzenia poprawki, aby sklonować. Jeśli nie zostanie podany, `HEAD` został sklonowany poprawki. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Wolumin gitRepo instalacji: Interfejs wiersza polecenia platformy Azure

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenera za pomocą [wiersza polecenia platformy Azure](/cli/azure), dostarczają `--gitrepo-url` i `--gitrepo-mount-path` parametry [utworzyć kontener az] [ az-container-create] polecenia. Opcjonalnie można określić katalog, w obrębie woluminu, aby sklonować do (`--gitrepo-dir`) oraz skrót zatwierdzenia poprawki do sklonowania (`--gitrepo-revision`).

To przykładowe polecenie klonuje Microsoft [aci-helloworld] [ aci-helloworld] przykładowej aplikacji do `/mnt/aci-helloworld` w wystąpieniu kontenera:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Aby sprawdzić, czy wolumin gitRepo został zainstalowany, uruchom powłokę w kontenerze za pomocą [az container exec] [ az-container-exec] i wyświetlanie listy katalogu:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Wolumin gitRepo instalacji: Resource Manager

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenera za pomocą [szablonu usługi Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), najpierw wypełnić `volumes` tablicy w grupie kontenerów `properties` części szablonu. Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować *gitRepo* woluminu, wypełnij `volumeMounts` tablicy w `properties` sekcja definicji kontenera.

Na przykład następujący szablon usługi Resource Manager tworzy grupę kontenerów składające się z jednego kontenera. Kontener klony dwóch repozytoriów GitHub, określony przez *gitRepo* bloków woluminu. Drugi wolumin zawiera dodatkowe właściwości, określając katalog w celu sklonowania oraz skrót zatwierdzenia określoną poprawkę, aby sklonować.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Wynikowy struktura katalogów dwóch sklonowanych repozytoriów zdefiniowane w szablonie poprzedzającego jest:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Aby zapoznać się z przykładem wdrażaniem wystąpienia kontenera za pomocą szablonu usługi Azure Resource Manager, zobacz [wdrażanie grup wielu kontenerów w usłudze Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Uwierzytelnianie repozytorium Git prywatne

Aby zainstalować wolumin gitRepo do prywatnego repozytorium Git, należy określić poświadczenia w adresie URL repozytorium. Zwykle poświadczenia znajdują się w formie nazwy użytkownika i osobistego tokenu dostępu (PAT) przyznająca dostęp do repozytorium o określonym zakresie.

Na przykład interfejsu wiersza polecenia Azure `--gitrepo-url` parametr prywatne repozytorium GitHub będzie wyglądać podobnie do następujące (gdzie "gituser" jest nazwą użytkownika usługi GitHub, a "abcdef1234fdsa4321abcdef" użytkownika osobistego tokenu dostępu):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Repozytorium Git repozytoriów platformy Azure należy określić w połączeniu z prawidłową osobisty token dostępu dowolną nazwę użytkownika (jak w poniższym przykładzie można użyć "azurereposuser"):

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Aby uzyskać więcej informacji na temat osobiste tokeny dostępu usługi GitHub i repozytoriów platformy Azure zobacz następujące tematy:

GitHub: [Tworzenie osobistego tokenu dostępu dla wiersza polecenia][pat-github]

Azure Repos: [Tworzyć osobiste tokeny dostępu do uwierzytelniania dostępu][pat-repos]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w usłudze Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstaluj wolumin tajny w usłudze Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
