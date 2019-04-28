---
title: Tworzenie klastrów usługi Apache Hadoop przy użyciu interfejsu API REST usługi Azure — platformy Azure
description: Dowiedz się, jak tworzyć klastry HDInsight po przesłaniu szablonów usługi Azure Resource Manager do interfejsu API REST platformy Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: hrasheed
ms.openlocfilehash: acf121c2954b3f324682578dd3ab2b4d8b1f63f2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124916"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Tworzenie klastrów usługi Apache Hadoop przy użyciu interfejsu API REST platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dowiedz się, jak utworzyć klaster usługi HDInsight przy użyciu szablonu usługi Azure Resource Manager i interfejsu API REST platformy Azure.

Interfejs API REST platformy Azure umożliwia wykonywanie operacji zarządzania w usługach hostowanych na platformie Azure, w tym tworzenia nowych zasobów, takich jak klastry HDInsight.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

> [!NOTE]  
> Kroki opisane w tej dokumentów użyj [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) narzędzie w celu komunikowania się z interfejsu API REST platformy Azure.

## <a name="create-a-template"></a>Tworzenie szablonu

Szablony usługi Azure Resource Manager są dokumentami JSON, które opisują **grupy zasobów** i wszystkie zasoby w niej (np. HDInsight). To podejście oparte na szablonach umożliwia definiowanie zasobów, które wymagają for HDInsight w jednym szablonie.

Następujący dokument JSON jest łączenie plików szablonu oraz parametrów z [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), co powoduje utworzenie klastra opartego na systemie Linux przy użyciu hasła w celu zabezpieczenia konta użytkownika SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

W tym przykładzie jest używany w krokach w tym dokumencie. Zastąp przykład *wartości* w **parametry** sekcji z wartościami dla klastra.

> [!IMPORTANT]  
> Szablon używa domyślną liczbę węzłów procesu roboczego (4) dla klastra usługi HDInsight. Jeśli planowane jest na więcej niż 32 węzły procesu roboczego, musisz wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci ram.
>
> Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

Wykonaj kroki opisane w temacie [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) i nawiąż połączenie z subskrypcją za pomocą `az login` polecenia.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

> [!NOTE]  
> Te kroki są skrócone wersję *Tworzenie jednostki usługi przy użyciu hasła* części [interfejsu wiersza polecenia użyj Azure, aby utworzyć jednostkę usługi, aby uzyskiwać dostęp do zasobów](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentu. Te kroki tworzenia jednostki usługi, który jest używany do uwierzytelniania interfejsu API REST platformy Azure.

1. Z poziomu wiersza polecenia użyj następującego polecenia, aby wyświetlić listę subskrypcji platformy Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na liście, wybierz subskrypcję, dla której chcesz użyć i zanotuj **Subscription_ID** i __Tenant_ID__ kolumn. Zapisz te wartości.

2. Użyj następującego polecenia, aby utworzyć aplikację w usłudze Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Zastąp wartości `--display-name`, `--homepage`, i `--identifier-uris` własnymi wartościami. Podaj hasło dla nowego wpisu w usłudze Active Directory.

   > [!NOTE]  
   > `--home-page` i `--identifier-uris` wartości nie muszą odwoływać się do rzeczywistej stronie sieci web hostowanych w Internecie. Klienci muszą mieć unikatowe identyfikatory URI.

   Wartość zwracana z tego polecenia jest __Identyfikatora aplikacji__ nowej aplikacji. Zapisz tę wartość.

3. Użyj następującego polecenia, aby utworzyć nazwę główną usługi za pomocą **Identyfikatora aplikacji**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Wartość zwracana z tego polecenia jest __obiektu o identyfikatorze__. Zapisz tę wartość.

4. Przypisz **właściciela** roli do jednostki usługi przy użyciu **obiektu o identyfikatorze** wartość. Użyj **identyfikator subskrypcji** uzyskanymi wcześniej.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania

Użyj następującego polecenia, aby można było pobrać tokenu uwierzytelniania:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Ustaw `$TENANTID`, `$APPID`, i `$PASSWORD` wartości uzyskane lub użyte wcześniej.

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź serię 200 i treść odpowiedzi zawiera dokument JSON.

Dokument JSON, w zwróconym przez to żądanie zawiera element o nazwie **access_token**. Wartość **access_token** jest używany do uwierzytelniania żądań interfejsu API REST.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Użyj następującego polecenia do utworzenia grupy zasobów.

* Ustaw `$SUBSCRIPTIONID` do subskrypcji identyfikator otrzymane podczas tworzenia jednostki usługi.
* Ustaw `$ACCESSTOKEN` do tokena dostępu, odbierane w poprzednim kroku.
* Zastąp `DATACENTERLOCATION` za pomocą chcesz utworzyć grupę zasobów i zasobów, w centrum danych. Na przykład "Południowo-środkowe stany USA".
* Ustaw `$RESOURCEGROUPNAME` nazwy, o których chcesz użyć dla tej grupy:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź serię 200 i treść odpowiedzi zawiera dokument JSON zawierający informacje o grupie. `"provisioningState"` Element zawiera wartość `"Succeeded"`.

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Użyj następującego polecenia, aby wdrożyć szablon do grupy zasobów.

* Ustaw `$DEPLOYMENTNAME` nazwy, o których chcesz użyć dla tego wdrożenia.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Jeśli szablon został zapisany do pliku, można użyć następującego polecenia zamiast `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź serię 200 i treść odpowiedzi zawiera dokument JSON zawierający informacje o operacji wdrażania.

> [!IMPORTANT]  
> Wdrożenie zostało przesłane, ale nie została ukończona. Może potrwać kilka minut, zwykle około 15 na zakończenie wdrożenia.

## <a name="check-the-status-of-a-deployment"></a>Sprawdź stan wdrożenia

Aby sprawdzić stan wdrożenia, użyj następującego polecenia:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

To polecenie zwraca dokument JSON zawierający informacje o operacji wdrażania. `"provisioningState"` Element zawiera stan wdrożenia. Jeśli ten element zawiera wartość `"Succeeded"`, a następnie wdrożenia została ukończona pomyślnie.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się utworzyć klaster usługi HDInsight, użyj następującego polecenia na temat sposobu pracy z klastrem.

### <a name="apache-hadoop-clusters"></a>Klastry platformy Apache Hadoop

* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Rozpoczynanie pracy z usługą Apache HBase na HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Twórz aplikacje Java dla bazy danych Apache HBase na HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastrów Apache Storm

* [Opracowywanie topologii języka Java dla usługi Storm Apache na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Storm Apache na HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Storm Apache na HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
