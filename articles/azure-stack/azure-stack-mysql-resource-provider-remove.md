---
title: Usuwanie dostawcy zasobów MySQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć dostawcy zasobów MySQL z wdrożenia usługi Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 2740da5a51e95a327a868734a7f009dddf40219a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964938"
---
# <a name="remove-the-mysql-resource-provider"></a>Usuwanie dostawcy zasobów bazy danych MySQL

Zanim usuniesz dostawcy zasobów bazy danych MySQL, należy usunąć wszystkie zależności dostawcy. Należy także kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

> [!NOTE]
> Można znaleźć łącza pobierania zasobu instalatorów dostawcy w [wdrażanie wstępnie wymaganych składników dla dostawcy zasobów](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Usuwanie dostawcy zasobów MySQL nie spowoduje usunięcia bazy danych dzierżaw z serwerów hosta.

## <a name="dependency-cleanup"></a>Oczyszczanie zależności

Istnieje kilka zadań oczyszczania, aby zrobić przed uruchomieniem skryptu DeployMySqlProvider.ps1, aby usunąć dostawcę zasobów.

Operator usługi Azure Stack jest odpowiedzialny za następujące zadania oczyszczania:

* Usunąć dowolne plany, które odwołują się karta MySQL.
* Usuń przydziały, które są skojarzone z kartą MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Aby usunąć dostawcy zasobów bazy danych MySQL

1. Sprawdź, czy po usunięciu wszystkich istniejących MySQL dostawcy zależności zasobów.

   > [!NOTE]
   > Odinstalowywanie dostawcy zasobów MySQL będzie kontynuowana nawet wtedy, gdy zasoby zależne obecnie używasz dostawcy zasobów.
  
2. Pobierz kopię pakietu instalacyjnego dostawcy zasobów MySQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego.
3. Otwórz okno konsoli programu PowerShell nowe z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym została rozpakowana plików instalacyjnych dostawcy zasobów MySQL.
4. Uruchom skrypt DeployMySqlProvider.ps1, korzystając z następujących parametrów:
    - **Odinstaluj**. Usuwa dostawcę zasobów i wszystkie powiązane zasoby.
    - **PrivilegedEndpoint**. Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    - **AzureEnvironment**. Środowisko platformy Azure używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD.
    - **CloudAdminCredential**. Poświadczenia dla administratora chmury, niezbędnych do uzyskania dostępu uprzywilejowanego punktu końcowego.
    - **DirectoryTenantID**
    - **AzCredential**. Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Oferowanie usług aplikacji jako usługi PaaS](azure-stack-app-service-overview.md)
