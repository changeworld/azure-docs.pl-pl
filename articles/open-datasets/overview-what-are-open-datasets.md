---
title: Co to są zestawy danych open? Wyselekcjonowane publicznych zestawów danych
titleSuffix: Azure Open Datasets (preview)
description: Więcej informacji na temat platformy Azure otwartych zestawów danych (wersja zapoznawcza), wyselekcjonowanych zestawy danych z domeny publicznej, które są gotowe do użycia w machine learning i rozwiązań analitycznych. Zestawy danych zawierają dane publiczne, takie jak pogody, spisu i dni wolnych od pracy oraz lokalizacji, aby pomóc Ci wzbogacić rozwiązania predykcyjnego.
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 439c25363d4c3b24b391b49811d3806c98171034
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026949"
---
# <a name="what-are-azure-open-datasets-preview-and-how-can-you-use-them"></a>Co to są otwarte zestawy danych platformy Azure (wersja zapoznawcza) i jak można ich używać?

[Azure Otwórz zestawów danych](https://azure.microsoft.com/services/open-datasets/) są wyselekcjonowanych publicznych zestawach danych, które można użyć, aby dodać funkcje specyficzne dla scenariusza do rozwiązania uczenia maszynowego dla dokładniejszych modeli. Otwieranie zestawów danych znajdują się w chmurze w systemie Microsoft Azure i są łatwo dostępne dla usługi Azure Databricks, usługi Machine Learning i usługi Machine Learning Studio. Można również dostęp do zestawów danych za pomocą interfejsów API i używać ich w innych produktów, takich jak usługa Power BI i usługi Azure Data Factory.

Zestawy danych zawierają dane domeny publicznej pogody, spisu, dni wolnych od pracy, organizacje bezpieczeństwa publicznego i lokalizacji, które pomagają szkolenie modeli uczenia maszynowego i udoskonalanie rozwiązań predykcyjne. Można również udostępniać publicznych zestawów danych w otwartych zestawy danych platformy Azure. 

![Składniki platformy Azure otwartych zestawów danych](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Wyselekcjonowane, przygotowanego zestawów danych
Wyselekcjonowanych Otwórz publicznych zestawów danych w usłudze Azure Otwórz w zestawach danych są zoptymalizowane do użycia w przepływach pracy programu machine learning. 

Analityków, którzy często spędzają większość czasu czyszczenia i przygotowania danych do zaawansowanej analizy. Otwórz zestawy danych są kopiowane do chmury platformy Azure i wstępnie przetworzony oszczędność czasu. W regularnych odstępach czasu dane są pobierane ze źródeł, takich jak przez połączenie FTP National Oceanic i Atmospheric administracji Agencją NOAA () przekształcany do formatu strukturalnego i następnie wzbogacone zgodnie z potrzebami dzięki takim funkcjom jak kod pocztowy lub lokalizacji najbliższej stacji o pogodzie.

Zestawy danych są cohosted z chmury obliczeniowej w wprowadzania dostępu na platformie Azure i manipulowania łatwiejsze.  

Poniżej przedstawiono przykładowe zestawy danych dostępne. 

### <a name="weather-data"></a>Dane o pogodzie
 
|Zestaw danych         | Notebooks     | Opis                                    |
|----------------|---------------|------------------------------------------------|
|[Danych agencji NOAA zintegrowane o urządzeniach Surface (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Na całym świecie godzinowe dane pogodowe z agencji NOAA najlepiej pokrywające przestrzennego w Ameryce Północnej, Europie, Australia i części Azja Wschodnia. Aktualizowane codziennie. |
|[NOAA Global Forecast System (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-dniowej USA godzinowe prognozę pogody danych z agencji NOAA. Aktualizowane codziennie. |

### <a name="calendar-data"></a>Dane kalendarza

|Zestaw danych         | Notebooks     | Opis                                    |
|----------------|---------------|------------------------------------------------|
|[Dni wolnych od pracy](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Dane na całym świecie świąteczny obejmujące 41 krajów lub regionów z 1970 2099. Obejmuje kraju i czy większość osób ma opłaciło czasu. |

## <a name="access-to-datasets"></a>Dostęp do zestawów danych  
Przy użyciu konta Azure, której będziesz mieć dostęp Otwórz zestawów danych przy użyciu kodu lub za pośrednictwem platformy Azure usługi interfejsu. Dane są wspólnie przechowywane przy użyciu zasobów obliczeniowych w chmurze platformy Azure do użytku w rozwiązaniu do uczenia maszynowego.  

Otwieranie zestawów danych zawiera notesy platformy Azure i usługi Azure Databricks notesów, których można użyć, aby nawiązać połączenie z danych usługi Azure Machine Learning i Azure Databricks. Zestawy danych można również uzyskać dostęp za pośrednictwem zestawu SDK języka Python. 

Jednak nie jest potrzebny otwartych zestawów danych; dostęp do konta platformy Azure mogą to być dostęp w dowolnym środowisku Python bez lub platformy Spark.

## <a name="request-or-contribute-datasets"></a>Żądania lub współtworzenia zestawów danych

Jeśli nie możesz znaleźć dane należy wiadomość e-mail nam [żądania zestawu danych](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) lub [współtworzenia dataset](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Kolejne kroki
* [Przykładowy notes](samples.md)
* [Samouczek: Modelowanie danych taksówek NY regresji](tutorial-opendatasets-automl.md)
* [Python SDK dla otwartych zestawów danych](https://aka.ms/open-datasets-api)
