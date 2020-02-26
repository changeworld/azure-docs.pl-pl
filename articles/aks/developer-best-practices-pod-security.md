---
title: Deweloper najlepsze rozwiązania — zasobnika zabezpieczeń w usłudze Azure Kubernetes usługi (AKS)
description: Poznaj najlepsze rozwiązania dla deweloperów dotyczące sposobu bezpiecznego zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: eaeb81d7f93124f1f3dedf9676314b1b786d8571
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595845"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń zasobnik w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenie zasobników kluczowa jest. Aplikacje powinny być przeznaczone dla zasad minimalnej liczby wymaganych uprawnień. Zapewnienie bezpieczeństwa danych prywatnych jest najważniejsze dla klientów. Nie chcesz, aby poświadczenia, takie jak parametry połączenia bazy danych, klucze lub wpisy tajne oraz certyfikaty ujawnione na świecie zewnętrznym, w którym osoba atakująca może wykorzystać te klucze tajne do złośliwych celów. Nie, dodać je do kodu lub osadzane w obrazów kontenerów. Takie podejście może utworzyć ryzyka dla zagrożeń i ograniczyć możliwość obracania tych poświadczeń jako obrazy kontenera będzie konieczne jest ponowne skompilowanie.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na sposobach zabezpieczania zespołów w AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Użyj zasobnika kontekstu zabezpieczeń do ograniczenia dostępu do procesów i usług lub podwyższenie poziomu uprawnień
> * Uwierzytelnianie za pomocą innych zasobów platformy Azure przy użyciu tożsamości zasobnika zarządzane
> * Żądania i pobierania poświadczeń z magazynu cyfrowych, takich jak usługi Azure Key Vault

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zabezpieczeń klastra][best-practices-cluster-security] i [zarządzania obrazami kontenerów][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Zasobnik bezpiecznego dostępu do zasobów

**Wskazówki dotyczące najlepszych** rozwiązań — do uruchamiania jako inny użytkownik lub Grupa i ograniczania dostępu do podstawowych procesów i usług węzła, definiowania ustawień kontekstu zabezpieczeń. Przypisz najmniejszej liczby wymaganych uprawnień.

Aby aplikacje działały prawidłowo, należy uruchomić jako zdefiniowany użytkownika lub grupę, a nie jako *element główny*. `securityContext` dla elementu lub kontenera umożliwia zdefiniowanie ustawień, takich jak *runAsUser* lub *fsGroup* , aby założyć odpowiednie uprawnienia. Tylko przypisz wymagane uprawnienia użytkownika lub grupy, a nie należy używać kontekstu zabezpieczeń jako środek do przyjęcia dodatkowych uprawnień. Ustawienia *runAsUser*, eskalacji uprawnień i inne funkcje systemu Linux są dostępne tylko w węzłach i w systemach Linux.

Po uruchomieniu jako użytkownik inny niż główny kontenerów nie można powiązać z uprzywilejowanym portów 1024 w obszarze. W tym scenariuszu usługi Kubernetes może służyć do zamaskowania fakt, że aplikacja jest uruchomiona na określonym porcie.

Kontekst zabezpieczeń zasobnika można również zdefiniować dodatkowe możliwości lub uprawnienia do uzyskiwania dostępu do procesów i usług. Można ustawić następujące typowe definicje kontekstu zabezpieczeń:

* **allowPrivilegeEscalation** określa, czy pod warunkiem, że może przyjmować uprawnienia *root* . Zaprojektuj swoje aplikacje, aby to ustawienie było zawsze ustawione na *wartość false*.
* **Możliwości systemu Linux** pozwalają na dostęp do węzła podstawowego węzłów. Należy zadbać o przypisanie tych możliwości. Przypisz najmniejszej liczby potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [możliwości systemu Linux][linux-capabilities].
* **Etykiety SELinux** to moduł zabezpieczeń jądra systemu Linux, który umożliwia definiowanie zasad dostępu dla usług, procesów i dostępu do systemu plików. Ponownie przypisać najmniejszej liczby potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [Opcje SELinux w Kubernetes][selinux-labels]

Następujące manifest YAML zasobnika przykład ustawia zabezpieczeń ustawienia kontekstu do definiowania:

* Pod uruchomieniem jako identyfikator użytkownika *1000* i częścią grupy o identyfikatorze *2000*
* Nie można eskalować uprawnień do używania `root`
* Zezwala na możliwości systemu Linux, aby uzyskiwać dostęp do interfejsów sieciowych i hosta w czasie rzeczywistym (sprzęt) zegara

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

Praca z operatora sieci klastra, aby określić, jakie ustawienia kontekstu zabezpieczeń potrzebne. Spróbuj zaprojektować aplikacje tak, aby zminimalizować dodatkowych uprawnień i dostępu, który wymaga zasobnik. Istnieją dodatkowe funkcje zabezpieczeń do ograniczenia dostępu przy użyciu AppArmor i funkcję seccomp (bezpiecznego przetwarzania danych), który może być implementowany przez operatorów klastra. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do kontenera do zasobów][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Limit widoczności poświadczeń

**Wskazówki dotyczące najlepszych** rozwiązań — nie Definiuj poświadczeń w kodzie aplikacji. Używaj zarządzanych tożsamości dla zasobów platformy Azure, aby umożliwić dostęp pod żądania do innych zasobów. Magazyn cyfrowych, takich jak usługi Azure Key Vault, należy również do przechowywania i pobierania kluczy cyfrowych i poświadczenia. Tożsamości zarządzane pod są przeznaczone wyłącznie dla systemów Linux i obrazów kontenerów.

Aby ograniczyć ryzyko związane z poświadczeniami ujawniania w kodzie aplikacji, należy unikać stosowania stałej lub udostępnionych poświadczeń. Poświadczeń ani kluczy nie powinny być uwzględniane bezpośrednio w kodzie. Jeśli te poświadczenia są udostępniane, aplikacja musi zostać zaktualizowany i ponownego wdrażania. Lepszym rozwiązaniem jest zapewnienie zasobników własnej tożsamości i sposób uwierzytelnić lub automatycznie pobrać poświadczeń z magazynu cyfrowych.

Następujące [skojarzone projekty typu open source AKS][aks-associated-projects] umożliwiają automatyczne uwierzytelnianie z magazynów lub zażądanie poświadczeń i kluczy z magazynu cyfrowego:

* Zarządzane tożsamości dla zasobów platformy Azure i
* Sterownik platformy Azure Key Vault FlexVol

Skojarzone projekty open source AKS nie są obsługiwane przez pomoc techniczną platformy Azure. Są one dostarczane w celu zebrania opinii i usterek z naszej społeczności. Te projekty nie są zalecane do użycia w środowisku produkcyjnym.

### <a name="use-pod-managed-identities"></a>Zasobnik wykorzystania zarządzanych tożsamości

Zarządzana tożsamość zasobów platformy Azure pozwala na uwierzytelnianie pod względem usług platformy Azure, które go obsługują, takich jak Storage czy SQL. Zasobnik jest przypisany, pozwalającą na uwierzytelnianie w usłudze Azure Active Directory i odebrać token cyfrowych tożsamość platformy Azure. Ten token cyfrowego widoczne dla innych usług platformy Azure, które sprawdzają, czy zasobnika ustawiany jest autoryzowany do uzyskania dostępu do usługi i wykonaj wymagane akcje. Tej metody oznacza, że nie wpisów tajnych są wymagane do parametrów połączenia bazy danych, na przykład. Uproszczone przepływu pracy do zasobnika zarządzanych tożsamości pokazano na poniższym diagramie:

![Uproszczone przepływ pracy dotyczący zasobnika zarządzane tożsamością na platformie Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Za pomocą tożsamości zarządzanej kod aplikacji nie musi zawierać poświadczenia dostępu do usługi, takie jak Azure Storage. Jak pod każdym uwierzytelnia się za pomocą jego tożsamość, można więc inspekcji i przegląd dostępu wszystkich użytkowników. Jeśli aplikacja łączy się z innymi usługami platformy Azure, użyj usługi managed tożsamości do ponownego używania poświadczeń limit i ryzyko narażenia na zagrożenia.

Aby uzyskać więcej informacji o tożsamościach pod, zobacz [Konfigurowanie klastra AKS do użycia z tożsamościami zarządzanymi i aplikacjami][aad-pod-identity] .

### <a name="use-azure-key-vault-with-flexvol"></a>Usługa Azure Key Vault za pomocą FlexVol

Tożsamości zarządzanej zasobnika sprawdzają się najlepiej do uwierzytelniania względem pomocnicze usługi platformy Azure. Dla własnych usług lub aplikacji bez zarządzanych tożsamości dla zasobów platformy Azure nadal uwierzytelniania za pomocą poświadczeń ani kluczy. Cyfrowy magazynu może służyć do przechowywania tych poświadczeń.

Jeśli aplikacje muszą poświadczeń, komunikują się z magazynem cyfrowej, pobieranie najnowszych poświadczeń, a następnie połącz do wymaganej usługi. Usługa Azure Key Vault może być ten magazyn cyfrowych. Na poniższym diagramie pokazano uproszczonego przepływu pracy do pobierania poświadczeń z usługi Azure Key Vault przy użyciu tożsamości zasobnika zarządzane:

![Uproszczone przepływ pracy na potrzeby pobierania poświadczeń z usługi Key Vault przy użyciu zasobnik tożsamości zarządzanej](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Usługa Key Vault do przechowywania i regularnie Obróć klucze tajne, takie jak poświadczenia, klucze konta magazynu lub certyfikatów. Usługa Azure Key Vault można zintegrować z klastrem usługi AKS przy użyciu FlexVolume. Sterownik FlexVolume umożliwia klastra AKS natywnie pobrać poświadczeń z usługi Key Vault oraz bezpiecznie udostępniać je tylko pod wysyłającego żądanie. Praca z operatorem klastra, aby wdrożyć sterownik Key Vault FlexVol na węzłów AKS. Tożsamość zasobnika zarządzane umożliwia zażądanie dostępu do usługi Key Vault i pobierania poświadczeń, których potrzebujesz, za pośrednictwem sterownika FlexVolume.

Azure Key Vault z FlexVol jest przeznaczony do użytku z aplikacjami i usługami działającymi w ramach systemów i węzłów systemu Linux.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na sposób zabezpieczania zasobników. Aby zaimplementować, niektóre z tych obszarów, zobacz następujące artykuły:

* [Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure z usługą AKS][aad-pod-identity]
* [Integracja Azure Key Vault z usługą AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
