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
ms.openlocfilehash: 567f2afdea44f439779212c61fb3a129f4f979be
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281576"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric mesh
Interfejs wiersza polecenia usługi Service Fabric mesh jest wymagany do wdrażania zasobów i zarządzania nimi w usłudze Service Fabric mesh. 

Na potrzeby wersji zapoznawczej interfejs wiersza polecenia usługi Azure Service Fabric mesh jest napisany jako rozszerzenie interfejsu wiersza polecenia platformy Azure. Zainstalować go można w usłudze Azure Cloud Shell lub w lokalnej instalacji wiersza polecenia platformy Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0.35 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować najnowszą wersję interfejsu wiersza polecenia lub uaktualnić do niej, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0][azure-cli-install].

Usuń wszystkie poprzednie instalacje modułu interfejsu wiersza polecenia usługi Azure Service Fabric Mesh.

```azurecli-interactive
az extension remove --name mesh
```

Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric mesh za pomocą następującego polecenia. 

```azurecli-interactive
az extension add --source https://meshcli.blob.core.windows.net/cli/mesh-0.9.1-py2.py3-none-any.whl
```

Skonfigurować możesz również [środowisko deweloperskie w systemie Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli