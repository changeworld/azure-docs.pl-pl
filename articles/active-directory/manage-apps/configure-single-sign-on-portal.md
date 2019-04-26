---
title: Konfigurowanie logowania jednokrotnego — Azure Active Directory | Microsoft Docs
description: W tym samouczku użyto witryny Azure Portal do skonfigurowania logowania jednokrotnego opartego na języku SAML dla aplikacji w ramach usługi Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d96799e69e2fdef3a4ffd1a436727e6a58da79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442241"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Samouczek: Konfigurowanie logowania jednokrotnego opartego na języku SAML dla aplikacji w ramach usługi Azure Active Directory

W tym samouczku użyto witryny [Azure Portal](https://portal.azure.com) do skonfigurowania logowania jednokrotnego opartego na języku SAML dla aplikacji w ramach usługi Azure Active Directory (Azure AD). Użyj tego samouczka, jeśli nie jest dostępny [samouczek specyficzny dla aplikacji](../saas-apps/tutorial-list.md). 

W tym samouczku użyto witryny Azure Portal do wykonania następujących zadań:

> [!div class="checklist"]
> * Wybieranie trybu logowania jednokrotnego opartego na języku SAML
> * Konfigurowanie adresów URL i domeny specyficznej dla aplikacji
> * Konfigurowanie atrybutów użytkownika
> * Tworzenie certyfikatu podpisywania SAML
> * Przypisywanie użytkowników do aplikacji
> * Konfigurowanie aplikacji na potrzeby logowania jednokrotnego opartego na języku SAML
> * Testowanie ustawień języka SAML

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Szybki start: dodawanie aplikacji do dzierżawy usługi Azure AD](add-application-portal.md).

2. Poproś z dostawcą aplikacji, aby informacje opisane w [skonfigurować podstawowe opcje SAML](#configure-basic-saml-options).

3. Użyj poza środowiskiem produkcyjnym do testowania kroki opisane w tym samouczku. Jeśli nie masz nieprodukcyjnego środowiska usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

4. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Wybieranie metody logowania jednokrotnego

Po dodaniu aplikacji do dzierżawy usługi Azure AD, możesz przystąpić do konfigurowania logowania jednokrotnego dla aplikacji.

Aby otworzyć ustawienia logowania jednokrotnego, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**. 

2. W obszarze **Zarządzaj** w **usługi Azure Active Directory** panelu nawigacyjnym zostanie wyświetlone, wybierz **aplikacje dla przedsiębiorstw**. Pojawi się losowej próbki aplikacji w dzierżawie usługi Azure AD. 

3. W **typ aplikacji** menu, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**.

4. Wprowadź nazwę aplikacji, dla której chcesz skonfigurować logowanie jednokrotne. Na przykład można wprowadzić **GitHub-test** do konfigurowania aplikacji zostały dodane w [Dodaj aplikację](add-application-portal.md) Szybki Start.  

     ![Zrzut ekranu pokazujący na pasku wyszukiwania w aplikacji.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Wybierz aplikację, dla której chcesz skonfigurować logowanie jednokrotne.

6. W obszarze **Zarządzaj** zaznacz **logowanie jednokrotne**. 

7. Wybierz **SAML** do skonfigurowania logowania jednokrotnego. **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML - Preview** zostanie wyświetlona strona.

## <a name="configure-basic-saml-options"></a>Konfigurowanie podstawowych opcji języka SAML

Aby skonfigurować domenę i adresy URL, wykonaj następujące czynności:

1. Skontaktuj się z dostawcą aplikacji, aby uzyskać poprawne informacje dla następujących ustawień:

    | Ustawienie konfiguracji | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
    |:--|:--|:--|:--|
    | Identyfikator (identyfikator jednostki) | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Jednoznacznie identyfikuje aplikację, dla której logowanie jednokrotne jest konfigurowane. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację.|
    | Adres URL odpowiedzi | Optional (Opcjonalność) | Wymagane | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). |
    | Adres URL logowania | Wymagane | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchomienia aplikacji z usługi Office 365 lub panelu dostępu usługi Azure AD. Jeśli pole pozostanie puste, usługi Azure AD zależy od dostawcy tożsamości, aby rozpocząć logowania jednokrotnego, gdy użytkownik uruchamia aplikację.|
    | Stan przekaźnika | Optional (Opcjonalność) | Optional (Opcjonalność) | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL dla aplikacji. Jednak niektóre aplikacje używają tego pola inaczej. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.
    | Adres URL wylogowywania | Optional (Opcjonalność) | Optional (Opcjonalność) | Używany do wysyłania odpowiedzi wylogowania protokołu SAML do aplikacji.


2. Aby edytować podstawowe opcje konfiguracji SAML, wybierz **Edytuj** ikonę (ołówka) w prawym górnym rogu **podstawową konfigurację protokołu SAML** sekcji.

     ![Konfigurowanie certyfikatów](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. W odpowiednich polach na stronie wprowadź informacji dostarczonych przez dostawców aplikacji w kroku 1.

4. W górnej części strony wybierz **Zapisz**.

## <a name="configure-user-attributes-and-claims"></a>Konfigurowanie atrybutów użytkowników i oświadczeń 

Można kontrolować, jakie informacje o usłudze Azure AD wysyła do aplikacji w tokenie SAML, gdy użytkownik loguje się. Te informacje można kontrolować, konfigurując atrybuty użytkownika. Na przykład można skonfigurować usługi Azure AD do wysłania identyfikator pracownika, poczty e-mail i nazwę użytkownika do aplikacji, gdy użytkownik loguje się. 

Te atrybuty mogą być wymagane do zapewnienia prawidłowego działania logowania jednokrotnego lub opcjonalne. Aby uzyskać więcej informacji, zobacz [samouczek specyficzny dla aplikacji](../saas-apps/tutorial-list.md) lub skontaktuj się z dostawcą aplikacji.

1. Aby edytować atrybutach i oświadczeniach użytkowników, wybierz **Edytuj** ikonę (ołówka) w prawym górnym rogu **atrybutach i oświadczeniach użytkowników** sekcji.

   **Wartość identyfikatora nazwy** została ustawiona za pomocą wartość domyślną *user.principalname*. Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

2. Aby zmodyfikować **wartość identyfikatora nazwy**, wybierz opcję **Edytuj** ikonę (ołówka) **wartość identyfikatora nazwy** pola. Format identyfikatora i źródła, należy wprowadzić odpowiednie zmiany, zgodnie z potrzebami. Gdy skończysz, Zapisz zmiany. Aby uzyskać więcej informacji na temat dostosowywania oświadczeń, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](../develop/active-directory-saml-claims-customization.md) instrukcje.

3. Do dodawania roszczenia, wybierz **Dodaj nowe oświadczenie** w górnej części strony. Wprowadź **nazwa** i wybierz odpowiedni źródłowy. Jeśli wybierzesz **atrybut** źródło, musisz wybrać **atrybut źródłowy** chcesz użyć. Jeśli wybierzesz **tłumaczenia** źródło, musisz wybrać **przekształcania** i **parametr 1** chcesz użyć.

4. Wybierz pozycję **Zapisz**. Nowe oświadczenie pojawia się w tabeli.
 
## <a name="generate-a-saml-signing-certificate"></a>Generuj certyfikat podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów języka SAML, które wysyła do aplikacji. 

1. Aby wygenerować nowy certyfikat, wybierz pozycję **Edytuj** ikonę (ołówka) w prawym górnym rogu **certyfikat podpisywania SAML** sekcji.

2. W **certyfikat podpisywania SAML** zaznacz **nowy certyfikat**.

3. W nowym wierszu certyfikat, który pojawia się, należy ustawić **datę wygaśnięcia**. Aby uzyskać więcej informacji na temat dostępnych opcji konfiguracji, zobacz [zaawansowane opcje podpisywanie certyfikatów](certificate-signing-options.md) artykułu.

4. Wybierz **Zapisz** w górnej części **certyfikat podpisywania SAML** sekcji. 

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

To dobry pomysł, aby przetestować logowanie jednokrotne za pomocą kilku użytkowników lub grup przed zarządzeniem aplikacji dla Twojej organizacji.

> [!NOTE]
>
> Poniższe kroki to przejście do **użytkowników i grup** sekcji konfiguracji w portalu. Gdy skończysz, musisz przejść z powrotem do **logowanie jednokrotne** sekcji do ukończenia tego samouczka.

Aby przypisać użytkownika lub grupę do aplikacji, wykonaj następujące czynności:

1. Otwórz aplikację w portalu, jeśli jeszcze nie jest otwarta.
2. W lewym panelu nawigacyjnym dla aplikacji, wybierz **użytkowników i grup**.
3. Wybierz przycisk **Dodaj użytkownika**.
4. W **Dodaj przydziału** zaznacz **użytkowników i grup**.
5. Aby znaleźć konkretnego użytkownika, wpisz nazwę użytkownika w **Wybierz członka lub Zaproś użytkownika zewnętrznego** pole. Następnie wybierz logo lub zdjęcie w profilu użytkownika, a następnie wybierz **wybierz**. 
6. W **Dodaj przydziału** zaznacz **przypisać**. Po zakończeniu wybranych użytkowników są wyświetlane w **użytkowników i grup** listy.

## <a name="set-up-the-application-to-use-azure-ad"></a>Konfigurowanie aplikacji do korzystania z usługi Azure AD

To już prawie koniec.  W ostatnim kroku musisz skonfigurować aplikację do używania usługi Azure AD jako dostawcy tożsamości SAML. 

1. Przewiń w dół do **Konfigurowanie <applicationName>**  sekcji. W tym samouczku jest wywoływana w tej sekcji **Konfigurowanie testów GitHub**. 
2. Skopiuj wartości z każdego wiersza w tej sekcji. Następnie wklej każdej wartości na odpowiedni wiersz **podstawową konfigurację protokołu SAML** sekcji. Na przykład skopiuj **adres URL logowania** wartość z **Konfigurowanie testów GitHub** sekcji i wklej go w **na adres URL logowania** pole **podstawową konfigurację protokołu SAML**  sekcji i tak dalej.
3. Podczas wszystkich wartości wklejony do odpowiednich pól, zaznacz **Zapisz**.

## <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Możesz przetestować ustawienia.  

1. Otwórz ustawienia logowania jednokrotnego dla aplikacji. 
2. Przewiń do **weryfikowanie logowania jednokrotnego przy użyciu <applicationName>**  sekcji. W tym samouczku jest wywoływana w tej sekcji **Konfigurowanie testów GitHub**.
3. Wybierz **testu**. Zostaną wyświetlone opcje testowania.
4. Wybierz **Zaloguj się jako bieżący użytkownik**. Dzięki temu testowi możesz najpierw sprawdzić, czy logowanie jednokrotne działa w Twoim przypadku, czyli administratora.

Jeśli wystąpi błąd, zostanie wyświetlony komunikat o błędzie. Wykonaj następujące czynności:

1. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?**.

    ![Uzyskiwanie wskazówek dotyczących rozwiązywania](media/configure-single-sign-on-portal/error-guidance.png)

2. Wybierz **zapoznaj się ze wskazówkami rozpoznawania**. Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.

3. Przeczytaj wskazówki dotyczące rozwiązywania, a następnie, jeśli to możliwe, rozwiązać ten problem.

4. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku skonfigurowano ustawienia logowania jednokrotnego dla aplikacji. Po zakończeniu konfiguracji przypisano użytkownika do aplikacji i skonfigurowano aplikację do używania logowania jednokrotnego opartego na języku SAML. Po wykonaniu tych wszystkich zadań zweryfikowano, że logowanie na podstawie protokołu SAML działa prawidłowo.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Wybranie SAML dla trybu logowania jednokrotnego
> * Skontaktowanie się z dostawcą aplikacji w celu skonfigurowania domen i adresów URL
> * Skonfigurowanie atrybutów użytkowników
> * Utworzono certyfikat podpisywania SAML
> * Ręcznie przypisano użytkowników lub grupy do aplikacji
> * Skonfigurowano aplikację do korzystania z usługi Azure AD jako dostawcy tożsamości SAML
> * Przetestowano logowanie jednokrotne oparte na języku SAML

Aby wdrożyć aplikację do większej liczby użytkowników w Twojej organizacji, należy użyć automatyczna aprowizacja użytkowników.

> [!div class="nextstepaction"]
> [Dowiedz się, jak przypisać użytkowników za pomocą automatycznej aprowizacji](configure-automatic-user-provisioning-portal.md)


