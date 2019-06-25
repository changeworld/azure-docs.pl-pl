---
title: Ustawianie haseł dla maszyn wirtualnych w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić i resetowanie haseł dla maszyn wirtualnych (VM) w laboratorium na potrzeby zajęć z usługi Azure Lab Services.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144100"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Ustawianie lub resetowanie haseł dla maszyn wirtualnych w laboratorium na potrzeby zajęć
Ten artykuł zawiera różne sposoby ustawienia i resettings haseł do uzyskania dostępu do maszyn wirtualnych w laboratorium na potrzeby zajęć. 

## <a name="lab-owners-teachers"></a>Właściciele laboratorium (nauczycieli)
Właściciel laboratorium (nauczyciela) można zestawu/Resetowanie hasła dla maszyn wirtualnych w czasie tworzenia laboratorium (Kreator tworzenia laboratorium) lub po utworzeniu laboratorium (na pulpicie nawigacyjnym). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Ustaw hasło w czasie tworzenia laboratorium
Właściciel laboratorium (nauczyciela) można ustawić hasło dla maszyn wirtualnych w laboratorium na **Ustaw poświadczenia** strona kreatora tworzenia laboratorium.

![Ustawianie poświadczeń](../media/tutorial-setup-classroom-lab/set-credentials.png)

Przez włączenie/wyłączenie **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** opcji na tej stronie nauczyciel można wybrać opcję Użyj tego samego hasła dla wszystkich maszyn wirtualnych w środowisku laboratoryjnym lub można ustawić hasła dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich Windows i Linux obrazy systemu operacyjnego, z wyjątkiem systemu Ubuntu. Gdy to ustawienie jest wyłączone, studentów zostanie wyświetlony monit ustawienia hasła, gdy użytkownik podejmie próbę nawiązania połączenia z maszyną wirtualną po raz pierwszy. 

Właściciel laboratorium zresetować to hasło (jeśli jest to konieczne) na **Konfigurowanie szablonu** strona kreatora tworzenia laboratorium. 

![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Po utworzeniu laboratorium, na pulpicie nawigacyjnym, właściciel laboratorium mogą również resetować hasła. 

### <a name="reset-password-on-the-dashboard"></a>Resetuj hasło na pulpicie nawigacyjnym

1. Wybierz menu Przepełnienie (w pionie trzy kropki) na kafelku laboratorium, a następnie wybierz pozycję **Resetuj hasło**. 

    ![Resetowanie hasła menu na stronie głównej](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Na **Ustaw hasło** okno dialogowe, wprowadź hasło, a następnie wybierz pozycję **Ustaw hasło**.
    
    ![Okno dialogowe Ustaw hasło](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Użytkownicy laboratorium (uczniowie i studenci)
W momencie tworzenia laboratorium właściciel laboratorium można włączać lub wyłączać **Użyj tego samego hasła dla wszystkich maszyn wirtualnych**. Jeśli ta opcja jest włączona, studentów, nie można zresetować hasła. Wszystkie maszyny wirtualne laboratoria będą mieć tego samego hasła, który jest uporządkowany według nauczyciel. 

Jeśli ta opcja jest wyłączona, użytkownicy muszą ustawić hasło podczas nawiązywania połączenia z maszyną wirtualną po raz pierwszy. Gdy użytkownicy (uczniowie i studenci) wybierz **Connect** znajdujący się na kafelku laboratorium na **Moje maszyny wirtualne** strony, użytkownik zobaczy następujące okno dialogowe, aby ustawić hasło dla maszyny Wirtualnej: 

![Resetowanie hasła dla uczniów lub studentów](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Student również wprowadzić hasło, klikając menu Przepełnienie (**pionowy wielokropek**) na kafelku laboratorium i wybierając polecenie **Resetuj hasło**. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o innych opcjach użycia uczniów możesz (jako właściciel laboratorium) można skonfigurować, zobacz następujący artykuł: [Konfigurowanie użycia uczniów](how-to-configure-student-usage.md).
