---
title: Tworzenie harmonogramu laboratorium na potrzeby zajęć w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć harmonogramy dla laboratorium na potrzeby zajęć w usłudze Azure Lab Services, dzięki czemu maszyny wirtualne w laboratorium, uruchom i zamknij o określonej godzinie.
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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60696038"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Tworzenie i zarządzanie nimi harmonogramy dla laboratorium na potrzeby zajęć w usłudze Azure Lab Services 
Harmonogramy umożliwiają konfigurowanie laboratorium na potrzeby zajęć w taki sposób, że maszyny wirtualne w laboratorium automatycznie uruchom i zamknij o określonej godzinie. Można zdefiniować jednorazowe harmonogramu lub Harmonogram cykliczny. Poniższe procedury stanowią kroki, aby tworzyć i zarządzać nimi harmonogramy dla laboratorium na potrzeby zajęć: 

> [!IMPORTANT]
> Planowana godzina uruchomione maszyny wirtualne nie wliczają [przydziału przydzielony do użytkownika](how-to-configure-student-usage.md#set-quotas-per-user). Limit przydziału jest czas poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="add-a-schedule-once"></a>Dodaj harmonogram (raz)

1. Przełącz się do **harmonogramy** strony i wybierz **harmonogramu Dodaj** na pasku narzędzi. 

    ![Dodaj przycisk harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/add-schedule-button.png)
2. Na **harmonogramu Dodaj** strony, upewnij się, że **raz** wybrano opcję u góry. Jeśli nie, wybierz **raz**. 
3. Aby uzyskać **zaplanować daty (wymagane)** wprowadź datę lub wybierz ikonę kalendarza, aby wybrać datę. 
4. Aby uzyskać **czas rozpoczęcia**, wybierz czas, kiedy zechcesz, maszyny wirtualne, które ma zostać uruchomiony. Czas rozpoczęcia jest wymagany, jeśli nie ustawiono czas przestoju. Wybierz **Usuń zdarzenie początkowe** Jeśli chcesz określić tylko godzina zatrzymania. Jeśli **czas rozpoczęcia** jest wyłączona, wybierz **Dodaj zdarzenie rozpoczęcia** obok listy rozwijanej, aby go włączyć. 
5. Aby uzyskać **czas zakończenia**, wybierz czas, kiedy zechcesz, maszyny wirtualne na zamykanie. Czas przestoju jest wymagany, jeśli nie ustawiono czasu rozpoczęcia. Wybierz **zdarzenie stop usuwania** Jeśli chcesz określić tylko godziny rozpoczęcia. Jeśli **czas zakończenia** jest wyłączona, wybierz **zdarzenia zatrzymania Dodaj** obok listy rozwijanej, aby go włączyć.
6. Aby uzyskać **strefy czasowej (wymagane)** , wybierz strefę czasową uruchomienia i zatrzymania razy określono. 
7. Aby uzyskać **uwagi**, wprowadź opis lub notatki dla harmonogramu. 
8. Wybierz pozycję **Zapisz**. 

    ![Jednorazowa harmonogramu](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Dodaj powtarzającego się harmonogramu (co tydzień)

1. Przełącz się do **harmonogramy** strony i wybierz **harmonogramu Dodaj** na pasku narzędzi. 

    ![Dodaj przycisk harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/add-schedule-button.png)
2. Na **harmonogramu Dodaj** strony, przełącz się do **tygodniowy** u góry. 
3. Aby uzyskać **zaplanować dni (wymagane)** , wybierz dni, w których ma harmonogram, aby zastosować zmiany. W poniższym przykładzie wybrano od poniedziałku do piątku. 
4. Dla **z** wprowadź **zaplanować Data rozpoczęcia** lub wybierz datę, wybierając **kalendarza** przycisku. To pole jest wymagane. 
5. Aby uzyskać **Data zakończenia harmonogramu**wprowadź lub wybierz datę zakończenia, w którym maszyny wirtualne mają być zamknięty. 
6. Aby uzyskać **czas rozpoczęcia**, wybierz godzinę maszyn wirtualnych ma zostać uruchomiony. Czas rozpoczęcia jest wymagany, jeśli nie ustawiono czas przestoju. Wybierz **Usuń zdarzenie początkowe** Jeśli chcesz określić tylko godzina zatrzymania. Jeśli **czas rozpoczęcia** jest wyłączona, wybierz **Dodaj zdarzenie rozpoczęcia** obok listy rozwijanej, aby go włączyć. 
7. Aby uzyskać **czas zakończenia**, wybierz godzinę maszyn wirtualnych można zamknąć. Czas przestoju jest wymagany, jeśli nie ustawiono czasu rozpoczęcia. Wybierz **zdarzenie stop usuwania** Jeśli chcesz określić tylko godziny rozpoczęcia. Jeśli **czas zakończenia** jest wyłączona, wybierz **zdarzenia zatrzymania Dodaj** obok listy rozwijanej, aby go włączyć.
8. Aby uzyskać **strefy czasowej (wymagane)** , wybierz strefę czasową uruchomienia i zatrzymania razy określono.  
9. Aby uzyskać **uwagi**, wprowadź opis lub notatki dla harmonogramu. 
10. Wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Wyświetl harmonogramy w kalendarzu
Możesz zobaczyć zaplanowane daty i godziny, wyróżnione w widoku kalendarza, jak pokazano na poniższej ilustracji:

![Harmonogramy w widoku kalendarza](../media/how-to-create-schedules/schedules-in-calendar.png)

Wybierz **już dziś** przycisk w prawym górnym rogu, aby przełączyć się do bieżącej daty w kalendarzu. Wybierz **Strzałka w lewo** Aby przełączyć się do poprzedniego tygodnia i **strzałkę w prawo** Aby przełączyć się do następnego tygodnia w kalendarzu. 

## <a name="edit-a-schedule"></a>Edytuj harmonogram
Po dwukrotnym kliknięciu zgodnie z harmonogramem wyróżnione w kalendarzu lub wybierz pozycję **ołówka** przycisk na pasku narzędzi, zostanie wyświetlony **Edycja harmonogramu** strony. Trwa aktualizowanie ustawień na tej stronie jest taka sama jak aktualizowanie ustawień w **harmonogramu Dodaj** stronie zgodnie z opisem w [Dodaj Harmonogram cykliczny](#add-a-recurring-schedule-weekly) sekcji. 

![Edytuj stronę harmonogramu](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

1. Aby usunąć harmonogram, wybierz Kosz może (Usuń) przycisk na pasku narzędzi, jak pokazano na poniższej ilustracji:

    ![Usuwanie przycisku na pasku narzędzi](../media/how-to-create-schedules/delete-schedule-button.png)

    Użyj przycisku usuwania jakichkolwiek zaplanowanej daty i godziny w kalendarzu i wybierz **Usuń**. 
2. Na **usuwanie harmonogramów** wybierz opcję **tak**.

    ![Harmonogramy potwierdzenie usunięcia](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)
