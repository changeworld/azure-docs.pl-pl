---
title: Używanie wykrywania ryzyka za pomocą dostępu warunkowego usługi Azure Active Directory
description: W tym przewodniku Szybki start dowiesz się, jak skonfigurować zasady dostępu warunkowego usługi Azure Active Directory (Azure AD) do blokowania logów na podstawie ryzyka sesji.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77186612"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Szybki start: blokowanie dostępu po wykryciu ryzyka sesji za pomocą dostępu warunkowego usługi Azure Active Directory  

Aby chronić środowisko, możesz zablokować podejrzanym użytkownikom możliwość logowania. [Usługa Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analizuje każde logowanie i oblicza prawdopodobieństwo, że próba logowania nie została wykonana przez prawowitego właściciela konta użytkownika. Prawdopodobieństwo (niskie, średnie, wysokie) jest wskazane w postaci obliczonej wartości zwanej [poziomami ryzyka logowania](concept-conditional-access-conditions.md#sign-in-risk). Ustawiając warunek ryzyka logowania, można skonfigurować zasady dostępu warunkowego, aby reagować na określone poziomy ryzyka logowania.

Ten przewodnik Szybki start pokazuje, jak skonfigurować [zasadę dostępu warunkowego,](../active-directory-conditional-access-azure-portal.md) która blokuje logowanie po wykryciu skonfigurowanego poziomu ryzyka logowania.

![Tworzenie zasad](./media/app-sign-in-risk/1000.png)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia scenariusza zaprezentowanego w tym samouczku potrzebne są następujące elementy:

- **Dostęp do usługi Azure AD Premium P2 —** podczas gdy dostęp warunkowy jest możliwością usługi Azure AD Premium P1, potrzebujesz wersji P2, ponieważ scenariusz w tym przewodniku Szybki start wymaga ochrony tożsamości.
- **Ochrona tożsamości** — scenariusz w tym przewodniku Szybki start wymaga włączenia ochrony tożsamości. Jeśli nie wiesz, jak włączyć ochronę tożsamości, zobacz [Włączanie ochrony tożsamości usługi Azure Active Directory](../identity-protection/overview-identity-protection.md).
- **Przeglądarka Tora** - [Przeglądarka Tora](https://www.torproject.org/projects/torbrowser.html.en) ma na celu pomóc Ci zachować swoją prywatność w Internecie. Ochrona tożsamości wykrywa logowanie z przeglądarki Tora jako logowania z anonimowych adresów IP, które mają średni poziom ryzyka. Aby uzyskać więcej informacji, zobacz [Wykrywanie ryzyka usługi Azure Active Directory](../reports-monitoring/concept-risk-events.md).  
- **Konto testowe o nazwie Alain Charon** — jeśli nie wiesz, jak utworzyć konto testowe, zobacz [Dodawanie użytkowników w chmurze.](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)

## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest upewnienie się, że twoje konto testowe może uzyskać dostęp do dzierżawy za pomocą przeglądarki Tora.

**Aby przetestować logowanie:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako **Alain Charon**.
1. Wyloguj się.

## <a name="create-your-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Scenariusz w tym przewodniku Szybki start używa logowania z przeglądarki Tora do **generowania wykrytych logów z wykrywania ryzyka anonimowych adresów IP.** Poziom ryzyka tego wykrywania ryzyka jest średni. Aby odpowiedzieć na to wykrywanie ryzyka, należy ustawić warunek ryzyka logowania na średni. W środowisku produkcyjnym należy ustawić warunek ryzyka logowania na wysoki lub średni i wysoki.

W tej sekcji pokazano, jak utworzyć wymagane zasady dostępu warunkowego. W zasadach ustaw:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Alain Charon  |
| Aplikacje w chmurze | Wszystkie aplikacje w chmurze |
| Ryzyko logowania | Medium |
| Uprawnienie | Blokowanie dostępu |

![Tworzenie zasad](./media/app-sign-in-risk/130.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. W witrynie Azure portal na lewym pasku nawigacyjnym kliknij pozycję **Usługa Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Na stronie **Usługi Azure Active Directory** w sekcji **Zabezpieczenia** kliknij pozycję **Dostęp warunkowy**.

   ![Dostęp warunkowy](./media/app-sign-in-risk/03.png)

1. Na stronie **Dostęp warunkowy** na pasku narzędzi u góry kliknij pozycję **Dodaj**.

   ![Nazwa](./media/app-sign-in-risk/108.png)

1. Na stronie **Nowy** w polu tekstowym **Nazwa** wpisz **pozycję Blokuj dostęp dla średniego poziomu ryzyka**.

   ![Nazwa](./media/app-sign-in-risk/104.png)

1. W sekcji **Przydział kliknij** pozycję **Użytkownicy i grupy**.

   ![Użytkownicy i grupy](./media/app-sign-in-risk/06.png)

1. Na stronie **Użytkownicy i grupy:**

   ![Dostęp warunkowy](./media/app-sign-in-risk/107.png)

   1. Kliknij **pozycję Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Użytkownicy i grupy**.
   1. Kliknij **pozycję Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Alain Charon**, a następnie kliknij przycisk **Zaznacz**.
   1. Na stronie **Użytkownicy i grupy** kliknij pozycję **Gotowe**.
1. Kliknij pozycję **Aplikacje w chmurze**.

   ![Aplikacje w chmurze](./media/app-sign-in-risk/08.png)

1. Na stronie **Aplikacje w chmurze:**

   ![Dostęp warunkowy](./media/app-sign-in-risk/109.png)

   1. Kliknij **pozycję Wszystkie aplikacje w chmurze**.
   1. Kliknij przycisk **Gotowe**.
1. Kliknij **pozycję Warunki**.

   ![Kontrole dostępu](./media/app-sign-in-risk/19.png)

1. Na stronie **Warunki:**

   ![Poziom ryzyka dotyczący logowania](./media/app-sign-in-risk/21.png)

   1. Kliknij pozycję **Ryzyko logowania**.
   1. Jak **skonfigurować**, kliknij przycisk **Tak**.
   1. Jako poziom ryzyka logowania wybierz pozycję **Średni**.
   1. Kliknij **pozycję Wybierz**.
   1. Na stronie **Warunki** kliknij pozycję **Gotowe**.
1. W sekcji **Formanty dostępu** kliknij pozycję **Przyznaj**.

   ![Kontrole dostępu](./media/app-sign-in-risk/10.png)

1. Na stronie **Grant:**

   ![Dostęp warunkowy](./media/app-sign-in-risk/105.png)

   1. Wybierz **pozycję Blokuj dostęp**.
   1. Kliknij **pozycję Wybierz**.
1. W sekcji **Włącz zasady** kliknij pozycję **Włączone**.

   ![Włączanie zasad](./media/app-sign-in-risk/18.png)

1. Kliknij przycisk **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Ocena symulowanego logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. W pierwszym kroku użyj narzędzia Dostępu **warunkowego, jeśli narzędzie zasad** do symulowania logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Po uruchomieniu **narzędzia zasad co zrobić, jeśli** w tym scenariuszu, **blokuj dostęp dla średniego poziomu ryzyka** powinny być wymienione w obszarze **Zasady, które będą stosowane**.

![Użytkownik](./media/app-sign-in-risk/117.png)

**Aby ocenić zasady dostępu warunkowego:**

1. Na stronie [Dostęp warunkowy — Zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij pozycję Co **jeśli**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Kliknij **pozycję Użytkownik**, wybierz pozycję Alan **Charon** na stronie **Użytkownicy,** a następnie kliknij przycisk **Wybierz**.

   ![Użytkownik](./media/app-sign-in-risk/116.png)

1. Jako **ryzyko logowania**wybierz Opcję **Średnia**.

   ![Użytkownik](./media/app-sign-in-risk/119.png)

1. Kliknij **przycisk Co jeśli**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji dowiesz się, jak ocenić symulowane logowanie. Oprócz symulacji należy również przetestować zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby przetestować swoje zasady, spróbuj zalogować się do [witryny Azure portal](https://portal.azure.com) jako **Alan Charon** przy użyciu przeglądarki Tora. Próba logowania powinna zostać zablokowana przez zasady dostępu warunkowego.

![Uwierzytelnianie wieloskładnikowe](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, usuń użytkownika testowego, przeglądarkę Tora i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [Usuwanie użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi szybki dostęp.

   ![Uwierzytelnianie wieloskładnikowe](./media/app-sign-in-risk/33.png)

- Aby uzyskać instrukcje dotyczące usuwania przeglądarki Tora, zobacz [Odinstalowanie](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wymagaj akceptowania warunków użytkowania](require-tou.md)
> Wymagaj usługi[MFA dla określonych aplikacji](app-based-mfa.md)
