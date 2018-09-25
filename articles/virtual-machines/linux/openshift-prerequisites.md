---
title: OpenShift w sekcji wymagania wstępne platformy Azure | Dokumentacja firmy Microsoft
description: Wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 36271116d697e5ee6c6ed08d5fdc6063a511e820
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984343"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Typowe wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure

W tym artykule opisano typowe wymagania wstępne dotyczące wdrożenia platformy OpenShift Origin lub OpenShift Container Platform na platformie Azure.

Instalacja platformy OpenShift korzysta z elementów playbook rozwiązania Ansible. Rozwiązanie Ansible używa protokołu Secure Shell (SSH) do łączenia dla wszystkich hostów klastra, aby wykonać kroki instalacji.

Po zainicjowaniu połączenia SSH do zdalnego hostów nie można wprowadzić hasło. Z tego powodu klucz prywatny nie może mieć skojarzone z nim hasło lub wdrożenie zakończy się niepowodzeniem.

Ponieważ maszyny wirtualne (VM) wdrażanie przy użyciu szablonów usługi Azure Resource Manager, sam klucz publiczny jest używany do uzyskiwania dostępu do wszystkich maszyn wirtualnych. Musisz wstawić odpowiedni klucz prywatny do maszyny Wirtualnej, który jest wykonywany wszystkich elementów playbook także. W tym celu bezpiecznie użyjesz usługi Azure key vault do przekazania klucza prywatnego do maszyny Wirtualnej.

W przypadku konieczności skorzystania z magazynu trwałego dla kontenerów woluminów trwałego są wymagane. OpenShift obsługuje Azure wirtualnych dysków twardych (VHD) dla tej funkcji, ale Azure musi być skonfigurowany jako dostawcy chmury. 

W tym modelu OpenShift:

- Tworzy obiekt dysku VHD na koncie usługi Azure Storage.
- Instaluje dysk VHD do maszyny Wirtualnej i formatowanie woluminu.
- Instaluje woluminu, który ma pod.

W przypadku tej konfiguracji do pracy OpenShift wymaga uprawnień do wykonywania poprzednich zadań na platformie Azure. Można to osiągnąć przy użyciu jednostki usługi. Nazwa główna usługi jest kontem zabezpieczeń w usłudze Azure Active Directory, któremu przyznano uprawnienia do zasobów.

Jednostka usługi musi mieć dostęp do konta magazynu i maszyn wirtualnych, które tworzą klaster. Jeśli wszystkie zasoby klastra OpenShift wdrożyć pojedynczą grupę zasobów, nazwy głównej usługi można udzielić uprawnienia do tej grupy zasobów.

Ten przewodnik opisuje sposób tworzenia artefaktów związanych z wymagań wstępnych.

> [!div class="checklist"]
> * Tworzenie magazynu kluczy, aby zarządzać kluczami SSH dla klastra OpenShift.
> * Tworzenie jednostki usługi do użytku przez dostawcy rozwiązań w chmurze platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do subskrypcji platformy Azure za pomocą [az login](/cli/azure/reference-index#az_login) polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami lub kliknij przycisk **wypróbuj** do użycia w usłudze Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Dedykowana grupa zasobów umożliwia hostowanie usługi key vault. Ta grupa jest oddzielony od grupy zasobów, do którego wdrażanie platformy OpenShift zasobów klastra. 

Poniższy przykład tworzy grupę zasobów o nazwie *keyvaultrg* w *eastus* lokalizacji:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Tworzenie magazynu kluczy do przechowywania kluczy SSH dla klastra przy użyciu [tworzenie az keyvault](/cli/azure/keyvault#az_keyvault_create) polecenia. Nazwa magazynu kluczy musi być unikatowa w skali globalnej.

Poniższy przykład obejmuje tworzenie usługi key vault o nazwie *keyvault* w *keyvaultrg* grupy zasobów:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Tworzenie klucza SSH 
Klucz SSH jest wymagane do bezpiecznego dostępu do klastra platformy OpenShift Origin. Tworzenie pary kluczy SSH przy użyciu `ssh-keygen` polecenia (w systemie Linux lub macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Pary kluczy SSH nie może mieć hasła.

Aby uzyskać więcej informacji na temat kluczy SSH w Windows, zobacz [sposób tworzenia SSH keys na Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Store prywatny klucz SSH w usłudze Azure Key Vault
Wdrażanie platformy OpenShift używa klucza SSH, utworzonego do bezpiecznego dostępu do poziomu głównego OpenShift. Aby umożliwić wdrożenie, bezpieczne pobieranie klucza SSH, należy przechowywać klucz w usłudze Key Vault przy użyciu następującego polecenia:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 
OpenShift komunikuje się z platformą Azure przy użyciu nazwy użytkownika i hasło lub jednostki usługi. Jednostka usługi platformy Azure to tożsamość zabezpieczeń korzystających z aplikacji, usług i narzędzia automatyzacji, takie jak OpenShift. Ty określasz i zdefiniować uprawnienia, aby operacje nazwy głównej usługi można wykonać na platformie Azure. Aby poprawić bezpieczeństwo poza tylko podanie nazwy użytkownika i hasła, ten przykład tworzy podstawowa usługa podmiotu zabezpieczeń.

Tworzenie usługi podmiotu zabezpieczeń za pomocą [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i dane wyjściowe poświadczenia, których potrzebuje OpenShift.

Poniższy przykład tworzy usługę podmiotu zabezpieczeń i przypisuje mu uprawnienia współautora do grupy zasobów o nazwie myResourceGroup. Jeśli używasz programu Windows, wykonaj ```az group show --name myResourceGroup --query id``` oddzielnie i użyć danych wyjściowych ze źródłem danych — zakresy opcji.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

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

- [Wdrażanie platformy OpenShift Origin](./openshift-origin.md)
- [Wdrażanie rozwiązania OpenShift Container Platform](./openshift-container-platform.md)

