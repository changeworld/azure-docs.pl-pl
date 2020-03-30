---
title: Rola administratora klastra Azure Red Hat OpenShift | Dokumenty firmy Microsoft
description: Przypisywanie i używanie roli administratora klastra Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139100"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Rola administratora klienta Usługi Azure Red Hat OpenShift

Jesteś administratorem klastra klastra Azure Red Hat OpenShift. Twoje konto ma zwiększone uprawnienia i dostęp do wszystkich projektów utworzonych przez użytkownika.

Gdy twoje konto ma powiązaną rolę autoryzacji klient-administrator-klaster, może automatycznie zarządzać projektem.

> [!Note] 
> Rola klastra klient-administrator-klaster nie jest taka sama jak rola klastra administratora klastra.

Na przykład można wykonać akcje skojarzone z zestawem zleceń (`create`) w`templates`celu działania na zestawie nazw zasobów ( ). Aby wyświetlić szczegóły tych ról i ich zestawy zleceń i zasobów, uruchom następujące polecenie:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Nazwy czasowników nie muszą być mapowane bezpośrednio na `oc` polecenia. Są one bardziej ogólnie utożsamiane z typami operacji interfejsu wiersza polecenia, które można wykonać. 

Na przykład posiadanie `list` zlecenia oznacza, że można wyświetlić listę wszystkich`oc get`obiektów o nazwie zasobu ( ). Czasownik `get` oznacza, że można wyświetlić szczegóły określonego obiektu,`oc describe`jeśli znasz jego nazwę ( ).

## <a name="configure-the-customer-administrator-role"></a>Konfigurowanie roli administratora klienta

Rolę klastra klient-administrator-klaster można skonfigurować tylko podczas `--customer-admin-group-id`tworzenia klastra, podając flagę . To pole nie jest obecnie konfigurowane w witrynie Azure portal. Aby dowiedzieć się, jak skonfigurować usługę Azure Active Directory i grupę Administratorzy, zobacz [Integracja usługi Azure Active Directory dla usługi Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Potwierdzanie członkostwa w roli administratora klienta

Aby potwierdzić członkostwo w grupie administracyjnej klienta, `oc get nodes` wypróbuj `oc projects`polecenia Interfejsu wiersza polecenia OpenShift lub . `oc get nodes`wyświetli listę węzłów, jeśli masz rolę klient-administrator-klaster, oraz błąd uprawnień, jeśli masz tylko rolę klient-administrator-projekt. `oc projects`pokaże wszystkie projekty w klastrze, w przeciwieństwie do tylko projektów, w których pracujesz.

Aby dalej eksplorować role i uprawnienia w [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) klastrze, można użyć polecenia.

## <a name="next-steps"></a>Następne kroki

Konfigurowanie roli klastra klient-administrator-klaster:
> [!div class="nextstepaction"]
> [Integracja usługi Azure Active Directory dla usługi Azure Red Hat OpenShift](howto-aad-app-configuration.md)
