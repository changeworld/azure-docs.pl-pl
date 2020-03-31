---
title: Dodawanie wewnętrznej instrukcji pomocy technicznej do laboratorium w laboratorium w usłudze Azure DevTest Labs
description: Dowiedz się, jak opublikować wewnętrzną instrukcję pomocy technicznej w laboratorium w laboratorium usługi Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170352"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Dodawanie wewnętrznej instrukcji pomocy technicznej do laboratorium w laboratorium w usłudze Azure DevTest Labs

Usługa Azure DevTest Labs umożliwia dostosowanie laboratorium za pomocą wewnętrznej instrukcji pomocy technicznej, która zapewnia użytkownikom informacje o pomocy technicznej dotyczące laboratorium. Na przykład można podać informacje kontaktowe, dzięki czemu użytkownik wie, jak uzyskać dostęp do wewnętrznej pomocy technicznej, gdy potrzebują pomocy w rozwiązywaniu problemów lub uzyskiwaniu dostępu do zasobów w laboratorium. Możesz również podać łącza do wewnętrznych witryn sieci Web lub często zadawanych pytań, do których twój zespół może uzyskać dostęp przed skontaktowaniem się z pomocą techniczną.

Wewnętrzna instrukcja pomocy technicznej ma na celu umożliwienie publikowania informacji o laboratorium, które zazwyczaj nie zmieniają się zbyt często. Aby powiadomić użytkowników o informacjach o laboratorium, które mają bardziej tymczasowy charakter – takich jak ostatnie aktualizacje zasad laboratoryjnych — zobacz [Ogłaszanie w laboratorium.](devtest-lab-announcements.md)

Możesz łatwo wyłączyć lub edytować instrukcję pomocy technicznej po jej nie ma już zastosowania.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Kroki, aby dodać instrukcję pomocy technicznej do istniejącego laboratorium

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.** (Twoje laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **Wszystkie zasoby).**
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz dodać instrukcję pomocy technicznej.  
1. W obszarze **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.  

    ![Przycisk Konfiguracja i zasady](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **USTAWIENIA**wybierz pozycję **Wsparcie wewnętrzne**.

    ![Przycisk wewnętrznej obsługi](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Aby utworzyć wewnętrzny komunikat pomocy technicznej dla użytkowników w tym laboratorium, ustaw enabled to **Yes**.

1. W polu **Komunikat pomocy technicznej** wprowadź wewnętrzną instrukcję pomocy technicznej, którą chcesz przedstawić użytkownikom laboratorium. Komunikat pomocy technicznej akceptuje Markdown. Po wprowadzeniu tekstu wiadomości można wyświetlić obszar **Podgląd** u dołu ekranu, aby zobaczyć, jak wiadomość jest wyświetlana użytkownikom.

    ![Wewnętrzny ekran pomocy technicznej, aby utworzyć komunikat.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Wybierz **pozycję Zapisz,** gdy wyciąg z pomocy technicznej będzie gotowy do opublikowania.

Jeśli nie chcesz już pokazywać tego komunikatu pomocy technicznej użytkownikom laboratorium, wróć do strony **Pomocy wewnętrznej** i ustaw **włączono** **pozycję Nie**.

## <a name="steps-for-users-to-view-the-support-message"></a>Kroki dla użytkowników, aby wyświetlić komunikat pomocy technicznej

1. Z [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)wybierz laboratorium.

1. W obszarze **Przegląd** laboratorium wybierz pozycję **Wsparcie wewnętrzne**.  

    ![Przycisk wewnętrznej obsługi](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Jeśli zostanie opublikowany komunikat pomocy technicznej, użytkownik może wyświetlić go w wewnętrznym okienku pomocy technicznej.

    ![Wewnętrzne okienko pomocy technicznej z opublikowaną wiadomością pomocy technicznej](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
* Wewnętrzne instrukcje pomocy technicznej są zazwyczaj używane do dostarczania informacji o pomocy technicznej, które nie zmieniają się tak często. Można również dowiedzieć się, jak [opublikować anons w laboratorium,](devtest-lab-announcements.md) aby poinformować użytkowników o tymczasowych zmianach lub aktualizacjach w laboratorium.
* [Ustaw zasady i harmonogramy](devtest-lab-set-lab-policy.md) zawiera informacje o tym, jak można stosować inne ograniczenia i konwencje w całej subskrypcji przy użyciu niestandardowych zasad.