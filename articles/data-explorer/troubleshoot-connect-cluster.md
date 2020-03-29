---
title: Rozwiązywanie problemów z błędami połączenia klastra usługi Azure Data Explorer
description: W tym artykule opisano kroki rozwiązywania problemów z łączeniem się z klastrem w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827040"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Rozwiązywanie problemów: niepowodzenie w nawiązaniu połączenia z klastrem w Eksploratorze danych platformy Azure

Jeśli nie możesz nawiązać połączenia z klastrem w usłudze Azure Data Explorer, wykonaj poniższe czynności.

1. Upewnij się, że parametry połączenia są poprawne. Powinien być w formie: `https://<ClusterName>.<Region>.kusto.windows.net`, takich jak `https://docscluster.westus.kusto.windows.net`następujący przykład: .

1. Upewnij się, że masz odpowiednie uprawnienia. W przeciwnym razie otrzymasz komunikat o *braku autoryzacji*.

    Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md). W razie potrzeby skontaktuj się z administratorem klastra, aby mógł dodać Cię do odpowiedniej roli.

1. Upewnij się, że klaster nie został usunięty: przejrzyj dziennik aktywności w subskrypcji.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Poszukaj stanu usługi Azure Data Explorer w regionie, w którym próbujesz nawiązać połączenie z klastrem.

    Jeśli stan nie jest **dobry** (zielony znacznik wyboru), spróbuj połączyć się z klastrem po poprawie stanu.

1. Jeśli nadal potrzebujesz pomocy w rozwiązaniu problemu, otwórz żądanie pomocy technicznej w [witrynie Azure portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)