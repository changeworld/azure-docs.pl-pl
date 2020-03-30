---
title: Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API usługi Azure REST — Azure
description: Dowiedz się, jak tworzyć klastry usługi HDInsight, przesyłając szablony usługi Azure Resource Manager do interfejsu API rest platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 2680304bd73bdbae35b29b89f38ae2665615f5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239921"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API rest platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dowiedz się, jak utworzyć klaster USŁUGI HDInsight przy użyciu szablonu usługi Azure Resource Manager i interfejsu API rest platformy Azure.

Interfejs API rest platformy Azure umożliwia wykonywanie operacji zarządzania w usługach hostowanych na platformie Azure, w tym tworzenie nowych zasobów, takich jak klastry HDInsight.

> [!NOTE]  
> Kroki w tym dokumencie użyć [curl (narzędziehttps://curl.haxx.se/) ](https://curl.haxx.se/) do komunikowania się z interfejsu API rest platformy Azure.

## <a name="create-a-template"></a>Tworzenie szablonu

Szablony usługi Azure Resource Manager to dokumenty JSON opisujące **grupę zasobów** i wszystkie zasoby w niej (takie jak HDInsight). To podejście oparte na szablonie umożliwia zdefiniowanie zasobów potrzebnych do hdinsight w jednym szablonie.

Poniższy dokument JSON jest połączeniem szablonu [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)i parametrów plików z programu , który tworzy klaster oparty na systemie Linux przy użyciu hasła do zabezpieczenia konta użytkownika SSH.

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
                                   "vmSize": "{}" 
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
                                   "vmSize": "{}"
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

Ten przykład jest używany w krokach w tym dokumencie. Zastąp *przykładowe wartości* w sekcji **Parametry** wartościami klastra.

> [!IMPORTANT]  
> Szablon używa domyślnej liczby węzłów procesu roboczego (4) dla klastra HDInsight. Jeśli planujesz więcej niż 32 węzłów procesu roboczego, należy wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
> Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

Wykonaj kroki opisane w [wprowadzenie do interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) i połącz się z subskrypcją `az login` za pomocą polecenia.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

> [!NOTE]  
> Te kroki są skróconą wersją *create service principal z hasłem* sekcji Użyj interfejsu [wiersza polecenia platformy Azure, aby utworzyć jednostkę usługi, aby uzyskać dostęp do](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentu zasobów. Te kroki tworzą jednostkę usługi, która jest używana do uwierzytelniania w interfejsie API rest platformy Azure.

1. W wierszu polecenia użyj następującego polecenia, aby wyświetlić listę subskrypcji platformy Azure.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na liście wybierz subskrypcję, której chcesz użyć, i zanotuj **kolumny Subscription_ID** i __Tenant_ID.__ Zapisz te wartości.

2. Użyj następującego polecenia, aby utworzyć aplikację w usłudze Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Zastąp `--display-name`wartości `--homepage`dla `--identifier-uris` , i własnych wartości. Podaj hasło do nowego wpisu usługi Active Directory.

   > [!NOTE]  
   > Wartości `--home-page` `--identifier-uris` i wartości nie muszą odwoływać się do rzeczywistej strony internetowej hostowanej w Internecie. Muszą to być unikatowe identyfikatory URI.

   Wartość zwrócona z tego polecenia jest __identyfikator aplikacji__ dla nowej aplikacji. Zapisz tę wartość.

3. Użyj następującego polecenia, aby utworzyć jednostkę usługi przy użyciu **identyfikatora aplikacji**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Wartością zwróconą z tego polecenia jest __identyfikator obiektu__. Zapisz tę wartość.

4. Przypisz rolę **Właściciel** do jednostki usługi przy użyciu wartości **identyfikatora obiektu.** Użyj identyfikatora **subskrypcji uzyskanego** wcześniej.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Pobierz token uwierzytelniania

Użyj następującego polecenia, aby pobrać token uwierzytelniania:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Ustaw `$TENANTID` `$APPID`, `$PASSWORD` oraz wartości uzyskane lub użyte wcześniej.

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź serii 200, a treść odpowiedzi zawiera dokument JSON.

Dokument JSON zwrócony przez to żądanie zawiera element o nazwie **access_token**. Wartość **access_token** jest używana do żądań uwierzytelniania do interfejsu API REST.

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

Aby utworzyć grupę zasobów, użyj następujących czynności.

* Ustaw `$SUBSCRIPTIONID` identyfikator subskrypcji odebrany podczas tworzenia jednostki usługi.
* Ustaw `$ACCESSTOKEN` token dostępu odebrany w poprzednim kroku.
* Zamień `DATACENTERLOCATION` centrum danych, w którym chcesz utworzyć grupę zasobów i zasoby. Na przykład "Południowo-środkowe stany USA".
* Ustaw `$RESOURCEGROUPNAME` nazwę, której chcesz użyć dla tej grupy:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź serii 200, a treść odpowiedzi zawiera dokument JSON zawierający informacje o grupie. Element `"provisioningState"` zawiera wartość `"Succeeded"`. .

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Użyj następującego polecenia, aby wdrożyć szablon w grupie zasobów.

* Ustaw `$DEPLOYMENTNAME` nazwę, której chcesz użyć dla tego wdrożenia.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Jeśli szablon został zapisany w pliku, można użyć następującego `-d "{ template and parameters}"`polecenia zamiast:
>
> `--data-binary "@/path/to/file.json"`

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź serii 200, a treść odpowiedzi zawiera dokument JSON zawierający informacje o operacji wdrażania.

> [!IMPORTANT]  
> Wdrożenie zostało przesłane, ale nie zostało ukończone. Wdrożenie może potrwać kilka minut, zwykle około 15.

## <a name="check-the-status-of-a-deployment"></a>Sprawdzanie stanu wdrożenia

Aby sprawdzić stan wdrożenia, użyj następującego polecenia:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

To polecenie zwraca dokument JSON zawierający informacje o operacji wdrażania. Element `"provisioningState"` zawiera stan wdrożenia. Jeśli ten element zawiera `"Succeeded"`wartość , a następnie wdrożenie zostało zakończone pomyślnie.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy pomyślnie utworzono klaster HDInsight, skorzystaj z poniższych czynności, aby dowiedzieć się, jak pracować z klastrem.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop klastrów

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do Apache HBase w programie HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla Apache HBase w programie HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastry Burzanych Apache

* [Opracowanie topologii Java dla Apache Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w aplikacji Apache Storm w programie HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii dzięki Apache Storm na hdinsight](storm/apache-storm-deploy-monitor-topology-linux.md)
