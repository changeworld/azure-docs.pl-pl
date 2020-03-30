---
title: Pojęcia — dostęp i tożsamość w usługach Azure Kubernetes (AKS)
description: Dowiedz się więcej o dostępie i tożsamości w usłudze Azure Kubernetes Service (AKS), w tym integracji usługi Azure Active Directory, kontroli dostępu opartej na rolach Kubernetes (RBAC) oraz ról i powiązań.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259606"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)

Istnieją różne sposoby uwierzytelniania za pomocą klastrów kubernetes i zabezpieczania ich. Za pomocą kontroli dostępu opartego na rolach (RBAC) można przyznać użytkownikom lub grupom dostęp tylko do potrzebnych zasobów. Za pomocą usługi Azure Kubernetes Service (AKS) można dodatkowo ulepszyć strukturę zabezpieczeń i uprawnień przy użyciu usługi Azure Active Directory. Te podejścia ułatwiają zabezpieczenie obciążeń aplikacji i danych klientów.

W tym artykule przedstawiono podstawowe pojęcia, które pomagają uwierzytelniać i przypisywać uprawnienia w uzywki AKS:

- [Konta usługi Kubernetes](#kubernetes-service-accounts)
- [Integracja z usługą Azure Active Directory](#azure-active-directory-integration)
- [Kontrola dostępu oparta na rolach (RBAC)](#role-based-access-controls-rbac)
- [Role i role klastra](#roles-and-clusterroles)
- [RoleBindings i ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Konta usługi Kubernetes

Jednym z podstawowych typów użytkowników w usłudze Kubernetes jest *konto usługi*. Konto usługi istnieje w interfejsie API usługi i jest nim zarządzane przez interfejs API usługi. Poświadczenia dla kont usługi są przechowywane jako wpisy tajne kubernetes, co pozwala na ich użycie przez autoryzowanych zasobników do komunikowania się z serwerem interfejsu API. Większość żądań interfejsu API zapewnia token uwierzytelniania dla konta usługi lub zwykłego konta użytkownika.

Zwykłe konta użytkowników umożliwiają bardziej tradycyjny dostęp dla administratorów lub deweloperów, a nie tylko usług i procesów. Sam kubernetes nie zapewnia rozwiązania do zarządzania tożsamościami, w którym przechowywane są konta zwykłych użytkowników i hasła. Zamiast tego rozwiązania tożsamości zewnętrznej można zintegrować z programem Kubernetes. W przypadku klastrów AKS to zintegrowane rozwiązanie tożsamości to usługa Azure Active Directory.

Aby uzyskać więcej informacji na temat opcji tożsamości w aplikacji Kubernetes, zobacz [Uwierzytelnianie kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Bezpieczeństwo klastrów AKS można zwiększyć za pomocą integracji usługi Azure Active Directory (AD). Oparta na dziesięcioleciach zarządzania tożsamościami przedsiębiorstwa usługa Azure AD jest usługą wielodostępniczą, opartą na chmurze i usługą zarządzania tożsamościami, która łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości. Za pomocą usługi Azure AD można integrować tożsamości lokalne z klastrami usługi AKS, aby zapewnić jedno źródło zarządzania kontami i zabezpieczeń.

![Integracja usługi Azure Active Directory z klastrami AKS](media/concepts-identity/aad-integration.png)

Za pomocą klastrów AKS zintegrowanych z usługą Azure AD można przyznać użytkownikom lub grupom dostęp do zasobów usługi Kubernetes w obszarze nazw lub w całym klastrze. Aby uzyskać `kubectl` kontekst konfiguracji, użytkownik może uruchomić polecenie [az aks get-credentials.][az-aks-get-credentials] Gdy użytkownik wchodzi w interakcję z klastrem AKS z `kubectl`programem , jest monitowany o zalogowanie się przy użyciu poświadczeń usługi Azure AD. Takie podejście zapewnia jedno źródło dla zarządzania kontem użytkownika i poświadczeń haseł. Użytkownik może uzyskać dostęp tylko do zasobów zdefiniowanych przez administratora klastra.

Uwierzytelnianie usługi Azure AD w klastrach AKS używa OpenID Connect, warstwy tożsamości utworzonej na podstawie protokołu OAuth 2.0. OAuth 2.0 definiuje mechanizmy uzyskiwania i używania tokenów dostępu w celu uzyskania dostępu do chronionych zasobów, a OpenID Connect implementuje uwierzytelnianie jako rozszerzenie procesu autoryzacji OAuth 2.0. Aby uzyskać więcej informacji na temat OpenID Connect, zobacz [dokumentację Open ID Connect][openid-connect]. Aby zweryfikować tokeny uwierzytelniania uzyskane z usługi Azure AD za pośrednictwem usługi OpenID Connect, klastry AKS używają uwierzytelniania tokenu elementu sieci Web Kubernetes. Aby uzyskać więcej informacji, zobacz [dokumentację uwierzytelniania tokenu elementu webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, program Kubernetes używa kontroli dostępu opartych na rolach (RBAC). Ten mechanizm sterowania umożliwia przypisywanie użytkownikom lub grupom użytkowników uprawnień do wykonywania takich czynności, jak tworzenie lub modyfikowanie zasobów lub wyświetlanie dzienników z uruchomionych obciążeń aplikacji. Te uprawnienia mogą być ograniczone do pojedynczego obszaru nazw lub przyznane w całym klastrze AKS. Za pomocą funkcji Kubernetes RBAC można tworzyć *role* do definiowania uprawnień, a następnie przypisywać te role użytkownikom z *powiązaniami ról*.

Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Formanty dostępu oparte na rolach platformy Azure (RBAC)
Dodatkowy mechanizm kontrolowania dostępu do zasobów jest kontrola dostępu oparta na rolach platformy Azure (RBAC). Usługa Kubernetes RBAC została zaprojektowana do pracy nad zasobami w klastrze AKS, a usługa Azure RBAC została zaprojektowana do pracy nad zasobami w ramach subskrypcji platformy Azure. Za pomocą narzędzia Azure RBAC tworzysz *definicję roli,* która określa uprawnienia, które mają być stosowane. Użytkownik lub grupa jest następnie przypisany tej definicji roli dla określonego *zakresu*, który może być pojedynczy zasób, grupa zasobów lub w całej subskrypcji.

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Role i role klastra

Przed przypisaniem uprawnień użytkownikom z usługą Kubernetes RBAC należy najpierw zdefiniować te uprawnienia jako *rolę*. Role usługi Kubernetes *przyznają* uprawnienia. Nie ma pojęcia *odmowy* zgody.

Role są używane do udzielania uprawnień w obszarze nazw. Jeśli chcesz udzielić uprawnień w całym klastrze lub klastrować zasoby poza danym obszarem nazw, możesz zamiast tego użyć *funkcji ClusterRoles*.

Rolowanie klastra działa w ten sam sposób, aby udzielić uprawnień do zasobów, ale mogą być stosowane do zasobów w całym klastrze, a nie określonego obszaru nazw.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowaniu ról w celu przyznania uprawnień do zasobów należy przypisać te uprawnienia RBAC kubernetes za pomocą *funkcji RoleBinding*. Jeśli klaster AKS integruje się z usługą Azure Active Directory, powiązania są jak tych użytkowników usługi Azure AD mają uprawnienia do wykonywania akcji w klastrze.

Powiązania ról są używane do przypisywania ról dla danej przestrzeni nazw. Takie podejście umożliwia logiczne segregowanie pojedynczego klastra AKS, a użytkownicy mogą uzyskiwać dostęp tylko do zasobów aplikacji w przypisanym obszarze nazw. Jeśli konieczne jest powiązanie ról w całym klastrze lub z zasobami klastra poza danym obszarem nazw, można zamiast tego użyć *funkcji ClusterRoleBindings*.

ClusterRoleBinding działa w ten sam sposób, aby powiązać role z użytkownikami, ale mogą być stosowane do zasobów w całym klastrze, a nie określonego obszaru nazw. Takie podejście umożliwia udzielanie administratorom lub inżynierom pomocy technicznej dostępu do wszystkich zasobów w klastrze AKS.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi Azure AD i usługi Kubernetes RBAC, zobacz [Integrowanie usługi Azure Active Directory z usługą AKS][aks-aad].

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w uzywce AKS][operator-best-practices-identity].

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć kubernetów i usługi AKS, zobacz następujące artykuły:

- [Klastry i obciążenia Kubernetes / AKS][aks-concepts-clusters-workloads]
- [Zabezpieczenia Kubernetes / AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes / AKS][aks-concepts-network]
- [Kubernetes / AKS pamięci masowej][aks-concepts-storage]
- [Skala Kubernetes / AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
