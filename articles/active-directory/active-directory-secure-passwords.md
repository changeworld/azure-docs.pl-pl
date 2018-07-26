---
title: Wielopoziomowe zabezpieczenia hasła w usłudze Azure AD | Microsoft Docs
description: W tym artykule wyjaśniono, jak usługa Azure AD wymusza korzystanie z silnych haseł i chroni hasła użytkowników przed cyberprzestępcami.
services: active-directory
documentationcenter: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/28/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 277e66e954d0fdff026d473ba3f6ad07a8b87da7
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173413"
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Wielopoziomowe podejście do zabezpieczeń haseł w usłudze Azure AD

W tym artykule omówiono najlepsze rozwiązania, które można zastosować jako użytkownik lub administrator, aby chronić usługę Azure Active Directory (Azure AD) i konto Microsoft.

 > [!NOTE]
 > **Jesteś tutaj, ponieważ masz problemy z logowaniem?** Jeśli tak, [w tym miejscu opisano, jak zmienić i zresetować własne hasło](user-help/active-directory-passwords-update-your-own-password.md).
 >
 > Administratorzy usługi Azure AD mogą resetować hasła użytkowników, korzystając z wskazówek zawartych w artykule [Reset the password for a user in Azure Active Directory (Resetowanie hasła użytkownika w usłudze Azure Active Directory)](fundamentals/active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Wymagania dotyczące hasła

Usługa Azure AD zapewnia następujące popularne podejścia do zabezpieczania haseł:

* Wymagania dotyczące długości hasła
* Wymagania dotyczące złożoności hasła
* Regularne i okresowe wygasanie haseł

Aby uzyskać informacje dotyczące resetowania haseł w usłudze Azure Active Directory, zobacz temat [Azure AD self-service password reset for the IT professional (Samodzielne resetowanie hasła usługi Azure AD dla informatyków)](user-help/active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Ochrona hasła w usłudze Azure AD

W usłudze Azure AD i systemie kont Microsoft używane są sprawdzone w branży metody zapewnienia bezpieczeństwa haseł użytkowników i administratorów, w tym:

* Hasła dynamicznie zakazane
* Inteligentna blokada haseł

Informacje dotyczące zarządzania hasłami oparte na aktualnych badaniach można znaleźć w oficjalnym dokumencie [Password Guidance (Wytyczne dotyczące haseł)](https://aka.ms/passwordguidance).

### <a name="dynamically-banned-passwords"></a>Hasła dynamicznie zakazane

Usługa Azure AD i konta Microsoft zapewniają ochronę haseł przez dynamiczne zakazywanie wszystkich stosowanych powszechnie haseł. Zespół usługi Azure AD Identity Protection regularnie analizuje listy zakazanych haseł, zapobiegając stosowaniu powszechnie używanych haseł przez użytkowników. Ta usługa jest dostępna dla klientów usługi Azure AD i usługi konta Microsoft.

Podczas tworzenia hasła ważne jest, aby administratorzy zachęcali użytkowników do wybierania haseł zawierających unikatową kombinację liter, cyfr, znaków i słów. Takie podejście pomaga zapewnić, że złamanie haseł użytkowników jest praktycznie niemożliwe, a jednocześnie ułatwia ich zapamiętywanie.

#### <a name="password-breaches"></a>Naruszenia haseł

Firma Microsoft nieustannie pracuje, by pozostawać o krok przed przestępcami.

Zespół ds. usługi Azure AD Identity Protection nieustannie analizuje często używane hasła. Cyberprzestępcy również często stosują podobne strategie do przeprowadzania ataków, na przykład tworzą [tęczowe tablice](https://en.wikipedia.org/wiki/Rainbow_table) w celu łamania skrótów haseł.

Firma Microsoft stale analizuje [naruszenia danych](https://www.privacyrights.org/data-breaches) w celu utrzymania dynamicznie aktualizowanej listy zakazanych haseł, dzięki której narażone hasła są zakazywane, zanim staną się prawdziwym zagrożeniem dla klientów usługi Azure AD. Więcej informacji o naszych bieżących działaniach związanych z zabezpieczeniami można znaleźć w raporcie analizy zabezpieczeń firmy Microsoft [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Inteligentna blokada haseł

Gdy usługa Azure AD wykryje próbę złamania hasła użytkownika przez potencjalnego przestępcę, blokujemy konto użytkownika przy użyciu funkcji inteligentnej blokady haseł. Usługę Azure AD zaprojektowano z myślą o określaniu ryzyka związanego z konkretnymi sesjami logowania. Następnie przy użyciu najbardziej aktualnych danych dotyczących zabezpieczeń stosujemy semantykę blokad, aby powstrzymać zagrożenia cybernetyczne.

W przypadku zablokowania użytkownika w usłudze Azure AD jego ekran przypomina następujący:

  ![Blokada w usłudze Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

W przypadku innych kont Microsoft ekran użytkownika przypomina następujący:

  ![Blokada konta Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Aby uzyskać informacje dotyczące resetowania haseł w usłudze Azure Active Directory, zobacz temat [Azure AD self-service password reset for the IT professional (Samodzielne resetowanie hasła usługi Azure AD dla informatyków)](user-help/active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Jeśli jesteś administratorem usługi Azure AD, możesz użyć usługi [Windows Hello](https://www.microsoft.com/windows/windows-hello), aby całkowicie uniknąć tworzenia tradycyjnych haseł przez użytkowników.
  >

## <a name="next-steps"></a>Następne kroki

* [Jak zaktualizować własne hasło](user-help/active-directory-passwords-update-your-own-password.md)
* [The fundamentals of Azure identity management](fundamentals-identity.md) (Podstawy zarządzania tożsamościami w usłudze Azure)
* [Report on password reset activity (Raporty dotyczące działań związanych z resetowaniem haseł)](authentication/howto-sspr-reporting.md)
