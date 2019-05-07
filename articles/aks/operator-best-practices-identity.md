---
title: Operator najlepsze rozwiązania — tożsamości w usłudze Azure Kubernetes usługi (AKS)
description: Dowiedz się, operator klastra najlepsze rozwiązania dotyczące sposobów zarządzania uwierzytelniania i autoryzacji dla klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: iainfou
ms.openlocfilehash: f98e38556458b8d8a675d1e3f985aacfca022082
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074166"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze Azure Kubernetes Service (AKS)

Wdrażanie i obsługa klastrów w usłudze Azure Kubernetes Service (AKS), należy zaimplementować sposobów, aby zarządzać dostępem do zasobów i usług. Bez tych kontrolek konta mogą mieć dostęp do zasobów i usług, które nie potrzebują. Również może być trudne do śledzenia, który zestaw poświadczeń użytych do zmiany.

Najlepsze rozwiązania dotyczące tej koncentruje się na jak operator klastra można zarządzanie dostępem i tożsamością dla klastrów usługi AKS. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Uwierzytelnianie użytkowników klaster AKS za pomocą usługi Azure Active Directory
> * Kontrola dostępu do zasobów przy użyciu kontroli dostępu opartej na rolach (RBAC)
> * Korzystania z zarządzanych tożsamości do uwierzytelniania się z innymi usługami

## <a name="use-azure-active-directory"></a>Use Azure Active Directory

**Najważniejsze wskazówki** -AKS wdrażanie klastrów przy użyciu integracji z usługą Azure AD. Używanie programu Azure AD centralizuje składnika zarządzania tożsamościami. Każda zmiana stanu konta lub grupy użytkowników jest automatycznie aktualizowana w dostęp do klastra usługi AKS. Zgodnie z opisem w następnej sekcji, ustal zakres użytkowników lub grup w celu najmniejszą ilością uprawnień niezbędnych, należy użyć ról lub ClusterRoles i powiązań.

Deweloperzy i właściciele aplikacji klastra Kubernetes muszą mieć dostęp do różnych zasobów. Rozwiązanie Kubernetes nie zapewnia rozwiązanie zarządzania tożsamościami, aby określić, które użytkownicy mogą korzystać z zasobów. Zamiast tego możesz zazwyczaj integracja klastra z istniejącego rozwiązania tożsamości. Usługa Azure Active Directory (AD) zapewnia rozwiązanie zarządzania tożsamościami w przedsiębiorstwach i można zintegrować z klastrami usługi AKS.

Dzięki usłudze Azure zintegrowanych z usługą AD klastrów w usłudze AKS, można tworzyć *role* lub *ClusterRoles* definiują uprawnienia dostępu do zasobów. Następnie *powiązać* ról do użytkowników lub grup z usługi Azure AD. W następnej sekcji omówiono te rozwiązania Kubernetes kontroli dostępu opartej na rolach (RBAC). Integracja usługi Azure AD i jak kontrolować dostęp do zasobów są widoczne na poniższym diagramie:

![Uwierzytelnianie poziomu klastra na potrzeby integracji usługi Azure Active Directory za pomocą usługi AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Deweloper uwierzytelnia się za pomocą usługi Azure AD.
1. Punkt końcowy wystawiania tokenu usługi Azure AD wystawia token dostępu.
1. Deweloper wykonuje akcję przy użyciu tokenu usługi Azure AD, takich jak `kubectl create pod`
1. Kubernetes weryfikuje token z usługi Azure Active Directory i pobiera członkostwa w grupach dla deweloperów.
1. Kubernetes opartej na rolach kontrola dostępu (RBAC) i klaster są stosowane zasady.
1. Żądanie dewelopera jest pomyślnie lub nie w zależności od poprzednich weryfikacji członkostwa w grupie usługi Azure AD, RBAC platformy Kubernetes i zasad.

Aby utworzyć klaster AKS, która używa usługi Azure AD, zobacz [integracji usługi Azure Active Directory za pomocą usługi AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Korzystanie z kontroli dostępu opartej na rolach (RBAC)

**Najważniejsze wskazówki** — Użyj Kubernetes RBAC do definiowania, które użytkownicy lub grupy mają uprawnienia do zasobów w klastrze. Tworzenie ról i powiązań, które przypisać najmniejszą ilością uprawnień wymaganych. Integracja z usługą Azure AD, więc wszelkie zmiany w stan użytkownika lub członkostwa w grupie jest automatycznie aktualizowana i dostępu do zasobów klastra jest aktualny.

W usłudze Kubernetes możesz podać szczegółową kontrolę dostępu do zasobów w klastrze. Można zdefiniować uprawnienia na poziomie klastra lub określonych obszarów nazw. Można określić, jakie zasoby mogą być zarządzane i z jakimi uprawnieniami. Te role są stosowane do użytkowników lub grup z powiązaniem. Aby uzyskać więcej informacji na temat *role*, *ClusterRoles*, i *powiązania*, zobacz [opcje dostęp i tożsamość dla usługi Azure Kubernetes Service (AKS)] [aks-concepts-identity].

Na przykład możesz utworzyć rolę, która przyznaje pełny dostęp do zasobów w przestrzeni nazw o nazwie *aplikacji finansowych*, jak pokazano w manifeście YAML następujący przykład:

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

RoleBinding wiążąca użytkownika usługi Azure AD zostanie utworzona *developer1\@contoso.com* do RoleBinding, jak pokazano w poniższym manifest YAML:

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

Gdy *developer1\@contoso.com* jest uwierzytelniany względem klastra AKS mają pełne uprawnienia do zasobów w *aplikacji finansowych* przestrzeni nazw. W tym sposób, możesz logicznie oddzielne i kontroli dostępu do zasobów. Kubernetes RBAC należy używać w połączeniu z platformą Azure — integracji z usługą AD, zgodnie z opisem w poprzedniej sekcji.

Aby zobaczyć, jak kontrolować dostęp do zasobów usługi Kubernetes przy użyciu funkcji RBAC za pomocą grup usługi Azure AD, zobacz [kontrolować dostęp do zasobów klastra przy użyciu tożsamości usługi Azure Active Directory i kontroli dostępu opartej na rolach w usłudze AKS] [ azure-ad-rbac].

## <a name="use-pod-identities"></a>Użyj tożsamości zasobników

**Najważniejsze wskazówki** — nie należy używać stałych poświadczeń w obrębie zasobników lub obrazów kontenerów, ponieważ są one ryzyko ujawnienia lub nadużycia związane. Zamiast tego należy użyć zasobnika tożsamości do automatycznego żądania dostępu przy użyciu centralnego rozwiązania z tożsamością usługi Azure AD. Zasobnik tożsamości jest przeznaczona dla systemu Linux zasobników i tylko obrazy kontenerów.

Gdy zasobników muszą mieć dostęp do innych usług platformy Azure, takich jak usługi Cosmos DB, usługa Key Vault lub usługi Blob Storage zasobnik musi poświadczenia dostępu. Te poświadczenia dostępu można zdefiniować przy użyciu obrazu kontenera lub wprowadzony jako wpisu tajnego rozwiązania Kubernetes, ale musisz ręcznie utworzone i przypisane. Często poświadczenia są używane ponownie na zasobników i nie są regularnie obracać.

Zarządzanych tożsamości dla zasobów platformy Azure (obecnie implementowane jako projekt typu open source AKS skojarzona) pozwalają na automatyczne żądanie dostępu do usług za pośrednictwem usługi Azure AD. Nie można ręcznie zdefiniować poświadczeń dla zasobników, zamiast tego są żądania tokenu dostępu w czasie rzeczywistym i można używać tylko w ich przypisanej usługi dostępu do. W usłudze AKS dwa składniki są wdrażane przez operator klastra umożliwia zasobników w celu używania tożsamości zarządzanej:

* **Serwer węzła zarządzania tożsamościami (NMI)** jest zasobników, która działa jako DaemonSet w każdym węźle w klastrze AKS. Serwer NMI nasłuchuje żądań zasobnika do usług platformy Azure.
* **Kontroler zarządzanych tożsamości (MIC)** jest centralna zasobnik za pomocą uprawnień, aby wysyłać zapytań do serwera interfejsu API rozwiązania Kubernetes i sprawdza, czy dla usługi Azure identity mapowania, który odnosi się do zasobników.

Zasobników żądania dostępu do usługi platformy Azure, reguł sieci przekierować ruch do serwera węzła zarządzania tożsamościami (NMI). Serwer NMI identyfikuje zasobników, żądających dostępu do usług platformy Azure na podstawie ich adresu zdalnego i zapytanie kontrolera zarządzanych tożsamości (MIC). MIC sprawdza, czy mapowania tożsamości platformy Azure w klastrze AKS i serwera NMI, a następnie żąda tokenu dostępu z usługi Azure Active Directory (AD) w oparciu zasobnika mapowania tożsamości. Usługa Azure AD zapewnia dostęp do serwera NMI, który jest zwracany do zasobników. Ten token dostępu może służyć przez zasobnik, aby następnie zażądać dostępu do usług na platformie Azure.

W poniższym przykładzie deweloper tworzy zasobnik, który korzysta z tożsamości zarządzanej umożliwia zażądanie dostępu do wystąpienia serwera SQL Azure:

![Zasobnik tożsamości, umożliwiają zasobników w celu automatycznego żądania dostępu do innych usług](media/operator-best-practices-identity/pod-identities.png)

1. Operator klastra najpierw tworzy konto usługi, który może służyć do mapowania tożsamości, gdy zasobników żądanie dostępu do usług.
1. Serwer NMI i sprawdzanie integralności komunikatu są wdrażane do przekazywania żądań zasobnika dla tokenów dostępu do usługi Azure AD.
1. Deweloper wdraża zasobnik za pomocą tożsamości zarządzanej, który żąda tokenu dostępu za pośrednictwem serwera NMI.
1. Token jest zwracana do zasobnika ustawiany i umożliwiają dostęp do wystąpienia programu SQL Server Azure.

> [!NOTE]
> Tożsamości zarządzanej zasobnik to projekt typu open source, a nie jest obsługiwana przez pomoc techniczną systemu Azure.

Aby użyć tożsamości zasobników, zobacz [tożsamości usługi Azure Active Directory dla aplikacji platformy Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące tej koncentruje się na uwierzytelnianie i autoryzacja dla klastra i zasoby. Aby zaimplementować niektórych z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Integrowanie usługi Azure Active Directory za pomocą usługi AKS][aks-aad]
* [Użyj zarządzanych tożsamości dla zasobów platformy Azure za pomocą usługi AKS][aad-pod-identity]

Aby uzyskać więcej informacji na temat operacji klastra w usłudze AKS zobacz poniższe najlepsze rozwiązania:

* [Izolację wielodostępu i klastra][aks-best-practices-scheduler]
* [Podstawowe funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-scheduler]
* [Zaawansowane funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-advanced-scheduler]

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
