---
title: Przykłady dla programu Azure PowerShell dla usługi Azure Cosmos DB | Microsoft Docs
description: Przykłady dla programu Azure PowerShell — skrypty ułatwiające tworzenie kont usługi Azure Cosmos DB i zarządzanie nimi.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: f651a88f71e62518d0531a2d5cee5c1cd2bc5ce4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Przykłady sla programu Azure PowerShell dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure Cosmos DB. W tej chwili za pomocą programu PowerShell można zarządzać tylko warstwą kont usługi Azure Cosmos DB. Innymi zasobami, takimi jak bazy danych i kolekcje, nie można zarządzać za pomocą programu PowerShell.

| |  |
|---|---|
|**Tworzenie konta usługi Azure Cosmos DB**||
|[Tworzenie konta interfejsu API SQL](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy jedno konto usługi Azure Cosmos DB do użycia z interfejsem API SQL. |
|**Skalowanie usługi Azure Cosmos DB**||
|[Replikowanie konta usługi Azure Cosmos DB w wielu regionach i konfigurowanie priorytetów trybu failover](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Globalnie replikuje dane konta w wielu regionach z określonym priorytetem trybu failover.|
|**Zabezpieczanie usługi Azure Cosmos DB**||
| [Uzyskiwanie kluczy kont](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Uzyskuje podstawowe i pomocnicze klucze główne zapisu oraz klucze podstawowe i pomocnicze tylko do odczytu dla konta.|
| [Uzyskiwanie parametrów połączenia bazy danych MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Uzyskuje parametry połączenia umożliwiające połączenie aplikacji bazy danych MongoDB z Twoim kontem usługi Azure Cosmos DB.|
|[Ponowne generowanie kluczy konta](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ponownie generuje klucz główny lub klucz tylko do odczytu dla konta.|
|[Tworzenie zapory](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzy zasady kontroli dostępu do adresów IP dla połączeń przychodzących w celu ograniczenia dostępu do konta z zatwierdzonego zestawu maszyn i/lub usług w chmurze.|
|**Wysoka dostępność, odzyskiwanie po awarii, tworzenie i przywracanie kopii zapasowej**||
|[Konfigurowanie zasad trybu failover](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ustawia priorytet trybu failover poszczególnych regionów, w których konto jest replikowane.|
|||
