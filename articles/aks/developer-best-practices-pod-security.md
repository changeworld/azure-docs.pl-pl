---
title: Deweloper najlepsze rozwiązania — zasobnika zabezpieczeń w usłudze Azure Kubernetes usługi (AKS)
description: Poznaj najlepsze rozwiązania dla deweloperów dotyczące sposobu bezpiecznego zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: f9d49d143b31b0b9e73d8a147605935cd88d412b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073965"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń zasobnik w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenie zasobników kluczowa jest. Aplikacje powinny zostać tak zaprojektowane, zasady najmniejszą liczbą uprawnień wymaganych. Zapewnienie bezpieczeństwa danych prywatnych jest najważniejsze dla klientów. Nie ma poświadczeń, takich jak parametry połączenia bazy danych, klucze lub wpisy tajne i certyfikaty widoczne na zewnątrz, gdzie osoba atakująca może korzystać z tych kluczy tajnych do złośliwych celów. Nie, dodać je do kodu lub osadzane w obrazów kontenerów. Takie podejście może utworzyć ryzyka dla zagrożeń i ograniczyć możliwość obracania tych poświadczeń jako obrazy kontenera będzie konieczne jest ponowne skompilowanie.

Najlepsze rozwiązania dotyczące tej koncentruje się na jak bezpieczne zasobników w usłudze AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Użyj zasobnika kontekstu zabezpieczeń do ograniczenia dostępu do procesów i usług lub podwyższenie poziomu uprawnień
> * Uwierzytelnianie za pomocą innych zasobów platformy Azure przy użyciu tożsamości zasobnika zarządzane
> * Żądania i pobierania poświadczeń z magazynu cyfrowych, takich jak usługi Azure Key Vault

Można również przeczytać najlepsze rozwiązania dotyczące [klastra zabezpieczeń] [ best-practices-cluster-security] i [zarządzanie obrazami kontenera][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Zasobnik bezpiecznego dostępu do zasobów

**Najważniejsze wskazówki** — Aby uruchomić jako różny dostęp użytkownika lub grupy i limit do podstawowych procesów węzła i usług, zdefiniuj ustawienia kontekstu zabezpieczeń pod. Przypisz najmniejszej liczby wymaganych uprawnień.

Dla aplikacji do poprawnego działania zasobników powinny być uruchamiane jako zdefiniowanych użytkownika lub grupy, a nie jako *głównego*. `securityContext` Dla pod lub kontenerów pozwala zdefiniować ustawienia, takie jak *nazwa_użytkownika* lub *fsGroup* założenie odpowiednie uprawnienia. Tylko przypisz wymagane uprawnienia użytkownika lub grupy, a nie należy używać kontekstu zabezpieczeń jako środek do przyjęcia dodatkowych uprawnień. *Nazwa_użytkownika*, podwyższenie poziomu uprawnień i inne ustawienia funkcji systemu Linux są dostępne tylko na węzłów systemu Linux i zasobników.

Po uruchomieniu jako użytkownik inny niż główny kontenerów nie można powiązać z uprzywilejowanym portów 1024 w obszarze. W tym scenariuszu usługi Kubernetes może służyć do zamaskowania fakt, że aplikacja jest uruchomiona na określonym porcie.

Kontekst zabezpieczeń zasobnika można również zdefiniować dodatkowe możliwości lub uprawnienia do uzyskiwania dostępu do procesów i usług. Można ustawić następujące typowe definicje kontekstu zabezpieczeń:

* **allowPrivilegeEscalation** Określa, czy można założyć zasobnik *głównego* uprawnień. Projektowanie aplikacji tak, to ustawienie jest zawsze równa *false*.
* **Możliwości Linux** umożliwiają zasobnika dostęp do podstawowych procesów węzła. Należy zadbać o przypisanie tych możliwości. Przypisz najmniejszej liczby potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [możliwości Linux][linux-capabilities].
* **Etykiety SELinux** jest modułu zabezpieczeń jądra systemu Linux, które umożliwiają definiowanie zasad dostępu dla dostępu do usług, procesów i system plików. Ponownie przypisać najmniejszej liczby potrzebnych uprawnień. Aby uzyskać więcej informacji, zobacz [SELinux opcje w usłudze Kubernetes][selinux-labels]

Następujące manifest YAML zasobnika przykład ustawia zabezpieczeń ustawienia kontekstu do definiowania:

* Zasobnik jest uruchamiana jako identyfikator użytkownika *1000* , identyfikator grupy jest częścią *2000*
* Nie można eskalować uprawnienia do użycia `root`
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

Praca z operatora sieci klastra, aby określić, jakie ustawienia kontekstu zabezpieczeń potrzebne. Spróbuj zaprojektować aplikacje tak, aby zminimalizować dodatkowych uprawnień i dostępu, który wymaga zasobnik. Istnieją dodatkowe funkcje zabezpieczeń do ograniczenia dostępu przy użyciu AppArmor i funkcję seccomp (bezpiecznego przetwarzania danych), który może być implementowany przez operatorów klastra. Aby uzyskać więcej informacji, zobacz [bezpieczny kontener dostęp do zasobów][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Limit widoczności poświadczeń

**Najważniejsze wskazówki** — nie definiują poświadczenia w kodzie aplikacji. Używaj zarządzanych tożsamości dla zasobów platformy Azure, aby umożliwić dostęp pod żądania do innych zasobów. Magazyn cyfrowych, takich jak usługi Azure Key Vault, należy również do przechowywania i pobierania kluczy cyfrowych i poświadczenia. Zasobnik zarządzanych tożsamości jest przeznaczona do użytku z zasobników systemu Linux i tylko obrazy kontenerów.

Aby ograniczyć ryzyko związane z poświadczeniami ujawniania w kodzie aplikacji, należy unikać stosowania stałej lub udostępnionych poświadczeń. Poświadczeń ani kluczy nie powinny być uwzględniane bezpośrednio w kodzie. Jeśli te poświadczenia są udostępniane, aplikacja musi zostać zaktualizowany i ponownego wdrażania. Lepszym rozwiązaniem jest zapewnienie zasobników własnej tożsamości i sposób uwierzytelnić lub automatycznie pobrać poświadczeń z magazynu cyfrowych.

Następujące [AKS skojarzona projektów typu open source] [ aks-associated-projects] umożliwiają automatyczne uwierzytelnianie zasobników lub żądania poświadczeń i kluczy z magazynu cyfrowych:

* Zarządzane tożsamości dla zasobów platformy Azure i
* Sterownik platformy Azure Key Vault FlexVol

Skojarzone projekty typu open source AKS nie są obsługiwane przez pomoc techniczną systemu Azure. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. Te projekty nie są zalecane do użytku produkcyjnego.

### <a name="use-pod-managed-identities"></a>Zasobnik wykorzystania zarządzanych tożsamości

Tożsamość zarządzaną dla zasobów platformy Azure umożliwia zasobnik uwierzytelniać dla każdej usługi platformy Azure, która obsługuje tę funkcję, takie jak Storage, SQL. Zasobnik jest przypisany, pozwalającą na uwierzytelnianie w usłudze Azure Active Directory i odebrać token cyfrowych tożsamość platformy Azure. Ten token cyfrowego widoczne dla innych usług platformy Azure, które sprawdzają, czy zasobnika ustawiany jest autoryzowany do uzyskania dostępu do usługi i wykonaj wymagane akcje. Tej metody oznacza, że nie wpisów tajnych są wymagane do parametrów połączenia bazy danych, na przykład. Uproszczone przepływu pracy do zasobnika zarządzanych tożsamości pokazano na poniższym diagramie:

![Uproszczone przepływ pracy dotyczący zasobnika zarządzane tożsamością na platformie Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Za pomocą tożsamości zarządzanej kod aplikacji nie musi zawierać poświadczenia dostępu do usługi, takie jak Azure Storage. Jak pod każdym uwierzytelnia się za pomocą jego tożsamość, można więc inspekcji i przegląd dostępu wszystkich użytkowników. Jeśli aplikacja łączy się z innymi usługami platformy Azure, użyj usługi managed tożsamości do ponownego używania poświadczeń limit i ryzyko narażenia na zagrożenia.

Aby uzyskać więcej informacji o tożsamościach zasobników, zobacz [Konfigurowanie klastra usługi AKS w celu używania tożsamości zasobnika zarządzane i ze swoimi aplikacjami][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Usługa Azure Key Vault za pomocą FlexVol

Tożsamości zarządzanej zasobnika sprawdzają się najlepiej do uwierzytelniania względem pomocnicze usługi platformy Azure. Dla własnych usług lub aplikacji bez zarządzanych tożsamości dla zasobów platformy Azure nadal uwierzytelniania za pomocą poświadczeń ani kluczy. Cyfrowy magazynu może służyć do przechowywania tych poświadczeń.

Jeśli aplikacje muszą poświadczeń, komunikują się z magazynem cyfrowej, pobieranie najnowszych poświadczeń, a następnie połącz do wymaganej usługi. Usługa Azure Key Vault może być ten magazyn cyfrowych. Na poniższym diagramie pokazano uproszczonego przepływu pracy do pobierania poświadczeń z usługi Azure Key Vault przy użyciu tożsamości zasobnika zarządzane:

![Uproszczone przepływ pracy na potrzeby pobierania poświadczeń z usługi Key Vault przy użyciu zasobnik tożsamości zarządzanej](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Usługa Key Vault do przechowywania i regularnie Obróć klucze tajne, takie jak poświadczenia, klucze konta magazynu lub certyfikatów. Usługa Azure Key Vault można zintegrować z klastrem usługi AKS przy użyciu FlexVolume. Sterownik FlexVolume umożliwia klastra AKS natywnie pobrać poświadczeń z usługi Key Vault oraz bezpiecznie udostępniać je tylko pod wysyłającego żądanie. Praca z operatorem klastra, aby wdrożyć sterownik Key Vault FlexVol na węzłów AKS. Tożsamość zasobnika zarządzane umożliwia zażądanie dostępu do usługi Key Vault i pobierania poświadczeń, których potrzebujesz, za pośrednictwem sterownika FlexVolume.

Usługa Azure Key Vault przy użyciu FlexVol jest przeznaczony do użytku z aplikacjami i usługami z systemem Linux zasobników i węzłów.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na sposób zabezpieczania zasobników. Aby zaimplementować, niektóre z tych obszarów, zobacz następujące artykuły:

* [Użyj zarządzanych tożsamości dla zasobów platformy Azure za pomocą usługi AKS][aad-pod-identity]
* [Integrowanie usługi Azure Key Vault za pomocą usługi AKS][aks-keyvault-flexvol]

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
