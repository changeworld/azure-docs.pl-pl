---
title: Pojęcia — dostęp i tożsamość w usłudze Azure Kubernetes usługi (AKS)
description: Więcej informacji na temat dostępu i tożsamości w usłudze Azure Kubernetes Service (AKS), w tym integracji usługi Azure Active Directory, kontroli dostępu opartej na rolach Kubernetes (RBAC) i ról i powiązania.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 02/28/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: 3432ba671431c25b7cd9ee58decc638861e884c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467061"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości usługi Azure Kubernetes Service (AKS)

Istnieją różne sposoby uwierzytelniania za pomocą i zabezpieczanie klastrów Kubernetes. Przy użyciu kontroli dostępu opartej na rolach (RBAC), można przyznać użytkownikom lub grupom dostęp do zasobów, które są im potrzebne. Za pomocą usługi Azure Kubernetes Service (AKS), struktury zabezpieczeń i uprawnień można dodatkowo zwiększyć za pomocą usługi Azure Active Directory. Te metody pomóc w ochronie obciążeń aplikacji i danych klientów.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają uwierzytelniania i przypisać uprawnienia w usłudze AKS:

- [Konta usługi Kubernetes](#kubernetes-service-accounts)
- [Integracja z usługą Azure Active Directory](#azure-active-directory-integration)
- [Kontroli dostępu opartej na rolach (RBAC)](#role-based-access-controls-rbac)
- [Role i ClusterRoles](#roles-and-clusterroles)
- [RoleBindings i ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Konta usługi Kubernetes

Jeden z typów głównego użytkownika w usłudze Kubernetes jest *konto usługi*. Konto usługi istnieje w i jest zarządzana przez interfejs API rozwiązania Kubernetes. Poświadczenia dla konta usług są przechowywane jako klucze tajne usługi Kubernetes, co umożliwia ich do użycia przez autoryzowany zasobników do komunikacji z serwerem interfejsu API. Większość żądań interfejsu API podawała token uwierzytelniania dla konta usługi lub kontem zwykłego użytkownika.

Konta zwykłego użytkownika umożliwiają bardziej tradycyjny dostęp dla administratorów i deweloperów, nie tylko do usług i procesów. Kubernetes, sama nie zapewnia rozwiązanie zarządzania tożsamościami, w którym są przechowywane kont zwykłych użytkowników i haseł. Zamiast tego rozwiązania tożsamości zewnętrznych mogą być zintegrowane rozwiązania Kubernetes. W przypadku klastrów usługi AKS to rozwiązanie zintegrowane tożsamości jest Azure Active Directory.

Aby uzyskać więcej informacji na temat opcji tożsamości w usłudze Kubernetes, zobacz [uwierzytelniania Kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Dzięki integracji z usługą programu Azure Active Directory (AD), może zostać poprawione zabezpieczeń klastra usługi AKS. Oparta na wieloletnim funkcji zarządzania tożsamościami w przedsiębiorstwie, usługi Azure AD to wielodostępna, oparta na chmurze katalogu, a usługa zarządzania tożsamościami, która łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości. Za pomocą usługi Azure AD możesz zintegrować tożsamości lokalnych klastrów usługi AKS zapewnia jedno źródło zarządzania kontami i zabezpieczeń.

![Integracja usługi Azure Active Directory przy użyciu klastrów usługi AKS](media/concepts-identity/aad-integration.png)

Przy użyciu klastrów AKS zintegrowanej z usługą AD systemu Azure można przyznać użytkownikom lub grupom dostęp do zasobów platformy Kubernetes w przestrzeni nazw lub w klastrze. Aby uzyskać `kubectl` kontekst konfiguracji, użytkownik może uruchomić [az aks get-credentials] [ az-aks-get-credentials] polecenia. Gdy użytkownik następnie wchodzi w interakcję z klastrem usługi AKS przy użyciu `kubectl`, użytkownicy są monitowani o Zaloguj się przy użyciu poświadczeń usługi Azure AD. To podejście zapewnia jedno źródło dla zarządzania kontami użytkowników i hasła poświadczeń. Użytkownik może tylko dostępu do zasobów, zgodnie z definicją administratora klastra.

Uwierzytelnianie usługi Azure AD w klastrach usługi AKS korzysta z protokołu OpenID Connect, warstwy tożsamości korzystających z protokołu OAuth 2.0. OAuth 2.0 definiuje mechanizmy do uzyskania i używania tokenów dostępu, uzyskiwać dostęp do chronionych zasobów i OpenID Connect uwierzytelniania są implementowane jako rozszerzenie proces autoryzacji OAuth 2.0. Aby uzyskać więcej informacji na temat protokołu OpenID Connect, zobacz [Open ID Connect dokumentacji][openid-connect]. Aby sprawdzić, tokeny uwierzytelniania uzyskane z usługi Azure AD za pomocą protokołu OpenID Connect, AKS klastry używają uwierzytelniania tokenu elementu Webhook platformy Kubernetes. Aby uzyskać więcej informacji, zobacz [dokumentacji uwierzytelnianie przy użyciu elementu Webhook tokenów][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Kontroli dostępu opartej na rolach (RBAC)

Umożliwia filtrowanie szczegółowych działań, które użytkownicy mogą wykonywać Kubernetes używa kontroli dostępu opartej na rolach (RBAC). Ten mechanizm kontroli można przypisać użytkowników lub grup użytkowników i uprawnień do wykonywania czynności, takich jak tworzenie lub modyfikowanie zasobów lub wyświetlanie dzienników z uruchomionych obciążeń aplikacji. Te uprawnienia mogą być ograniczone do pojedynczej przestrzeni nazw lub udzielane przez cały klaster AKS. Dzięki usłudze Kubernetes RBAC, można tworzyć *role* do definiowania uprawnień, a następnie przypisz te role do użytkowników z *powiązania roli*.

Aby uzyskać więcej informacji, zobacz [autoryzacji RBAC przy użyciu][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Kontroli dostępu opartej na rolach na platformie Azure (RBAC)
Jeden dodatkowy mechanizm do kontrolowania dostępu do zasobów jest kontroli dostępu opartej na rolach na platformie Azure (RBAC). Kubernetes RBAC jest przeznaczona do pracy na zasoby w ramach klastra usługi AKS i Azure RBAC jest przeznaczona do pracy na zasoby w ramach subskrypcji platformy Azure. Dzięki usłudze Azure RBAC można tworzyć *definicji roli* oraz przedstawiono uprawnienia, które mają być stosowane. Użytkownik lub grupa jest przypisywany tej definicji roli dla określonego *zakres*, co może być pojedynczy zasób, zasób grupy, lub różnych subskrypcji.

Aby uzyskać więcej informacji, zobacz [co to jest Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Role i ClusterRoles

Przed przypisaniem uprawnień dla użytkowników przy użyciu kontroli RBAC usługi Kubernetes, najpierw zdefiniować te uprawnienia, jak *roli*. Role usługi Kubernetes *udzielić* uprawnienia. Nie obowiązuje koncepcja *Odmów* uprawnień.

Role są używane do udzielania uprawnień w przestrzeni nazw. Jeśli musisz przyznać uprawnienia dla całego klastra lub klastra zasobów spoza danej przestrzeni nazw, możesz zamiast tego użyć *ClusterRoles*.

ClusterRole działa w taki sam sposób, aby udzielić uprawnień do zasobów, ale mogą być stosowane do zasobów dla całego klastra, a nie określonych przestrzeni nazw.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowaniu role o nadanie uprawnień do zasobów, Przypisz te uprawnienia RBAC usługi Kubernetes za pomocą *RoleBinding*. W przypadku klastra usługi AKS integruje się z usługą Azure Active Directory, powiązania są, jak te użytkowników usługi Azure AD są przyznawane uprawnienia do wykonania akcji w ramach klastra.

Powiązania roli są używane do przypisywania ról dla danego obszaru nazw. Takie podejście umożliwia logicznego rozdzielania jeden klaster AKS użytkownikom tylko mogli uzyskiwać dostęp do zasobów aplikacji w ich przypisanej przestrzeni nazw. Jeśli musisz powiązać ról dla całego klastra lub klastra zasobów spoza danej przestrzeni nazw, możesz zamiast tego użyć *ClusterRoleBindings*.

ClusterRoleBinding działa w taki sam sposób, aby powiązać ról użytkowników, ale mogą być stosowane do zasobów dla całego klastra, a nie określonych przestrzeni nazw. To podejście pozwala udzielać administratorom lub pomocy technicznej inżynierów dostęp do wszystkich zasobów w klastrze AKS.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z usługą Azure AD i kontroli RBAC usługi Kubernetes, zobacz [integracji usługi Azure Active Directory za pomocą usługi AKS][aks-aad].

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][operator-best-practices-identity].

Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes / zabezpieczeń usługi AKS][aks-concepts-security]
- [Kubernetes / sieci wirtualne w usłudze AKS][aks-concepts-network]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md