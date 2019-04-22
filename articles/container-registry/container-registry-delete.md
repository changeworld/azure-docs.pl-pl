---
title: Usuń zasoby obrazów w usłudze Azure Container Registry
description: Szczegółowe informacje na temat sposobu skutecznie zarządzać rozmiarem rejestru, usuwając dane obrazu w kontenerze.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/04/2019
ms.author: danlep
ms.openlocfilehash: 1e496002c869c5d2c072773d37ed5fd5d4a5841e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683464"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Usuwanie obrazów kontenerów w usłudze Azure Container Registry

Aby zachować rozmiaru usługi Azure container registry, należy okresowo usuwać danych starych obrazów. Podczas gdy niektóre obrazy kontenera wdrażane w środowisku produkcyjnym mogą wymagać przechowywania długoterminowego przechowywania, inne zazwyczaj można usunąć szybciej. Na przykład w automatycznej kompilacji i w scenariuszu testu rejestru można szybko wypełnić przy użyciu obrazów, które nigdy nie mogą być wdrażane i mogą być czyszczone wkrótce po ukończeniu przebiegu kompilacji i testowania.

Ponieważ dane obrazów można usunąć na kilka różnych sposobów, jest ważne, aby zrozumieć, jak każda operacja usuwania wpływa na użycie magazynu. W tym artykule najpierw przedstawiono składniki, do rejestru i kontenera obrazów platformy Docker, a następnie obejmuje kilka metod związanych z usuwaniem danych obrazu. Przykładowe skrypty znajdują się w celu automatyzacji operacji usuwania.

## <a name="registry"></a>Rejestr

Kontener *rejestru* to usługa, która przechowuje i rozpowszechnianie obrazów kontenerów. Usługa docker Hub jest publicznego rejestru kontenerów platformy Docker, a usługi Azure Container Registry zapewnia Docker prywatnych rejestrów kontenerów na platformie Azure.

## <a name="repository"></a>Repozytorium

Rejestry kontenerów, zarządzanie *repozytoriów*, kolekcje obrazy kontenera przy użyciu tej samej nazwie, ale różnych znaczników. Na przykład następujące trzy obrazy znajdują się w repozytorium "acr-helloworld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Nazwy repozytorium mogą również zawierać [przestrzenie nazw](container-registry-best-practices.md#repository-namespaces). Przestrzenie nazw umożliwiają grupę obrazów przy użyciu nazwy do przodu repozytorium rozdzielonych ukośnikiem, na przykład:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Składniki obrazu

Obraz kontenera w rejestrze jest skojarzony z co najmniej jednego znacznika, ma jedną lub więcej warstw i jest identyfikowany przez manifestu. Zrozumienie, jak tych składników powiązane są ze sobą może pomóc określić najlepszą metodę dla zwalnianie miejsca w rejestrze.

### <a name="tag"></a>Tag

Obraz *tag* określa jego wersję. Pojedynczy obraz w repozytorium można przypisać jeden lub wiele tagów i mogą być również "nieoznakowany". Oznacza to możesz usunąć wszystkie tagi z obrazu, podczas gdy dane obrazu (warstwy) pozostaje w rejestrze.

Repozytorium (lub repozytorium i przestrzeni nazw) oraz tag Określa nazwę obrazu. Można wypychanie i ściąganie obrazu, określając jej nazwę w operacji wypychania i ściągania.

W rejestrze prywatnym, takich jak usługa Azure Container Registry nazwa obrazu zawiera w pełni kwalifikowaną nazwę hosta rejestru. Host rejestr obrazów ACR jest w formacie *acrname.azurecr.io* (tylko małe litery). Na przykład będzie pełna nazwa pierwszy obraz w przestrzeni nazw "marketing" w poprzedniej sekcji:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Do dyskusji na temat obrazów, tagowanie najlepszych rozwiązań, zobacz [znakowanie platformy Docker: Najlepsze rozwiązania dotyczące obrazów platformy docker przechowywanie wersji i tagowania] [ tagging-best-practices] wpis w blogu w witrynie MSDN.

### <a name="layer"></a>Warstwa

Obrazy składają się z co najmniej jeden *warstwy*, odpowiadający każdego wiersza w pliku Dockerfile, który definiuje obraz. Obrazów w rejestrze Udostępnij wspólnej warstwy, zwiększając wydajność magazynu. Na przykład kilka obrazów w różnych repozytoriach może współużytkować tę samą warstwę podstawowa Alpine Linux, ale tylko jedną kopię tej warstwy są przechowywane w rejestrze.

Udostępnianie warstwy optymalizuje też dystrybucji warstwy do węzłów przy użyciu wielu obrazów udostępnianie wspólnej warstwy. Na przykład jeśli obraz już w węźle zawiera warstwę Alpine Linux jako jego podstawowy, kolejne ściąganie inny obraz, który odwołuje się do tej samej warstwie nie przenieść warstwę do węzła. Zamiast tego odwołuje się warstwy już istniejących w węźle.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera, wypychany do rejestru kontenera jest skojarzony z *manifestu*. Manifest, generowane przez rejestr obrazu zostanie przypisany, unikatowo identyfikuje obraz i określa jej warstwy. Możesz wyświetlić listę manifesty dla repozytorium przy użyciu polecenia interfejsu wiersza polecenia Azure [az acr repozytorium show manifesty][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Na przykład lista manifest skróty służące do repozytorium "acr-helloworld":

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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

Manifest omówionych w tym miejscu różni się od manifestu obrazu można wyświetlić w witrynie Azure portal lub za pomocą [sprawdzanie manifestu docker][docker-manifest-inspect]. W poniższej sekcji "manifestu skrót" odnosi się do szyfrowanego generowane przez operację push nie *config.digest* w manifeście obrazu. Można ściągnąć i usuwać obrazów **skrótu manifestu**, nie config.digest. Na poniższym obrazie przedstawiono dwa rodzaje skróty.

![Podsumowanie manifestu i config.digest w witrynie Azure portal][manifest-digest]

### <a name="manifest-digest"></a>Podsumowanie manifestu

Manifesty są identyfikowane przez unikatową Skrót SHA-256, lub *skrótu manifestu*. Każdy obraz — czy oznakowane lub nie — jest identyfikowane przez jego skrót. Wartość skrótu jest unikatowa, nawet jeśli dane warstw obrazów jest taka sama jak w przypadku innego obrazu. Ten mechanizm jest o tym, co pozwala na wielokrotne wypychanie identycznie oznakowane obrazów do rejestru. Na przykład możesz wielokrotnie wypchnąć `myimage:latest` do rejestru bez błędów, ponieważ każdy obraz jest identyfikowane przez jego unikatowy skrót.

Można ściągnąć obraz z rejestru, określając jego skrót w operacji ściągania. Niektóre systemy może być skonfigurowany do ściągnięcia przez szyfrowane, ponieważ gwarantuje to wersja obrazu są pobierane, nawet wtedy, gdy identycznie TIFF jest następnie wypchnięte do rejestru.

Na przykład ściąganie obrazów z repozytorium "acr-helloworld" przez skrót manifestu:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> W przypadku modyfikacji obrazów z tagami identyczny wielokrotnie wypchnięcia, możesz utworzyć oddzielone obrazy — obrazy, które są nieoznakowany, ale nadal zużywają miejsca w rejestrze. Nieoznakowany obrazy nie są wyświetlane w interfejsie wiersza polecenia platformy Azure lub w witrynie Azure portal po liście lub wyświetlanie obrazów według tagów. Jednak ich warstwy nadal istnieje i zajmować dużo miejsca w rejestrze. [Usunąć zdjęcia bez znaczników](#delete-untagged-images) dalszej części tego artykułu w tym artykule omówiono zwalnianie miejsca nieoznakowany obrazów.

## <a name="delete-image-data"></a>Usuń obraz danych

Dane obrazów można usunąć z rejestru kontenerów na kilka sposobów:

* Usuń [repozytorium](#delete-repository): Usuwa wszystkie obrazy i wszystkie warstwy unikatowy w repozytorium.
* Usuń, [tag](#delete-by-tag): Usuwa obrazu, tag, wszystkie warstwy unikatowy odwołuje się obraz i wszystkie inne znaczniki skojarzone z obrazem.
* Usuń, [skrótu manifestu](#delete-by-manifest-digest): Usuwa obraz, wszystkie warstwy unikatowy odwołuje się obraz i wszystkie znaczniki skojarzone z obrazem.

## <a name="delete-repository"></a>Usuwanie repozytorium

Usuwanie repozytorium powoduje usunięcie wszystkich obrazów w repozytorium, m.in. wszystkie tagi unikatowy warstwy i manifesty. Jeśli usuniesz repozytorium, można odzyskać miejsce do magazynowania używane przez obrazów, które były w tym repozytorium.

Następujące wiersza polecenia platformy Azure polecenie usuwa repozytorium "acr-helloworld" wszystkie tagi i manifesty w repozytorium. W przypadku warstwy odwołuje się usunięto manifesty nie odwołują się inne obrazy, w rejestrze, Warstwa danych również zostaną usunięte.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Usuwanie według tagu

Pojedyncze obrazy można usunąć z repozytorium, określając nazwę repozytorium i tagów w operacji usuwania. Jeśli usuniesz według tagów, można odzyskać miejsce do magazynowania używane przez wszystkie unikatowe warstw obrazu (warstwy nie są współużytkowane przez wszystkie inne obrazów w rejestrze).

Aby usunąć według tagów, użyj [az acr repozytorium delete] [ az-acr-repository-delete] i określ nazwę obrazu w `--image` parametru. Usuwane są wszystkie warstwy unikatowe dla obrazu i innych tagów skojarzonych z obrazem.

Na przykład usunięcie "acr-helloworld:latest" obraz z rejestru "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Usuwanie *według tagów* nie powinny być mylone z usuwanie tagu (zdejmowanie). Można usunąć tagu za pomocą polecenia interfejsu wiersza polecenia Azure [usunąć oznakowanie az acr repozytorium][az-acr-repository-untag]. Miejsce nie jest zwalniana, gdy oznaczenie obrazu, ponieważ jego [manifestu](#manifest) i dane warstw w rejestrze. Odwołanie tag, sama jest usuwany.

## <a name="delete-by-manifest-digest"></a>Usuń, szyfrowanego manifestu

A [skrótu manifestu](#manifest-digest) może być skojarzony z jednym, none lub wiele tagów. Po usunięciu przez szyfrowane są usuwane wszystkie tagi, które odwołuje się do manifestu, zgodnie z warstwy danych dla dowolnej warstwy, które są unikatowe dla obrazu. Udostępniane warstwy, której dane nie są usuwane.

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

Następnie określ skrót do usunięcia w [az acr repozytorium delete] [ az-acr-repository-delete] polecenia. Format polecenia jest następujący:

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

### <a name="list-digests-by-timestamp"></a>Skróty listy według sygnatur czasowych

Aby zachować rozmiar repozytorium lub rejestru, konieczne może być okresowo usuwać manifestu skróty starsze niż określona data.

Następujące polecenie z wiersza polecenia platformy Azure zawiera listę wszystkich skrótu manifestu w repozytorium, które są starsze niż określona sygnatura czasowa w kolejności rosnącej. Zastąp `<acrName>` i `<repositoryName>` przy użyciu wartości odpowiednich dla danego środowiska. Sygnatura czasowa może być wyrażeniem pełnej daty i godziny lub daty, jak w poniższym przykładzie.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

### <a name="delete-digests-by-timestamp"></a>Usuń skróty, znacznik czasu:

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

Jak wspomniano w [skrótu manifestu](#manifest-digest) sekcji wypychanie zmodyfikowany obraz za pomocą istniejącego tagu **untags** poprzednio włożonego obraz wynikowy obraz oddzielone (lub "delegujące"). Obraz poprzednio włożonego manifestu — i jego danych warstwy — pozostaje w rejestrze. Należy wziąć pod uwagę następująca sekwencja zdarzeń:

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

### <a name="list-untagged-images"></a>Wyświetlenie listy obrazów bez znaczników

Możesz wyświetlić listę wszystkich obrazów bez znaczników w repozytorium, używając następującego polecenia wiersza polecenia platformy Azure. Zastąp `<acrName>` i `<repositoryName>` przy użyciu wartości odpowiednich dla danego środowiska.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>Usuń wszystkie obrazy bez znaczników

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
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
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
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat magazyn obrazów w usłudze Azure Container Registry zobacz [magazyn obrazów kontenerów w usłudze Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
