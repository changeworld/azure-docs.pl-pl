---
title: Co to są otwarte zestawy danych? Wyselekcjonowane publiczne zestawy danych
titleSuffix: Azure Open Datasets
description: Dowiedz się więcej o otwartych zestawach danych platformy Azure, wyselekcjonowanych zestawach danych z domeny publicznej, takich jak pogoda, spis, święta i lokalizacja, aby wzbogacić rozwiązania predykcyjne.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: fd5697f9c325dc4ad866c333ce1b20e008ebfa24
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73606156"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Czym są zestawy danych azure open i jak można z nich korzystać?

[Zestawy danych azure open](https://azure.microsoft.com/services/open-datasets/) są wyselekcjonowane publicznych zestawów danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego dla bardziej dokładnych modeli. Otwarte zestawy danych znajdują się w chmurze na platformie Microsoft Azure i są zintegrowane z usługą Azure Machine Learning i są łatwo dostępne dla usługi Azure Databricks i Machine Learning Studio (klasyczne). Można również uzyskać dostęp do zestawów danych za pośrednictwem interfejsów API i używać ich w innych produktach, takich jak Usługa Power BI i usługa Azure Data Factory.

Zestawy danych obejmują dane z domeny publicznej dotyczące pogody, spisu ludności, świąt, bezpieczeństwa publicznego i lokalizacji, które pomagają szkolić modele uczenia maszynowego i wzbogacać rozwiązania predykcyjne. Można również udostępniać zestawy danych publicznych w zestawach danych Azure Open. 

![Składniki otwartych zestawów danych platformy Azure](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Wyselekcjonowane, przygotowane zestawy danych
Wyselekcjonowane otwarte zestawy danych publicznych w zestawach danych Azure Open są zoptymalizowane pod kątem zużycia w przepływach pracy uczenia maszynowego. 

Analitycy danych często spędzają większość czasu na czyszczeniu i przygotowywaniu danych do zaawansowanej analizy. Otwarte zestawy danych są kopiowane do chmury platformy Azure i wstępnie przetwarzane, aby zaoszczędzić czas. W regularnych odstępach czasu dane są pobierane ze źródeł, na przykład przez połączenie FTP z National Oceanic and Atmospheric Administration (NOAA). Następnie dane są analizowane w formacie strukturalnym, a następnie wzbogacane odpowiednio o funkcje, takie jak kod pocztowy lub lokalizacja najbliższej stacji meteorologicznej.

Zestawy danych są współtworzone za pomocą obliczeń w chmurze na platformie Azure, ułatwiając dostęp i manipulację.  

Poniżej przedstawiono przykłady dostępnych zestawów danych. 

### <a name="weather-data"></a>Dane pogodowe
 
|Dataset         | Notebooks     | Opis                                    |
|----------------|---------------|------------------------------------------------|
|[Zintegrowane dane powierzchniOWE NOAA (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Na całym świecie godzinne dane pogodowe z NOAA z najlepszym zasięgiem przestrzennym w Ameryce Północnej, Europie, Australii i części Azji. Aktualizowane codziennie. |
|[Globalny System Prognoz NOAA (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-dniowe dane o prognozie pogody w USA z NOAA. Aktualizowane codziennie. |

### <a name="calendar-data"></a>Dane kalendarza

|Dataset         | Notebooks     | Opis                                    |
|----------------|---------------|------------------------------------------------|
|[Święta](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Dane dotyczące dni wolnych od pracy na całym świecie, obejmujące 41 krajów lub regionów w latach 1970–2099. Obejmuje kraj i to, czy większość ludzi ma płatny czas wolny. |

## <a name="access-to-datasets"></a>Dostęp do zestawów danych  
Za pomocą konta platformy Azure można uzyskać dostęp do otwartych zestawów danych przy użyciu kodu lub interfejsu usługi platformy Azure. Dane są współlokowane z zasobami obliczeniowymi w chmurze platformy Azure do użycia w rozwiązaniu uczenia maszynowego.  

Otwarte zestawy danych są dostępne za pośrednictwem interfejsu użytkownika usługi Azure Machine Learning i zestawu SDK. Open Datasets udostępnia również notesy azure i usługi Azure Databricks, których można używać do łączenia danych z usługą Azure Machine Learning i Azure Databricks. Zestawy danych są również dostępne za pośrednictwem zestawu SDK języka Python. 

Jednak nie potrzebujesz konta platformy Azure, aby uzyskać dostęp do otwartych zestawów danych; można uzyskać do nich dostęp z dowolnego środowiska Python z lub bez Spark.

## <a name="request-or-contribute-datasets"></a>Żądania lub współtworzenia zestawów danych

Jeśli nie możesz znaleźć żądanych danych, wyślij do nas wiadomość e-mail [z prośbą o zestaw danych](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) lub [współtworzysz zestaw danych.](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) 

## <a name="next-steps"></a>Następne kroki
* [Przykładowy notes](samples.md)
* [Samouczek: Modelowanie regresji z danymi taksówek NY](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Zestaw SDK języka Python dla otwartych zestawów danych](/python/api/azureml-opendatasets/?view=azure-ml-py)
