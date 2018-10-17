---
title: Przegląd projektu DevOps platformy Azure | Microsoft Docs
description: Informacje o wartości projektu DevOps platformy Azure
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 39dffad597b8382dea4df6fa1b0726d9582d67d1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44293630"
---
# <a name="overview-of-azure-devops-project"></a>Przegląd projektu DevOps platformy Azure

Projekt DevOps platformy Azure ułatwia rozpoczęcie pracy na platformie Azure. Zasób projektu DevOps pomaga uruchomić Twój ulubiony typ aplikacji w usłudze platformy Azure wybranej w kilku szybkich krokach w witrynie Azure Portal. Projekt DevOps konfiguruje wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji.
Pulpit nawigacyjny projektu DevOps umożliwia monitorowanie zatwierdzeń kodu, kompilacji i wdrożeń, a to wszystko z pojedynczego widoku w witrynie Azure Portal.

## <a name="why-should-i-use-the-azure-devops-project"></a>Dlaczego warto używać projektu DevOps platformy Azure?

Projekt DevOps platformy Azure automatyzuje konfigurację całego potoku ciągłej integracji i ciągłego dostarczania na platformie Azure.  Możesz rozpocząć od swojego istniejącego kodu lub użyć jednej z udostępnionych przykładowych aplikacji, a następnie szybko wdrożyć tę aplikację w różnych usługach platformy Azure, takich jak Virtual Machines, App Service, Azure Container Service, Azure SQL Database i Azure Service Fabric.  

Projekt usługi Azure DevOps wykonuje całą pracę w ramach początkowej konfiguracji potoku DevOps, w tym wszystkie akcje związane z konfigurowaniem początkowego repozytorium Git, konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania, tworzeniem zasobu usługi Application Insights na potrzeby monitorowania oraz udostępnianiem pojedynczego widoku całego rozwiązania wraz z tworzeniem pulpitu nawigacyjnego projektu usługi Azure DevOps w witrynie Azure Portal.

Projektu DevOps platformy Azure możesz używać w następujących celach:

* Szybkie wdrażanie aplikacji na platformie Azure
* Automatyzacja instalacji potoku ciągłej integracji/ciągłego wdrażania na platformie Azure
* Jako szablonu, aby zobaczyć i zrozumieć, jak prawidłowo skonfigurować potok ciągłej integracji/ciągłego wdrażania na platformie Azure za pomocą usługi Azure DevOps
* Rozpoczęcie pracy z potokiem ciągłej integracji/ciągłego dostarczania do platformy Azure, a następnie dalsze dostosowanie potoku wersji na podstawie własnych określonych scenariuszy

## <a name="how-do-i-use-the-azure-devops-project"></a>Jak mogę używać projektu DevOps platformy Azure?

Projekt DevOps platformy Azure jest dostępny w witrynie Azure Portal.  Zasób projektu usługi Azure DevOps można utworzyć tak samo, jak dowolny inny zasób platformy Azure w portalu.  Projekt DevOps zapewnia podobne do kreatora środowisko z instrukcjami krok po kroku dotyczącymi różnych opcji konfiguracji.  

W ramach początkowej konfiguracji należy wybrać kilka opcji konfiguracji.  Dostępne są następujące opcje:

* Używanie udostępnionej przykładowej aplikacji lub dodanie własnego kodu
* Wybieranie języka aplikacji
* Wybieranie platformy aplikacji na podstawie języka
* Wybieranie usługi platformy Azure (cel wdrożenia)
* Organizacja usługi Azure DevOps (nowa lub istniejąca)
* Wybieranie Twojej subskrypcji platformy Azure
* Wybieranie lokalizacji usług platformy Azure
* Wybieranie spośród różnych warstw cenowych usług platformy Azure

Po skorzystaniu z projektu DevOps platformy Azure możesz także usunąć wszystkie zasoby z jednego miejsca, na pulpicie nawigacyjnym projektu DevOps platformy Azure w witrynie Azure Portal.

## <a name="azure-devops-project-and-azure-devops-integration"></a>Integracja projektu usługi Azure DevOps i usługi Azure DevOps

Projekty DevOps są obsługiwane przez usługę Azure DevOps.  Projekt DevOps automatyzuje wszystkie prace, które trzeba wykonać w usłudze Azure DevOps w celu skonfigurowania ciągłej integracji/ciągłego wdrażania na platformie Azure.  Repozytorium Git jest tworzone w ramach nowej lub istniejącej organizacji usługi Azure DevOps.  Projekt DevOps zatwierdza przykładową aplikację lub istniejący kod w nowym repozytorium Git.  Automatyzacja ustala również wyzwalacz ciągłej integracji na potrzeby kompilacji, aby każde nowe zatwierdzenie kodu inicjowało kompilację.  Ponadto projekt DevOps tworzy wyzwalacz ciągłego wdrażania i wdraża każdą nową udaną kompilację w wybranej usłudze platformy Azure.  Potoki kompilacji i wydania można dostosować do dodatkowych scenariuszy.  Można także sklonować potoki kompilacji i wydania w celu użycia w innych projektach.

Po utworzeniu projektu DevOps możesz wykonywać następujące czynności:

* Dostosowywanie potoku kompilacji i wydania
* Zarządzanie przepływem kodu i zapewnianie wysokiej jakości za pomocą żądań ściągnięcia
* Testowanie i kompilowanie każdego zatwierdzenia przed scaleniem kodu, aby podnieść poziom jakości
* Śledzenie listy prac i problemów projektu wraz z aplikacją

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Jak mogę rozpocząć pracę za pomocą projektu DevOps platformy Azure?

* [Wprowadzenie do projektu DevOps platformy Azure](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Filmy wideo dotyczące projektu DevOps platformy Azure

* [Tworzenie potoku ciągłej integracji i ciągłego dostarczania za pomocą projektu DevOps platformy Azure](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
