---
title: Przykłady szablonów usługi Azure Resource Manager
description: Znajdź przykłady szablonów usługi Azure Resource Manager w celu wdrożenia wystąpień kontenerów platformy Azure w różnych konfiguracjach
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981650"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Szablony usługi Azure Resource Manager dla wystąpień kontenerów platformy Azure

Poniższe przykładowe szablony wdrażają wystąpienia kontenerów w różnych konfiguracjach.

Aby uzyskać opcje wdrażania, zobacz sekcję [Wdrażanie.](#deployment) Jeśli chcesz utworzyć własne szablony, usługa Azure Container Instances [Resource Manager odwołuje się][ref] do szczegółów szablonu i dostępnych właściwości.

## <a name="sample-templates"></a>Przykładowe szablony

| | |
|-|-|
| **Aplikacje** ||
| [WordPress][app-wp] | Tworzy witrynę WordPress i jej bazę danych MySQL w grupie kontenerów. Zawartość witryny WordPress i bazy danych MySQL są utrwalone do udziału usługi Azure Files. Tworzy również bramę aplikacji, aby udostępnić publiczny dostęp do sieci WordPress. |
| [MS NAV z programem SQL Server i usługami IIS][app-nav] | Wdraża jeden kontener systemu Windows z w pełni funkcjonalnym, samodzielnym środowiskiem Dynamics NAV / Dynamics 365 Business Central. |
| **Woluminów** ||
| [emptyDir][vol-emptydir] | Wdraża dwa kontenery systemu Linux, które współużytkuje wolumin emptyDir. |
| [gitRepo][vol-gitrepo] | Wdraża kontener systemu Linux, który klonuje repozytorium GitHub i montuje go jako wolumin. |
| [Tajne][vol-secret] | Wdraża kontener systemu Linux z certyfikatem PFX zainstalowanym jako wolumin tajny. |
| **Obsługa sieci** ||
| [Pojemnik na odsłonięty UDP][net-udp] | Wdraża kontener systemu Windows lub Linux, który udostępnia port UDP. |
| [Kontener Linuksa z publicznym adresem IP][net-publicip] | Wdraża jeden kontener systemu Linux dostępny za pośrednictwem publicznego adresu IP. |
| [Wdrażanie grupy kontenerów z siecią wirtualną (wersja zapoznawcza)][net-vnet] | Wdraża nową sieć wirtualną, podsieć, profil sieciowy i grupę kontenerów. |
| **Zasoby platformy Azure** ||
| [Tworzenie konta usługi Azure Storage i udziału w plikach][az-files] | Używa interfejsu wiersza polecenia platformy Azure w wystąpieniu kontenera do utworzenia konta magazynu i udziału usługi Azure Files.

## <a name="deployment"></a>wdrażania

Istnieje kilka opcji wdrażania zasobów za pomocą szablonów Menedżera zasobów:

[Interfejs wiersza polecenia platformy Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portal Azure][deploy-portal]

[INTERFEJS API ODPOCZYNKU][deploy-rest]

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
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
