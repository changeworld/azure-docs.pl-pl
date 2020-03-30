---
title: Zagadnienia dotyczące inicjowania obsługi administracyjnej udziałów plików platformy Azure.
description: Aprowizuj udziały plików platformy Azure do użycia z synchronizacją plików platformy Azure. Wspólny blok tekstu, współużytkowane przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209430"
---
Udział plików platformy Azure jest przechowywany w chmurze na koncie magazynu platformy Azure.
Istnieje inny poziom zagadnień wydajności tutaj.

Jeśli masz bardzo aktywne udziały — udziały używane przez wielu użytkowników i/lub aplikacji, a następnie dwa udziały plików platformy Azure może osiągnąć limit wydajności konta magazynu.

Najlepszym rozwiązaniem jest wdrożenie kont magazynu z jednym udziałem plików.
Można połączyć wiele udziałów plików platformy Azure na tym samym koncie magazynu, w przypadku, gdy masz udziały archiwalne lub oczekujesz niskiej codziennej aktywności w nich.

Te zagadnienia dotyczą bardziej bezpośredniego dostępu do chmury (za pośrednictwem maszyny Wirtualnej platformy Azure) niż mają zastosowanie do synchronizacji plików platformy Azure. Jeśli planujesz używać tylko usługi Azure File Sync w tych udziałach, grupowanie kilku na jedno konto magazynu platformy Azure jest w porządku.

Jeśli dokonałeś listy swoich udziałów, powinieneś zamapować każdy udział na konto magazynu, na które będą przebywać.

W poprzedniej fazie określono odpowiednią liczbę udziałów. W tym kroku utworzono mapowanie kont magazynu do udziałów plików. Wdrażanie teraz odpowiednią liczbę kont magazynu platformy Azure z odpowiednią liczbą udziałów plików platformy Azure w nich.

Upewnij się, że region każdego konta magazynu jest taki sam i odpowiada regionowi zasobu usługi synchronizacji magazynu, który został już wdrożony.

> [!CAUTION]
> Jeśli utworzysz 100 TiB limit udziału plików platformy Azure, udział ten może używać tylko lokalnie nadmiarowych magazynów lub strefowych opcji nadmiarowości magazynu. Należy wziąć pod uwagę potrzeby nadmiarowości pamięci masowej przed użyciem 100 udziałów plików TiB.

Udziały plików platformy Azure są nadal tworzone z limitem 5 TiB domyślnie. Ponieważ tworzysz nowe konta magazynu, postępuj zgodnie ze wskazówkami dotyczącymi [tworzenia kont magazynu, które zezwalają na udziały plików platformy Azure z limitami 100 TiB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Inną kwestią podczas wdrażania konta magazynu jest nadmiarowość magazynu platformy Azure. Zobacz: [Opcje nadmiarowości usługi Azure Storage](../articles/storage/common/storage-redundancy.md).

Nazwy zasobów są również ważne. Na przykład jeśli grupujesz wiele udziałów dla działu KADR na konto magazynu platformy Azure, należy odpowiednio nazwać konto magazynu. Podobnie podczas nazywania udziałów plików platformy Azure należy używać nazw podobnych do tych używanych dla ich odpowiedników lokalnych.