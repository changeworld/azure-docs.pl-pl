---
title: Połączenie wystąpienia zarządzanego usługi Azure SQL dla indeksowania wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz publiczny punkt końcowy, aby zezwolić na połączenia z wystąpieniami zarządzanymi SQL z indeksatora w usłudze Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964893"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Konfigurowanie połączenia z indeksatora usługi Azure Cognitive Search do wystąpienia zarządzanego sql

Jak wspomniano w [connecting azure sql database do usługi Azure Cognitive Search przy użyciu indeksatorów,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)tworzenie indeksatorów dla **wystąpień zarządzanych SQL** jest obsługiwany przez usługę Azure Cognitive Search za pośrednictwem publicznego punktu końcowego.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Tworzenie wystąpienia zarządzanego usługi Azure SQL przy za pomocą publicznego punktu końcowego
Utwórz wystąpienie zarządzane SQL z wybraną opcją **Włącz publiczny punkt końcowy.**

   ![Włączanie publicznego punktu końcowego](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Włączanie publicznego punktu końcowego")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Włączanie publicznego punktu końcowego wystąpienia zarządzanego sql platformy Azure
Można również włączyć publiczny punkt końcowy w istniejącym wystąpieniu zarządzanym SQL w obszarze**Publiczny punkt końcowy** > **sieci** >  **zabezpieczeń** > **Włącz**.

   ![Włączanie publicznego punktu końcowego](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Włączanie publicznego punktu końcowego")

## <a name="verify-nsg-rules"></a>Weryfikowanie reguł sieciowych sieci płciowych
Sprawdź, czy sieciowa grupa zabezpieczeń ma poprawne **reguły zabezpieczeń przychodzących,** które zezwalają na połączenia z usług platformy Azure.

   ![Reguła zabezpieczeń przychodzących nsg](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Reguła zabezpieczeń przychodzących nsg")

> [!NOTE]
> Indeksatory nadal wymagają, aby wystąpienie zarządzane sql być skonfigurowane z publicznego punktu końcowego w celu odczytu danych.
> Można jednak ograniczyć dostęp przychodzący do tego publicznego punktu końcowego, zastępując bieżącą regułę (`public_endpoint_inbound`) następującymi regułami 2:
>
> * Zezwalanie na dostęp `AzureCognitiveSearch` przychodzący z [tagu usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`)
>
> * Zezwolenie na dostęp przychodzący z adresu IP usługi wyszukiwania, który można uzyskać poprzez pingowanie jego w pełni kwalifikowanej nazwy domeny (np., `<your-search-service-name>.search.windows.net`). ("ŹRÓDŁO" `IP address`= , "NAZWA" = `search_service_inbound`)
>
> Dla każdej z tych 2 reguł `3342`ustaw "PORT" `TCP`= , `Any`"PROTOCOL" = , "DESTINATION" = , "ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Pobierz ciąg połączenia publicznego punktu końcowego
Upewnij się, że używasz ciągu połączenia dla **publicznego punktu końcowego** (port 3342, a nie port 1433).

   ![Publiczny ciąg połączenia punktu końcowego](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Publiczny ciąg połączenia punktu końcowego")

## <a name="next-steps"></a>Następne kroki
Z konfiguracji na uboczu, można teraz określić wystąpienie zarządzane sql jako źródło danych dla indeksatora usługi Azure Cognitive Search przy użyciu portalu lub interfejsu API REST. Zobacz [Łączenie usługi Azure SQL Database do usługi Azure Cognitive Search przy użyciu indeksatorów, aby](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) uzyskać więcej informacji.
