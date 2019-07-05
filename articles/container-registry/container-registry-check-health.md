---
title: Sprawdzanie kondycji rejestru w usłudze Azure Container Registry
description: Dowiedz się, jak uruchomić szybkie polecenia diagnostycznego w celu zidentyfikowania często występujących problemów w przypadku korzystania z usługi Azure container registry, łącznie z lokalnej konfiguracji platformy Docker i łączność z rejestru
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555100"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Sprawdzanie kondycji usługi Azure container registry

Korzystając z usługi Azure container registry, czasami mogą wystąpić problemy. Na przykład nie można ściągnąć obrazu kontenera ze względu na problem z platformą Docker w środowisku lokalnym. Lub problem z siecią może uniemożliwić nawiązywania połączenia z rejestru. 

Pierwszym krokiem diagnostycznych, należy uruchomić [kondycja sprawdzania az acr][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Uruchom kondycja sprawdzania az acr

Przykładach przedstawiono różne sposoby, aby uruchomić `az acr check-health` polecenia.

> [!NOTE]
> Po uruchomieniu polecenia w usłudze Azure Cloud Shell w środowisku lokalnym nie jest zaznaczone. Jednak można sprawdzić dostępu do rejestru docelowego.

### <a name="check-the-environment-only"></a>Sprawdź środowisko tylko

Aby sprawdzić lokalnej platformy Docker demona, wersję interfejsu wiersza polecenia i narzędzia Helm konfiguracji klienta, uruchom polecenie bez dodatkowych parametrów:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Sprawdź środowisko i rejestru docelowego

Aby Sprawdź dostęp do rejestru także wykonywać testy w środowisku lokalnym, należy przekazać nazwę rejestru docelowego. Na przykład:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Raportowanie błędów

Polecenie rejestruje informacje do wyjścia standardowego. Jeśli zostanie wykryty problem, zawiera kod błędu i opis. Aby uzyskać więcej informacji na temat kodów i możliwe rozwiązania, zobacz [informacje o błędzie](container-registry-health-error-reference.md).

Domyślnie polecenie zatrzymuje zawsze wtedy, gdy znajdzie się błąd. Możesz również uruchomić polecenie tak, aby wszystkie kontrole kondycji, zapewnia dane wyjściowe nawet wtedy, gdy wystąpią błędy. Dodaj `--ignore-errors` parametru, jak pokazano w poniższych przykładach:

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



## <a name="next-steps"></a>Kolejne kroki

Szczegółowe informacje na temat kodów błędów zwróconych przez [kondycja sprawdzania az acr][az-acr-check-health] polecenia, zobacz [informacje o błędzie sprawdzania kondycji](container-registry-health-error-reference.md).

Zobacz [— często zadawane pytania](container-registry-faq.md) często zadawane pytania i inne znane problemy dotyczące usługi Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
