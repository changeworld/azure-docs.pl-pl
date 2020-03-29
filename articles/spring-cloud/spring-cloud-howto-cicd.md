---
title: Ciągła integracja/płyta CD dla wiosennej chmury platformy Azure
description: Ciągła integracja/płyta CD dla wiosennej chmury platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278512"
---
# <a name="cicd-for-azure-spring-cloud"></a>Ciągła integracja/płyta CD dla wiosennej chmury platformy Azure

Ciągła integracja i narzędzia ciągłego dostarczania umożliwiają deweloperom szybkie wdrażanie aktualizacji istniejących aplikacji przy minimalnym wysiłku i ryzyku. Usługa Azure DevOps ułatwia organizowanie i kontrolowanie tych kluczowych zadań. Obecnie usługa Azure Spring Cloud nie oferuje określonej wtyczki Azure DevOps.  Można jednak zintegrować aplikacje Spring Cloud z devops przy użyciu [zadania interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops) W tym artykule pokazano, jak używać zadania interfejsu wiersza polecenia platformy Azure z usługą Azure Spring Cloud do integracji z platformą Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Tworzenie połączenia usługi Usługi Azure Resource Manager

Przeczytaj [ten artykuł,](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) aby dowiedzieć się, jak utworzyć połączenie usługi Azure Resource Manager z projektem programu Azure DevOps. Pamiętaj, aby wybrać tę samą subskrypcję, której używasz dla wystąpienia usługi Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Szablony zadań interfejsu wiersza polecenia platformy Azure

### <a name="deploy-artifacts"></a>Wdrażanie artefaktów

Projekty można tworzyć i wdrażać `tasks`przy użyciu serii programu . Ten fragment kodu najpierw definiuje zadanie Maven do utworzenia aplikacji, a następnie drugie zadanie, które wdraża plik JAR przy użyciu rozszerzenia interfejsu wiersza polecenia platformy Azure Spring Cloud.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Wdrażanie ze źródła

Istnieje możliwość wdrożenia bezpośrednio na platformie Azure bez kroku kompilacji oddzielne.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
