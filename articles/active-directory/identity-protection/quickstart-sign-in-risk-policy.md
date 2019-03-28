---
title: Przewodnik Szybki Start — blokuje dostęp po wykryciu zagrożenia sesji przy użyciu usługi Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak konfigurować zasady dostępu warunkowego usługi Azure Active Directory (Azure AD) Identity Protection ryzyka logowania do blokowania operacji logowania w oparciu o ryzyko sesji.
services: active-directory
keywords: ochronę tożsamości i dostępu warunkowego do aplikacji, dostęp warunkowy w usłudze Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f5127342f97a90103ef56efbd7465832440ec0f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521818"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Szybki start: Zablokuj dostęp po wykryciu zagrożenia sesji przy użyciu usługi Azure Active Directory Identity Protection  

Aby zachować ochronę środowiska, można zablokować podejrzanych użytkowników z logowaniem. Usługa Azure Active Directory (Azure AD) Identity Protection analizuje każdy logowania i oblicza prawdopodobieństwo, że logowanie próba nie było wykonywane przez prawowitym właścicielem konta użytkownika. Prawdopodobieństwo (niskiej, średniej, wysokiej) jest wskazywany w postaci obliczonej wartości o nazwie poziom ryzyka logowania. Ustawiając warunek ryzyka logowania, można skonfigurować zasady dostępu warunkowego ryzyka logowania reagują na wystąpienie określonego ryzyka logowania poziomów. 

Ten przewodnik Szybki Start przedstawia sposób konfigurowania zasad dostępu warunkowego ryzyka logowania które blokuje logowania w przypadku średniej lub i powyżej ryzyka logowania poziomu został wykryty. 

![Tworzenie zasad](./media/quickstart-sign-in-risk-policy/1004.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="prerequisites"></a>Wymagania wstępne 

Do ukończenia scenariusza z tego samouczka są potrzebne następujące elementy:

- **Dostęp do wersji Azure AD Premium P2** -Azure AD Identity Protection to funkcja usługi Azure AD Premium P2. 

- **Ochrona tożsamości** — scenariusz, w tym przewodniku Szybki Start wymaga włączenia ochrony tożsamości. Jeśli nie wiesz, jak włączyć usługę Identity Protection, zobacz [włączania usługi Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Przeglądarka tor** — [przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en) zaprojektowano w celu zachowania prywatności w trybie online. Identity Protection wykrywa logowania w przeglądarce sieci Tor jako **logowania z anonimowych adresów IP**, który ma poziom średniego ryzyka. Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Konto testowe o nazwie Alain Charon** — Jeśli nie wiesz, jak tworzyć konta testowego, zobacz [dodać nowego użytkownika](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testowanie logowania 

Celem tego kroku jest, aby upewnić się, że Twoje konto testu mają dostęp do dzierżawy za pomocą przeglądarki sieci Tor.

**Aby przetestować logowanie:**

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com) jako **Alain Charon**.

2. Wyloguj się. 


## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego 

Scenariusz, w tym przewodniku Szybki Start używa logowania za pomocą przeglądarki Tor do generowania wykryte **logowania z anonimowych adresów IP** zdarzenie o podwyższonym ryzyku. Poziom ryzyka dotyczący tego zdarzenia o podwyższonym ryzyku to średni. Aby odpowiedzieć na to zdarzenie o podwyższonym ryzyku, należy ustawić warunkiem ryzyka logowania średniej. 

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego wymagany ryzyka logowania. Ustaw w zasadach:

|Ustawienie |Wartość|
|---     | --- |
| Użytkownicy  | Alain Charon  |
| Warunki | Ryzyko logowania, średnie i wyższe |
| Kontrolki | Blokuj dostęp |
 

![Tworzenie zasad](./media/quickstart-sign-in-risk-policy/201.png)

 


**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com) jako administrator globalny.

2. Przejdź do [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
 
3. Na **usługi Azure AD Identity Protection** strony w **Konfiguruj** , kliknij przycisk **zasad ryzyka logowania**.
 
4. Na stronie zasad w **przypisania** kliknij **użytkowników**.

5. Na **użytkowników** kliknij **wybranym użytkownikom**.

6. Na **wybranym użytkownikom** wybierz **Alain Charon**, a następnie kliknij przycisk **wybierz**.

7. Na **użytkowników** kliknij **gotowe**. 

8. Na stronie zasad w **przypisania** kliknij **warunki**.

9. Na **warunki** kliknij **ryzyka logowania**.

10. Na **ryzyka logowania** wybierz opcję **średni i nowsze wersje**, a następnie kliknij przycisk **wybierz**. 

11. Na **warunki** kliknij **gotowe**.

12. Na stronie zasad w **kontrolki** kliknij **dostępu**.

13. Na **dostępu** kliknij **zezwolić na dostęp**, wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie kliknij przycisk **wybierz**.

14. Na stronie zasad kliknij **Zapisz**.  


## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

Aby sprawdzić zasady, spróbuj zalogować się do swojej [witryny Azure portal](https://portal.azure.com) jako **Alan Charon** za pomocą przeglądarki sieci Tor. Próba logowania powinien być blokowany przez zasady dostępu warunkowego.

![Uwierzytelnianie wieloskładnikowe](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie będą już potrzebne, Usuń użytkownika testowego, w przeglądarce sieci Tor i wyłączanie zasad dostępu warunkowego ryzyka logowania:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [jak dodać lub usunąć użytkowników](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Aby uzyskać instrukcje, aby usunąć przeglądarki Tor, zobacz [odinstalowywanie](https://tb-manual.torproject.org/uninstalling/).


