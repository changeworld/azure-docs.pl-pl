---
title: Próbki szablon Menedżera zasobów Azure — wystąpień kontenera platformy Azure
description: Przykładów dla platformy Azure Resource Manager szablonu dla wystąpień kontenera platformy Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Szablony usługi Azure Resource Manager dla wystąpień kontenera platformy Azure

Następujące przykładowe szablony wdrażanie wystąpień kontenera w różnych konfiguracjach.

Opcje wdrażania, zobacz [wdrożenia](#deployment) sekcji. Jeśli chcesz tworzyć własne szablony wystąpień kontenera Azure [odwołania do szablonu usługi Resource Manager] [ ref] szczegóły formacie szablonu i dostępnych właściwości.

## <a name="sample-templates"></a>Przykładowe szablony

| | |
|-|-|
| **Aplikacje** ||
| [WordPress][app-wp] | Tworzy witrynę sieci Web WordPress i jej baza danych MySQL w wystąpieniu kontenera. Zawartość witryny WordPress i baza danych MySQL są zachowywane do usługi pliki Azure udziału. |
| [NAV MS SQL Server i usług IIS][app-nav] | Wdraża jeden kontener systemu Windows z oferujący wszystkie potrzebne funkcje niezależne Dynamics NAV / środowiska Dynamics 365 Business centralnej. |
| **Woluminy** ||
| [emptyDir][vol-emptydir] | Wdraża dwa kontenery Linux mających emptyDir woluminu. |
| [GitRepo][vol-gitrepo] | Wdraża kontenera systemu Linux, w którym klonów repozytorium GitHub i instaluje ją jako wolumin. |
| [klucz tajny][vol-secret] | Wdraża kontenera Linux z certyfikatu PFX, zainstalować jako woluminu tajny. |
| **Sieć** ||
| [Kontener dostępne za pośrednictwem protokołu UDP][net-udp] | Wdraża kontener systemu Windows lub Linux, który przedstawia portu UDP. |
| [Kontener Linux z publicznego adresu IP][net-publicip] | Wdraża jeden kontener Linux dostępny za pośrednictwem publicznego adresu IP. |
| **Zasoby platformy Azure** ||
| [Tworzenie konta magazynu Azure i udostępnianie plików][az-files] | Używa interfejsu wiersza polecenia Azure w wystąpieniu kontener można utworzyć konta magazynu i udział plików Azure.

## <a name="deployment"></a>Wdrożenie

Istnieje kilka opcji wdrażania zasobów z szablonami usługi Resource Manager:

[Interfejs wiersza polecenia platformy Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure portal][deploy-portal]

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