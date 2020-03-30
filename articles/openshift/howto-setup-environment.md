---
title: Konfigurowanie środowiska programistycznego Azure Red Hat OpenShift
description: Poniżej przedstawiono wymagania wstępne dotyczące pracy z programem Microsoft Azure Red Hat OpenShift.
keywords: konfiguracja openshift czerwonego kapelusza
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477038"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurowanie środowiska deweloperskiego usługi Azure Red Hat OpenShift

Aby tworzyć i uruchamiać aplikacje OpenShift red hat platformy Microsoft Azure, musisz:

* Zainstaluj wersję 2.0.65 (lub nowszą) interfejsu wiersza polecenia platformy Azure (lub użyj usługi Azure Cloud Shell).
* Zarejestruj się `AROGA` dla dostawców funkcji i skojarzonych zasobów.
* Tworzenie dzierżawy usługi Azure Active Directory (Azure AD).
* Tworzenie obiektu aplikacji usługi Azure AD.
* Utwórz użytkownika usługi Azure AD.

Poniższe instrukcje poprą cię przez wszystkie te wymagania wstępne.

## <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Usługa Azure Red Hat OpenShift wymaga wersji 2.0.65 lub nowszej interfejsu wiersza polecenia platformy Azure. Jeśli masz już zainstalowany wiersz polecenia platformy Azure, możesz sprawdzić, którą wersję masz, uruchamiając:

```azurecli
az --version
```

Pierwszy wiersz wyjścia będzie miał wersję CLI, na przykład `azure-cli (2.0.65)`.

Poniżej przedstawiono instrukcje [dotyczące instalowania interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) jeśli potrzebujesz nowej instalacji lub uaktualnienia.

Alternatywnie można użyć [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Korzystając z usługi Azure Cloud Shell, należy wybrać środowisko **Bash,** jeśli planujesz wykonać wraz z [tworzenie i zarządzanie serii samouczków klastra Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Rejestrowanie dostawców i funkcji

Funkcja `Microsoft.ContainerService AROGA` , `Microsoft.Solutions` `Microsoft.Compute`, `Microsoft.Storage` `Microsoft.KeyVault` i `Microsoft.Network` dostawców muszą być zarejestrowane w subskrypcji ręcznie przed wdrożeniem pierwszego klastra Azure Red Hat OpenShift.

Aby zarejestrować tych dostawców i funkcje ręcznie, użyj następujących instrukcji z powłoki Bash, jeśli zainstalowano interfejsu wiersza polecenia lub z sesji usługi Azure Cloud Shell (Bash) w witrynie Azure portal:

1. Jeśli masz wiele subskrypcji platformy Azure, określ odpowiedni identyfikator subskrypcji:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zarejestruj funkcję AROGA usługi Microsoft.ContainerService:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Zarejestruj dostawcę magazynu Microsoft.Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Zarejestruj dostawcę microsoft.compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Zarejestruj dostawcę microsoft.solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Zarejestruj dostawcę microsoft.network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Zarejestruj dostawcę usługi Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Odśwież rejestrację dostawcy zasobów microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Tworzenie dzierżawy usługi Azure Active Directory (Azure AD)

Usługa Azure Red Hat OpenShift wymaga skojarzonej dzierżawy usługi Azure Active Directory (Azure AD), która reprezentuje twoją organizację i jej relację z firmą Microsoft. Dzierżawa usługi Azure AD umożliwia rejestrowanie, tworzenie i zarządzanie aplikacjami, a także korzystanie z innych usług platformy Azure.

Jeśli nie masz usługi Azure AD do użycia jako dzierżawy dla klastra Azure Red Hat OpenShift lub chcesz utworzyć dzierżawę do testowania, postępuj zgodnie z instrukcjami w [Tworzenie dzierżawy usługi Azure AD dla klastra Azure Red Hat OpenShift](howto-create-tenant.md) przed kontynuowaniem tego przewodnika.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Tworzenie użytkownika, grupy zabezpieczeń i obiektu aplikacji usługi Azure AD

Usługa Azure Red Hat OpenShift wymaga uprawnień do wykonywania zadań w klastrze, takich jak konfigurowanie magazynu. Te uprawnienia są reprezentowane za pośrednictwem [jednostki usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Należy również utworzyć nowego użytkownika usługi Active Directory do testowania aplikacji uruchomionych w klastrze Azure Red Hat OpenShift.

Postępuj zgodnie z instrukcjami w [Tworzenie obiektu aplikacji usługi Azure AD i użytkownika,](howto-aad-app-configuration.md) aby utworzyć jednostkę usługi, wygenerować adres URL wywołania zwrotnego klienta i uwierzytelniania dla aplikacji i utworzyć nową grupę zabezpieczeń usługi Azure AD i użytkownika, aby uzyskać dostęp do klastra.

## <a name="next-steps"></a>Następne kroki

Teraz możesz korzystać z usługi Azure Red Hat OpenShift!

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
