---
title: Przykłady programu Azure PowerShell dla usługi Azure Cosmos DB
description: Przykłady dla programu Azure PowerShell — skrypty ułatwiające tworzenie kont usługi Azure Cosmos DB i zarządzanie nimi.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069302"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Przykłady sla programu Azure PowerShell dla usługi Azure Cosmos DB

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure Cosmos DB dla podstawowej (SQL) interfejsu API.

| |  |
|---|---|
|**Konta usługi Azure Cosmos**||
|[Create an account](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Tworzenie konta)| Tworzy konto interfejsu API SQL usługi Azure Cosmos. |
|[Utwórz konto](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz właściwości konta usługi Azure Cosmos. |
|[Dodawanie regionu](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Konto usługi Azure Cosmos i dodawanie regionu do listy lokalizacji. |
|[Zmień priorytet trybu failover](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zmień priorytet trybu failover z konta usługi Azure Cosmos za pomocą wyzwalacza ręcznego przełączania trybu failover. |
|[Aktualizacji tagów](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Zaktualizuj tagi dla konta usługi Azure Cosmos. |
|[Uzyskiwanie kluczy kont](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz klucze podstawowe i pomocnicze konta usługi Azure Cosmos. |
|[Ponowne generowanie kluczy konta](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wygeneruj ponownie klucze podstawowe i pomocnicze konta usługi Azure Cosmos. |
|[Lista parametrów połączenia](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Pobierz parametry połączenia podstawowego i pomocniczego konta usługi Azure Cosmos. |
|[Tworzenie zapory adresów IP](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tworzenie zapory adresów IP dla konta usługi Azure Cosmos. |
|[Usuwanie konta usługi Azure Cosmos](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Usuwanie konta usługi Azure Cosmos. |
|**Baz danych Azure Cosmos**||
| [Tworzenie bazy danych](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie bazy danych w ramach konta usługi Azure Cosmos.|
| [Tworzenie bazy danych o przepływności udostępnione/poziomie bazy danych](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Utwórz bazę danych Azure Cosmos przy przepływności na poziomie bazy danych, współużytkowaną z jego kontenerów.|
| [Lista wszystkich baz danych](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Wyświetl listę wszystkich baz danych na koncie usługi Azure Cosmos.|
| [Pobierz bazę danych](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Pobierz właściwości bazy danych Azure Cosmos.|
|**Kontenery usługi Azure Cosmos**||
| [Tworzenie kontenera](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie kontenera usługi Azure Cosmos za pomocą dedykowanej przepływności.|
| [Utwórz kontener przy użyciu udostępnionej przepływności](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie kontenera usługi Azure Cosmos przy przepływności udostępnione dla innych kontenerów w bazie danych.|
| [Utwórz kontener przy użyciu zasad indeksu](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie kontenera usługi Azure Cosmos za pomocą zasad niestandardowych indeksu.|
| [Tworzenie kontenera bez zasad indeksu](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Utwórz kontener usługi Azure Cosmos z indeks zasadach wyłączone.|
| [Utwórz kontener przy użyciu kluczy unikatowych i czas wygaśnięcia](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie bazy danych Cosmos Azure skonfigurowany kontener za pomocą klucza unikatowego ograniczenia i czas wygaśnięcia.|
| [Utwórz kontener przy użyciu Rozwiązywanie konfliktów](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tworzenie kontenera usługi Azure Cosmos za pomocą ostatni składnik zapisywania usługi wins zasad rozpoznawania w konflikcie.|
| [Listę wszystkich kontenerów](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Wyświetl listę wszystkich kontenerów w bazie danych Azure Cosmos.|
| [Pobierz kontener](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Pobierz właściwości dla kontenera w bazie danych Azure Cosmos.|
| [Usuwanie kontenera](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Usuń kontener w bazie danych Azure Cosmos.|
|||