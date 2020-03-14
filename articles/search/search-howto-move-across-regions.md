---
title: Jak przenieść zasób usługi między regionami
titleSuffix: Azure Cognitive Search
description: W tym artykule przedstawiono sposób przenoszenia zasobów Wyszukiwanie poznawcze platformy Azure z jednego regionu do innego w chmurze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/06/2020
ms.openlocfilehash: c31a81d2836e9f8c00dec3c0c2eb3a43800a5322
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136265"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Przenoszenie usługi Wyszukiwanie poznawcze platformy Azure do innego regionu platformy Azure

Czasami klienci zgłaszają informacje o przenoszeniu istniejącej usługi wyszukiwania do innego regionu. Obecnie nie ma żadnych wbudowanych mechanizmów i narzędzi ułatwiających to zadanie. Jest to proces ręczny przedstawiony poniżej w tym artykule.

> [!NOTE]
> W Azure Portal wszystkie usługi mają polecenie **eksportu szablonu** . W przypadku usługi Azure Wyszukiwanie poznawcze to polecenie tworzy podstawową definicję usługi (nazwę, lokalizację, warstwę, replikę i liczbę partycji), ale nie rozpoznaje zawartości usługi ani nie przeprowadzi z kluczy, ról ani dzienników. Chociaż to polecenie istnieje, nie zaleca się używania go do przeniesienia usługi wyszukiwania.

## <a name="steps-for-moving-a-service"></a>Procedura przechodzenia do usługi

Jeśli musisz przenieść usługę wyszukiwania do innego regionu, Twoje podejście powinno wyglądać podobnie do poniższego kroku:

1. Zidentyfikuj powiązane usługi, aby zrozumieć pełen wpływ zmiany lokalizacji usługi. Być może korzystasz z usługi Azure Storage do rejestrowania, sklepu wiedzy lub zewnętrznego źródła danych. Być może używasz Cognitive Services do wzbogacania AI. Dostęp do usług w innych regionach jest powszechny, ale wiąże się z dodatkowymi opłatami za przepustowość. W przypadku korzystania z wzbogacania AI Cognitive Services i Wyszukiwanie poznawcze platformy Azure muszą znajdować się w tym samym regionie.

1. Sporządź spis istniejącej usługi, aby uzyskać pełną listę obiektów w usłudze. W przypadku włączenia rejestrowania należy utworzyć i zarchiwizować wszystkie raporty, które mogą być potrzebne w rekordzie historycznym.

1. Sprawdź ceny i dostępność w nowym regionie, aby zapewnić dostępność systemu Azure Wyszukiwanie poznawcze i wszystkie powiązane usługi, które możesz chcieć utworzyć w tym samym regionie. Sprawdź, czy funkcja ma parzystość. Niektóre funkcje w wersji zapoznawczej mają ograniczoną dostępność.

1. Utwórz usługę w nowym regionie i ponownie Opublikuj z kodu źródłowego wszystkie istniejące indeksy, indeksatory, źródła danych, umiejętności, sklepy wiedzy i mapy synonimów. Nazwy usług muszą być unikatowe, aby nie można było użyć istniejącej nazwy.

1. Załaduj ponownie indeksy i magazyny wiedzy, jeśli ma to zastosowanie. Użyjesz kodu aplikacji, aby wypchnąć dane JSON do indeksu, lub ponownie uruchomić indeksatory, aby ściągnąć dokumenty ze źródeł zewnętrznych. 

1. Włącz rejestrowanie i jeśli są używane, Utwórz ponownie role zabezpieczeń.

1. Zaktualizuj aplikacje klienckie i zestawy testów, aby użyć nowej nazwy usługi i kluczy interfejsu API, i przetestuj wszystkie aplikacje.

1. Usuń starą usługę, gdy nowa usługa zostanie w pełni przetestowana i działa.

## <a name="next-steps"></a>Następne kroki

+ [Wybierz warstwę](search-sku-tier.md)
+ [Tworzenie usługi wyszukiwania](search-create-service-portal.md)
+ [Załaduj dokumenty do przeszukiwania](search-what-is-data-import.md)
+ [Włączanie rejestrowania](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->