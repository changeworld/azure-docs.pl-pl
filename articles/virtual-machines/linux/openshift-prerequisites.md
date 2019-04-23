---
title: OpenShift w sekcji wymagania wstępne platformy Azure | Dokumentacja firmy Microsoft
description: Wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: d8a9b82e51c837af6343ddf851545d02299aa527
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001653"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Typowe wymagania wstępne dotyczące wdrażania OpenShift na platformie Azure

W tym artykule opisano typowe wymagania wstępne dotyczące wdrażania, OpenShift Container Platform lub OKD na platformie Azure.

Instalacja platformy OpenShift korzysta z elementów playbook rozwiązania Ansible. Rozwiązanie Ansible używa protokołu Secure Shell (SSH) do łączenia dla wszystkich hostów klastra, aby wykonać kroki instalacji.

Po rozwiązaniu ansible sprawia, że połączenia SSH do hostów zdalnych, nie można go wprowadzić hasło. Z tego powodu klucz prywatny nie może mieć (hasło) skojarzone z nim hasło lub wdrożenie zakończy się niepowodzeniem.

Ponieważ maszyny wirtualne (VM) wdrażanie przy użyciu szablonów usługi Azure Resource Manager, sam klucz publiczny jest używany do uzyskiwania dostępu do wszystkich maszyn wirtualnych. Odpowiedni klucz prywatny musi być na maszynie Wirtualnej, który jest wykonywany wszystkich elementów playbook także. Aby bezpiecznie wykonać tę akcję, usługi Azure key vault jest używany do przekazania klucza prywatnego do maszyny Wirtualnej.

W przypadku konieczności skorzystania z magazynu trwałego dla kontenerów woluminów trwałego są wymagane. OpenShift obsługuje Azure wirtualnych dysków twardych (VHD) dla trwałego woluminów, ale Azure musi być skonfigurowany jako dostawcy chmury.

W tym modelu OpenShift:

- Tworzy obiekt wirtualnego dysku twardego w konto magazynu platformy Azure lub dysku zarządzanego.
- Instaluje dysk VHD do maszyny Wirtualnej i sformatowanie woluminu.
- Instaluje woluminu, który ma pod.

W przypadku tej konfiguracji do pracy OpenShift wymaga uprawnień do wykonywania tych zadań na platformie Azure. Jednostka usługi jest używana w tym celu. Nazwa główna usługi jest kontem zabezpieczeń w usłudze Azure Active Directory, któremu przyznano uprawnienia do zasobów.

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

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Dedykowana grupa zasobów należy używać do hostowania usługi key vault. Ta grupa jest oddzielony od grupy zasobów, do którego wdrażanie platformy OpenShift zasobów klastra.

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

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Wymagania wstępne dotyczy tylko szablonu usługi Resource Manager

Hasła będą musieli można utworzyć klucza prywatnego SSH (**sshPrivateKey**), klucz tajny klienta usługi Azure AD (**aadClientSecret**), hasło administratora OpenShift (**openshiftPassword** ) i Red Hat subskrypcji Menedżera haseł lub aktywacji klucza (**rhsmPasswordOrActivationKey**).  Ponadto, jeśli używane są niestandardowe certyfikaty SSL, następnie sześć dodatkowe wpisy tajne będzie muszą zostać utworzone - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**, i **masterkeyfile**.  Te parametry zostaną wyjaśnione bardziej szczegółowo.

Szablon odwołuje się do określonych nazw kluczy tajnych więc możesz **musi** Użyj nazwy pogrubiony wymienione powyżej (z uwzględnieniem wielkości liter).

### <a name="custom-certificates"></a>Certyfikaty niestandardowe

Domyślnie szablon umożliwia wdrożenie klastra usługi OpenShift przy użyciu certyfikatów z podpisem własnym dla konsoli sieci web platformy OpenShift i domeny routingu. Jeśli chcesz używać niestandardowych certyfikatów protokołu SSL, ustaw "routingCertType" do "custom" i "masterCertType" do "custom".  Będą potrzebne dla certyfikatów urzędu certyfikacji, certyfikatu i klucza pliki w formacie PEM.  Jest możliwe użycie niestandardowych certyfikatów dla jednego, ale nie drugiej.

Należy zapisać te pliki w wpisy tajne usługi Key Vault.  Użyj tej samej usługi Key Vault, jak użyć klucza prywatnego.  Zamiast wymagać 6 dodatkowe dane wejściowe dla nazw kluczy tajnych, szablon jest ustalony używać określonych nazw kluczy tajnych dla każdego z plików certyfikatów SSL.  Store danych certyfikatu, korzystając z informacji w poniższej tabeli.

| Nazwa wpisu tajnego      | Plik certyfikatu   |
|------------------|--------------------|
| mastercafile     | plik główny urząd certyfikacji     |
| mastercertfile   | pliku certyfikatu głównego   |
| masterkeyfile    | Plik klucza głównego    |
| routingcafile    | Plik routingu urzędu certyfikacji    |
| routingcertfile  | Plik routingu certyfikat  |
| routingkeyfile   | Plik klucza routingu   |

Utwórz wpisy tajne przy użyciu wiersza polecenia platformy Azure. Poniżej znajduje się przykład.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano następujące tematy:
> [!div class="checklist"]
> * Tworzenie magazynu kluczy, aby zarządzać kluczami SSH dla klastra OpenShift.
> * Tworzenie jednostki usługi do użytku przez dostawcy rozwiązań w chmurze platformy Azure.

Następnie można wdrożyć klaster usługi OpenShift:

- [Wdrażanie rozwiązania OpenShift Container Platform](./openshift-container-platform.md)
- [Wdrażanie oferty Samozarządzanej portalu Marketplace platformy kontenera OpenShift](./openshift-marketplace-self-managed.md)