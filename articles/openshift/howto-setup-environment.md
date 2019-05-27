---
title: Konfigurowanie środowiska projektowego Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono wymagania wstępne dotyczące pracy z Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: Red hat openshift Konfiguracja
author: TylerMSFT
ms.author: twhitney
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 6ba7e67620ebfd7ff98c1ba81c7cecb133288be7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962126"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurowanie środowiska deweloperskiego usługi Azure Red Hat OpenShift

Aby skompilować i uruchomić aplikacje Microsoft Azure Red Hat OpenShift, musisz:

* Kup wystąpienia zarezerwowane maszyn wirtualnych platformy Azure.
* Zainstaluj wersję 2.0.65 (lub nowszej) z wiersza polecenia platformy Azure (lub użyj usługi Azure Cloud Shell).
* Zarejestruj się, aby `openshiftmanagedcluster` funkcji i dostawcy skojarzonego zasobu.
* Tworzenie dzierżawy usługi Azure Active Directory (Azure AD).
* Utwórz obiekt aplikacji usługi Azure AD.
* Utwórz użytkownika usługi Azure AD.

Zgodnie z poniższymi instrukcjami przeprowadzi użytkownika przez wszystkie te wymagania wstępne.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Kup zarezerwowane wystąpienia węzłów aplikacji Azure Red Hat OpenShift

Zanim użyjesz usługi Azure Red Hat OpenShift, należy kupić co najmniej 4 węzły aplikacji Azure Red Hat OpenShift zarezerwowane, po upływie których będzie można się inicjowanie obsługi klastrów.

Jeśli korzystasz z platformy Azure, [Kup wystąpienia zarezerwowane program Azure Red Hat OpenShift](https://aka.ms/openshift/buy) za pośrednictwem witryny Azure portal. Po zakupie, Twoja subskrypcja zostanie uaktywniona w ciągu 24 godzin.

Jeśli nie jesteś klientem Azure [kontakt ze sprzedażą](https://aka.ms/openshift/contact-sales) i wypełnij formularz sprzedaży w dolnej części strony Aby rozpocząć proces.

Zapoznaj się [Azure Red Hat OpenShift stronę z cennikiem](https://aka.ms/openshift/pricing) Aby uzyskać więcej informacji.

## <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Azure Red Hat OpenShift wymaga wersji 2.0.65 lub nowszej interfejsu wiersza polecenia platformy Azure. Jeśli masz już zainstalowaną wiersza polecenia platformy Azure, możesz sprawdzić posiadaną wersję, uruchamiając:

```bash
az --version
```

Pierwszy wiersz danych wyjściowych będzie mieć wersję interfejsu wiersza polecenia, na przykład `azure-cli (2.0.65)`.

Poniżej przedstawiono instrukcje dotyczące [instalowanie interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) w razie potrzeby nowych instalacji lub uaktualnienia.

Alternatywnie możesz użyć [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Korzystając z usługi Azure Cloud Shell, pamiętaj o wybraniu **Bash** środowiska, jeśli planujesz postępuj zgodnie ze szczegółowymi [tworzenie i Zarządzanie klastrem usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) serii samouczków.

## <a name="register-providers-and-features"></a>Zarejestruj dostawców i funkcje

`Microsoft.ContainerService openshiftmanagedcluster` Funkcji `Microsoft.Solutions`, i `Microsoft.Network` dostawcy musi być zarejestrowana do subskrypcji ręcznie, przed wdrożeniem swój pierwszy klaster Azure Red Hat OpenShift.

Aby ręcznie zarejestrować tych dostawców i funkcje, użyj poniższych instrukcji z poziomu powłoki Bash, jeśli po zainstalowaniu interfejsu wiersza polecenia lub z sesji usługi Azure Cloud Shell (powłoka Bash), w portalu Azure:

1. Jeśli masz wiele subskrypcji platformy Azure, należy określić identyfikator subskrypcji istotne:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Zarejestruj funkcję openshiftmanagedcluster Microsoft.ContainerService:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Zarejestruj dostawcę Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Zarejestruj dostawcę Microsoft.Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Zarejestruj dostawcę Microsoft.KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

6. Odśwież rejestracji dostawcy zasobów Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Tworzenie dzierżawy usługi Azure Active Directory (Azure AD)

Usługa Azure Red Hat OpenShift wymaga skojarzonych dzierżawę usługi Azure Active Directory (Azure AD), reprezentujący Twojej organizacji i jej zależności do firmy Microsoft. Dzierżawy usługi Azure AD umożliwia rejestrowanie, kompilacja i zarządzanie aplikacjami, a także innych usług platformy Azure.

Jeśli nie masz usługi Azure AD do użycia jako dzierżawy dla klastra usługi Azure Red Hat OpenShift lub chcesz utworzyć dzierżawę do testowania, postępuj zgodnie z instrukcjami [utworzyć dzierżawę usługi Azure AD dla klastra usługi Azure Red Hat OpenShift](howto-create-tenant.md) przed Kontynuowanie z tego przewodnika.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Utwórz użytkownika usługi Azure AD, grupy zabezpieczeń i aplikacji obiektu

Azure Red Hat OpenShift musi mieć uprawnienia do wykonywania zadań w klastrze, takich jak Konfigurowanie magazynu. Te uprawnienia są reprezentowane przez [nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Należy także utworzyć nowego użytkownika usługi Active Directory do testowania aplikacji działających w klastrze Azure Red Hat OpenShift.

Postępuj zgodnie z instrukcjami w [utworzyć obiekt aplikacji usługi Azure AD i użytkownika](howto-aad-app-configuration.md) Aby utworzyć nazwę główną usługi, generowanie klienta wpisu tajnego i uwierzytelniania adres URL wywołania zwrotnego dla aplikacji i Utwórz nową grupę zabezpieczeń usługi Azure AD i użytkownika, aby uzyskać dostęp do klaster.

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz używać usługi Azure Red Hat OpenShift!

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
