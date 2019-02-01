---
title: Konfigurowanie ustawień użycia w laboratorium na potrzeby zajęć z usługi Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować liczbę użytkowników na potrzeby laboratorium, Uzyskaj je zarejestrowane w usłudze laboratorium, kontrolować liczbę godzin, które mogą używać maszyny Wirtualnej i nie tylko.
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 946a2a05cee0cf8f3b91eef58442fbb2e26935c4
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490451"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurowanie zasad i ustawień użycia
W tym artykule opisano, jak dodać użytkowników do laboratorium, Uzyskaj je zarejestrowane w usłudze laboratorium, kontrolować liczbę godzin, które mogą używać maszyny Wirtualnej i nie tylko. 


## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium
Jeśli masz **ograniczać** włączone, dodawanie użytkowników (adresy e-mail) do listy.

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**.
2. Na pasku narzędzi wybierz pozycję **Dodaj użytkowników**. 
3. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>Wysyłanie linków rejestracyjnych do uczniów
Poniższa procedura zawiera kroki, aby wysłać link rejestracji dla użytkowników. Jeśli **ograniczać** jest włączona w środowisku laboratoryjnym, tylko użytkownicy na liście Użytkownicy mogą używać łącze do zarejestrowania do laboratorium. 

1. Przełącz się do **użytkowników** widoku, wybierając **użytkowników** w menu po lewej stronie. 
2. Wybierz kafelek **Pobierz link rejestracji**.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Zamknij**. 
4. Udostępnij link rejestracji uczniowi, aby mógł się zarejestrować w klasie. Jeśli ustawienie **Ogranicz dostęp** jest włączone i masz listę użytkowników, wykonaj następujące czynności:
    1. Wybierz **adres e-mail** użytkownika na liście. 
    2. Zostanie wyświetlone okno domyślnego programu poczty e-mail z wypełnionym polem adresu **DO**. 
    3. Wklej skopiowany wcześniej **adres URL rejestracji**. 
    4. Wyślij wiadomość **e-mail**. 

## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

Wybierz **użytkowników** w menu po lewej stronie, aby zapoznać się z listą użytkowników zarejestrowane w usłudze laboratorium. 

![Lista użytkowników zarejestrowane w usłudze laboratorium](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Ustawianie przydziałów dla poszczególnych użytkowników
Można ustawić limity przydziału dla poszczególnych użytkowników wykonując następujące kroki: 

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**.
2. Wybierz **limitu przydziału dla poszczególnych użytkowników: bez ograniczeń** na pasku narzędzi. 
3. Na **limitu przydziału dla poszczególnych użytkowników** wybierz opcję **ograniczyć liczbę godzin, które użytkownik może używać maszyny Wirtualnej**. 
4. Dla **ile godzin czy chcesz dać każdemu użytkownikowi**, wprowadź liczbę godzin, a wybierz **Zapisz**. 

    ![Liczba godzin na użytkownika](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Teraz wyświetlić liczbę godzin na pasku narzędzi: **Limit przydziału dla poszczególnych użytkowników: &lt;liczby godzin&gt;**. 

    ![Limit przydziału dla poszczególnych użytkowników](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> [Zaplanowany czas maszyny wirtualne działania](how-to-create-schedules.md) nie uwzględniane w limicie przydziału przydzielony do użytkownika. Limit przydziału jest czas poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

### <a name="add-users-by-uploading-a-csv-file"></a>Aby dodać użytkowników, przekazywanie pliku CSV
Możesz również dodać użytkowników, przekazując plik CSV z adresów e-mail użytkowników.

1. Wybierz **przekazywanie CSV** na pasku narzędzi.
2. Wybierz plik CSV zawierający adresy e-mail użytkowników. Wszystkie adresy e-mail powinna mieć jedną kolumnę, po otwarciu go przy użyciu programu Excel. 

## <a name="manage-user-vms"></a>Zarządzanie maszynami wirtualnymi użytkownika
Gdy uczniowie rejestru za pomocą usługi Azure Lab Services przy użyciu rejestracji link, pod warunkiem, zostanie wyświetlony przypisane maszyny wirtualne dla uczniów i studentów w **maszyn wirtualnych** kartę. 

![Maszyny wirtualne przypisane dla uczniów i studentów](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Możesz wykonać następujące zadania na ucznia maszyny Wirtualnej: 

- Jeśli maszyna wirtualna jest uruchomiona, Zatrzymaj Maszynę wirtualną. 
- Uruchom Maszynę wirtualną, jeśli maszyna wirtualna jest zatrzymana. 
- Nawiąż połączenie z maszyną wirtualną. 
- Usuń maszynę Wirtualną. 
- Wyświetl liczbę godzin, aby użytkownicy używali maszyny wirtualnej. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aktualizowanie liczby maszyn wirtualnych w laboratorium
Aby zaktualizować liczbę maszyn wirtualnych w środowisku laboratoryjnym, wykonaj następujące kroki **maszyn wirtualnych** strony:

1. Wybierz **maszyn wirtualnych** w menu po lewej stronie. 
2. Wybierz **pojemności laboratorium: &lt;numer&gt; następującą liczbę maszyn:** na pasku narzędzi. 
3. Wprowadź **numer** maszyn wirtualnych.
4. Wybierz pozycję **Zapisz**.

    ![Maszyny wirtualne w laboratorium](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)
