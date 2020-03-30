---
title: Jak przenieść zasób usługi między regionami
titleSuffix: Azure Cognitive Search
description: W tym artykule pokazano, jak przenieść zasoby usługi Azure Cognitive Search z jednego regionu do drugiego w chmurze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246307"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Przenoszenie usługi Azure Cognitive Search do innego regionu platformy Azure

Od czasu do czasu klienci pytają o przeniesienie usługi wyszukiwania do innego regionu. Obecnie nie ma wbudowanego mechanizmu lub narzędzi, aby pomóc w tym zadaniu, ale ten artykuł może pomóc zrozumieć ręczne kroki w celu osiągnięcia tego samego wyniku.

> [!NOTE]
> W witrynie Azure portal wszystkie usługi mają polecenie **Eksportuj szablon.** W przypadku usługi Azure Cognitive Search to polecenie tworzy podstawową definicję usługi (nazwa, lokalizacja, warstwa, replika i liczba partycji), ale nie rozpoznaje zawartości usługi, ani nie przenosi kluczy, ról lub dzienników. Mimo że polecenie istnieje, nie zaleca się używania go do przenoszenia usługi wyszukiwania.

## <a name="guidance-for-moving-a-service"></a>Wskazówki dotyczące przenoszenia usługi

1. Identyfikowanie zależności i powiązanych usług, aby zrozumieć pełny wpływ przeniesienia usługi, w przypadku, gdy trzeba przenieść więcej niż tylko usługi Azure Cognitive Search.

   Usługa Azure Storage jest używana do rejestrowania, tworzenia magazynu wiedzy i jest powszechnie używanym zewnętrznym źródłem danych do wzbogacania i indeksowania sztucznej inteligencji. Usługi Cognitive Services to zależność w wzbogacaniu SI. Usługi Cognitive Services i usługa wyszukiwania muszą znajdować się w tym samym regionie, jeśli używasz wzbogacania si.

1. Utwórz spis wszystkich obiektów w usłudze, aby wiedzieć, co przenieść: indeksy, mapy synonimów, indeksatory, źródła danych, skillsets. Jeśli włączono rejestrowanie, utwórz i zarchiwizuj wszystkie raporty, które mogą być potrzebne do uzyskania rekordu historycznego.

1. Sprawdź ceny i dostępność w nowym regionie, aby zapewnić dostępność usługi Azure Cognitive Search oraz powiązanych usług w nowym regionie. Większość funkcji jest dostępna we wszystkich regionach, ale niektóre funkcje w wersji zapoznawczej mają ograniczoną dostępność.

1. Utwórz usługę w nowym regionie i ponownie opublikuj z kodu źródłowego wszystkie istniejące indeksy, mapy synonimów, indeksatory, źródła danych i zestawy umiejętności. Należy pamiętać, że nazwy usług muszą być unikatowe, więc nie można ponownie użyć istniejącej nazwy. Sprawdź każdy zestaw umiejętności, aby sprawdzić, czy połączenia z usługami Cognitive Services są nadal ważne pod względem wymagań tego samego regionu. Ponadto jeśli są tworzone magazyny wiedzy, sprawdź parametry połączenia dla usługi Azure Storage, jeśli używasz innej usługi.

1. W stosownych przypadkach ponownie załaduj indeksy i magazyny wiedzy. Użyjesz kodu aplikacji do wypychania danych JSON do indeksu lub ponownie uruchom indeksatory, aby pobierać dokumenty ze źródeł zewnętrznych. 

1. Włącz rejestrowanie, a jeśli ich używasz, ponownie utwórz role zabezpieczeń.

1. Zaktualizuj aplikacje klienckie i zestawy testów, aby używać nowej nazwy usługi i kluczy interfejsu API oraz przetestuj wszystkie aplikacje.

1. Usuń starą usługę, gdy nowa usługa jest w pełni przetestowana i działa.

## <a name="next-steps"></a>Następne kroki

Poniższe łącza mogą pomóc w znalezieniu więcej informacji podczas wykonywania kroków opisanych powyżej.

+ [Ceny i regiony usługi Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Wybieranie warstwy](search-sku-tier.md)
+ [Tworzenie usługi wyszukiwania](search-create-service-portal.md)
+ [Ładowanie dokumentów wyszukiwania](search-what-is-data-import.md)
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