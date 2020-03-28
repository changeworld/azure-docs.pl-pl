---
title: 'Samouczek: Tworzenie klastra z włączonym serwerem proxy Apache Rest w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Dowiedz się, jak wykonywać operacje apache platformy Kafka przy użyciu serwera proxy Odwościenia platformy Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201885"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Samouczek: Tworzenie klastra z włączonym serwerem proxy Apache Rest w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku dowiesz się, jak utworzyć apache Kafka [REST włączone klastra](./rest-proxy.md) w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Apache Kafka to rozproszona platforma przesyłania strumieniowego typu open source. Jest ona często używana jako broker komunikatów, ponieważ oferuje funkcje podobne do kolejki komunikatów dotyczących publikowania i subskrybowania. Serwer proxy Kafka REST umożliwia interakcję z klastrem platformy Kafka za pośrednictwem [interfejsu API REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) za pośrednictwem protokołu HTTP. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Dostęp do interfejsu API platformy Apache Kafka mogą uzyskać tylko zasoby będące w tej samej sieci wirtualnej. Dostęp do klastra można uzyskać bezpośrednio za pomocą funkcji SSH. Aby do platformy Apache Kafka podłączyć inne usługi, sieci lub maszyny wirtualne, należy najpierw utworzyć sieć wirtualną, a następnie utworzyć zasoby w obrębie tej sieci. Aby uzyskać więcej informacji, zobacz [Łączenie się z platformą Apache Kafka przy użyciu sieci wirtualnej](./apache-kafka-connect-vpn-gateway.md).

W tym samouczku dowiesz się:

> [!div class="checklist"]
> * Wymagania wstępne dla serwera proxy Kafka REST
> * Tworzenie klastra platformy Apache Platformy Kafka przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja zarejestrowana w usłudze Azure AD. Aplikacje klienckie, które piszesz do interakcji z serwerem proxy Rest platformy Kafka użyje identyfikatora tej aplikacji i klucz tajny do uwierzytelniania na platformie Azure. Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md).

* Grupa zabezpieczeń usługi Azure AD z zarejestrowaną aplikacją jako członkiem. Ta grupa zabezpieczeń będzie używana do kontrolowania, które aplikacje mogą wchodzić w interakcje z serwerem proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Interfejsu wiersza polecenia platformy Azure. Upewnij się, że masz co najmniej wersję 2.0.79. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Tworzenie klastra platformy Apache Kafka

1. Zaloguj się do subskrypcji platformy Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ustawianie zmiennych środowiskowych. Użycie zmiennych w tym samouczku opiera się na Bash. Niewielkie różnice będą potrzebne w innych środowiskach.

    |Zmienna | Opis |
    |---|---|
    |resourceGroupName|Zastąp NAZWĘ GRUPY ZASOBÓW nazwą nowej grupy zasobów.|
    |location|Zamień lokalizację na region, w którym zostanie utworzony klaster. Aby uzyskać listę prawidłowych `az account list-locations` lokalizacji, użyj polecenia|
    |clusterName|Zamień nazwę CLUSTERNAME na unikatową globalnie nazwę nowego klastra.|
    |storageAccount|Zastąp pamięć STORAGEACCOUNTNAME nazwą nowego konta magazynu.|
    |httpPassword|Zamień HASŁO na hasło do logowania do klastra, **admin**.|
    |sshPassword (hasło ssh)|Zamień hasło na hasło do bezpiecznej nazwy użytkownika powłoki, **sshuser**.|
    |securityGroupName (Nazwa grupy)|Zastąp SECURITYGROUPNAME nazwą grupy zabezpieczeń AAD dla serwera proxy obsługi sieci Kafka. Zmienna zostanie przekazana `--kafka-client-group-name` do `az-hdinsight-create`parametru dla .|
    |securityGroupID (ida grupy)|Zastąp SECURITYGROUPID identyfikatorem grupy zabezpieczeń AAD dla serwera proxy spoczynku platformy Kafka. Zmienna zostanie przekazana `--kafka-client-group-id` do `az-hdinsight-create`parametru dla .|
    |magazynPrzejednacz|Kontener magazynu, którego będzie używał klaster, pozostaw jako —jest w tym samouczku. Ta zmienna zostanie ustawiona z nazwą klastra.|
    |workernodeCount (liczba pracowników)|Liczba węzłów procesu roboczego w klastrze, pozostawić jako — jest w tym samouczku. Aby zagwarantować wysoką dostępność, platforma Kafka wymaga co najmniej 3 węzłów procesu roboczego|
    |typ klastra|Typ klastra HDInsight, pozostaw jako —jest w tym samouczku.|
    |clusterVersion (Version klastra)|Wersja klastra HDInsight, pozostaw jako —jest dla tego samouczka. Serwer proxy spoczynku platformy Kafka wymaga minimalnej wersji klastra 4.0.|
    |componentVersion|Wersja kafka, pozostawić tak jak jest w tym samouczku. Serwer proxy spoczynku platformy Kafka wymaga minimalnej wersji komponentu 2.1.|

    Zaktualizuj zmienne z żądanymi wartościami. Następnie wprowadź polecenia interfejsu wiersza polecenia, aby ustawić zmienne środowiskowe.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Utwórz grupę zasobów,](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) wprowadzając poniższe polecenie:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Utwórz konto usługi Azure Storage,](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) wprowadzając poniższe polecenie:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Wyodrębnij klucz podstawowy](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) z konta usługi Azure Storage i przechowuj go w zmiennej, wprowadzając poniższe polecenie:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Utwórz kontener usługi Azure Storage,](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) wprowadzając poniższe polecenie:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Tworzenie klastra HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). Przed wprowadzeniem polecenia należy zwrócić uwagę na następujące parametry:

    1. Wymagane parametry dla klastrów platformy Kafka:

        |Parametr | Opis|
        |---|---|
        |--typ|Wartością musi być **kafka**.|
        |--workernode-data-disks-per-node|Liczba dysków danych do użycia na węzeł procesu roboczego. Usługa HDInsight Kafka jest obsługiwana tylko z dyskami z danymi. Ten samouczek używa wartości **2**.|

    1. Wymagane parametry dla serwera proxy Kafka REST:

        |Parametr | Opis|
        |---|---|
        |--kafka-management-node-size|Rozmiar węzła. W tym samouczku użyto wartości **Standard_D4_v2**.|
        |--kafka-client-group-id|Identyfikator grupy zabezpieczeń AAD klienta dla serwera proxy spoczynku platformy Kafka. Wartość jest przekazywana ze zmiennej **$securityGroupID**.|
        |--kafka-client-group-name|Nazwa grupy zabezpieczeń AAD klienta dla serwera proxy spoczynku platformy Kafka. Wartość jest przekazywana ze zmiennej **$securityGroupName**.|
        |--wersja|Wersja klastra HDInsight musi mieć co najmniej 4.0. Wartość jest przekazywana ze zmiennej **$clusterVersion**.|
        |--component-version|Wersja platformy Kafka musi mieć co najmniej 2.1. Wartość jest przekazywana ze zmiennej **$componentVersion**.|
    
        Jeśli chcesz utworzyć klaster bez serwera `--kafka-management-node-size`proxy `--kafka-client-group-id`REST, wyeliminuj i `--kafka-client-group-name` z `az hdinsight create` polecenia.

    1. Jeśli masz istniejącą sieć wirtualną, dodaj parametry `--vnet-name` i `--subnet`, i ich wartości.

    Wprowadź następujące polecenie, aby utworzyć klaster:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Może upłynąć kilka minut, aby zakończyć proces tworzenia klastra. Zwykle około 15.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z artykułu warto usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

Wprowadź wszystkie lub niektóre z następujących poleceń, aby usunąć zasoby:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym utworzeniu apache Kafka REST włączone klastra w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure, użyj kodu Języka Python do interakcji z serwerem proxy REST:

> [!div class="nextstepaction"]
> [Tworzenie przykładowej aplikacji](./rest-proxy.md#client-application-sample)