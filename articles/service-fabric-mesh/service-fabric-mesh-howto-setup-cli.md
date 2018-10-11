---
title: Konfigurowanie interfejsu wiersza polecenia usługi Azure Service Fabric mesh | Microsoft Docs
description: Dowiedz się, jak skonfigurować interfejs wiersza polecenia usługi Azure Service Fabric mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5e8eaca7f759842a8097184dafc1f3ea183b898c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993191"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric mesh
Interfejs wiersza polecenia usługi Service Fabric mesh jest wymagany do wdrażania zasobów i zarządzania nimi w usłudze Service Fabric mesh. 

Na potrzeby wersji zapoznawczej interfejs wiersza polecenia usługi Azure Service Fabric mesh jest napisany jako rozszerzenie interfejsu wiersza polecenia platformy Azure. Zainstalować go można w usłudze Azure Cloud Shell lub w lokalnej instalacji wiersza polecenia platformy Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Instalowanie lokalne interfejsu wiersza polecenia usługi Service Fabric Mesh
Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0.43 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować najnowszą wersję interfejsu wiersza polecenia lub uaktualnić do niej, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric mesh za pomocą następującego polecenia. 

```azurecli-interactive
az extension add --name mesh
```

Aby zaktualizować istniejący moduł interfejsu wiersza polecenia usługi Azure Service Fabric Mesh, uruchom następujące polecenie.

```azurecli-interactive
az extension update --name mesh
```

Skonfigurować możesz również [środowisko deweloperskie w systemie Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli
