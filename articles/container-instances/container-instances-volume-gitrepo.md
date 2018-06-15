---
title: Zainstalować wolumin gitRepo wystąpień kontenera platformy Azure
description: Informacje o sposobie instalowania woluminu gitRepo w klonowania repozytorium Git do swoich wystąpień kontenera
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166783"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure

Dowiedz się, jak zainstalować *gitRepo* woluminu sklonować repozytorium Git do swoich wystąpień kontenera.

> [!NOTE]
> Instalowanie *gitRepo* wolumin jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo woluminu

*GitRepo* woluminu instaluje katalogu i klonów określonego repozytorium Git do niego przy uruchamianiu kontenera. Za pomocą *gitRepo* woluminu w swoich wystąpień kontenera, można uniknąć dodawania kod w ten sposób w aplikacji.

Podczas instalowania *gitRepo* woluminu, można ustawić właściwości trzy do konfigurowania woluminu:

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| `repository` | Yes | Pełny adres URL, w tym `http://` lub `https://`, można sklonować repozytorium Git.|
| `directory` | Nie | Katalog, w którym można sklonować repozytorium. Ścieżka nie może zawierać ani rozpoczynać się od "`..`".  Jeśli określisz "`.`", repozytorium został sklonowany w katalogu woluminu. W przeciwnym razie repozytorium Git został sklonowany w podkatalogu o podanej nazwie w katalogu woluminu. |
| `revision` | Nie | Skrót zatwierdzenia poprawki w klonowania. Jeśli nie zostanie podany, `HEAD` został sklonowany poprawki. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo instalacji woluminu: Zarządzanie Klastrami

Aby zamontować wolumin gitRepo podczas wdrażania instancjach kontenera z [platformy Azure](/cli/azure), dostarczają `--gitrepo-url` i `--gitrepo-mount-path` parametry, aby [utworzyć kontener az] [ az-container-create] polecenia. Opcjonalnie można określić katalog na woluminie sklonować do (`--gitrepo-dir`) i mieszania commit rewizji sklonowane (`--gitrepo-revision`).

To polecenie przykładowe klonów [aci helloworld] [ aci-helloworld] przykładowej aplikacji do `/mnt/aci-helloworld` w instancji container:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Aby sprawdzić, czy wolumin gitRepo został zainstalowany, należy uruchomić powłoki w pojemniku z [exec kontenera az] [ az-container-exec] i Lista katalogów:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Wolumin instalacji gitRepo: Menedżer zasobów

Aby zainstalować wolumin gitRepo podczas wdrażania instancjach kontenera z [szablonu Menedżera zasobów](/azure/templates/microsoft.containerinstance/containergroups), najpierw wypełnić `volumes` tablicy w grupie kontener `properties` sekcji szablonu. Następnie dla każdego pojemnika w grupie kontenera, w którym chcesz zainstalować *gitRepo* woluminu, wypełnić `volumeMounts` tablica z `properties` sekcji definicji kontenera.

Na przykład następujący szablon usługi Resource Manager tworzy grupę kontenera składające się z jeden kontener. Kontener klonów określonego przez dwie repozytoriów GitHub *gitRepo* bloków woluminu. Drugi wolumin zawiera dodatkowe właściwości określenia katalogu sklonować do oraz skrót zatwierdzenia określoną poprawkę sklonować.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Wynikowa struktura katalogów dwóch repozytoriów sklonowany zdefiniowane w powyższych szablonu jest:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Aby zapoznać się przykładem kontenera wystąpienia wdrożenia z szablonem usługi Azure Resource Manager, zobacz [wdrożenia kontenera wielu grup wystąpień kontenera Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w wystąpień kontenera platformy Azure:

* [Instalowanie udziału plików na platformę Azure w wystąpień kontenera platformy Azure](container-instances-volume-azure-files.md)
* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstalować tajny woluminu w wystąpień kontenera platformy Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec