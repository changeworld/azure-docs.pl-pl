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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 30c033b487fe58d017080b02c257502f82338164
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710044"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurowanie zasad i ustawień użycia
W tym artykule opisano jak skonfigurować liczbę użytkowników na potrzeby laboratorium, Uzyskaj je zarejestrowane w usłudze laboratorium, kontrolować liczbę godzin, które mogą używać maszyny Wirtualnej i nie tylko. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Określ liczbę użytkowników uprawnionych do laboratorium

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-students"></a>Wysyłanie linków rejestracyjnych do uczniów

1. Przełącz się do **użytkowników** widoku, wybierając **użytkowników** w menu po lewej stronie. 
2. Wybierz **pobrania rejestracji linku** kafelka.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Zamknij**. 
4. Udostępnij link rejestracji uczniowi, aby mógł się zarejestrować w klasie. Jeśli masz **ograniczenia opcji** ustawienie włączone i ma listę użytkowników, na liście, wykonaj następujące czynności:
    1. Wybierz **adres e-mail** użytkownika na liście. 
    2. Wyświetlone okno z domyślnego programu poczty e-mail przy użyciu **na** adres wypełnione. 
    3. Wklej **adres URL rejestracji** wcześniej zostały skopiowane. 
    4. Wyślij **e-mail**. 

## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

Wybierz **użytkowników** w menu po lewej stronie, aby zapoznać się z listą użytkowników zarejestrowane w usłudze laboratorium. 

![Lista użytkowników zarejestrowane w usłudze laboratorium](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Ustawianie przydziałów dla poszczególnych użytkowników

1. Wybierz **użytkowników** w menu po lewej stronie.
2. Wybierz **limitu przydziału dla poszczególnych użytkowników: bez ograniczeń** na pasku narzędzi. 
3. Na **limitu przydziału dla poszczególnych użytkowników** wybierz opcję **ograniczyć liczbę godzin, które użytkownik może używać maszyny Wirtualnej**. 
4. Dla **ile godzin czy chcesz dać każdemu użytkownikowi**, wprowadź liczbę godzin, a wybierz **Zapisz**. 

    ![Liczba godzin na użytkownika](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Na pasku narzędzi, zobacz teraz liczbę godzin: **limitu przydziału dla poszczególnych użytkowników: &lt;liczby godzin&gt;**. 

    ![Limit przydziału dla poszczególnych użytkowników](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium
Jeśli masz **ograniczać** włączone, dodawanie użytkowników (adresy e-mail) do listy.

1. Wybierz **użytkowników** w menu po lewej stronie.
2. Wybierz **dodawania użytkowników** na pasku narzędzi. 
3. Na **dodawania użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu rozdzielone średnikami. 

    ![Dodaj adresy e-mail użytkowników](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Zostanie wyświetlony adresy e-mail użytkowników i ich Stany (zarejestrowane lub nie) na liście. 

    ![Lista użytkowników](../media/how-to-configure-student-usage/users-list-new.png)

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


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [Jako administrator tworzenie i zarządzanie kontami laboratorium](how-to-manage-lab-accounts.md)
- [Jako właściciel laboratorium tworzenie i zarządzanie laboratoriami](how-to-manage-classroom-labs.md)
- [Jako właściciel laboratorium Konfigurowanie i publikowanie szablonów](how-to-create-manage-template.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)
