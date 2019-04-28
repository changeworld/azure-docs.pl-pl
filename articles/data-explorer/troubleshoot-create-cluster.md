---
title: Rozwiązywanie problemów z błąd tworzenia klastra Eksploratora danych usługi Azure
description: W tym artykule opisano kroki rozwiązywania problemów do tworzenia klastra w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829280"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Rozwiązywanie problemów: Tworzenie klastra nie powiodło się Eksploratora danych usługi Azure

W mało prawdopodobnym przypadku, gdy tworzenie klastra kończy się niepowodzeniem w Eksploratorze danych platformy Azure wykonaj następujące kroki.

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć klaster, musi być członkiem *Współautor* lub *właściciela* roli w subskrypcji platformy Azure. Jeśli to konieczne, działają z Twoim administratorem subskrypcji, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Upewnij się, że weryfikacja nie zwróciła błędów związanych z nazwy klastra wprowadzona w obszarze **Utwórz klaster** w witrynie Azure portal.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz utworzyć klaster.

    Jeśli stan jest **dobre** (zielony znacznik wyboru), spróbuj utworzyć klaster po zwiększa stan.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
