---
title: Rozwiązywanie problemów z niepowodzeniem tworzenia klastra usługi Azure Data Explorer
description: W tym artykule opisano kroki rozwiązywania problemów z tworzeniem klastra w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562415"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Rozwiązywanie problemów: Nieudane tworzenie klastra w Eksploratorze danych platformy Azure

W mało prawdopodobnym przypadku, że tworzenie klastra kończy się niepowodzeniem w Eksploratorze danych platformy Azure, wykonaj następujące kroki.

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć klaster, musisz być członkiem roli *współautora* lub *właściciela* dla subskrypcji platformy Azure. W razie potrzeby należy współpracować z administratorem subskrypcji, aby można go było dodać do odpowiedniej roli.

1. Upewnij się, że nie ma żadnych błędów sprawdzania poprawności związanych z nazwą klastra wprowadzone w obszarze **Tworzenie klastra** w witrynie Azure portal.

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Poszukaj stanu Eksploratora danych platformy Azure w regionie, w którym próbujesz utworzyć klaster.

    Jeśli stan nie jest **dobry** (zielony znacznik wyboru), spróbuj utworzyć klaster po poprawie stanu.

1. Jeśli nadal potrzebujesz pomocy w rozwiązaniu problemu, otwórz żądanie pomocy technicznej w [witrynie Azure portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
