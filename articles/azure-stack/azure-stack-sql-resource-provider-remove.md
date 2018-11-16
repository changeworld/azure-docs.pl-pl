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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b7af23ccdd379aac9959bb9993fc1781a44e705e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684030"
---
# <a name="remove-the-sql-resource-provider"></a>Usuwanie dostawcy zasobów bazy danych SQL

Zanim usuniesz dostawcy zasobów bazy danych SQL, należy usunąć wszystkie zależności dostawcy. Należy także kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

  |Minimalna wersja usługi Azure Stack|Wersja SQL RP|
  |-----|-----|
  |Wersja 1808 (1.1808.0.97)|[SQL RP wersji 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |W wersji 1804 (1.0.180513.1)|[SQL RP wersji 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

## <a name="dependency-cleanup"></a>Oczyszczanie zależności

Istnieje kilka zadań oczyszczania, aby zrobić przed uruchomieniem skryptu DeploySqlProvider.ps1, aby usunąć dostawcę zasobów.

Użytkownicy dzierżawy usługi Azure Stack jest odpowiedzialny za następujące zadania oczyszczania:

* Usuń wszystkie swoje bazy danych od dostawcy zasobów. (Usuwanie baz danych dzierżawy nie powoduje usunięcia danych.)
* Aby wyrejestrować się z przestrzeni nazw dostawcy.

Operator usługi Azure Stack jest odpowiedzialny za następujące zadania oczyszczania:

* Usuwa serwerami hostingu z karty MySQL.
* Usuwa dowolne plany, które odwołują się karta MySQL.
* Usuwa wykorzystani limitów przydziałów, które są skojarzone z kartą MySQL.

## <a name="to-remove-the-sql-resource-provider"></a>Aby usunąć dostawcy zasobów bazy danych SQL

1. Sprawdź, czy po usunięciu wszystkich istniejących zasobów dostawcy zależności SQL.

   > [!NOTE]
   > Odinstalowywanie dostawcy zasobów bazy danych SQL będzie kontynuowana nawet wtedy, gdy zasoby zależne obecnie używasz dostawcy zasobów.
  
2. Pobierz kopię dostawcy zasobów bazy danych SQL binarnym, a następnie uruchom samodzielnej wyodrębniania, aby wyodrębnić zawartość do katalogu tymczasowego.

3. Otwórz okno konsoli programu PowerShell nowe z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym zostały wyodrębnione pliki binarne dostawcy zasobów programu SQL.

4. Uruchom skrypt DeploySqlProvider.ps1, korzystając z następujących parametrów:

    * **Odinstaluj**. Usuwa dostawcę zasobów i wszystkie powiązane zasoby.
    * **PrivilegedEndpoint**. Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    * **AzureEnvironment**. Środowisko platformy Azure używanych do wdrażania usługi Azure Stack. Wymagane tylko w przypadku wdrożeń usługi Azure AD.
    * **CloudAdminCredential**. Poświadczenia dla administratora chmury, niezbędnych do uzyskania dostępu uprzywilejowanego punktu końcowego.
    * **AzCredential**. Poświadczenia dla konta administratora usługi Azure Stack. Użyj tych samych poświadczeń, które były używane do wdrażania usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Oferowanie usług aplikacji jako usługi PaaS](azure-stack-app-service-overview.md)
