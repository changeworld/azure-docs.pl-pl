---
title: Dodaj informacje o prywatności w organizacji w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak dodać informacje o prywatności w organizacji do obszaru właściwości usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: a34fa2b8c2d966af108664c219a222fb9a5b7abc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35938569"
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Instrukcje: Dodawanie informacji o prywatności w organizacji w usłudze Azure Active Directory
W tym artykule wyjaśniono, jak dodać informacje dotyczące ochrony prywatności do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji, w witrynie Azure portal jako Administrator dzierżawy.

Zdecydowanie zalecamy dodawanie przez osobę kontaktową globalne ochrona prywatności i poufności informacji w organizacji, więc pracowników wewnętrznych i zewnętrznych gości, można przejrzeć swoje zasady. Ponieważ zasady zachowania poufności informacji jednoznacznie są tworzone i przeznaczonym dla każdej firmy, zdecydowanie zalecamy się z prawnikiem uzyskania pomocy.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Dostęp do obszaru właściwości, aby dodać swoje informacje o prywatności

1.  Zaloguj się do witryny Azure portal jako administrator dzierżawy.

2.  Na lewym pasku nawigacyjnym wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **właściwości**.

    **Właściwości** obszarze.

    ![Usługa Azure obszaru właściwości usługi AD wyróżnienie obszaru informacje o prywatności](./media/active-directory-properties-area/properties-area.png)

3.  Dodaj swoje informacje o prywatności dla pracowników:

    - **Skontaktuj się z pomocą technicznych.** Wpisz adres e-mail osoby kontaktowej w celu uzyskania pomocy technicznej w Twojej organizacji.
    
    - **Skontaktuj się z zachowania globalnego.** Wpisz adres e-mail osoby kontaktowej dla zapytania dotyczące prywatności danych osobowych. Osoba ta jest kto firmy Microsoft skontaktuje się w przypadku naruszenia danych. W przypadku braku osoby wymienione w tym miejscu firmy Microsoft skontaktuje się z administratorów globalnych.

    - **Adres URL zasad zachowania poufności informacji.** Typ łącza do dokumentu w organizacji, w tym artykule opisano, jak Twoja organizacja obsługuje zarówno prywatność danych wewnętrznych i zewnętrznych gościa.

        >[!Important]
        >Jeśli nie podasz własne zasady zachowania poufności i ochrony prywatności przejrzenia przez osobę kontaktową zewnętrznych gości zostanie wyświetlony tekst w **Przegląd uprawnień** polu, które  **< _nazwę swojej organizacji_> nie udostępnił linków do swoich warunków, którymi możesz się zapoznać**. Na przykład użytkownik-Gość zostanie wyświetlony ten komunikat, po otrzymaniu zaproszenia dostęp do organizacji za pośrednictwem współpracy B2B.

        ![Okno uprawnień przeglądu współpracy B2B z komunikatem](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
- [Realizacja zaproszenia współpracy w usłudze Azure Active Directory B2B](https://aka.ms/b2bredemption)
- [Dodawanie lub zmienianie informacji o profilu użytkownika w usłudze Azure Active Directory](fundamentals/active-directory-users-profile-azure-portal.md)