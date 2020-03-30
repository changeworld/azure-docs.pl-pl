---
title: Moduły programu PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Program PowerShell służy do tworzenia obszarów roboczych usługi Azure Machine Learning Studio (klasycznych), eksperymentów, usług sieci Web i innych zarządzania programem Azure Machine Learning Studio (klasycznymi).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204294"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Moduły programu PowerShell dla usługi Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Za pomocą modułów programu PowerShell można programowo zarządzać zasobami i zasobami studio (klasycznymi), takimi jak obszary robocze, zestawy danych i usługi sieci web.

Zasoby studio (klasyczne) można wchodzić w interakcje przy użyciu trzech modułów programu Powershell:

* [Usługa Azure PowerShell Az wydana](#az-rm) w 2018 r. zawiera wszystkie funkcje usługi AzureRM, chociaż z różnymi nazwami poleceń cmdlet
* [Usługa AzureRM](#az-rm) wydana w 2016 r., zastąpiona przez program PowerShell Az
* [Usługa Azure Machine Learning PowerShell classic](#classic) wydana w 2016 r.

Mimo że te moduły programu PowerShell mają pewne podobieństwa, każdy jest przeznaczony dla określonych scenariuszy. W tym artykule opisano różnice między modułami programu PowerShell i pomaga zdecydować, które z nich wybrać.  

Sprawdź w [poniższej tabeli pomocy technicznej,](#support-table) aby zobaczyć, które zasoby są obsługiwane przez każdy moduł. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a>Usługi Azure PowerShell Az i azurerm

Az jest teraz modułem programu PowerShell do interakcji z platformą Azure i zawiera wszystkie poprzednie funkcje usługi AzureRM. Usługa AzureRM będzie nadal otrzymywać poprawki błędów, ale nie otrzyma żadnych nowych poleceń cmdlet ani funkcji.  Az i AzureRM zarządzają rozwiązaniami wdrożonymi przy użyciu modelu wdrażania **usługi Azure Resource Manager.** Zasoby te obejmują obszary robocze Studio (klasyczne) i studio (klasyczne) "Nowe" usługi sieci web. 

Program PowerShell classic można zainstalować obok Az lub AzureRM w celu uwzględnienia zarówno "nowych" i "klasycznych" typów zasobów. Jednak nie zaleca się instalowania az i azurerm w tym samym czasie. Aby zdecydować między Az i AzureRM, Firma Microsoft zaleca Az dla wszystkich przyszłych wdrożeń.  Dowiedz się więcej o az kontra AzureRM i ścieżce migracji [we wstępie do usługi Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Aby rozpocząć korzystanie z usługi Az, postępuj zgodnie z [instrukcjami instalacji platformy Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a>Program PowerShell classic

Klasyczny [moduł programu PowerShell (klasyczny)](https://aka.ms/amlps) umożliwia zarządzanie zasobami wdrożonymi przy użyciu **klasycznego modelu wdrażania.** Zasoby te obejmują zasoby użytkownika Studio (klasyczne), "klasyczne" usługi sieci web i "klasyczne" punkty końcowe usługi sieci web.

Firma Microsoft zaleca jednak użycie modelu wdrażania Menedżera zasobów dla wszystkich przyszłych zasobów w celu uproszczenia wdrażania zasobów i zarządzania nimi. Jeśli chcesz dowiedzieć się więcej o modelach wdrażania, zobacz artykuł [Usługi Azure Resource Manager vs. classic deployment.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)

Aby rozpocząć korzystanie z programu PowerShell classic, pobierz [pakiet wersji](https://github.com/hning86/azuremlps/releases) z gitHub i postępuj zgodnie z [instrukcjami dotyczącymi instalacji.](https://github.com/hning86/azuremlps/blob/master/README.md) Instrukcje wyjaśniają, jak odblokować pobraną/rozpakowany bibliotekę DLL, a następnie zaimportować ją do środowiska programu PowerShell.

Program PowerShell classic można zainstalować obok Az lub AzureRM w celu uwzględnienia zarówno "nowych" i "klasycznych" typów zasobów.

## <a name="powershell-support-table"></a><a name="support-table"></a>Tabela obsługi programu PowerShell


| | **Az** |  **Program PowerShell classic** |
| --- | --- | --- |
| Tworzenie/usuwanie obszarów roboczych | [Szablony usługi Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Zarządzanie planami zobowiązań dotyczących obszaru roboczego | [Nowy plan realizacji AzMl](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Zarządzanie użytkownikami obszaru roboczego |  | [Add-AmlWorkspaceUżydnicy](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Zarządzanie usługami sieci Web | [Nowy-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nowe" usługi internetowe)|| [Usługa New-AmlWeb](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasyczne" usługi internetowe) |
| Zarządzanie punktami końcowymi/kluczami usługi sieci Web |  [Klucz Usługi Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Punkt końcowy dodatku AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Zarządzanie zestawami danych użytkowników/wyszkolonymi modelami| | [Zestaw Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Zarządzanie eksperymentami użytkowników |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Zarządzanie modułami niestandardowymi | | [Nowy-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z pełną dokumentacją modułu programu PowerShell:
* [Program PowerShell classic](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
