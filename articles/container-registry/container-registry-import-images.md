---
title: Importowanie obrazów kontenera do usługi Azure Container Registry
description: Importuj obrazów kontenera do usługi Azure container registry przy użyciu interfejsów API platformy Azure, bez konieczności uruchamiania polecenia Docker.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417747"
---
# <a name="import-container-images-to-a-container-registry"></a>Importowanie obrazów kontenera do rejestru kontenerów

Można łatwo importować obrazów kontenerów (kopiowanie) do usługi Azure container registry, bez używania poleceń platformy Docker. Na przykład importowanie obrazów z rejestru projektowania do produkcji rejestru lub skopiować podstawowe obrazy z publicznego rejestru.

Usługa Azure Container Registry obsługuje szereg typowych scenariuszy, aby skopiować obrazy z istniejącego rejestru:

* Importuj z publicznego rejestru

* Importuj z innej usługi Azure container registry, w tym samym lub różnych subskrypcji platformy Azure

* Importowanie z prywatnego spoza platformy Azure container registry

Importuj obraz do rejestru kontenerów platformy Azure ma następujące korzyści za pośrednictwem za pomocą poleceń interfejsu wiersza polecenia platformy Docker:

* Ponieważ środowiska klienta nie wymaga lokalnej instalacji platformy Docker, należy zaimportować dowolnego obrazu kontenera, niezależnie od obsługiwanego typu systemu operacyjnego.

* Po zaimportowaniu architektury wielu obrazów (takie jak oficjalne obrazy Docker), skopiowania obrazów dla wszystkich architektur i platform określonych na liście manifestu.

Aby zaimportować obrazów kontenerów, w tym artykule wymaga uruchamiania wiersza polecenia platformy Azure w usłudze Azure Cloud Shell lub lokalnie (wersja 2.0.55 lub nowszy zalecane). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!NOTE]
> Jeśli konieczne jest dystrybuowanie obrazów kontenera identyczne w wielu regionach platformy Azure, Azure Container Registry obsługuje również [geografickou replikaci](container-registry-geo-replication.md). Dzięki replikacji geograficznej w rejestrze (jednostki SKU Premium wymagane) może obsługiwać wiele regionów z identyczne nazwy obrazu i tagu z pojedynczym rejestrem.
>

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze rejestr kontenerów platformy Azure, Utwórz rejestr. Aby uzyskać instrukcje, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów przy użyciu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

Aby zaimportować obrazu do usługi Azure container registry, swoją tożsamość musi mieć uprawnienia do zapisu do rejestru docelowego (co najmniej rola "Współautor"). Zobacz [uprawnienia i role usługi Azure Container Registry](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importuj z publicznego rejestru

### <a name="import-from-docker-hub"></a>Importuj z usługi Docker Hub

Na przykład użyć [az acr importu] [ az-acr-import] polecenie, aby zaimportować wielu architektury `hello-world:latest` obrazu z usługi Docker Hub, rejestru o nazwie *myregistry*. Ponieważ `hello-world` jest oficjalnego obrazu z usługi Docker Hub ten obraz jest w domyślnym `library` repozytorium. Obejmują nazwę repozytorium i opcjonalnie tagu w wartości `--source` parametr obrazu. (Można opcjonalnie określić obrazu przez jego manifestu szyfrowanego zamiast według tagów, co gwarantuje konkretnej wersji obrazu.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Możesz sprawdzić, wiele manifesty nie są powiązane z tym obrazem, uruchamiając `az acr repository show-manifests` polecenia:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

Następujący przykład importuje obrazu publicznego z `tensorflow` repozytorium w usłudze Docker Hub:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importuj z rejestru kontenerów firmy Microsoft

Na przykład zaimportować najnowszego obrazu systemu Windows Server Core z `windows` repozytorium w rejestrze kontenerów firmy Microsoft.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importuj z innej usługi Azure container registry

Obraz można importować z innej usługi Azure container registry przy użyciu zintegrowanego uprawnień usługi Azure Active Directory.

* Swoją tożsamość musi mieć uprawnienia usługi Azure Active Directory, można odczytać z rejestru źródłowego (roli Czytelnik) i zapisu do rejestru docelowego (Rola współautora).

* Rejestr może być w tej samej lub innej subskrypcji platformy Azure w tej samej dzierżawie usługi Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importuj z rejestru w tej samej subskrypcji

Na przykład zaimportować `aci-helloworld:latest` obraz z rejestru źródłowego *mysourceregistry* do *myregistry* w tej samej subskrypcji platformy Azure.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

Następujący przykład importuje obraz przez skrót manifestu (Skrót SHA-256, reprezentowane jako `sha256:...`) zamiast przez tag:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importuj z rejestru w innej subskrypcji

W poniższym przykładzie *mysourceregistry* znajduje się w innej subskrypcji z *myregistry* w tej samej dzierżawie usługi Active Directory. Podaj identyfikator zasobu rejestru źródłowego przy użyciu `--registry` parametru. Należy zauważyć, że `--source` parametr określa tylko repozytoriów i obrazów nazwę źródła, nie nazwę serwera logowania rejestru.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importuj z rejestru przy użyciu poświadczeń jednostki usługi

Aby zaimportować z rejestru, których nie można uzyskać dostęp przy użyciu uprawnień usługi Active Directory, można użyć poświadczeń jednostki usługi (jeśli jest dostępny). Podaj identyfikator aplikacji i hasła usługi Active Directory [nazwy głównej usługi](container-registry-auth-service-principal.md) zawierający ACRPull dostępu do rejestru źródła. Za pomocą nazwy głównej usługi jest przydatne w przypadku systemów kompilacji i innymi systemami instalacji nienadzorowanej, które należy zaimportować obrazów do rejestru.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importowanie z prywatnego spoza platformy Azure container registry

Importuj obraz z rejestru prywatnego, określając poświadczeń umożliwiających dostęp ściągnięcia do rejestru. Na przykład można ściągnąć obraz z rejestru prywatnego platformy Docker: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono informacje o importowaniu obrazów kontenera do usługi Azure container registry z publicznego rejestru lub inny rejestr prywatny. Obraz dodatkowe opcje importu, zobacz [az acr importu] [ az-acr-import] poleceń. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
