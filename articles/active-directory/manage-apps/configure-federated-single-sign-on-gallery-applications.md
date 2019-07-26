---
title: Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD | Microsoft Docs
description: Jak skonfigurować federacyjne Logowanie jednokrotne dla istniejącej aplikacji w galerii usługi Azure AD i korzystać z samouczków, aby szybko rozpocząć pracę
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
ms.openlocfilehash: bb5d2c693047dd0aa53430ba531dfd246cc77be9
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422530"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD

Wszystkie aplikacje w galerii usługi Azure AD z włączoną funkcją logowania jednokrotnego dla firm mają dostępny samouczek krok po kroku. Aby uzyskać szczegółowe wskazówki krok po kroku, możesz uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) .

## <a name="overview-of-steps-required"></a>Przegląd czynności wymaganych
Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobierz metadane i certyfikat usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (adres URL logowania, wystawca, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub współadministrator

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Kliknij przycisk **Dodaj** , aby dodać aplikację.

Po krótkim czasie będzie możliwe wyświetlenie okienka konfiguracja aplikacji.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator .

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte** na protokole SAML z listy rozwijanej **tryb** .

9. Wprowadź wymagane wartości w polu **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowanie jednokrotne zainicjowane przez usługę SP, adres URL logowania to wymagana wartość. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

   2. Aby skonfigurować aplikację jako zainicjowaną przez dostawcy tożsamości Logowanie jednokrotne, adres URL odpowiedzi to wymagana wartość. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

10. **Opcjonalne:** aby wyświetlić wartości niewymagane, kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL** .

11. W polu **atrybuty użytkownika**wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** .

12. **Opcjonalnie:** kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

    Aby dodać atrybut:
   
    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    1. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij **pozycję &lt;Konfiguruj nazwę&gt; aplikacji** , aby uzyskać dostęp do dokumentacji dotyczącej sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL metadanych i certyfikat wymagany do skonfigurowania logowania jednokrotnego za pomocą aplikacji.

14. Kliknij przycisk **Zapisz** , aby zapisać konfigurację.

15. Przypisz użytkowników do aplikacji.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. W sekcji **atrybuty użytkownika** wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** . Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE] 
   >Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:
  
   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij polecenie **Zapisz**. Nowy atrybut zostanie wyświetlony w tabeli.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane lub certyfikat usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   *  Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje**.

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

Usługa Azure AD udostępnia również adres URL umożliwiający pobieranie metadanych. Postępuj zgodnie z tym wzorcem, aby uzyskać adres URL metadanych charakterystyczny dla aplikacji:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj następujące czynności:

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

Po krótkim czasie użytkowników, dla których wybrano mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłanych do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)



