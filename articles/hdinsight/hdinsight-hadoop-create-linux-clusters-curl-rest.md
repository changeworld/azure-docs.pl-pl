---
title: Tworzenie klastrów platformy Hadoop za pomocą usługi Azure API REST - Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia klastrów usługi HDInsight poprzez przesłanie szablonów usługi Azure Resource Manager w interfejsie API REST Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: larryfr
ms.openlocfilehash: 9c2779c692e944bed62d7ae9b76bb8929e62e5f3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-hadoop-clusters-using-the-azure-rest-api"></a>Tworzenie klastrów Hadoop przy użyciu interfejsu API REST Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Informacje o sposobie tworzenia klastra usługi HDInsight przy użyciu szablonu usługi Azure Resource Manager i interfejsu API REST Azure.

Interfejs API REST Azure umożliwia wykonywanie operacji zarządzania na usługi hostowanej na platformie Azure, w tym tworzenie nowych zasobów, takich jak klastry usługi HDInsight.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

> [!NOTE]
> Użyj dokumentów z krokami w tym [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) narzędzie do komunikowania się z interfejsu API REST Azure.

## <a name="create-a-template"></a>Tworzenie szablonu

Szablony usługi Azure Resource Manager są dokumentów JSON, które opisują **grupy zasobów** i wszystkie zasoby w niej (np. usługi HDInsight). Takie podejście oparty na szablonie można zdefiniować zasoby potrzebne dla usługi HDInsight w jednym szablonie.

Następujący dokument JSON jest połączenie z plików szablonu i parametry [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), który jest tworzony klaster opartych na systemie Linux przy użyciu hasła, aby zabezpieczyć konto użytkownika SSH.

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

W tym przykładzie jest używany w procedurze w tym dokumencie. Zastąp przykładzie *wartości* w **parametry** sekcji z wartościami dla klastra.

> [!IMPORTANT]
> Szablon używa domyślna liczba węzłów procesu roboczego (4) do klastra usługi HDInsight. Jeśli planujesz na więcej niż 32 węzłami procesów roboczych, musi wybierz rozmiar węzła głównego z co najmniej 8 rdzeni i 14 GB pamięci ram.
>
> Aby uzyskać więcej informacji na węzeł rozmiary i koszty, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

Wykonaj kroki opisane w temacie [wprowadzenie Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) i nawiązać połączenia z subskrypcją za pomocą `az login` polecenia.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

> [!NOTE]
> Te kroki są skróconej wersji *Tworzenie nazwy głównej usługi z hasłem* sekcji [Użyj wiersza polecenia platformy Azure można utworzyć nazwy głównej usługi dostępu do zasobów](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) dokumentu. Te kroki tworzenia nazwy głównej usługi, który jest używany do uwierzytelniania interfejsu API REST Azure.

1. Z wiersza polecenia użyj następującego polecenia, aby wyświetlić listę subskrypcji platformy Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na liście, wybierz subskrypcję, którą chcesz używać i zanotuj **IDENTYFIKATOR_SUBSKRYPCJI** i __Tenant_ID__ kolumn. Zapisz te wartości.

2. Użyj następującego polecenia, aby utworzyć aplikację w usłudze Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Zastąp wartości `--display-name`, `--homepage`, i `--identifier-uris` z własne wartości. Podaj hasło dla nowego wpisu usługi Active Directory.

   > [!NOTE]
   > `--home-page` i `--identifier-uris` wartości nie ma potrzeby odwołać rzeczywistej stronie sieci web hostowanych w Internecie. Klienci muszą mieć unikatowe identyfikatory URI.

   Wartość zwracana z tego polecenia jest __identyfikator aplikacji__ nowej aplikacji. Zapisz tę wartość.

3. Użyj następującego polecenia, aby utworzyć przy użyciu nazwy głównej usługi **identyfikator aplikacji**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Wartość zwracana z tego polecenia jest __obiektu o identyfikatorze__. Zapisz tę wartość.

4. Przypisz **właściciela** roli do głównej usługi przy użyciu **obiektu o identyfikatorze** wartość. Użyj **identyfikator subskrypcji** uzyskanymi wcześniej.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania

Użyj następującego polecenia, aby pobrać tokenu uwierzytelniania:

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

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź 200 serii i treść odpowiedzi zawiera dokument JSON.

Dokument JSON zwróconych przez to żądanie zawiera element o nazwie **' access_token '**. Wartość **' access_token '** jest używany do uwierzytelniania żądań interfejsu API REST.

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

Aby utworzyć grupę zasobów, należy użyć następującego.

* Ustaw `$SUBSCRIPTIONID` subskrypcji identyfikator otrzymane podczas tworzenia nazwy głównej usługi.
* Ustaw `$ACCESSTOKEN` do tokena dostępu została odebrana w poprzednim kroku.
* Zastąp `DATACENTERLOCATION` przy użyciu chcesz utworzyć grupę zasobów i zasobów, w centrum danych. Na przykład "południowo-środkowe Stany".
* Ustaw `$RESOURCEGROUPNAME` na nazwę chcesz użyć dla tej grupy:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź 200 serii i treść odpowiedzi zawiera dokument JSON zawierający informacje o grupie. `"provisioningState"` Element zawiera wartość `"Succeeded"`.

## <a name="create-a-deployment"></a>Tworzenie wdrożenia

Użyj następującego polecenia, aby wdrożyć szablon do grupy zasobów.

* Ustaw `$DEPLOYMENTNAME` nazwa ma zostać użyty dla tego wdrożenia.

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

Jeśli to żądanie zakończy się pomyślnie, otrzymasz odpowiedź 200 serii i treść odpowiedzi zawiera dokument JSON zawierający informacje o operacji wdrażania.

> [!IMPORTANT]
> Wdrożenie zostało przesłane, ale nie zostało ukończone. Może upłynąć kilka minut, zazwyczaj około 15 dla wdrożenia, aby zakończyć.

## <a name="check-the-status-of-a-deployment"></a>Sprawdź stan wdrożenia

Aby sprawdzić stan wdrożenia, użyj następującego polecenia:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

To polecenie zwraca dokument JSON zawierający informacje o operacji wdrażania. `"provisioningState"` Element zawiera stan wdrożenia. Jeśli ten element zawiera wartość `"Succeeded"`, a następnie wdrożenie zostało ukończone pomyślnie.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że pomyślnie utworzono klaster usługi HDInsight, użyj następującego polecenia, aby dowiedzieć się, jak pracować z klastra.

### <a name="hadoop-clusters"></a>Klastry Hadoop

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Klastrów HBase

* [Wprowadzenie do usługi HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java bazy danych hbase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry STORM

* [Tworzenie topologii Java dla Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Użyj składników języka Python w Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii z systemu Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
