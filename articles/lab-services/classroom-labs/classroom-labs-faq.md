---
title: Laboratoria w klasie w usługach Azure Lab — często zadawane pytania | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące laboratoriów w klasie w usługach Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443503"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Laboratoria w classroom w usługach Azure Lab — często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)
Uzyskaj odpowiedzi na niektóre z najczęstszych pytań dotyczących laboratoriów w laboratoriach w usłudze Azure Lab Services. 

## <a name="quotas"></a>Przydziały

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Czy przydział przypada na użytkownika, tydzień lub cały czas trwania laboratorium? 
Przydział ustawiony dla laboratorium jest dla każdego ucznia przez cały czas trwania laboratorium. I [zaplanowany czas pracy maszyn wirtualnych](how-to-create-schedules.md) nie jest wliczany do przydziału przydzielonego użytkownikowi. Przydział jest dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych.  Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Jeśli profesor włącza maszynę wirtualną dla studentów, czy ma to wpływ na przydział studentów? 
Nie. Nie. Gdy profesor włącza maszynę wirtualną studenta, nie ma to wpływu na przydział przydzielony studentowi. 

## <a name="schedules"></a>Harmonogramy

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Czy wszystkie maszyny wirtualne w laboratorium uruchamiają się automatycznie po ustawieniu harmonogramu? 
Nie. Nie wszystkie maszyny wirtualne. Tylko maszyny wirtualne, które są przypisane do użytkowników zgodnie z harmonogramem. Maszyny wirtualne, które nie są przypisane do użytkownika nie są uruchamiane automatycznie. To jest zgodnie z projektem. 

## <a name="lab-accounts"></a>Konta laboratorium

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Dlaczego nie mogę utworzyć laboratorium z powodu niedostępności zakresu adresów? 
Laboratoria classroom można tworzyć maszyny wirtualne laboratorium w zakresie adresów IP określone podczas tworzenia konta laboratorium w witrynie Azure portal. Po podaniu zakresu adresów każde laboratorium utworzone po przydzieleniu 512 adresów IP dla maszyn wirtualnych w laboratorium. Zakres adresów dla konta laboratorium musi być wystarczająco duży, aby pomieścić wszystkie laboratoria, które zamierzasz utworzyć w ramach konta laboratorium. 

Na przykład jeśli masz blok /19 - 10.0.0.0/19, ten zakres adresów obsługuje 8192 adresy IP i 16 laboratoriów(8192/512 = 16 laboratoriów). W takim przypadku tworzenie laboratorium kończy się niepowodzeniem przy tworzeniu 17 laboratorium.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jakie zakresy portów należy otworzyć w ustawieniach zapory mojej organizacji, aby połączyć się z maszynami wirtualnymi laboratorium za pośrednictwem protokołu RDP/SSH?

Porty to: 49152–65535. Laboratoria szkolne siedzą za modułem równoważenia obciążenia. Każde laboratorium ma jeden publiczny adres IP, a każda maszyna wirtualna w laboratorium ma unikatowy port. 

Można również zobaczyć prywatny adres IP każdej maszyny wirtualnej na karcie **Puli maszyny wirtualnej** na stronie głównej dla laboratorium w witrynie Azure portal. Jeśli ponownie opublikujesz laboratorium, publiczny adres IP laboratorium nie ulegnie zmianie, ale prywatny adres IP i numer portu każdej maszyny wirtualnej w laboratorium mogą ulec zmianie. Więcej informacji można dowiedzieć się w artykule: [Ustawienia zapory dla usług Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaki publiczny zakres adresów IP powinien być otwarty w ustawieniach zapory mojej organizacji, aby połączyć się z maszynami wirtualnymi laboratorium za pośrednictwem protokołu RDP/SSH?
Zobacz [Zakresy adresów IP platformy Azure i tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519)— która zapewnia publiczny zakres adresów IP dla centrów danych na platformie Azure. Można otworzyć adresy IP dla regionów, w których znajdują się konta laboratoryjne.

## <a name="virtual-machine-images"></a>Obrazy maszyn wirtualnych

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Dlaczego, jako twórca laboratorium, nie mogę włączyć dodatkowych opcji obrazu w menu rozwijanym obrazów maszyn wirtualnych podczas tworzenia nowego laboratorium?

Gdy administrator doda Cię jako twórcę laboratorium do konta laboratorium, masz uprawnienia do tworzenia laboratoriów. Nie masz jednak uprawnień do edytowania żadnych ustawień wewnątrz konta laboratorium, w tym listy włączonych obrazów maszyn wirtualnych. Aby włączyć dodatkowe obrazy, skontaktuj się z administratorem konta laboratorium, aby to zrobić za Ciebie, lub poproś administratora, aby dodał Cię jako rolę współautora do konta laboratorium. Rola współautora daje uprawnienia do edytowania listy obrazów maszyny wirtualnej na koncie laboratorium.

## <a name="users"></a>Użytkownicy

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Ilu użytkowników może być w laboratorium w klasie?
Do laboratorium w klasie można dodać do 400 użytkowników. 

## <a name="blog-post"></a>Blogu
Subskrybuj [blog usługi Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Aktualizowanie powiadomień
Subskrybuj [aktualizacje usług laboratoryjnych,](https://azure.microsoft.com/updates/?product=lab-services) aby być na bieżąco z nowymi funkcjami w usługach laboratoryjnych.

## <a name="general"></a>Ogólne
### <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli nie ma tutaj odpowiedzi na moje pytanie?
Jeśli twojego pytania nie ma na liście, poinformuj nas o tym, abyśmy mogli pomóc Ci znaleźć odpowiedź.

- Zadaj pytanie na końcu tego faq. 
- Aby dotrzeć do szerszego grona odbiorców, opublikuj pytanie na [forum Usługi Azure Lab — Stack Overflow](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- W przypadku żądań funkcji należy przesyłać żądania i pomysły do [usługi Azure Lab Services — User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

