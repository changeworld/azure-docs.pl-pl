---
title: Samouczek — Konfigurowanie ról kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Kubernetes Service (AKS) przy użyciu rozwiązania ansible
description: Dowiedz się, jak używać rozwiązania ansible do konfigurowania RBAC w klastrze usługi Azure Kubernetes Service (AKS)
keywords: rozwiązania ansible, Azure, DevOps, bash, cloudshell, element PlayBook, AKS, kontener, AKS, Kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1be123eb06bd2679169478daf27a7148d2a8b055
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156875"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie ról kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Kubernetes Service (AKS) przy użyciu rozwiązania ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS można skonfigurować do korzystania z [Azure Active Directory (AD)](/azure/active-directory/) do uwierzytelniania użytkowników. Po skonfigurowaniu programu Użyj tokenu uwierzytelniania usługi Azure AD, aby zalogować się do klastra AKS. Kontrola RBAC może być oparta na tożsamości użytkownika lub członkostwie w grupie katalogów.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS z obsługą usługi Azure AD
> * Konfigurowanie roli RBAC w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Zainstaluj - biblioteki OpenShift RedHat** `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurowanie uwierzytelniania usługi Azure AD for AKS

Podczas konfigurowania uwierzytelniania usługi Azure AD for AKS są konfigurowane dwie aplikacje usługi Azure AD. Ta operacja musi zostać wykonana przez administratora dzierżawy platformy Azure. Aby uzyskać więcej informacji, zobacz [integrowanie Azure Active Directory z AKS](/azure/aks/aad-integration#create-the-server-application). 

Z poziomu administratora dzierżawy platformy Azure Uzyskaj następujące wartości:

- Klucz tajny aplikacji serwera
- Identyfikator aplikacji serwera
- Identyfikator aplikacji klienta 
- Identyfikator dzierżawy

Te wartości są konieczne do uruchomienia przykładowej element PlayBook.  

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W tej sekcji utworzysz AKS z [aplikacją usługi Azure AD](#configure-azure-ad-for-aks-authentication).

Poniżej przedstawiono niektóre kluczowe uwagi, które należy wziąć pod uwagę podczas pracy z przykładową element PlayBook:

- Element PlayBook ładuje `ssh_key` z `~/.ssh/id_rsa.pub`. Jeśli zmodyfikujesz go, użyj formatu jednowierszowego — rozpoczynając od "SSH-RSA" (bez cudzysłowów).
- Wartości `client_id` i `client_secret` są ładowane z `~/.azure/credentials`, czyli domyślnego pliku poświadczeń. Można ustawić te wartości jako nazwę główną usługi lub załadować te wartości ze zmiennych środowiskowych:

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
  azure_rm_aksversion_facts:
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

## <a name="get-the-azure-ad-object-id"></a>Pobieranie identyfikatora obiektu usługi Azure AD

Aby utworzyć powiązanie RBAC, należy najpierw uzyskać identyfikator obiektu usługi Azure AD. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. W polu wyszukiwania w górnej części strony wprowadź `Azure Active Directory`. 

1. Kliknij pozycję `Enter` (Dalej).

1. W menu **Zarządzaj** wybierz pozycję **Użytkownicy**.

1. W polu Nazwa Wyszukaj swoje konto.

1. W kolumnie **Nazwa** wybierz link do swojego konta.

1. W sekcji **tożsamość** Skopiuj **Identyfikator obiektu**.

    ![Skopiuj identyfikator obiektu usługi Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Tworzenie powiązania RBAC

W tej sekcji utworzysz powiązanie roli lub powiązanie roli klastra w AKS. 

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

Zastąp symbol zastępczy `&lt;your-aad-account>` [identyfikatorem obiektu](#get-the-azure-ad-object-id)dzierżawy usługi Azure AD.

Zapisz następujący element PlayBook — który wdraża nową rolę do AKS — jako `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowej element PlayBook

W tej sekcji znajduje się kompletna Przykładowa element PlayBook, która wywołuje zadania utworzone w tym artykule. 

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

W sekcji `vars` Zastąp następujące symbole zastępcze informacjami o usłudze Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Uruchom kompletną element PlayBook za pomocą polecenia `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Weryfikowanie wyników

W tej sekcji użyjesz polecenia kubectl listę węzłów tworzonych w tym artykule.

Wprowadź następujące polecenie w wierszu polecenia terminalu:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Polecenie przekieruje Cię do strony uwierzytelniania. Zaloguj się przy użyciu konta platformy Azure.

Po uwierzytelnieniu polecenia kubectl wyświetla listę węzłów w podobny sposób, aby uzyskać następujące wyniki:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod jako `cleanup.yml`:

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

Uruchom element PlayBook przy użyciu polecenia `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)
