---
title: Problemy z konfigurowaniem federacyjnego przysłowiowa dla aplikacji usługi Azure AD Gallery
description: Rozwiązywanie niektórych typowych problemów, które mogą wystąpić podczas konfigurowania federacyjnego logowania jednokrotnego przy użyciu saml dla aplikacji wymienionych w Galerii aplikacji usługi Azure AD
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
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274655"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem z konfigurowaniem federacyjnego logowania jednokrotnego dla aplikacji usługi Azure AD Gallery

Jeśli wystąpi problem podczas konfigurowania aplikacji. Sprawdź, czy postępuje wszystkie kroki opisane w samouczku dla aplikacji. W konfiguracji aplikacji masz wbudowaną dokumentację dotyczącą konfigurowania aplikacji. Ponadto można uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory, aby](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) uzyskać szczegółowe wskazówki krok po kroku.

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać innego wystąpienia aplikacji

Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:

-   Skonfiguruj unikatowy identyfikator dla drugiego wystąpienia. Nie będzie można skonfigurować tego samego identyfikatora używanego dla pierwszego wystąpienia.

-   Skonfiguruj inny certyfikat niż używany w pierwszym wystąpieniu.

Jeśli aplikacja nie obsługuje żadnego z powyższych. Następnie nie będzie można skonfigurować drugiego wystąpienia.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nie można dodać identyfikatora ani adresu URL odpowiedzi

Jeśli nie możesz skonfigurować identyfikatora lub adresu URL odpowiedzi, upewnij się, że wartości identyfikatora i adresu URL odpowiedzi są zgodne z wzorcami wstępnie skonfigurowanym dla aplikacji.

Aby poznać wzorce wstępnie skonfigurowane dla aplikacji:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.** Przejdź do kroku 7. Jeśli jesteś już w bloku konfiguracji aplikacji na platformie Azure AD.

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Z listy rozwijanej **Tryb** wybierz **opcję Logowanie oparte na SAML.**

9. Przejdź do pola tekstowego **Identyfikator** lub **Odpowiedz adres URL** w sekcji **Domena i adresy URL.**

10. Istnieją trzy sposoby poznania obsługiwanych wzorców dla aplikacji:

    * W polu tekstowym zobaczysz obsługiwany wzorzec(-y) jako przykład zastępczy: *Example:* <https://contoso.com>.

    * Jeśli wzorzec nie jest obsługiwany, podczas próby wprowadzenia wartości w polu tekstowym zostanie wyświetlony czerwony wykrzyknik. Po najechaniu kursorem myszy na czerwony wykrzyknik, zobaczysz obsługiwane wzorce.

    * W samouczku dla aplikacji można również uzyskać informacje o obsługiwanych wzorców. W obszarze **Konfigurowanie usługi Azure AD logowania jednokrotnego** sekcji. Przejdź do kroku, aby skonfigurować wartości w sekcji **Domena i adresy URL.**

Jeśli wartości nie są zgodne z wzorców wstępnie skonfigurowane w usłudze Azure AD. Możesz:

-   Praca z dostawcą aplikacji w celu uzyskania wartości zgodnych ze wzorcem wstępnie skonfigurowanym w usłudze Azure AD

-   Możesz też skontaktować się <aadapprequest@microsoft.com> z zespołem usługi Azure AD lub zostawić komentarz w samouczku, aby poprosić o aktualizację obsługiwanych wzorców dla aplikacji

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format Identyfikator jednostki (identyfikator użytkownika)

Nie będzie można wybrać formatu Identyfikator użytkownika , który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół SAML logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nie można znaleźć metadanych usługi Azure AD, aby zakończyć konfigurację za pomocą aplikacji

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **Saml Podpisywanie certyfikatów,** a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga konfigurowania logowania jednokrotnego, zostanie wyświetlone możliwe pobranie metadanych XML lub certyfikatu.

Usługa Azure AD nie udostępnia adresu URL, aby uzyskać metadane. Metadane można pobrać tylko jako plik XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nie wiem, jak dostosować oświadczenia SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutów SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory,](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
