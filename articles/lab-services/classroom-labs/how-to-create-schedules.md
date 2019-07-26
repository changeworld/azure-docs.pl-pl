---
title: Tworzenie harmonogramu dla laboratoriów zajęć w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak tworzyć harmonogramy dla laboratoriów stacjonarnych w Azure Lab Services tak, aby maszyny wirtualne w laboratoriach były uruchamiane i zamykane w określonym czasie.
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
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385600"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Twórz harmonogramy dla laboratoriów stacjonarnych w Azure Lab Services i zarządzaj nimi 
Harmonogramy umożliwiają skonfigurowanie laboratorium klasy w taki sposób, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub cykliczny. Poniższe procedury umożliwiają tworzenie harmonogramów dla laboratorium zajęć i zarządzanie nimi: 

> [!IMPORTANT]
> Zaplanowany czas działania maszyn wirtualnych nie jest uwzględniany w stosunku do [przydziału przydzielonego dla użytkownika](how-to-configure-student-usage.md#set-quotas-for-users). Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="add-a-schedule-once"></a>Dodaj harmonogram (raz)

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj harmonogram** na pasku narzędzi. 

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stronie **Dodawanie harmonogramu** upewnij się, że w górnej części zaznaczone **jest pole wyboru** . Jeśli tak nie jest, wybierz **jeden raz**. 
3. W polu **Data planu (wymagane)** wprowadź datę lub wybierz ikonę kalendarza, aby wybrać datę. 
4. W polu **czas rozpoczęcia**wybierz godzinę rozpoczęcia uruchamiania maszyn wirtualnych. Czas rozpoczęcia jest wymagany, jeśli czas zatrzymania nie jest ustawiony. Wybierz pozycję **Usuń zdarzenie uruchomienia** , jeśli chcesz określić tylko czas zatrzymania. Jeśli **czas rozpoczęcia** jest wyłączony, wybierz pozycję **Dodaj zdarzenie uruchomienia** obok listy rozwijanej, aby ją włączyć. 
5. W polu **czas zatrzymania**wybierz czas zamykania maszyn wirtualnych. Czas zatrzymania jest wymagany, jeśli nie ustawiono czasu rozpoczęcia. Wybierz pozycję **Usuń zdarzenie zatrzymania** , jeśli chcesz określić tylko godzinę rozpoczęcia. Jeśli **czas zatrzymania** jest wyłączony, zaznacz pole wyboru **Dodaj zdarzenie zatrzymania** obok listy rozwijanej, aby je włączyć.
6. Dla **strefy czasowej (wymagane)** wybierz strefę czasową dla określonych godzin rozpoczęcia i zakończenia. 
7. W polu **uwagi**wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
8. Wybierz pozycję **Zapisz**. 

    ![Harmonogram jednorazowej](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Dodawanie harmonogramu cyklicznego (co tydzień)

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj harmonogram** na pasku narzędzi. 

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stronie **Dodawanie harmonogramu** przejdź na pierwszy **tydzień** . 
3. W polu **dni harmonogramu (wymagane)** wybierz dni, w których ma obowiązywać harmonogram. W poniższym przykładzie wybrano poniedziałek-piątek. 
4. W polu **od** wprowadź **datę rozpoczęcia harmonogramu** lub wybierz datę, wybierając przycisk **Kalendarz** . To pole jest wymagane. 
5. W polu **Data zakończenia harmonogramu**wprowadź lub wybierz datę końcową, w której mają zostać zamknięte maszyny wirtualne. 
6. W polu **czas rozpoczęcia**wybierz godzinę, o której mają zostać uruchomione maszyny wirtualne. Czas rozpoczęcia jest wymagany, jeśli czas zatrzymania nie jest ustawiony. Wybierz pozycję **Usuń zdarzenie uruchomienia** , jeśli chcesz określić tylko czas zatrzymania. Jeśli **czas rozpoczęcia** jest wyłączony, wybierz pozycję **Dodaj zdarzenie uruchomienia** obok listy rozwijanej, aby ją włączyć. 
7. W polu **czas zatrzymania**wybierz godzinę, o której maszyny wirtualne mają być zamykane. Czas zatrzymania jest wymagany, jeśli nie ustawiono czasu rozpoczęcia. Wybierz pozycję **Usuń zdarzenie zatrzymania** , jeśli chcesz określić tylko godzinę rozpoczęcia. Jeśli **czas zatrzymania** jest wyłączony, zaznacz pole wyboru **Dodaj zdarzenie zatrzymania** obok listy rozwijanej, aby je włączyć.
8. Dla **strefy czasowej (wymagane)** wybierz strefę czasową dla określonych godzin rozpoczęcia i zakończenia.  
9. W polu **uwagi**wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
10. Wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Wyświetl harmonogramy w kalendarzu
W widoku kalendarza można zobaczyć zaplanowane daty i godziny, jak pokazano na poniższej ilustracji:

![Harmonogramy w widoku kalendarza](../media/how-to-create-schedules/schedules-in-calendar.png)

Wybierz przycisk **dzisiaj** w prawym górnym rogu, aby przełączyć się na bieżącą datę w kalendarzu. Wybierz **strzałkę w lewo** , aby przełączyć się do poprzedniego tygodnia i **strzałki w prawo** , aby przejść do następnego tygodnia w kalendarzu. 

## <a name="edit-a-schedule"></a>Edytowanie harmonogramu
Po dwukrotnym kliknięciu wyróżnionego harmonogramu w kalendarzu lub wybraniu przycisku **ołówka** na pasku narzędzi zostanie wyświetlona strona **Edytowanie harmonogramu** . Aktualizowanie ustawień na tej stronie jest takie samo jak ustawienia aktualizacji na stronie **Dodawanie harmonogramu** , zgodnie z opisem w sekcji [Dodawanie harmonogramu cyklicznego](#add-a-recurring-schedule-weekly) . 

![Edytuj stronę harmonogramu](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

1. Aby usunąć harmonogram, wybierz przycisk kosza na śmieci (Usuń) na pasku narzędzi, jak pokazano na poniższej ilustracji:

    ![Przycisk Usuń na pasku narzędzi](../media/how-to-create-schedules/delete-schedule-button.png)

    Możesz użyć przycisku Usuń dla każdej zaplanowanej daty i godziny w kalendarzu, a następnie wybrać pozycję **Usuń**. 
2. Na stronie **usuwanie harmonogramów** wybierz pozycję **tak**.

    ![Potwierdzenie usunięcia harmonogramów](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)
