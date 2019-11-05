---
title: Szybki Start — Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla określonych aplikacji z Azure Active Directory dostęp warunkowy | Microsoft Docs
description: W tym przewodniku szybki start dowiesz się, jak można powiązać wymagania dotyczące uwierzytelniania z typem dostępnej aplikacji w chmurze za pomocą dostępu warunkowego Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a23f8fc10e0e5a19be1f93cc6d6e5e8e301f86d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474031"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Szybki Start: Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji przy użyciu Azure Active Directory dostępu warunkowego

Aby uprościć środowisko logowania użytkowników, możesz chcieć zezwolić na logowanie się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Jednak wiele środowisk ma co najmniej kilka aplikacji, dla których zaleca się wymaganie, aby była wymagana silniejsza weryfikacja konta, na przykład uwierzytelnianie wieloskładnikowe (MFA). Te zasady mogą być prawdziwe w przypadku dostępu do systemu poczty e-mail organizacji lub aplikacji KADRowych. W Azure Active Directory (Azure AD) można osiągnąć ten cel za pomocą zasad dostępu warunkowego.

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasad dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) , które wymagają uwierzytelniania wieloskładnikowego w przypadku wybranej aplikacji w chmurze w danym środowisku.

![Przykładowe zasady dostępu warunkowego w Azure Portal](./media/app-based-mfa/32.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz w tym przewodniku Szybki Start, musisz wykonać następujące czynności:

- **Dostęp do Azure AD — wersja Premium Edition** — dostęp warunkowy usługi Azure AD jest funkcją Azure AD — wersja Premium.
- **Konto testowe o nazwie Isabella Simonsen** — Jeśli nie wiesz, jak utworzyć konto testowe, zobacz [Dodawanie użytkowników opartych na chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Scenariusz w tym przewodniku Szybki Start wymaga, aby uwierzytelnianie wieloskładnikowe poszczególnych użytkowników nie zostało włączone dla konta testowego. Aby uzyskać więcej informacji, zobacz [jak wymagać weryfikacji dwuetapowej dla użytkownika](../authentication/howto-mfa-userstates.md).

## <a name="test-your-experience"></a>Testowanie środowiska

Celem tego kroku jest zaplanowanie doświadczenia w pracy bez zasad dostępu warunkowego.

**Aby zainicjować środowisko:**

1. Zaloguj się do Azure Portal jako Isabella Simonsen.
1. Wyloguj się.

## <a name="create-your-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

W tej sekcji przedstawiono sposób tworzenia wymaganych zasad dostępu warunkowego. Scenariusz w tym przewodniku szybki start używa:

- Azure Portal jako symbol zastępczy dla aplikacji w chmurze, która wymaga uwierzytelniania wieloskładnikowego. 
- Przykładowy użytkownik do testowania zasad dostępu warunkowego.  

W zasadach ustaw następujące ustawienia:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Isabella Simonsen |
| Aplikacje w chmurze | Zarządzanie Microsoft Azure |
| Udzielanie dostępu | Wymagaj uwierzytelniania wieloskładnikowego |

![Rozszerzone zasady dostępu warunkowego](./media/app-based-mfa/31.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/app-based-mfa/02.png)

1. Na stronie **Azure Active Directory** w sekcji **zabezpieczenia** kliknij pozycję **dostęp warunkowy**.

   ![Dostęp warunkowy](./media/app-based-mfa/03.png)

1. Na stronie **dostęp warunkowy** na pasku narzędzi u góry kliknij pozycję **nowe zasady**.

   ![Dodawanie](./media/app-based-mfa/04.png)

1. Na **nowej** stronie w polu tekstowym **Nazwa** wpisz **Wymagaj uwierzytelniania wieloskładnikowego dla Azure Portal dostępu**.

   ![Nazwa](./media/app-based-mfa/05.png)

1. W sekcji **przypisanie** kliknij pozycję **Użytkownicy i grupy**.

   ![Użytkownicy i grupy](./media/app-based-mfa/06.png)

1. Na stronie **Użytkownicy i grupy** wykonaj następujące czynności:

   ![Użytkownicy i grupy](./media/app-based-mfa/24.png)

   1. Kliknij pozycję **Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Użytkownicy i grupy**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Isabella Simonsen**, a następnie kliknij pozycję **Wybierz**.
   1. Na stronie **Użytkownicy i grupy** kliknij przycisk **gotowe**.

1. Kliknij pozycję **aplikacje w chmurze**.

   ![Aplikacje w chmurze](./media/app-based-mfa/08.png)

1. Na stronie **aplikacje w chmurze** wykonaj następujące czynności:

   ![Wybierz aplikacje w chmurze](./media/app-based-mfa/26.png)

   1. Kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Zarządzanie Microsoft Azure**, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie **aplikacje w chmurze** kliknij pozycję **gotowe**.

1. W sekcji **kontrole dostępu** kliknij pozycję **Udziel**.

   ![Kontrola dostępu](./media/app-based-mfa/10.png)

1. Na stronie **Grant** wykonaj następujące czynności:

   ![Dawać](./media/app-based-mfa/11.png)

   1. Wybierz pozycję **Udziel dostępu**.
   1. Wybierz opcję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij pozycję **Wybierz**.

1. W sekcji **Włączanie zasad** **kliknij pozycję włączone.**

   ![Włącz zasady](./media/app-based-mfa/18.png)

1. Kliknij pozycję **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowanie

Teraz, po skonfigurowaniu zasad dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. Pierwszym krokiem jest użycie narzędzia do działania warunkowego, co pozwala na symulowanie logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Aby zainicjować narzędzie do oceny zasad **What If** , ustaw następujące polecenie:

- **Isabella Simonsen** jako użytkownik
- **Zarządzanie Microsoft Azure** jako aplikacja w chmurze

Kliknięcie przycisku **What If** powoduje utworzenie raportu symulacji, który pokazuje:

- **Wymagaj uwierzytelniania wieloskładnikowego dla Azure Portal dostępu** w ramach **zasad, które będą stosowane**
- **Wymagaj uwierzytelniania wieloskładnikowego** jako **kontroli uprawnień**.

![Co to jest narzędzie zasad](./media/app-based-mfa/23.png)

**Aby oszacować zasady dostępu warunkowego:**

1. Na stronie [zasady dostępu warunkowego](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij pozycję **What If**.  

   ![A co jeżeli](./media/app-based-mfa/14.png)

1. Kliknij pozycję **Użytkownicy**, wybierz pozycję **Isabella Simonsen**, a następnie kliknij pozycję **Wybierz**.

   ![Użytkownik](./media/app-based-mfa/15.png)

1. Aby wybrać aplikację w chmurze, wykonaj następujące czynności:

   ![Aplikacje w chmurze](./media/app-based-mfa/16.png)

   1. Kliknij pozycję **aplikacje w chmurze**.
   1. Na **stronie aplikacje w chmurze**kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Zarządzanie Microsoft Azure**, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie aplikacje w chmurze kliknij pozycję **gotowe**.

1. Kliknij **What If**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji wiesz już, jak oszacować symulowane logowanie. Oprócz symulacji należy również przetestować zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby przetestować zasady, spróbuj zalogować się do [Azure Portal](https://portal.azure.com) przy użyciu konta testowego usługi **Isabella Simonsen** . Powinno zostać wyświetlone okno dialogowe, które wymaga skonfigurowania konta pod kątem dodatkowej weryfikacji zabezpieczeń.

![Uwierzytelnianie wieloskładnikowe](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń użytkownika testowego i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [usuwanie użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi Szybki dostęp.

    ![Uwierzytelnianie wieloskładnikowe](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wymagaj zaakceptowania warunków użytkowania](require-tou.md)
> [Blokuj dostęp w przypadku wykrycia ryzyka sesji](app-sign-in-risk.md)
