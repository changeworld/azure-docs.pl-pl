---
title: OpenShift w sekcji wymagania wstępne platformy Azure | Dokumentacja firmy Microsoft
description: Wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: f4fd33b250bf1f79610f4363e85b97be87892d78
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449975"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Typowe wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure

W tym artykule opisano typowe wymagania wstępne dotyczące wdrażania, OpenShift Container Platform lub OKD na platformie Azure.

Instalacja platformy OpenShift korzysta z elementów playbook rozwiązania Ansible. Rozwiązanie Ansible używa protokołu Secure Shell (SSH) do łączenia dla wszystkich hostów klastra, aby wykonać kroki instalacji.

Gdy ansible inicjuje połączenia SSH do hostów zdalnych, nie można go wprowadzić hasło. Z tego powodu klucz prywatny nie może mieć (hasło) skojarzone z nim hasło lub wdrożenie zakończy się niepowodzeniem.

Ponieważ maszyny wirtualne (VM) wdrażanie przy użyciu szablonów usługi Azure Resource Manager, sam klucz publiczny jest używany do uzyskiwania dostępu do wszystkich maszyn wirtualnych. Musisz wstawić odpowiedni klucz prywatny do maszyny Wirtualnej, który jest wykonywany wszystkich elementów playbook także. W tym celu bezpiecznie użyjesz usługi Azure key vault do przekazania klucza prywatnego do maszyny Wirtualnej.

W przypadku konieczności skorzystania z magazynu trwałego dla kontenerów woluminów trwałego są wymagane. OpenShift obsługuje Azure wirtualnych dysków twardych (VHD) dla tej funkcji, ale Azure musi być skonfigurowany jako dostawcy chmury.

W tym modelu OpenShift:

- Tworzy obiekt wirtualnego dysku twardego w konto usługi Azure Storage lub dysku zarządzanego.
- Instaluje dysk VHD do maszyny Wirtualnej i sformatowanie woluminu.
- Instaluje woluminu, który ma pod.

W przypadku tej konfiguracji do pracy OpenShift wymaga uprawnień do wykonywania tych zadań na platformie Azure. Można to osiągnąć przy użyciu jednostki usługi. Nazwa główna usługi jest kontem zabezpieczeń w usłudze Azure Active Directory, któremu przyznano uprawnienia do zasobów.

Jednostka usługi musi mieć dostęp do konta magazynu i maszyn wirtualnych, które tworzą klaster. Jeśli wszystkie zasoby klastra OpenShift wdrożyć pojedynczą grupę zasobów, nazwy głównej usługi można udzielić uprawnienia do tej grupy zasobów.

Ten przewodnik opisuje sposób tworzenia artefaktów związanych z wymagań wstępnych.

> [!div class="checklist"]
> * Tworzenie magazynu kluczy, aby zarządzać kluczami SSH dla klastra OpenShift.
> * Tworzenie jednostki usługi do użytku przez dostawcę chmury platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do subskrypcji platformy Azure za pomocą [az login](/cli/azure/reference-index) polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami lub kliknij przycisk **wypróbuj** do użycia w usłudze Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Zalecane jest użycie grupy zasobów dedykowanych do hostowania usługi key vault. Ta grupa jest oddzielony od grupy zasobów, do którego wdrażanie platformy OpenShift zasobów klastra.

Poniższy przykład tworzy grupę zasobów o nazwie *keyvaultrg* w *eastus* lokalizacji:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Tworzenie magazynu kluczy do przechowywania kluczy SSH dla klastra przy użyciu [tworzenie az keyvault](/cli/azure/keyvault) polecenia. Nazwa magazynu kluczy musi być unikatowa w skali globalnej.

Poniższy przykład obejmuje tworzenie usługi key vault o nazwie *keyvault* w *keyvaultrg* grupy zasobów:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Tworzenie klucza SSH 
Klucz SSH jest wymagane do bezpiecznego dostępu do klastra platformy OpenShift. Tworzenie pary kluczy SSH przy użyciu `ssh-keygen` polecenia (w systemie Linux lub macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Pary kluczy SSH nie może zawierać hasła / hasła.

Aby uzyskać więcej informacji na temat kluczy SSH w Windows, zobacz [sposób tworzenia SSH keys na Windows](/azure/virtual-machines/linux/ssh-from-windows). Pamiętaj wyeksportować klucz prywatny w formacie OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Store prywatny klucz SSH w usłudze Azure Key Vault
Wdrażanie platformy OpenShift używa klucza SSH, utworzonego do bezpiecznego dostępu do poziomu głównego OpenShift. Aby umożliwić wdrożenie, bezpieczne pobieranie klucza SSH, należy przechowywać klucz w usłudze Key Vault przy użyciu następującego polecenia:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 
OpenShift komunikuje się z platformą Azure przy użyciu nazwy użytkownika i hasło lub jednostki usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń korzystających z aplikacji, usług i narzędzia automatyzacji, takie jak OpenShift. Ty określasz i zdefiniować uprawnienia, aby operacje nazwy głównej usługi można wykonać na platformie Azure. Zaleca się określić zakres uprawnień jednostki do określonych grup zasobów usługi, a nie całej subskrypcji.

Tworzenie usługi podmiotu zabezpieczeń za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp) i dane wyjściowe poświadczenia, których potrzebuje OpenShift.

Poniższy przykład tworzy usługę podmiotu zabezpieczeń i przypisuje mu uprawnienia współautora do grupy zasobów o nazwie openshiftrg.

Najpierw utwórz grupę zasobów o nazwie openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Tworzenie jednostki usługi:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Jeśli używasz programu Windows, wykonaj ```az group show --name openshiftrg --query id``` i użyć danych wyjściowych zamiast $scope.

Zwróć uwagę na właściwość appId zwróconym w poleceniu:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Pamiętaj utworzyć bezpieczne hasło. Postępuj zgodnie ze wskazówkami zawartymi w [ograniczeniach i regułach dotyczących hasła usługi Azure AD](/azure/active-directory/active-directory-passwords-policy).

Aby uzyskać więcej informacji na temat nazw głównych usług, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano następujące tematy:
> [!div class="checklist"]
> * Tworzenie magazynu kluczy, aby zarządzać kluczami SSH dla klastra OpenShift.
> * Tworzenie jednostki usługi do użytku przez dostawcy rozwiązań w chmurze platformy Azure.

Następnie można wdrożyć klaster usługi OpenShift:

- [Wdrażanie rozwiązania OpenShift Container Platform](./openshift-container-platform.md)
- [Wdrażanie OKD](./openshift-okd.md)
