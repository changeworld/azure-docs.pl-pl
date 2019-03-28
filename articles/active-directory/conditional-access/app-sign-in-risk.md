---
title: Przewodnik Szybki Start — blokuje dostęp po wykryciu zagrożenia sesji przy użyciu dostępu warunkowego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak konfigurować zasady dostępu warunkowego usługi Azure Active Directory (Azure AD) do blokowania operacji logowania w oparciu o ryzyko sesji.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, zabezpieczenia dostępu do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de5fdfab0858536cdd0cd81a81ea7cbc012b855
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518222"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Szybki start: Zablokuj dostęp po wykryciu zagrożenia sesji przy użyciu dostępu warunkowego usługi Azure Active Directory  

Aby zachować ochronę środowiska, można zablokować podejrzanych użytkowników logowania. [Usługa Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analizuje każdy logowania, a następnie oblicza prawdopodobieństwo, że logowanie próba nie było wykonywane przez prawowitym właścicielem konta użytkownika. Prawdopodobieństwo (niskiej, średniej, wysokiej) jest wskazywany w postaci obliczonej wartości o nazwie [poziomy ryzyka logowania](conditions.md#sign-in-risk). Ustawiając warunek ryzyka logowania, można skonfigurować zasady dostępu warunkowego, aby odpowiedzieć na poziomach określonych ryzyka logowania. 

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasad dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) blokuje logowania podczas wykryto poziom skonfigurowany ryzyka logowania. 

![Tworzenie zasad](./media/app-sign-in-risk/1000.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="prerequisites"></a>Wymagania wstępne 

Do ukończenia scenariusza z tego samouczka są potrzebne następujące elementy:

- **Dostęp do wersji Azure AD Premium P2** — dostęp warunkowy jest funkcja Azure AD Premium P1, należy wersji P2, ponieważ scenariusz, w tym przewodniku Szybki Start wymaga Identity Protection. 

- **Ochrona tożsamości** — scenariusz, w tym przewodniku Szybki Start wymaga włączenia ochrony tożsamości. Jeśli nie wiesz, jak włączyć usługę Identity Protection, zobacz [włączania usługi Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Przeglądarka tor** — [przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en) zaprojektowano w celu zachowania prywatności w trybie online. Identity Protection wykrywa logowania w przeglądarce sieci Tor jako **logowania z anonimowych adresów IP**, który ma poziom średniego ryzyka. Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Konto testowe o nazwie Alain Charon** — Jeśli nie wiesz, jak tworzyć konta testowego, zobacz [Dodawanie użytkowników w chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testowanie logowania 

Celem tego kroku jest, aby upewnić się, że Twoje konto testu mają dostęp do dzierżawy za pomocą przeglądarki sieci Tor.

**Aby przetestować logowanie:**

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com) jako **Alain Charon**.

2. Wyloguj się. 


## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego 

Scenariusz, w tym przewodniku Szybki Start używa logowania za pomocą przeglądarki Tor do generowania wykryte **logowania z anonimowych adresów IP** zdarzenie o podwyższonym ryzyku. Poziom ryzyka dotyczący tego zdarzenia o podwyższonym ryzyku to średni. Aby odpowiedzieć na to zdarzenie o podwyższonym ryzyku, należy ustawić warunkiem ryzyka logowania średniej. W środowisku produkcyjnym należy ustawić stan ryzyka logowania wysoki lub na Średni i wysoki.     

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego wymagany. Ustaw w zasadach:

|Ustawienie |Wartość|
|---     | --- |
| Użytkownicy i grupy | Alain Charon  |
| Aplikacje w chmurze | Wszystkie aplikacje w chmurze |
| Ryzyko logowania | Medium |
| Udziel | Blokuj dostęp |
 

![Tworzenie zasad](./media/app-sign-in-risk/130.png)

 


**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.

2. W witrynie Azure portal, w lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**. 

    ![Usługa Azure Active Directory](./media/app-sign-in-risk/02.png)

3. Na **usługi Azure Active Directory** stronie **zabezpieczeń** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/app-sign-in-risk/03.png)
 
4. Na **dostępu warunkowego** w pasku narzędzi u góry, kliknij **Dodaj**.

    ![Name (Nazwa)](./media/app-sign-in-risk/108.png)

5. Na **New** strony w **nazwa** polu tekstowym wpisz **zablokować dostęp dla poziomu średniego ryzyka**.

    ![Name (Nazwa)](./media/app-sign-in-risk/104.png)

6. W **przypisania** kliknij **użytkowników i grup**.

    ![Użytkownicy i grupy](./media/app-sign-in-risk/06.png)

7. Na **użytkowników i grup** strony:

    ![Dostęp warunkowy](./media/app-sign-in-risk/107.png)

    a. Kliknij przycisk **Wybieranie użytkowników i grup**, a następnie wybierz pozycję **użytkowników i grup**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** wybierz **Alain Charon**, a następnie kliknij przycisk **wybierz**.

    d. Na **użytkowników i grup** kliknij **gotowe**.

8. Kliknij przycisk **aplikacje w chmurze**.

    ![Aplikacje w chmurze](./media/app-sign-in-risk/08.png)

9. Na **aplikacje w chmurze** strony:

    ![Dostęp warunkowy](./media/app-sign-in-risk/109.png)

    a. Kliknij przycisk **wszystkie aplikacje w chmurze**.

    b. Kliknij przycisk **Gotowe**.

10. Kliknij przycisk **warunki**. 

    ![Kontrole dostępu](./media/app-sign-in-risk/19.png)

11. Na **warunki** strony:

    ![Poziom ryzyka logowania](./media/app-sign-in-risk/21.png)

    a. Kliknij przycisk **ryzyka logowania**.
 
    b. Jako **Konfiguruj**, kliknij przycisk **tak**.

    c. Poziom ryzyka logowania wybrać **średni**.

    d. Kliknij pozycję **Wybierz**.

    e. Na **warunki** kliknij **gotowe**.



10. W **kontrole dostępu** kliknij **Grant**.

    ![Kontrole dostępu](./media/app-sign-in-risk/10.png)

11. Na **Grant** strony:

    ![Dostęp warunkowy](./media/app-sign-in-risk/105.png)

    a. Wybierz **blokowana**.

    b. Kliknij pozycję **Wybierz**.

12. W **Włącz zasady** kliknij **na**.

    ![Włącz zasady](./media/app-sign-in-risk/18.png)

13. Kliknij pozycję **Utwórz**.


## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działają one zgodnie z oczekiwaniami. Pierwszym krokiem używać dostępu warunkowego **co zrobić, jeśli narzędzie zasad** do symulowania logowania dla użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Po uruchomieniu **co zrobić, jeśli narzędzie zasad** w tym scenariuszu **zablokować dostęp dla poziomu średniego ryzyka** powinny zostać wyświetlone w obszarze **zasad, które będą stosowane**. 

![Użytkownik](./media/app-sign-in-risk/117.png)


**Aby ocenić zasady dostępu warunkowego:**

1. Na [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij **co zrobić, jeśli**.  
 
    ![What If](./media/app-sign-in-risk/14.png)

2. Kliknij przycisk **użytkownika**, wybierz opcję **Alan Charon** na **użytkowników** strony, a następnie kliknij przycisk **wybierz**.

    ![Użytkownik](./media/app-sign-in-risk/116.png)

3. Jako **ryzyka logowania**, wybierz opcję **średni**.

    ![Użytkownik](./media/app-sign-in-risk/119.png)


3. Kliknij przycisk **co zrobić, jeśli**.


## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji mają pokazaliśmy, jak można obliczyć symulowane logowania. Oprócz symulacji należy przetestować również zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami. 

Aby sprawdzić zasady, spróbuj zalogować się do swojej [witryny Azure portal](https://portal.azure.com) jako **Alan Charon** za pomocą przeglądarki sieci Tor. Próba logowania powinien być blokowany przez zasady dostępu warunkowego.

![Uwierzytelnianie wieloskładnikowe](./media/app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń użytkownika testowego, w przeglądarce sieci Tor i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [usunąć użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi Szybki dostęp.

    ![Uwierzytelnianie wieloskładnikowe](./media/app-sign-in-risk/33.png)

- Aby uzyskać instrukcje, aby usunąć przeglądarki Tor, zobacz [odinstalowywanie](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wymagane warunki użytkowania, należy zaakceptować](require-tou.md)
> [wymagają usługi MFA dla określonych aplikacji](app-based-mfa.md)

