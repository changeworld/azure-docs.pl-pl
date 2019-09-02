---
title: Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD | Microsoft Docs
description: Jak skonfigurować federacyjne Logowanie jednokrotne dla istniejącej aplikacji w galerii usługi Azure AD oraz jak korzystać z samouczków, aby szybko rozpocząć pracę
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
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207121"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD

Dostępny jest samouczek krok po kroku dotyczący wszystkich aplikacji w Galerii Azure Active Directory (Azure AD), które mają możliwość logowania jednokrotnego w przedsiębiorstwie. Aby uzyskać szczegółowe wskazówki krok po kroku, możesz uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) .

## <a name="overview-of-steps-required"></a>Przegląd czynności wymaganych
Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobierz metadane i certyfikat usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (adres URL logowania, wystawca, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące kroki:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub współadministrator.

2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz "Azure Active Directory" w polu wyszukiwania i wybierz pozycję **Azure Active Directory**.

4.  Wybierz pozycję **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure AD.

5.  Wybierz pozycję **Dodaj** w prawym górnym rogu w okienku **aplikacje dla przedsiębiorstw** .

6.  W polu **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę w polu **Nazwa** .

9.  Wybierz pozycję **Dodaj** , aby dodać aplikację.

Po krótkim czasie powinno być możliwe wyświetlenie okienka konfiguracja aplikacji.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator.

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz "Azure Active Directory" w polu wyszukiwania i wybierz pozycję **Azure Active Directory**.

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie Azure Active Directory.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne z menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte** na protokole SAML z listy rozwijanej **tryb** .

9. Wprowadź wymagane wartości w polu **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowanie jednokrotne zainicjowane przez usługę SP, adres URL logowania to wymagana wartość. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

   2. Aby skonfigurować aplikację jako zainicjowaną przez dostawcy tożsamości Logowanie jednokrotne, adres URL odpowiedzi to wymagana wartość. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

10. **Opcjonalnie**: Wybierz pozycję **Pokaż zaawansowane ustawienia adresu URL** , jeśli chcesz zobaczyć wartości niewymagane.

11. W polu **atrybuty użytkownika**wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** .

12. **Opcjonalnie**: Wybierz pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

    Aby dodać atrybut:
   
    1. Wybierz pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    1. Wybierz przycisk **Save** (Zapisz). Nowy atrybut zostanie wyświetlony w tabeli.

13. Wybierz **pozycję &lt;Konfiguruj nazwę&gt; aplikacji** , aby uzyskać dostęp do dokumentacji dotyczącej sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz wymagane adresy URL metadanych i certyfikat, aby skonfigurować Logowanie jednokrotne za pomocą aplikacji.

14. Wybierz pozycję **Zapisz** , aby zapisać konfigurację.

15. Przypisz użytkowników do aplikacji.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz "Azure Active Directory" w polu wyszukiwania i wybierz pozycję **Azure Active Directory**.

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie Azure Active Directory.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne z menu nawigacji po lewej stronie aplikacji.

8. W sekcji **atrybuty użytkownika** wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** . Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE] 
   >Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zobacz [Protokół SAML logowania](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) jednokrotnego w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, wybierz pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:
  
   1. Wybierz pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Wybierz pozycję **Zapisz**. Nowy atrybut zostanie wyświetlony w tabeli.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane lub certyfikat usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz "Azure Active Directory" w polu wyszukiwania i wybierz pozycję **Azure Active Directory**.

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie Azure Active Directory.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   *  Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje**.

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne z menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie wybierz pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

Usługa Azure AD udostępnia również adres URL do uzyskiwania dostępu do metadanych. Użyj poniższego modelu, aby uzyskać adres URL metadanych charakterystyczny dla aplikacji:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby bezpośrednio przypisać jednego lub większą liczbę użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz "Azure Active Directory" w polu wyszukiwania i wybierz pozycję **Azure Active Directory**.

4. Wybierz pozycję **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie Azure Active Directory.

5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji wybierz pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Wybierz przycisk **Dodaj** znajdujący się na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Wybierz selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, który chcesz przypisać do pola wyszukiwania **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Zaznacz pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalnie**: Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie zaznacz pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników wybierz przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie**: Wybierz selektor **ról** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Wybierz przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłanych do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Następne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)



