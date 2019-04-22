---
title: Platforma OpenShift na zadania usługi Azure po wdrożeniu | Dokumentacja firmy Microsoft
description: Dodatkowe zadania po OpenShift wdrożeniu klastra.
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
ms.openlocfilehash: cf3a3ca1f751ce9eed5ee5c5397c1d9c864a1dd6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58903679"
---
# <a name="post-deployment-tasks"></a>Po wdrożeniu zadania

Po wdrożeniu klastra usługi OpenShift, można skonfigurować dodatkowe elementy. W tym artykule omówiono:

- Jak skonfigurować logowanie jednokrotne za pomocą usługi Azure Active Directory (Azure AD)
- Jak skonfigurować dzienniki usługi Azure Monitor do monitorowania platformy OpenShift
- Jak skonfigurować rejestrowanie i metryki
- Jak zainstalować usługi Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurowanie logowania jednokrotnego przy użyciu usługi Azure Active Directory

Do uwierzytelniania usługi Azure Active Directory, musisz najpierw utworzyć rejestrowanie aplikacji usługi Azure AD. Ten proces obejmuje dwa kroki: Tworzenie rejestracji aplikacji i konfigurowaniu uprawnień.

### <a name="create-an-app-registration"></a>Tworzenie rejestracji aplikacji

Te kroki odnoszą się do utworzenia rejestracji aplikacji i Graficznym (portal), aby ustawić uprawnienia wiersza polecenia platformy Azure. Do utworzenia rejestracji aplikacji, potrzebne są następujące pięć rodzajów informacji:

- Nazwa wyświetlana: Nazwa rejestracji aplikacji (na przykład OCPAzureAD)
- Strona główna: Adres URL konsoli OpenShift (na przykład https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Identyfikator URI: Adres URL konsoli OpenShift (na przykład https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Adres URL odpowiedzi: Główna publiczny adres URL i nazwę rejestracji aplikacji (np.) https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Hasło: Bezpieczne hasło (Użyj silnego hasła)

Poniższy przykład tworzy rejestracji aplikacji, korzystając z informacji przedstawionych powyżej:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Jeśli polecenie zakończy się pomyślnie, otrzymasz dane wyjściowe JSON podobne do:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Zwróć uwagę na właściwość appId zwrócone w wyniku polecenia w późniejszym kroku.

W witrynie Azure Portal:

1. Wybierz **usługi Azure Active Directory** > **rejestracji aplikacji**.
2. Wyszukiwanie rejestracji aplikacji (na przykład OCPAzureAD).
3. W wynikach kliknij przycisk z rejestracji aplikacji.
4. W obszarze **ustawienia**, wybierz opcję **wymagane uprawnienia**.
5. W obszarze **wymagane uprawnienia**, wybierz opcję **Dodaj**.

   ![Rejestracja aplikacji](media/openshift-post-deployment/app-registration.png)

6. Kliknij krok 1: Wybierz interfejs API, a następnie kliknij przycisk **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Kliknij przycisk **wybierz** u dołu.

   ![Rejestracja aplikacji interfejsu API](media/openshift-post-deployment/app-registration-select-api.png)

7. W kroku 2: Wybierz uprawnienia, wybierz pozycję **Zaloguj się i odczytuj profil użytkownika** w obszarze **delegowane uprawnienia**, a następnie kliknij przycisk **wybierz**.

   ![Dostęp do rejestracji aplikacji](media/openshift-post-deployment/app-registration-access.png)

8. Wybierz pozycję **Done** (Gotowe).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurowanie platformy OpenShift na potrzeby uwierzytelniania usługi Azure AD

Aby skonfigurować OpenShift używać usługi Azure AD jako dostawcy uwierzytelniania, należy edytować plik /etc/origin/master/master-config.yaml we wszystkich węzłach wzorca.

Znajdź identyfikator dzierżawy za pomocą następującego polecenia interfejsu wiersza polecenia:

```azurecli
az account show
```

W pliku yaml Znajdź następujące wiersze:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Bezpośrednio po poprzednich wierszy, Wstaw następujące wiersze:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Upewnij się, że tekst jest wyrównywany prawidłowo w ramach identityProviders. Znajdź identyfikator dzierżawy za pomocą następującego polecenia interfejsu wiersza polecenia: ```az account show```

Uruchom ponownie usługi głównej OpenShift we wszystkich węzłach wzorca:

**OpenShift Container Platform (OCP) z wieloma wzorcami**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform z pojedynczym obszarze głównym**

```bash
sudo systemctl restart atomic-openshift-master
```

**OKD przy użyciu wielu wzorców**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OKD z pojedynczym obszarze głównym**

```bash
sudo systemctl restart origin-master
```

W konsoli platformy OpenShift będą teraz widoczne dwie opcje dla uwierzytelniania: htpasswd_auth i [rejestracji aplikacji].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorowanie platformy OpenShift przy użyciu dzienników usługi Azure Monitor

Istnieją trzy sposoby, aby dodać agenta usługi Log Analytics do OpenShift.
- Zainstaluj agenta usługi Log Analytics dla systemu Linux bezpośrednio w każdym węźle platformy OpenShift
- Włącz rozszerzenie maszyny Wirtualnej usługi Azure Monitor w każdym węźle platformy OpenShift
- Zainstaluj agenta usługi Log Analytics jako zestawie demona platformy OpenShift

Pełne instrukcje znajdują się w folderze: https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift.

## <a name="configure-metrics-and-logging"></a>Skonfiguruj metryki i rejestrowanie

Oparte na gałęzi, szablony usługi Azure Resource Manager dla rozwiązania OpenShift Container Platform i OKD może dostarczyć parametry wejściowe dla włączenie metryk i rejestrowania jako część instalacji.

Oferta platformy OpenShift Container Platform w witrynie Marketplace zapewnia również opcję, aby włączyć metryki i rejestrowanie podczas instalacji klastra.

Jeśli metryki / podczas instalacji klastra nie zostało włączone rejestrowanie, łatwo można włączyć w późniejszym czasie.

### <a name="ansible-inventory-pre-work"></a>Rozwiązanie Ansible spisu wstępne pracy

Sprawdź plik spisu ansible (/ etc/ansible/hosty) ma odpowiednią zmienne metryki / rejestrowania. Plik spisu znajdują się na różnych hostach, na podstawie szablonu używanego.

Szablon kontenera OpenShift i oferty w portalu Marketplace plik magazynu znajduje się na hostem bastionu. Dla szablonu OKD plik magazynu znajduje się na hoście głównego 0 lub hostem bastionu oparte na gałęzi w użyciu.

1. Edytuj plik /etc/ansible/hosts i dodaj następujące wiersze po sekcji dostawcy tożsamości (# HTPasswdPasswordIdentityProvider Włącz). Jeśli te wiersze są już zainstalowane, nie dodawaj ich ponownie.

   OpenShift / OKD w wersji 3.9 i starszych

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

   OpenShift / wersji OKD 3.10 i nowsze

   ```yaml
   # Setup metrics
   openshift_metrics_install_metrics=false
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_cassandra_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_heapster_nodeselector={"node-role.kubernetes.io/infra":"true"}

   # Setup logging
   openshift_logging_install_logging=false
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_kibana_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_curator_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_master_public_url=https://kibana.$ROUTING
   ```

3. Zastąp ciąg używany dla opcji openshift_master_default_subdomain, w tym samym pliku /etc/ansible/hosts $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Dostawca usługi Azure Cloud w użyciu

Protokół SSH z bastionu węzła lub pierwszy węzeł główny (na podstawie szablonu i gałęzi w użyciu) przy użyciu poświadczeń dostarczonych podczas wdrażania. Należy wydać następujące polecenie:

**OpenShift Container Platform 3.7 lub starszej**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OpenShift Container Platform 3.9 i nowsze**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

**OKD 3.7 lub starszej**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OKD 3.9 i nowsze**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Dostawca usługi Azure Cloud nieużywany

Protokół SSH z bastionu węzła lub pierwszy węzeł główny (na podstawie szablonu i gałęzi w użyciu) przy użyciu poświadczeń dostarczonych podczas wdrażania. Należy wydać następujące polecenie:


**OpenShift Container Platform 3.7 lub starszej**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OpenShift Container Platform 3.9 i nowsze**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.7 lub starszej**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.9 i nowsze**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True
ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalowanie usługi Open Service Broker for Azure (OSBA)

Otwórz brokera usług dla platformy Azure lub usługą OSBA, pozwala aprowizować usług Azure Cloud Services bezpośrednio z OpenShift. OSBA w implementacji interfejsu Open API brokera usług dla platformy Azure. Otwarty interfejs API brokera usług jest specyfikacje, który definiuje wspólny język dla dostawców, którzy natywnych aplikacji w chmurze można używać do zarządzania usługami w chmurze bez blokady w chmurze.

Aby zainstalować OSBA na OpenShift, postępuj zgodnie z instrukcjami, znajduje się w tym miejscu: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform)
- [Wprowadzenie do OKD](https://docs.okd.io/latest)
