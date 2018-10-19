---
title: Platforma OpenShift na zadania usługi Azure po wdrożeniu | Dokumentacja firmy Microsoft
description: Dodatkowe zadania po OpenShift wdrożeniu klastra.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/09/2018
ms.author: haroldw
ms.openlocfilehash: 39febceff58127fb9777ace6e3063fbe41605b79
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426451"
---
# <a name="post-deployment-tasks"></a>Po wdrożeniu zadania

Po wdrożeniu klastra usługi OpenShift, można skonfigurować dodatkowe elementy. W tym artykule opisano następujące czynności:

- Jak skonfigurować logowanie jednokrotne za pomocą usługi Azure Active Directory (Azure AD)
- Jak skonfigurować usługi Log Analytics do monitorowania platformy OpenShift
- Jak skonfigurować rejestrowanie i metryki

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Konfigurowanie logowania jednokrotnego przy użyciu usługi Azure Active Directory

Do uwierzytelniania usługi Azure Active Directory, musisz najpierw utworzyć rejestrowanie aplikacji usługi Azure AD. Ten proces obejmuje dwa kroki: Tworzenie rejestracji aplikacji i konfigurowaniu uprawnień.

### <a name="create-an-app-registration"></a>Tworzenie rejestracji aplikacji

Te kroki odnoszą się do utworzenia rejestracji aplikacji i Graficznym (portal), aby ustawić uprawnienia wiersza polecenia platformy Azure. Do utworzenia rejestracji aplikacji, potrzebne są następujące pięć rodzajów informacji:

- Nazwa wyświetlana: Nazwa rejestracji aplikacji (na przykład OCPAzureAD)
- Strona główna: (na przykład adres URL konsoli platformy OpenShift https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Identyfikator URI: Adres URL konsoli OpenShift (na przykład https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Adres URL odpowiedzi: Głównym publiczny adres URL i nazwa rejestracji aplikacji (np.) https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Hasło: Bezpieczne hasło (Użyj silnego hasła)

Poniższy przykład tworzy rejestracji aplikacji, korzystając z informacji przedstawionych powyżej:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Jeśli polecenie zakończy się pomyślnie, otrzymasz dane wyjściowe JSON podobne do:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
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

1.  Wybierz **usługi Azure Active Directory** > **rejestracji aplikacji**.
2.  Wyszukiwanie rejestracji aplikacji (na przykład OCPAzureAD).
3.  W wynikach kliknij przycisk z rejestracji aplikacji.
4.  W obszarze **ustawienia**, wybierz opcję **wymagane uprawnienia**.
5.  W obszarze **wymagane uprawnienia**, wybierz opcję **Dodaj**.

  ![Rejestracja aplikacji](media/openshift-post-deployment/app-registration.png)

6.  Kliknij krok 1: Wybierz interfejs API, a następnie kliknij przycisk **usługi Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Kliknij przycisk **wybierz** u dołu.

  ![Rejestracja aplikacji interfejsu API](media/openshift-post-deployment/app-registration-select-api.png)

7.  W kroku 2: Wybierz uprawnienia, wybierz pozycję **Zaloguj się i odczytuj profil użytkownika** w obszarze **delegowane uprawnienia**, a następnie kliknij przycisk **wybierz**.

  ![Dostęp do rejestracji aplikacji](media/openshift-post-deployment/app-registration-access.png)

8.  Wybierz pozycję **Done** (Gotowe).

### <a name="configure-openshift-for-azure-ad-authentication"></a>Konfigurowanie platformy OpenShift na potrzeby uwierzytelniania usługi Azure AD

Aby skonfigurować OpenShift używać usługi Azure AD jako dostawcy uwierzytelniania, należy edytować plik /etc/origin/master/master-config.yaml we wszystkich węzłach wzorca.

Znajdź identyfikator dzierżawy za pomocą następującego polecenia interfejsu wiersza polecenia:

```azurecli
az account show
```

W pliku yaml Znajdź następujące wiersze:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
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

Znajdź identyfikator dzierżawy za pomocą następującego polecenia interfejsu wiersza polecenia: ```az account show```

Uruchom ponownie usługi głównej OpenShift we wszystkich węzłach wzorca:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform (OCP) z wieloma wzorcami**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform z pojedynczym obszarze głównym**

```bash
sudo systemctl restart atomic-openshift-master
```

W konsoli platformy OpenShift będą teraz widoczne dwie opcje dla uwierzytelniania: htpasswd_auth i [rejestracji aplikacji].

## <a name="monitor-openshift-with-log-analytics"></a>OpenShift monitora z usługą Log Analytics

Aby monitorować OpenShift z usługą Log Analytics, należy użyć jednej z dwóch opcji: Instalacja agenta usługi Log Analytics na hoście maszyny Wirtualnej lub kontener usługi Log Analytics. Ten artykuł zawiera instrukcje dotyczące wdrażania kontenera w usłudze Log Analytics.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Tworzenie projektu platformy OpenShift usługi Log Analytics i ustaw dostępu użytkownika

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Utwórz plik yaml zestaw demona

Utwórz plik o nazwie ocp-omsagent.yml:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Utwórz plik yaml wpisu tajnego

Aby utworzyć plik yaml wpisu tajnego, potrzebujesz dwóch rodzajów informacji: identyfikator obszaru roboczego usługi Log Analytics i klucza wspólnego obszar roboczy Log Analytics. 

Przykładowy plik ocp secret.yml następująco: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Zastąp wsid_data z Base64 zakodowany identyfikator obszaru roboczego usługi Log Analytics. Następnie zastąp key_data algorytmem Base64 Log Analytics klucza obszaru roboczego udostępnione.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Tworzenie wpisu tajnego i demona zestawu

Wdróż plik wpisów tajnych:

```bash
oc create -f ocp-secret.yml
```

Wdrożenie zestawu demona Log Analytics Agent:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Skonfiguruj metryki i rejestrowanie

Szablon usługi Azure Resource Manager dla rozwiązania OpenShift Container Platform zawiera parametry wejściowe dla włączenie metryk i rejestrowania. Szablon platformy OpenShift Origin Resource Manager i oferty OpenShift Container Platform w witrynie Marketplace nie są.

Jeśli użyto szablonu usługi Resource Manager OCP i metryki i rejestrowanie nie zostały włączone w czasie instalacji lub jeśli używasz oferty w portalu Marketplace OCP, można łatwo włączyć je w późniejszym czasie. Jeśli używasz szablonu usługi Resource Manager w bazie wiedzy platformy OpenShift Origin jakąś pracę wstępnego jest wymagana.

### <a name="openshift-origin-template-pre-work"></a>Pracy przed szablonu platformy OpenShift Origin

1. Protokół SSH z pierwszym węzła głównego przy użyciu portu 2200.

   Przykład:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Edytuj plik /etc/ansible/hosts i dodaj następujące wiersze po sekcji dostawcy tożsamości (# Włącz HTPasswdPasswordIdentityProvider):

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

3. Zastąp ciąg używany dla opcji openshift_master_default_subdomain, w tym samym pliku /etc/ansible/hosts $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Dostawca usługi Azure Cloud w użyciu

W pierwszym węzłem głównym (źródło) lub bastionu węźle (OCP), protokołu SSH przy użyciu poświadczeń dostarczonych podczas wdrażania. Należy wydać następujące polecenie:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Dostawca usługi Azure Cloud nieużywany

W pierwszym węzłem głównym (źródło) lub bastionu węźle (OCP), protokołu SSH przy użyciu poświadczeń dostarczonych podczas wdrażania. Należy wydać następujące polecenie:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Wprowadzenie do platformy OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
