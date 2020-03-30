---
title: Resetowanie haseł dla maszyn wirtualnych z laboratorium w klasie w usłudze Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak resetować hasła do maszyn wirtualnych (VM) w laboratoriach szkolnych usługi Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583704"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Ustawianie lub resetowanie hasła maszyn wirtualnych w laboratoriach szkolnych (uczniowie)
W tym artykule pokazano, jak uczniowie mogą ustawiać/resetować hasło dla swoich maszyn wirtualnych. 

## <a name="enable-resetting-of-passwords"></a>Włączanie resetowania haseł
W momencie tworzenia laboratorium właściciel laboratorium może włączyć lub wyłączyć **użycie tego samego hasła dla wszystkich maszyn wirtualnych.** Jeśli ta opcja została włączona, uczniowie nie mogą zresetować hasła. Wszystkie maszyny wirtualne w laboratoriach będą miały to samo hasło, które jest ustawiane przez instruktora. 

Jeśli ta opcja jest wyłączona, użytkownicy będą musieli ustawić hasło podczas próby połączenia się z maszyną wirtualną po raz pierwszy. Uczniowie mogą również zresetować hasło później w dowolnym momencie, jak pokazano w ostatniej części tego artykułu. 

## <a name="reset-password-for-the-first-time"></a>Resetowanie hasła po raz pierwszy
Jeśli opcja **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** została wyłączona, gdy użytkownicy (studenci) wybierzą przycisk **Połącz** na kafelku laboratoryjnym na stronie **Moje maszyny wirtualne,** użytkownik zobaczy następujące okno dialogowe, aby ustawić hasło dla maszyny Wirtualnej: 

![Resetowanie hasła dla ucznia](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Późniejsze zresetowanie hasła
Student może również ustawić hasło, klikając menu przepełnienia **(pionowe trzy kropki)** na kafelku laboratorium i wybierając **reset hasło**. 

![Późniejsze zresetowanie hasła](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o innych opcjach użycia uczniów, które może skonfigurować właściciel laboratorium, zobacz następujący artykuł: [Konfigurowanie użycia uczniów](how-to-configure-student-usage.md).
