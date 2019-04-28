---
title: Platforma OpenShift na zadania usługi Azure po wdrożeniu | Dokumentacja firmy Microsoft
description: Dodatkowe zadania po OpenShift wdrożeniu klastra.
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
ms.openlocfilehash: fba29cd55f2d765faa107de3a8961032ef44deec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771371"
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

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

W konsoli platformy OpenShift będą teraz widoczne dwie opcje dla uwierzytelniania: htpasswd_auth i [rejestracji aplikacji].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorowanie platformy OpenShift przy użyciu dzienników usługi Azure Monitor

Istnieją trzy sposoby, aby dodać agenta usługi Log Analytics do OpenShift.
- Zainstaluj agenta usługi Log Analytics dla systemu Linux bezpośrednio w każdym węźle platformy OpenShift
- Włącz rozszerzenie maszyny Wirtualnej usługi Azure Monitor w każdym węźle platformy OpenShift
- Zainstaluj agenta usługi Log Analytics jako zestawie demona platformy OpenShift

Przeczytaj pełny [instrukcje](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) Aby uzyskać więcej informacji.

## <a name="configure-metrics-and-logging"></a>Skonfiguruj metryki i rejestrowanie

Oparte na gałęzi, szablony usługi Azure Resource Manager dla rozwiązania OpenShift Container Platform i OKD może dostarczyć parametry wejściowe dla włączenie metryk i rejestrowania jako część instalacji.

Oferta platformy OpenShift Container Platform w witrynie Marketplace zapewnia również opcję, aby włączyć metryki i rejestrowanie podczas instalacji klastra.

Jeśli metryki / podczas instalacji klastra nie zostało włączone rejestrowanie, łatwo można włączyć w późniejszym czasie.

### <a name="azure-cloud-provider-in-use"></a>Dostawca usługi Azure Cloud w użyciu

Protokół SSH z bastionu węzła lub pierwszy węzeł główny (na podstawie szablonu i gałęzi w użyciu) przy użyciu poświadczeń dostarczonych podczas wdrażania. Należy wydać następujące polecenie:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Dostawca usługi Azure Cloud nieużywany

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalowanie usługi Open Service Broker for Azure (OSBA)

Otwórz brokera usług dla platformy Azure lub usługą OSBA, pozwala aprowizować usług Azure Cloud Services bezpośrednio z OpenShift. OSBA w implementacji interfejsu Open API brokera usług dla platformy Azure. Otwarty interfejs API brokera usług jest specyfikacje, który definiuje wspólny język dla dostawców, którzy natywnych aplikacji w chmurze można używać do zarządzania usługami w chmurze bez blokady w chmurze.

Aby zainstalować OSBA na OpenShift, postępuj zgodnie z instrukcjami, znajduje się w tym miejscu: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Tylko wykonaj kroki opisane w sekcji szablon projektu platformy OpenShift, a nie całą sekcję instalowanie.

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do platformy OpenShift Container Platform](https://docs.openshift.com/container-platform)
