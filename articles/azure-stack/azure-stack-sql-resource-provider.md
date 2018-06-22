---
title: Przy użyciu bazy danych SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć baz danych jako usługa na stosie Azure i Szybkie kroki wdrażania karty dostawcy zasobów programu SQL Server.
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
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307829"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych SQL Microsoft Azure stosu

Użyj karty dostawcy zasobów programu SQL Server interfejsu API do udostępnienia baz danych jako usługa [stosu Azure](azure-stack-poc.md). Po zainstalowaniu dostawcy zasobów i połącz go z co najmniej jedno wystąpienie programu SQL Server, można utworzyć i użytkowników:

- Bazy danych dla chmury natywnych aplikacji.
- Witryn sieci Web SQL.
- Obciążenia, które wykorzystują SQL.

Dostawca zasobów nie zapewnia wszystkich baz danych, możliwości zarządzania [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/). Na przykład pule elastyczne, automatycznie przydzielania zasobów nie są obsługiwane. Jednak obsługuje dostawcy zasobów podobne tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD) w bazie danych programu SQL Server. Aby uzyskać więcej informacji na temat interfejsu API dostawcy zasobów, zobacz [systemu Windows Azure Pack SQL Server zasobów dostawcy dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/dn528529.aspx).

>[!NOTE]
Interfejsu API dostawcy zasobów programu SQL Server nie jest zgodna z bazy danych SQL Azure.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura karty dostawcy zasobów SQL

Dostawca zasobów składa się z następujących składników:

- **SQL zasobów dostawcy karty maszyny wirtualnej (VM)**, która jest maszynę Wirtualną serwera systemu Windows, która uruchamia dostawcy usług.
- **Dostawca zasobów**, który przetwarza żądania i uzyskuje dostęp do bazy danych zasobów.
- **Serwery obsługujące program SQL Server**, które zapewniają pojemność baz danych o nazwie serwerami hostingu.

Należy utworzyć co najmniej jedno wystąpienie programu SQL Server lub zapewniają dostęp do zewnętrznych wystąpień programu SQL Server.

> [!NOTE]
> Hosting serwerów, które są zainstalowane na stosie Azure zintegrowanych systemów musi zostać utworzona z subskrypcji dzierżawcy. Nie można ich utworzyć z subskrypcji dostawcy domyślnego. Musi zostać utworzony z portalu dzierżawcy lub za pomocą programu PowerShell odpowiednie logowanie. Wszystkie serwery hostingu rozliczeniowy maszyn wirtualnych i musi mieć licencje. Administrator usługi może być właścicielem subskrypcji dzierżawcy.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
