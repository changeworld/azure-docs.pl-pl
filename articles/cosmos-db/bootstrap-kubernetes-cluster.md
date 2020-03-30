---
title: Jak korzystać z usługi Azure Kubernetes z usługą Azure Cosmos DB
description: Dowiedz się, jak bootstrap klastra Kubernetes na platformie Azure, który używa usługi Azure Cosmos DB (wersja zapoznawcza)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70093726"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Jak korzystać z usługi Azure Kubernetes z usługą Azure Cosmos DB (wersja zapoznawcza)

Interfejs API etcd w usłudze Azure Cosmos DB umożliwia używanie usługi Azure Cosmos DB jako magazynu zaplecza dla usługi Azure Kubernetes. Usługa Azure Cosmos DB implementuje protokół przewodowy etcd, który umożliwia serwerom interfejsu API węzła głównego używanie usługi Azure Cosmos DB, tak jak w przypadku lokalnego instalowania etcd. interfejs API etcd w usłudze Azure Cosmos DB jest obecnie w wersji zapoznawczej. Korzystając z interfejsu API usługi Azure Cosmos etcd jako magazynu zapasowego dla aplikacji Kubernetes, można uzyskać następujące korzyści: 

* Nie ma potrzeby ręcznego konfigurowania i zarządzania etcd.
* Wysoka dostępność etcd, gwarantowana przez cosmos (99,99% w jednym regionie, 99,999% w wielu regionach).
* Elastyczna skalowalność etcd.
* Bezpieczne domyślnie & gotowe przedsiębiorstwo.
* Wiodące w branży, kompleksowe łata.

Aby dowiedzieć się więcej na temat interfejsu API etcd w usłudze Azure Cosmos DB, zobacz [artykuł omówienie.](etcd-api-introduction.md) W tym artykule pokazano, jak używać [usługi Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aparat aks) do uruchamiania klastra Kubernetes na platformie Azure, który używa usługi Azure [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) zamiast lokalnie zainstalowane i skonfigurowane itp. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Można pobrać interfejsu wiersza polecenia platformy Azure specyficzne dla systemu operacyjnego i zainstalować.

1. Zainstaluj [najnowszą wersję](https://github.com/Azure/aks-engine/releases) usługi Azure Kubernetes Engine. Instrukcje instalacji dla różnych systemów operacyjnych są dostępne na stronie [Aparat azure Kubernetes.](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) Wystarczy wykonać kroki z sekcji **Zainstaluj aparat AKS** połączonego doc. Po pobraniu wyodrębnij plik zip.

   Aparat kubernetes platformy Azure **(aparat aks)** generuje szablony usługi Azure Resource Manager dla klastrów kubernetes na platformie Azure. Dane wejściowe do aks-engine to plik definicji klastra, który opisuje żądany klaster, w tym koordynatora, funkcje i agentów. Struktura plików wejściowych jest podobna do publicznego interfejsu API dla usługi Azure Kubernetes.

1. Interfejs API etcd w usłudze Azure Cosmos DB jest obecnie w wersji zapoznawczej. Zarejestruj się, aby użyć https://aka.ms/cosmosetcdapi-signupwersji zapoznawczej pod adresem: . Po przesłaniu formularza subskrypcja zostanie opublikowana na białej liście w celu użycia interfejsu API usługi Azure Cosmos etcd. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Wdrażanie klastra za pomocą usługi Azure Cosmos DB

1. Otwórz okno wiersza polecenia i zaloguj się na platformie Azure za pomocą następującego polecenia:

   ```azurecli-interactive
   az login 
   ```

1. Jeśli masz więcej niż jedną subskrypcję, przełącz się do subskrypcji, która została łączona na białej liście dla interfejsu API usługi Azure Cosmos DB itp. Do wymaganej subskrypcji można przełączyć się za pomocą następującego polecenia:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Następnie utwórz nową grupę zasobów, w której będzie wdrażać zasoby wymagane przez klaster Azure Kubernetes. Upewnij się, że grupa zasobów w regionie "centralus". Nie jest obowiązkowe dla grupy zasobów, aby być w regionie "centralus", jednak interfejs API usługi Azure Cosmos etcd jest obecnie dostępny do wdrożenia tylko w regionie "centralus". Dlatego najlepiej jest mieć klaster Kubernetes być współlokowane z Cosmos etcd wystąpienia:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Następnie utwórz jednostkę usługi dla klastra Azure Kubernetes, aby mógł komunikować się z zasobami, które są częścią tej samej grupy zasobów. Można utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub witryny Azure portal, w tym przykładzie będzie można cli go utworzyć.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   To polecenie wyprowadza szczegóły jednostki usługi, na przykład:
   
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
   
   Zanotuj **appId** i pola **hasła,** ponieważ użyjesz tych parametrów w następnych krokach. 

1. W wierszu polecenia przejdź do folderu, w którym znajduje się plik wykonywalny usługi Azure Kubernetes Engine. Na przykład w wierszu polecenia można przejść do folderu w następujący sposób:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Otwórz wybrany edytor tekstu i zdefiniuj szablon Menedżera zasobów, który wdraża klaster Azure Kubernetes za pomocą interfejsu API usługi Azure Cosmos DB itp. Skopiuj następującą definicję JSON `apiModel.json`do edytora tekstu i zapisz plik jako:

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

   W pliku definicji JSON/klastra kluczowym parametrem do zanotowania jest **"cosmosEtcd": true**. Ten parametr jest we właściwościach "masterProfile" i wskazuje wdrożenie do korzystania z interfejsu API usługi Azure Cosmos etcd zamiast regularnych etcd. 

1. Wdrażanie klastra usługi Azure Kubernetes, który używa usługi Azure Cosmos DB za pomocą następującego polecenia:

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

   Usługa Azure Kubernetes Engine zużywa definicję klastra, która określa żądany kształt, rozmiar i konfigurację platformy Azure Kubernetes. Istnieje kilka funkcji, które można włączyć za pomocą definicji klastra. W tym przykładzie użyjesz następujących parametrów:

   * **identyfikator subskrypcji:** Identyfikator subskrypcji platformy Azure, który ma azure cosmos db etcd API włączone.
   * **identyfikator klienta:** AppId jednostki usługi. Został `appId` zwrócony jako wyjście w kroku 4.
   * **Tajemnica klienta:** Hasło jednostki usługi lub losowo wygenerowane hasło. Ta wartość została zwrócona jako dane wyjściowe w parametrze "hasło" w kroku 4. 
   * **dnsPrefix:** Unikatowa nazwa DNS regionu. Ta wartość będzie stanowić część nazwy hosta (przykładowe wartości to- myprod1, staging).
   * **lokalizacja:**  Lokalizacja, w której klaster powinien być wdrożony, obecnie obsługiwane jest tylko "centralus".

   > [!Note]
   > Interfejs API usługi Azure Cosmos etcd jest obecnie dostępny do wdrożenia tylko w regionie "centralus". 
 
   * **api-model:** W pełni kwalifikowana ścieżka do pliku szablonu.
   * **zastąpienie siły:** Ta opcja służy do automatycznego zastępowanie istniejących plików w katalogu wyjściowym.
 
   Następujące polecenie pokazuje przykładowe wdrożenie:

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

Wdrożenie szablonu trwa kilka minut. Po pomyślnym zakończeniu wdrożenia w wierszu polecenia zostaną wyświetlona następująca liczba danych wyjściowych:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Grupa zasobów zawiera teraz zasoby, takie jak- maszyna wirtualna, konto usługi Azure Cosmos (etcd API), sieć wirtualna, zestaw dostępności i inne zasoby wymagane przez klaster Kubernetes. 

Nazwa konta usługi Azure Cosmos będzie zgodna z określonym prefiksem DNS dołączonego do k8s. Twoje konto usługi Azure Cosmos zostanie automatycznie aprowizowana za pomocą bazy danych o nazwie **EtcdDB** i kontenera o nazwie **EtcdData**. Kontener będzie przechowywać wszystkie dane związane z etcd. Kontener jest aprowizowana z określoną liczbą jednostek żądań i można [skalować (zwiększać/zmniejszać) przepływność](scaling-throughput.md) na podstawie obciążenia. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [pracować z bazą danych, kontenerami i elementami usługi Azure Cosmos](databases-containers-items.md)
* Dowiedz się, jak [zoptymalizować aprowizowaną przepustowość](optimize-cost-throughput.md)

