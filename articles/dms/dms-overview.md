---
title: Omówienie usługi migracji bazy danych platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Database Migration Service, które zapewnia bezproblemowe migracje z wielu źródeł baz danych do platformy danych.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/05/2018
ms.openlocfilehash: 44c536d3633286f8279cda89dd95cd7b29a81eb7
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803855"
---
# <a name="what-is-the-azure-database-migration-service"></a>Co to jest Azure Database Migration Service?
Azure Database Migration Service to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemową migrację z wielu źródłowych baz danych na danych na platformę Azure przy minimalnych przestojach (migracja online).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrowanie baz danych na platformie Azure przy użyciu znanych narzędzi
Azure Database Migration Service integruje niektóre funkcje oferowane przez naszych istniejących narzędzi i usług. Jego zapewnia klientom kompleksowe rozwiązanie o wysokiej dostępności. Używa usługi [Data Migration Assistant](http://aka.ms/dma) można wygenerować raporty z oceny zawierające przedstawić zalecenia w zakresie przeprowadzenie Cię przez proces zmiany wymagane przed migracją. To Ty możesz aby skorygować wszystkie wymagane. Gdy wszystko będzie gotowe do rozpoczęcia procesu migracji, Azure Database Migration Service wykonuje wszystkie wymagane kroki. Można wyzwalać i zapomnij migrację projektów z poczucie spokoju, wiedząc, że proces korzysta z najlepszych rozwiązań, zgodnie z ustaleniami firmy Microsoft.

> [!NOTE]
> Przy użyciu usługi Azure Database Migration Service, aby dokonać migracji online wymaga tworzenia wystąpienia oparte na krytyczne dla działania firmy (wersja zapoznawcza) warstwy cenowej.

## <a name="regional-availability"></a>Dostępność regionalna
Azure Database Migration Service jest obecnie dostępna w następujących regionach:

![Azure Database Migration Service dostępność regionalna](media\overview\dms-regional-availability.png)

Aby uzyskać najbardziej aktualne informacje o dostępności regionalnej usługi Azure Database Migration Service, w tym miejscu globalna infrastruktura platformy Azure, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie wystąpienia usługi Azure Database Migration Service przy użyciu witryny Azure portal](quickstart-create-data-migration-service-portal.md).
- [Migrowanie programu SQL Server do usługi Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service](pre-reqs.md).
- [Często zadawane pytania dotyczące korzystania z usługi Azure Database Migration Service](faq.md).
