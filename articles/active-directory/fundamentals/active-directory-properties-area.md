---
title: Dodaj informacje o prywatności w organizacji w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak dodać informacje o prywatności w organizacji do obszaru właściwości usługi Azure Active Directory (Azure AD).
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: f32eeee8f02cee32eb05e14aa98387a20cb2e626
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904296"
---
# <a name="how-to-add-your-privacy-info-using-azure-active-directory"></a>Instrukcje: Dodawanie informacji zabezpieczających zachowania przy użyciu usługi Azure Active Directory
W tym artykule wyjaśniono, jak dodać informacje dotyczące ochrony prywatności do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji, w witrynie Azure portal jako Administrator dzierżawy.

Zdecydowanie zalecamy dodawanie przez osobę kontaktową globalne ochrona prywatności i poufności informacji w organizacji, więc pracowników wewnętrznych i zewnętrznych gości, można przejrzeć swoje zasady. Ponieważ zasady zachowania poufności informacji jednoznacznie są tworzone i przeznaczonym dla każdej firmy, zdecydowanie zalecamy się z prawnikiem uzyskania pomocy.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Dodawanie zachowania poufności informacji w usłudze Azure AD
Dodaj informacje o ochronie prywatności w Twojej organizacji w **właściwości** obszar usługi Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Dostęp do obszaru właściwości i Dodaj informacje o prywatności

1.  Zaloguj się do witryny Azure portal jako administrator dzierżawy.

2.  Na lewym pasku nawigacyjnym wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **właściwości**.

    **Właściwości** obszarze.

    ![Usługa Azure obszaru właściwości usługi AD wyróżnienie obszaru informacje o prywatności](media/active-directory-properties-area/properties-area.png)

3.  Dodaj swoje informacje o prywatności dla pracowników:

    - **Skontaktuj się z pomocą technicznych.** Wpisz adres e-mail osoby kontaktowej w celu uzyskania pomocy technicznej w Twojej organizacji.
    
    - **Skontaktuj się z zachowania globalnego.** Wpisz adres e-mail osoby kontaktowej dla zapytania dotyczące prywatności danych osobowych. Osoba ta jest kto firmy Microsoft skontaktuje się w przypadku naruszenia danych. W przypadku braku osoby wymienione w tym miejscu firmy Microsoft skontaktuje się z administratorów globalnych.

    - **Adres URL zasad zachowania poufności informacji.** Typ łącza do dokumentu w organizacji, w tym artykule opisano, jak Twoja organizacja obsługuje zarówno prywatność danych wewnętrznych i zewnętrznych gościa.

        >[!Important]
        >Jeśli nie podasz własne zasady zachowania poufności i ochrony prywatności przejrzenia przez osobę kontaktową zewnętrznych gości zostanie wyświetlony tekst w **Przegląd uprawnień** polu, które  **< _nazwę swojej organizacji_> nie udostępnił linków do swoich warunków, którymi możesz się zapoznać**. Na przykład użytkownik-Gość zostanie wyświetlony ten komunikat, po otrzymaniu zaproszenia dostęp do organizacji za pośrednictwem współpracy B2B.

        ![Okno uprawnień przeglądu współpracy B2B z komunikatem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
- [Realizacja zaproszenia współpracy w usłudze Azure Active Directory B2B](https://aka.ms/b2bredemption)
- [Dodawanie lub zmienianie informacji o profilu użytkownika w usłudze Azure Active Directory](active-directory-users-profile-azure-portal.md)