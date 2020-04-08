---
title: Najważniejsze wskazówki dotyczące zabezpieczeń
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami dla deweloperów dotyczących zabezpieczania zasobników w usłudze Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804388"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące zabezpieczeń zasobników w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) bezpieczeństwo zasobników jest kluczowym czynnikiem. Aplikacje powinny być zaprojektowane z myślą o zasadzie najmniejszej liczby wymaganych uprawnień. Zapewnienie bezpieczeństwa prywatnych danych jest najważniejsze dla klientów. Nie chcesz poświadczeń, takich jak parametry połączenia bazy danych, klucze lub wpisy tajne i certyfikaty udostępniane światu zewnętrznemu, gdzie osoba atakująca może skorzystać z tych wpisów tajnych do złośliwych celów. Nie dodawaj ich do kodu ani nie osadzaj w obrazach kontenerów. Takie podejście stworzyłoby ryzyko narażenia i ograniczyłoby możliwość obracania tych poświadczeń, ponieważ obrazy kontenerów będą musiały zostać przebudowane.

W tym artykule najważniejsze wskazówki koncentruje się na tym, jak zabezpieczyć zasobników w układzie AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Użyj kontekstu zabezpieczeń zasobnika, aby ograniczyć dostęp do procesów i usług lub eskalacji uprawnień
> * Uwierzytelnij się przy użyciu innych zasobów platformy Azure przy użyciu tożsamości zarządzanych zasobników
> * Żądaj i pobieraj poświadczenia z magazynu cyfrowego, takiego jak Usługa Azure Key Vault

Można również zapoznać się z najlepszymi rozwiązaniami w zakresie [zabezpieczeń klastra][best-practices-cluster-security] i [zarządzania obrazami kontenerów][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Bezpieczny dostęp zasobników do zasobów

**Wskazówki dotyczące najlepszych rozwiązań** — aby uruchomić jako inny użytkownik lub grupa i ograniczyć dostęp do podstawowych procesów i usług węzła, należy zdefiniować ustawienia kontekstu zabezpieczeń zasobników. Przypisz najmniejszą wymaganą liczbę uprawnień.

Aby aplikacje działały poprawnie, zasobniki powinny działać jako zdefiniowany użytkownik lub grupa, a nie jako *główny*. Dla `securityContext` zasobnika lub kontenera pozwala zdefiniować ustawienia, takie jak *runAsUser* lub *fsGroup,* aby przyjąć odpowiednie uprawnienia. Przypisz tylko wymagane uprawnienia użytkownika lub grupy i nie używaj kontekstu zabezpieczeń jako środka do przyjęcia dodatkowych uprawnień. *RunAsUser*, eskalacja uprawnień i inne ustawienia możliwości systemu Linux są dostępne tylko w węzłach i zasobnikach systemu Linux.

Po uruchomieniu jako użytkownik niekorzeń kontenerów nie można powiązać z portami uprzywilejowanymi w obszarze 1024. W tym scenariuszu usługi Kubernetes może służyć do ukrycia fakt, że aplikacja jest uruchomiona na określonym porcie.

Kontekst zabezpieczeń zasobnika można również zdefiniować dodatkowe możliwości lub uprawnienia dostępu do procesów i usług. Można ustawić następujące typowe definicje kontekstu zabezpieczeń:

* **allowPrivilegeEscalation** definiuje, czy zasobnik może zakładać uprawnienia *główne.* Zaprojektuj aplikacje, aby to ustawienie było zawsze ustawione na *false*.
* **Możliwości systemu Linux** umożliwiają zasobnikowi dostęp do podstawowych procesów węzłów. Należy zadbać o przypisanie tych możliwości. Przypisz najmniejszą liczbę wymaganych uprawnień. Aby uzyskać więcej informacji, zobacz [możliwości systemu Linux][linux-capabilities].
* **Etykiety SELinux** to moduł bezpieczeństwa jądra Linuksa, który umożliwia definiowanie zasad dostępu dla usług, procesów i dostępu do systemu plików. Ponownie przypisz najmniejszą liczbę wymaganych uprawnień. Aby uzyskać więcej informacji, zobacz [Opcje SELinux w aplikacji Kubernetes][selinux-labels]

Poniższy przykładowy manifest YAML zasobnika ustawia ustawienia kontekstu zabezpieczeń do zdefiniowania:

* Zasobnik działa jako identyfikator użytkownika *1000* i część identyfikatora grupy *2000*
* Nie można eskalować uprawnień do użycia`root`
* Umożliwia linuksowi dostęp do interfejsów sieciowych i zegara hosta w czasie rzeczywistym (sprzętowym)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Skontaktuj się z operatorem klastra, aby określić, jakich ustawień kontekstu zabezpieczeń potrzebujesz. Spróbuj zaprojektować aplikacje, aby zminimalizować dodatkowe uprawnienia i dostęp do zasobnika wymaga. Istnieją dodatkowe funkcje zabezpieczeń, aby ograniczyć dostęp przy użyciu AppArmor i seccomp (bezpieczne przetwarzanie), które mogą być implementowane przez operatorów klastra. Aby uzyskać więcej informacji, zobacz [Bezpieczny dostęp kontenera do zasobów][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Ograniczanie ekspozycji poświadczeń

**Wskazówki dotyczące najlepszych rozwiązań** — nie definiuj poświadczeń w kodzie aplikacji. Użyj tożsamości zarządzanych dla zasobów platformy Azure, aby umożliwić zasobnikowi żądanie dostępu do innych zasobów. Magazyn cyfrowy, taki jak Usługa Azure Key Vault, powinien być również używany do przechowywania i pobierania kluczy cyfrowych i poświadczeń. Pod tożsamości zarządzane jest przeznaczony do użytku tylko z zasobników systemu Linux i obrazów kontenerów.

Aby ograniczyć ryzyko udostępnienia poświadczeń w kodzie aplikacji, należy unikać używania poświadczeń stałych lub udostępnionych. Poświadczenia lub klucze nie powinny być uwzględniane bezpośrednio w kodzie. Jeśli te poświadczenia są widoczne, aplikacja musi zostać zaktualizowana i ponownie wyeszła. Lepszym rozwiązaniem jest nadajnienie zasobników własnej tożsamości i sposobu uwierzytelniania się lub automatycznego pobierania poświadczeń z cyfrowego magazynu.

Następujące [skojarzone projekty typu open source usługi AKS][aks-associated-projects] umożliwiają automatyczne uwierzytelnianie zasobników lub żądania poświadczeń i kluczy z cyfrowego magazynu:

* Tożsamości zarządzane dla zasobów platformy Azure i
* Sterownik usługi Azure Key Vault FlexVol

Skojarzone projekty open source usługi AKS nie są obsługiwane przez pomoc techniczną platformy Azure. Są one dostarczane w celu zebrania opinii i błędów od naszej społeczności. Projekty te nie są zalecane do użytku produkcyjnego.

### <a name="use-pod-managed-identities"></a>Używanie tożsamości zarządzanych zasobników

Tożsamość zarządzana dla zasobów platformy Azure umożliwia zasobnikowi uwierzytelnić się na usługach platformy Azure, które go obsługują, takich jak Magazyn lub SQL. Zasobnik jest przypisany do usługi Azure Identity, która umożliwia im uwierzytelnienie się w usłudze Azure Active Directory i odbieranie tokenu cyfrowego. Ten token cyfrowy można przedstawić innym usługom platformy Azure, które sprawdzają, czy zasobnik jest autoryzowany do uzyskiwania dostępu do usługi i wykonywania wymaganych akcji. Takie podejście oznacza, że nie są wymagane żadne wpisy tajne dla ciągów połączeń bazy danych, na przykład. Uproszczony przepływ pracy dla tożsamości zarządzanej zasobnika jest pokazany na poniższym diagramie:

![Uproszczony przepływ pracy dla tożsamości zarządzanej zasobnika na platformie Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Przy użyciu tożsamości zarządzanej kod aplikacji nie musi zawierać poświadczeń, aby uzyskać dostęp do usługi, takiej jak usługa Azure Storage. Jak każdy zasobnik uwierzytelnia się z własną tożsamością, dzięki czemu można inspekcji i przeglądu dostępu. Jeśli aplikacja łączy się z innymi usługami platformy Azure, użyj zarządzanych tożsamości, aby ograniczyć ponowne użycie poświadczeń i ryzyko narażenia.

Aby uzyskać więcej informacji na temat tożsamości zasobników, zobacz [Konfigurowanie klastra AKS do używania tożsamości zarządzanych zasobników i aplikacji][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Korzystanie z usługi Azure Key Vault z usługą FlexVol

Zarządzane tożsamości zasobników działają świetnie, aby uwierzytelnić się przed obsługą usług platformy Azure. W przypadku własnych usług lub aplikacji bez zarządzanych tożsamości zasobów platformy Azure nadal uwierzytelniasz się przy użyciu poświadczeń lub kluczy. Do przechowywania tych poświadczeń można używać magazynu cyfrowego.

Gdy aplikacje potrzebują poświadczenia, komunikują się z magazynem cyfrowym, pobierają najnowsze poświadczenia, a następnie łączą się z wymaganą usługą. Usługa Azure Key Vault może być tym cyfrowym magazynem. Uproszczony przepływ pracy do pobierania poświadczeń z usługi Azure Key Vault przy użyciu tożsamości zarządzanych zasobnika jest pokazany na poniższym diagramie:

![Uproszczony przepływ pracy do pobierania poświadczeń z magazynu kluczy przy użyciu tożsamości zarządzanej zasobnika](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Za pomocą usługi Key Vault można przechowywać i regularnie obracać wpisy tajne, takie jak poświadczenia, klucze kont magazynu lub certyfikaty. Usługę Azure Key Vault można zintegrować z klastrem AKS za pomocą funkcji FlexVolume. Sterownik FlexVolume umożliwia klastrowi AKS natywnie pobieranie poświadczeń z usługi Key Vault i bezpieczne dostarczanie ich tylko do żądanego zasobnika. Współpracuj z operatorem klastra, aby wdrożyć sterownik Key Vault FlexVol w węzłach AKS. Za pomocą zasobnika tożsamości zarządzanej można zażądać dostępu do usługi Key Vault i pobrać poświadczenia potrzebne za pośrednictwem sterownika FlexVolume.

Usługa Azure Key Vault z usługą FlexVol jest przeznaczona do użytku z aplikacjami i usługami działającymi w zasobnikach i węzłach systemu Linux.

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na tym, jak zabezpieczyć zasobników. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Używanie tożsamości zarządzanych dla zasobów platformy Azure przy użyciu usługi AKS][aad-pod-identity]
* [Integracja usługi Azure Key Vault z usługą AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
