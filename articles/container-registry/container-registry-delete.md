---
title: Usuwanie zasobów obrazów
description: Szczegółowe informacje na temat efektywnego zarządzania rozmiarem rejestru przez usunięcie danych obrazu kontenera przy użyciu poleceń interfejsu wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403342"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Aby zachować rozmiar rejestru kontenerów platformy Azure, należy okresowo usuwać przestarzałe dane obrazu. Niektóre obrazy kontenerów wdrożone w procesach produkcyjnych mogą wymagać dłuższego magazynu, inne zazwyczaj można usunąć szybciej. Na przykład w scenariuszu automatycznej kompilacji i testowania rejestru można szybko wypełnić obrazów, które nigdy nie mogą być wdrożone i mogą być czyszczone wkrótce po zakończeniu kompilacji i przebiegu testu.

Ponieważ można usunąć dane obrazu na kilka różnych sposobów, ważne jest, aby zrozumieć, jak każda operacja usuwania wpływa na użycie magazynu. W tym artykule opisano kilka metod usuwania danych obrazu:

* Usuwanie [repozytorium:](#delete-repository)Usuwa wszystkie obrazy i wszystkie unikatowe warstwy w repozytorium.
* Usuń według [znacznika](#delete-by-tag): Usuwa obraz, znacznik, wszystkie unikatowe warstwy, do których odwołuje się obraz, oraz wszystkie inne znaczniki skojarzone z obrazem.
* Usuń przez [podsumowanie manifestu](#delete-by-manifest-digest): Usuwa obraz, wszystkie unikatowe warstwy, do których odwołuje się obraz, oraz wszystkie znaczniki skojarzone z obrazem.

Przykładowe skrypty są dostarczane w celu automatyzacji operacji usuwania.

Aby zapoznać się z wprowadzeniem do tych pojęć, zobacz [Informacje o rejestrach, repozytoriach i obrazach](container-registry-concepts.md).

## <a name="delete-repository"></a>Usuwanie repozytorium

Usunięcie repozytorium powoduje usunięcie wszystkich obrazów w repozytorium, w tym wszystkich znaczników, unikatowych warstw i manifestów. Po usunięciu repozytorium można odzyskać miejsce do magazynowania używane przez obrazy, które odwołują się do unikatowych warstw w tym repozytorium.

Następujące polecenie interfejsu wiersza polecenia platformy Azure usuwa repozytorium "acr-helloworld" oraz wszystkie tagi i manifesty w repozytorium. Jeśli do warstw, do których odwołują się usunięte manifesty, nie są odwoływane żadne inne obrazy w rejestrze, ich dane warstw są również usuwane, odzyskując miejsce do magazynowania.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Usuń według tagu

Poszczególne obrazy można usunąć z repozytorium, określając nazwę repozytorium i znacznik w operacji usuwania. Po usunięciu przez znacznik, można odzyskać miejsce do magazynowania używane przez wszystkie unikatowe warstwy na obrazie (warstwy nie są współużytkowane przez inne obrazy w rejestrze).

Aby usunąć według [tagu, użyj az acr repozytorium delete][az-acr-repository-delete] i określ nazwę obrazu w parametrze. `--image` Usuwane są wszystkie warstwy unikatowe dla obrazu oraz wszystkie inne znaczniki skojarzone z obrazem.

Na przykład usunięcie obrazu "acr-helloworld:latest" z rejestru "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Usuwanie *przez tag* nie należy mylić z usuwaniem znacznika (odblokowata). Tag można usunąć za pomocą polecenia azure cli [az acr repozytorium untag][az-acr-repository-untag]. Po odznaczeniu obrazu nie zostanie zwolniona żadna przestrzeń, ponieważ jego [dane manifestu](container-registry-concepts.md#manifest) i warstwy pozostaną w rejestrze. Usuwany jest tylko sam numer referencyjny tagu.

## <a name="delete-by-manifest-digest"></a>Usuń przez podsumowanie manifestu

Podsumowanie [manifestu](container-registry-concepts.md#manifest-digest) może być skojarzone z jednym, żadnym lub wielokrotnym znacznikiem. Po usunięciu przez skróty usuwane są wszystkie znaczniki, do których odwołuje się manifest, podobnie jak dane warstw dla wszystkich warstw unikatowych dla obrazu. Udostępnione dane warstwy nie są usuwane.

Aby usunąć przez skróty, najpierw wyświetl podsumowanie manifestu dla repozytorium zawierającego obrazy, które chcesz usunąć. Przykład:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

Następnie określ skrót, który chcesz usunąć w poleceniu [az acr repozytorium delete.][az-acr-repository-delete] Format polecenia jest następujący:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Na przykład, aby usunąć ostatni manifest wymieniony w poprzednim wyjściu (z tagiem "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

Obraz `acr-helloworld:v2` zostanie usunięty z rejestru, podobnie jak wszystkie dane warstwy unikatowe dla tego obrazu. Jeśli manifest jest skojarzony z wieloma znacznikami, wszystkie skojarzone tagi są również usuwane.

## <a name="delete-digests-by-timestamp"></a>Usuwanie skrótów według sygnatury czasowych

Aby zachować rozmiar repozytorium lub rejestru, może być konieczne okresowe usuwanie podsumowań manifestów starszych niż określona data.

Następujące polecenie interfejsu wiersza polecenia platformy Azure zawiera listę wszystkich podsumowań manifestu w repozytorium starszych niż określony sygnatura czasowa w kolejności rosnącej. Zastąp `<acrName>` i `<repositoryName>` wartościami odpowiednimi dla danego środowiska. Sygnatura czasowa może być wyrażeniem pełnej daty i godziny lub datą, jak w tym przykładzie.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po zidentyfikowaniu starych podsumowań manifestu, można uruchomić następujący skrypt Bash, aby usunąć podsumowanie manifestu starsze niż określony sygnatura czasowa. Wymaga interfejsu wiersza polecenia platformy Azure i **xargs**. Domyślnie skrypt nie wykonuje żadnych usuwania. Zmień `ENABLE_DELETE` wartość, `true` aby włączyć usuwanie obrazu.

> [!WARNING]
> Użyj następującego przykładowego skryptu z ostrożnością - usunięte dane obrazu są nieodzyskane. Jeśli masz systemy, które ściągają obrazy przez podsumowanie manifestu (w przeciwieństwie do nazwy obrazu), nie należy uruchamiać tych skryptów. Usunięcie podsumowań manifestu uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ciągnąć przez manifest, należy rozważyć przyjęcie unikalnego schematu *znakowania,* [zalecana najlepsza praktyka](container-registry-image-tag-version.md). 

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

Jak wspomniano w [sekcji Podsumowanie manifestu,](container-registry-concepts.md#manifest-digest) wypychanie zmodyfikowanego obrazu przy użyciu istniejącego **znacznika powoduje odznaczenie** wcześniej wypchniętego obrazu, co powoduje, że obraz osierocony (lub "zwisający"). Manifest wcześniej wypchniętego obrazu — i jego dane warstwowe — pozostaje w rejestrze. Należy wziąć pod uwagę następującą sekwencję zdarzeń:

1. Push obraz *acr-helloworld* z **tagiem najnowsze:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty dla repozytorium *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
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

1. Modyfikowanie pliku dockerfile *acr-helloworld*
1. Push obraz *acr-helloworld* z **tagiem najnowsze:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty dla repozytorium *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
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

Jak widać w danych wyjściowych ostatniego kroku w sekwencji, jest `"tags"` teraz oddzielony manifest, którego właściwość jest pustą listą. Ten manifest nadal istnieje w rejestrze, wraz z unikatowymi danymi warstwy, do których się odwołuje. **Aby usunąć takie osierocone obrazy i ich dane warstwy, należy usunąć przez podsumowanie manifestu**.

## <a name="delete-all-untagged-images"></a>Usuwanie wszystkich obrazów bez znaczników

Można wyświetlić listę wszystkich obrazów nieoznakowanych w repozytorium przy użyciu następującego polecenia interfejsu wiersza polecenia platformy Azure. Zastąp `<acrName>` i `<repositoryName>` wartościami odpowiednimi dla danego środowiska.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Za pomocą tego polecenia w skrypcie można usunąć wszystkie nieoznakowane obrazy w repozytorium.

> [!WARNING]
> Użyj następujących przykładowych skryptów z ostrożnością - usunięte dane obrazu są nieodzyskane. Jeśli masz systemy, które ściągają obrazy przez podsumowanie manifestu (w przeciwieństwie do nazwy obrazu), nie należy uruchamiać tych skryptów. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ciągnąć przez manifest, należy rozważyć przyjęcie unikalnego schematu *znakowania,* [zalecana najlepsza praktyka](container-registry-image-tag-version.md).

**Interfejsu wiersza polecenia platformy Azure w bashu**

Poniższy skrypt Bash usuwa wszystkie nieoznakowane obrazy z repozytorium. Wymaga interfejsu wiersza polecenia platformy Azure i **xargs**. Domyślnie skrypt nie wykonuje żadnych usuwania. Zmień `ENABLE_DELETE` wartość, `true` aby włączyć usuwanie obrazu.

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
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Narzędzie CLI platformy Azure w programie PowerShell**

Poniższy skrypt programu PowerShell usuwa wszystkie nieoznakowane obrazy z repozytorium. Wymaga programu PowerShell i interfejsu wiersza polecenia platformy Azure. Domyślnie skrypt nie wykonuje żadnych usuwania. Zmień `$enableDelete` wartość, `$TRUE` aby włączyć usuwanie obrazu.

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


## <a name="automatically-purge-tags-and-manifests-preview"></a>Automatyczne przeczyszczanie tagów i manifestów (wersja zapoznawcza)

Jako alternatywę dla skryptów azure cli polecenia, uruchom zadanie na żądanie lub zaplanowane ACR, aby usunąć wszystkie tagi, które są starsze niż określony czas trwania lub dopasować filtr określonej nazwy. Aby uzyskać więcej informacji, zobacz [Automatyczne czyszczenie obrazów z rejestru kontenerów platformy Azure](container-registry-auto-purge.md).

Opcjonalnie ustaw [zasady przechowywania](container-registry-retention-policy.md) dla każdego rejestru, aby zarządzać nieoznakowanymi manifestami. Po włączeniu zasad przechowywania manifesty obrazów w rejestrze, które nie mają żadnych skojarzonych tagów, oraz dane warstwy podstawowej są automatycznie usuwane po upływie określonego czasu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat magazynu obrazów w rejestrze kontenerów platformy Azure, zobacz [magazyn obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
