---
title: Konfigurowanie Azure File Sync
description: Skonfiguruj Azure File Sync. Wspólny blok tekstu współużytkowany przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209586"
---
Ten krok polega na połączeniu wszystkich zasobów i folderów skonfigurowanych na serwerze Windows Server podczas poprzednich kroków.

* Zaloguj się do [Azure Portal](https://portal.azure.com).
* Znajdź zasób usługi synchronizacji magazynu.
* Utwórz nową *grupę synchronizacji* w ramach zasobu usługi synchronizacji magazynu dla każdego udziału plików platformy Azure. W terminologii Azure File Sync udział plików platformy Azure stanie się *punktem końcowym w chmurze* w topologii synchronizacji opisującym się tworzeniem grupy synchronizacji. Podczas tworzenia grupy synchronizacji nadaj jej znaną nazwę, aby rozpoznać, który zestaw plików jest synchronizowany w tym miejscu. Upewnij się, że Przywołujesz udział plików platformy Azure o pasującej nazwie.
* Po utworzeniu grupy synchronizacji na liście grup synchronizacji zostanie wyświetlony wiersz dla tego elementu. Kliknij nazwę (łącze), aby wyświetlić zawartość grupy synchronizacji. Udział plików platformy Azure zostanie wyświetlony w obszarze "punkty końcowe chmury".
* Znajdź przycisk polecenia do *+ Dodaj punkt końcowy serwera*. Folder na serwerze lokalnym, który został zainicjowany, stanie się ścieżką dla tego *punktu końcowego serwera*.
