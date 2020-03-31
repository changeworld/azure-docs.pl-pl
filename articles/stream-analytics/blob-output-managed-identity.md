---
title: Uwierzytelnij dane wyjściowe obiektu blob za pomocą usługi Managed Identity Azure Stream Analytics
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania usługi Azure Stream Analytics na wyjściu magazynu obiektów Blob platformy Azure.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129975"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Uwierzytelnianie zadania usługi Azure Stream Analytics w usłudze Azure Blob Storage za pomocą tożsamości zarządzanej

[Uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla danych wyjściowych do magazynu obiektów Blob platformy Azure daje zadania usługi Stream Analytics bezpośredni dostęp do konta magazynu zamiast przy użyciu ciągu połączenia. Oprócz poprawy zabezpieczeń ta funkcja umożliwia również zapisywanie danych na koncie magazynu w sieci wirtualnej (VNET) na platformie Azure.

W tym artykule pokazano, jak włączyć tożsamość zarządzaną dla danych wyjściowych obiektów blob zadania usługi Stream Analytics za pośrednictwem witryny Azure portal i za pośrednictwem wdrożenia usługi Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Tworzenie zadania usługi Stream Analytics przy użyciu portalu Azure

1. Utwórz nowe zadanie usługi Stream Analytics lub otwórz istniejące zadanie w witrynie Azure portal. Na pasku menu znajdującym się po lewej stronie ekranu wybierz pozycję **Tożsamość zarządzana** znajdująca się w obszarze **Konfiguruj**. Upewnij się, że wybrano opcję "Użyj tożsamości zarządzanej przypisanej do systemu", a następnie kliknij przycisk **Zapisz** u dołu ekranu.

   ![Konfigurowanie tożsamości zarządzanej usługi Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. W oknie właściwości danych wyjściowych ujścia danych wyjściowych magazynu obiektów Blob wybierz pozycję rozwijaną Tryb uwierzytelniania i wybierz pozycję **Tożsamość zarządzana**. Aby uzyskać informacje dotyczące innych właściwości danych wyjściowych, zobacz [Opis danych wyjściowych z usługi Azure Stream Analytics.](./stream-analytics-define-outputs.md) Po zakończeniu kliknij pozycję **Zapisz**.

   ![Konfigurowanie danych wyjściowych magazynu obiektów Blob platformy Azure](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Teraz, gdy zadanie zostanie utworzone, zobacz [Give the Stream Analytics zadanie dostępu do konta magazynu w](#give-the-stream-analytics-job-access-to-your-storage-account) tym artykule.

## <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

Korzystanie z usługi Azure Resource Manager umożliwia pełną automatyzację wdrażania zadania usługi Stream Analytics. Szablony usługi Resource Manager można wdrożyć przy użyciu programu Azure PowerShell lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Poniższe przykłady używają interfejsu wiersza polecenia platformy Azure.


1. Zasób **Microsoft.StreamAnalytics/streamingjobs** z tożsamością zarządzaną można utworzyć, dołączając następującą właściwość w sekcji zasobów szablonu Menedżera zasobów:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ta właściwość informuje usługę Azure Resource Manager, aby utworzyć i zarządzać tożsamości dla zadania usługi Stream Analytics. Poniżej znajduje się przykładowy szablon Menedżera zasobów, który wdraża zadanie usługi Stream Analytics z włączoną tożsamością zarządzaną i ujściem wyjściowym obiektu Blob, który używa tożsamości zarządzanej:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Powyższe zadanie można wdrożyć w grupie zasobów **ExampleGroup** przy użyciu poniższego polecenia interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po utworzeniu zadania można użyć usługi Azure Resource Manager, aby pobrać pełną definicję zadania.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Powyższe polecenie zwróci odpowiedź podobną do poniższej:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Zanotuj **identyfikator główny** z definicji zadania, który identyfikuje zarządzaną tożsamość zadania w usłudze Azure Active Directory i będzie używany w następnym kroku w celu przyznania zadania usługi Stream Analytics dostępu do konta magazynu.

3. Teraz, gdy zadanie zostanie utworzone, zobacz [Give the Stream Analytics zadanie dostępu do konta magazynu w](#give-the-stream-analytics-job-access-to-your-storage-account) tym artykule.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Zapewnij zadanie usługi Stream Analytics dostęp do swojego konta magazynu

Istnieją dwa poziomy dostępu, które możesz wybrać, aby nadać zadanie usługi Stream Analytics:

1. **Dostęp na poziomie kontenera:** ta opcja daje zadanie dostęp do określonego istniejącego kontenera.
2. **Dostęp na poziomie konta:** ta opcja daje zadanie ogólny dostęp do konta magazynu, w tym możliwość tworzenia nowych kontenerów.

Jeśli nie potrzebujesz zadania do tworzenia kontenerów w Twoim imieniu, należy wybrać **dostęp na poziomie kontenera,** ponieważ ta opcja zapewni zadaniu minimalny wymagany poziom dostępu. Obie opcje są wyjaśnione poniżej dla witryny Azure portal i wiersza polecenia.

### <a name="grant-access-via-the-azure-portal"></a>Udziel dostępu za pośrednictwem portalu Azure

#### <a name="container-level-access"></a>Dostęp na poziomie kontenera

1. Przejdź do okienka konfiguracji kontenera na koncie magazynu.

2. Wybierz **kontrolę dostępu (IAM)** po lewej stronie.

3. W sekcji "Dodawanie przypisania roli" kliknij pozycję **Dodaj**.

4. W okienku przypisania roli:

    1. Ustaw **rolę** na "Współautor danych obiektów blob magazynu"
    2. Upewnij **się,** że z listy rozwijanej Przypisanie dostępu do usługi Azure AD jest ustawiona na "Użytkownik, grupa lub podmiot usługi Azure AD".
    3. Wpisz nazwę zadania usługi Stream Analytics w polu wyszukiwania.
    4. Wybierz zadanie usługi Stream Analytics i kliknij przycisk **Zapisz**.

   ![Udziel dostępu do kontenera](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Dostęp do poziomu konta

1. Przejdź do swojego konta magazynu.

2. Wybierz **kontrolę dostępu (IAM)** po lewej stronie.

3. W sekcji "Dodawanie przypisania roli" kliknij pozycję **Dodaj**.

4. W okienku przypisania roli:

    1. Ustaw **rolę** na "Współautor danych obiektów blob magazynu"
    2. Upewnij **się,** że z listy rozwijanej Przypisanie dostępu do usługi Azure AD jest ustawiona na "Użytkownik, grupa lub podmiot usługi Azure AD".
    3. Wpisz nazwę zadania usługi Stream Analytics w polu wyszukiwania.
    4. Wybierz zadanie usługi Stream Analytics i kliknij przycisk **Zapisz**.

   ![Uzyskaj dostęp do konta](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Udzielanie dostępu za pośrednictwem wiersza polecenia

#### <a name="container-level-access"></a>Dostęp na poziomie kontenera

Aby udzielić dostępu do określonego kontenera, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Dostęp do poziomu konta

Aby udzielić dostępu do całego konta, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Włączanie dostępu do sieci wirtualnej

Podczas konfigurowania **zapór i sieci wirtualnych**konta magazynu można opcjonalnie zezwolić na ruch sieciowy z innych zaufanych usług firmy Microsoft. Gdy usługa Stream Analytics uwierzytelnia się przy użyciu tożsamości zarządzanej, dostarcza dowodu, że żądanie pochodzi z zaufanej usługi. Poniżej znajdują się instrukcje, aby włączyć ten wyjątek dostępu do sieci wirtualnej.

1.  Przejdź do okienka "Zapory i sieci wirtualne" w okienku konfiguracji konta magazynu.
2.  Upewnij się, że jest włączona opcja "Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu".
3.  Jeśli została włączona, kliknij przycisk **Zapisz**.

   ![Włączanie dostępu do sieci wirtualnej](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Ograniczenia
Poniżej znajdują się aktualne ograniczenia tej funkcji:

1. Klasyczne konta usługi Azure Storage.

2. Konta platformy Azure bez usługi Azure Active Directory.

3. Dostęp dla wielu dzierżawców nie jest obsługiwany. Podmiot zabezpieczeń usługi utworzony dla danego zadania usługi Usługi Usługi Musi znajdować się w tej samej dzierżawie usługi Azure Active Directory, w której utworzono zadanie, i nie może być używany z zasobem, który znajduje się w innej dzierżawie usługi Azure Active Directory.

4. [Tożsamość przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md) nie jest obsługiwana. Oznacza to, że użytkownik nie może wprowadzić własnego podmiotu usługi, który ma być używany przez zadanie usługi Stream Analytics. Podmiot usługi musi być generowany przez usługę Azure Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Opis produktów z usługi Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Partycjonowanie danych wyjściowych niestandardowych obiektów blob usługi Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
