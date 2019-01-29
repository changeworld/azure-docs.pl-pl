---
Tytuł: Moduły programu PowerShell dla usługi Machine Learning Studio titleSuffix: Opis usługi Azure Machine Learning Studio: Tworzenie i zarządzanie nimi w usłudze Azure Machine Learning Studio obszarów roboczych, eksperymentów, usług sieci web i więcej za pomocą programu PowerShell. usługi: uczenie maszynowe ms.service: ms.component uczenia maszynowego: studio ms.topic: artykuł

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-ms.author=haining, previous-author=hning86 ms.date: 01/25/2019
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Moduły programu PowerShell dla usługi Azure Machine Learning Studio

Korzystanie z modułów programu PowerShell, można programowo zarządzać Studio zasobów i zasoby, takie jak obszary robocze, zestawów danych i usług sieci web.

Możesz porozmawiać z zasobów Studio przy użyciu trzech modułów programu Powershell:

* [Azure PowerShell Az](#az-rm) wydanej w 2018 r., obejmuje wszystkie funkcje usługi AzureRM, mimo że za pomocą nazwy różnych poleceń cmdlet
* [AzureRM](#az-rm) udostępnione w 2016
* [Klasyczny Portal Azure programu PowerShell usługi Machine Learning](#classic) udostępnione w 2016

Mimo że te moduły mają pewne podobieństwa, każdy jest przeznaczony dla konkretnych scenariuszach. W tym artykule opisano różnice między modułów programu PowerShell i ułatwia podjęcie decyzji, które z nich do wyboru.

## <a name="choosing-modules"></a> Wybieranie modułów

Wybieranie między dostępnych modułów programu PowerShell, zależy od typu używanych zasobów, którymi zarządzasz.

Sprawdź [tabeli obsługi](#support-table) poniżej, aby zobaczyć, jakie zasoby są obsługiwane przez każdego modułu. Ponieważ klasyczny program PowerShell można zainstalować równolegle z Az lub AzureRM, można zainstalować dwa moduły i obejmują wszystkie typy zasobów (klasycznej za pomocą polecenia Az lub klasycznym przy użyciu usługi AzureRM)

Jednak nie zaleca się mieć Az i AzureRM zainstalowane w tym samym czasie. Aby wybrać między Az i AzureRM, firma Microsoft zaleca Az we wszystkich przyszłych wdrożeniach. Użyj usługi AzureRm tylko wtedy, gdy istnieją specjalne okoliczności, w danym środowisku, które tego wymagają.

Aby dowiedzieć się więcej na temat różnic między Az i AzureRM, a także przez nas ścieżka migracji podanych, zobacz nasze [wprowadzenie do programu Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Azure PowerShell Az i AzureRM

AZ i AzureRM Zarządzanie obu rozwiązań wdrożonych przy użyciu **usługi Azure Resource Manager** modelu wdrażania. Te zasoby obejmują Studio obszarów roboczych i Studio nowych usług sieci web. Aby zarządzać zasobów wdrożonych przy użyciu klasycznego modelu wdrażania, należy użyć modułu klasycznego programu PowerShell. Jeśli chcesz dowiedzieć się więcej na temat modeli wdrażania, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artykułu.

AZ jest teraz moduł programu PowerShell przeznaczone do interakcji z platformą Azure i zawiera wszystkie poprzednie funkcjonalności usługi AzureRM. AzureRM będą w dalszym ciągu otrzymywać poprawek, ale będzie ona otrzymywać nie nowych poleceń cmdlet lub funkcji. Choć ma ścieżki uaktualnienia z usługi AzureRM, jeśli wystąpią problemy za pomocą polecenia Az podczas pracy z usługą Studio, należy zgłosić problem i wrócić korzystania z usługi AzureRM.

Aby rozpocząć pracę za pomocą polecenia Az, postępuj zgodnie z [instrukcje instalacji Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell — Model Klasyczny

Studio [moduł programu PowerShell klasycznego](https://aka.ms/amlps) umożliwia zarządzanie zasobów wdrożonych przy użyciu **klasycznego modelu wdrażania**. Te zasoby obejmują Studio użytkownika zasoby, klasyczne usługi sieci web i punkty końcowe usługi sieci web.

Jednak firma Microsoft zaleca użycie modelu wdrażania usługi Resource Manager dla wszystkich nowych zasobów można Uproszczenie wdrażania i zarządzania zasobami. Jeśli chcesz dowiedzieć się więcej na temat modeli wdrażania, zobacz [usługi Azure Resource Manager, a wdrożeniem klasycznym](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artykułu.

Aby rozpocząć pracę z PowerShell — Model Klasyczny, Pobierz [pakiet wydania](https://github.com/hning86/azuremlps/releases) z serwisu GitHub i postępuj zgodnie z [instrukcjami dotyczącymi instalacji](https://github.com/hning86/azuremlps/blob/master/README.md). Instrukcje wyjaśniają sposób odblokowanie pobranej i rozpakowanej biblioteki DLL, a następnie zaimportuj go do środowiska programu PowerShell.

## <a name="support-table"></a> Tabela obsługi programu PowerShell

 **Obszary robocze Studio** | **Az** |  **AzureRM** | **PowerShell — model klasyczny** |
| --- | --- | --- | --- | --- |
| Tworzenie/usuwanie obszarów roboczych | [Szablony usługi Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Szablony usługi Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Zarządzanie użytkownikami obszaru roboczego |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Zarządzaj planami zobowiązania | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Usługi sieci Web** | **Az** | **AzureRM** | **PowerShell — model klasyczny** |
| Zarządzanie usługami sieci Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Nowych usług sieci web) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Nowych usług sieci web) |[Nowe AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (klasycznych usług sieci web) |
| Zarządzanie punktami końcowymi/kluczy |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Nowych usług sieci web) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Nowych usług sieci web) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (klasycznych usług sieci web) |
|||
| **Zasoby użytkowników** | **Az** | **AzureRM** | **PowerShell — model klasyczny** |
| Zarządzanie modelami uczony/zestawów danych |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Zarządzanie eksperymentami |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Zarządzanie moduły niestandardowe |  |  | [Nowe AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższych linków, aby uzyskać pełną dokumentację dla modułów programu PowerShell:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell — model klasyczny](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
