---
title: Szybki Start — Blokuj dostęp w przypadku wykrycia ryzyka sesji z Azure Active Directory Identity Protection | Microsoft Docs
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować zasady dostępu warunkowego dla ochrony tożsamości w usłudze Azure Active Directory (Azure AD) w celu blokowania logowania na podstawie ryzyka związanego z sesją.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb1e29735a860f5dc3b6ce8996af9fcd4962871
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335306"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Szybki start: Blokuj dostęp w przypadku wykrycia ryzyka sesji z Azure Active Directory Identity Protection  

Aby zachować ochronę środowiska, można zablokować podejrzanych użytkowników przed zalogowaniem się. Usługa Azure Active Directory (Azure AD) Identity Protection analizuje poszczególne logowania i oblicza prawdopodobieństwo, że próba logowania nie została wykonana przez uprawnionego właściciela konta użytkownika. Prawdopodobieństwo (niski, średni, wysoki) jest wskazywane w postaci wartości obliczanej o nazwie poziom ryzyka logowania. Ustawiając warunek ryzyka związanego z logowaniem, można skonfigurować zasady dostępu warunkowego dotyczącego ryzyka związanego z logowaniem w celu reagowania na określone poziomy ryzyka związanego z logowaniem. 

W tym przewodniku szybki start przedstawiono sposób konfigurowania zasad dostępu warunkowego dotyczącego ryzyka związanego z logowaniem, które blokują logowanie w przypadku wykrycia średniego i wyższego poziomu ryzyka związanego z logowaniem. 

![Utwórz zasady](./media/quickstart-sign-in-risk-policy/1004.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne 

Do ukończenia scenariusza z tego samouczka są potrzebne następujące elementy:

- **Dostęp do Azure AD — wersja Premium P2 Edition** — Azure AD Identity Protection jest funkcją Azure AD — wersja Premium P2. 
- **Ochrona tożsamości** — scenariusz w tym przewodniku Szybki Start wymaga włączenia ochrony tożsamości. Jeśli nie wiesz, jak włączyć ochronę tożsamości, zobacz [włączanie Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Przeglądarka sieci Tor** — [przeglądarka tor](https://www.torproject.org/projects/torbrowser.html.en) została zaprojektowana tak, aby pomóc zachować prywatność w trybie online. Ochrona tożsamości wykrywa logowanie z przeglądarki tor jako **logowania z anonimowych adresów IP**, które mają poziom ryzyka średniego. Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../reports-monitoring/concept-risk-events.md).  
- **Konto testowe o nazwie Alain Charon** — Jeśli nie wiesz, jak utworzyć konto testowe, zobacz [Dodawanie nowego użytkownika](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testowanie logowania 

Celem tego kroku jest upewnienie się, że konto testowe może uzyskać dostęp do dzierżawy przy użyciu przeglądarki sieci Tor.

**Aby przetestować Logowanie:**

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako **Alain Charon**.
2. Wyloguj się. 

## <a name="create-your-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego 

Scenariusz w tym przewodniku szybki start używa logowania z przeglądarki Tor w celu wygenerowania wykrytych logowań **z poziomu zdarzenia anonimowych adresów IP** . Poziomem ryzyka tego zdarzenia jest średnie. Aby odpowiedzieć na to zdarzenie związane z ryzykiem, należy ustawić wartość średni warunek ryzyka logowania. 

W tej sekcji przedstawiono sposób tworzenia wymaganych zasad dostępu warunkowego dotyczącego ryzyka związanego z logowaniem. W zasadach ustaw następujące ustawienia:

|Ustawienie |Value|
|---     | --- |
| Użytkownicy  | Alain Charon  |
| Warunki | Ryzyko związane z logowaniem, średnie i powyżej |
| Formanty | Blokuj dostęp |

![Utwórz zasady](./media/quickstart-sign-in-risk-policy/201.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny.
2. Przejdź do [strony Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Na stronie **Azure AD Identity Protection** w sekcji **Konfiguracja** kliknij pozycję **zasady dotyczące ryzyka związanego**z logowaniem.
4. Na stronie zasady w sekcji **przypisania** kliknij pozycję **Użytkownicy**.
5. Na stronie **Użytkownicy** kliknij pozycję **Wybierz użytkowników**.
6. Na stronie **Wybieranie użytkowników** wybierz pozycję **Alain Charon**, a następnie kliknij pozycję **Wybierz**.
7. Na stronie **Użytkownicy** kliknij przycisk **gotowe**. 
8. Na stronie zasady w sekcji **przypisania** kliknij pozycję **warunki**.
9. Na stronie **warunki** kliknij pozycję **ryzyko związane**z logowaniem.
10. Na stronie **ryzyko związane** z logowaniem wybierz pozycję **średni i powyżej**, a następnie kliknij pozycję **Wybierz**. 
11. Na stronie **warunki** kliknij przycisk **gotowe**.
12. Na stronie zasady w sekcji kontrolki  kliknij pozycję **dostęp**.
13. Na stronie **dostęp** kliknij pozycję **Zezwalaj na dostęp**, wybierz pozycję **Wymagaj uwierzytelniania**wieloskładnikowego, a następnie kliknij pozycję **Wybierz**.
14. Na stronie zasady kliknij przycisk **Zapisz**.  

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

Aby przetestować zasady, spróbuj zalogować się do [Azure Portal](https://portal.azure.com) jako **Artur Charon** przy użyciu przeglądarki sieci Tor. Próba logowania powinna być blokowana przez zasady dostępu warunkowego.

![Uwierzytelnianie wieloskładnikowe](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń użytkownika testowego, przeglądarkę sieci Tor i wyłącz zasady dostępu warunkowego dotyczącego ryzyka związanego z logowaniem:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [jak dodawać i usuwać użytkowników](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby uzyskać instrukcje dotyczące usuwania przeglądarki tor, zobacz [Odinstalowywanie](https://tb-manual.torproject.org/uninstalling/).
