---
title: Konfigurowanie logowania jednokrotnego — Azure Active Directory | Microsoft Docs
description: W tym samouczku użyto witryny Azure Portal do skonfigurowania logowania jednokrotnego opartego na języku SAML dla aplikacji w ramach usługi Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 12/06/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 9da66bc82de1b8beeacadf3a38dda11cf0b2f2e1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170797"
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

2. Poproś dostawcę aplikacji o informacje opisane w artykule [Konfigurowanie domen i adresów URL](#configure-domain-and-urls).

3. Aby przetestować kroki opisane w tym samouczku, zalecamy użycie środowiska innego niż produkcyjne. Jeśli nie masz nieprodukcyjnego środowiska usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

4. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Wybieranie metody logowania jednokrotnego

Po dodaniu aplikacji do dzierżawy usługi Azure AD możesz przystąpić do konfigurowania logowania jednokrotnego dla aplikacji.

Aby otworzyć ustawienia logowania jednokrotnego, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com) w panelu nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory**. 

2. W bloku **Azure Active Directory** kliknij pozycję **Aplikacje dla przedsiębiorstw**. Zostanie otwarty blok **Wszystkie aplikacje** zawierający losowy przykład aplikacji w dzierżawie usługi Azure AD. 

3. W menu **Typ aplikacji** wybierz pozycję **Wszystkie aplikacje** i kliknij pozycję **Zastosuj**.

4. Wprowadź nazwę aplikacji, dla której chcesz skonfigurować logowanie jednokrotne. Wybierz własną aplikację lub wprowadź **GitHub-test**, aby skonfigurować aplikację dodaną w przewodniku szybkiego startu opisującym [dodawanie aplikacji](add-application-portal.md).

5. Kliknij pozycję **Logowanie jednokrotne**. W obszarze **Tryb logowania jednokrotnego** jako opcja domyślna jest wyświetlane **Logowanie na podstawie protokołu SAML**. 

    ![Opcje konfiguracji](media/configure-single-sign-on-portal/config-options.png)

6. Kliknij pozycję **Zapisz** w górnej części bloku. 

## <a name="configure-domain-and-urls"></a>Konfigurowanie domeny i adresów URL

Aby skonfigurować domenę i adresy URL, wykonaj następujące czynności:

1. Skontaktuj się z dostawcą aplikacji, aby uzyskać poprawne informacje dla następujących ustawień:

    | Ustawienie konfiguracji | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
    |:--|:--|:--|:--|
    | Adres URL logowania | Wymagany | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchomienia aplikacji z usługi Office 365 lub panelu dostępu usługi Azure AD. Jeśli adres pozostanie pusty, usługa Azure AD będzie bazowała na dostawcy tożsamości w celu zainicjowania logowania jednokrotnego, gdy użytkownik uruchamia aplikację.|
    | Identyfikator (identyfikator jednostki) | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Jednoznacznie identyfikuje aplikację, dla której logowanie jednokrotne jest konfigurowane. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację.|
    | Adres URL odpowiedzi | Optional (Opcjonalność) | Wymagany | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). |
    | Stan przekaźnika | Optional (Opcjonalność) | Optional (Opcjonalność) | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zwykle ta wartość jest prawidłowym adresem URL dla aplikacji, ale niektóre aplikacje wykorzystują to pole w inny sposób. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.

2. Wprowadź informacje. Aby wyświetlić wszystkie ustawienia, kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL**.

    ![Opcje konfiguracji](media/configure-single-sign-on-portal/config-urls.png)

3. Kliknij pozycję **Zapisz** w górnej części bloku.

4. W tej sekcji znajduje się przycisk **Testuj ustawienia języka SAML**. Uruchom ten test w dalszej części samouczka w sekcji [Testowanie logowania jednokrotnego](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Konfigurowanie atrybutów użytkownika

Atrybuty użytkownika umożliwiają kontrolowanie, jakie informacje usługa Azure AD wysyła do aplikacji w tokenie SAML podczas każdorazowego logowania użytkownika. Na przykład usługa Azure AD może wysłać nazwę, adres e-mail i identyfikator użytkownika do aplikacji. 

Te atrybuty mogą być wymagane do zapewnienia prawidłowego działania logowania jednokrotnego lub opcjonalne. Aby uzyskać więcej informacji, zobacz [samouczek specyficzny dla aplikacji](../saas-apps/tutorial-list.md) lub skontaktuj się z dostawcą aplikacji.

1. Aby wyświetlić wszystkie opcje, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika**.

    ![Konfigurowanie atrybutów użytkownika](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Wprowadź **identyfikator użytkownika**.

    Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

3. Aby uzyskać więcej atrybutów tokenu języka SAML, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika**.

4. Aby dodać atrybut do **atrybutów tokenu języka SAML**, kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z menu.

5. Kliknij pozycję **Zapisz**. Nowy atrybut zostanie wyświetlony w tabeli.
 
## <a name="create-a-saml-signing-certificate"></a>Tworzenie certyfikatu podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów języka SAML, które wysyła do aplikacji. 

1. Aby wyświetlić wszystkie opcje, kliknij pozycję **Pokaż zaawansowane opcje podpisywania certyfikatu**.

    ![Konfigurowanie certyfikatów](media/configure-single-sign-on-portal/config-certificate.png)

2. Aby skonfigurować certyfikat, kliknij pozycję **Utwórz nowy certyfikat**.

3. W bloku **Tworzenie nowego certyfikatu** ustaw **datę wygaśnięcia** i kliknij pozycję **Zapisz**.

4. Kliknij pozycję **Ustaw nowy certyfikat jako aktywny**.

5. Aby dowiedzieć się więcej, zobacz [Zaawansowane opcje podpisywania certyfikatów](certificate-signing-options.md).

6. Aby zachować zmiany wprowadzone do tej pory, kliknij pozycję **Zapisz** w górnej części bloku **Logowanie jednokrotne**. 

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Firma Microsoft zaleca przetestowanie logowania jednokrotnego z kilkoma użytkownikami lub grupami przed wdrożeniem aplikacji do organizacji.

Aby przypisać użytkownika lub grupę do aplikacji, wykonaj następujące czynności:

1. Otwórz aplikację w portalu, jeśli jeszcze nie jest otwarta.
2. W bloku aplikacji po lewej stronie kliknij pozycję **Użytkownicy i grupy**.
3. Kliknij pozycję **Dodaj użytkownika**.
4. W bloku **Dodawanie przypisania** kliknij pozycję **Użytkownicy i grupy**.
5. Aby znaleźć konkretnego użytkownika, wpisz nazwę użytkownika w polu **Wybierz**, kliknij pole wyboru obok logo lub zdjęcia profilowego użytkownika i kliknij pozycję **Wybierz**. 
6. Znajdź bieżącą nazwę użytkownika i zaznacz ją. Opcjonalnie możesz zaznaczyć więcej użytkowników.
7. W bloku **Dodawanie przypisania** kliknij pozycję **Przypisz**. Po zakończeniu wybrani użytkownicy zostaną wyświetleni na liście **Użytkownicy i grupy**.

## <a name="configure-the-application-to-use-azure-ad"></a>Konfigurowanie aplikacji do korzystania z usługi Azure AD

To już prawie koniec.  W ostatnim kroku należy skonfigurować aplikację do korzystania z usługi Azure AD jako dostawcy tożsamości SAML. 

1. Przewiń do samego dołu bloku **Logowanie jednokrotne** aplikacji. 

    ![Konfigurowanie aplikacji](media/configure-single-sign-on-portal/configure-app.png)

2. Kliknij pozycję **Skonfiguruj aplikację** w portalu i postępuj zgodnie z instrukcjami.
3. Ręcznie utwórz konta użytkowników w aplikacji na potrzeby testowania logowania jednokrotnego. Utwórz konta użytkowników, których przypisano do aplikacji w [poprzedniej sekcji](#assign-users-to-the-application). 

## <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Możesz teraz przetestować swoje ustawienia.  

1. Otwórz ustawienia logowania jednokrotnego dla aplikacji. 
2. Przewiń do sekcji **Konfigurowanie domen i adresów URL**.
2. Kliknij pozycję **Testuj ustawienia języka SAML**. Zostaną wyświetlone opcje testowania.

    ![Opcje testowania logowania jednokrotnego](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Kliknij pozycję **Zaloguj się jako bieżący użytkownik**. Dzięki temu testowi możesz najpierw sprawdzić, czy logowanie jednokrotne działa w Twoim przypadku, czyli administratora.
4. Jeśli wystąpi błąd, zostanie wyświetlony komunikat o błędzie. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?**.

    ![Uzyskiwanie wskazówek dotyczących rozwiązywania](media/configure-single-sign-on-portal/error-guidance.png)

5. Kliknij pozycję**Uzyskaj wskazówki dotyczące rozwiązywania**. Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.

    ![Usuwanie błędu](media/configure-single-sign-on-portal/fix-error.png)

6. Przeczytaj wskazówki dotyczące rozwiązywania, a następnie w razie potrzeby kliknij pozycję **Napraw**.

7. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.



## <a name="next-steps"></a>Następne kroki
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

Aby wdrożyć aplikację dla większej liczby użytkowników w organizacji, zalecamy użycie automatycznej aprowizacji użytkowników.

> [!div class="nextstepaction"]
>[Dowiedz się, jak przypisać użytkowników za pomocą automatycznej aprowizacji](configure-automatic-user-provisioning-portal.md)


