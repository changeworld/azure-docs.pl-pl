---
title: Samouczek — konfigurowanie ról kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Kubernetes Service (AKS) przy użyciu usługi Ansible
description: Dowiedz się, jak skonfigurować usługę RBAC w klastrze usługi Azure Kubernetes (AKS) za pomocą funkcji Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, kontener, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76836970"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Samouczek: Konfigurowanie ról kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Kubernetes Service (AKS) przy użyciu usługi Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Usługę AKS można skonfigurować tak, aby [używała usługi Azure Active Directory (AD)](/azure/active-directory/) do uwierzytelniania użytkowników. Po skonfigurowaniu token uwierzytelniania usługi Azure AD służy do logowania się do klastra AKS. RBAC może opierać się na tożsamości użytkownika lub członkostwa w grupie katalogów.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Tworzenie klastra AKS z obsługą usługi Azure AD
> * Konfigurowanie roli RBAC w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instalowanie biblioteki RedHat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurowanie usługi Azure AD dla uwierzytelniania usługi AKS

Podczas konfigurowania usługi Azure AD dla uwierzytelniania AKS skonfigurowano dwie aplikacje usługi Azure AD. Ta operacja musi zostać ukończona przez administratora dzierżawy platformy Azure. Aby uzyskać więcej informacji, zobacz [Integrowanie usługi Azure Active Directory z usługą AKS](/azure/aks/aad-integration#create-the-server-application). 

Administrator dzierżawy platformy Azure pobierz następujące wartości:

- Klucz tajny aplikacji serwera
- Identyfikator aplikacji serwera
- Identyfikator aplikacji klienckiej 
- Identyfikator dzierżawy

Te wartości są potrzebne do uruchomienia przykładowego podręcznika.  

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W tej sekcji utworzysz usługę AKS za pomocą [aplikacji usługi Azure AD](#configure-azure-ad-for-aks-authentication).

Oto kilka kluczowych uwag, które należy wziąć pod uwagę podczas pracy z przykładowym podręcznikiem:

- Podręcznik ładuje `ssh_key` się `~/.ssh/id_rsa.pub`z . Jeśli go zmodyfikujesz, użyj formatu jednowierszowego - zaczynając od "ssh-rsa" (bez cudzysłowów).
- I `client_id` `client_secret` wartości są `~/.azure/credentials`ładowane z , który jest domyślnym plikiem poświadczeń. Można ustawić te wartości na jednostkę usługi lub załadować te wartości ze zmiennych środowiskowych:

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

## <a name="get-the-azure-ad-object-id"></a>Pobierz identyfikator obiektu usługi Azure AD

Aby utworzyć powiązanie RBAC, należy najpierw uzyskać identyfikator obiektu usługi Azure AD. 

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. W polu wyszukiwania u góry strony `Azure Active Directory`wprowadź . 

1. Kliknij pozycję `Enter` (Dalej).

1. W menu **Zarządzanie** wybierz polecenie **Użytkownicy**.

1. W polu nazwa wyszukaj konto.

1. W kolumnie **Nazwa** wybierz łącze do swojego konta.

1. W sekcji **Tożsamość** skopiuj **identyfikator obiektu**.

    ![Skopiuj identyfikator obiektu usługi Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Tworzenie powiązania RBAC

W tej sekcji utworzysz powiązanie roli lub powiązanie roli klastra w udziale usługi AKS. 

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

Zastąp symbol zastępczy `<your-aad-account>` identyfikatorem obiektu [dzierżawcy](#get-the-azure-ad-object-id)usługi Azure AD .

Zapisz następujący podręcznik — który wdraża nową rolę w `aks-kube-deploy.yml`AKS — jako:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Uruchamianie przykładowego podręcznika

W tej sekcji wymieniono pełny przykładowy podręcznik, który wywołuje zadania tworzenia w tym artykule. 

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

W `vars` sekcji zastąp następujące symbole zastępcze informacjami usługi Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Uruchom kompletny podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Weryfikowanie wyników

W tej sekcji należy użyć kubectl listy węzłów tworzenia w tym artykule.

Wprowadź następujące polecenie w wierszu terminala:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Polecenie przekieruje Cię do strony uwierzytelniania. Zaloguj się za pomocą konta platformy Azure.

Po uwierzytelnieniu kubectl wyświetla listę węzłów w podobny sposób do następujących wyników:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń zasoby utworzone w tym artykule. 

Zapisz następujący kod `cleanup.yml`jako:

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

Uruchom podręcznik za `ansible-playbook` pomocą polecenia:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązanie Ansible na platformie Azure](/azure/ansible/)
