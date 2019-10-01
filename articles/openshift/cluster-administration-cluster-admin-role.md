---
title: Rola administratora klastrów Red Hat OpenShift na platformie Azure | Microsoft Docs
description: Przypisanie i użycie roli administratora usługi Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709951"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rola Administrator klienta Red Hat OpenShift na platformie Azure

Jako Administrator klastrów usługi (Azure Red Hat OpenShift) klastra OpenShift, Twoje konto ma zwiększone uprawnienia i dostęp do wszystkich projektów utworzonych przez użytkownika.

Gdy konto ma przypowiązaną rolę autoryzacji OSA-admin-administrator, może automatycznie zarządzać projektem.

> [!Note] 
> OSA-clusterrole klienta nie jest taki sam jak klaster-administrator clusterrole


Na przykład można wykonać akcje skojarzone z zestawem czasowników (`create`) do działania na zestawie nazw zasobów (`templates`). Aby wyświetlić szczegóły tych ról i ich zestawów zleceń i zasobów, uruchom następujące polecenie:

`$ oc describe clusterrole/osa-customer-admin`

Nazwy zleceń nie muszą być bezpośrednio mapowane do poleceń OC, ale raczej są bardziej ogólne dla typów operacji interfejsu wiersza polecenia, które można wykonywać. Na przykład, jeśli zlecenie `list` oznacza, że można wyświetlić listę wszystkich obiektów o danej nazwie zasobu (`oc get`), a czasownik `get` oznacza, że można wyświetlić szczegóły konkretnego obiektu, jeśli znasz jego nazwę (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Jak skonfigurować rolę administratora klienta

Rolę administratora klienta można skonfigurować tylko podczas tworzenia klastra, dostarczając flagę `--customer-admin-group-id`. Jak skonfigurować Azure Active Directory i grupę administratorów postępuj zgodnie z przewodnikiem: [Azure Active Directory integrację z usługą Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Następne kroki

Jak skonfigurować rolę OSA-Customer-administrator:
> [!div class="nextstepaction"]
> [Azure Active Directory integrację z usługą Azure Red Hat OpenShift](howto-aad-app-configuration.md)
