---
title: Przykłady szablonów usługi Resource Manager platformy Azure — usłudze Azure Container Instances
description: Przykłady szablonów usługi Azure Resource Manager dla usługi Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: danlep
ms.openlocfilehash: e825e0bdd08db0e9c1b51c09859aba2e7c716f91
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856472"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Szablony usługi Azure Resource Manager dla usługi Azure Container Instances

Następujące przykładowe szablony wdrażanie wystąpień kontenerów w różnych konfiguracjach.

Dla opcji wdrażania, zobacz [wdrożenia](#deployment) sekcji. Jeśli chcesz tworzyć własne szablony usługi Azure Container Instances [odwołanie do szablonu usługi Resource Manager] [ ref] szczegółowo określa najważniejsze formacie szablonu i dostępnych właściwości.

## <a name="sample-templates"></a>Przykładowe szablony

| | |
|-|-|
| **Aplikacje** ||
| [WordPress][app-wp] | Tworzy witrynę sieci Web WordPress i jego bazę danych MySQL w wystąpieniu kontenera. Zawartość witryny WordPress i baza danych MySQL zostaną utrwalone w usłudze Azure Files udostępniania. |
| [MS NAV przy użyciu programu SQL Server i usług IIS][app-nav] | Wdraża jeden kontener Windows za pomocą w pełni wyposażone niezależna Dynamics NAV / środowiska Dynamics 365 Business Central. |
| **Woluminy** ||
| [emptyDir][vol-emptydir] | Wdraża dwóch kontenerów systemu Linux, które współużytkują woluminu emptyDir. |
| [GitRepo][vol-gitrepo] | Wdraża klonuje repozytorium GitHub, która instaluje ją jako wolumin kontenera systemu Linux. |
| [Klucz tajny][vol-secret] | Służy do wdrażania kontenerów systemu Linux przy użyciu certyfikatu PFX, zainstalowany jako wolumin tajny. |
| **Sieć** ||
| [Kontener dostępne za pośrednictwem protokołu UDP][net-udp] | Służy do wdrażania kontenerów Windows lub Linux, która uwidacznia UDP port. |
| [Kontener systemu Linux z publicznym adresem IP][net-publicip] | Wdraża jednego kontenera systemu Linux dostępnych za pośrednictwem publicznego adresu IP. |
| **Zasoby platformy Azure** ||
| [Tworzenie konta usługi Azure Storage i udostępnianie plików][az-files] | Używa wiersza polecenia platformy Azure w wystąpieniu kontenera, aby utworzyć konto magazynu i udział plików platformy Azure.

## <a name="deployment"></a>Wdrożenie

Masz kilka opcji wdrożenia zasobów przy użyciu szablonów usługi Resource Manager:

[Wiersza polecenia platformy Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Witryna Azure portal][deploy-portal]

[INTERFEJS API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups