---
title: Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii
description: Jak skonfigurować federacyjne logowanie jednokrotne dla niestandardowej aplikacji nieszercjowej, którą chcesz zintegrować z usługą Azure AD
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
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274596"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować logowanie jednokrotne dla aplikacji spoza galerii *bez pisania kodu,* musisz mieć subskrypcję lub usługę Azure AD Premium, a aplikacja musi obsługiwać SAML 2.0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, odwiedź stronę [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Omówienie wymaganych kroków
Poniżej znajduje się ogólny przegląd kroków wymaganych do skonfigurowania federacyjnego logowania jednokrotnego za pomocą SAML 2.0 dla aplikacji niezwiązanych z galerią (np. niestandardowej).

-   Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)

-   [Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych i certyfikatów usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (Logowanie do adresu URL, wystawca, adres URL wylogowania i certyfikat)

-   Przypisywanie użytkowników do aplikacji

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurowanie logowania jednokrotnego w aplikacjach innych niż galeria

Aby skonfigurować logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii usługi Azure AD, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6. kliknij pozycję **Aplikacja spoza galerii** w sekcji **Dodawanie własnej aplikacji**

7. Wprowadź nazwę aplikacji w polach tekstowych **Nazwa.**

8. Kliknij przycisk **Dodaj,** aby dodać aplikację.

9. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

10. Wybierz **pozycję Logowanie oparte na SAML w** menu rozwijanym **Tryb.**

11. Wprowadź wymagane wartości w **obszarze Domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako wpisałem SSO inicjowanym przez IdP, wprowadź adres URL odpowiedzi i identyfikator.

    2. **Opcjonalnie:** Aby skonfigurować aplikację jako logowania sytego inicjowane jako spso, sign-on URL jest to wartość wymagana.

12. W **atrybutach Użytkownik**wybierz unikatowy identyfikator dla użytkowników w rozwijaniu **Identyfikator użytkownika.**

13. **Opcjonalnie:** kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij **pozycję &lt;Konfiguruj&gt; nazwę aplikacji,** aby uzyskać dostęp do dokumentacji dotyczącej konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL usługi Azure AD i certyfikat wymagany dla aplikacji.

15. [Przypisz użytkowników do aplikacji.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. W sekcji **Atrybuty użytkownika** wybierz unikatowy identyfikator użytkowników w rozwijaniu **Identyfikator użytkownika.** Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji, aby uwierzytelnić użytkownika.

   >[!NOTE]
   >Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół saml logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych lub certyfikatu usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **Saml Podpisywanie certyfikatów,** a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga konfigurowania logowania jednokrotnego, zostanie wyświetlone możliwe pobranie metadanych XML lub certyfikatu.

Usługa Azure AD udostępnia również adres URL, aby uzyskać metadane. Postępuj zgodnie z tym wzorcem, `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`aby uzyskać adres URL metadanych specyficzny dla aplikacji: .

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy,** aby otworzyć okienko **Dodaj przypisanie.**

9. kliknij selektor **Użytkownicy i grupy** w okienku **Dodawanie przypisania.**

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego jesteś zainteresowany, aby przypisać do pola wyszukiwania według nazwy lub adresu **e-mail.**

11. Umieść wskaźnik myszy na **użytkowniku** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo użytkownika, aby dodać go do **wybranej** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika,** wpisz inne imię i **nazwisko** lub **adres e-mail** w polu wyszukiwania według nazwy lub adresu **e-mail,** a następnie kliknij to pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych użytkowników.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrani użytkownicy będą mogli uruchamiać te aplikacje przy użyciu metod opisanych w sekcji opis rozwiązania.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłanych do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutów SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory,](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
