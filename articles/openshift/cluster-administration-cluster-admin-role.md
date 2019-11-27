---
title: Rola administratora klastrów Red Hat OpenShift na platformie Azure | Microsoft Docs
description: Przypisanie i użycie roli administratora klastra Red Hat OpenShift platformy Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539270"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rola Administrator klienta Red Hat OpenShift na platformie Azure

Jesteś administratorem klastra usługi Azure Red Hat OpenShift. Twoje konto ma zwiększone uprawnienia i dostęp do wszystkich projektów utworzonych przez użytkownika.

Jeśli dla konta jest powiązana rola autoryzacji klient-administrator-klaster, może ona automatycznie zarządzać projektem.

> [!Note] 
> Rola klastra klient-administrator-klaster nie jest taka sama jak rola klastra-administratora klastra.


Na przykład można wykonać akcje skojarzone z zestawem czasowników (`create`) do działania na zestawie nazw zasobów (`templates`). Aby wyświetlić szczegóły tych ról i ich zestawów zleceń i zasobów, uruchom następujące polecenie:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Nazwy zleceń nie zawsze są mapowane bezpośrednio do poleceń `oc`. Są one bardziej ogólne dla typów operacji interfejsu wiersza polecenia, które można wykonywać. 

Na przykład, jeśli zlecenie `list` oznacza, że można wyświetlić listę wszystkich obiektów o nazwie zasobu (`oc get`). Zlecenie `get` oznacza, że w przypadku znajomości nazwy (`oc describe`) można wyświetlić szczegóły konkretnego obiektu.

## <a name="configure-the-customer-administrator-role"></a>Konfigurowanie roli administratora klienta

Rolę klastra klient-administrator-klaster można skonfigurować tylko podczas tworzenia klastra, dostarczając flagę `--customer-admin-group-id`. Aby dowiedzieć się, jak skonfigurować Azure Active Directory i grupę administratorów, zobacz [Azure Active Directory Integration for Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Następne kroki

Skonfiguruj rolę klastra klient-administrator-klaster:
> [!div class="nextstepaction"]
> [Azure Active Directory integrację z usługą Azure Red Hat OpenShift](howto-aad-app-configuration.md)
