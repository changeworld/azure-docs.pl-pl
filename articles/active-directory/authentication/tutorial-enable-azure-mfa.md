---
title: Włączanie usługi Azure Multi-Factor Authentication
description: W tym samouczku dowiesz się, jak włączyć uwierzytelnianie wieloskładnikowe platformy Azure dla grupy użytkowników i przetestować monit o współczynnik pomocniczy podczas zdarzenia logowania.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77154823"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>Samouczek: Bezpieczne zdarzenia logowania użytkownika za pomocą uwierzytelniania wieloskładnikowego platformy Azure

Uwierzytelnianie wieloskładnikowe (MFA) to proces, w którym użytkownik jest monitowany podczas zdarzenia logowania o dodatkowe formy identyfikacji. Ten monit może być wprowadzenie kodu na telefonie komórkowym lub dostarczenie skanowania odcisków palców. Jeśli potrzebujesz drugiej formy uwierzytelniania, zwiększa się bezpieczeństwo, ponieważ ten dodatkowy czynnik nie jest łatwym dla osoby atakującej w uzyskaniu lub zduplikowaniu.

Zasady uwierzytelniania wieloskładnikowego platformy Azure i dostępu warunkowego zapewniają elastyczność umożliwiającą obsługę usługi MFA dla użytkowników podczas określonych zdarzeń logowania.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad dostępu warunkowego w celu włączenia uwierzytelniania wieloskładnikowego platformy Azure dla grupy użytkowników
> * Konfigurowanie warunków zasad monitujących o uwierzytelnianie usługi MFA
> * Testowanie procesu usługi MFA jako użytkownika

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z włączoną licencją usługi Azure AD Premium lub licencją próbną.
    * W razie potrzeby [utwórz go za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego.*
* Użytkownik niebędący administratorem z hasłem, który znasz, na przykład *testuser*. W tym samouczku można przetestować środowisko uwierzytelniania wieloskładnikowego platformy Azure dla użytkownika końcowego przy użyciu tego konta.
    * Jeśli chcesz utworzyć użytkownika, zobacz [Szybki start: Dodawanie nowych użytkowników do usługi Azure Active Directory](../add-users-azure-active-directory.md).
* Grupa, do których należy użytkownik niebędący administratorem, na przykład *GRUPA TEST WIĄZANY..".* W tym samouczku włączysz uwierzytelnianie wieloskładnikowe platformy Azure dla tej grupy.
    * Jeśli chcesz utworzyć grupę, zobacz, jak [utworzyć grupę i dodać członków w usłudze Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Zalecany sposób włączania i używania uwierzytelniania wieloskładnikowego platformy Azure jest z zasadami dostępu warunkowego. Dostęp warunkowy umożliwia tworzenie i definiowanie zasad, które reagują na zdarzenia logowania i żądać dodatkowych akcji, zanim użytkownikowi przyzna się dostęp do aplikacji lub usługi.

![Omówienie diagramu działania programu Dostęp warunkowy w celu zabezpieczenia procesu logowania](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Zasady dostępu warunkowego mogą być szczegółowe i specyficzne, aby umożliwić użytkownikom produktywność w dowolnym miejscu i czasie, ale także chronić organizację. W tym samouczku utwórzmy podstawowe zasady dostępu warunkowego, aby monitować o uwierzytelnianie wieloskładnikowe, gdy użytkownik loguje się do witryny Azure portal. W późniejszym samouczku z tej serii można skonfigurować uwierzytelnianie wieloskładnikowe platformy Azure przy użyciu zasad dostępu warunkowego opartego na ryzyku.

Najpierw utwórz zasady dostępu warunkowego i przypisz grupę testową użytkowników w następujący sposób:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta z uprawnieniami *administratora globalnego.*
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Zabezpieczenia** z menu po lewej stronie.
1. Wybierz **pozycję Dostęp warunkowy**, a następnie wybierz pozycję **+ Nowa zasada**.
1. Wprowadź nazwę zasad, takich jak *Pilot usługi MFA*.
1. W obszarze **Przydziały**wybierz pozycję **Użytkownicy i grupy**, a następnie przycisk opcji **Wybierz użytkowników i grupuje.**
1. Zaznacz pole wyboru **Użytkownicy i grupy**, a następnie **wybierz,** aby przeglądać dostępnych użytkowników i grupy usługi Azure AD.
1. Wyszukaj i wybierz grupę usługi Azure AD, na przykład *MFA-Test-Group,* a następnie wybierz pozycję **Wybierz**.

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Aby zastosować zasadę dostępu warunkowego dla grupy, wybierz opcję **Gotowe**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Konfigurowanie warunków uwierzytelniania wieloskładnikowego

Po utworzeniu zasad dostępu warunkowego i grupie testowej przypisanej grupy użytkowników zdefiniuj teraz aplikacje w chmurze lub akcje, które wyzwalają zasady. Te aplikacje w chmurze lub akcje są scenariusze, które zdecydujesz wymagają dodatkowego przetwarzania, takich jak monit o uwierzytelnianie wieloskładnikowe. Na przykład można zdecydować, że dostęp do aplikacji finansowej lub korzystanie z narzędzi zarządzania wymaga jako dodatkowy monit weryfikacji.

W tym samouczku skonfiguruj zasady dostępu warunkowego, aby wymagać usługi MFA, gdy użytkownik loguje się do portalu Azure.

1. Wybierz pozycję **Aplikacje w chmurze lub akcje**. Zasady dostępu warunkowego można zastosować do *wszystkich aplikacji w chmurze* lub *Wybierz aplikacje*. Aby zapewnić elastyczność, można również wykluczyć niektóre aplikacje z zasad.

    W tym samouczku na stronie *Dołączanie* wybierz przycisk opcji **Wybierz aplikacje.**

1. Wybierz **pozycję Wybierz**, a następnie przejrzyj listę dostępnych zdarzeń logowania, z których można korzystać.

    W tym samouczku wybierz pozycję **Microsoft Azure Management,** aby zasady dotyczyły zdarzeń logowania w witrynie Azure portal.

1. Aby zastosować wybrane aplikacje, wybierz pozycję **Wybierz**, a następnie **gotowe**.

    ![Wybierz aplikację Microsoft Azure Management do uwzględnienia w zasadach dostępu warunkowego](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Formanty dostępu umożliwiają definiowanie wymagań dla użytkownika, które mają być przyznane dostępu, takich jak konieczności zatwierdzonej aplikacji klienckiej lub przy użyciu urządzenia, które jest hybrid Azure AD przyłączony. W tym samouczku skonfiguruj formanty dostępu, aby wymagać usługi MFA podczas zdarzenia logowania w witrynie Azure portal.

1. W obszarze *Formanty dostępu*wybierz pozycję **Przyznanie**, a następnie upewnij się, że jest zaznaczony przycisk opcji **Udzielanie dostępu.**
1. Zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego,** a następnie wybierz pozycję **Wybierz**.

Zasady dostępu warunkowego można ustawić *na Tylko raport,* jeśli chcesz zobaczyć, jak konfiguracja wpłynie na użytkowników, lub *Off,* jeśli nie chcesz używać zasad teraz. Jako grupa testowa użytkowników została przeznaczona dla tego samouczka, umożliwia włączenie zasad, a następnie przetestowanie uwierzytelniania wieloskładnikowego platformy Azure.

1. Ustaw włącz przełącznik *zasad* na **Włączone**.
1. Aby zastosować zasadę Dostępu warunkowego, wybierz pozycję **Utwórz**.

## <a name="test-azure-multi-factor-authentication"></a>Testowanie usługi Azure Multi-Factor Authentication

Zobaczmy zasady dostępu warunkowego i uwierzytelnianie wieloskładnikowe platformy Azure w działaniu. Najpierw zaloguj się do zasobu, który nie wymaga usługi MFA w następujący sposób:

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Zaloguj się za pomocą użytkownika testowego niebędącego administratorem, takiego jak *testuser*. Nie ma monitu o ukończenie usługi MFA.
1. Zamknij okno przeglądarki.

Teraz zaloguj się do witryny Azure portal. Ponieważ portal Azure został skonfigurowany w zasadach dostępu warunkowego, aby wymagać dodatkowej weryfikacji, zostanie wyświetlony monit o uwierzytelnianie wieloskładnikowe platformy Azure.

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub [https://portal.azure.com](https://portal.azure.com)incognito i przejdź do .
1. Zaloguj się za pomocą użytkownika testowego niebędącego administratorem, takiego jak *testuser*. Musisz zarejestrować się i używać uwierzytelniania wieloskładnikowego platformy Azure. Postępuj zgodnie z instrukcjami, aby zakończyć proces i zweryfikuj pomyślne zalogowanie się do witryny Azure portal.

    ![Postępuj zgodnie z instrukcjami przeglądarki, a następnie w zarejestrowanym uwierzytelniania wieloskładnikowym, aby się zalogować](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Zamknij okno przeglądarki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz już używać zasad dostępu warunkowego do włączania uwierzytelniania wieloskładnikowego platformy Azure skonfigurowanego w ramach tego samouczka, usuń zasady, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Zabezpieczenia** z menu po lewej stronie.
1. Wybierz **pozycję Dostęp warunkowy**, a następnie wybierz utworzone zasady, takie jak *Pilot usługi MFA*
1. Wybierz **polecenie Usuń**, a następnie potwierdź, że chcesz usunąć zasady.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono uwierzytelnianie wieloskładnikowe platformy Azure przy użyciu zasad dostępu warunkowego dla wybranej grupy użytkowników. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie zasad dostępu warunkowego w celu włączenia uwierzytelniania wieloskładnikowego platformy Azure dla grupy użytkowników usługi Azure AD
> * Konfigurowanie warunków zasad monitujących o uwierzytelnianie usługi MFA
> * Testowanie procesu usługi MFA jako użytkownika

> [!div class="nextstepaction"]
> [Włącz zapisywanie hasła w celu samodzielnego resetowania hasła (SSPR)](tutorial-enable-writeback.md)
