---
title: Połączenie wystąpienia zarządzanego usługi Azure SQL na potrzeby indeksowania wyszukiwania — Azure Search
description: Włącz publiczny punkt końcowy, aby zezwolić na połączenia z wystąpieniami zarządzanymi SQL z indeksatora na Azure Search.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229130"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Konfigurowanie połączenia z indeksatora Azure Search do wystąpienia zarządzanego SQL
Jak zostało to opisane w temacie [łączenie Azure SQL Database Azure Search za pomocą indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), tworzenie indeksatorów z **wystąpieniami zarządzanymi SQL** jest obsługiwane przez Azure Search za pośrednictwem publicznego punktu końcowego.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Utwórz wystąpienie zarządzane Azure SQL z publicznym punktem końcowym
Utwórz wystąpienie zarządzane SQL z wybraną opcją **Włącz publiczny punkt końcowy** .

   ![Włącz publiczny punkt końcowy](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Włącz publiczny punkt końcowy")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Włącz publiczny punkt końcowy wystąpienia zarządzanego usługi Azure SQL
Możesz również włączyć publiczny punkt końcowy na istniejącym wystąpieniu zarządzanym SQL w obszarze **Security** > **Network** > Virtual**Endpoint** > **enable**.

   ![Włącz publiczny punkt końcowy](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Włącz publiczny punkt końcowy")

## <a name="verify-nsg-rules"></a>Weryfikowanie reguł sieciowej grupy zabezpieczeń
Sprawdź, czy sieciowa Grupa zabezpieczeń ma poprawne **reguły zabezpieczeń ruchu przychodzącego** , które zezwalają na połączenia z usług platformy Azure.

   ![Reguła zabezpieczeń dla ruchu przychodzącego sieciowej grupy zabezpieczeń](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Reguła zabezpieczeń dla ruchu przychodzącego sieciowej grupy zabezpieczeń")

## <a name="get-public-endpoint-connection-string"></a>Pobierz parametry połączenia publicznego punktu końcowego
Upewnij się, że używasz parametrów połączenia dla **publicznego punktu końcowego** (port 3342, nie port 1433).

   ![Parametry połączenia publicznego punktu końcowego](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Parametry połączenia publicznego punktu końcowego")

## <a name="next-steps"></a>Kolejne kroki
Za pomocą konfiguracji można teraz określić wystąpienie zarządzane SQL jako źródło danych dla indeksatora Azure Search przy użyciu portalu lub interfejsu API REST. Aby uzyskać więcej informacji [, zobacz łączenie Azure SQL Database do Azure Search za pomocą indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
