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
ms.openlocfilehash: f66dcc55b01b407c59c65ea300757ab4ee1002ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965062"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Rozwiązywanie problemów: Nie można połączyć z klastra w Eksploratorze danych platformy Azure

Jeśli nie możesz połączyć się z klastrem w Eksploratorze danych platformy Azure, wykonaj następujące kroki.

1. Upewnij się, że parametry połączenia są poprawne. Należy go w formie: `https://<ClusterName>.<Region>.kusto.windows.net`, takim jak poniższy: `https://docscluster.westus.kusto.windows.net`.

1. Upewnij się, że masz odpowiednie uprawnienia. Jeśli to zrobisz, uzyskasz odpowiedź *nieautoryzowanych*.

    Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md). Jeśli to konieczne, działają z Twoim administratorem klastra, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Sprawdź, że klaster nie został usunięty: przeglądanie dziennika aktywności w ramach subskrypcji.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/>). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz nawiązać połączenie z klastra.

    Jeśli stan jest **dobre** zwiększa spróbuj nawiązać połączenie z klastrem po stan (zielony znacznik wyboru).

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com).