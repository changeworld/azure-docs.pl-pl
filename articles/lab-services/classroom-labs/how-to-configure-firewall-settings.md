---
title: Ustawienia zapory dla Azure Lab Services
description: Dowiedz się, jak określić publiczny adres IP i zakres numerów portów maszyn wirtualnych w laboratorium, aby można było dodać informacje do reguł zapory.
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692773"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Ustawienia zapory dla Azure Lab Services

Każda organizacja lub szkoła skonfiguruje własną sieć w taki sposób, który najlepiej pasuje do swoich potrzeb.  Czasami obejmuje ustawienie reguł zapory, które blokują połączenia Remote Desktop Protocol (RDP) lub Secure Shell (SSH) z maszynami spoza sieci.  Ponieważ Azure Lab Services działa w chmurze publicznej, niektóre dodatkowe konfiguracje mogą być konieczne, aby umożliwić uczniom dostęp do maszyny wirtualnej podczas nawiązywania połączenia z sieci kampusowej.

Każde laboratorium używa jednego publicznego adresu IP i wielu portów.  Na wszystkich maszynach wirtualnych, na maszynach wirtualnych z szablonami i dla uczniów, będzie używany ten publiczny adres IP.  Publiczny adres IP nie zmieni się w okresie istnienia laboratorium.  Jednak każda maszyna wirtualna będzie mieć inny numer portu.  Numery portów mieszczą się w zakresie od 49152 do 65535.  Kombinacja publicznego adresu IP i numeru portu służy do łączenia instruktorów i studentów z poprawną maszyną wirtualną.  W tym artykule opisano sposób znajdowania określonego publicznego adresu IP używanego przez laboratorium.  Te informacje mogą służyć do aktualizowania reguł zapory dla ruchu przychodzącego i wychodzącego, aby studenci mogli uzyskiwać dostęp do swoich maszyn wirtualnych.

>[!IMPORTANT]
>Każde laboratorium będzie miało inny publiczny adres IP.

## <a name="find-public-ip-for-a-lab"></a>Znajdź publiczny adres IP dla laboratorium

Publiczne adresy IP dla każdego laboratorium są wymienione w bloku **Wszystkie laboratoria** konta laboratorium usług Lab Services.  Aby uzyskać instrukcje dotyczące znajdowania bloku **Wszystkie laboratoria** , zobacz [jak zarządzać laboratoriami na koncie laboratorium](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![wszystkie](../media/how-to-configure-firewall-settings/all-labs-properties.png) bloku laboratoria

>[!NOTE]
>Publiczny adres IP nie zostanie wyświetlony, jeśli komputer szablonu dla laboratorium nie zostanie jeszcze opublikowany.

## <a name="conclusion"></a>Podsumowanie

Teraz wiemy publiczny adres IP dla laboratorium.  Reguły ruchu przychodzącego i wychodzącego można utworzyć dla zapory organizacji dla publicznego adresu IP i zakresu portów 49152-65535.  Po zaktualizowaniu reguł uczniowie mogą uzyskać dostęp do swoich maszyn wirtualnych bez dostępu blokowania zapory sieciowej.
