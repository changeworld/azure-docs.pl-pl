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
ms.date: 09/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: cd0195796189158650c9c2655062950b71130ad7
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578484"
---
# <a name="remove-the-mysql-resource-provider"></a>Usuwanie dostawcy zasobów bazy danych MySQL

Zanim usuniesz dostawcy zasobów bazy danych MySQL, należy usunąć wszystkie zależności dostawcy. Należy także kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

## <a name="dependency-cleanup"></a>Oczyszczanie zależności

Istnieje kilka zadań oczyszczania, aby zrobić przed uruchomieniem skryptu DeployMySqlProvider.ps1, aby usunąć dostawcę zasobów.

Dzierżawcy jest odpowiedzialny za następujące zadania oczyszczania:

* Usuń wszystkie swoje bazy danych od dostawcy zasobów. (Usuwanie baz danych dzierżawy nie powoduje usunięcia danych.)
* Aby wyrejestrować się z przestrzeni nazw dostawcy.

Administrator jest odpowiedzialna za następujące zadania oczyszczania:

* Usuwa serwerami hostingu z karty MySQL.
* Usuwa dowolne plany, które odwołują się karta MySQL.
* Usuwa wykorzystani limitów przydziałów, które są skojarzone z kartą MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Aby usunąć dostawcy zasobów bazy danych MySQL

1. Sprawdź, czy po usunięciu wszystkich istniejących MySQL dostawcy zależności zasobów.

   >[!NOTE]
   >Odinstalowywanie dostawcy zasobów MySQL będzie kontynuowana nawet wtedy, gdy zasoby zależne obecnie używasz dostawcy zasobów.
  
2. Pobierz kopię dostawcy zasobów MySQL binarnym, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego.
3. Pobierz kopię dostawcy zasobów bazy danych SQL binarnym, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego.
4. Otwórz okno konsoli programu PowerShell nowe z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym zostały wyodrębnione pliki binarne dostawcy zasobów MySQL.
5. Uruchom skrypt DeployMySqlProvider.ps1, korzystając z następujących parametrów:
    - **Odinstaluj**. Usuwa dostawcę zasobów i wszystkie powiązane zasoby.
    - **PrivilegedEndpoint**. Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    - **AzureEnvironment**. Środowisko platformy Azure używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD.
    - **CloudAdminCredential**. Poświadczenia dla administratora chmury, niezbędnych do uzyskania dostępu uprzywilejowanego punktu końcowego.
    - **DirectoryTenantID**
    - **AzCredential**. Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Oferowanie usług aplikacji jako usługi PaaS](azure-stack-app-service-overview.md)
