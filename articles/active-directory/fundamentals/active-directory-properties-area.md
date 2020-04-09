---
title: Dodawanie informacji o ochronie prywatności organizacji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące dodawania informacji o prywatności organizacji do obszaru Właściwości usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876188"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Dodawanie informacji o ochronie prywatności organizacji przy użyciu usługi Azure Active Directory
W tym artykule wyjaśniono, jak administrator dzierżawy może dodawać informacje związane z prywatnością do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji za pośrednictwem witryny Azure Portal.

Zdecydowanie zalecamy dodanie zarówno globalnego kontaktu prywatności, jak i zasad zachowania poufności informacji w organizacji, aby twoi pracownicy wewnętrzni i goście zewnętrzni mogli zapoznać się z twoimi zasadami. Ponieważ oświadczenia o ochronie prywatności są jednoznacznie tworzone i dostosowane do każdej firmy, zalecamy skontaktowanie się z prawnikiem w celu uzyskania pomocy.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Dodawanie informacji o prywatności w usłudze Azure AD
Dodaj informacje o prywatności organizacji w obszarze **Właściwości** usługi Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Aby uzyskać dostęp do obszaru Właściwości i dodać informacje o prywatności

1.    Zaloguj się do witryny Azure portal jako administrator dzierżawy.

2.    Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Właściwości**.

    Pojawi się obszar **Właściwości.**

    ![Obszar Właściwości usługi Azure AD z wyróżnioniem obszaru informacji o prywatności](media/active-directory-properties-area/properties-area.png)

3.    Dodaj informacje o prywatności dla swoich pracowników:

    - **Kontakt techniczny.** Wpisz adres e-mail osoby, z którą ma się skontaktować w celu uzyskania pomocy technicznej w organizacji.
    
    - **Globalny kontakt z prywatnością.** Wpisz adres e-mail, aby osoba kontaktować się w celu uzyskania informacji na temat prywatności danych osobowych. Ta osoba jest również kontakty firmy Microsoft w przypadku naruszenia bezpieczeństwa danych. Jeśli na liście nie ma w tym miejscu żadnej osoby, firma Microsoft skontaktuje się z administratorami globalnymi.

    - **Adres URL zasad zachowania poufności informacji.** Wpisz łącze do dokumentu organizacji opisujące sposób, w jaki organizacja obsługuje prywatność danych gościa wewnętrznego i zewnętrznego.

        >[!Important]
        >Jeśli nie uwzględnisz własnego oświadczenia o ochronie prywatności ani kontaktu z prywatnością, twoi goście zewnętrzni zobaczą tekst w polu **Uprawnienia recenzji,** który mówi, ** <że _Twoja nazwa organizacji_> nie poda linków do ich warunków do sprawdzenia.** Na przykład użytkownik-gość zobaczy ten komunikat po otrzymaniu zaproszenia do uzyskania dostępu do organizacji za pośrednictwem współpracy B2B.

        ![B2B Collaboration Review Permissions pole z komunikatem](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Wybierz **pozycję Zapisz**.

## <a name="next-steps"></a>Następne kroki
- [Realizacja zaproszenia do współpracy usługi Azure Active Directory B2B](../b2b/redemption-experience.md)
- [Dodawanie lub zmienianie informacji o profilu dla użytkownika w usłudze Azure Active Directory](active-directory-users-profile-azure-portal.md)
