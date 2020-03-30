---
title: Uwierzytelnianie zadania usługi Azure Stream Analytics do danych wyjściowych usługi Power BI za pomocą tożsamości zarządzanej
description: W tym artykule opisano, jak używać tożsamości zarządzanych do uwierzytelniania zadania usługi Azure Stream Analytics w danych wyjściowych usługi Power BI.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 8a7dfd7c690d79d8430f7c33a25b38949dbd06c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086324"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Uwierzytelnianie zadania usługi Azure Stream Analytics w usłudze Power BI za pomocą tożsamości zarządzanej

[Uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla danych wyjściowych usługi Power BI zapewnia zadaniaom usługi Stream Analytics bezpośredni dostęp do obszaru roboczego na koncie usługi Power BI. Ta funkcja umożliwia wdrożenie zadań usługi Stream Analytics do pełnej automatyzacji, ponieważ nie jest już wymagane dla użytkownika interaktywnie zalogować się do usługi Power BI za pośrednictwem witryny Azure portal. Ponadto długo działające zadania, które zapisują w usłudze Power BI, są teraz lepiej obsługiwane, ponieważ nie trzeba okresowo ponownie autoryzować zadania.

W tym artykule pokazano, jak włączyć tożsamość zarządzaną dla danych wyjściowych usługi Power BI zadania usługi Stream Analytics za pośrednictwem witryny Azure portal i wdrożenia usługi Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z tej funkcji wymagane są następujące elementy:

- Konto usługi Power BI z [licencją Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).

- Uaktualniony obszar roboczy na koncie usługi Power BI. Aby uzyskać więcej informacji, zobacz ogłoszenie tej funkcji w usłudze [Power BI.](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Tworzenie zadania usługi Stream Analytics przy użyciu portalu Azure

1. Utwórz nowe zadanie usługi Stream Analytics lub otwórz istniejące zadanie w witrynie Azure portal. Na pasku menu znajdującym się po lewej stronie ekranu wybierz pozycję **Tożsamość zarządzana** znajdująca się w obszarze **Konfiguruj**. Upewnij się, że wybrano opcję "Użyj tożsamości zarządzanej przypisanej do systemu", a następnie wybierz przycisk **Zapisz** u dołu ekranu.

   ![Konfigurowanie tożsamości zarządzanej usługi Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Przed skonfigurowaniem danych wyjściowych należy udzielić zadania usługi Stream Analytics dostępu do obszaru roboczego usługi Power BI, postępując zgodnie ze wskazówkami w sekcji [Nadaj zadaniu usługi Stream Analytics dostęp do obszaru roboczego usługi Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) w tym artykule.

3. Przejdź do sekcji **Wyjścia** zadania analitycznego strumienia, wybierz pozycję **+ Dodaj**, a następnie wybierz pozycję **Power BI**. Następnie wybierz przycisk **Autoryzuj** i zaloguj się za pomocą konta usługi Power BI.

   ![Autoryzowanie przy za pomocą konta usługi Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Po autoryzacji lista rozwijana zostanie wypełniona wszystkimi obszarami roboczymi, do których masz dostęp. Wybierz obszar roboczy, który został autoryzowany w poprzednim kroku. Następnie wybierz **tożsamość zarządzana** jako "Tryb uwierzytelniania". Na koniec wybierz przycisk **Zapisz.**

   ![Konfigurowanie danych wyjściowych usługi Power BI przy obliczu tożsamości zarządzanej](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

Usługa Azure Resource Manager umożliwia pełną automatyzację wdrażania zadania usługi Stream Analytics. Szablony usługi Resource Manager można wdrożyć przy użyciu programu Azure PowerShell lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Poniższe przykłady używają interfejsu wiersza polecenia platformy Azure.


1. Zasób **Microsoft.StreamAnalytics/streamingjobs** z tożsamością zarządzaną można utworzyć, dołączając następującą właściwość w sekcji zasobów szablonu Menedżera zasobów:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Ta właściwość informuje usługę Azure Resource Manager, aby utworzyć i zarządzać tożsamości dla zadania usługi Stream Analytics. Poniżej znajduje się przykładowy szablon Menedżera zasobów, który wdraża zadanie usługi Stream Analytics z włączoną tożsamością zarządzaną i ujściem wyjściowym usługi Power BI, który używa tożsamości zarządzanej:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    Wdrażanie powyższego zadania w grupie zasobów **ExampleGroup** przy użyciu poniższego polecenia interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po utworzeniu zadania użyj usługi Azure Resource Manager, aby pobrać pełną definicję zadania.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Powyższe polecenie zwróci odpowiedź podobną do poniższej:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Jeśli planujesz dodać zadanie usługi Stream Analytics do obszaru roboczego usługi Power BI za pomocą interfejsu API REST usługi Power BI, zanotuj zwrócony "principalId".

3. Teraz, gdy zadanie jest tworzone, przejdź do sekcji [Give the Stream Analytics job job to your Power BI workspace](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) section of this article.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Udzielanie dostępu do zadania usługi Stream Analytics do obszaru roboczego usługi Power BI

Teraz, gdy zostało utworzone zadanie usługi Stream Analytics, można uzyskać dostęp do obszaru roboczego usługi Power BI.

### <a name="use-the-power-bi-ui"></a>Korzystanie z interfejsu użytkownika usługi Power BI

   > [!Note]
   > Aby dodać zadanie usługi Stream Analytics do obszaru roboczego usługi Power BI przy użyciu interfejsu użytkownika, należy również włączyć dostęp jednostki usługi w **ustawieniach dewelopera** w portalu administracyjnym usługi Power BI. Zobacz [Wprowadzenie do jednostki usługi, aby](https://docs.microsoft.com/power-bi/developer/embed-service-principal#get-started-with-a-service-principal) uzyskać więcej informacji.

1. Przejdź do ustawień dostępu obszaru roboczego. Zobacz ten artykuł, aby uzyskać więcej informacji: [Udzielanie dostępu do obszaru roboczego](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Wpisz nazwę zadania usługi Stream Analytics w polu tekstowym i wybierz **pozycję Współautor** jako poziom dostępu.

3. Wybierz **pozycję Dodaj** i zamknij okienko.

   ![Dodawanie zadania usługi Stream Analytics do obszaru roboczego usługi Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Korzystanie z poleceń cmdlet programu Power BI PowerShell

1. Zainstaluj polecenia `MicrosoftPowerBIMgmt` cmdlet programu Power BI PowerShell.

   > [!Important]
   > Upewnij się, że używasz poleceń cmdlet w wersji 1.0.821 lub nowszej.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Zaloguj się do usługi Power BI.

```powershell
Login-PowerBI
```

3. Dodaj zadanie usługi Stream Analytics jako współautora do obszaru roboczego.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Korzystanie z interfejsu API REST usługi Power BI

Zadanie usługi Stream Analytics można również dodać jako współautor do obszaru roboczego za pomocą interfejsu API REST "Dodaj użytkownika grupy". Pełną dokumentację dla tego interfejsu API można znaleźć tutaj: [Grupy - Dodaj użytkownika grupy](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Przykładowe żądanie**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Treść żądania
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Ograniczenia
Poniżej znajdują się ograniczenia tej funkcji:

- Klasyczne obszary robocze usługi Power BI nie są obsługiwane.

- Konta platformy Azure bez usługi Azure Active Directory.

- Dostęp dla wielu dzierżawców nie jest obsługiwany. Podmiot zabezpieczeń usługi utworzony dla danego zadania usługi Usługi Usługi Musi znajdować się w tej samej dzierżawie usługi Azure Active Directory, w której utworzono zadanie, i nie może być używany z zasobem, który znajduje się w innej dzierżawie usługi Azure Active Directory.

- [Tożsamość przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md) nie jest obsługiwana. Oznacza to, że nie możesz wprowadzić własnego podmiotu usługi, który ma być używany przez zadanie usługi Stream Analytics. Podmiot usługi musi być generowany przez usługę Azure Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Integracja pulpitu nawigacyjnego usługi Power BI z usługą Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Opis produktów z usługi Azure Stream Analytics](./stream-analytics-define-outputs.md)
