---
title: Repozytoria & obrazy – informacje
description: Wprowadzenie do kluczowych pojęć rejestrów kontenerów platformy Azure, repozytoriów i obrazów kontenerów.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247061"
---
# <a name="about-registries-repositories-and-images"></a>Rejestry, repozytoria i obrazy – informacje

W tym artykule przedstawiono kluczowe pojęcia rejestrów kontenerów, repozytoriów i obrazów kontenerów i powiązanych artefaktów. 

## <a name="registry"></a>Rejestr

*Rejestr* kontenerów to usługa, która przechowuje i dystrybuuje obrazy kontenerów. Docker Hub to publiczny rejestr kontenerów, który obsługuje społeczność open source i służy jako ogólny katalog obrazów. Usługa Azure Container Registry zapewnia użytkownikom bezpośrednią kontrolę nad swoimi obrazami, zintegrowane uwierzytelnianie, [replikację geograficzną](container-registry-geo-replication.md) obsługującą dystrybucję globalną i niezawodność w przypadku wdrożeń w pobliżu sieci, [konfiguracji sieci wirtualnej i zapory,](container-registry-vnet.md) [blokowania znaczników](container-registry-image-lock.md)i wielu innych ulepszonych funkcji. 

Oprócz obrazów kontenerów platformy Docker usługa Azure Container Registry obsługuje [powiązane artefakty zawartości,](container-registry-image-formats.md) w tym formaty obrazów Open Container Initiative (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Adresowalne elementy artefaktu

Adres artefaktu w rejestrze kontenerów platformy Azure zawiera następujące elementy. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - w pełni kwalifikowana nazwa hosta rejestru. Host rejestru w rejestrze kontenerów platformy Azure jest w formacie *myregistry*.azurecr.io (wszystkie małe litery). Należy określić loginUrl podczas korzystania z platformy Docker lub innych narzędzi klienckich do ściągania lub wypychania artefaktów do rejestru kontenerów platformy Azure. 
* **obszar nazw** — grupowanie logiczne rozdzielane ukośnikiem powiązanych obrazów lub artefaktów — na przykład dla grupy roboczej lub aplikacji
* **artefakt** — nazwa repozytorium określonego obrazu lub artefaktu
* **tag** — określona wersja obrazu lub artefaktu przechowywanego w repozytorium


Na przykład pełna nazwa obrazu w rejestrze kontenerów platformy Azure może wyglądać następująco:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Zobacz poniższe sekcje, aby uzyskać szczegółowe informacje na temat tych elementów.

## <a name="repository-name"></a>Nazwa repozytorium

Rejestry kontenerów zarządzają *repozytoriami,* kolekcjami obrazów kontenerów lub innych artefaktów o tej samej nazwie, ale z różnymi tagami. Na przykład następujące trzy obrazy znajdują się w repozytorium "acr-helloworld":


- *acr-helloworld:najnowsze*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Nazwy repozytorium mogą również zawierać [przestrzenie nazw](container-registry-best-practices.md#repository-namespaces). Przestrzenie nazw umożliwiają grupowanie obrazów przy użyciu nazw repozytorium rozdzielanych ukośnikiem, na przykład:

- *marketing/kampania10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *zwroty produktu/przesyłanie stron internetowych:20180604*
- *zwroty produktu/starszy integrator:20180715*

## <a name="image"></a>Image (Obraz)

Obraz kontenera lub inny artefakt w rejestrze jest skojarzony z co najmniej jednym znacznikiem, ma jedną lub więcej warstw i jest identyfikowany przez manifest. Zrozumienie, jak te składniki odnoszą się do siebie nawzajem może pomóc w skutecznym zarządzaniu rejestrem.

### <a name="tag"></a>Tag

*Znacznik* obrazu lub innego artefaktu określa jego wersję. Pojedynczy artefakt w repozytorium można przypisać jeden lub wiele tagów, a także może być "nieoznakowany." Oznacza to, że można usunąć wszystkie znaczniki z obrazu, podczas gdy dane obrazu (jego warstwy) pozostają w rejestrze.

Repozytorium (lub repozytorium i obszar nazw) wraz z tagiem definiuje nazwę obrazu. Obraz można wypchnąć i wyciągnąć, określając jego nazwę w operacji wypychania lub ściągania.

Sposób tagowanie obrazów kontenerów jest kierowany przez scenariusze do ich opracowania lub wdrożenia. Na przykład tagi stabilne są zalecane do obsługi obrazów podstawowych i unikatowe znaczniki do wdrażania obrazów. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące tagowania i przechowywania obrazów kontenerów](container-registry-image-tag-version.md).

### <a name="layer"></a>Warstwa

Obrazy kontenerów składa się z jednej lub więcej *warstw*, każda odpowiadająca linii w pliku dockerfile, który definiuje obraz. Obrazy w rejestrze współużytkuje wspólne warstwy, zwiększając wydajność magazynu. Na przykład kilka obrazów w różnych repozytoriach może współużytkować tę samą warstwę podstawową systemu Alpine Linux, ale tylko jedna kopia tej warstwy jest przechowywana w rejestrze.

Udostępnianie warstw optymalizuje również rozkład warstw do węzłów z wieloma obrazami udostępniającymi wspólne warstwy. Na przykład jeśli obraz już w węźle zawiera warstwę Linuksa Alpine jako bazę, kolejne pobranie innego obrazu odwołującego się do tej samej warstwy nie przenosi warstwy do węzła. Zamiast tego odwołuje się do warstwy już istniejącej w węźle.

Aby zapewnić bezpieczną izolację i ochronę przed potencjalną manipulacją warstwą, warstwy nie są współużytkowane przez rejestry.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera lub artefakt wypchnięty do rejestru kontenerów jest skojarzony z *manifestem*. Manifest, generowany przez rejestr, gdy obraz jest wypychany, jednoznacznie identyfikuje obraz i określa jego warstwy. Można wyświetlić listę manifestów dla repozytorium za pomocą polecenia interfejsu wiersza polecenia azure [az acr repozytorium show-manifests:][az-acr-repository-show-manifests]

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Na przykład wyświetl listę manifestów repozytorium "acr-helloworld":

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

Manifesty są identyfikowane przez unikatowy skrót SHA-256 lub *podsumowanie manifestu*. Każdy obraz lub artefakt — niezależnie od tego, czy jest oznaczony, czy nie — jest identyfikowany przez jego skrót. Wartość skrótu jest unikatowa, nawet jeśli dane warstwy obrazu są identyczne z danymi innego obrazu. Mechanizm ten pozwala wielokrotnie wypchnąć identycznie oznakowane obrazy do rejestru. Na przykład można wielokrotnie `myimage:latest` wypychać do rejestru bez błędu, ponieważ każdy obraz jest identyfikowany przez jego unikatowe podsumowanie.

Obraz można pobierać z rejestru, określając jego skrót w operacji ściągania. Niektóre systemy mogą być skonfigurowane do ciągnięcia przez skrót, ponieważ gwarantuje, że wersja obrazu jest ściągana, nawet jeśli obraz o identycznym tagowaniu jest następnie wypychany do rejestru.

Na przykład ściągnij obraz z repozytorium "acr-helloworld" przez podsumowanie manifestu:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Jeśli wielokrotnie wypychasz zmodyfikowane obrazy z identycznymi tagami, możesz utworzyć osierocone obrazy — obrazy, które nie są oznaczane, ale nadal zużywają miejsce w rejestrze. Obrazy bez znaczników nie są wyświetlane w interfejsu wiersza polecenia platformy Azure ani w witrynie Azure portal podczas listy lub wyświetlania obrazów według tagów. Jednak ich warstwy nadal istnieją i zajmują miejsce w rejestrze. Usunięcie nieoznakowanego obrazu zwalnia miejsce w rejestrze, gdy manifest jest jedynym lub ostatnim, wskazującym określoną warstwę. Aby uzyskać informacje dotyczące zwalniania miejsca używanego przez obrazy nieoznakowane, zobacz [Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-delete.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [magazynie obrazów](container-registry-storage.md) i [obsługiwanych formatach zawartości](container-registry-image-formats.md) w rejestrze kontenerów platformy Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


