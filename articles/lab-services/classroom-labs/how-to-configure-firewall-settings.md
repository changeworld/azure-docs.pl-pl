---
title: Ustawienia zapory dla usługi Azure Lab Services
description: Dowiedz się, jak określić publiczny adres IP i zakres numerów portów maszyn wirtualnych w laboratorium, aby informacje można było dodać do reguł zapory.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443463"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Ustawienia zapory dla usługi Azure Lab Services

Każda organizacja lub szkoła stworzy własną sieć w sposób, który najlepiej odpowiada ich potrzebom.  Czasami obejmuje to ustawianie reguł zapory blokujących połączenia protokołu RDP (Remote Desktop Protocol) lub Secure Shell (ssh) z komputerami spoza ich własnej sieci.  Ponieważ usługa Azure Lab Services działa w chmurze publicznej, może być potrzebna dodatkowa konfiguracja, aby umożliwić uczniom dostęp do maszyny wirtualnej podczas łączenia się z siecią kampusu.

Każde laboratorium używa jednego publicznego adresu IP i wielu portów.  Wszystkie maszyny wirtualne, zarówno szablon maszyny wirtualne, jak i maszyny wirtualne dla studentów, będą używać tego publicznego adresu IP.  Publiczny adres IP nie zmieni się przez cały okres pracy laboratorium.  Jednak każda maszyna wirtualna będzie miała inny numer portu.  Numery portów wahają się od 49152 do 65535.  Kombinacja publicznego adresu IP i numeru portu służy do łączenia instruktora i studentów z poprawną maszyną wirtualną.  W tym artykule opisano, jak znaleźć określony publiczny adres IP używany przez laboratorium.  Te informacje mogą służyć do aktualizowania reguł zapory przychodzącej i wychodzącej, aby uczniowie mogli uzyskiwać dostęp do swoich maszyn wirtualnych.

>[!IMPORTANT]
>Każde laboratorium będzie miało inny publiczny adres IP.

## <a name="find-public-ip-for-a-lab"></a>Znajdowanie publicznych adresów IP dla laboratorium

Publiczne adresy IP dla każdego laboratorium są wymienione na stronie **Wszystkie laboratoria** konta laboratorium usług laboratoryjnych.  Aby uzyskać wskazówki dotyczące znajdowania strony **Wszystkie laboratoria,** zobacz [jak zarządzać laboratoriami na koncie laboratoryjnym](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![Strona Wszystkie laboratoria](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Nie zobaczysz publicznego adresu IP, jeśli komputer szablonu dla twojego laboratorium nie został jeszcze opublikowany.

## <a name="conclusion"></a>Podsumowanie

Teraz znamy publiczny adres IP laboratorium.  Reguły ruchu przychodzącego i wychodzącego można utworzyć dla zapory organizacji dla publicznego adresu IP i zakresu portów 49152-65535.  Po zaktualizowaniu reguł uczniowie mogą uzyskiwać dostęp do swoich maszyn wirtualnych bez dostępu do zapory sieciowej.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Łączenie sieci laboratorium za pomocą sieci wirtualnej równorzędnej](how-to-connect-peer-virtual-network.md)
- [Dołączanie udostępnionej galerii obrazów do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Konfigurowanie innych ustawień dla laboratorium](how-to-configure-lab-accounts.md)
