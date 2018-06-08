---
title: Moduł programu PowerShell dla usługi Machine Learning | Dokumentacja firmy Microsoft
description: Moduł programu PowerShell dla usługi Azure Machine Learning jest dostępny w trybie publicznej wersji zapoznawczej. Tworzenie i zarządzanie nimi obszarów roboczych, eksperymentów, usług sieci web i za pomocą programu PowerShell.
keywords: experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques,data science experiment
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: 6ecd2d9a1519cd89058385ad1e40aee9b3fc9082
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835699"
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Moduł programu PowerShell dla usługi Azure Machine Learning
Moduł programu PowerShell dla usługi Azure Machine Learning to zaawansowane narzędzie, które umożliwia przy użyciu programu Windows PowerShell do zarządzania obszarami roboczymi, eksperymentów, zestawy danych i usług sieci web klasycznego.

Można zapoznaj się z dokumentacją i pobrać moduł, wraz z kodu źródłowego w [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Moduł programu PowerShell usługi Azure Machine Learning jest obecnie w wersji zapoznawczej. Moduł będzie udoskonalić i rozwinięty w tym okresie podglądu. Śledzą [Cortana Intelligence i Machine Learning blogu](https://blogs.technet.microsoft.com/machinelearning/) wiadomości lub informacji.

## <a name="what-is-the-machine-learning-powershell-module"></a>Co to jest moduł programu PowerShell usługi Machine Learning?
Moduł programu PowerShell Learning maszyny. Oparte na sieci DLL moduł, który umożliwia zarządzanie pełni obszarów roboczych usługi Azure Machine Learning, eksperymenty zestawów danych, usługi sieci web klasycznego i punktów końcowych usługi sieci web klasycznego ze środowiska Windows PowerShell. 

Wraz z modułu, można pobrać kodu źródłowego, który obejmuje prawidłowo rozdzielonych [warstwę interfejsu API języka C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Możesz odwoływać się do tej biblioteki DLL z projektu platformy .NET i zarządzać uczenia maszynowego Azure za pomocą kodu platformy .NET. Ponadto biblioteki DLL jest zależna od podstawowych interfejsów API REST używaną bezpośrednio z ulubionych klienta.

## <a name="what-can-i-do-with-the-powershell-module"></a>Co można zrobić za pomocą modułu programu PowerShell?
Oto niektóre zadania, które można wykonać za pomocą tego modułu programu PowerShell. Zapoznaj się z [pełną dokumentacją](https://aka.ms/amlps) dotyczącą tej i wielu innych funkcji.

* Aprowizacja nowego obszaru roboczego za pomocą certyfikatu zarządzania ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Eksportowanie i importowanie pliku JSON reprezentującego wykres eksperymentu ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) i [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Uruchamianie eksperymentu ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Tworzenie usługi sieci Web na podstawie eksperymentu predykcyjnego ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Tworzenie punktu końcowego w usłudze opublikowanych w sieci web ([AmlWebServiceEndpoint Dodaj](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Wywoływanie punktu końcowego usługi sieci Web RRS i/lub BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) i [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Poniżej przedstawiono prosty przykład użycia programu PowerShell do uruchamiania istniejącego eksperymentu:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Aby uzyskać więcej informacji na temat przypadek użycia, zobacz w tym artykule przy użyciu modułu programu PowerShell, aby zautomatyzować zadanie najczęściej wymagane: [utworzyć wiele modeli uczenia maszynowego i sieci web punktów końcowych usługi z doświadczenia przy użyciu programu PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Jak rozpocząć?
Aby rozpocząć korzystanie z programu PowerShell usługi Machine Learning, pobierz [pakiet wersji](https://github.com/hning86/azuremlps/releases) z serwisu GitHub i postępuj zgodnie z [instrukcjami instalacji](https://github.com/hning86/azuremlps/blob/master/README.md). Instrukcje wyjaśniają sposób odblokowania pobrane rozpakowane biblioteki DLL, a następnie zaimportuj go do środowiska PowerShell. Większość poleceń cmdlet wymaga podania identyfikatora obszaru roboczego, tokenu autoryzacji obszaru roboczego i regionu platformy Azure, w którym znajduje się obszar roboczy. Najprostszym sposobem, aby podać wartości jest użycie domyślnego pliku config.json. Instrukcje również wyjaśniają sposób konfigurowania tego pliku. 

Jeśli chcesz, można sklonować drzewa git zmodyfikować kod i skompiluj go lokalnie za pomocą programu Visual Studio.

## <a name="next-steps"></a>Kolejne kroki
Pełną dokumentację dla modułu programu PowerShell można znaleźć [ https://aka.ms/amlps ](https://aka.ms/amlps). 

Rozszerzone przykład sposobu użycia modułu w rzeczywistych scenariuszy, zapoznaj się z przypadek użycia szczegółowych, [utworzyć wiele modeli uczenia maszynowego i sieci web punktów końcowych usługi z doświadczenia przy użyciu programu PowerShell](create-models-and-endpoints-with-powershell.md).
