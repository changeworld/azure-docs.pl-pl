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
ms.openlocfilehash: eb37fe247901b799a845ce75723a4a6b535cbb28
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422583"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji z galerii usługi Azure AD

Po dodaniu aplikacji z [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)możesz wybrać, jak użytkownicy mają logować się do tej aplikacji. Wybór można skonfigurować w dowolnym momencie, wybierając element nawigacji **logowania** jednokrotnego w aplikacji przedsiębiorstwa w [Azure Portal](https://portal.azure.com/).

Jedną z dostępnych metod logowania jednokrotnego jest opcja [logowania](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) jednokrotnego opartego na hasłach. Jest to doskonały sposób, aby szybko rozpocząć Integrowanie aplikacji z usługą Azure AD, dzięki czemu można:

-   Włączanie **logowania jednokrotnego dla użytkowników** przez bezpieczne przechowywanie i odtwarzanie nazw użytkownika i haseł dla aplikacji zintegrowanej z usługą Azure AD

-   **Obsługa aplikacji, które wymagają wielu pól logowania** dla aplikacji, które wymagają więcej niż tylko nazwy użytkownika i hasła do logowania

-   **Dostosuj etykiety** pól wprowadzania nazwy użytkownika i hasła, które użytkownicy widzą w [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) po wprowadzeniu poświadczeń

-   Zezwól **użytkownikom** na udostępnianie własnych nazw użytkowników i haseł dla wszystkich istniejących kont aplikacji, które są wpisywane ręcznie w [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Zezwól **członkowi grupy biznesowej** na określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji samoobsługowego [dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Zezwalaj **administratorowi** na określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji aktualizacji poświadczeń podczas przypisywania [użytkownika do aplikacji](#assign-a-user-to-an-application-directly)

-   Zezwalaj **administratorowi** na określenie udostępnionej nazwy użytkownika lub hasła używanego przez grupę osób przy użyciu funkcji aktualizacji poświadczeń podczas przypisywania [grupy do aplikacji](#assign-an-application-to-a-group-directly)

W poniższej sekcji opisano sposób włączania [logowania](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) jednokrotnego opartego na hasłach w aplikacji, która znajduje się już w [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-steps-required"></a>Przegląd czynności wymaganych
Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

-   Przypisywanie aplikacji do użytkownika lub grupy

    -   [Przypisywanie użytkownika do aplikacji bezpośrednio](#assign-a-user-to-an-application-directly)

    -   [Bezpośrednie przypisywanie aplikacji do grupy](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub współadministrator

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Kliknij przycisk **Dodaj** , aby dodać aplikację.

Po krótkim czasie zobaczysz okienko Konfiguracja aplikacji.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte na haśle.**

9. [Przypisz użytkowników do aplikacji](#assign-a-user-to-an-application-directly).

10. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

## <a name="assign-a-user-to-an-application-directly"></a>Przypisywanie użytkownika do aplikacji bezpośrednio

Aby bezpośrednio przypisać jednego lub większą liczbę użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

## <a name="assign-an-application-to-a-group-directly"></a>Bezpośrednie przypisywanie aplikacji do grupy

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Nazwa grupy pełną** grupy osób zainteresowanych przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok zdjęcie w profilu lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę**, wpisz inną **pełną nazwę grupy** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po wybraniu grup kliknij **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych grupach.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje w panelu dostępu.

## <a name="next-steps"></a>Następne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
