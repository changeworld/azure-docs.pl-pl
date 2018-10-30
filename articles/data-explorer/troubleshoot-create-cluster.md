---
title: Nie można utworzyć klaster w Eksploratorze danych platformy Azure
description: W tym artykule opisano kroki rozwiązywania problemów do tworzenia klastra w Eksploratorze danych platformy Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0edf9ebcde2df7e639666f8fe7472baacdeb8640
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212186"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Rozwiązywanie problemów: Nie można utworzyć klaster w Eksploratorze danych usługi Azure

W mało prawdopodobnym przypadku, gdy tworzenie klastra kończy się niepowodzeniem w Eksploratorze danych platformy Azure wykonaj następujące kroki.

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć klaster, musi być członkiem *Współautor* lub *właściciela* roli w subskrypcji platformy Azure. Jeśli to konieczne, działają z Twoim administratorem subskrypcji, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Upewnij się, że weryfikacja nie zwróciła błędów związanych z nazwy klastra wprowadzona w obszarze **Utwórz klaster** w witrynie Azure portal.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/>). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz utworzyć klaster.

    Jeśli stan jest **dobre** (zielony znacznik wyboru), spróbuj utworzyć klaster po zwiększa stan.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).