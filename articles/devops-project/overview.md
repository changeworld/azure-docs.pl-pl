---
title: Omówienie usługi Azure DevOps Projects | Microsoft Docs
description: Informacje o wartości usługi Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971472"
---
# <a name="overview-of-azure-devops-projects"></a>Omówienie usługi Azure DevOps Projects

 Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Pomaga ona uruchomić ulubioną aplikację w usłudze platformy Azure wybranej w kilku szybkich krokach w witrynie Azure Portal. 

 Usługa DevOps Projects konfiguruje wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji. Pulpit nawigacyjny usługi DevOps Projects umożliwia monitorowanie zatwierdzeń kodu, kompilacji i wdrożeń, a to wszystko z pojedynczego widoku w witrynie Azure Portal.

## <a name="why-should-i-use-devops-projects"></a>Dlaczego warto używać usługi DevOps Projects?

  Usługa DevOps Projects automatyzuje konfigurację całego potoku ciągłej integracji i ciągłego dostarczania na platformie Azure.  Możesz rozpocząć z istniejącym kodem lub użyć jednej z dostarczonych aplikacji przykładowych. Następnie możesz szybko wdrożyć tę aplikację w różnych usługach platformy Azure, takich jak Virtual Machines, App Service, Azure Kubernetes Services (AKS), Azure SQL Database i Azure Service Fabric.  

  Usługa DevOps Projects wykonuje całą pracę w ramach początkowej konfiguracji potoku DevOps, w tym wszystkie akcje związane z konfigurowaniem początkowego repozytorium Git, konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania, tworzeniem zasobu usługi Application Insights na potrzeby monitorowania oraz udostępnianiem pojedynczego widoku całego rozwiązania wraz z tworzeniem pulpitu nawigacyjnego usługi DevOps Projects w witrynie Azure Portal.

Usługi DevOps Projects można użyć do wykonywania następujących czynności:

* Szybkie wdrażanie aplikacji na platformie Azure
* Automatyzacja instalacji potoku ciągłej integracji/ciągłego wdrażania
* Wyświetlanie potoku ciągłej integracji/ciągłego wdrażania oraz poznanie sposobu jego prawidłowego konfigurowania
* Dalsze dostosowywanie potoków wydania w oparciu o określone scenariusze

## <a name="how-do-i-use-devops-projects"></a>Jak mogę używać usługi DevOps Projects?

  Usługa DevOps Projects jest dostępna w witrynie Azure Portal. Zasób usługi DevOps Projects można utworzyć tak samo jak dowolny inny zasób platformy Azure w portalu. Usługa DevOps Projects zapewnia podobne do kreatora środowisko z instrukcjami krok po kroku dotyczącymi różnych opcji konfiguracji.  

W ramach początkowej konfiguracji należy wybrać kilka opcji konfiguracji. Dostępne są następujące opcje:

* Używanie udostępnionej przykładowej aplikacji lub dodanie własnego kodu
* Wybieranie języka programowania aplikacji
* Wybieranie platformy aplikacji na podstawie języka
* Wybieranie usługi platformy Azure (cel wdrożenia)
* Tworzenie nowej organizacji usługi Azure DevOps lub wybieranie istniejącej organizacji 
* Wybieranie subskrypcji platformy Azure
* Wybieranie lokalizacji usług platformy Azure
* Wybieranie spośród różnych warstw cenowych usług platformy Azure

Po skorzystaniu z usługi DevOps Projects możesz także usunąć wszystkie zasoby z jednego miejsca, na pulpicie nawigacyjnym usługi DevOps Projects w witrynie Azure Portal.

## <a name="devops-projects-and-azure-devops-integration"></a>Integracja usługi DevOps Projects i usługi Azure DevOps

Usługa DevOps Projects jest obsługiwana przez usługę Azure DevOps. Usługa DevOps Projects automatyzuje całą pracę wymaganą do skonfigurowania potoku ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Usługa DevOps Projects tworzy repozytorium Git w nowej lub istniejącej organizacji usługi Azure DevOps, a następnie zatwierdza aplikację przykładową lub istniejący kod w nowym repozytorium Git.  

Automatyzacja ustala również wyzwalacz ciągłej integracji na potrzeby kompilacji, aby każde nowe zatwierdzenie kodu inicjowało kompilację. Usługa DevOps Projects tworzy wyzwalacz ciągłego wdrażania i wdraża każdą nową udaną kompilację w wybranej usłudze platformy Azure.  

Potoki kompilacji i wydania można dostosować do dodatkowych scenariuszy. Potoki kompilacji i wydania można także sklonować w celu użycia w innych projektach.

Po utworzeniu projektu DevOps możesz wykonywać następujące czynności:

* Dostosowywanie potoku kompilacji i wydania
* Zarządzanie przepływem kodu i zapewnianie wysokiej jakości za pomocą żądań ściągnięcia
* Testowanie i kompilowanie każdego zatwierdzenia przed scaleniem kodu, aby podnieść poziom jakości
* Śledzenie listy prac i problemów wraz z aplikacją

## <a name="how-do-i-start-using-devops-projects"></a>Jak mogę rozpocząć korzystanie z usługi DevOps Projects?

* [Rozpoczynanie pracy z usługą DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Filmy wideo dotyczące usługi DevOps Projects

* [Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
