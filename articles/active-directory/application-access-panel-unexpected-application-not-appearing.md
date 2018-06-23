---
title: Przypisane aplikacji nie jest wyświetlane w panelu dostępu | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z powodu aplikacji nie jest wyświetlane w panelu dostępu
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviwer: japere
ms.openlocfilehash: e67f64a40d99bdced79f37cd206a048ee60cfbfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333439"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Przypisane aplikacji nie jest wyświetlane w panelu dostępu

Panel dostępu jest portalu sieci web, która pozwala użytkownikom przy użyciu konta służbowego w usłudze Azure Active Directory (Azure AD) do wyświetlania i uruchamiania aplikacji opartej na chmurze, czy administrator usługi Azure AD udzielił im dostępu do. Te aplikacje są skonfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi prawidłowo skonfigurowane i przypisane do użytkownika lub grupy, których użytkownik jest członkiem, aby zobaczyć aplikację w panelu dostępu.

Typ aplikacji, które użytkownik może być widoczny można podzielić na następujące kategorie:

-   Aplikacje pakietu Office 365

-   Aplikacje firmy Microsoft i innych firm skonfigurowaną na podstawie federacyjnego logowania jednokrotnego

-   Aplikacje oparte na hasłach logowania jednokrotnego

-   Aplikacje z istniejącymi rozwiązaniami do logowania jednokrotnego

## <a name="general-issues-to-check-first"></a>Ogólne problemy, aby sprawdzić w pierwszej kolejności

-   Jeśli aplikacja właśnie został dodany do użytkownika, spróbuj zarejestrować i wylogowywanie ponownie do panelu dostępu użytkownika po kilku minutach aby zobaczyć, czy aplikacja jest dodany.

-   Jeśli licencji został właśnie usunięty ze użytkownika lub grupę, których użytkownik jest członkiem to może potrwać dłuższy czas, w zależności od rozmiaru i złożoności grupy zmian wprowadzanych. Zezwalaj na dodatkowy czas przed zalogowaniem się do panelu dostępu.

## <a name="problems-related-to-application-configuration"></a>Problemy związane z konfiguracją aplikacji

Aplikacja może być wyświetlana w panelu dostępu użytkownika, ponieważ nie został prawidłowo skonfigurowany. Poniżej przedstawiono kilka sposobów, można rozwiązywać problemy związane z konfiguracją aplikacji:

-   [Konfigurowanie federacyjnych rejestracji jednokrotnej dla galerii aplikacji usługi Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasła pojedynczej logowania aplikacji dla galerii aplikacji usługi Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasła pojedynczej logowania aplikacji dla aplikacji z systemem innym niż galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurowanie federacyjnych rejestracji jednokrotnej dla galerii aplikacji usługi Azure AD

Wszystkie aplikacje w galerii Azure AD włączone możliwości przedsiębiorstwa rejestracji jednokrotnej ma dostępne samouczek krok po kroku. Dostęp można uzyskać [lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) szczegółowe wskazówki krok po kroku.

Aby skonfigurować aplikację z galerii Azure AD, która ma być:

-   [Dodawanie aplikacji w galerii Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (znak na adres URL odpowiedzi adres URL, identyfikator)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i dodać atrybuty użytkownika mają być wysyłane do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (znak na adres URL, wystawcy, adres URL wylogowania i certyfikatu)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji w galerii Azure AD

Aby dodać aplikację z poziomu galerii Azure AD, wykonaj następujące czynności:

1.  Otwórz [portalu Azure](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdującego się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  W **wprowadź nazwę** pole tekstowe z **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować pod kątem logowania jednokrotnego.

8.  Przed dodaniem aplikacji, można zmienić jego nazwę z **nazwa** pola tekstowego.

9.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie rejestracji jednokrotnej dla aplikacji z galerii Azure AD

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz **na języku SAML logowania jednokrotnego** z **tryb** listy rozwijanej.

9.  Wprowadź wymagane wartości w **domeny i adres URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane SP, zaloguj się na adres URL jest wymagana wartość. Dla pewnych aplikacji identyfikator jest również wymagana wartość.

   2. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane IdP, adres URL odpowiedzi jest wymagana wartość. Dla pewnych aplikacji identyfikator jest również wymagana wartość.

10. **Opcjonalnie:** kliknij **Pokaż zaawansowane ustawienia adresu URL** Jeśli chcesz zobaczyć wartości innych niż wymagana.

11. W **atrybuty użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

12. **Opcjonalnie:** kliknij **widoku i edytować wszystkie atrybuty użytkowników** atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania.

   Aby dodać atrybutu:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

13. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu na temat konfigurowania rejestracji jednokrotnej w aplikacji. Ponadto trzeba metadanych adresy URL i certyfikatu wymaganego do konfigurowania rejestracji Jednokrotnej z aplikacją.

14. Kliknij przycisk **zapisać** Aby zapisać konfigurację.

15. Przypisywanie użytkowników do aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodać atrybuty użytkownika mają być wysyłane do aplikacji

Aby wybrać identyfikator użytkownika lub Dodaj atrybuty użytkownika, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie widzisz aplikacji ma być wyświetlane w tym miejscu, należy użyć **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu  **Wszystkie aplikacje.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  W obszarze **atrybuty użytkownika** wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej. Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE] 
   >Usługi Azure AD wybiera formatu żądany przez aplikację w SAML AuthRequest lub format dla atrybutu NameID (identyfikator użytkownika) oparte na wybranej wartości. Aby uzyskać więcej informacji można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9.  Aby dodać atrybuty użytkownika, kliknij przycisk **widoku i edytować wszystkie atrybuty użytkowników** atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania.

   Aby dodać atrybutu:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych usługi Azure AD lub certyfikat

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartość w kolumnie. W zależności od tego, jakie aplikacji wymaga Konfigurowanie logowania jednokrotnego zobaczysz opcję, aby pobrać metadane XML albo certyfikatu.

    Usługi Azure AD nie udostępnia adresu URL można pobrać metadanych. Można pobrać metadanych jako plik XML.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii

Aby skonfigurować aplikację z systemem innym niż galerii, musisz mieć usługi Azure AD premium, a aplikacja obsługuje SAML 2.0. Aby uzyskać więcej informacji o wersji usługi Azure AD, odwiedź stronę [cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (znak na adres URL odpowiedzi adres URL, identyfikator)](#configuring-single-sign-on)

-   [Wybierz identyfikator użytkownika i dodać atrybuty użytkownika mają być wysyłane do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (znak na adres URL, wystawcy, adres URL wylogowania i certyfikatu)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (znak na adres URL odpowiedzi adres URL, identyfikator)

Aby skonfigurować logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii Azure AD, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdującego się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  Kliknij przycisk **Non galerii aplikacji** w **dodać własną aplikację** sekcji.

7.  Wprowadź nazwę aplikacji w **nazwa** pola tekstowego.

8.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

9.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

10. Wybierz **na języku SAML logowania jednokrotnego** w **tryb** listy rozwijanej.

11. Wprowadź wymagane wartości w **domeny i adres URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako SSO inicjowanych przez dostawców tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

   2.  **Opcjonalnie:** Konfigurowanie aplikacji jako logowanie Jednokrotne zainicjowane SP, zaloguj się na adres URL jest wymagana wartość.

12. W **atrybuty użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

13. **Opcjonalnie:** kliknij **widoku i edytować wszystkie atrybuty użytkowników** atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania.

   Aby dodać atrybutu:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

14. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu na temat konfigurowania rejestracji jednokrotnej w aplikacji. Ponadto trzeba Azure AD adresy URL i certyfikaty wymagane dla aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodać atrybuty użytkownika mają być wysyłane do aplikacji

Aby wybrać identyfikator użytkownika lub Dodaj atrybuty użytkownika, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  W obszarze **atrybuty użytkownika** wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej. Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE] 
   >Usługi Azure AD wybiera formatu żądany przez aplikację w SAML AuthRequest lub format dla atrybutu NameID (identyfikator użytkownika) oparte na wybranej wartości. Aby uzyskać więcej informacji można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9.  Aby dodać atrybuty użytkownika, kliknij przycisk **widoku i edytować wszystkie atrybuty użytkowników** atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania.

   Aby dodać atrybutu:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych usługi Azure AD lub certyfikat

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartość w kolumnie. W zależności od tego, jakie aplikacji wymaga Konfigurowanie logowania jednokrotnego zobaczysz opcję, aby pobrać metadane XML albo certyfikatu.

Usługi Azure AD nie udostępnia adresu URL można pobrać metadanych. Można pobrać metadanych jako plik XML.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować hasło rejestracji jednokrotnej dla galerii aplikacji usługi Azure AD

Aby skonfigurować aplikację z galerii Azure AD, która ma być:

-   [Dodawanie aplikacji w galerii Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Skonfiguruj aplikację dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji w galerii Azure AD

Aby dodać aplikację z poziomu galerii Azure AD, wykonaj następujące czynności:

1.  Otwórz [portalu Azure](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdującego się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  W **wprowadź nazwę** pole tekstowe z **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować pod kątem logowania jednokrotnego.

8.  Przed dodaniem aplikacji, można zmienić jego nazwę z **nazwa** pola tekstowego.

9.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

Po krótkim czasie widoczne okienko konfiguracji aplikacji.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Skonfiguruj aplikację dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  [Przypisywanie użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

10. Ponadto można też podać poświadczenia w imieniu użytkownika, wybierając wierszy użytkowników i kliknięcie **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń się po uruchomieniu.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować hasła logowanie jednokrotne dla aplikacji z systemem innym niż galerii

Aby skonfigurować aplikację z galerii Azure AD, która ma być:

-   [Dodawanie aplikacji z systemem innym niż galerii](#add-a-non-gallery-application)

-   [Skonfiguruj aplikację dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji z systemem innym niż galerii

Aby dodać aplikację z poziomu galerii Azure AD, wykonaj następujące czynności:

1.  Otwórz [portalu Azure](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministrator**.

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdującego się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  Kliknij przycisk **Non galerii aplikacji.**

7.  Wprowadź nazwę aplikacji w **nazwa** pola tekstowego. Wybierz **dodać.**

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Skonfiguruj aplikację dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

    1.  Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  Wprowadź **adres URL logowania**. Jest to adres URL, których użytkownicy wprowadzić swoją nazwę i hasło do logowania się na. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. [Przypisywanie użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

11. Ponadto można też podać poświadczenia w imieniu użytkownika, wybierając wierszy użytkowników i kliknięcie **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń się po uruchomieniu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisywaniem aplikacji dla użytkowników

Użytkownik może nie być widoczny aplikacji na ich Panel dostępu, ponieważ nie są przypisane do aplikacji. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdź, czy użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Jak przypisać użytkownika bezpośrednio do aplikacji](#how-to-assign-a-user-to-an-application-directly)

-   [Sprawdź, czy użytkownik jest przypisany do licencji związanych z aplikacją](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Jak przypisać licencję do użytkownika](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, czy użytkownik jest przypisany do aplikacji

Aby sprawdzić, czy użytkownik jest przypisany do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

6.  **Wyszukiwanie** nazwy w aplikacji.

7.  Kliknij przycisk **użytkowników i grup**.

8.  Sprawdź, czy użytkownika jest przypisany do aplikacji.

   * Jeśli nie, wykonaj kroki opisane w artykule "Jak bezpośrednio przypisać użytkownika do aplikacji" w tym celu.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak przypisać użytkownika bezpośrednio do aplikacji

Aby przypisać bezpośrednio co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego**.

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **Pełna nazwa** lub **adres e-mail** użytkownika planuje się przypisanie do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Zaznacz pole wyboru obok zdjęcia profilu użytkownika lub logo, aby dodać użytkownika do **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typu w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwania według nazwy lub adres e-mail** polu wyszukiwania, a następnie kliknij przycisk wyboru, aby dodać użytkownika do **wybrane** listy.

13. Po zakończeniu wybierania użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych użytkowników.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mieć możliwość uruchamiania tych aplikacji w panelu dostępu.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik jest w ramach licencji, związanych z aplikacją

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

  * Jeśli użytkownik jest przypisany do licencji usługi Office, umożliwia to aplikacjom pierwszej strony pakietu Office są wyświetlane w panelu dostępu użytkownika.

### <a name="how-to-assign-a-user-a-license"></a>Jak przypisać licencję użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalne** kliknij **opcje przydziału** element, aby przypisać częściami produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tego użytkownika.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisywanie aplikacji do grupy

Użytkownik może być widoczny aplikacji na ich Panel dostępu, ponieważ są one częścią grupy przypisanej do aplikacji. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdzanie członkostwa w grupach użytkownika](#check-a-users-group-memberships)

-   [Jak przypisać bezpośrednio do grupy aplikacji](#how-to-assign-an-application-to-a-group-directly)

-   [Sprawdź, czy użytkownik jest częścią grupy przypisane do licencji](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Jak przypisać licencję do grupy](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupach użytkownika

Aby sprawdzić członkostwa w grupie, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup**.

8.  Sprawdź, czy użytkownika jest częścią grupy przypisane do aplikacji.

  * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz opcję Usuń.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Jak przypisać bezpośrednio do grupy aplikacji

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego**.

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy, aby otworzyć **Dodaj przydziału** okienka.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **grupy Pełna nazwa** planuje się przypisanie do grupy **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok profilu zdjęcie lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę**, typu w innym **grupy Pełna nazwa** do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania i Kliknij pole wyboru, aby dodać tę grupę do **wybrane** listy.

13. Po wybraniu grup kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienko, aby wybrać rolę można przypisać do wybranych grup.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkowników, dla których wybrano mieć możliwość uruchamiania tych aplikacji w panelu dostępu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Sprawdź, czy użytkownik jest częścią grupy przypisane do licencji

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup**.

8.  Kliknij wiersz określonej grupy.

9.  Kliknij przycisk **licencji** aby zobaczyć, które licencje grupy został przypisany do niej.

   * Jeśli ta grupa jest przypisana do licencji pakietu Office, to może włączyć określonych aplikacji pierwszej strony pakietu Office na panelu dostępu użytkownika.

### <a name="how-to-assign-a-license-to-a-group"></a>Jak przypisać licencję do grupy

Aby przypisać licencję do grupy, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** dla grupy Użytkownicy zainteresowani i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje grupy został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalne** kliknij **opcje przydziału** element, aby przypisać częściami produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tej grupy. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

>[!NOTE]
>Aby szybciej to zrobić, należy wziąć pod uwagę tymczasowo bezpośrednio przypisywania licencji do użytkownika. 
>
>

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie nowych użytkowników do usługi Azure Active Directory](active-directory-users-create-azure-portal.md)

