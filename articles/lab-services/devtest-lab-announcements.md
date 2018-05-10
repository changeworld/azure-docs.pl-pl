---
title: Opublikuj announcment do laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać anonsu do laboratorium w usłudze Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3282a90069ecd119154df492ac6dc366d26b5300
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Opublikuj anonsu do laboratorium w usłudze Azure DevTest Labs

Jako administrator laboratorium może po anonsu niestandardowe w istniejących laboratorium w celu powiadomienia użytkowników o najnowszych zmian lub ma zostać dodany do laboratorium. Na przykład możesz chcieć poinformować użytkowników o:

- Nowe rozmiary maszyn wirtualnych, które są dostępne
- Obrazy, które są obecnie korzystanie z niej
- Aktualizacje zasad laboratorium

Po opublikowaniu anonsu jest wyświetlany na stronie Przegląd laboratorium, a użytkownik może wybrać go więcej szczegółów.

Ta funkcja anonsu jest przeznaczona do użycia dla tymczasowych powiadomienia.  Można łatwo wyłączyć powiadomienia po nie jest już potrzebne.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Kroki publikowania powiadomienia w istniejących laboratorium

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy. (Laboratorium może być jest wyświetlana na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Z listy labs wybierz laboratorium, w którym chcesz post anonsu.  
1. W laboratorium **omówienie** wybierz opcję **konfiguracji i zasadach**.  

    ![Przycisk zasad i konfiguracji](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **ustawienia**, wybierz pozycję **anonsu laboratorium**.

    ![Przycisk anonsu laboratorium](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Aby utworzyć wiadomości dla użytkowników w tym laboratorium, ustaw **włączone** do **tak**.

1. Możesz wprowadzić **Data wygaśnięcia** do określenia daty i czasu, po którym anons nie jest już wyświetlana dla użytkowników. Jeśli nie wprowadzisz datę wygaśnięcia, anons pozostaje dopóki nie zostanie wyłączona.

   > [!NOTE]
   > Po wygaśnięciu anonsu, jest już wyświetlana dla użytkowników, lecz nadal istnieje w **anonsu laboratorium** okienka. Możesz dokonać edycji i ją włączyć ponownie, aby ją uaktywnić ponownie.
   >
   >

1. Wprowadź **Tytuł anonsu** i **tekst anonsu**.

   Tytuł może być maksymalnie 100 znaków i jest wyświetlany na stronie Przegląd laboratorium. Jeśli użytkownik wybierze tytuł, zostanie wyświetlony tekst anonsu.

   Tekst anonsu akceptuje markdown. Podczas wprowadzania tekstu anonsu komunikat można wyświetlić w obszarze podglądu w dolnej części ekranu.

    ![Ekran anonsu laboratorium do tworzenia komunikatu.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Wybierz **zapisać** po anonsu jest gotowy do publikowania.

Gdy nie chcesz pokazać to zawiadomienie użytkownikom laboratorium, wróć do **anonsu laboratorium** strony i ustaw **włączone** do **nr**. Jeśli określono datę wygaśnięcia, anons jest automatycznie wyłączana podczas który daty i godziny.

## <a name="steps-for-users-to-view-an-announcement"></a>Kroki użytkownikom na wyświetlanie anonsu

1. Z [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), wybierz laboratorium.

1. Jeśli laboratorium ma zaksięgowany dla tego powiadomienia, ogłoszenie jest wyświetlany w górnej części strony Przegląd laboratorium. Tego komunikatu informacji jest tytuł powiadomienia, który został określony podczas tworzenia anonsu.

    ![Zawiadomienie laboratorium na stronie Przegląd](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Użytkownik może wybrać komunikat, aby wyświetlić całą anonsu.

    ![Więcej informacji na ogłoszenie laboratorium](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
* W przypadku zmiany lub ustawianie zasad laboratorium, możesz chcieć post anonsu, aby informować użytkowników. [Ustaw zasady i harmonogramy](devtest-lab-set-lab-policy.md) informacje na temat stosowania ograniczeń i konwencje między subskrypcją za pomocą niestandardowych zasad.
* Eksploruj [galerię szablonów DevTest Labs Azure Resource Manager — Szybki Start](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
