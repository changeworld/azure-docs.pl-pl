---
title: Tworzenie rozwiązania Cloud Foundry na platformie Azure
description: Dowiedz się, jak skonfigurować parametry wymagane do aprowizowania klastra rozwiązania Cloud Foundry PCF
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250623"
---
# <a name="create-cloud-foundry-on-azure"></a>Tworzenie rozwiązania Cloud Foundry na platformie Azure

W tym samouczku opisano szybkie kroki dotyczące tworzenia i generowania parametrów potrzebnych do aprowizowania klastra rozwiązania Pivotal Cloud Foundry (PCF) na platformie Azure.  Rozwiązanie Pivotal Cloud Foundry można znaleźć, wykonując wyszukiwanie w witrynie Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Alternatywny tekst obrazu](media/deploy/pcf-marketplace.png "Wyszukiwanie rozwiązania Pivotal Cloud Foundry na platformie Azure")


## <a name="generate-an-ssh-public-key"></a>Generowanie klucza publicznego SSH

Istnieje kilka sposobów generowania klucza publicznego SSH przy użyciu systemu Windows, komputera Mac lub systemu Linux.

```Bash
ssh-keygen -t rsa -b 2048
```
- Kliknij tutaj, aby zobaczyć [instrukcje]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) dla swojego środowiska.

## <a name="create-a-service-principal"></a>Tworzenie jednostki usługi

> [!NOTE]
>
> Tworzenie jednostki usługi wymaga uprawnień właściciela konta.  Ponadto możesz napisać skrypt w celu zautomatyzowania tworzenia jednostki usługi. Na przykład za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Zaloguj się do konta platformy Azure.

    `az login`

    ![Alternatywny tekst obrazu](media/deploy/az-login-output.png "Logowanie interfejsu wiersza polecenia platformy Azure")
 
    Skopiuj wartość „id” jako swój **identyfikator subskrypcji** oraz wartość **tenantId**, której użyjesz później.

2. Ustaw subskrypcję domyślną dla tej konfiguracji.

    `az account set -s {id}`

3. Utwórz aplikację usługi AAD dla rozwiązania PCF i określ unikatowe alfanumeryczne hasło.  Zapisz hasło jako element **clientSecret** do późniejszego użycia.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Skopiuj wartość„appId” w danych wyjściowych jako identyfikator **ClientID** do późniejszego użycia.

    > [!NOTE]
    >
    > Wybierz identyfikator URI oraz stronę główną swojej aplikacji,  na przykład http://www.contoso.com.

4. Utwórz jednostkę usługi przy użyciu nowego identyfikatora „appId”.

    `az ad sp create --id {appId}`

5. Ustaw rolę uprawnień jednostki usługi jako **Współautor**.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Lub możesz również użyć…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Alternatywny tekst obrazu](media/deploy/svc-princ.png "Przypisywanie roli jednostki usługi")

6. Upewnij się, że możesz pomyślnie zalogować się do jednostki usługi przy użyciu identyfikatora appId, hasła oraz identyfikatora dzierżawy.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Utwórz plik JSON w następującym formacie przy użyciu wszystkich powyższych wartości **identyfikatora subskrypcji** oraz elementów **tenantId**, **clientID** i **clientSecret** skopiowanych wcześniej.  Zapisz plik.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Pobierz token usługi Pivotal Network

1. Zarejestruj się lub zaloguj się na swoje konto usługi [Pivotal Network](https://network.pivotal.io).
2. Kliknij nazwę profilu w prawej górnej części strony i wybierz pozycję „Edit Profile” (Edytuj profil).
3. Przewiń do dołu strony i skopiuj wartość **LEGACY API TOKEN**.  To jest wartość **tokenu usługi Pivotal Network**, która zostanie użyta później.

## <a name="provision-your-cloud-foundry-on-azure"></a>Aprowizowanie usługi Cloud Foundry na platformie Azure

1. Masz teraz wszystkie parametry potrzebne do zaaprowizowania klastra rozwiązania [Pivotal Clound Foundry na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
2. Wprowadź parametry, a następnie utwórz klaster PCF.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Weryfikowanie wdrożenia i logowanie się w usłudze Pivotal Ops Manager

1. W klastrze PCF powinien być wyświetlany stan wdrożenia.

    ![Alternatywny tekst obrazu](media/deploy/deployment.png "Stan wdrożenia platformy Azure")

2. Kliknij link **Deployments** (Wdrożenia) w obszarze nawigacji po lewej stronie, aby uzyskać poświadczenia dla usługi PCF Ops Manager, a następnie kliknij pozycję **Deployment Name** (Nazwa wdrożenia) na następnej stronie.
3. W obszarze nawigacyjnym po lewej stronie kliknij link **Outputs** (Dane wyjściowe), aby wyświetlić adres URL, nazwę użytkownika i hasło dla usługi PCF Ops Manager.  Wartość „OPSMAN FQDN” jest adresem URL.
 
    ![Alternatywny tekst obrazu](media/deploy/deploy-outputs.png "Dane wyjściowe wdrożenia rozwiązania Cloud Foundry")
 
4. Przejdź do adresu URL w przeglądarce internetowej i wprowadź poświadczenia z poprzedniego kroku, aby się zalogować.

    ![Alternatywny tekst obrazu](media/deploy/pivotal-login.png "Strona logowania rozwiązania Pivotal")
         
    > [!NOTE]
    >
    > Jeśli w przeglądarce Internet Explorer zostanie wyświetlony błąd mówiący, że witryna nie jest bezpieczna, kliknij pozycję „Więcej informacji” i „Przejdź do strony internetowej”.  W przeglądarce Firefox kliknij pozycję „Zaawansowane” i dodaj certyfikację, aby kontynuować.

5. W usłudze PCF Ops Manager powinny zostać wyświetlone wdrożone wystąpienia platformy Azure. Możesz teraz rozpocząć wdrażanie aplikacji i zarządzanie nimi!
               
    ![Alternatywny tekst obrazu](media/deploy/ops-mgr.png "Wdrożone wystąpienie platformy Azure w rozwiązaniu Pivotal")
 
