---
title: Importowanie obrazów kontenerów
description: Importowanie obrazów kontenerów do rejestru kontenerów platformy Azure przy użyciu interfejsów API platformy Azure bez konieczności uruchamiania poleceń platformy Docker.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051920"
---
# <a name="import-container-images-to-a-container-registry"></a>Importowanie obrazów kontenerów do rejestru kontenerów

Można łatwo importować (kopiować) obrazy kontenerów do rejestru kontenerów platformy Azure, bez użycia poleceń platformy Docker. Na przykład importowanie obrazów z rejestru deweloperów do rejestru produkcyjnego lub kopiowanie obrazów bazowych z rejestru publicznego.

Usługa Azure Container Registry obsługuje szereg typowych scenariuszy do kopiowania obrazów z istniejącego rejestru:

* Importowanie z rejestru publicznego

* Importowanie z innego rejestru kontenerów platformy Azure w tej samej lub innej subskrypcji platformy Azure

* Importowanie z rejestru kontenerów prywatnych innych niż azure

Importowanie obrazów do rejestru kontenerów platformy Azure ma następujące korzyści w przypadku korzystania z poleceń interfejsu wiersza polecenia platformy Docker:

* Ponieważ środowisko klienta nie wymaga lokalnej instalacji platformy Docker, zaimportuj dowolny obraz kontenera, niezależnie od obsługiwanego typu systemu operacyjnego.

* Podczas importowania obrazów wielowymiężników (takich jak oficjalne obrazy platformy Docker) obrazy dla wszystkich architektur i platform określonych na liście manifestów są kopiowane.

Aby zaimportować obrazy kontenerów, w tym artykule wymagane jest uruchomienie interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell lub lokalnie (zalecane w wersji 2.0.55 lub nowszej). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!NOTE]
> Jeśli chcesz rozpowszechniać identyczne obrazy kontenerów w wielu regionach platformy Azure, usługa Azure Container Registry obsługuje również [replikację geograficzną.](container-registry-geo-replication.md) Dzięki replikowaniu geograficznemu rejestru (wymagana warstwa usługi Premium) można obsługiwać wiele regionów o identycznych nazwach obrazów i tagów z jednego rejestru.
>

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze rejestru kontenerów platformy Azure, utwórz rejestr. Aby uzyskać instrukcje, zobacz [Szybki start: Tworzenie rejestru kontenerów prywatnych przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

Aby zaimportować obraz do rejestru kontenerów platformy Azure, twoja tożsamość musi mieć uprawnienia do zapisu w rejestrze docelowym (co najmniej rola współautora). Zobacz [Role i uprawnienia rejestru kontenerów platformy Azure](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importowanie z rejestru publicznego

### <a name="import-from-docker-hub"></a>Importowanie z centrum platformy Docker

Na przykład użyj polecenia [importu az acr,][az-acr-import] aby zaimportować obraz wieloascenowy `hello-world:latest` z Centrum platformy Docker do rejestru o nazwie *myregistry*. Ponieważ `hello-world` jest to oficjalny obraz z usługi Docker `library` Hub, ten obraz znajduje się w domyślnym repozytorium. Dołącz nazwę repozytorium i opcjonalnie znacznik w `--source` wartości parametru obrazu. (Opcjonalnie można zidentyfikować obraz przez jego podsumowanie manifestu zamiast według tagu, który gwarantuje określoną wersję obrazu.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Można sprawdzić, czy wiele manifestów są skojarzone `az acr repository show-manifests` z tym obrazem, uruchamiając polecenie:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Poniższy przykład importuje obraz `tensorflow` publiczny z repozytorium w centrum platformy Docker Hub:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importowanie z rejestru kontenerów firmy Microsoft

Na przykład zaimportuj najnowszy `windows` obraz rdzenia systemu Windows Server z repozytorium w rejestrze kontenerów firmy Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importowanie z innego rejestru kontenerów platformy Azure

Obraz można zaimportować z innego rejestru kontenerów platformy Azure przy użyciu zintegrowanych uprawnień usługi Azure Active Directory.

* Twoja tożsamość musi mieć uprawnienia usługi Azure Active Directory do odczytu z rejestru źródłowego (roli czytnika) i zapisu w rejestrze docelowym (rola współautora).

* Rejestr może znajdować się w tej samej lub innej subskrypcji platformy Azure w tej samej dzierżawie usługi Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importowanie z rejestru w tej samej subskrypcji

Na przykład zaimportować `aci-helloworld:latest` obraz z rejestru źródłowego *mysourceregistry* do *myregistry* w tej samej subskrypcji platformy Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Poniższy przykład importuje obraz przez podsumowanie manifestu (skrót SHA-256, reprezentowany jako `sha256:...`) zamiast według znacznika:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importowanie z rejestru w innej subskrypcji

W poniższym przykładzie *mysourceregistry* jest w innej subskrypcji niż *myregistry* w tej samej dzierżawy usługi Active Directory. Podaj identyfikator zasobu rejestru `--registry` źródłowego z parametrem. Należy zauważyć, że `--source` parametr określa tylko repozytorium źródłowe i tag, a nie nazwę serwera logowania rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importowanie z rejestru przy użyciu poświadczeń głównej usługi

Aby zaimportować z rejestru, do którego nie można uzyskać dostępu przy użyciu uprawnień usługi Active Directory, można użyć poświadczeń jednostki usługi (jeśli są dostępne). Podaj appID i hasło [jednostki usługi](container-registry-auth-service-principal.md) Active Directory, która ma dostęp ACRPull do rejestru źródłowego. Korzystanie z jednostki usługi jest przydatne w przypadku systemów kompilacji i innych systemów nienadzorowanych, które muszą importować obrazy do rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importowanie z rejestru kontenerów prywatnych innych niż azure

Zaimportuj obraz z rejestru prywatnego, określając poświadczenia, które umożliwiają ściąganie dostępu do rejestru. Na przykład ściągaj obraz z prywatnego rejestru platformy Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o importowaniu obrazów kontenerów do rejestru kontenerów platformy Azure z rejestru publicznego lub innego rejestru prywatnego. Aby uzyskać dodatkowe opcje importowania obrazów, zobacz odwołanie do polecenia [importu az acr.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
