---
title: Migrowanie kont magazynu, subskrypcje menedżera urządzeń StorSimple
description: Dowiedz się, jak przeprowadzić migrację subskrypcji, kont magazynu dla usługi Menedżer urządzeń StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169719"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrowanie subskrypcji i kont magazynu skojarzonych z usługą StorSimple Device Manager

Może być konieczne przeniesienie usługi StorSimple do nowej rejestracji lub do nowej subskrypcji. Te scenariusze migracji są albo zmiany konta lub zmiany centrum danych. Użyj poniższej tabeli, aby zrozumieć, które z tych scenariuszy są obsługiwane, w tym szczegółowe kroki, aby przejść.

## <a name="account-changes"></a>Zmiany konta

| Można przenieść ...| Obsługiwane| Downtime (Przestoje)| Proces pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Cała subskrypcja (obejmuje storsimple usługi i kont magazynu) do innej rejestracji? | Tak       | Nie       | **Transfer rejestracji**<br>Używać:<li>Przy zakupie nowego zobowiązania platformy Azure w ramach nowej umowy.</li><li>Chcesz przeprowadzić migrację wszystkich kont i subskrypcji ze starej rejestracji do nowej. Obejmuje to wszystkie usługi platformy Azure w ramach starej subskrypcji.</li> | **Krok 1: Otwórz bilet pomocy technicznej dla przedsiębiorstw platformy Azure.**<li>Przejdź [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)do .</li><li> Wybierz **pozycję Administracja rejestracją,** a następnie wybierz pozycję **Przenieś z jednej rejestracji do nowej rejestracji**.<br>**Krok 2: Podaj wymagane informacje**<br>Obejmują:<li>numer rejestracji źródłowego</li><li> numer rejestracji docelowej</li><li>data wejścia w życie przelewu|
| Usługa StorSimple z istniejącego konta do nowej rejestracji?    | Tak       | Nie       | **Przelew na konto**<br>Używać:<li>Jeśli nie chcesz pełnego transferu rejestracji.</li><li>Chcesz przenieść tylko określone konta do nowej rejestracji.</li>| **Krok 1: Otwórz bilet pomocy technicznej dla przedsiębiorstw platformy Azure.**<li>Przejdź [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)do .</li><li>Wybierz **pozycję Administracja rejestracją,** a następnie wybierz pozycję Przenieś konto EA do nowej **rejestracji**.<br>**Krok 2: Podaj wymagane informacje**<br>Obejmują:<li>numer rejestracji źródłowego</li><li> numer rejestracji docelowej</li><li>data wejścia w życie przelewu|
| Usługa StorSimple z jednej subskrypcji do innej subskrypcji?      | Nie        |    Tak         | Brak, proces ręczny|<li>Migrowanie danych z urządzenia StorSimple.</li><li>Wykonaj przywrócenie ustawień fabrycznych urządzenia, spowoduje to usunięcie wszystkich danych lokalnych na urządzeniu.</li><li>Zarejestruj urządzenie w nowej subskrypcji usługi StorSimple Device Manager.</li><li>Migrowanie danych z powrotem do urządzenia.|
|Czy mogę przenieść własność subskrypcji platformy Azure do innego katalogu? | Tak       | Nie       | Kojarzenie istniejącej subskrypcji z katalogiem usługi Azure AD | Zapoznaj się [z artykułem Skojarz istniejącą subskrypcję z katalogiem usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.|
| Urządzenie StorSimple z jednej usługi StorSimple Device Manager do innej usługi w innym regionie?      | Nie        | Tak            | Brak, proces ręczny |Tak samo jak powyżej.|
| Konto magazynu do nowej subskrypcji lub grupy zasobów?     | Tak        | Nie             |Przenoszenie konta magazynu do innej subskrypcji lub grupy zasobów |Po przejściu, jeśli klucze dostępu do konta magazynu są aktualizowane, użytkownik będzie musiał ręcznie skonfigurować klucze dostępu dla zmigrowanego konta magazynu za pośrednictwem usługi StorSimple Device Manager.|
| Klasyczne konto magazynu na koncie magazynu usługi Azure Resource Manager      | Tak        | Nie             |Migrowanie z klasycznego do usługi Azure Resource Manager |<li>Aby uzyskać szczegółowe instrukcje dotyczące migracji konta magazynu z klasycznego do usługi Azure Resource Manager, przejdź do [migrowania klasycznego konta magazynu](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Jeśli klucze dostępu do konta magazynu są aktualizowane po migracji, użytkownik będzie musiał zsynchronizować klucze dostępu dla zmigrowanego konta magazynu za pośrednictwem usługi StorSimple Device Manager. Ma to na celu zapewnienie, że urządzenia StorSimple nadal działają normalnie i są w stanie warstwy danych podstawowych/kopii zapasowych na platformie Azure. Aby uzyskać szczegółowe instrukcje dotyczące synchronizowania kluczy dostępu, przejdź do [przepływu pracy Obrót](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> W przypadku urządzenia StorSimple Cloud Appliance, jeśli klasyczne konto magazynu jest migrowane, ale podstawowa maszyna wirtualna nadal pozostaje w wersji klasycznej, urządzenie powinno działać poprawnie. Jeśli podstawowa maszyna wirtualna dla urządzenia w chmurze jest migrowana, funkcja dezaktywacji i usuwania nie będzie działać.</li><li> Należy utworzyć nowe urządzenia storsimple w chmurze w witrynie Azure portal, a następnie w trybie fail over ze starszych urządzeń w chmurze. Nie można utworzyć urządzenia StorSimple Cloud Appliance w nowym portalu Azure przy użyciu klasycznego konta magazynu, muszą mieć konto magazynu usługi Azure Resource Manager. Aby uzyskać więcej informacji, przejdź do [wdrażania urządzenia StorSimple Cloud Appliance i zarządzania nim.](storsimple-8000-cloud-appliance-u2.md)</li>|

## <a name="datacenter-changes"></a>Zmiany w centrum danych

| Można przenieść ...| Obsługiwane|Downtime (Przestoje)| Proces pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Usługa StorSimple z jednego centrum danych platformy Azure do innej? | Nie | Tak |Brak, proces ręczny  |<li>Migrowanie danych z urządzenia StorSimple.</li><li>Wykonaj przywrócenie ustawień fabrycznych urządzenia, spowoduje to usunięcie wszystkich danych lokalnych na urządzeniu.</li><li>Zarejestruj urządzenie w nowej subskrypcji nowej usługi StorSimple Device Manager.</li><li>Migrowanie danych z powrotem do urządzenia.|
| Konto magazynu z jednego centrum danych platformy Azure do innego? | Nie |Tak  |Brak, proces ręczny  | Tak samo jak powyżej.|

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie usługi StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Wdrażanie urządzenia z serii StorSimple 8000 w witrynie Azure portal](storsimple-8000-deployment-walkthrough-u2.md)
