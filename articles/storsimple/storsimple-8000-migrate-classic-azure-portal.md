---
title: Migrowanie kont magazynu, subskrypcji dla usługi Menedżer urządzeń StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację subskrypcji, kont magazynu dla service8000 usługi Menedżer urządzeń StorSimple.
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
ms.openlocfilehash: 3cce18fa1890fc9e518294e294cc43e0e55065aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631537"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrowanie subskrypcji i kontami magazynu skojarzonymi z usługi Menedżer urządzeń StorSimple

Może być konieczne przenieść usługi StorSimple do nowej rejestracji lub do nowej subskrypcji. Scenariusze migracji są zmiany konta lub zmiany centrum danych. Skorzystaj z poniższej tabeli, aby zrozumieć, w której te scenariusze są obsługiwane w tym szczegółowe kroki wymagane do przeniesienia.

## <a name="account-changes"></a>Zmiany konta

| Czy jest możliwe przeniesienie...| Obsługiwane| Przestój| Procesu pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Całej subskrypcji (w tym kont magazynu i usługi StorSimple) do innego rejestracji? | Yes       | Nie       | **Transfer rejestracji**<br>Użycie:<li>Po zakupie nowych okresów zobowiązania platformy Azure w ramach nowej umowy.</li><li>Użytkownik chce migrować wszystkich kont i subskrypcji z rejestracji w starej do nowej. Obejmuje to wszystkich usług platformy Azure w ramach starego subskrypcji.</li> | **Krok 1. Otwórz bilet pomocy technicznej operacji Enterprise platformy Azure.**<li>Przejdź do obszaru [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) (Ustawienia — Integracje i usługi).</li><li> Wybierz **administracji rejestracji** , a następnie wybierz **przenieść z jednej rejestracji do nowej rejestracji**.<br>**Krok 2. Podaj wymagane informacje**<br>Obejmują:<li>numer rejestracji źródła</li><li> docelowy numer rejestracji</li><li>Data rozpoczęcia obowiązywania transferu|
| Usługa StorSimple z istniejącego konta do nowej rejestracji?    | Yes       | Nie       | **Przeniesienie konta**<br>Użycie:<li>Jeśli nie chcesz transferu pełną rejestrację.</li><li>Chcesz przenieść określonych kont do nowej rejestracji.</li>| **Krok 1. Otwórz bilet pomocy technicznej operacji Enterprise platformy Azure.**<li>Przejdź do obszaru [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) (Ustawienia — Integracje i usługi).</li><li>Wybierz **administracji rejestracji** , a następnie wybierz **przeniesienia konta EA do nowej rejestracji**.<br>**Krok 2. Podaj wymagane informacje**<br>Obejmują:<li>numer rejestracji źródła</li><li> docelowy numer rejestracji</li><li>Data rozpoczęcia obowiązywania transferu|
| Usługa StorSimple z jednej subskrypcji do innej subskrypcji?      | Nie        |    Yes         | Żaden proces ręczny|<li>Migracja danych poza urządzeniem StorSimple.</li><li>Wykonaj Reset do ustawień fabrycznych urządzenia, to usuwa wszystkie dane lokalne na urządzeniu.</li><li>Rejestrowanie urządzenia z nową subskrypcję w usłudze Menedżer urządzeń StorSimple.</li><li>Wróć do urządzenia, należy przeprowadzić migrację danych.|
|Czy mogę przenieść własność subskrypcji platformy Azure do innego katalogu? | Yes       | Nie       | Kojarzenie istniejącej subskrypcji do katalogu usługi Azure AD | Zapoznaj się [skojarzyć istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.|
| Urządzenia StorSimple z jednej usługi Menedżer urządzeń StorSimple do innej usługi w różnych regionach?      | Nie        | Yes            | Żaden proces ręczny |Wartość taka sama jak powyżej.|
| Konto magazynu do nowej subskrypcji lub grupy zasobów?     | Yes        | Nie             |Przenieś konta magazynu do innej subskrypcji lub grupy zasobów |Po przeniesieniu, jeśli klucze dostępu konta magazynu są aktualizowane, użytkownik będzie musiał skonfigurować ręcznie na koncie magazynu migrowane za pomocą usługi Menedżer urządzeń StorSimple klucze dostępu.|
| Klasycznego konta magazynu do konta magazynu usługi Azure Resource Manager      | Yes        | Nie             |Migrowanie z wersji klasycznej do usługi Azure Resource Manager |<li>Aby uzyskać szczegółowe instrukcje dotyczące sposobu przeprowadzenia migracji konta magazynu z wersji klasycznej do usługi Azure Resource Manager, przejdź do [migrację konta magazynu klasycznego](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Jeśli klucze dostępu konta magazynu zostaną zaktualizowane po migracji, użytkownik musi zsynchronizować klucze dostępu dla konta magazynu migrowane za pomocą usługi Menedżer urządzeń StorSimple. To jest zapewnienie urządzeń StorSimple będzie działać normalnie i są możliwe do warstwy podstawowej/kopia zapasowa danych na platformie Azure. Aby uzyskać szczegółowe informacje dotyczące synchronizacji kluczy dostępu, przejdź do [przepływu pracy obrotu](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> W przypadku urządzenia StorSimple w chmurze migracji konta klasycznego magazynu, ale podstawowej maszyny wirtualnej nadal pozostaje w modelu klasycznym, urządzenie powinno działać poprawnie. Jeśli zostanie zmigrowany podstawowej maszyny wirtualnej dla urządzenia w chmurze, Dezaktywuj i usuń funkcje nie będzie działać.</li><li> Należy utworzyć nowe urządzenia StorSimple w chmurze w witrynie Azure portal i następnie przełączyć w tryb failover z starsze urządzenia w chmurze. Nie można utworzyć urządzenie StorSimple w chmurze w nowej witrynie Azure portal przy użyciu klasycznego konta magazynu, muszą mieć konto magazynu usługi Azure Resource Manager. Aby uzyskać więcej informacji, przejdź do [wdrażanie i zarządzanie nimi urządzenie StorSimple w chmurze](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Zmiany centrum danych

| Czy jest możliwe przeniesienie...| Obsługiwane|Przestój| Procesu pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Usługa StorSimple z jednego centrum danych platformy Azure do innego? | Nie | Yes |Żaden proces ręczny  |<li>Migracja danych poza urządzeniem StorSimple.</li><li>Wykonaj Reset do ustawień fabrycznych urządzenia, to usuwa wszystkie dane lokalne na urządzeniu.</li><li>Rejestrowanie urządzenia za pomocą nowej subskrypcji do nowej usługi Menedżer urządzeń StorSimple.</li><li>Wróć do urządzenia, należy przeprowadzić migrację danych.|
| Konto magazynu, z jednym centrum danych platformy Azure do innego? | Nie |Yes  |Żaden proces ręczny  | Wartość taka sama jak powyżej.|

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie usługi Menedżer urządzeń StorSimple](storsimple-8000-manage-service.md)
* [Wdrażanie urządzenia StorSimple 8000 series w witrynie Azure portal](storsimple-8000-deployment-walkthrough-u2.md)
