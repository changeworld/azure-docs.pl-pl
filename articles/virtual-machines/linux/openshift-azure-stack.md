---
title: Wdrażanie platformy OpenShift w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Wdrażanie platformy OpenShift w usłudze Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: 91b37753ae80596612eda9d3ccd34858691e35ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771553"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Wdrażanie rozwiązania OpenShift Container Platform lub OKD w usłudze Azure Stack

OpenShift można wdrożyć w usłudze Azure Stack. Istnieją niektóre podstawowe różnice między platformą Azure i usługi Azure Stack, więc wdrażania będzie różnić się nieznacznie i funkcje będą również się nieznacznie różnić.

Obecnie dostawcy chmury Azure nie działa w usłudze Azure Stack. Z tego powodu nie można używać dysku dołączania dla zapewnienia trwałego magazynu w usłudze Azure Stack. Zamiast tego można skonfigurować inne opcje magazynu, np. NFS, iSCSI, GlusterFS, itp. Alternatywnie można włączyć CNS i użyj GlusterFS dla zapewnienia trwałego magazynu. Włączenie CNS trzy dodatkowe węzły będą wdrażane przy użyciu dodatkowego magazynu do użycia GlusterFS.

Wdrażanie OpenShift Container Platform lub OKD w usłudze Azure Stack, można użyć jednej z kilku metod:

- Można ręcznie wdrożyć składniki niezbędne infrastruktury platformy Azure, a następnie wykonaj [dokumentacja platformy OpenShift Container Platform](https://docs.openshift.com/container-platform) lub [dokumentacji OKD](https://docs.okd.io).
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-container-platform/) upraszczające proces wdrożenia klastra OpenShift Container Platform.
- Można także użyć istniejącego [szablonu usługi Resource Manager](https://github.com/Microsoft/openshift-origin) upraszczające proces wdrażania OKD klastra.

Jeśli przy użyciu szablonu usługi Resource Manager, należy wybrać odpowiednie gałęzi (azurestack-release-3.x). Szablony platformy Azure nie będzie działać zgodnie z wersji interfejsu API różnią się między platformą Azure i usługi Azure Stack. Odwołanie do obrazu systemu RHEL jest obecnie zakodowane jako zmienna w pliku azuredeploy.json i będzie musiał zostać zmienione w celu dopasowania obrazu.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Wszystkie opcje wymagana jest subskrypcja firmy Red Hat. Podczas wdrażania wystąpienie Red Hat Enterprise Linux jest zarejestrowany do subskrypcji Red Hat i dołączony do identyfikator puli, który zawiera uprawnień dla rozwiązania OpenShift Container Platform.
Upewnij się, że masz prawidłowe Red Hat subskrypcji Menedżera (RHSM) nazwa użytkownika, hasło i identyfikator puli. Alternatywnie można użyć klucza aktywacji, Org ID i identyfikator puli.  Możesz sprawdzić te informacje, logując się do https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Wymagania wstępne dotyczące usługi Azure Stack

Obrazu systemu RHEL (OpenShift Container Platform) lub obrazu systemu CentOS (OKD) musi zostać dodane do środowiska usługi Azure Stack wdrożyć klaster usługi OpenShift. Skontaktuj się z administratorem usługi Azure Stack, aby dodać te obrazy. Instrukcje można znaleźć tutaj:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Wdrażanie przy użyciu szablonu platformy OpenShift Container Platform lub OKD Resource Manager

Aby wdrożyć przy użyciu szablonu usługi Resource Manager, należy użyć pliku parametrów umożliwiają określanie wartości parametrów wejściowych. Aby dostosować wdrożenia, rozwidlenie repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- Szczegóły klastra OpenShift, zmodyfikować za pomocą pliku hosts (deployOpenShift.sh)
- Odwołanie do obrazu systemu RHEL (zmienna w azuredeploy.json)

Instrukcje dotyczące wdrażania przy użyciu wiersza polecenia platformy Azure, postępuj zgodnie z odpowiednią sekcję w [OpenShift Container Platform](./openshift-container-platform.md) sekcji lub [OKD](./openshift-okd.md) sekcji.

## <a name="next-steps"></a>Kolejne kroki

- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia OpenShift na platformie Azure](./openshift-troubleshooting.md)