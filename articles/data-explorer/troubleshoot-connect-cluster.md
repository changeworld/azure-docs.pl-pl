---
title: Nie można połączyć się z klastrem w Eksploratorze danych platformy Azure
description: W tym artykule opisano kroki rozwiązywania problemów do łączenia się z klastrem w Eksploratorze Azure.Data.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d10d39a65acd3664c99e8b5aa5cc015a76d9d1aa
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209381"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Rozwiązywanie problemów: Nie można połączyć z klastra w Eksploratorze danych platformy Azure

Jeśli nie możesz połączyć się z klastrem w Eksploratorze danych platformy Azure, wykonaj następujące kroki.

1. Upewnij się, że parametry połączenia są poprawne. Należy go w formie: `https://<ClusterName>.<Region>.kusto.windows.net`, takim jak poniższy: `https://docscluster.westus.kusto.windows.net`.

1. Upewnij się, że masz odpowiednie uprawnienia. Jeśli to zrobisz, uzyskasz odpowiedź *nieautoryzowanych*.

    Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md). Jeśli to konieczne, działają z Twoim administratorem klastra, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Sprawdź, że klaster nie został usunięty: przeglądanie dziennika aktywności w ramach subskrypcji.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/>). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz nawiązać połączenie z klastra.

    Jeśli stan jest **dobre** zwiększa spróbuj nawiązać połączenie z klastrem po stan (zielony znacznik wyboru).

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).