---
title: Dodaj informacje o ochronie prywatności w organizacji w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak dodać informacje o ochronie prywatności w organizacji do obszaru właściwości usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: 8cdf30ed09601a31529073eaedd4ab53780157d5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Porada: Dodawanie informacji o prywatności organizacji w usłudze Azure Active Directory
W tym artykule opisano, jak dodać informacje dotyczące ochrony prywatności organizacji dzierżawcy usługi Azure Active Directory (Azure AD), za pośrednictwem portalu Azure Administrator dzierżawy.

Stanowczo zaleca się, że dodać kontakt globalne prywatności i poufności w organizacji, tak pracowników wewnętrznych i zewnętrznych gości można sprawdzić zasady. Ponieważ zasady zachowania poufności jednoznacznie są tworzone i dostosowane do każdego biznesowych, zdecydowanie zaleca się się prawnik w celu uzyskania pomocy.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Dostęp do obszaru właściwości, aby dodać swoje informacje o prywatności

1.  Zaloguj się do portalu Azure jako administrator dzierżawy.

2.  Na lewy pasek nawigacyjny, wybierz **usługi Azure Active Directory**, a następnie wybierz **właściwości**.

    **Właściwości** obszarze.

    ![Azure obszaru właściwości AD wyróżnianie obszaru informacji o prywatności](./media/active-directory-properties-area/properties-area.png)

3.  Dodaj swoje informacje o ochronie prywatności dla pracowników:

    - **Skontaktuj się z pomocą techniczną.** Wpisz adres e-mail osoby kontaktowej pomocy technicznej w Twojej organizacji.
    
    - **Skontaktuj się z globalnego prywatności.** Wpisz adres e-mail osoby kontaktowej dla zapytania o prywatności danych osobowych. Ta osoba jest także który Microsoft skontaktuje się w przypadku naruszenia danych. Jeśli nie ma żadnych osoby wymienione w tym miejscu, Microsoft kontaktuje się z administratorami w Twojej organizacji globalnych.

    - **Adres URL prywatności.** Typ łącza do dokumentu w organizacji, opisujące, jak organizacji obsługuje zarówno prywatność danych wewnętrznych i zewnętrznych gościa.

        >[!Important]
        >Jeśli nie zawierają własne zasady zachowania poufności lub kontaktu prywatności, zewnętrznych gości zostanie wyświetlony tekst w **Przejrzyj uprawnienia** polu, które  **< _nazwę organizacji_> nie dostarczył łącza do ich postanowień możesz się zapoznać**. Na przykład użytkownika gościa zostanie wyświetlony ten komunikat po otrzymaniu zaproszenia dostępu do organizacji za pomocą współpracy B2B.

        ![Uprawnienia Przegląd współpracy B2B okno z komunikatem](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
- [Realizacji zaproszenia współpraca w usłudze Azure Active Directory B2B](https://aka.ms/b2bredemption)
- [Dodawanie lub zmienianie informacji o profilu użytkownika w usłudze Azure Active Directory](/active-directory-users-profile-azure-portal.md)