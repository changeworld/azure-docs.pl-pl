---
title: Zamontowanie woluminu gitRepo do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin gitRepo, aby sklonować repozytorium Git w wystąpieniach kontenera
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 405cacd7a1649f95640a8dabf476729e101d03f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252084"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Instalowanie woluminu gitRepo w wystąpieniach kontenera platformy Azure

Dowiedz się, jak zainstalować wolumin *gitRepo,* aby sklonować repozytorium Git w wystąpieniach kontenera.

> [!NOTE]
> Montaż woluminu *gitRepo* jest obecnie ograniczony do kontenerów Linuksa. Podczas gdy pracujemy nad wprowadzeniem wszystkich funkcji do kontenerów systemu Windows, możesz znaleźć aktualne różnice w platformie w [przeglądzie](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>głośność gitRepo

Wolumin *gitRepo* montuje katalog i klonuje do niego określone repozytorium Git podczas uruchamiania kontenera. Za pomocą woluminu *gitRepo* w wystąpieniach kontenera, można uniknąć dodawania kodu w ten sposób w aplikacjach.

Podczas montażu woluminu *gitRepo* można ustawić trzy właściwości, aby skonfigurować wolumin:

| Właściwość | Wymagany | Opis |
| -------- | -------- | ----------- |
| `repository` | Tak | Pełny adres URL, w tym `http://` lub `https://`, repozytorium Git do sklonowania.|
| `directory` | Nie | Katalog, do którego należy sklonować repozytorium. Ścieżka nie może zawierać ani`..`zaczynać od " ".  Jeśli określisz`.`" ", repozytorium zostanie sklonowane w katalogu woluminu. W przeciwnym razie repozytorium Git jest klonowane do podkatalogu danej nazwy w katalogu woluminów. |
| `revision` | Nie | Skrót zatwierdzenia poprawki, która ma zostać sklonowana. Jeśli nie określono, `HEAD` wersja jest klonowana. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Wolumin gitRepo instalacji: narzędzie interfejsu wiersza polecenia platformy Azure

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenera `--gitrepo-url` za `--gitrepo-mount-path` pomocą [interfejsu wiersza polecenia platformy Azure,](/cli/azure)należy podać parametry i parametry do polecenia [tworzenia kontenera az.][az-container-create] Opcjonalnie można określić katalog w woluminie,`--gitrepo-dir`który ma zostać sklonowany ( )`--gitrepo-revision`i skrót zatwierdzenia poprawki, która ma zostać sklonowana ( ).

W tym przykładowym poleceniu klonuje przykładową aplikację firmy Microsoft [aci-helloworld][aci-helloworld] `/mnt/aci-helloworld` w wystąpieniu kontenera:

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

Aby sprawdzić, czy wolumin gitRepo został zainstalowany, uruchom powłokę w kontenerze z [az container exec][az-container-exec] i wyświetl katalog:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Zamontuj wolumin gitRepo: Menedżer zasobów

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenera za pomocą [szablonu usługi Azure Resource Manager,](/azure/templates/microsoft.containerinstance/containergroups)najpierw wypełnij tablicę `volumes` w sekcji grupy `properties` kontenerów szablonu. Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować wolumin *gitRepo,* wypełnij `volumeMounts` tablicę `properties` w sekcji definicji kontenera.

Na przykład następujący szablon Menedżera zasobów tworzy grupę kontenerów składającą się z jednego kontenera. Kontener klonuje dwa repozytoria GitHub określone przez bloki woluminu *gitRepo.* Drugi wolumin zawiera dodatkowe właściwości określające katalog do sklonowania i skrót zatwierdzenia określonej wersji do klonowania.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Wynikowa struktura katalogów dwóch sklonowanych repozytoriów zdefiniowanych w poprzednim szablonie jest:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Aby wyświetlić przykład wdrożenia wystąpienia kontenera za pomocą szablonu usługi Azure Resource Manager, zobacz [Wdrażanie grup wielu kontenerów w wystąpieniach kontenerów platformy Azure.](container-instances-multi-container-group.md)

## <a name="private-git-repo-authentication"></a>Prywatne uwierzytelnianie repozytorium Git

Aby zainstalować wolumin gitRepo dla prywatnego repozytorium Git, określ poświadczenia w adresie URL repozytorium. Zazwyczaj poświadczenia są w postaci nazwy użytkownika i tokenu dostępu osobistego (PAT), który udziela ograniczonego dostępu do repozytorium.

Na przykład parametr `--gitrepo-url` interfejsu wiersza polecenia platformy Azure dla prywatnego repozytorium GitHub wydaje się podobny do następującego (gdzie "gituser" jest nazwą użytkownika GitHub, a "abcdef1234fdsa4321abcdef" jest osobistym tokenem dostępu użytkownika):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

W przypadku repozytorium Git repozytorium repozytorium usługi Azure Reppos, określ dowolną nazwę użytkownika (można użyć "azurereposuser" jak w poniższym przykładzie) w połączeniu z prawidłowym PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Aby uzyskać więcej informacji na temat tokenów dostępu osobistego dla witryn GitHub i usługi Azure Repos, zobacz następujące kwestie:

GitHub: [Tworzenie tokenu dostępu osobistego dla wiersza polecenia][pat-github]

Repozytoria platformy Azure: [tworzenie tokenów dostępu osobistego w celu uwierzytelnienia dostępu][pat-repos]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w wystąpieniach kontenera platformy Azure:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Instalowanie woluminu pustegodiru w wystąpieniach kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Instalowanie woluminu tajnego w wystąpieniach kontenera platformy Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
