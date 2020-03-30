---
title: Dodawanie użytkownika jako twórcy laboratorium w usługach Azure Lab Services
description: W tym artykule pokazano, jak dodać użytkownika do roli Twórca laboratorium dla konta laboratorium w usłudze Azure Lab Services. Twórcy laboratorium mogą tworzyć laboratoria na tym koncie laboratorium.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444773"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Dodawanie twórców laboratorium do konta laboratorium w usłudze Azure Lab Services
W tym artykule pokazano, jak dodać użytkowników jako twórców laboratorium do konta laboratorium w usłudze Azure Lab Services. Te zastosowania następnie można utworzyć laboratoria w klasie na koncie laboratorium. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Dodawanie konta użytkownika Microsoft do roli Twórca laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** i kliknij pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Jeśli dodajesz użytkownika konta innych niż Microsoft jako twórcę laboratorium, zobacz [sekcję Dodawanie użytkownika konta innych niż Microsoft jako twórca laboratorium.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Dodawanie użytkownika konta innych firm jako twórcy laboratorium
Aby dodać użytkownika jako twórcę laboratorium, użyj jego kont e-mail. Można użyć następujących typów kont e-mail:

- Konto e-mail udostępniane przez usługę Office 365 Azure Active Directory (AAD) dla twojej uczelni. 
- Konto e-mail Firmy `@outlook.com` `@hotmail.com`Microsoft, takie jak , , `@msn.com`lub `@live.com`.
- Konto e-mail inne niż Microsoft, takie jak konto dostarczone przez Yahoo lub Google. Jednak te typy kont muszą być połączone z kontem Microsoft.
- Konto usługi GitHub. To konto musi być połączone z kontem Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Korzystanie z konta e-mail firmy innych niż Microsoft
Twórcy/instruktorzy laboratorium mogą używać kont e-mail innych firm do rejestrowania się i logowania w laboratorium w klasie.  Jednak logowanie do portalu usług Lab Services wymaga, aby instruktorzy najpierw utworzyli konto Microsoft, które jest połączone z ich adresem e-mail firmy innych niż Microsoft.

Wielu instruktorów może mieć już konto Microsoft połączone z ich adresami e-mail innych firm. Na przykład instruktorzy mają już konto Microsoft, jeśli używali swojego adresu e-mail z innymi produktami lub usługami firmy Microsoft, takimi jak Office, Skype, OneDrive lub Windows.  

Gdy instruktorzy logują się do portalu Usług laboratoryjnych, są monitowani o podanie adresu e-mail i hasła. Jeśli instruktor próbuje zalogować się za pomocą konta innych niż Microsoft, które nie ma połączonego konta Microsoft, instruktor otrzyma następujący komunikat o błędzie: 

![Komunikat o błędzie](../media/how-to-configure-student-usage/cant-find-account.png)

Aby założyć konto Microsoft, instruktorzy powinni [http://signup.live.com](http://signup.live.com)przejść do pliku .  


### <a name="using-a-github-account"></a>Korzystanie z konta GitHub
Instruktorzy mogą również korzystać z istniejącego konta Usługi GitHub, aby zarejestrować się i zalogować się do laboratorium w klasie. Jeśli instruktor ma już konto Microsoft połączone z kontem GitHub, może zalogować się i podać swoje hasło, jak pokazano w poprzedniej sekcji. Jeśli nie połączyli jeszcze swojego konta GitHub z kontem Microsoft, powinni wybrać **opcje logowania:**

![Łącze opcji logowania](../media/how-to-configure-student-usage/signin-options.png)

Na stronie **Opcje logowania** wybierz pozycję Zaloguj się za **pomocą githuuba**.

![Logowanie się za pomocą łącza GitHub](../media/how-to-configure-student-usage/signin-github.png)

Na koniec są monitowani o utworzenie konta Microsoft, które jest połączone z ich kontem GitHub. Dzieje się to automatycznie, gdy instruktor wybierze **przycisk Dalej**.  Instruktor jest następnie natychmiast zalogowany i podłączony do laboratorium w klasie.


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, uzyskaj dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)
