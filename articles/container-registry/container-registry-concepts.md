---
title: Temat repozytoriów i obrazów w usłudze Azure Container Registry
description: Wprowadzenie do podstawowych pojęć rejestry kontenerów platformy Azure, repozytoriów i obrazów kontenerów.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800330"
---
# <a name="about-registries-repositories-and-images"></a>Rejestry, repozytoriów i obrazów — informacje

W tym artykule przedstawiono najważniejsze pojęcia związane z rejestrów kontenerów, repozytoriów i obrazów kontenerów i powiązanych artefaktów. 

## <a name="registry"></a>Rejestr

Kontener *rejestru* to usługa, która przechowuje i rozpowszechnianie obrazów kontenerów. Usługa docker Hub jest rejestru publicznego kontenera, który wspiera społeczność oprogramowania typu open source i służy jako ogólny katalog obrazów. Usługa Azure Container Registry zapewnia użytkownikom bezpośredniej kontroli nad ich obrazów, za pomocą uwierzytelniania zintegrowanego [geografickou replikaci](container-registry-geo-replication.md) obsługując dystrybucji globalnej i niezawodności na potrzeby wdrożenia w pobliżu sieci [ konfiguracja wirtualnej sieci i zapory](container-registry-vnet.md), [blokowania tag](container-registry-image-lock.md), a wiele innych funkcji. 

Oprócz obrazów kontenerów platformy Docker, związane z usługi Azure Container Registry obsługuje [zawartości artefaktów](container-registry-image-formats.md) tym formatów obrazów Otwórz kontener Initiative (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Mogą być adresowane elementów zawartości artefaktu

Adres artefakt w usłudze Azure container registry obejmuje następujące elementy. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** — w pełni kwalifikowaną nazwę hosta rejestru. Hosta rejestru w usłudze Azure container registry jest w formacie *myregistry*. azurecr.io (tylko małe litery). Należy określić loginUrl, korzystając z platformy Docker lub innych narzędzi klienta w celu ściągania lub wypychania artefakty do usługi Azure container registry. 
* **przestrzeń nazw** - rozdzielonych ukośnikiem powodują ustawienie logicznego grupowania powiązanych obrazów lub artefakty — na przykład w przypadku aplikacji lub grupy roboczej
* **artefakt** — nazwę repozytorium dla określonego obrazu lub artefaktu
* **tag** -określoną wersję obrazu lub przechowywane w repozytorium artefaktów


Na przykład Pełna nazwa obrazu w usłudze Azure container registry może mieć następującą postać:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Zobacz następujące sekcje, aby uzyskać szczegółowe informacje o tych elementów.

## <a name="repository-name"></a>Nazwa repozytorium

Rejestry kontenerów, zarządzanie *repozytoriów*, kolekcji obrazów kontenerów lub inne artefakty o takiej samej nazwie, ale różnych znaczników. Na przykład następujące trzy obrazy znajdują się w repozytorium "acr-helloworld":

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

## <a name="image"></a>Image

Obraz kontenera ani innego artefaktu w ramach rejestru jest skojarzony z co najmniej jednego znacznika, ma jedną lub więcej warstw i jest identyfikowany przez manifestu. Zrozumienie, jak tych składników powiązane są ze sobą może pomóc efektywne zarządzanie rejestru.

### <a name="tag"></a>Tag

*Tag* dla obrazu ani innego artefaktu określa jej wersji. Pojedynczego artefakt w repozytorium można przypisać jeden lub wiele tagów i mogą być również "nieoznakowany". Oznacza to możesz usunąć wszystkie tagi z obrazu, podczas gdy dane obrazu (warstwy) pozostaje w rejestrze.

Repozytorium (lub repozytorium i przestrzeni nazw) oraz tag Określa nazwę obrazu. Można wypychanie i ściąganie obrazu, określając jej nazwę w operacji wypychania i ściągania.

Scenariuszy, twórz albo wdrażać je z przewodnikiem jak tagowanie obrazów kontenerów. Na przykład stabilne znaczniki są zalecane w przypadku zachowaniu obrazy podstawowe i unikatowych tagów do wdrażania obrazów. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące obrazów kontenerów przechowywanie wersji i tagowania](container-registry-image-tag-version.md).

### <a name="layer"></a>Warstwa

Obrazy kontenera składają się z co najmniej jeden *warstwy*, odpowiadający każdego wiersza w pliku Dockerfile, który definiuje obraz. Obrazów w rejestrze Udostępnij wspólnej warstwy, zwiększając wydajność magazynu. Na przykład kilka obrazów w różnych repozytoriach może współużytkować tę samą warstwę podstawowa Alpine Linux, ale tylko jedną kopię tej warstwy są przechowywane w rejestrze.

Udostępnianie warstwy optymalizuje też dystrybucji warstwy do węzłów przy użyciu wielu obrazów udostępnianie wspólnej warstwy. Na przykład jeśli obraz już w węźle zawiera warstwę Alpine Linux jako jego podstawowy, kolejne ściąganie inny obraz, który odwołuje się do tej samej warstwie nie przenieść warstwę do węzła. Zamiast tego odwołuje się warstwy już istniejących w węźle.

Zapewnienie bezpiecznej izolacji i ochronę przed potencjalnym manipulowania warstwy warstwy nie są współdzielone przez rejestrów.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera lub artefaktu wypchnięte do rejestru kontenera jest skojarzony z *manifestu*. Manifest, generowane przez rejestr obrazu zostanie przypisany, unikatowo identyfikuje obraz i określa jej warstwy. Możesz wyświetlić listę manifesty dla repozytorium przy użyciu polecenia interfejsu wiersza polecenia Azure [az acr repozytorium show manifesty][az-acr-repository-show-manifests]:

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

### <a name="manifest-digest"></a>Podsumowanie manifestu

Manifesty są identyfikowane przez unikatową Skrót SHA-256, lub *skrótu manifestu*. Każdy obraz lub artefaktu — czy oznakowane lub nie — jest identyfikowane przez jego skrót. Wartość skrótu jest unikatowa, nawet jeśli dane warstw obrazów jest taka sama jak w przypadku innego obrazu. Ten mechanizm jest o tym, co pozwala na wielokrotne wypychanie identycznie oznakowane obrazów do rejestru. Na przykład możesz wielokrotnie wypchnąć `myimage:latest` do rejestru bez błędów, ponieważ każdy obraz jest identyfikowane przez jego unikatowy skrót.

Można ściągnąć obraz z rejestru, określając jego skrót w operacji ściągania. Niektóre systemy może być skonfigurowany do ściągnięcia przez szyfrowane, ponieważ gwarantuje to wersja obrazu są pobierane, nawet wtedy, gdy identycznie TIFF jest następnie wypchnięte do rejestru.

Na przykład ściągnąć obraz z repozytorium "acr-helloworld" przez skrót manifestu:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> W przypadku modyfikacji obrazów z tagami identyczny wielokrotnie wypchnięcia, możesz utworzyć oddzielone obrazy — obrazy, które są nieoznakowany, ale nadal zużywają miejsca w rejestrze. Nieoznakowany obrazy nie są wyświetlane w interfejsie wiersza polecenia platformy Azure lub w witrynie Azure portal po liście lub wyświetlanie obrazów według tagów. Jednak ich warstwy nadal istnieje i zajmować dużo miejsca w rejestrze. Aby dowiedzieć się, jak zwalnianie miejsca używanego przez nieoznakowany obrazów, zobacz [usuwanie obrazów kontenerów w usłudze Azure Container Registry](container-registry-delete.md).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [obrazu magazynu](container-registry-storage.md) i [obsługiwane formaty zawartości](container-registry-image-formats.md) w usłudze Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


