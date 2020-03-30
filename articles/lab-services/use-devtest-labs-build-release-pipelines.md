---
title: Używanie usługi DevTest Labs w potokach kompilacji i wydań usługi Azure Pipelines
description: Dowiedz się, jak korzystać z laboratoriów Azure DevTest w potokach kompilacji i wydawania potoków usługi Azure Pipelines.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169238"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Używanie usługi DevTest Labs w potokach kompilacji i wydań usługi Azure Pipelines
Ten artykuł zawiera informacje na temat sposobu DevTest Labs mogą być używane w potokach kompilacji i wydania usługi Azure Pipelines. 

## <a name="overall-flow"></a>Całkowity przepływ
Podstawowy przepływ ma mieć **potok kompilacji,** który wykonuje następujące zadania:

1. Tworzenie kodu aplikacji.
1. Tworzenie środowiska podstawowego w DevTest Labs.
1. Zaktualizuj środowisko za pomocą informacji niestandardowych.
1. Wdrażanie aplikacji w środowisku DevTest Labs
1. Przetestuj kod. 

Po pomyślnym zakończeniu **kompilacji potoku wydania** użyje artefaktów kompilacji do wdrożenia przemieszczania lub produkcji. 

Jednym z niezbędnych pomieszczeń jest to, że wszystkie informacje potrzebne do odtworzenia testowanego ekosystemu są dostępne w artefaktach kompilacji, w tym konfiguracji zasobów platformy Azure. Ponieważ zasoby platformy Azure ponoszą koszty, gdy są używane, firmy chcą kontrolować lub śledzić użycie tych zasobów. W niektórych sytuacjach szablony usługi Azure Resource Manager, które są używane do tworzenia i konfigurowania zasobów mogą być zarządzane przez inny dział, taki jak DZIAŁ IT. I te szablony mogą być przechowywane w innym repozytorium. Prowadzi to do interesującej sytuacji, w której kompilacja zostanie utworzona i przetestowana, a kod i konfiguracja będą musiały być przechowywane w artefaktach kompilacji, aby poprawnie odtworzyć system w środowisku produkcyjnym. 

Korzystając z laboratoriów DevTest Labs podczas fazy kompilacji/testu, można dodać szablony usługi Azure Resource Manager i pliki pomocnicze do źródeł kompilacji, dzięki czemu podczas fazy wydania dokładna konfiguracja używana do testowania z jest wdrażany w wersji produkcyjnej. Zadanie **Utwórz środowisko laboratorium devtest platformy Azure** z odpowiednią konfiguracją zapisze szablony Menedżera zasobów w artefaktach kompilacji. W tym przykładzie będziesz używać kodu z [samouczka: Tworzenie aplikacji sieci web .NET Core i bazy danych SQL w usłudze Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md), aby wdrożyć i przetestować aplikację sieci web na platformie Azure.

![Całkowity przepływ](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure
Istnieje kilka elementów, które należy utworzyć wcześniej:

- Dwa repozytoria. Pierwszy z kodem z samouczka i szablonem Menedżera zasobów z dwoma dodatkowymi maszynami wirtualnymi. Drugi będzie zawierał podstawowy szablon usługi Azure Resource Manager (istniejąca konfiguracja).
- Grupa zasobów do wdrożenia kodu produkcyjnego i konfiguracji.
- Laboratorium musi być skonfigurowane przy połączeniu z [repozytorium konfiguracji](devtest-lab-create-environment-from-arm.md) dla potoku kompilacji. Szablon Menedżera zasobów musi zostać sprawdzony w repozytorium konfiguracji jako azuredeploy.json z metadata.json, aby umożliwić DevTest Labs rozpoznawanie i wdrażanie szablonu.

Potok kompilacji utworzy środowisko DevTest Labs i wdroży kod do testowania.

## <a name="set-up-a-build-pipeline"></a>Konfigurowanie potoku kompilacji
W potokach platformy Azure utwórz potok kompilacji przy użyciu kodu z [samouczka: Tworzenie aplikacji sieci web .NET Core i bazy danych SQL w usłudze Azure App Service.](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md) Użyj szablonu **ASP.NET Core,** który wypełni zadanie niezbędne do tworzenia, testowania i publikowania kodu.

![Zaznaczanie szablonu ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Należy dodać trzy dodatkowe zadania, aby utworzyć środowisko w DevTest Labs i wdrożyć w środowisku.

![Potok z trzema zadaniami](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Tworzenie zadania środowiska
W zadaniu tworzenia środowiska **(Zadanie tworzenia środowiska DevTest Laboratoria Azure)** użyj list rozwijanych, aby wybrać następujące wartości:

- Subskrypcja platformy Azure
- nazwa laboratorium
- nazwa repozytorium
- nazwa szablonu (który pokazuje folder, w którym przechowywane jest środowisko). 

Zaleca się używanie list rozwijanych na stronie zamiast ręcznego wprowadzania informacji. Jeśli ręcznie wprowadzisz informacje, wprowadź w pełni kwalifikowane identyfikatory zasobów platformy Azure. Zadanie wyświetla przyjazne nazwy zamiast identyfikatorów zasobów. 

Nazwa środowiska jest wyświetlaną nazwą wyświetlaną w laboratoriach DevTest. Powinna to być unikatowa nazwa dla każdej kompilacji. Na przykład: **TestEnv$(Build.BuildId)**. 

Można określić plik parametrów lub parametry, aby przekazać informacje do szablonu Menedżera zasobów. 

Wybierz opcję **Utwórz zmienne wyjściowe na podstawie opcji wyjściowej szablonu środowiska** i wprowadź nazwę odwołania. W tym przykładzie wprowadź **BaseEnv** dla nazwy odwołania. Użyjesz tego BaseEnv podczas konfigurowania następnego zadania. 

![Tworzenie zadania środowiska laboratoriów devtest platformy Azure](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Wypełnianie zadania środowiska
Drugim zadaniem **(Azure DevTest Labs Wypełnianie** środowiska) jest zaktualizowanie istniejącego środowiska DevTest Labs. Zadanie Utwórz środowisko wyprowadza **identyfikator BaseEnv.environmentResourceId,** który służy do konfigurowania nazwy środowiska dla tego zadania. Szablon Menedżera zasobów w tym przykładzie ma dwa parametry - **adminUserName** i **adminPassword**. 

![Wypełnianie zadania środowiska laboratorium devtest platformy Azure](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Zadanie wdrażania usługi App Service
Trzecim zadaniem jest zadanie **wdrażania usługi Azure App Service.** Typ aplikacji jest ustawiony na **aplikację sieci Web,** a nazwa usługi app service jest ustawiona na **$(WebSite).**

![Zadanie wdrażania usługi App Service](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Konfigurowanie potoku wydań
Tworzenie potoku wydania z dwoma zadaniami: **Wdrożenie platformy Azure: Tworzenie lub aktualizowanie grupy zasobów** i **wdrażanie usługi Azure App Service**. 

Dla pierwszego zadania określ nazwę i lokalizację grupy zasobów. Lokalizacja szablonu jest połączonym artefaktem. Jeśli szablon Menedżera zasobów zawiera połączone szablony, należy zaimplementować wdrożenie niestandardowej grupy zasobów. Szablon znajduje się w opublikowanym artefaktu upuszczania. Zastąd w szablonie Menedżera zasobów zastądają parametry szablonu. Pozostałe ustawienia można pozostawić z wartościami domyślnymi. 

W przypadku drugiego zadania **Wdrażanie usługi Azure App Service**określ subskrypcję platformy Azure, wybierz aplikację sieci **Web** dla **typu aplikacji**i **$(WebSite)** dla **nazwy usługi app service**. Pozostałe ustawienia można pozostawić z wartościami domyślnymi. 

## <a name="test-run"></a>Przebieg testowy
Teraz, gdy oba potoki są skonfigurowane, ręcznie kolejkować kompilacji i zobaczyć to działa. Następnym krokiem jest ustawienie odpowiedniego wyzwalacza dla kompilacji i połączenie kompilacji z potokiem wydania.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Integracja laboratoriów Azure DevTest Labs z ciągłą integracją i potokiem dostarczania potoku potoku potoku ciągłej integracji i dostarczania potoku potoku potoku potoku usługi Azure Pipelines](devtest-lab-integrate-ci-cd-vsts.md)
- [Integrowanie środowisk z potokami ciągłej integracji/dysków CD potoków potoków potoków usługi Azure Pipelines](integrate-environments-devops-pipeline.md)
