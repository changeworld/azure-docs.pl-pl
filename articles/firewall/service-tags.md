---
title: Omówienie zapory usługi Azure service tags
description: W tym artykule przedstawiono omówienie procedury tagi usługi zapory usługi Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193734"
---
# <a name="azure-firewall-service-tags"></a>Usługa Azure tagi usługi zapory

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

Tagi usługi w usłudze Azure zapory może służyć w pole docelowe zasady sieciowej. Można je zamiast konkretnych adresów IP.

## <a name="supported-service-tags"></a>Obsługiwane tagi usługi

Poniżej wymienione tagi usługi są dostępne do użycia w regułach sieciowej zapory platformy Azure:

* **AzureCloud** (tylko usługa Resource Manager): ten tag określa przestrzeń adresów IP dla platformy Azure, w tym wszystkie [publiczne adresy IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653). W przypadku określenia wartości *AzureCloud* dozwolony lub blokowany jest ruch do publicznych adresów IP platformy Azure. Jeśli chcesz zezwolić na dostęp do usługi AzureCloud w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi AzureCloud platformy Azure tylko w regionie Wschodnie stany USA, możesz określić *AzureCloud.EastUS* jako tag usługi. 
* **AzureTrafficManager** (tylko usługa Resource Manager): ten tag określa przestrzeń adresów IP dla adresów IP sondy usługi Azure Traffic Manager. Więcej informacji na temat adresów IP sondy usługi Traffic Manager można znaleźć w temacie [Usługa Azure Traffic Manager — często zadawane pytania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Storage** (tylko usługa Resource Manager): ten tag określa przestrzeń adresów IP dla usługi Azure Storage. W przypadku określenia wartości *Storage* dozwolony lub blokowany jest ruch do usługi Storage. Jeśli chcesz zezwolić na dostęp do usługi Storage w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi Azure Storage tylko w regionie Wschodnie stany USA, możesz określić *Storage.EastUS* jako tag usługi. Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage.
* **SQL** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usług Azure SQL Database i Azure SQL Data Warehouse. W przypadku określenia wartości *Sql* dozwolony lub blokowany jest ruch do usługi Sql. Jeśli chcesz zezwolić na dostęp do usługi Sql w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi Azure SQL Database tylko w regionie Wschodnie stany USA, możesz określić *Sql.EastUS* jako tag usługi. Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL.
* **AzureCosmosDB** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Cosmos Database. W przypadku określenia wartości *AzureCosmosDB* dozwolony lub blokowany jest ruch do usługi AzureCosmosDB. Jeśli chcesz zezwolić na dostęp do AzureCosmosDB w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie AzureCosmosDB. [ Nazwa regionu].
* **AzureKeyVault** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi platformy Azure KeyVault. W przypadku określenia wartości *AzureKeyVault* dozwolony lub blokowany jest ruch do usługi AzureKeyVault. Jeśli chcesz zezwolić na dostęp do AzureKeyVault w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie AzureKeyVault. [ Nazwa regionu].
* **EventHub** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure EventHub. W przypadku określenia wartości *EventHub* dozwolony lub blokowany jest ruch do usługi EventHub. Jeśli chcesz zezwolić na dostęp do Centrum zdarzeń w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie EventHub. [ Nazwa regionu]. 
* **ServiceBus** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure ServiceBus. W przypadku określenia wartości *ServiceBus* dozwolony lub blokowany jest ruch do usługi ServiceBus. Jeśli chcesz zezwolić na dostęp do magistrali usług w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie magistrali usług. [ Nazwa regionu].
* **MicrosoftContainerRegistry** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Microsoft Container Registry. W przypadku określenia wartości *MicrosoftContainerRegistry* dozwolony lub blokowany jest ruch do usługi MicrosoftContainerRegistry. Jeśli chcesz zezwolić na dostęp do MicrosoftContainerRegistry w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie MicrosoftContainerRegistry. [ Nazwa regionu].
* **AzureContainerRegistry** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Container Registry. W przypadku określenia wartości *AzureContainerRegistry* dozwolony lub blokowany jest ruch do usługi AzureContainerRegistry. Jeśli chcesz zezwolić na dostęp do AzureContainerRegistry w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie AzureContainerRegistry. [ Nazwa regionu]. 
* **AppService** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure AppService. W przypadku określenia wartości *AppService* dozwolony lub blokowany jest ruch do usługi AppService. Jeśli chcesz zezwolić na dostęp do usługi App Service w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie usługi App Service. [ Nazwa regionu]. 
* **AppServiceManagement** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure AppService Management. W przypadku określenia wartości *AppServiceManagement* dozwolony lub blokowany jest ruch do usługi AppServiceManagement. 
* **ApiManagement** (tylko usługa Resource Manager): Ten tag określa prefiksy adresów usługi Azure API Management. W przypadku określenia wartości *ApiManagement* dozwolony lub blokowany jest ruch do usługi ApiManagement.  
* **AzureConnectors** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Connectors. W przypadku określenia wartości *AzureConnectors* dozwolony lub blokowany jest ruch do usługi AzureConnectors. Jeśli chcesz zezwolić na dostęp do AzureConnectors w konkretnym [region](https://azure.microsoft.com/regions), możesz określić region, w formacie AzureConnectors. [ Nazwa regionu].
* **AzureDataLake** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Data Lake. W przypadku określenia wartości *AzureDataLake* dozwolony lub blokowany jest ruch do usługi AzureDataLake.
* **AzureActiveDirectory** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi AzureActiveDirectory. W przypadku określenia wartości *AzureActiveDirectory* dozwolony lub blokowany jest ruch do usługi AzureActiveDirectory.
* **AzureMonitor** (tylko model Resource Manager): Ten tag określa prefiksy adresów usługi AzureMonitor. Jeśli określisz *AzureMonitor* wartości, ruch jest dozwolony lub zablokowany do AzureMonitor.
* **ServiceFabric** (tylko model Resource Manager): Ten tag określa prefiksy adresów usługi ServiceFabric. Jeśli określisz *ServiceFabric* wartości, ruch jest dozwolony lub zablokowany do ServiceFabric.
* **AzureMachineLearning** (tylko model Resource Manager): Ten tag określa prefiksy adresów usługi AzureMachineLearning. Jeśli określisz *AzureMachineLearning* wartości, ruch jest dozwolony lub zablokowany do AzureMachineLearning.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat reguł zapory usługi Azure, zobacz [reguły zapory na platformie Azure przetwarzanie logiki](rule-processing.md).