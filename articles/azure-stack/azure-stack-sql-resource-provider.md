---
title: Przy użyciu bazy danych SQL w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć bazy danych SQL jako usługi w usłudze Azure Stack i Szybkie kroki, które umożliwia wdrażanie karty dostawcy zasobów programu SQL Server.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 3d608843ef31a1ed665fcb1fd90b822f34f77fdd
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086356"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych SQL w usłudze Microsoft Azure Stack

Karty dostawcy zasobów programu SQL Server umożliwiają oferowanie baz danych SQL jako usługa [usługi Azure Stack](azure-stack-poc.md). Po zainstalowaniu dostawcy zasobów i połącz go z co najmniej jednego wystąpienia programu SQL Server, Ty i Twoi użytkownicy można utworzyć:

- Bazy danych w przypadku aplikacji natywnych dla chmury.
- Witryn sieci Web z programu SQL.
- Obciążenia, które używają programu SQL.

Dostawca zasobów nie zawiera wszystkich baz danych, możliwości zarządzania [usługi Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Na przykład pul elastycznych, które automatycznie przydzielać zasoby nie są obsługiwane. Jednak obsługuje dostawcy zasobów podobne tworzenie, Odczyt, aktualizowanie i usuwanie operacji (CRUD) w bazie danych programu SQL Server. 

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura karty dostawcy zasobów SQL

Dostawca zasobów składa się z następujących składników:

- **SQL zasobów dostawcy karty maszyny wirtualnej (VM)**, który jest maszyną Wirtualną serwera systemu Windows, która działa dostawca usługi.
- **Dostawca zasobów**, która przetwarza żądania i uzyskuje dostęp do bazy danych zasobów.
- **Serwery obsługujące program SQL Server**, które zapewniają pojemność dla bazy danych o nazwie serwerami hostingu.

Należy utworzyć co najmniej jedno wystąpienie programu SQL Server lub zapewnić dostęp do zewnętrznych wystąpień programu SQL Server.

> [!NOTE]
> Hostowania serwerów, które są zainstalowane w usłudze Azure Stack zintegrowanych systemów musi zostać utworzona z subskrypcji dzierżawcy. Nie można ich tworzyć subskrypcji dostawcy domyślnego. Musi zostać utworzona z poziomu portalu dzierżawcy lub przy użyciu programu PowerShell przy użyciu odpowiedniej logowania. Wszystkie serwery hostingu są płatne maszyn wirtualnych i musi mieć licencje. Administrator usługi może być właścicielem subskrypcji dzierżawcy.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
