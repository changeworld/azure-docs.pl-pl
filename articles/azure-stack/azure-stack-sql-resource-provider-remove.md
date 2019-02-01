---
title: Usuwanie dostawcy zasobu języka SQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć dostawcy zasobów bazy danych SQL z wdrożenia usługi Azure Stack.
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
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: ac0b4ddf876fb8108e20eeaf48c6fb0ab797af0d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246336"
---
# <a name="remove-the-sql-resource-provider"></a>Usuwanie dostawcy zasobów bazy danych SQL

Zanim usuniesz dostawcy zasobów bazy danych SQL, należy usunąć wszystkie zależności dostawcy. Należy także kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

> [!NOTE]
> Można znaleźć łącza pobierania zasobu instalatorów dostawcy w [wdrażanie wstępnie wymaganych składników dla dostawcy zasobów](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Usuwanie dostawcy zasobów bazy danych SQL nie spowoduje usunięcia bazy danych dzierżaw z serwerów hosta.

## <a name="dependency-cleanup"></a>Oczyszczanie zależności

Istnieje kilka zadań oczyszczania, aby zrobić przed uruchomieniem skryptu DeploySqlProvider.ps1, aby usunąć dostawcę zasobów.

Operator usługi Azure Stack jest odpowiedzialny za następujące zadania oczyszczania:

* Usunąć dowolne plany, które odwołują się karta SQL.
* Usuń przydziały, które są skojarzone z kartą SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Aby usunąć dostawcy zasobów bazy danych SQL

1. Sprawdź, czy po usunięciu wszystkich istniejących zasobów dostawcy zależności SQL.

   > [!NOTE]
   > Odinstalowywanie dostawcy zasobów bazy danych SQL będzie kontynuowana nawet wtedy, gdy zasoby zależne obecnie używasz dostawcy zasobów.
  
2. Pobierz kopię pakietu instalacyjnego dostawcy zasobów programu SQL, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego.

3. Otwórz okno konsoli programu PowerShell nowe z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym została rozpakowana plików instalacyjnych dostawcy zasobów programu SQL.

4. Uruchom skrypt DeploySqlProvider.ps1, korzystając z następujących parametrów:

    * **Odinstaluj**. Usuwa dostawcę zasobów i wszystkie powiązane zasoby.
    * **PrivilegedEndpoint**. Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    * **AzureEnvironment**. Środowisko platformy Azure używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD.
    * **CloudAdminCredential**. Poświadczenia dla administratora chmury, niezbędnych do uzyskania dostępu uprzywilejowanego punktu końcowego.
    * **AzCredential**. Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Oferowanie usług aplikacji jako usługi PaaS](azure-stack-app-service-overview.md)
