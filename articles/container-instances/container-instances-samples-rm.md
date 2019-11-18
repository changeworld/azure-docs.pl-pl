---
title: Przykłady szablonów Azure Resource Manager — Azure Container Instances
description: Znajdź przykłady szablonów Azure Resource Manager do wdrożenia Azure Container Instances w różnych konfiguracjach
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 7dead5cc83717a011120ef6c17b9268bd78ed171
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150156"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Szablony Azure Resource Manager dla Azure Container Instances

Następujące przykładowe szablony wdrażają wystąpienia kontenerów w różnych konfiguracjach.

Aby zapoznać się z opcjami wdrażania, zobacz sekcję [wdrażanie](#deployment) . Jeśli chcesz utworzyć własne szablony, Azure Container Instances szablonu [Menedżer zasobów szczegóły odwołania szablonu][ref] i dostępne właściwości.

## <a name="sample-templates"></a>Przykładowe szablony

| | |
|-|-|
| **Aplikacje** ||
| [WordPress][app-wp] | Tworzy witrynę sieci Web WordPress i jej bazę danych MySQL w grupie kontenerów. Zawartość witryny WordPress i baza danych MySQL są utrwalane w udziale Azure Files. Program tworzy również bramę aplikacji, aby udostępnić publiczny dostęp do sieci WordPress. |
| [MS NAV z SQL Server i IIS][app-nav] | Wdraża pojedynczy kontener systemu Windows z w pełni wyróżnionym własnym środowiskiem programu Dynamics NAV/Dynamics 365 Business Central. |
| **Objętości** ||
| [emptyDir][vol-emptydir] | Wdraża dwa kontenery systemu Linux, które współużytkują wolumin emptyDir. |
| [gitRepo][vol-gitrepo] | Wdraża kontener systemu Linux, który klonuje repozytorium GitHub i instaluje go jako wolumin. |
| [wpisu][vol-secret] | Wdraża kontener systemu Linux z certyfikatem PFX zainstalowanym jako wolumin tajny. |
| **Sieć** ||
| [Kontener narażony na ruch UDP][net-udp] | Wdraża kontener systemu Windows lub Linux, który uwidacznia port UDP. |
| [Kontener systemu Linux z publicznym adresem IP][net-publicip] | Wdraża pojedynczy kontener systemu Linux dostępny za pośrednictwem publicznego adresu IP. |
| [Wdrażanie grupy kontenerów za pomocą sieci wirtualnej (wersja zapoznawcza)][net-vnet] | Wdraża nową sieć wirtualną, podsieć, profil sieci i grupę kontenerów. |
| **Zasoby platformy Azure** ||
| [Utwórz udział plików i konta usługi Azure Storage][az-files] | Używa interfejsu wiersza polecenia platformy Azure w wystąpieniu kontenera, aby utworzyć konto magazynu i udział Azure Files.

## <a name="deployment"></a>Wdrożenie

Istnieje kilka opcji wdrażania zasobów przy użyciu szablonów Menedżer zasobów:

[Interfejs wiersza polecenia platformy Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[Interfejs API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
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
