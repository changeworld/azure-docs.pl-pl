---
title: Dodaj instrukcję wewnętrznej pomocy technicznej do laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak publikować instrukcji wewnętrznej pomocy technicznej do laboratorium Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: deb98c2c633200ab4be1d763a94fd2a04979a3b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562342"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Dodaj instrukcję wewnętrznej pomocy technicznej do laboratorium Azure DevTest Labs

Usługa Azure DevTest Labs pozwala dostosować swoje laboratorium za pomocą instrukcji wewnętrznej pomocy technicznej, która dostarcza użytkownikom informacje techniczne dotyczące laboratorium. Na przykład można określić informacje kontaktowe, co użytkownik wie, jak nawiązać wewnętrznej pomocy technicznej, gdy ich potrzebujesz pomocy przy rozwiązywaniu problemów lub uzyskiwania dostępu do zasobów w środowisku laboratoryjnym. Można również zawierają linki do wewnętrznych witryn sieci Web lub często zadawanych pytań, które Twój zespół ma dostęp, skontaktuj się z pomocą techniczną.

Instrukcja wewnętrznej pomocy technicznej jest przeznaczona do umożliwiają ogłaszanie informacji laboratoryjnych, które zazwyczaj nie zmienia się zbyt często. Aby powiadomić użytkowników o informacje laboratorium, który jest bardziej miał charakter przejściowy — takich jak najnowsze aktualizacje zasad laboratorium — zobacz [publikowanie anonsu w laboratorium](devtest-lab-announcements.md).

Łatwo można wyłączyć lub edytować oświadczenie pomocy technicznej, po jej nie ma już zastosowania.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Kroki pozwalające dodawać oświadczenie pomocy technicznej do istniejącego laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby zaznacz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy. (Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Zaznacz na liście laboratoriów laboratorium, w której chcesz dodać oświadczenie pomocy technicznej.  
1. W laboratorium **Przegląd** wybierz opcję **konfiguracji i zasad**.  

    ![Przycisk konfiguracji i zasad](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Po lewej stronie w obszarze **ustawienia**, wybierz opcję **wewnętrznej pomocy technicznej**.

    ![Przycisk wewnętrznej pomocy technicznej](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Aby utworzyć wiadomość wewnętrznej pomocy technicznej dla użytkowników, w tym środowisku laboratoryjnym, ustaw włączone **tak**.

1. W **komunikat o pomocy technicznej** pole, wprowadź informacje o prawach wewnętrznej pomocy technicznej, którą chcesz przedstawić użytkownikom laboratorium. Komunikat o pomocy technicznej akceptuje języka znaczników Markdown. Podczas wprowadzania tekstu wiadomości można wyświetlić **Podgląd** obszar w dolnej części ekranu, aby zobaczyć, jak pojawi się komunikat do użytkowników.

    ![Ekran wewnętrznej pomocy technicznej do utworzenia wiadomości.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Wybierz **Zapisz** po instrukcji obsługi jest gotowy do opublikowania.

Jeśli nie chcesz już nie pokazuj tego komunikatu pomocy technicznej do laboratorium użytkowników, wróć do **wewnętrznej pomocy technicznej** strony, a następnie ustaw **włączone** do **nie**.

## <a name="steps-for-users-to-view-the-support-message"></a>Kroki dla użytkowników wyświetlić komunikat o pomocy technicznej

1. Z [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), wybierz laboratorium.

1. W laboratorium **Przegląd** wybierz opcję **wewnętrznej pomocy technicznej**.  

    ![Przycisk wewnętrznej pomocy technicznej](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Jeśli wiadomość pomocy technicznej zostanie opublikowana, użytkownik może przeglądać je z poziomu okienka wewnętrznej pomocy technicznej.

    ![Okienko obsługi wiadomości opublikowanej w wewnętrznej pomocy technicznej](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
* Oświadczenia dotyczące pomocy technicznej wewnętrzne są zazwyczaj używane do informacje pomocy technicznej, które nie zmieniają się, często. Możesz też dowiedzieć się, jak [publikowanie anonsu do laboratorium](devtest-lab-announcements.md) aby informować użytkowników o tymczasowej zmiany lub aktualizacji do laboratorium.
* [Ustawianie zasad i harmonogramów](devtest-lab-set-lab-policy.md) zawiera informacje dotyczące sposobu można zastosować inne ograniczenia i konwencje w ramach subskrypcji za pomocą zasad niestandardowych.