---
title: Przenosimy obrazy maszyn wirtualnych do magazynu dysków zarządzanych w portalu Azure Marketplace
description: Aby zapewnić szybszą i bardziej niezawodną pamięć masową i obsługę nowych funkcji i możliwości w portalu Marketplace, przenosimy obrazy maszyn wirtualnych z rynku do zarządzanego magazynu dysków.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285116"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Przenosimy obrazy maszyn wirtualnych (VM) w witrynie Azure Marketplace do zarządzanego magazynu dysków

Aby zapewnić szybszą i bardziej niezawodną pamięć masową i obsługę nowych funkcji i możliwości w portalu Marketplace, przenosimy obrazy maszyn wirtualnych z rynku do zarządzanego magazynu dysków.

Począwszy od 2 stycznia 2020 r., przeniesiemy obrazy maszyn wirtualnych do magazynu dysków zarządzanych w fazach. W pierwszej fazie będziemy przenosić obrazy tylko bez nowych wdrożeń lub uruchomionych maszyn wirtualnych w ciągu ostatnich 90 dni. Zanim przeniesiemy którykolwiek z obrazów, wyślemy wiadomość e-mail z powiadomieniem wydawcy, które obrazy zostaną przeniesione i kiedy zostaną przeniesione.

Wydawcy lub konsumenci nie muszą podejmować żadnych działań, a użytkownicy nie będą mieli wpływu. Oferty portalu Marketplace pozostaną dostępne, a klienci nadal będą mogli wdrażać zarządzane maszyny wirtualne z tych obrazów w trakcie i po przeprowadzce.

Jeśli masz jakieś pytania, [skontaktuj się z nami.](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)

## <a name="faqs"></a>Często zadawane pytania

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Czy użytkownicy obrazów maszyny Wirtualnej wystąpią awaria?

Użytkownicy obrazów maszyny Wirtualnej nie wystąpią awaria. 

W pierwszej fazie będziemy przenosić tylko obrazy maszyn wirtualnych, które nie mają uruchomionych maszyn wirtualnych. Ponieważ nie ma użytkowników dla tych obrazów, nie będzie żadnego wpływu. Również w kolejnych fazach nie będzie to miało wpływu na użytkowników.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Jak długo trwa zakończenie procesu?

Migracja może potrwać do 24 godzin.

### <a name="do-i-need-to-take-any-action"></a>Czy muszę podjąć jakieś działania?

Nie. Wydawcy lub konsumenci nie muszą podejmować żadnych działań.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Czy muszę zaktualizować system, aby wywołać interfejsy API portalu w chmurze w inny sposób po przeniesieniu ich do magazynu dysku zarządzanego?

Nie. Istniejące wywołania interfejsu API będą nadal działać.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Czy wszystkie moje obrazy maszyn wirtualnych zostaną przeniesione na dysk zarządzany w tym samym czasie?

Będziemy przenosić wszystkie obrazy maszyn wirtualnych tego samego dnia. Powiadomimy Cię o tym po ich przeniesieniu.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Czy mogę poprosić o zaplanowanie przeniesienia obrazów maszyny Wirtualnej na późniejszy czas?

Zalecamy przeniesienie obrazów w zaplanowanym terminie. Jeśli jednak masz wątpliwości, skontaktuj się z nami, aby przesunąć ruch.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Czy mogę publikować aktualizacje obrazów maszyn wirtualnych podczas przenoszenia?

Podczas przenoszenia nie można dokonać aktualizacji obrazów maszyn wirtualnych.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Czy proces publikowania zmieni się po przeniesieniu obrazu maszyny Wirtualnej na dysk zarządzany?

Nie, proces publikowania pozostanie taki sam. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Czy wydawca może przenieść swoje oferty na dysk zarządzany?

Nie, wydawcy nie mogą przenosić swoich ofert na dysk zarządzany. Będą musieli poczekać, a ich obrazy zostaną przeniesione automatycznie. Powiadomimy wydawcę przed wprowadzeniem jakichkolwiek zmian.
