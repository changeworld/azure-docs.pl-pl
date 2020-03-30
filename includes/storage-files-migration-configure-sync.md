---
title: Konfigurowanie synchronizacji plików platformy Azure
description: Konfigurowanie synchronizacji plików platformy Azure. Wspólny blok tekstu, współużytkowane przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209586"
---
Ten krok łączy ze sobą wszystkie zasoby i foldery skonfigurowane w systemie Windows Server podczas poprzednich kroków.

* Zaloguj się do [witryny Azure portal](https://portal.azure.com).
* Znajdź zasób usługi synchronizacji magazynu.
* Utwórz nową *grupę synchronizacji* w zasobie usługi synchronizacji magazynu dla każdego udziału plików platformy Azure. W terminologii usługi Azure File Sync udział plików platformy Azure stanie się *punktem końcowym chmury* w topologii synchronizacji, którą opisujesz podczas tworzenia grupy synchronizacji. Podczas tworzenia grupy synchronizacji nadaj jej znaną nazwę, dzięki czemu rozpoznasz, który zestaw plików synchronizuje się tutaj. Upewnij się, że odwołujesz się do udziału plików platformy Azure o pasującej nazwie.
* Po utworzeniu grupy synchronizacji na liście grup synchronizacji pojawi się wiersz. Kliknij nazwę (łącze), aby wyświetlić zawartość grupy synchronizacji. Zobaczysz swój udział plików platformy Azure w obszarze "Punkty końcowe chmury".
* Znajdź przycisk polecenia , aby *+ Dodaj punkt końcowy serwera*. Folder na aprowizowanym serwerze lokalnym stanie się ścieżką dla tego *punktu końcowego serwera*.
