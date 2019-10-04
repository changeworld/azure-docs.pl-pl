---
title: Rola administratora klastrów Red Hat OpenShift na platformie Azure | Microsoft Docs
description: Przypisanie i użycie roli administratora klastra Red Hat OpenShift platformy Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936924"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rola Administrator klienta Red Hat OpenShift na platformie Azure

Jesteś administratorem klastra usługi Azure Red Hat OpenShift. Twoje konto ma zwiększone uprawnienia i dostęp do wszystkich projektów utworzonych przez użytkownika.

Gdy konto ma przypowiązaną rolę autoryzacji OSA-klient-administrator, może automatycznie zarządzać projektem.

> [!Note] 
> Rola klastra OSA-Customer-admin nie jest taka sama jak rola klastra klastra-administratora.


Na przykład można wykonać akcje skojarzone z zestawem czasowników (`create`) do działania na zestawie nazw zasobów (`templates`). Aby wyświetlić szczegóły tych ról i ich zestawów zleceń i zasobów, uruchom następujące polecenie:

`$ oc describe clusterrole/osa-customer-admin`

Nazwy zleceń nie zawsze są mapowane bezpośrednio na polecenia `oc`. Są one bardziej ogólne dla typów operacji interfejsu wiersza polecenia, które można wykonywać. 

Na przykład, jeśli zlecenie `list` oznacza, że można wyświetlić listę wszystkich obiektów o nazwie zasobu (`oc get`). Czasownik `get` oznacza, że można wyświetlić szczegóły konkretnego obiektu, jeśli znasz jego nazwę (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Konfigurowanie roli administratora klienta

Rolę administratora klienta można skonfigurować tylko podczas tworzenia klastra, podając flagę `--customer-admin-group-id`. Aby dowiedzieć się, jak skonfigurować Azure Active Directory i grupę administratorów, zobacz [Azure Active Directory Integration for Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Następne kroki

Skonfiguruj rolę OSA-Customer-administrator:
> [!div class="nextstepaction"]
> [Azure Active Directory integrację z usługą Azure Red Hat OpenShift](howto-aad-app-configuration.md)
