---
title: Samouczek — konfigurowania ról kontroli dostępu opartej na rolach w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania Ansible | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania funkcji RBAC w klastrze usługi Azure Kubernetes Service(AKS) za pomocą rozwiązania Ansible
keywords: ansible, azure, metodyki devops, bash, cloudshell, element playbook, aks, kontener, aks, kubernetes, usługi azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: cf2bd694e126acb61ea31e71ff3fd9d15a6db9ef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656566"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowania ról kontroli dostępu opartej na rolach w usłudze Azure Kubernetes Service (AKS) za pomocą rozwiązania Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS, może być skonfigurowana do używania [usługi Azure Active Directory (AD)](/azure/active-directory/) do uwierzytelniania użytkowników. Po skonfigurowaniu używasz token uwierzytelniania usługi Azure AD do logowania do klastra usługi AKS. RBAC mogą być oparte na tożsamości użytkownika lub członkostwa w grupie katalogu.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS z obsługą usługi AD systemu Azure
> * Skonfiguruj rolę RBAC w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Zainstaluj bibliotekę firmę Red Hat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurowanie usługi Azure AD do uwierzytelniania w usłudze AKS

Podczas konfigurowania usługi Azure AD do uwierzytelniania usługi AKS, są skonfigurowane dwie aplikacje usługi Azure AD. Musi być zakończona przez administratora dzierżawy usługi Azure. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure Active Directory za pomocą usługi AKS](/azure/aks/aad-integration#create-the-server-application). 

Od administratora dzierżawy usługi Azure Pobierz następujące wartości:

- Klucz tajny aplikacji serwera
- Identyfikator aplikacji serwera
- Identyfikator aplikacji klienta 
- Identyfikator dzierżawy

Te wartości są wymagane, można uruchomić elementu playbook próbki.  

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W tej sekcji utworzysz usłudze AKS za pomocą [aplikacji usługi Azure AD](#configure-azure-ad-for-aks-authentication).

Poniżej przedstawiono ważne uwagi należy wziąć pod uwagę podczas pracy z elementu playbook próbki:

- Element playbook ładuje `ssh_key` z `~/.ssh/id_rsa.pub`. Jeśli zmodyfikujesz go tak, użyj formacie jednowierszowym — począwszy od "ssh-rsa" (bez cudzysłowu).
- `client_id` i `client_secret` wartości są ładowane z `~/.azure/credentials`, która jest domyślny plik poświadczeń. Możesz ustawić te wartości z usługą główną lub załadować te wartości ze zmiennych środowiskowych:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Zapisz następujący podręcznik jako `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Pobierz identyfikator obiektu usługi Azure AD

Aby utworzyć powiązanie RBAC, należy najpierw uzyskać identyfikator obiektów usługi Azure AD. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. W polu wyszukiwania w górnej części strony, wprowadź `Azure Active Directory`. 

1. Kliknij pozycję `Enter` (Dalej).

1. W **Zarządzaj** menu, wybierz opcję **użytkowników**.

1. W polu Nazwa wyszukiwania dla swojego konta.

1. W **nazwa** kolumnę, wybierz łącze do Twojego konta.

1. W **tożsamości** sekcji, skopiuj **obiektu o identyfikatorze**.

    ![Skopiuj identyfikator obiektu usługi Azure AD](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Utwórz powiązanie RBAC

W tej sekcji utworzysz powiązania roli lub powiązania roli klastra w usłudze AKS. 

Zapisz następujący podręcznik jako `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Zastąp `&lt;your-aad-account>` symbolu zastępczego z dzierżawą usługi Azure AD [obiektu o identyfikatorze](#get-the-azure-ad-object-id).

Zapisz poniższy element playbook — który wdraża Twojej nowej roli usługi AKS — jako `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Uruchamianie elementu playbook próbki

W tej sekcji przedstawiono pełny przykład elementu playbook, który wywołuje zadania tworzenia w tym artykule. 

Zapisz następujący podręcznik jako `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

W `vars` sekcji, zastąp symbole zastępcze następujących informacji z usługi Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Uruchamianie przy użyciu elementu playbook pełną `ansible-playbook` polecenia:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Sprawdź wyniki

W tej sekcji możesz użyć narzędzia kubectl węzłów, w tym artykule.

Wprowadź następujące polecenie w wierszu terminalu:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Polecenie nastąpi bezpośrednie przekierowanie do strony uwierzytelniania. Zaloguj się przy użyciu konta platformy Azure.

Po uwierzytelnieniu kubectl zawiera listę węzłów w podobny sposób następujący format:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz poniższy kod jako `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Uruchamianie elementu playbook, przy użyciu `ansible-playbook` polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)