---
title: Jak skonfigurować hasło logowania jednokrotnego dla aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak skonfigurować aplikację pod kątem bezpiecznej opartego na hasłach logowanie jednokrotne, gdy jest ono już wyświetlane w galerii aplikacji usługi Azure AD
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
ms.openlocfilehash: d53dbcddf2025ddc3111bc585655e2c157844b18
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190169"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować hasło logowania jednokrotnego dla aplikacji galerii usługi Azure AD

Po dodaniu aplikacji za pomocą [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), masz do wyboru, w jaki sposób należy użytkownikom logować się do tej aplikacji. Ten wybór można skonfigurować w dowolnym momencie, wybierając **logowania jednokrotnego** element nawigacji w aplikacji dla przedsiębiorstw w [witryny Azure portal](https://portal.azure.com/).

Jednym z pojedynczego logowania jednokrotnego metody dostępne dla użytkownika jest [opartego na hasłach logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) opcji. Jest doskonałym sposobem na rozpoczęcie pracy, szybkie Integrowanie aplikacji w usłudze Azure AD i pozwala na:

-   Włącz **logowania jednokrotnego dla użytkowników** przez bezpieczne przechowywanie i odtwarzanie nazwy użytkowników i hasła dla aplikacji zostały zintegrowane z usługą Azure AD

-   **Obsługuje aplikacje, które wymagają wielu pól logowania** dla aplikacji, które wymagają więcej niż tylko pola nazwy użytkownika i hasła, aby zarejestrować się w

-   **Dostosowywanie etykiet** pól danych wejściowych użytkownika i hasło użytkownicy zobaczą na [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) po użytkownik podał poświadczeń

-   Zezwalaj na Twoje **użytkowników** zapewnienie własnej nazwy użytkowników i hasła dla wszystkich istniejących kont aplikacji wpisywania w ręcznie na [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Zezwalaj na **grupy biznesowej** do określenia nazwy użytkowników i haseł przypisane do użytkownika za pomocą [samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funkcji

-   Zezwalaj na **administratora** do określenia nazwy użytkowników i haseł przypisane do użytkownika za pomocą poświadczeń aktualizacji funkcji podczas [przypisanie użytkownika do aplikacji](#assign-a-user-to-an-application-directly)

-   Zezwalaj na **administratora** do określenia udostępnionego nazwy użytkownika i hasło używane przez grupę osób przy użyciu poświadczeń aktualizacji funkcji podczas [przypisanie grupy do aplikacji](#assign-an-application-to-a-group-directly)

W poniższej sekcji opisano, jak umożliwić [opartego na hasłach logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) do aplikacji, która znajduje się już w [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-steps-required"></a>Omówienie kroków wymaganych
Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

-   Przypisz aplikację do użytkownika lub grupy

    -   [Bezpośrednio przypisać użytkownika do aplikacji](#assign-a-user-to-an-application-directly)

    -   [Bezpośrednie przypisywanie aplikacji do grupy](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  W **wprowadź nazwę** pole tekstowe z **Dodaj z galerii** sekcji, wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

8.  Przed dodaniem aplikacji, można zmienić jego nazwę, z **nazwa** pola tekstowego.

9.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

Po krótkim czasie możesz zobaczyć okienko konfiguracji aplikacji.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9. [Przypisywanie użytkowników do aplikacji](#assign-a-user-to-an-application-directly).

10. Ponadto, możesz także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń, się po uruchomieniu.

## <a name="assign-a-user-to-an-application-directly"></a>Bezpośrednio przypisać użytkownika do aplikacji

Aby przypisać jeden lub więcej użytkowników aplikacji bezpośrednio, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

## <a name="assign-an-application-to-a-group-directly"></a>Bezpośrednie przypisywanie aplikacji do grupy

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Nazwa grupy pełną** grupy osób zainteresowanych przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok zdjęcie w profilu lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednej grupy**, typ w innym **Nazwa grupy pełną** do **wyszukiwanie według nazwy lub adresu e-mail** polu wyszukiwania, a następnie kliknij pole wyboru, aby dodać tę grupę Aby **wybrane** listy.

13. Po wybraniu grup kliknij **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych grupach.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkowników, dla których wybrano można uruchamiać te aplikacje w panelu dostępu.

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
