---
title: Jak używać usługi Azure Kubernetes za pomocą usługi Azure Cosmos DB
description: Dowiedz się, jak uruchomienie klastra Kubernetes na platformie Azure, która używa usługi Azure Cosmos DB (wersja zapoznawcza)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 43aa0956ef1f44fa5705800ff2b424608ec75499
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795612"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Jak używać usługi Azure Kubernetes za pomocą usługi Azure Cosmos DB (wersja zapoznawcza)

Etcd interfejsu API w usłudze Azure Cosmos DB pozwala używać usługi Azure Cosmos DB jako magazynu zaplecza dla usługi Azure Kubernetes. Usługa Azure Cosmos DB implementuje protokół przewodowy etcd, co pozwala węzła głównego interfejsu API serwerów do korzystania z usługi Azure Cosmos DB, tak samo, jak ona dostęp do etcd zainstalowane lokalnie. etcd interfejsu API w usłudze Azure Cosmos DB jest obecnie dostępna w wersji zapoznawczej. Usługa Azure Cosmos etcd interfejsu API jest używany jako magazyn zapasowy Kubernetes, pojawi się następujące korzyści: 

* Nie trzeba ręcznie skonfigurować i zarządzać etcd.
* Wysoka dostępność etcd, gwarantowane przez Cosmos (99,99% w obrębie jednego regionu, w wielu regionach przez 99,999% czasu).
* Elastycznej skalowalności etcd.
* Zabezpieczanie przez domyślny & z myślą o przedsiębiorstwach.
* Wiodące w branży, wszechstronnymi umowami SLA.

Aby dowiedzieć się więcej na temat etcd interfejsu API w usłudze Azure Cosmos DB, zobacz [Przegląd](etcd-api-introduction.md) artykułu. W tym artykule dowiesz się, jak używać [Azure Kubernetes aparatu](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine), uruchomienie klastra Kubernetes na platformie Azure, która używa [usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) zamiast etcd lokalnie zainstalowany i skonfigurowany. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Można pobrać wiersza polecenia platformy Azure jest określonego systemu operacyjnego i zainstalować.

1. Zainstaluj [v0.32.3](https://github.com/Azure/aks-engine/releases/tag/v0.32.3) wersja aparatu Kubernetes usługi Azure. Instrukcje dotyczące instalacji dla różnych systemów operacyjnych są dostępne w [Azure Kubernetes aparatu](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) strony. Po prostu potrzebujesz z procedury **Instalowanie aparatu AKS** sekcji połączonych doc. Po pobraniu, Wyodrębnij plik zip.

   Aparat usługi Azure Kubernetes (**aks aparatu**) generuje szablony usługi Azure Resource Manager dla klastrów Kubernetes na platformie Azure. Dane wejściowe aks aparat są pliku definicji klastra, który opisuje żądany klaster z programu orchestrator, funkcje i agentów. Struktura plików wejściowych jest podobny do publicznego interfejsu API dla usługi Azure Kubernetes Service.

1. Etcd interfejsu API w usłudze Azure Cosmos DB jest obecnie dostępna w wersji zapoznawczej. Utwórz konto, aby użyć wersji zapoznawczej w: https://aka.ms/cosmosetcdapi-signup. Po przesłaniu formularza, subskrypcja zostanie umieszczona na białej liście, aby użyć usługi Azure Cosmos etcd interfejsu API. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Wdrażanie klastra za pomocą usługi Azure Cosmos DB

1. Otwórz okno wiersza polecenia, a następnie zaloguj się do platformy Azure za pomocą następującego polecenia:

   ```azurecli-interactive
   az login 
   ```

1. Jeśli masz więcej niż jedną subskrypcję, przełącz się do subskrypcji, który znajduje się na liście dozwolonych dla usługi Azure Cosmos DB etcd interfejsu API. Można przełączać się do subskrypcji wymagany przy użyciu następującego polecenia:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Następnie należy utworzyć nową grupę zasobów, w którym będą wdrażane zasoby wymagane przez klaster Kubernetes na platformie Azure. Upewnij się utworzyć grupę zasobów w regionie "centralus". Nie jest to obowiązkowe dla grupy zasobów, jednak znajdować się w regionie "centralus", Azure Cosmos etcd interfejsu API jest obecnie dostępna do wdrożenia "centralus" tylko w regionie. Dlatego najlepiej jest klaster Kubernetes jako wspólnie przechowywane w wystąpieniu etcd Cosmos:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Następnie należy utworzyć usługę podmiotu zabezpieczeń dla klastra Kubernetes usługi Azure tak, że będzie mogła komunikować się z zasobami, które są częścią tej samej grupie zasobów. Można utworzyć jednostki usługi przy użyciu wiersza polecenia platformy Azure, programu PowerShell lub witryny Azure portal, w tym przykładzie będzie można interfejsu wiersza polecenia, aby go utworzyć.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   To polecenie wyświetla szczegóły jednostki, usługi, na przykład:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Zwróć uwagę na **appId** i **hasło** pola, ponieważ użyjesz tych parametrów w następnych krokach. 

1. W wierszu polecenia przejdź do folderu, w którym znajduje się plik wykonywalny usługi Azure Kubernetes aparatu. Na przykład w wierszu polecenia można przejść do folderu jako:

   ```cmd
   cd "\aks-engine-v0.32.3-windows-amd64\aks-engine-v0.32.3-windows-amd64"
   ```

1. Otwórz ulubionego edytora tekstu, a następnie zdefiniować szablon usługi Resource Manager, który jest wdrażany klaster Azure Kubernetes za pomocą usługi Azure Cosmos DB etcd interfejsu API. Skopiuj poniższą definicję formatu JSON do edytora tekstu, a następnie zapisz plik jako `apiModel.json`:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   W pliku definicji JSON/klaster jest parametr klucza, należy pamiętać, **"cosmosEtcd": true**. Ten parametr jest we właściwościach "profilu masterProfile" i wskazuje wdrożenia do korzystania z interfejsu API usługi Azure Cosmos etcd zamiast regularnego etcd. 

1. Wdrażanie klastra Kubernetes usługi Azure, która używa usługi Azure Cosmos DB za pomocą następującego polecenia:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Definicja klastra, zawierający żądany kształt, rozmiar i Konfiguracja usługi Azure Kubernetes korzysta z aparatu usługi Azure Kubernetes. Istnieje kilka funkcji, które można włączyć za pośrednictwem definicji klastra. W tym przykładzie będzie używać następujących parametrów:

   * **Identyfikator subskrypcji:** Identyfikator subskrypcji platformy Azure, który ma włączone etcd usługi Azure Cosmos DB w interfejsu API.
   * **Identyfikator klienta:** Identyfikator aplikacji nazwy głównej usługi. `appId` Został zwrócony jako dane wyjściowe w kroku 4.
   * **Klucz tajny klienta:** Hasło jednostki usługi lub losowo wygenerowane hasło. Ta wartość została zwrócona jako dane wyjściowe w parametrze "password" w kroku 4. 
   * **dnsPrefix:** Region — unikatowa nazwa DNS. Ta wartość będzie stanowić część nazwy hosta (przykład wartości są myprod1, przejściowe).
   * **Lokalizacja:**  Lokalizacja, w którym klaster powinny być wdrożone, obecnie jedyną "centralus" jest obsługiwana.

   > [!Note]
   > Interfejs API Azure etcd Cosmos jest obecnie dostępna do wdrożenia "centralus" tylko w regionie. 
 
   * **api-model:** W pełni kwalifikowana ścieżka do pliku szablonu.
   * **force-overwrite:** Ta opcja jest używana w celu automatycznego zastępowania istniejących plików w katalogu wyjściowym.
 
   Następujące polecenie wyświetla Przykładowe wdrożenie:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie szablonu zajmuje kilka minut. Po pomyślnym zakończeniu wdrożenia, zobaczysz następujące dane wyjściowe w wierszu polecenia:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Takie jak grupy zasobów zasobów zawiera teraz — maszyna wirtualna, usługa Azure Cosmos konta (etcd interfejsu API), sieci wirtualnej, zestawu dostępności, i inne zasoby wymagane przez klaster Kubernetes. 

Nazwa konta usługi Azure Cosmos będzie odpowiadał dołączany wraz z k8s określonego prefiksu DNS. Twoje konto usługi Azure Cosmos będą automatycznie udostępniane z bazy danych o nazwie **EtcdDB** i kontener o nazwie **EtcdData**. Kontener będzie przechowywać wszystkie etcd powiązane dane. Kontener jest inicjowana z określoną liczbą jednostek żądania, a następnie możesz [skali (zwiększyć/zmniejszyć) przepływność](scaling-throughput.md) na podstawie własnego obciążenia. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [działają z bazy danych Azure Cosmos, kontenery i elementów](databases-containers-items.md)
* Dowiedz się, jak [optymalizować koszty aprowizowana przepływność](optimize-cost-throughput.md)

