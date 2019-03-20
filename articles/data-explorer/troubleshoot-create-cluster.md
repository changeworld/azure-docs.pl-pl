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
ms.openlocfilehash: 9b8bfe2a4b9b7a8432f14fb53b3e7a4cae49a3b4
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189975"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Rozwiązywanie problemów: Nie można utworzyć klaster w Eksploratorze danych platformy Azure

W mało prawdopodobnym przypadku, gdy tworzenie klastra kończy się niepowodzeniem w Eksploratorze danych platformy Azure wykonaj następujące kroki.

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć klaster, musi być członkiem *Współautor* lub *właściciela* roli w subskrypcji platformy Azure. Jeśli to konieczne, działają z Twoim administratorem subskrypcji, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Upewnij się, że weryfikacja nie zwróciła błędów związanych z nazwy klastra wprowadzona w obszarze **Utwórz klaster** w witrynie Azure portal.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz utworzyć klaster.

    Jeśli stan jest **dobre** (zielony znacznik wyboru), spróbuj utworzyć klaster po zwiększa stan.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).