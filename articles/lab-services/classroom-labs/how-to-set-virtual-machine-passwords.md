---
title: Ustawianie haseł dla maszyn wirtualnych w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak ustawiać i resetować hasła dla maszyn wirtualnych w laboratorium z klasą Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645026"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>Ustawianie lub Resetowanie hasła dla maszyn wirtualnych w laboratoriach zajęć (instruktorzy)
Właściciel laboratorium (nauczyciel) może ustawić/zresetować hasło dla maszyn wirtualnych w momencie tworzenia laboratorium (Kreator tworzenia laboratorium) lub po utworzeniu laboratorium (na pulpicie nawigacyjnym). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Ustaw hasło podczas tworzenia laboratorium
Właściciel laboratorium (nauczyciel) może ustawić hasło dla maszyn wirtualnych w laboratorium na stronie **Ustawianie poświadczeń** w Kreatorze tworzenia laboratorium.

![Ustaw poświadczenia](../media/tutorial-setup-classroom-lab/set-credentials.png)

Przez włączenie/wyłączenie opcji **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** na tej stronie nauczyciel może wybrać użycie tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawianie haseł dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemu operacyjnego Windows i Linux z wyjątkiem Ubuntu. Gdy to ustawienie jest wyłączone, uczniowie będą monitowani o ustawienie hasła podczas próby nawiązania połączenia z maszyną wirtualną po raz pierwszy. 

Właściciel laboratorium może zresetować to hasło (jeśli jest to możliwe) na stronie **Konfigurowanie szablonu** Kreatora tworzenia laboratorium. 

![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Właściciel laboratorium może również zresetować hasło po utworzeniu laboratorium na pulpicie nawigacyjnym. 

## <a name="reset-password-on-the-dashboard"></a>Resetowanie hasła na pulpicie nawigacyjnym

1. Wybierz menu przepełnienie (trzy kropki w pionie) na kafelku Lab i wybierz polecenie **Resetuj hasło**. 

    ![Menu resetowania hasła na stronie głównej](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. W oknie dialogowym **Ustawianie hasła** wprowadź hasło, a następnie wybierz pozycję **Ustaw hasło**.
    
    ![Okno dialogowe Ustawianie hasła](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o opcjach użycia ucznia (jako właściciel laboratorium), zobacz następujący artykuł: [Skonfiguruj użycie ucznia](how-to-configure-student-usage.md).

Aby dowiedzieć się, w jaki sposób uczniowie mogą resetować hasła dla swoich maszyn wirtualnych, zobacz [Ustawianie lub Resetowanie hasła dla maszyny wirtualnej w laboratoriach z zajęć (students)](how-to-set-virtual-machine-passwords-student.md).