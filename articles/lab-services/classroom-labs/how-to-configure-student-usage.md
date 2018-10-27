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
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142411"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurowanie zasad i ustawień użycia
W tym artykule opisano jak skonfigurować liczbę użytkowników na potrzeby laboratorium, Uzyskaj je zarejestrowane w usłudze laboratorium, kontrolować liczbę godzin, które mogą używać maszyny Wirtualnej i nie tylko. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Określ liczbę użytkowników uprawnionych do laboratorium

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>Wyślij łącze rejestracji dla użytkowników

1. Przejdź do widoku **Pulpit nawigacyjny**. 
2. Wybierz kafelek **Rejestracja użytkownika**.

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. Studenci Użyj linku, aby przejść do strony, która pomoże mu rejestrację z laboratorium. 

    ![Link rejestracji ucznia](../media/tutorial-setup-classroom-lab/registration-link.png)
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Zamknij**. 

## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

Wybierz **użytkowników** w menu po lewej stronie, aby zapoznać się z listą użytkowników zarejestrowane w usłudze laboratorium. 

![Lista użytkowników zarejestrowane w usłudze laboratorium](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Ustawianie przydziałów dla poszczególnych użytkowników

1. Wybierz **ustawienia użytkownika** w menu po lewej stronie.
2. Wybierz **Quta na użytkownika** kafelka. 
3. Wybierz **ograniczyć liczbę godzin, które użytkownik może używać maszyny Wirtualnej**. 
4. Dla **ile godzin czy chcesz dać każdemu użytkownikowi?**, wprowadź liczbę godzin, a wybierz **Zapisz**. 

    ![Liczba godzin na użytkownika](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Zobacz liczbę godzin w **limitu przydziału dla poszczególnych użytkowników** kafelka teraz. 

    ![Limit przydziału dla poszczególnych użytkowników](../media/how-to-configure-student-usage/quota-per-user.png)

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
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md)
- [Konfigurowanie laboratorium](../tutorial-create-custom-lab.md)
