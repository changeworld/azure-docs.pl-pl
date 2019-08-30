---
title: Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji z galerii usługi Azure AD | Microsoft Docs
description: Jak skonfigurować aplikację pod kątem bezpiecznego logowania jednokrotnego opartego na hasłach, gdy jest już wymieniona w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146883"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurowanie logowania jednokrotnego przy użyciu hasła dla aplikacji z galerii usługi Azure AD

Po dodaniu aplikacji z [galerii aplikacji Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)możesz wybrać, w jaki sposób użytkownicy mają logować się do tej aplikacji. Wybór można skonfigurować w dowolnym momencie, wybierając pozycję **Logowanie** jednokrotne w aplikacji przedsiębiorstwa w [Azure Portal](https://portal.azure.com/).

Jedną z dostępnych opcji logowania jednokrotnego jest logowanie jednokrotne [oparte](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)na hasłach. Jest to doskonały sposób, aby szybko rozpocząć Integrowanie aplikacji z usługą Azure AD. Oferuje następujące elementy:

-   Bezpieczne przechowywanie i odtwarzanie nazw użytkowników i haseł dla aplikacji zintegrowanej z usługą Azure AD

-   Zapewnia obsługę aplikacji, które wymagają wielu pól logowania poza polami username i Password

-   Umożliwia dostosowanie etykiet pól username i Password, które użytkownicy widzą w [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) po wprowadzeniu poświadczeń

-   Umożliwia użytkownikom udostępnianie własnych nazw użytkowników i haseł dla wszystkich istniejących kont aplikacji, które wprowadzają ręcznie w [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) .

-   Umożliwia członkom grupy biznesowej korzystanie z funkcji samoobsługowego [dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) w celu określenia nazw użytkowników i haseł przypisanych do użytkownika

-   Umożliwia administratorom określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji aktualizacji poświadczeń podczas przypisywania [użytkownika do aplikacji](#assign-a-user-to-an-application-directly) .

-   Umożliwia administratorowi użycie funkcji aktualizacji poświadczeń w celu określenia udostępnionej nazwy użytkownika lub hasła dla grupy osób, gdy [przypisze grupę do aplikacji](#assign-an-application-to-a-group-directly)

W poniższej sekcji opisano, jak można włączyć [Logowanie](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) jednokrotne oparte na hasłach w aplikacji, która znajduje się już w [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-required-steps"></a>Przegląd wymaganych kroków
Aby skonfigurować aplikację z galerii usługi Azure AD, musisz:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

-   Przypisz aplikację do użytkownika lub grupy:

    -   [Przypisywanie użytkownika do aplikacji bezpośrednio](#assign-a-user-to-an-application-directly)

    -   [Bezpośrednie przypisywanie aplikacji do grupy](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [Azure Portal](https://portal.azure.com)i zaloguj się jako **administrator globalny** lub współadministrator.

2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu po lewej stronie.

3.  W polu wyszukiwania wprowadź **usługę Azure Active Directory** , a następnie wybierz element **Azure Active Directory** .

4.  Wybierz pozycję **aplikacje dla przedsiębiorstw** w menu usługi Azure AD po lewej stronie.

5.  Wybierz przycisk **Dodaj** w prawym górnym rogu okienka **aplikacje dla przedsiębiorstw** .

6.  W polu **Wprowadź nazwę** w sekcji **Dodaj z galerii** wprowadź nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do rejestracji jednokrotnej.

8.  Przed dodaniem aplikacji można zmienić jej nazwę w polu **Nazwa** .

9.  Wybierz pozycję **Dodaj** , aby dodać aplikację.

Po krótkim czasie zobaczysz okienko Konfiguracja aplikacji.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator.

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu po lewej stronie.

3. W polu wyszukiwania wprowadź **usługę Azure Active Directory** , a następnie wybierz element **Azure Active Directory** .

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** z menu Azure Active Directory po lewej stronie.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   Jeśli nie widzisz aplikacji, którą chcesz umieścić w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**, a następnie ustaw opcję **Pokaż** na **wszystkie aplikacje**.

6. Wybierz aplikację, którą chcesz skonfigurować do rejestracji jednokrotnej.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne w menu aplikacji po lewej stronie.

8. Wybierz tryb **logowania opartego na hasłach** .

9. [Przypisz użytkowników do aplikacji](#assign-a-user-to-an-application-directly).

10. Możesz również podać poświadczenia w imieniu użytkowników, wybierając wiersz użytkownika, wybierając pozycję **Aktualizuj poświadczenia**, a następnie wprowadzając nazwę użytkownika i hasło. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń podczas uruchamiania aplikacji.

## <a name="assign-a-user-to-an-application-directly"></a>Przypisywanie użytkownika do aplikacji bezpośrednio

Aby bezpośrednio przypisać jednego lub większą liczbę użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu po lewej stronie.

3. W polu wyszukiwania wprowadź **usługę Azure Active Directory** , a następnie wybierz element **Azure Active Directory** .

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** z menu Azure Active Directory po lewej stronie.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   Jeśli nie widzisz aplikacji, którą chcesz umieścić w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**, a następnie ustaw opcję **Pokaż** na **wszystkie aplikacje**.

6. Wybierz aplikację, do której chcesz przypisać użytkownika.

7. Po załadowaniu aplikacji wybierz pozycję **Użytkownicy i grupy** z menu aplikacji po lewej stronie.

8. Wybierz przycisk **Dodaj** znajdujący się na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wprowadź pełną nazwę lub adres e-mail użytkownika w polu **Wyszukaj według nazwy lub adresu e-mail** .

11. Umieść kursor nad użytkownikiem na liście, a następnie zaznacz pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać je do **wybranej** listy.

12. Opcjonalnie: Jeśli chcesz dodać więcej niż jednego użytkownika, wprowadź kolejną pełną nazwę lub adres e-mail w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie zaznacz pole wyboru dla tego użytkownika, aby dodać je do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników Użyj przycisku **Wybierz** , aby dodać je do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. Opcjonalnie: Za pomocą polecenia **Wybierz rolę** w okienku **Dodaj przypisanie** wybierz rolę, która ma zostać przypisana do wybranych użytkowników.

15. Wybierz pozycję **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

## <a name="assign-an-application-to-a-group-directly"></a>Bezpośrednie przypisywanie aplikacji do grupy

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu po lewej stronie.

3. W polu wyszukiwania wprowadź **usługę Azure Active Directory** , a następnie wybierz element **Azure Active Directory** .

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** z menu usługi Azure AD po lewej stronie.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   Jeśli nie widzisz aplikacji, którą chcesz umieścić w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje**.

6. Wybierz aplikację, do której chcesz przypisać użytkownika.

7. Po załadowaniu aplikacji wybierz pozycję **Użytkownicy i grupy** z menu aplikacji po lewej stronie.

8. Wybierz przycisk **Dodaj** znajdujący się u góry listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wprowadź pełną nazwę grupy, którą chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad grupą na liście, a następnie zaznacz pole wyboru obok zdjęcia lub logo profilu grupy, aby dodać grupę do **wybranej** listy.

12. Opcjonalnie: Jeśli chcesz dodać więcej niż jedną grupę, wprowadź kolejną pełną nazwę grupy w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie zaznacz odpowiednie pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po zakończeniu wybierania grup Użyj przycisku **Wybierz** , aby dodać je do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. Opcjonalnie: Za pomocą polecenia **Wybierz rolę** w okienku **Dodaj przypisanie** wybierz rolę, która ma zostać przypisana do wybranych grup.

15. Wybierz pozycję **Przypisz** , aby przypisać aplikację do wybranych grup.

Po krótkim czasie wybrane użytkownicy powinni mieć możliwość uruchamiania tych aplikacji z poziomu panelu dostępu.

## <a name="next-steps"></a>Następne kroki
Zapewniaj Logowanie jednokrotne [do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).
