---
title: Tworzenie harmonogramu dla laboratoriów w laboratoriach w usłudze Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć harmonogramy dla laboratoriów w laboratoriach platformy Azure Lab Services, aby maszyny wirtualne w laboratoriach uruchamiały się i wyłączały w określonym czasie.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72330538"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Tworzenie harmonogramów laboratoriów w laboratoriach w usłudze Azure Lab Services i zarządzanie nimi 
Harmonogramy umożliwiają skonfigurowanie laboratorium w klasie w taki sposób, aby maszyny wirtualne w laboratorium automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub harmonogram cykliczny. Poniższe procedury umożliwiają tworzenie harmonogramów dla laboratorium w klasie i zarządzanie nimi: 

> [!IMPORTANT]
> Zaplanowany czas pracy maszyn wirtualnych nie jest wliczany [do przydziału przydzielonego użytkownikowi.](how-to-configure-student-usage.md#set-quotas-for-users) Przydział jest dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="set-a-schedule-for-the-lab"></a>Ustawianie harmonogramu laboratorium
Utwórz zaplanowane zdarzenie dla laboratorium, tak aby maszyny wirtualne w laboratorium były automatycznie uruchamiane/zatrzymywane o określonych porach. Przydział użytkownika określony wcześniej jest dodatkowy czas przypisany do każdego użytkownika poza tym zaplanowanym czasie. 

1. Przełącz się do strony **Harmonogramy** i wybierz pozycję **Dodaj zaplanowane zdarzenie** na pasku narzędzi. 

    ![Przycisk Dodaj harmonogram na stronie Harmonogramy](../media/how-to-create-schedules/add-schedule-button.png)
2. Upewnij się, że **standard** jest wybrany **typ zdarzenia**. Wybierz **start tylko** określić tylko czas rozpoczęcia dla maszyn wirtualnych. Wybierz **stop tylko** określić tylko czas zatrzymania dla maszyn wirtualnych. 
7. W sekcji **Powtórz** wybierz bieżący harmonogram. 

    ![Przycisk Dodaj harmonogram na stronie Harmonogramy](../media/how-to-create-schedules/select-current-schedule.png)
5. W oknie dialogowym **Powtarzanie** wykonaj następujące czynności:
    1. Upewnij się, że dla pola **Powtórz** jest ustawiony **co tydzień.** 
    3. Określ **datę rozpoczęcia**.
    4. Określ **godzinę rozpoczęcia,** w której mają być uruchamiane maszyny wirtualne.
    5. Określ **czas zatrzymania,** w którym maszyny wirtualne mają zostać zamknięte. 
    6. Określ **strefę czasową** dla określonych godzin rozpoczęcia i zatrzymania. 
    2. Wybierz dni, w których harmonogram ma obowiązywać. W poniższym przykładzie wybrano od poniedziałku do czwartku. 
    8. Wybierz **pozycję Zapisz**. 

        ![Ustawianie harmonogramu powtarzania](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Teraz na stronie **Dodaj zaplanowane wydarzenie** w polu **Notatki (opcjonalnie)** wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
4. Na stronie **Dodawanie zaplanowanego wydarzenia** wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Wyświetlanie harmonogramów w kalendarzu
Zaplanowane daty i godziny są wyróżnione w widoku kalendarza, jak pokazano na poniższej ilustracji:

![Harmonogramy w widoku kalendarza](../media/how-to-create-schedules/schedules-calendar.png)

Wybierz przycisk **Dzisiaj** w prawym górnym rogu, aby przełączyć się na bieżącą datę w kalendarzu. Wybierz strzałkę w **lewo,** aby przełączyć się na poprzedni tydzień i strzałkę w **prawo,** aby przełączyć się na następny tydzień w kalendarzu. 

## <a name="edit-a-schedule"></a>Edytowanie harmonogramu
Po wybraniu wyróżnionego harmonogramu w kalendarzu zostaną wyświetlone przyciski służące do **edytowania** lub **usuwania** harmonogramu. 

![Edytuj stronę harmonogramu](../media/how-to-create-schedules/schedule-edit-button.png)

Na stronie **Edytowanie zaplanowanego wydarzenia** możesz zaktualizować harmonogram i wybrać pozycję **Zapisz**. 

## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

1. Aby usunąć harmonogram, wybierz wyróżniony harmonogram w kalendarzu i wybierz przycisk Ikona kosza (usuń):

    ![Przycisk Usuń na pasku narzędzi](../media/how-to-create-schedules/schedule-delete-button.png)
2. W oknie dialogowym **Usuwanie zaplanowanego zdarzenia** wybierz pozycję **Tak,** aby potwierdzić usunięcie. 



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, uzyskaj dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)
