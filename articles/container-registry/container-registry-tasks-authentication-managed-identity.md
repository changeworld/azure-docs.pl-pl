---
title: Używanie tożsamości zarządzanej z zadaniami Azure Container Registry
description: Zapewnianie Azure Container Registry dostęp do zasobów platformy Azure, w tym do innych prywatnych rejestrów kontenerów, przypisując zarządzaną tożsamość zasobów platformy Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311532"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Korzystanie z tożsamości zarządzanej przez platformę Azure w zadaniach ACR 

Za pomocą [zarządzanej tożsamości zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) można uwierzytelniać z ACR zadań do usługi Azure Container Registry lub innych zasobów platformy Azure bez konieczności udostępniania poświadczeń ani zarządzania nimi w kodzie. Na przykład można użyć tożsamości zarządzanej do ściągania lub wypychania obrazów kontenera do innego rejestru jako krok w zadaniu.

W tym artykule dowiesz się więcej na temat tożsamości zarządzanych i instrukcje:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej do systemu lub tożsamości przypisanej przez użytkownika w zadaniu ACR
> * Przyznaj tożsamości dostęp do zasobów platformy Azure, takich jak inne rejestry kontenerów platformy Azure
> * Korzystanie z tożsamości zarządzanej w celu uzyskiwania dostępu do zasobów z zadania 

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.66 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Zarządzana tożsamość zasobów platformy Azure zapewnia usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory (Azure AD). Istnieje możliwość skonfigurowania [niektórych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), w tym zadań ACR, z zarządzaną tożsamością. Następnie użyj tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, bez przekazywania poświadczeń w kodzie lub w skryptach.

Zarządzane tożsamości są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i zachowują się tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie w wersji zapoznawczej.

* *Tożsamość zarządzana przez system*, która jest unikatowa dla określonego zasobu, takiego jak zadanie ACR i obowiązuje w okresie istnienia tego zasobu.

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej Udziel tożsamości dostępu do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. Na przykład Przypisz zarządzaną tożsamość rolę z ściąganiem, wypychaniem i ściąganiem lub innymi uprawnieniami do prywatnego rejestru kontenerów na platformie Azure. Aby uzyskać pełną listę ról rejestru, zobacz [Azure Container Registry role i uprawnienia](container-registry-roles.md).) Możesz nadać tożsamości dostęp do jednego lub większej liczby zasobów.

## <a name="create-container-registries"></a>Tworzenie rejestrów kontenerów

W tym samouczku potrzebne są trzy rejestry kontenerów:

* Pierwszy rejestr służy do tworzenia i wykonywania zadań ACR. W tym artykule rejestr źródłowy nosi nazwę "Moje *Rejestr*". 
* Drugie i trzecie rejestry są docelowymi rejestrami dla pierwszego przykładowego zadania, aby wypchnąć obraz, który kompiluje. W tym artykule rejestry docelowe mają nazwy *customregistry1* i *customregistry2*.

Zastąp ciąg własnymi nazwami rejestru w dalszych krokach.

Jeśli nie masz jeszcze wymaganych rejestrów kontenerów platformy Azure, [zobacz Szybki Start: Utwórz prywatny rejestr kontenerów za pomocą interfejsu wiersza](container-registry-get-started-azure-cli.md)polecenia platformy Azure. Nie jest jeszcze konieczne wypychanie obrazów do rejestru.

## <a name="example-task-with-a-system-assigned-identity"></a>Przykład: Zadanie z tożsamością przypisaną do systemu

Ten przykład pokazuje, jak utworzyć [zadanie](container-registry-tasks-multi-step.md) wieloetapowe z tożsamością przypisaną do systemu. Zadanie tworzy obraz, a następnie używa tożsamości do uwierzytelniania przy użyciu dwóch rejestrów docelowych w celu wypchnięcia obrazu.

Kroki tego przykładowego zadania są zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md) o nazwie `testtask.yaml`. Plik znajduje się w katalogu multipleRegistries w repozytorium przykładów [ACR-Tasks](https://github.com/Azure-Samples/acr-tasks) . Plik zostanie utworzony tutaj:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Tworzenie zadania z tożsamością przypisaną do systemu

Utwórz zadanie *wielu reg* , wykonując następujące polecenie [AZ ACR Task Create][az-acr-task-create] . Kontekst zadania to folder multipleRegistries repozytorium przykładów, a polecenie odwołuje się do pliku `testtask.yaml` w repozytorium. `--assign-identity` Parametr bez dodatkowych wartości tworzy dla zadania tożsamość przypisaną do systemu. To zadanie jest skonfigurowane tak, aby trzeba było wyzwolić je ręcznie, ale można je skonfigurować do uruchomienia, gdy zatwierdzenia są przekazywane do repozytorium lub zostanie wykonane żądanie ściągnięcia. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

W danych wyjściowych `identity` polecenia sekcja pokazuje tożsamość typu `SystemAssigned` jest ustawiana w zadaniu. `principalId` Jest identyfikatorem jednostki usługi tożsamości:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Użyj polecenia [AZ ACR Task show][az-acr-task-show] , aby przechowywać `principalId` w zmiennej, aby użyć w późniejszych poleceniach:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Udziel uprawnień wypychania tożsamości do dwóch docelowych rejestrów kontenerów

W tej sekcji nadaj przypisanemu systemowi uprawnienia do tożsamości wypychania do dwóch rejestrów docelowych o nazwie *customregistry1* i *customregistry2*.

Najpierw użyj polecenia [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu każdego rejestru i zapisać identyfikatory w zmiennych:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamość `acrpush` do poszczególnych rejestrów. Ta rola ma uprawnienia do ściągania i wypychania obrazów do rejestru kontenerów.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Dodaj docelowe poświadczenia rejestru do zadania

Teraz za pomocą polecenia [AZ ACR Task Credential Add][az-acr-task-credential-add] Dodaj poświadczenia tożsamości do zadania, aby można było uwierzytelnić się w obu rejestrach docelowych.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Użyj polecenia [AZ ACR Task Run][az-acr-task-run] , aby ręcznie wyzwolić zadanie. Parametr jest używany do przekazywania nazw serwerów logowania dwóch rejestrów docelowych jako wartości zmiennych `REGISTRY1` zadania i `REGISTRY2`. `--set`

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

Dane wyjściowe są podobne do następujących:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Przykład: Zadanie z tożsamością przypisaną przez użytkownika

W tym przykładzie utworzysz tożsamość przypisaną przez użytkownika z uprawnieniami do odczytu wpisów tajnych z magazynu kluczy platformy Azure. Ta tożsamość zostanie przypisana do zadania wieloetapowego, które odczytuje wpis tajny, kompiluje obraz i zaloguje się do interfejsu wiersza polecenia platformy Azure w celu odczytania znacznika obrazu.

### <a name="create-a-key-vault-and-store-a-secret"></a>Tworzenie magazynu kluczy i przechowywanie klucza tajnego

Po pierwsze, jeśli chcesz, Utwórz grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* przy użyciu następującego polecenia [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [AZ Key magazynu Create][az-keyvault-create] , aby utworzyć magazyn kluczy. Pamiętaj, aby określić unikatową nazwę magazynu kluczy. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Zapisz przykładowy klucz tajny w magazynie kluczy za pomocą polecenia AZ Key Key [Secret Set][az-keyvault-secret-set] :

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Na przykład może być konieczne przechowywanie poświadczeń w celu uwierzytelnienia przy użyciu prywatnego rejestru platformy Docker, aby można było ściągnąć obraz prywatny.

### <a name="create-an-identity"></a>Tworzenie tożsamości

Utwórz tożsamość o nazwie *myACRTasksId* w subskrypcji za pomocą polecenia [AZ Identity Create][az-identity-create] . Możesz użyć tej samej grupy zasobów, która została wcześniej użyta do utworzenia rejestru kontenerów lub magazynu kluczy lub innego.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Aby skonfigurować tożsamość w poniższych krokach, użyj polecenia [AZ Identity show][az-identity-show] , aby zapisać identyfikator zasobu tożsamości i identyfikator jednostki usługi w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Przyznaj tożsamości dostęp do magazynu kluczy w celu odczytu wpisu tajnego

Uruchom następujące polecenie [AZ KeyBinding Set-Policy][az-keyvault-set-policy] , aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości przypisanej przez użytkownika uzyskanie wpisów tajnych z magazynu kluczy. Ten dostęp jest potrzebny później w celu pomyślnego uruchomienia zadania wieloetapowego.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Przyznaj czytelnikowi tożsamości dostęp do grupy zasobów dla rejestru

Uruchom następujące polecenie [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamość rolę czytnika, w tym przypadku do grupy zasobów zawierającej rejestr źródłowy. Ta rola jest wymagana później, aby pomyślnie uruchomić zadanie wieloetapowe.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Tworzenie zadania z tożsamością przypisaną przez użytkownika

Teraz można utworzyć [zadanie](container-registry-tasks-multi-step.md) wieloetapowe i przypisać je tożsamości przypisanej do użytkownika. Na potrzeby tego przykładowego zadania Utwórz [plik YAML](container-registry-tasks-reference-yaml.md) o `managed-identities.yaml` nazwie w lokalnym katalogu roboczym i wklej poniższą zawartość. Pamiętaj, aby zastąpić nazwę magazynu kluczy w pliku nazwą magazynu kluczy

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

To zadanie wykonuje następujące czynności:

* Sprawdza, czy może uzyskać dostęp do wpisu tajnego w magazynie kluczy. Ten krok jest przeznaczony dla celów demonstracyjnych. W rzeczywistym scenariuszu może być potrzebny krok zadania, aby uzyskać poświadczenia umożliwiające dostęp do prywatnego repozytorium centrum platformy Docker.
* Kompiluje i wypchnij `mywebsite` obraz do rejestru źródłowego.
* Rejestruje w interfejsie wiersza polecenia platformy Azure `my-website` , aby wyświetlić listę tagów obrazu w rejestrze źródłowym.

Utwórz zadanie o nazwie *msitask* i przekaż mu identyfikator zasobu utworzonej wcześniej tożsamości przypisanej do użytkownika. To przykładowe zadanie jest tworzone na podstawie `managed-identities.yaml` pliku zapisanego w lokalnym katalogu roboczym, dlatego należy wyzwolić go ręcznie.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

W danych wyjściowych `identity` polecenia sekcja pokazuje tożsamość typu `UserAssigned` jest ustawiana w zadaniu. `principalId` Jest identyfikatorem jednostki usługi tożsamości:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Użyj polecenia [AZ ACR Task Run][az-acr-task-run] , aby ręcznie wyzwolić zadanie. `--set` Parametr jest używany do przekazania nazwy rejestru źródłowego do zadania:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Dane wyjściowe pokazują, że klucz tajny został rozwiązany, obraz został pomyślnie skompilowany i wypychany, a zadanie zostanie zapisane w interfejsie wiersza polecenia platformy Azure przy użyciu tożsamości, aby odczytać tag obrazu z rejestru źródłowego:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o używaniu zarządzanych tożsamości z zadaniami Azure Container Registry i sposobami wykonywania tych czynności:

> [!div class="checklist"]
> * Włączanie przypisanej do systemu tożsamości lub przypisanej przez użytkownika do zadania ACR
> * Przyznaj tożsamości dostęp do zasobów platformy Azure, takich jak inne rejestry kontenerów platformy Azure
> * Korzystanie z tożsamości zarządzanej w celu uzyskiwania dostępu do zasobów z zadania  

* Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
