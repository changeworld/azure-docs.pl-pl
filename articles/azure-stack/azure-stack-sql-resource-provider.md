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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206939"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Użyj bazy danych SQL Microsoft Azure stosu
Użyj karty dostawcy zasobów programu SQL Server do udostępnienia baz danych jako usługa [stosu Azure](azure-stack-poc.md). Po zainstalowaniu dostawcy zasobów i połącz go z co najmniej jedno wystąpienie programu SQL Server, można utworzyć i użytkowników:
- Bazy danych dla chmury natywnych aplikacji.
- Witryny sieci Web, które są oparte na SQL.
- Obciążenia, które są oparte na SQL.
Nie trzeba udostępnić maszynę wirtualną (VM) obsługującego program SQL Server zawsze.

Dostawca zasobów nie obsługuje wszystkie funkcje zarządzania bazy danych z [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/). Na przykład elastyczne pule baz danych i możliwości wybierania wydajność bazy danych w górę i w dół automatycznie nie są dostępne. Jednak podobnie Obsługa ma dostawcy zasobów tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD). Interfejs API nie jest zgodny z bazy danych SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura karty dostawcy zasobów SQL
Dostawca zasobów składa się z trzech składników:

- **Karta Dostawca zasobów SQL maszyny Wirtualnej**, która jest maszyny wirtualnej systemu Windows, która uruchamia dostawcy usług.
- **Dostawca zasobów sam**, który przetwarza żądania alokacji i ujawnia bazy danych zasobów.
- **Serwery obsługujące program SQL Server**, które zapewniają pojemność baz danych o nazwie serwerami hostingu.

Należy utworzyć jedną (lub więcej) wystąpienia programu SQL Server i zapewniają dostęp do zewnętrznych wystąpień programu SQL Server.

> [!NOTE]
> Hosting serwerów, które są zainstalowane na stosie Azure zintegrowanych systemów musi zostać utworzona z subskrypcji dzierżawcy. Nie można ich utworzyć z subskrypcji dostawcy domyślnego. Muszą one zostać utworzone w portalu dzierżawcy lub w sesji programu PowerShell z odpowiednią logowania. Wszystkie serwery hostingu maszyn wirtualnych mogą być obciążane i musi mieć odpowiednie licencje. Administrator usługi może być właścicielem subskrypcji dzierżawcy.


## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
