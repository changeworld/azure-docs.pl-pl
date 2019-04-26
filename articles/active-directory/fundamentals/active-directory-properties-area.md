---
title: Dodawanie organizacji zachowania poufności informacji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania informacji o prywatności w organizacji do obszaru właściwości usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b4e3585f25a0b081e30c5524678a0f7bb27942
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248497"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Dodaj informacje o prywatności w organizacji za pomocą usługi Azure Active Directory
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
