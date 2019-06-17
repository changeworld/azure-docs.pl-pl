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
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123176"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Ustawianie lub resetowanie haseł dla maszyn wirtualnych w laboratorium na potrzeby zajęć
Ten artykuł zawiera różne sposoby ustawienia i resettings haseł do uzyskania dostępu do maszyn wirtualnych w laboratorium na potrzeby zajęć. 

## <a name="lab-owners-teachers"></a>Właściciele laboratorium (nauczycieli)
Właściciel laboratorium (nauczyciela) można ustawić hasło dla maszyn wirtualnych w laboratorium na **Ustaw poświadczenia** strona kreatora tworzenia laboratorium.

![Ustawianie poświadczeń](../media/tutorial-setup-classroom-lab/set-credentials.png)

Następnie właściciel laboratorium może zresetować hasło (jeśli jest to konieczne) na **Konfigurowanie szablonu** strona kreatora tworzenia laboratorium. 

![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Po utworzeniu laboratorium, na pulpicie nawigacyjnym, właściciel laboratorium mogą również resetować hasła. 

![Resetowanie hasła menu na stronie głównej](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

Następnie wprowadź nowe hasło w **Ustaw hasło** strony i wybierz **Ustaw hasło**. 

![Resetowanie hasła menu na stronie głównej](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Użytkownicy laboratorium (uczniowie i studenci)
W czasie tworzenia laboratorium. W tym samym czasie właściciel laboratorium można włączyć lub wyłączyć umożliwiająca uczniom ustawiają swoje hasła dla maszyn wirtualnych. Jeśli właściciel laboratorium (nauczyciela), włączenie tej opcji, użytkownik laboratorium (dla uczniów) będzie miał możliwość ustawić hasło dla maszyny Wirtualnej, gdy Uczeń loguje się do maszyny Wirtualnej po raz pierwszy. Na **Windows VM**, naciśnij klawisz **CTRL + ALT + END**i wybierz **Zmień hasło**. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o innych opcjach użycia uczniów możesz (jako właściciel laboratorium) można skonfigurować, zobacz następujący artykuł: [Konfigurowanie użycia uczniów](how-to-configure-student-usage.md).
