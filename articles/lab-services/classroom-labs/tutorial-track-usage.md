---
title: Śledzenie użycia laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: W tym samouczku omówiono śledzenie użycia laboratorium przez twórcę/właściciela laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 710d157dcf4c6d060e59bcfbb69455e2ddc91bdd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450134"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Samouczek: śledzenie użycia laboratorium w usłudze Azure Lab Service
W tym samouczku zostanie pokazane, w jaki sposób twórca/właściciel laboratorium może śledzić użycie laboratorium.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Wyświetlanie użytkowników zarejestrowanych w Twoim laboratorium
> * Wyświetlanie użycia maszyn wirtualnych w laboratorium
> * Zarządzanie maszynami wirtualnymi uczniów 


## <a name="view-users-registered-with-the-lab"></a>Wyświetlenie użytkowników zarejestrowanych w laboratorium

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft.
3. Na stronie **My labs** (Moje laboratoria) wybierz laboratorium, którego użycie chcesz śledzić. 
4. Wybierz kartę **Users** (Użytkownicy). Zostanie wyświetlona lista uczniów, którzy zarejestrowali się w Twoim laboratorium. Wybierz pozycję **Registration link** (Link rejestracji), skopiuj link i wyślij go do każdego nowego studenta, który jeszcze nie zarejestrował się w Twoim laboratorium. 

    ![Zarejestrowani użytkownicy](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Wyświetlanie użycia maszyn wirtualnych w laboratorium 

1. Wybierz pozycję **Virtual Machines** (Maszyny wirtualne) w menu po lewej stronie. 
2. Upewnij się, że jest wyświetlany stan maszyn wirtualnych i liczba godzin, podczas których maszyny wirtualne były uruchomione. Czas spędzony na maszynie wirtualnej ucznia nie jest wliczany do czasu użycia widocznego w ostatniej kolumnie. 

    ![Użycie maszyny wirtualnej](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Zarządzanie maszynami wirtualnymi uczniów 
Po umieszczeniu wskaźnika myszy nad wierszem na liście maszyn wirtualnych zostaną wyświetlone funkcje sterowania umożliwiające wykonywanie następujących zadań: 

- Łączenie z maszyną wirtualną
- Uruchamianie maszyny wirtualnej
- Zatrzymywanie maszyny wirtualnej
- Usuwanie maszyny wirtualnej

![Funkcje sterowania maszyny wirtualnej](../media/tutorial-track-usage/vm-controls.png) 



## <a name="next-steps"></a>Następne kroki
W tym samouczku pokazano, w jaki sposób odnaleźć użytkowników zarejestrowanych w laboratorium, śledzić użycie maszyn wirtualnych w laboratorium i zarządzać maszynami wirtualnymi w laboratorium.

Aby dowiedzieć się więcej na temat laboratoriów na potrzeby zajęć, zobacz tematy w sekcji [Przewodniki z instrukcjami](how-to-manage-lab-accounts.md).
