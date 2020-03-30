---
title: Sprawdzanie kondycji rejestru
description: Dowiedz się, jak uruchomić szybkie polecenie diagnostyczne w celu zidentyfikowania typowych problemów podczas korzystania z rejestru kontenerów platformy Azure, w tym lokalnej konfiguracji platformy Docker i łączności z rejestrem
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456421"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Sprawdzanie kondycji rejestru kontenerów platformy Azure

Podczas korzystania z rejestru kontenerów platformy Azure, czasami mogą wystąpić problemy. Na przykład może nie być w stanie wyciągnąć obraz kontenera z powodu problemu z docker w środowisku lokalnym. Lub problem z siecią może uniemożliwić łączenie się z rejestrem. 

Jako pierwszy krok diagnostyczny uruchom polecenie [az acr check-health,][az-acr-check-health] aby uzyskać informacje o kondycji środowiska i opcjonalnie uzyskać dostęp do rejestru docelowego. To polecenie jest dostępne w usłudze Azure CLI w wersji 2.0.67 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="run-az-acr-check-health"></a>Uruchom az acr check-health

Poniższe przykłady pokazują różne `az acr check-health` sposoby uruchamiania polecenia.

> [!NOTE]
> Jeśli polecenie zostanie uruchomione w usłudze Azure Cloud Shell, środowisko lokalne nie jest sprawdzane. Można jednak sprawdzić dostęp do rejestru docelowego.

### <a name="check-the-environment-only"></a>Sprawdź tylko środowisko

Aby sprawdzić lokalny demon platformy Docker, wersję interfejsu wiersza polecenia i konfigurację klienta helm, uruchom polecenie bez dodatkowych parametrów:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Sprawdzanie środowiska i rejestru docelowego

Aby sprawdzić dostęp do rejestru, a także przeprowadzić kontrole środowiska lokalnego, należy podać nazwę rejestru docelowego. Przykład:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Raportowanie błędów

Polecenie rejestruje informacje do standardowego wyjścia. Jeśli problem zostanie wykryty, zawiera kod błędu i opis. Aby uzyskać więcej informacji na temat kodów i możliwych rozwiązań, zobacz [odwołanie do błędu](container-registry-health-error-reference.md).

Domyślnie polecenie zatrzymuje się za każdym razem, gdy znajdzie błąd. Można również uruchomić polecenie, tak aby zapewnia dane wyjściowe dla wszystkich kontroli kondycji, nawet jeśli zostaną znalezione błędy. Dodaj `--ignore-errors` parametr, jak pokazano w poniższych przykładach:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Przykładowe dane wyjściowe:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat kodów błędów zwracanych przez polecenie [az acr check-health,][az-acr-check-health] zobacz [odwołanie od błędu sprawdzania kondycji](container-registry-health-error-reference.md).

Zobacz [często](container-registry-faq.md) zadawane pytania dotyczące często zadawanych pytań i innych znanych problemów dotyczących rejestru kontenerów platformy Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
