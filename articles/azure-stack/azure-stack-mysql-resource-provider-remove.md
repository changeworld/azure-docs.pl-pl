---
title: Usunięcie dostawcy zasobów MySQL na stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można usunąć z wdrożenia stosu Azure dostawcy zasobów MySQL.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: d3a615e3b92a62709a787d0463dfa3148f14d07e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085808"
---
# <a name="remove-the-mysql-resource-provider"></a>Usuń dostawcę zasobów MySQL

Przed usunięciem dostawcy zasobów MySQL, należy usunąć wszystkie zależności dostawcy. Należy również kopię pakiet wdrożeniowy, który został użyty do zainstalowania dostawcy zasobów.

## <a name="dependency-cleanup"></a>Oczyszczanie zależności

Istnieje kilka zadań oczyszczania przed uruchomieniem skryptu DeployMySqlProvider.ps1, aby usunąć dostawcę zasobów.

Dzierżawcy są odpowiedzialne za następujące zadania oczyszczania:

* Usuń wszystkie ich bazy danych od dostawcy zasobów. (Usuwanie baz danych dzierżawy nie powoduje usunięcia danych.)
* Wyrejestruj z przestrzeni nazw dostawcy.

Administrator jest odpowiedzialny za następujące zadania oczyszczania:

* Usuwa serwerami hostingu z karty MySQL.
* Usuwa wszystkie plany, które odwołują się do karty MySQL.
* Usuwa wszelkie przydziały, które są skojarzone z kartą MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Aby usunąć dostawcę zasobów programu MySQL

1. Sprawdź, czy po usunięciu wszystkich istniejących MySQL dostawcy zależności zasobu.

   >[!NOTE]
   >Odinstalowywanie dostawcy zasobów MySQL będzie kontynuowane, nawet wtedy, gdy zasoby zależne są aktualnie używa dostawcy zasobów.
  
2. Uzyskaj kopię MySQL dostawcy zasobów binarnych, a następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego.
3. Uzyskaj kopię SQL dostawcy zasobów binarnych, a następnie uruchom samorozpakowujący się plik typu wyodrębnienie zawartości do katalogu tymczasowego.
4. Otwórz okno konsoli programu PowerShell nowych z podwyższonym poziomem uprawnień i przejdź do katalogu, w którym wyodrębniono pliki binarne dostawcy zasobów MySQL.
5. Uruchom skrypt DeployMySqlProvider.ps1, korzystając z następujących parametrów:
    - **Odinstaluj**. Usuwa dostawcy zasobów i wszystkie powiązane zasoby.
    - **PrivilegedEndpoint**. Adres IP lub nazwa DNS uprzywilejowanych punktu końcowego.
    - **CloudAdminCredential**. Poświadczenia dla administratora chmury niezbędnych do uzyskania dostępu uprzywilejowanego punktu końcowego.
    - **DirectoryTenantID**
    - **AzCredential**. Poświadczenia dla konta administratora usługi Azure stosu. Użyj tych samych poświadczeń, które używane do wdrożenia usługi Azure stosu.

## <a name="next-steps"></a>Kolejne kroki

[Oferta usługi aplikacji jako PaaS](azure-stack-app-service-overview.md)
