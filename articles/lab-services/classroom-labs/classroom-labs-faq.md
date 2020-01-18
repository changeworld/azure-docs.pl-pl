---
title: Pracowni Labs w Azure Lab Services — często zadawane pytania | Microsoft Docs
description: Znajdź odpowiedzi na często zadawane pytania dotyczące klas z laboratoriami w Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 3bc58e9545f38508a9e08e9ae1aa9cf8713cc520
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264750"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Pracowni Labs w Azure Lab Services — często zadawane pytania
Uzyskaj odpowiedzi na kilka typowych pytań dotyczących laboratoriów stacjonarnych w Azure Lab Services. 

## <a name="quotas"></a>Przydziały

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Czy przydziały na użytkownika, czy na tydzień czy na cały czas trwania laboratorium? 
Przydział ustawiony dla laboratorium jest przeznaczony dla każdego ucznia na cały czas trwania laboratorium. [Zaplanowana godzina działania maszyn wirtualnych](how-to-create-schedules.md) nie jest uwzględniana w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych.  Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Harmonogramy

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Czy wszystkie maszyny wirtualne w laboratorium są uruchamiane automatycznie po ustawieniu harmonogramu? 
Nie. Nie wszystkie maszyny wirtualne. Tylko maszyny wirtualne, które są przypisane do użytkowników zgodnie z harmonogramem. Maszyny wirtualne, które nie są przypisane do użytkownika, nie są automatycznie uruchamiane. Jest to zaprojektowane. 

## <a name="lab-accounts"></a>Konta laboratorium

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Dlaczego nie mogę utworzyć laboratorium z powodu niedostępności zakresu adresów? 
Pracownice klasy mogą tworzyć maszyny wirtualne laboratorium w zakresie adresów IP określonym podczas tworzenia konta laboratorium w Azure Portal. Po podaniu zakresu adresów każde laboratorium, które jest tworzone po przypisaniu 512 adresów IP dla maszyn wirtualnych laboratorium. Zakres adresów dla konta laboratorium musi być wystarczająco duży, aby pomieścić wszystkie laboratoria, które mają zostać utworzone w ramach konta laboratorium. 

Na przykład jeśli masz blok/19-10.0.0.0/19, ten zakres adresów uwzględnia 8192 adresy IP i 16 laboratoriów (8192/512 = 16 laboratoriów). W takim przypadku tworzenie laboratorium kończy się niepowodzeniem na siedemnastu tworzenia laboratorium.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jakie zakresy portów należy otworzyć w ustawieniach zapory organizacji, aby połączyć się z maszynami wirtualnymi laboratorium za pośrednictwem protokołu RDP/SSH?

Porty to: 49152 – 65535. W laboratoriach stacjonarnych znajduje się za modułem równoważenia obciążenia, więc wszystkie maszyny wirtualne w laboratorium mają jeden adres IP, a każda maszyna wirtualna w laboratorium ma unikatowy port. Numery portów i publiczny adres IP mogą być zmieniane za każdym razem, gdy laboratorium jest ponownie publikowane.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaki zakres publicznych adresów IP należy otworzyć w ustawieniach zapory organizacji w celu połączenia z maszynami wirtualnymi laboratorium za pośrednictwem protokołu RDP/SSH?
Zobacz [zakresy adresów IP i Tagi usług platformy Azure — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519), która udostępnia publiczny zakres adresów IP dla centrów danych na platformie Azure. Możesz otworzyć adresy IP dla regionów, w których znajdują się konta w laboratorium.

## <a name="users"></a>Użytkownicy

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Ilu użytkowników może być w laboratorium zajęć?
Do laboratorium zajęć można dodać maksymalnie 400 użytkowników. 

## <a name="blog-post"></a>Wpis w blogu
Zasubskrybuj [blog Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Powiadomienia o aktualizacjach
Zasubskrybuj [Aktualizacje usług Lab Services](https://azure.microsoft.com/updates/?product=lab-services) , aby otrzymywać informacje o nowych funkcjach usługi Lab Services.

## <a name="general"></a>Ogólne
### <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli w tym miejscu nie udzielono odpowiedzi na moje pytanie?
Jeśli pytania nie ma na liście, daj nam znać, aby pomóc Ci znaleźć odpowiedź.

- Opublikuj pytanie na końcu tego często zadawanych pytań. 
- Aby dotrzeć do szerszego grona użytkowników, Opublikuj pytanie na [Azure Lab Services — Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- W przypadku żądań funkcji Prześlij swoje żądania i pomysły dotyczące [Azure Lab Services — User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

