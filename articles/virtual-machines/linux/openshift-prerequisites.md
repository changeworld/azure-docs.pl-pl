---
title: OpenShift w wymagania wstępne platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 178f4d6dab5c87c9c53ab87c4a3b1edcfa61c221
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847128"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Typowe wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure

W tym artykule opisano typowe wymagania wstępne dotyczące wdrażania pochodzenia OpenShift lub OpenShift platformy kontenera na platformie Azure.

Instalacja programu OpenShift używa Ansible playbooks. Ansible używa protokołu Secure Shell (SSH) do łączenia dla wszystkich hostów klastra, wykonaj kroki instalacji.

Po zainicjowaniu połączenia SSH hostom zdalnym nie można wprowadzić hasło. Z tego powodu klucza prywatnego nie może mieć skojarzone z nim hasło lub wdrożenie zakończy się niepowodzeniem.

Ponieważ wdrażanie maszyn wirtualnych (VM) za pomocą szablonów usługi Azure Resource Manager, sam klucz publiczny jest używany do uzyskiwania dostępu do wszystkich maszyn wirtualnych. Należy wprowadzić odpowiedni klucz prywatny do maszyny Wirtualnej, która wykonuje wszystkie playbooks również. Aby bezpiecznie to zrobić, należy użyć usługi Azure key vault do przekazania klucza prywatnego do maszyny Wirtualnej.

Jeśli istnieje potrzeba magazynu trwałego dla kontenerów, trwałe woluminy są wymagane. OpenShift obsługuje Azure wirtualnych dysków twardych (VHD) dla tej funkcji, ale Azure musi być skonfigurowany jako dostawcy chmury. 

W tym modelu OpenShift:

- Tworzy obiekt wirtualnego dysku twardego na koncie magazynu Azure.
- Instaluje dysk VHD do maszyny Wirtualnej i formatowanie woluminu.
- Instaluje woluminu pod.

W przypadku tej konfiguracji do pracy OpenShift wymaga uprawnień do wykonania poprzednich zadań na platformie Azure. Można to osiągnąć z nazwy głównej usługi. Nazwy głównej usługi jest kontem zabezpieczeń w usłudze Azure Active Directory, któremu przyznano uprawnienia do zasobów.

Nazwy głównej usługi musi mieć dostęp do konta magazynu i maszyny wirtualne wchodzące w skład klastra. Jeśli wszystkie zasoby klastra OpenShift wdrożyć pojedyncza grupa zasobów, nazwę główną usługi można udzielać uprawnień do tej grupy zasobów.

Ten przewodnik zawiera opis sposobu tworzenia artefaktów związanych z wymagań wstępnych.

> [!div class="checklist"]
> * Tworzenie magazynu kluczy do zarządzania kluczy SSH dla klastra OpenShift.
> * Tworzenie nazwy głównej usługi do użytku przez usługi Azure Cloud Solution Provider.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się do Twojej subskrypcji platformy Azure z [logowania az](/cli/azure/reference-index#az_login) polecenia i wykonaj na ekranie wskazówek, lub kliknij przycisk **wypróbuj** użycia chmury powłoki.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupa zasobów dedykowanych służy do obsługi magazynu kluczy. Ta grupa jest oddzielony od grupy zasobów, do którego wdrażanie OpenShift zasobów klastra. 

Poniższy przykład tworzy grupę zasobów o nazwie *keyvaultrg* w *eastus* lokalizacji:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Tworzenie magazynu kluczy w celu przechowywania kluczy SSH dla klastra z [az keyvault utworzyć](/cli/azure/keyvault#az_keyvault_create) polecenia. Nazwa magazynu kluczy musi być globalnie unikatowa.

Poniższy przykład tworzy magazyn kluczy o nazwie *keyvault* w *keyvaultrg* grupy zasobów:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Tworzenie klucza SSH 
Klucz SSH jest wymagany do bezpiecznego dostępu do klastra OpenShift pochodzenia. Utwórz parę kluczy SSH, używając `ssh-keygen` polecenia (w systemie Linux lub macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Twoje pary kluczy SSH nie może mieć hasłem.

Aby uzyskać więcej informacji dotyczących kluczy SSH w systemie Windows, zobacz [tworzenie SSH kluczy w systemie Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Przechowywanie klucza prywatnego SSH w usłudze Azure Key Vault
Wdrożenie OpenShift używa klucza SSH, utworzenia bezpiecznego dostępu do głównego OpenShift. Aby włączyć wdrożenie w celu bezpiecznego pobierania klucza SSH, należy przechowywać klucz w Key Vault za pomocą następującego polecenia:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 
OpenShift komunikuje się z platformą Azure za pomocą nazwy użytkownika i hasła lub nazwy głównej usługi. Podmiot zabezpieczeń usługi Azure jest tożsamość zabezpieczeń korzystających z aplikacji, usługami i automatyzacja takich narzędzi jak OpenShift. Kontrolowanie i definiowanie uprawnień, aby operacje nazwy głównej usługi można wykonać na platformie Azure. Aby poprawić bezpieczeństwo poza tylko podanie nazwy użytkownika i hasła, w tym przykładzie tworzy podstawowej usługi podmiotu zabezpieczeń.

Utwórz usługę podmiotu zabezpieczeń z [az ad sp utworzyć do rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) i poświadczenia, które wymaga OpenShift output.

Poniższy przykład tworzy usługę podmiotu zabezpieczeń i przypisuje go uprawnienia współautora do grupy zasobów o nazwie myResourceGroup. Jeśli używasz systemu Windows, należy wykonać ```az group show --name myResourceGroup --query id``` oddzielnie i użyć danych wyjściowych ze źródłem opcja zakresów.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Zwróć uwagę na właściwość appId zwrócił polecenia:
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

Aby uzyskać więcej informacji dotyczących nazwy główne usług, zobacz [zapisać nazwy głównej usługi platformy Azure z Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł obejmuje następujące tematy:
> [!div class="checklist"]
> * Tworzenie magazynu kluczy do zarządzania kluczy SSH dla klastra OpenShift.
> * Tworzenie nazwy głównej usługi do użytku przez usługi Azure Cloud Solution Provider.

Następnie można wdrożyć klaster OpenShift:

- [Wdrażanie OpenShift źródła](./openshift-origin.md)
- [Wdrażanie OpenShift kontenera platformy](./openshift-container-platform.md)

