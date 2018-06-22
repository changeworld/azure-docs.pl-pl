---
title: Użyj bazy danych MySQL jako PaaS w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć MySQL dostawcy zasobów i zapewnić baz danych MySQL jako usługa na stosie Azure.
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
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309841"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych MySQL na Microsoft Azure stosu

Można wdrożyć interfejsu API do używania bazy danych MySQL wdrożone w stosie Azure dostawcy zasobów MySQL. Aby uzyskać więcej informacji na temat interfejsu API dostawcy zasobów, zobacz [systemu Windows Azure Pack MySQL zasobów dostawcy dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/dn528442.aspx).

Bazy danych MySQL są często używane w witrynach sieci Web i obsługuje wiele platform witryny sieci Web. Na przykład można utworzyć witryny sieci Web WordPress przy użyciu platformy aplikacji sieci Web jako dodatek usługa (PaaS).

Po wdrożeniu dostawcy zasobów, można:

* Utwórz MySQL serwerów i baz danych za pomocą szablonów wdrożenia usługi Azure Resource Manager.
* Podaj baz danych MySQL jako usługa.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektura karty dostawcy zasobów MySQL

Dostawca zasobów zawiera następujące składniki:

* **MySQL zasobów dostawcy karty maszyny wirtualnej (VM)**, która jest wirtualna serwera systemu Windows, którym są uruchomione usługi dostawcy.
* **Dostawca zasobów**, który przetwarza żądania i uzyskuje dostęp do bazy danych zasobów.
* **Serwery obsługujące serwer MySQL**, które zapewniają pojemność baz danych, które są nazywane serwerów hosta. Tworzenie wystąpień MySQL, samodzielnie lub zapewniają dostęp do zewnętrznych wystąpień MySQL. [Galerii Szybki Start Azure stosu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) zawiera przykładowy szablon, który służy do:

  * Tworzy serwer MySQL.
  * Pobieranie i wdrażanie serwera MySQL z portalu Azure Marketplace.

> [!NOTE]
> Hosting serwerów, które są zainstalowane na stosie Azure zintegrowanych systemów musi zostać utworzona z subskrypcji dzierżawcy. Nie można ich utworzyć z subskrypcji dostawcy domyślnego. Muszą one zostać utworzone w portalu dzierżawcy lub w sesji programu PowerShell z odpowiednią logowania. Wszystkie serwery hostingu rozliczeniowy maszyn wirtualnych i musi mieć licencje. Administrator usługi może być właścicielem subskrypcji dzierżawcy.

### <a name="required-privileges"></a>Wymagane uprawnienia

Konto system musi mieć następujące uprawnienia:

* **Baza danych:** utworzyć, porzucić
* **Nazwa użytkownika:** tworzenie, ustawić, porzucić, przydzielić, odwołać  

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie MySQL dostawcy zasobów](azure-stack-mysql-resource-provider-deploy.md)
