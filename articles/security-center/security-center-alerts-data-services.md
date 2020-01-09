---
title: Wykrywanie zagrożeń dla usług danych w Azure Security Center | Microsoft Docs
description: W tym artykule przedstawiono alerty usług danych dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665738"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Wykrywanie zagrożeń dla usług danych w Azure Security Center

 Azure Security Center analizuje dzienniki usług magazynu danych i wyzwala alerty w przypadku wykrycia zagrożenia dla zasobów danych. W tym artykule wymieniono alerty, które Security Center wygenerowały dla następujących usług:

* [Azure SQL Database i Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database i SQL Data Warehouse<a name="data-sql"></a>

Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Pojawią się alerty, gdy istnieją podejrzane działania bazy danych, potencjalne luki w zabezpieczeniach lub ataki iniekcji kodu SQL, a także nietypowy dostęp do bazy danych i wzorce zapytań.

Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database i SQL jest częścią ujednoliconego pakietu [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) na potrzeby zaawansowanych funkcji zabezpieczeń SQL, obejmujących bazy danych SQL azure, Azure SQL Database wystąpienia zarządzane, bazy danych Azure SQL Data Warehouse i serwery SQL na platformie Azure Virtual Machines.

Aby uzyskać więcej informacji, zobacz:

* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla serwerów SQL na platformie Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla SQL Database i SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Usługa Azure Storage<a name="azure-storage"></a>

Zaawansowana ochrona przed zagrożeniami dla magazynu (obecnie dostępna tylko w przypadku magazynu obiektów BLOB) wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont magazynu lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności posiadania eksperta zabezpieczeń oraz zarządzania systemami monitorowania zabezpieczeń.

>[!NOTE]
>Zaawansowana ochrona przed zagrożeniami dla magazynu nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

Aby uzyskać więcej informacji, zobacz:

* [Jak włączyć zaawansowaną ochronę przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla usługi Azure Storage](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Alerty Azure Cosmos DB są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont Azure Cosmos DB lub ich wykorzystania.

Aby uzyskać więcej informacji, zobacz:

* [Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)](alerts-reference.md#alerts-azurecosmos)
