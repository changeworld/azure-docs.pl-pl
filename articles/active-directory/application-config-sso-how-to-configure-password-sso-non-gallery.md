---
title: Jak skonfigurować hasło rejestracji jednokrotnej dla applicationn z systemem innym niż galerii | Dokumentacja firmy Microsoft
description: Konfigurowanie niestandardowych aplikacji z systemem innym niż galerii dla bezpiecznej opartego na hasłach rejestracji jednokrotnej, gdy nie znajduje się w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: b928d71fbff232ae93dbf3d24517e7efc4cf8bfd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować hasła logowanie jednokrotne dla aplikacji z systemem innym niż galerii

Oprócz opcji znaleziony w galerii aplikacji usługi Azure AD, masz również możliwość dodania **aplikacji z systemem innym niż galerii** gdy aplikacja ma nie ma na liście istnieje. Przy użyciu tej możliwości, możesz dodać dowolnej aplikacji, która już istnieje w danej organizacji lub dowolnej aplikacji innych firm, z którego można korzystać z dostawcy, który nie jest już częścią [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

Po dodaniu aplikacji z systemem innym niż galerii, następnie można skonfigurować pojedynczy metodę logowania jednokrotnego korzysta z tej aplikacji, wybierając **rejestracji jednokrotnej** element nawigacji w aplikacji przedsiębiorstwa w [portaluAzure](https://portal.azure.com/).

Dostępne metod rejestracji jednokrotnej jest [opartego na hasłach rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) opcji. Z **dodania aplikacji z systemem innym niż galerii** doświadczenia, można je zintegrować w dowolnej aplikacji, która renderuje oparty na języku HTML nazwy użytkownika i hasła, wprowadź pola, nawet jeśli nie jest w naszym zestawie wstępnie zintegrowanych aplikacji.

Jest to działania przez stronę oskrobaniu technologii, która jest częścią rozszerzenia Panelu dostępu, które pozwala na automatyczne wykrywanie pól wejściowych użytkownika i hasło, bezpieczne przechowywanie dla swojego wystąpienia określonej aplikacji. Bezpiecznie powtarzania nazwy użytkownika i hasła do tych pól, gdy użytkownik przechodzi do tej aplikacji na panelu dostępu do aplikacji.

Jest to dobry sposób, aby rozpocząć integrowanie dowolnego rodzaju aplikacji usługi Azure AD szybko i umożliwia:

-   Integracja **aplikacje w świecie** z dzierżawy usługi Azure AD, ile renderowania HTML nazwy użytkownika i hasła pola wejściowego

-   Włącz **rejestracji jednokrotnej dla użytkowników** bezpieczne przechowywanie i odtwarzanie nazwy użytkowników i hasła dla aplikacji zostały zintegrowane z usługą Azure AD

-   **Autowykrywanie wprowadzania** pól dla każdej aplikacji i umożliwiają ręcznie wykryć tych pól przy użyciu rozszerzenia przeglądarki panelu dostępu, w przypadku automatycznego wykrywania ich nie znajdzie

-   **Obsługuje aplikacje, które wymagają wielu pól logowania** dla aplikacji, które wymagają więcej niż tylko pola Nazwa użytkownika i hasło do logowania się w

-   **Dostosowywanie etykiet** pól wprowadzania nazwy użytkownika i hasła użytkownicy zobaczą na [panelu dostępu aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) po oni wprowadzić swoje poświadczenia

-   Zezwalaj na Twojej **użytkowników** zapewnienie własne nazwy użytkowników i hasła dla wszystkich istniejących kont aplikacji wpisywania w ręcznie na [Panel dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Zezwalaj na **grupy biznesowej** do określenia nazwy użytkowników i hasła przypisana do użytkownika za pomocą [samoobsługi dostęp do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funkcji

-   Zezwalaj na **administratora** do określenia nazwy użytkowników i hasła przypisana do użytkownika przy użyciu poświadczeń aktualizacji funkcji podczas [przypisanie użytkownika do aplikacji](#_How_to_configure_1)

-   Zezwalaj na **administratora** do określenia udostępnionego nazwy użytkownika i hasło używane przez grupy osób przy użyciu poświadczeń aktualizacji funkcji podczas [przypisanie grupy do aplikacji](#assign-an-application-to-a-group-directly)

W poniższej sekcji opisano, jak można włączyć [opartego na hasłach rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) do dowolnej aplikacji, które można dodać przy użyciu **dodania aplikacji z systemem innym niż galerii** wystąpić.

## <a name="overview-of-steps-required"></a>Omówienie kroków wymaganych

Aby skonfigurować aplikację z galerii Azure AD, która ma być:

-   [Dodawanie aplikacji z systemem innym niż galerii](#add-a-non-gallery-application)

-   [Skonfiguruj aplikację dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

-   [Przypisywanie aplikacji do użytkownika lub grupy](#assign-the-application-to-a-user-or-a-group)

    -   [Bezpośrednio przypisać użytkownika do aplikacji](#assign-a-user-to-an-application-directly)

    -   [Bezpośrednie przypisywanie aplikacji do grupy](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji z systemem innym niż galerii

Aby dodać aplikację z poziomu galerii Azure AD, wykonaj następujące kroki:

1.  Otwórz [portalu Azure](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdującego się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  Kliknij przycisk **Non galerii aplikacji**.

7.  Wprowadź nazwę aplikacji w **nazwa** pola tekstowego. Wybierz **dodać.**

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

## <a name="configure-the-application-for-password-single-sign-on"></a>Skonfiguruj aplikację dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  Wprowadź **adres URL logowania**. Jest to adres URL, których użytkownicy wprowadzić swoją nazwę i hasło do logowania się na. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisywanie użytkowników do aplikacji.

11. Ponadto można też podać poświadczenia w imieniu użytkownika, wybierając wierszy użytkowników i kliknięcie **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń się po uruchomieniu.

12. **Opcjonalnie:** dla określonych aplikacji mediów społecznościowych, takich jak Facebook i Twitter, istnieje również możliwość włączenia automatycznego przerzucania hasła dla aplikacji w wybranej częstotliwości. Aby włączyć ten wybierz **ma usługi Azure AD automatycznie zarządzać tego użytkownika lub hasło grupy** podczas wprowadzania poświadczeń w imieniu użytkownika lub grupy. Następnie wybierz **przerzucania częstotliwość (w tygodniach)**.

## <a name="assign-a-user-to-an-application-directly"></a>Bezpośrednio przypisać użytkownika do aplikacji

Aby przypisać bezpośrednio co najmniej jednego użytkownika do aplikacji, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Aby otworzyć **Dodaj przydziału** okienku, kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **Pełna nazwa** lub **adres e-mail** użytkownika planuje się przypisanie do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Zaznacz pole wyboru obok zdjęcia profilu użytkownika lub logo, aby dodać użytkownika do **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typu w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwania według nazwy lub adres e-mail** polu wyszukiwania, a następnie kliknij przycisk wyboru, aby dodać użytkownika do **wybrane** listy.

13. Po zakończeniu wybierania użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych użytkowników.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

## <a name="assign-an-application-to-a-group-directly"></a>Bezpośrednie przypisywanie aplikacji do grupy

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Aby otworzyć **Dodaj przydziału** okienku, kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **grupy Pełna nazwa** planuje się przypisanie do grupy **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok profilu zdjęcie lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę**, typu w innym **grupy Pełna nazwa** do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania i Kliknij pole wyboru, aby dodać tę grupę do **wybrane** listy.

13. Po wybraniu grup kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienko, aby wybrać rolę można przypisać do wybranych grup.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkowników, dla których wybrano mieć możliwość uruchamiania tych aplikacji w panelu dostępu.

## <a name="next-steps"></a>Kolejne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](active-directory-application-proxy-sso-using-kcd.md)
