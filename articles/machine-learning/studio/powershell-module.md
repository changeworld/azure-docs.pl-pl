---
title: Moduły programu PowerShell dla usługi Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Tworzenie i zarządzanie nimi w usłudze Azure Machine Learning Studio obszarów roboczych, eksperymentów, usług sieci web i więcej za pomocą programu PowerShell.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: bee42f8a9582908963c0eef95a2fd04742cd425e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205658"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Moduły programu PowerShell dla usługi Azure Machine Learning Studio

Korzystanie z modułów programu PowerShell, można programowo zarządzać Studio zasobów i zasoby, takie jak obszary robocze, zestawów danych i usług sieci web.

Możesz porozmawiać z zasobów Studio przy użyciu trzech modułów programu Powershell:

* [Azure PowerShell Az](#az-rm) wydanej w 2018 r., obejmuje wszystkie funkcje usługi AzureRM, mimo że za pomocą nazwy różnych poleceń cmdlet
* [AzureRM](#az-rm) wydanej w 2016 r., zastępuje Az programu PowerShell
* [Klasyczny Portal Azure programu PowerShell usługi Machine Learning](#classic) udostępnione w 2016

Mimo że te moduły programu PowerShell mają pewne podobieństwa, każdy jest przeznaczony dla konkretnych scenariuszach. W tym artykule opisano różnice między modułów programu PowerShell i ułatwia podjęcie decyzji, które z nich do wyboru.  

Sprawdź [tabeli obsługi](#support-table) poniżej, aby zobaczyć, jakie zasoby są obsługiwane przez każdego modułu. 

## <a name="az-rm"></a> Azure PowerShell Az i AzureRM

AZ jest teraz moduł programu PowerShell przeznaczone do interakcji z platformą Azure i zawiera wszystkie poprzednie funkcjonalności usługi AzureRM. AzureRM będą w dalszym ciągu otrzymywać poprawek, ale będzie ona otrzymywać nie nowych poleceń cmdlet lub funkcji.  AZ i AzureRM Zarządzanie obu rozwiązań wdrożonych przy użyciu **usługi Azure Resource Manager** modelu wdrażania. Te zasoby obejmują obszary robocze Studio i usług internetowych "New" Studio. 

PowerShell — Model Klasyczny można zainstalować obok Az albo AzureRM opisane zostały oba typy zasobów "new" i "klasyczny". Jednak nie zaleca się mieć Az i AzureRM zainstalowane w tym samym czasie. Aby wybrać między Az i AzureRM, firma Microsoft zaleca Az we wszystkich przyszłych wdrożeniach.  Dowiedz się więcej o Az i AzureRM oraz ścieżki migracji w [wprowadzenie do programu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Aby rozpocząć pracę za pomocą polecenia Az, postępuj zgodnie z [instrukcje instalacji Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell — Model Klasyczny

Studio [moduł programu PowerShell klasycznego](https://aka.ms/amlps) umożliwia zarządzanie zasobów wdrożonych przy użyciu **klasycznego modelu wdrażania**. Te zasoby obejmują Studio użytkownika zasoby, "klasycznych" usług sieci web i punkty końcowe usługi sieci web "klasyczny".

Jednak firma Microsoft zaleca użycie modelu wdrażania usługi Resource Manager dla wszystkich przyszłych zasobów można Uproszczenie wdrażania i zarządzania zasobami. Jeśli chcesz dowiedzieć się więcej na temat modeli wdrażania, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artykułu.

Aby rozpocząć pracę z PowerShell — Model Klasyczny, Pobierz [pakiet wydania](https://github.com/hning86/azuremlps/releases) z serwisu GitHub i postępuj zgodnie z [instrukcjami dotyczącymi instalacji](https://github.com/hning86/azuremlps/blob/master/README.md). Instrukcje wyjaśniają sposób odblokowanie pobranej i rozpakowanej biblioteki DLL, a następnie zaimportuj go do środowiska programu PowerShell.

PowerShell — Model Klasyczny można zainstalować obok Az albo AzureRM opisane zostały oba typy zasobów "new" i "klasyczny".

## <a name="support-table"></a> Tabela obsługi programu PowerShell


| | **Az** |  **PowerShell — model klasyczny** |
| --- | --- | --- |
| Tworzenie/usuwanie obszarów roboczych | [Szablony usługi Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Zarządzaj planami zobowiązania obszaru roboczego | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Zarządzanie użytkownikami obszaru roboczego |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Zarządzanie usługami sieci Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nowe" usługi internetowe)|| [Nowe AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasycznych" usług sieci web) |
| Zarządzanie klucze punkty końcowe usługi sieci web |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Zarządzanie modelami zestawy danych/uczony użytkownika| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Zarządzanie doświadczenia użytkownika |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Zarządzanie moduły niestandardowe | | [Nowe AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z pełną dokumentację te moduł programu PowerShell:
* [PowerShell — model klasyczny](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
