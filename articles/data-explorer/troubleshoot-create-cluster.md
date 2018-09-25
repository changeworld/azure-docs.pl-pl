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
ms.openlocfilehash: 6009953ece78eefd52fca9f12e3db80a6d2cc3eb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953212"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Rozwiązywanie problemów: Nie można utworzyć klaster w Eksploratorze danych usługi Azure

W mało prawdopodobnym przypadku, gdy tworzenie klastra kończy się niepowodzeniem w Eksploratorze danych platformy Azure wykonaj następujące kroki.

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć klaster, musi być członkiem *Współautor* lub *właściciela* roli w subskrypcji platformy Azure. Jeśli to konieczne, działają z Twoim administratorem subskrypcji, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Upewnij się, że weryfikacja nie zwróciła błędów związanych z nazwy klastra wprowadzona w obszarze **Utwórz klaster** w witrynie Azure portal.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/>). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz utworzyć klaster.

    Jeśli stan jest **dobre** (zielony znacznik wyboru), spróbuj utworzyć klaster po zwiększa stan.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com).