---
title: Najważniejsze wskazówki dotyczące zarządzania tożsamością
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatorów klastra dotyczącymi zarządzania uwierzytelnianiem i autoryzacją klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e3569be769fcf70a65cbfee62a3b80a5abdc3b5
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668314"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w usłudze Azure Kubernetes (AKS)

Podczas wdrażania i obsługi klastrów w usłudze Azure Kubernetes Service (AKS) należy zaimplementować sposoby zarządzania dostępem do zasobów i usług. Bez tych formantów konta mogą mieć dostęp do zasobów i usług, których nie potrzebują. Może być również trudne do śledzenia, który zestaw poświadczeń zostały użyte do wprowadzania zmian.

W tym artykule najważniejsze wskazówki koncentruje się na tym, jak operator klastra może zarządzać dostępem i tożsamością klastrów AKS. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Uwierzytelnij użytkowników klastra AKS za pomocą usługi Azure Active Directory
> * Kontroluj dostęp do zasobów za pomocą kontroli dostępu opartych na rolach (RBAC)
> * Używanie tożsamości zarządzanej do uwierzytelniania się za pomocą innych usług

## <a name="use-azure-active-directory"></a>Korzystanie z usługi Azure Active Directory

**Wskazówki dotyczące najlepszych rozwiązań** — wdrażanie klastrów AKS za pomocą integracji usługi Azure AD. Za pomocą usługi Azure AD centralizuje składnik zarządzania tożsamościami. Wszelkie zmiany stanu konta użytkownika lub grupy są automatycznie aktualizowane w dostępie do klastra AKS. Użyj ról lub role klastra i powiązania, jak omówiono w następnej sekcji, do zakresu użytkowników lub grup do najmniejszej ilości uprawnień wymaganych.

Deweloperzy i właściciele aplikacji klastra Kubernetes potrzebują dostępu do różnych zasobów. Kubernetes nie zapewnia rozwiązania do zarządzania tożsamościami, aby kontrolować, którzy użytkownicy mogą wchodzić w interakcje z jakimi zasobami. Zamiast tego zazwyczaj zintegrować klastra z istniejącego rozwiązania tożsamości. Usługa Azure Active Directory (AD) zapewnia rozwiązanie do zarządzania tożsamościami gotowe do organizacji i może integrować się z klastrami AKS.

Za pomocą klastrów zintegrowanych z usługą Azure AD w usłudze AKS tworzysz *role* lub *role klastrowe* definiujące uprawnienia dostępu do zasobów. Następnie *należy powiązać* role z użytkownikami lub grupami z usługi Azure AD. Te kubernetes kontroli dostępu opartego na rolach (RBAC) zostały omówione w następnej sekcji. Integracja usługi Azure AD i sposób kontrolowania dostępu do zasobów można zobaczyć na poniższym diagramie:

![Uwierzytelnianie na poziomie klastra dla integracji usługi Azure Active Directory z usługą AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Deweloper uwierzytelnia się za pomocą usługi Azure AD.
1. Punkt końcowy wystawiania tokenu usługi Azure AD wystawia token.
1. Deweloper wykonuje akcję przy użyciu tokenu usługi Azure AD, na przykład`kubectl create pod`
1. Usługa Kubernetes sprawdza poprawność tokenu za pomocą usługi Azure Active Directory i pobiera członkostwo w grupach dewelopera.
1. Zastosowano zasady kontroli dostępu opartej na rolach (RBAC) i klastra.
1. Żądanie dewelopera jest pomyślnie lub nie na podstawie wcześniejszej weryfikacji członkostwa w grupie usługi Azure AD i narzędzia Kubernetes RBAC i zasad.

Aby utworzyć klaster AKS korzystający z usługi Azure AD, zobacz [Integrowanie usługi Azure Active Directory z usługą AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Korzystanie z kontroli dostępu opartego na rolach (RBAC)

**Wskazówki dotyczące najlepszych rozwiązań** — użyj funkcji Kubernetes RBAC, aby zdefiniować uprawnienia, które użytkownicy lub grupy mają do zasobów w klastrze. Tworzenie ról i powiązań, które przypisują najmniejszą wymaganą ilość uprawnień. Integracja z usługą Azure AD, dzięki czemu wszelkie zmiany stanu użytkownika lub członkostwa w grupie są automatycznie aktualizowane, a dostęp do zasobów klastra jest aktualny.

W umięsień można zapewnić szczegółową kontrolę dostępu do zasobów w klastrze. Uprawnienia można zdefiniować na poziomie klastra lub do określonych obszarów nazw. Można zdefiniować, jakie zasoby mogą być zarządzane i z jakimi uprawnieniami. Te role są następnie stosowane do użytkowników lub grup z powiązaniem. Aby uzyskać więcej informacji na temat *ról,* *role klastra*i *powiązań,* zobacz [Opcje dostępu i tożsamości usługi Azure Kubernetes Service (AKS)][aks-concepts-identity].

Na przykład można utworzyć rolę, która udziela pełnego dostępu do zasobów w obszarze nazw o nazwie *finance-app*, jak pokazano w poniższym przykładzie manifestu YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Następnie tworzony jest element Wiązania roli, który wiąże dewelopera użytkownika usługi Azure *AD1 contoso.com\@* z elementami wiązania ról, jak pokazano w następującym manifeście YAML:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Gdy *\@developer1 contoso.com* jest uwierzytelniony względem klastra AKS, mają pełne uprawnienia do zasobów w obszarze nazw aplikacji *finanse.* W ten sposób logicznie oddzielić i kontrolować dostęp do zasobów. Usługa Kubernetes RBAC powinna być używana w połączeniu z integracją usługi Azure AD, jak omówiono w poprzedniej sekcji.

Aby zobaczyć, jak używać grup usługi Azure AD do kontrolowania dostępu do zasobów usługi Kubernetes przy użyciu funkcji RBAC, zobacz [Kontrolowanie dostępu do zasobów klastra przy użyciu kontroli dostępu opartych na rolach i tożsamościach usługi Azure Active Directory w usłudze AKS][azure-ad-rbac].

## <a name="use-pod-identities"></a>Używanie tożsamości zasobników

**Wskazówki dotyczące najlepszych praktyk** — nie należy używać stałych poświadczeń w zasobnikach lub obrazach kontenerów, ponieważ są one narażone na ryzyko narażenia lub nadużycia. Zamiast tego użyj tożsamości zasobników, aby automatycznie zażądać dostępu przy użyciu centralnego rozwiązania tożsamości usługi Azure AD. Tożsamości zasobników jest przeznaczony do użytku tylko z zasobników Systemu Linux i obrazów kontenerów.

Gdy zasobników potrzebują dostępu do innych usług platformy Azure, takich jak Usługi Usługi Cosmos DB, Key Vault lub Magazyn obiektów blob, zasobnik wymaga poświadczeń dostępu. Te poświadczenia dostępu mogą być zdefiniowane za pomocą obrazu kontenera lub wstrzykuje się jako klucz tajny Kubernetes, ale muszą być ręcznie tworzone i przypisywane. Często poświadczenia są ponownie używane w zasobnikach i nie są regularnie obracane.

Tożsamości zarządzane dla zasobów platformy Azure (obecnie implementowane jako skojarzony projekt open source usługi AKS) umożliwiają automatyczne żądanie dostępu do usług za pośrednictwem usługi Azure AD. Nie definiujesz ręcznie poświadczeń dla zasobników, zamiast tego żądają tokenu dostępu w czasie rzeczywistym i można go używać do uzyskiwania dostępu tylko do przypisanych im usług. W uzywu usługi AKS operator klastra wdraża dwa składniki, aby umożliwić zasobnikom używanie tożsamości zarządzanych:

* **Serwer tożsamości zarządzania węzłami (NMI)** jest zasobnikiem, który działa jako DemonSet w każdym węźle w klastrze AKS. Serwer NMI nasłuchuje żądań zasobników do usług platformy Azure.
* **Kontroler tożsamości zarządzanej (MIC)** jest zasobnikiem centralnym z uprawnieniami do wykonywania zapytań na serwerze interfejsu API usługi Kubernetes i sprawdzania mapowania tożsamości platformy Azure, które odpowiada zasobnikowi.

Gdy zasobników żądania dostępu do usługi platformy Azure, reguły sieciowe przekierować ruch do serwera tożsamości zarządzania węzłami (NMI). Serwer NMI identyfikuje zasobników, które żądają dostępu do usług platformy Azure na podstawie ich adresu zdalnego i wysyła zapytania do kontrolera tożsamości zarządzanej (MIC). Mic sprawdza mapowania tożsamości platformy Azure w klastrze AKS, a serwer NMI następnie żąda tokenu dostępu z usługi Azure Active Directory (AD) na podstawie mapowania tożsamości zasobnika. Usługa Azure AD zapewnia dostęp do serwera NMI, który jest zwracany do zasobnika. Ten token dostępu może służyć przez zasobnika, aby następnie zażądać dostępu do usług na platformie Azure.

W poniższym przykładzie deweloper tworzy zasobnik, który używa tożsamości zarządzanej do żądania dostępu do wystąpienia programu Azure SQL Server:

![Tożsamości zasobników umożliwiają zasobnikowi automatyczne żądanie dostępu do innych usług](media/operator-best-practices-identity/pod-identities.png)

1. Operator klastra najpierw tworzy konto usługi, które może służyć do mapowania tożsamości, gdy zasobników żądania dostępu do usług.
1. Serwer NMI i mic są wdrażane do przekazywania wszelkich żądań zasobnika tokenów dostępu do usługi Azure AD.
1. Deweloper wdraża zasobnik z tożsamością zarządzaną, która żąda tokenu dostępu za pośrednictwem serwera NMI.
1. Token jest zwracany do zasobnika i używany do uzyskiwania dostępu do wystąpienia programu Azure SQL Server.

> [!NOTE]
> Zarządzane tożsamości zasobników jest projektem open source i nie jest obsługiwany przez pomoc techniczną platformy Azure.

Aby użyć tożsamości zasobników, zobacz [Tożsamości usługi Azure Active Directory dla aplikacji Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Następne kroki

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na uwierzytelnianiu i autoryzacji klastra i zasobów. Aby zaimplementować niektóre z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Integracja usługi Azure Active Directory z usługą AKS][aks-aad]
* [Używanie tożsamości zarządzanych dla zasobów platformy Azure przy użyciu usługi AKS][aad-pod-identity]

Aby uzyskać więcej informacji na temat operacji klastra w uzywce AKS, zobacz następujące najlepsze rozwiązania:

* [Wielodostęp i izolacja klastra][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje harmonogramu kubernetes][aks-best-practices-scheduler]
* [Zaawansowane funkcje harmonogramu Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
