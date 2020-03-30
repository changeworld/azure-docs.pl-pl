---
title: Konfigurowanie kont laboratoryjnych w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia konta laboratorium, wyświetlania wszystkich kont laboratorium lub usuwania konta laboratorium w usłudze Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284306"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurowanie kont laboratoryjnych w usługach Azure Lab Services 
W usłudze Azure Lab Services konto laboratorium jest kontenerem dla zarządzanych typów laboratoriów, takich jak laboratoria w klasie. Administrator konfiguruje konto laboratorium za pomocą usługi Azure Lab Services i zapewnia dostęp do właścicieli laboratoriów, którzy mogą tworzyć laboratoria na koncie. 

W tym artykule pokazano, jak wykonać następujące zadania: 

- Określanie zakresu adresów dla maszyn wirtualnych w laboratorium
- Konfigurowanie automatycznego zamykania maszyn wirtualnych po rozłączeniu

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Określanie zakresu adresów dla maszyn wirtualnych w laboratorium
Poniższa procedura ma kroki, aby określić zakres adresów dla maszyn wirtualnych w laboratorium. Jeśli zaktualizujesz zakres, który został wcześniej określony, zmodyfikowany zakres adresów ma zastosowanie tylko do maszyn wirtualnych, które są tworzone po wykonaniu zmiany. 

Oto kilka ograniczeń podczas określania zakresu adresów, o których należy pamiętać. 

- Prefiks musi być mniejszy lub równy 23. 
- Jeśli sieć wirtualna jest równorzędna z kontem laboratorium, podany zakres adresów nie może pokrywać się z zakresem adresów z sieci wirtualnej równorzędnej.

1. Na stronie **Konto laboratorium** wybierz pozycję **Ustawienia laboratoriów** w menu po lewej stronie.
2. W polu **Zakres adresów** określ zakres adresów maszyn wirtualnych, które zostaną utworzone w laboratorium. Zakres adresów powinien znajdować się w notacji cidr (cidr) bezklasowej międzydomenowej (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Konfigurowanie zakresu adresów](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatyczne wyłączanie maszyn wirtualnych po odłączeniu
Po odłączeniu połączenia pulpitu zdalnego można włączyć lub wyłączyć automatyczne zamykanie maszyn wirtualnych z laboratorium systemu Windows (szablonu lub ucznia). Można również określić, jak długo maszyny wirtualne powinny czekać na ponowne połączenie użytkownika przed automatycznym zamknięciem.

![Automatyczne zamykanie na koncie laboratorium](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

To ustawienie dotyczy wszystkich laboratoriów utworzonych na koncie laboratorium. Właściciel laboratorium można zastąpić to ustawienie na poziomie laboratorium. Zmiana tego ustawienia na koncie laboratorium będzie miała wpływ tylko na laboratoria, które są tworzone po dokonaniu zmiany.

Aby dowiedzieć się, jak właściciel laboratorium może skonfigurować to ustawienie na poziomie laboratorium, zobacz [ten artykuł](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Łączenie sieci laboratorium za pomocą sieci wirtualnej równorzędnej](how-to-connect-peer-virtual-network.md)
- [Dołączanie udostępnionej galerii obrazów do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
