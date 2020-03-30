---
title: Wdrażanie platformy kontenerowej OpenShift 4.x na platformie Azure
description: Wdrażanie platformy kontenerowej OpenShift 4.x na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035433"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Wdrażanie platformy kontenerowej OpenShift 4.x na platformie Azure

Wdrażanie platformy kontenerowej OpenShift (OCP) 4.2 jest teraz obsługiwane na platformie Azure za pośrednictwem modelu infrastruktury aprowizacji instalatora (IPI).  Strona docelowa do wypróbowania OpenShift 4 jest [try.openshift.com](https://try.openshift.com/). Aby zainstalować OCP 4.2 na platformie Azure, odwiedź stronę [Menedżera klastrów Red Hat OpenShift.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  Poświadczenia Red Hat są wymagane, aby uzyskać dostęp do tej witryny.


## <a name="notes"></a>Uwagi 

 - Podmiot zabezpieczeń usługi Azure Active Directory (AAD) (SP) jest wymagany do zainstalowania i uruchomienia protokołu OCP 4.x na platformie Azure
     - Dodatek SP musi mieć uprawnienie interfejsu API **aplikacji.ReadWrite.OwnedBy** dla usługi Azure Active Directory Graphy
     - Administrator dzierżawy usługi AAD musi udzielić zgody administratora, aby to uprawnienie interfejsu API stało się skuteczne
     - Sp musi mieć przyznane **współtwórca** i **administratora dostępu** użytkownika role do subskrypcji
 - Model instalacji dla OCP 4.x różni się od 3.x i nie ma dostępnych szablonów usługi Azure Resource Manager do wdrażania OCP 4.x na platformie Azure
 - Jeśli podczas instalacji wystąpią problemy, skontaktuj się z odpowiednią firmą (Microsoft lub Red Hat)

| Opis problemu | Punkt kontaktowy |
|-------------------|---------------|
| Specyficzne problemy związane z platformą Azure (usługi AAD, SP, subskrypcja platformy Azure itp.)                              | Microsoft |
| Problemy specyficzne dla OpenShift (błędy instalacji / błędy, subskrypcja Red Hat itp.) |  Red Hat  |




## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy kontenerowej OpenShift](https://docs.openshift.com)
