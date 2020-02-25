---
title: Rozwiązywanie problemów z niepowodzeniem tworzenia klastra Eksplorator danych platformy Azure
description: W tym artykule opisano procedurę rozwiązywania problemów podczas tworzenia klastra w usłudze Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562415"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Rozwiązywanie problemów: nie można utworzyć klastra na platformie Azure Eksplorator danych

W najprawdopodobniej razie niepowodzenia tworzenia klastra w usłudze Azure Eksplorator danych wykonaj następujące czynności.

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć klaster, musisz być członkiem roli *współautora* lub *właściciela* subskrypcji platformy Azure. Jeśli to konieczne, skontaktuj się z administratorem subskrypcji, aby móc dodać Cię do odpowiedniej roli.

1. Upewnij się, że nie występują błędy walidacji związane z nazwą klastra wprowadzoną w obszarze **Tworzenie klastra** w Azure Portal.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Poszukaj stanu usługi Azure Eksplorator danych w regionie, w którym próbujesz utworzyć klaster.

    Jeśli stan nie jest **dobry** (zielony znacznik wyboru), spróbuj utworzyć klaster po zwiększeniu stanu.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, Otwórz żądanie pomocy technicznej w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
