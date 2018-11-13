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
ms.openlocfilehash: c30f4b9de279f8c02b7f6bc7fa7d9765972899b1
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977435"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric mesh
Interfejs wiersza polecenia usługi Service Fabric Mesh jest wymagany do wdrażania zasobów i zarządzania nimi w usłudze Service Fabric Mesh. 

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
## <a name="next-steps"></a>Następne kroki

Skonfigurować możesz również [środowisko deweloperskie w systemie Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Znajdź odpowiedzi na [typowe pytania i problemy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
