---
title: Usuń zasoby obrazów w usłudze Azure Container Registry
description: Szczegółowe informacje na temat sposobu skutecznie zarządzać rozmiarem rejestru, usuwając dane obrazu w kontenerze.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/17/2019
ms.author: danlep
ms.openlocfilehash: c603afa61499a615a0882cef06f14fd3d080a9ef
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797769"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Usuwanie obrazów kontenerów w usłudze Azure Container Registry

Aby zachować rozmiaru usługi Azure container registry, należy okresowo usuwać danych starych obrazów. Podczas gdy niektóre obrazy kontenera wdrażane w środowisku produkcyjnym mogą wymagać przechowywania długoterminowego przechowywania, inne zazwyczaj można usunąć szybciej. Na przykład w automatycznej kompilacji i w scenariuszu testu rejestru można szybko wypełnić przy użyciu obrazów, które nigdy nie mogą być wdrażane i mogą być czyszczone wkrótce po ukończeniu przebiegu kompilacji i testowania.

Ponieważ dane obrazów można usunąć na kilka różnych sposobów, jest ważne, aby zrozumieć, jak każda operacja usuwania wpływa na użycie magazynu. W tym artykule opisano kilka metod związanych z usuwaniem danych obrazu:

* Usuń [repozytorium](#delete-repository): Usuwa wszystkie obrazy i wszystkie warstwy unikatowy w repozytorium.
* Usuń, [tag](#delete-by-tag): Usuwa obrazu, tag, wszystkie warstwy unikatowy odwołuje się obraz i wszystkie inne znaczniki skojarzone z obrazem.
* Usuń, [skrótu manifestu](#delete-by-manifest-digest): Usuwa obraz, wszystkie warstwy unikatowy odwołuje się obraz i wszystkie znaczniki skojarzone z obrazem.

Przykładowe skrypty znajdują się w celu automatyzacji operacji usuwania.

Aby zapoznać się z wprowadzeniem do tych pojęć, zobacz [o rejestrów, repozytoriów i obrazów](container-registry-concepts.md).

## <a name="delete-repository"></a>Usuwanie repozytorium

Usuwanie repozytorium powoduje usunięcie wszystkich obrazów w repozytorium, m.in. wszystkie tagi unikatowy warstwy i manifesty. Jeśli usuniesz repozytorium, można odzyskać miejsce do magazynowania używane przez obrazy, które odwołują się unikatowy warstwy w tym repozytorium.

Następujące wiersza polecenia platformy Azure polecenie usuwa repozytorium "acr-helloworld" wszystkie tagi i manifesty w repozytorium. Warstwy odwołuje się usunięto manifesty nie odwołują się inne obrazy, w rejestrze, dane warstwy powoduje również usunięcie, odzyskiwanie miejsca do magazynowania.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Usuwanie według tagu

Pojedyncze obrazy można usunąć z repozytorium, określając nazwę repozytorium i tagów w operacji usuwania. Jeśli usuniesz według tagów, można odzyskać miejsce do magazynowania używane przez wszystkie unikatowe warstw obrazu (warstwy nie są współużytkowane przez wszystkie inne obrazów w rejestrze).

Aby usunąć według tagów, użyj [az acr repozytorium delete][az-acr-repository-delete] i określ nazwę obrazu w `--image` parametru. Usuwane są wszystkie warstwy unikatowe dla obrazu i innych tagów skojarzonych z obrazem.

Na przykład usunięcie "acr-helloworld:latest" obraz z rejestru "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Usuwanie *według tagów* nie powinny być mylone z usuwanie tagu (zdejmowanie). Można usunąć tagu za pomocą polecenia interfejsu wiersza polecenia Azure [usunąć oznakowanie az acr repozytorium][az-acr-repository-untag]. Miejsce nie jest zwalniana, gdy oznaczenie obrazu, ponieważ jego [manifestu](container-registry-concepts.md#manifest) i dane warstw w rejestrze. Odwołanie tag, sama jest usuwany.

## <a name="delete-by-manifest-digest"></a>Usuń, szyfrowanego manifestu

A [skrótu manifestu](container-registry-concepts.md#manifest-digest) może być skojarzony z jednym, none lub wiele tagów. Po usunięciu przez szyfrowane są usuwane wszystkie tagi, które odwołuje się do manifestu, zgodnie z warstwy danych dla dowolnej warstwy, które są unikatowe dla obrazu. Udostępniane warstwy, której dane nie są usuwane.

Aby usunąć skrót, pierwszej listy manifest skróty służące do repozytorium zawierające obrazy, które chcesz usunąć. Na przykład:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Następnie określ skrót do usunięcia w [az acr repozytorium delete][az-acr-repository-delete] polecenia. Format polecenia jest następujący:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Na przykład można usunąć ostatniego manifest wymienione w powyższym danych wyjściowych (z tagiem "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

`acr-helloworld:v2` Obraz zostanie usunięty z rejestru, ponieważ wszystkie dane warstwy unikatowe dla tego obrazu. Jeśli manifest jest skojarzony z wielu tagów, również zostaną usunięte wszystkie znaczniki skojarzone.

## <a name="delete-digests-by-timestamp"></a>Usuń skróty, znacznik czasu:

Aby zachować rozmiar repozytorium lub rejestru, konieczne może być okresowo usuwać manifestu skróty starsze niż określona data.

Następujące polecenie z wiersza polecenia platformy Azure zawiera listę wszystkich skrótu manifestu w repozytorium, które są starsze niż określona sygnatura czasowa w kolejności rosnącej. Zastąp `<acrName>` i `<repositoryName>` przy użyciu wartości odpowiednich dla danego środowiska. Sygnatura czasowa może być wyrażeniem pełnej daty i godziny lub daty, jak w poniższym przykładzie.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po określeniu starych skróty manifestu, można uruchomić poniższy skrypt powłoki Bash, aby usunąć skróty manifestu starsze niż określona sygnatura czasowa. Wymaga interfejsu wiersza polecenia platformy Azure i **xargs**. Domyślnie skrypt wykonuje nie usuwania. Zmiana `ENABLE_DELETE` wartość `true` umożliwiające usunięcie obrazu.

> [!WARNING]
> Użyj następującego przykładowego skryptu z ostrożnością — usunięto obraz danych jest UNRECOVERABLE. W przypadku systemów, które ściągania obrazów przez skrót manifestu (w przeciwieństwie do nazwy obrazu) nie należy uruchamiać te skrypty. Usunięcie manifestu skróty uniemożliwi tych systemów ściąganie obrazów z rejestru. Zamiast pobierać manifest, należy rozważyć zastosowanie *unikatowy znakowanie* schematu [najlepszym rozwiązaniem jest zalecane][tagging-best-practices]. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Usuwanie obrazów bez znaczników

Jak wspomniano w [skrótu manifestu](container-registry-concepts.md#manifest-digest) sekcji wypychanie zmodyfikowany obraz za pomocą istniejącego tagu **untags** poprzednio włożonego obraz wynikowy obraz oddzielone (lub "delegujące"). Obraz poprzednio włożonego manifestu — i jego danych warstwy — pozostaje w rejestrze. Należy wziąć pod uwagę następująca sekwencja zdarzeń:

1. Wypchnij obraz *acr-helloworld* z tagiem **najnowsze**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty dla repozytorium *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modyfikowanie *acr-helloworld* pliku Dockerfile
1. Wypchnij obraz *acr-helloworld* z tagiem **najnowsze**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty dla repozytorium *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Jak widać w danych wyjściowych w ostatnim kroku w sekwencji, ma teraz oddzielone manifestu, którego `"tags"` właściwość jest pusta lista. Tego manifestu nadal istnieje w rejestrze, wraz z danymi dowolnego unikatowe warstwy, której się odwołuje. **Można usunąć takie oddzielone obrazy i ich warstwy danych, należy usunąć przez skrót manifestu**.

## <a name="delete-all-untagged-images"></a>Usuń wszystkie obrazy bez znaczników

Możesz wyświetlić listę wszystkich obrazów bez znaczników w repozytorium, używając następującego polecenia wiersza polecenia platformy Azure. Zastąp `<acrName>` i `<repositoryName>` przy użyciu wartości odpowiednich dla danego środowiska.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

W skrypcie, przy użyciu tego polecenia, możesz usunąć wszystkich obrazów bez znaczników w repozytorium.

> [!WARNING]
> Następujące przykładowe skrypty należy używać ostrożnie — usunięte dane obrazu są UNRECOVERABLE. W przypadku systemów, które ściągania obrazów przez skrót manifestu (w przeciwieństwie do nazwy obrazu) nie należy uruchamiać te skrypty. Usuwanie obrazów nieoznakowany uniemożliwi tych systemów ściąganie obrazów z rejestru. Zamiast pobierać manifest, należy rozważyć zastosowanie *unikatowy znakowanie* schematu [najlepszym rozwiązaniem jest zalecane][tagging-best-practices].

**Wiersza polecenia platformy Azure w powłoce Bash**

Poniższy skrypt powłoki Bash spowoduje usunięcie wszystkich obrazów nieoznakowany z repozytorium. Wymaga interfejsu wiersza polecenia platformy Azure i **xargs**. Domyślnie skrypt wykonuje nie usuwania. Zmiana `ENABLE_DELETE` wartość `true` umożliwiające usunięcie obrazu.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Wiersza polecenia platformy Azure w programie PowerShell**

Poniższy skrypt programu PowerShell powoduje usunięcie wszystkich nieoznakowany obrazów z repozytorium. Wymaga programu PowerShell i wiersza polecenia platformy Azure. Domyślnie skrypt wykonuje nie usuwania. Zmiana `$enableDelete` wartość `$TRUE` umożliwiające usunięcie obrazu.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat magazyn obrazów w usłudze Azure Container Registry zobacz [magazyn obrazów kontenerów w usłudze Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
