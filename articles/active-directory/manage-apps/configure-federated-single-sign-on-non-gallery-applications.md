---
title: Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii | Dokumentacja firmy Microsoft
description: Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii niestandardowej, którą chcesz zintegrować z usługą Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcb77fe257a1b99525d009a1756a473e7e61a5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291895"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować logowanie jednokrotne dla aplikacji spoza galerii *bez konieczności pisania kodu*, musisz mieć subskrypcję lub Azure AD Premium i aplikacja muszą obsługiwać SAML 2.0. Aby uzyskać więcej informacji o wersji usługi Azure AD, odwiedź stronę [cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Omówienie kroków wymaganych
Poniżej przedstawiono ogólne omówienie kroków wymaganych do skonfigurowania federacyjnego logowania jednokrotnego przy użyciu protokołu SAML 2.0 dla aplikacji spoza galerii (np. niestandardowe).

-   Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (logowanie jednokrotne adresu URL, wystawcy, adres URL wylogowania i certyfikat)

-   Przypisywanie użytkowników do aplikacji

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurowanie logowania jednokrotnego do aplikacji spoza galerii

Aby skonfigurować logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6. Kliknij przycisk **aplikacji spoza galerii** w **Dodaj własną aplikację** sekcji

7. Wprowadź nazwę aplikacji w **nazwa** pola tekstowego.

8. Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

9. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

10. Wybierz **opartej na SAML logowania jednokrotnego** w **tryb** listy rozwijanej.

11. Wprowadź wymagane wartości w **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez dostawcę tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

    2. **Opcjonalnie:** Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez Dostawcę, adres URL logowania jest wymagana wartość.

12. W **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

13. **Opcjonalnie:** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

    Aby dodać atrybut:

    1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu dotyczące sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto zawiera adresy URL platformy Azure AD i certyfikat jest wymagany dla aplikacji.

15. [Przypisywanie użytkowników do aplikacji.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub Dodaj atrybuty użytkowników, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. W obszarze **atrybutów użytkownika** wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej. Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE]
   >Wybrana usługa Azure AD wybierz pozycję formatu NameID atrybutu (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

   Aby dodać atrybut:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane usługi Azure AD lub certyfikat

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartości w kolumnie. W zależności od tego, jakie aplikacji wymagane jest skonfigurowanie logowania jednokrotnego zobaczysz opcję Pobierz kod XML metadanych albo certyfikatu.

Usługa Azure AD zapewnia również adres URL, aby pobrać metadane. Postępuj zgodnie z tego wzorca, aby uzyskać adres URL metadanych specyficznych dla aplikacji: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj następujące czynności:

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

Po krótkim czasie użytkowników, dla których wybrano mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczeń atrybutów SAML, które są wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
