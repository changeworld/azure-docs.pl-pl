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
ms.openlocfilehash: 992b469803b26a11e7ca26142beba7d238e25452
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952215"
---
# <a name="overview-of-azure-devops-project"></a>Przegląd projektu DevOps platformy Azure

Projekt DevOps platformy Azure ułatwia rozpoczęcie pracy na platformie Azure. Zasób projektu DevOps pomaga uruchomić Twój ulubiony typ aplikacji w usłudze platformy Azure wybranej w kilku szybkich krokach w witrynie Azure Portal. Projekt DevOps konfiguruje wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji.
Pulpit nawigacyjny projektu DevOps umożliwia monitorowanie zatwierdzeń kodu, kompilacji i wdrożeń, a to wszystko z pojedynczego widoku w witrynie Azure Portal.

## <a name="why-should-i-use-the-azure-devops-project"></a>Dlaczego warto używać projektu DevOps platformy Azure?

Projekt DevOps platformy Azure automatyzuje konfigurację całego potoku ciągłej integracji i ciągłego dostarczania na platformie Azure.  Możesz rozpocząć od swojego istniejącego kodu lub użyć jednej z udostępnionych przykładowych aplikacji, a następnie szybko wdrożyć tę aplikację w różnych usługach platformy Azure, takich jak Virtual Machines, App Service, Azure Container Service, Azure SQL Database i Azure Service Fabric.  

Projekt DevOps platformy Azure wykonuje całą pracę w ramach początkowej konfiguracji potoku DevOps, w tym wszystkie akcje związane z konfigurowaniem początkowego repozytorium Git, konfigurowaniem potoku ciągłej integracji/ciągłego dostarczania, tworzeniem zasobu usługi Application Insights na potrzeby monitorowania oraz udostępnianiem pojedynczego widoku całego rozwiązania wraz z tworzeniem pulpitu nawigacyjnego projektu DevOps platformy Azure w witrynie Azure Portal.

Projektu DevOps platformy Azure możesz używać w następujących celach:

* Szybkie wdrażanie aplikacji na platformie Azure
* Automatyzacja instalacji potoku ciągłej integracji/ciągłego dostarczania usługi VSTS
* Jako szablonu, aby zobaczyć i zrozumieć, jak prawidłowo skonfigurować potok ciągłej integracji/ciągłego dostarczania na platformie Azure za pomocą usługi VSTS
* Rozpoczęcie pracy z potokiem ciągłej integracji/ciągłego dostarczania do platformy Azure, a następnie dalsze dostosowanie potoku wersji na podstawie własnych określonych scenariuszy

## <a name="how-do-i-use-the-azure-devops-project"></a>Jak mogę używać projektu DevOps platformy Azure?

Projekt DevOps platformy Azure jest dostępny w witrynie Azure Portal.  Zasób projektu DevOps platformy Azure można utworzyć tak samo, jak dowolny inny zasób platformy Azure w portalu.  Projekt DevOps udostępnia środowisko podobne do kreatora krok po kroku dla różnych opcji konfiguracji.  

W ramach początkowej konfiguracji należy wybrać kilka opcji konfiguracji.  Dostępne są następujące opcje:

* Używanie udostępnionej przykładowej aplikacji lub dodanie własnego kodu
* Wybieranie języka aplikacji
* Wybieranie platformy aplikacji na podstawie języka
* Wybieranie usługi platformy Azure (cel wdrożenia)
* Konto usługi VSTS (nowe lub istniejące)
* Wybieranie subskrypcji platformy Azure
* Wybieranie lokalizacji usług platformy Azure
* Wybieranie spośród różnych warstw cenowych usług platformy Azure

Po skorzystaniu z projektu DevOps platformy Azure możesz także usunąć wszystkie zasoby z jednego miejsca, na pulpicie nawigacyjnym projektu DevOps platformy Azure w witrynie Azure Portal.

## <a name="azure-devops-project-and-vsts-integration"></a>Integracja projektu DevOps platformy Azure i usługi VSTS

Projekty DevOps są obsługiwane przez usługę VSTS.  Projekt DevOps automatyzuje wszystkie prace, które trzeba wykonać w usłudze VSTS w celu skonfigurowania ciągłej integracji/ciągłego dostarczania do platformy Azure.  Repozytorium Git jest tworzone w ramach nowego lub istniejącego konta usługi VSTS.  Projekt DevOps zatwierdza przykładową aplikację lub Twój istniejący kod w nowym repozytorium Git.  Automatyzacja ustala również wyzwalacz ciągłej integracji na potrzeby kompilacji, aby każde nowe zatwierdzenie kodu inicjowało kompilację.  Ponadto projekt DevOps tworzy wyzwalacz ciągłego dostarczania i wdraża każdą nową udaną kompilację w wybranej usłudze platformy Azure.  Definicje kompilacji i wydania można dostosować do dodatkowych scenariuszy.  Możesz także sklonować definicje kompilacji i wydania w celu użycia w innych projektach.

Po utworzeniu projektu DevOps możesz wykonywać następujące czynności:

* Dostosowywanie potoku kompilacji i wydania
* Zarządzanie przepływem kodu i zapewnianie wysokiej jakości za pomocą żądań ściągnięcia
* Testowanie i kompilowanie każdego zatwierdzenia przed scaleniem kodu, aby podnieść poziom jakości
* Śledzenie listy prac i problemów projektu wraz z aplikacją

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Jak mogę rozpocząć pracę za pomocą projektu DevOps platformy Azure?

* [Wprowadzenie do projektu DevOps platformy Azure](https://docs.microsoft.com/azure/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Filmy wideo dotyczące projektu DevOps platformy Azure

* [Tworzenie potoku ciągłej integracji i ciągłego dostarczania za pomocą projektu DevOps platformy Azure](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
