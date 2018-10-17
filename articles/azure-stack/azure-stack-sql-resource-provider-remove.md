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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: f5aa67ad0588e3f42e68056c8ffca97767975e8b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361485"
---
# <a name="remove-the-sql-resource-provider"></a>Usuwanie dostawcy zasobów bazy danych SQL

Zanim usuniesz dostawcy zasobów bazy danych SQL, należy usunąć wszystkie zależności dostawcy. Należy także kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

Istnieje kilka zadań oczyszczania zrobić przed uruchomieniem _DeploySqlProvider.ps1_ skrypt, aby usunąć dostawcę zasobów.
Dzierżawcy jest odpowiedzialny za następujące zadania oczyszczania:

* Usuń wszystkie swoje bazy danych od dostawcy zasobów. (Usuwanie baz danych dzierżawy nie powoduje usunięcia danych.)
* Aby wyrejestrować się z przestrzeni nazw dostawcy zasobów.

Administrator jest odpowiedzialna za następujące zadania oczyszczania:

* Usuwa serwerami hostingu z dostawcy zasobów bazy danych SQL.
* Usuwa wszystkie plany odwołujące się do dostawcy zasobów bazy danych SQL.
* Usuwa wykorzystani limitów przydziałów, które są skojarzone z dostawcy zasobów bazy danych SQL.

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
