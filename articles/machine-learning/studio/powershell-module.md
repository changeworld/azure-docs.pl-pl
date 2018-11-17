---
title: Moduł programu PowerShell dla usługi Machine Learning | Dokumentacja firmy Microsoft
description: Moduł programu PowerShell dla usługi Azure Machine Learning jest dostępny w trybie publicznej wersji zapoznawczej. Tworzenie i zarządzanie obszarami roboczymi, eksperymentów, usług sieci web i za pomocą programu PowerShell.
keywords: experiment,linear regression,machine learning algorithms,machine learning tutorial,predictive modeling techniques,data science experiment
services: machine-learning
documentationcenter: ''
author: hning86
ms.custom: (previous ms.author haining)
ms.author: amlstudiodocs
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
ms.openlocfilehash: c46b99de8a93c54246d52a3cb369fd926e7c60cd
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822045"
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Moduł programu PowerShell dla usługi Azure Machine Learning
Moduł programu PowerShell dla usługi Azure Machine Learning to zaawansowane narzędzie, które pozwala na zarządzanie obszarami roboczymi, eksperymenty, zestawy danych i klasycznych usług sieci web za pomocą programu Windows PowerShell.

Można wyświetlić dokumentację i pobrać moduł wraz z pełnym kodem źródłowym, pod [ https://aka.ms/amlps ](https://aka.ms/amlps). 

> [!NOTE]
> Moduł programu PowerShell usługi Azure Machine Learning jest obecnie w wersji zapoznawczej. Moduł będzie udoskonalany i rozwijany w trakcie tego okresu (wersja zapoznawcza). Publikujemy [pakietu Cortana Intelligence i blogu dotyczącym uczenia maszynowego](https://blogs.technet.microsoft.com/machinelearning/) wiadomości i informacje.

## <a name="what-is-the-machine-learning-powershell-module"></a>Co to jest moduł programu PowerShell usługi Machine Learning?
Moduł programu PowerShell usługi Machine Learning. Na podstawie NET moduł DLL, który umożliwia zarządzanie pełni obszary robocze usługi Azure Machine Learning, eksperymentów, zestawów danych, klasyczne usługi sieci web oraz punkty końcowe usługi sieci web klasyczne za pomocą programu Windows PowerShell. 

Wraz z modułem, można pobrać pełny kod źródłowy zawierający nie pozostawia żadnych śladów rozdzielonych [ C# warstwę interfejsu API](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Można odwoływać się do tej biblioteki DLL z projektu platformy .NET i zarządzania usługi Azure Machine Learning za pomocą kodu platformy .NET. Ponadto biblioteka DLL zależy od podstawowych interfejsów API REST, którego można użyć bezpośrednio ze swojego ulubionego klienta.

## <a name="what-can-i-do-with-the-powershell-module"></a>Co można zrobić za pomocą modułu programu PowerShell?
Oto niektóre zadania, które można wykonać za pomocą tego modułu programu PowerShell. Zapoznaj się z [pełną dokumentacją](https://aka.ms/amlps) dotyczącą tej i wielu innych funkcji.

* Aprowizacja nowego obszaru roboczego za pomocą certyfikatu zarządzania ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Eksportowanie i importowanie pliku JSON reprezentującego wykres eksperymentu ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) i [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Uruchamianie eksperymentu ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Tworzenie usługi sieci Web na podstawie eksperymentu predykcyjnego ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Utworzenie punktu końcowego usługi sieci web opublikowane ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Wywoływanie punktu końcowego usługi sieci Web RRS i/lub BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) i [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Poniżej przedstawiono prosty przykład użycia programu PowerShell do uruchamiania istniejącego eksperymentu:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Aby uzyskać bardziej szczegółowy przypadek użycia, znajduje się w artykule na temat używania modułu programu PowerShell, aby zautomatyzować często żądanego zadania: [tworzenie wielu modeli usługi Machine Learning i sieci web punktów końcowych usługi podstawie jednego eksperymentu przy użyciu programu PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Jak rozpocząć?
Aby rozpocząć korzystanie z programu PowerShell usługi Machine Learning, pobierz [pakiet wersji](https://github.com/hning86/azuremlps/releases) z serwisu GitHub i postępuj zgodnie z [instrukcjami instalacji](https://github.com/hning86/azuremlps/blob/master/README.md). Instrukcje wyjaśniają sposób odblokowanie pobranej i rozpakowanej biblioteki DLL, a następnie zaimportuj go do środowiska programu PowerShell. Większość poleceń cmdlet wymaga podania identyfikatora obszaru roboczego, tokenu autoryzacji obszaru roboczego i regionu platformy Azure, w którym znajduje się obszar roboczy. Najprostszym sposobem dostarczenia wartości jest użycie domyślnego pliku config.json. Instrukcje również wyjaśniają sposób konfigurowania tego pliku. 

Jeśli chcesz, możesz sklonować drzewo repozytorium git, zmodyfikuj kod i skompiluj go lokalnie przy użyciu programu Visual Studio.

## <a name="next-steps"></a>Kolejne kroki
Pełną dokumentację dla modułu programu PowerShell można znaleźć [ https://aka.ms/amlps ](https://aka.ms/amlps). 

Rozszerzone przykład jak używać modułu w rzeczywistych scenariuszy, zapoznaj się z przypadek użycia szczegółowe, [tworzenie wielu modeli usługi Machine Learning i sieci web punktów końcowych usługi podstawie jednego eksperymentu przy użyciu programu PowerShell](create-models-and-endpoints-with-powershell.md).
