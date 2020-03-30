---
title: Przewodnik administratora atlassian Jira/Confluence — usługa Azure Active Directory| Dokumenty firmy Microsoft
description: Przewodnik administratora do korzystania z atlasu Jira i confluence z usługi Azure Active Directory (Azure AD).Administrator przewodnik do korzystania z Atlassian Jira i Confluence with Azure Active Directory (Azure AD).Administrator przewodnik do korzystania z atlassian Jira i Confluence with Azure Active Directory (Azure AD).Administrator
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161196"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Przewodnik administracyjny atlassian Jira i confluence dla usługi Azure Active Directory

## <a name="overview"></a>Omówienie

Wtyczka logowania jednokrotnego usługi Azure Active Directory (Azure AD) umożliwia klientom usługi Microsoft Azure AD korzystanie z konta służbowego w celu zalogowania się do produktów opartych na systemie Atlassian Jira i Confluence Server. Implementuje samoso oparte na SAML 2.0.

## <a name="how-it-works"></a>Jak to działa

Gdy użytkownicy chcą zalogować się do aplikacji Atlassian Jira lub Confluence, **zobaczą przycisk Zaloguj się za pomocą usługi Azure AD** na stronie logowania. Po wybraniu go, są one wymagane do logowania przy użyciu usługi Azure AD organizacji strony logowania (czyli ich konta służbowego).

Po uwierzytelnienia użytkowników powinny mieć możliwość logowania się do aplikacji. Jeśli są już uwierzytelnione przy użyciu identyfikatora i hasła do konta służbowego lub szkolnego, logują się bezpośrednio do aplikacji. 

Logowanie działa w jira i confluence. Jeśli użytkownicy są zalogowani do aplikacji Jira i Confluence jest otwarty w tym samym oknie przeglądarki, nie trzeba podać poświadczenia dla innej aplikacji. 

Użytkownicy mogą również uzyskać dostęp do produktu Atlassian za pośrednictwem aplikacji Moje aplikacje na koncie służbowym lub szkolnym. Należy je zalogować bez monitu o podanie poświadczeń.

> [!NOTE]
> Inicjowanie obsługi administracyjnej użytkownika nie jest wykonywane za pośrednictwem wtyczki.

## <a name="audience"></a>Grupy odbiorców

Administratorzy Jira i Confluence mogą używać dodatku plug-in do włączania usługi SSO przy użyciu usługi Azure AD.

## <a name="assumptions"></a>Założenia

* Wystąpienia Jira i Confluence są włączone HTTPS.
* Użytkownicy są już tworzone w Jira lub Confluence.
* Użytkownicy mają przypisane role w Jira lub Confluence.
* Administratorzy mają dostęp do informacji wymaganych do skonfigurowania wtyczki.
* Jira lub Confluence jest dostępny poza siecią firmy, jak również.
* Wtyczka działa tylko z lokalną wersją Jira i Confluence.

## <a name="prerequisites"></a>Wymagania wstępne

Zanotuj następujące informacje przed zainstalowaniem wtyczki:

* Jira i Confluence są instalowane w wersji 64-bitowej systemu Windows.
* Wersje Jira i Confluence są włączone HTTPS.
* Jira i Confluence są dostępne w Internecie.
* Poświadczenia administratora są na miejscu dla Jira i Confluence.
* Poświadczenia administratora są na miejscu dla usługi Azure AD.
* WebSudo jest wyłączona w Jira i Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Obsługiwane wersje Jira i Confluence

Wtyczka obsługuje następujące wersje Jira i Confluence:

* Jira Core i oprogramowanie: 6.0 do 7.12
* Jira Service Desk: 3.0.0 do 3.5.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Zbieg: 5,0 do 5,10
* Zbieg: 6.0.1
* Zbieg: 6.1.1
* Zbieg: 6.2.1
* Zbieg: 6.3.4
* Zbieg: 6.4.0
* Zbiegu: 6.5.0
* Zbiegu: 6.6.2
* Zbieg: 6.7.0
* Zbieg: 6.8.1
* Zbieg: 6.9.0
* Zbieg: 6.10.0
* Zbieg: 6.11,0
* Zbieg: 6.12.0

## <a name="installation"></a>Instalacja

Aby zainstalować wtyczkę, wykonaj następujące czynności:

1. Zaloguj się do instancji Jira lub Confluence jako administrator.

2. Przejdź do konsoli administracyjnej Jira/Confluence i wybierz pozycję **Dodatki**.

3. Z Centrum pobierania Microsoft pobierz [wtyczkę SSO microsoft SAML dla wtyczki SSO Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO dla confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   W wynikach wyszukiwania pojawi się odpowiednia wersja wtyczki.

4. Wybierz wtyczkę, a uniwersalny menedżer wtyczek (UPM) zainstaluje ją.

Po zainstalowaniu wtyczki pojawi się w sekcji **Dodatki zainstalowane przez użytkownika** w sekcji **Zarządzaj dodatkami**.

## <a name="plug-in-configuration"></a>Konfiguracja wtyczki

Przed rozpoczęciem korzystania z wtyczki należy ją skonfigurować. Wybierz wtyczkę, wybierz przycisk **Konfiguruj** i podaj szczegóły konfiguracji.

Na poniższej ilustracji przedstawiono ekran konfiguracji zarówno w Jira i Confluence:

![Ekran konfiguracji wtyczki](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Adres URL metadanych:** adres URL, aby uzyskać metadane federacji z usługi Azure AD.

* **Identyfikatory:** Adres URL używany przez usługę Azure AD do sprawdzania poprawności źródła żądania. Jest mapowana do **elementu identyfikatora** w usłudze Azure AD. Wtyczka automatycznie wyprowadza ten adres URL jako https://*\<domena:port>*/.

* **Adres URL odpowiedzi:** adres URL odpowiedzi w dostawcy tożsamości (IdP), który inicjuje logowanie SAML. Jest mapowana do elementu **adresu URL odpowiedzi** w usłudze Azure AD. Wtyczka automatycznie wyprowadza ten adres URL jako https://*\<domena:port>*/plugins/servlet/saml/auth.

* **Adres URL logowania:** adres URL logowania w IdP, który inicjuje logowanie SAML. Jest mapowana do elementu **Logowania w** usłudze Azure AD. Wtyczka automatycznie wyprowadza ten adres URL jako https://*\<domena:port>*/plugins/servlet/saml/auth.

* **Identyfikator jednostki IdP:** identyfikator jednostki używany przez twój IdP. To pole jest wypełniane po usunięciu adresu URL metadanych.

* **Adres URL logowania:** adres URL logowania z IdP. To pole jest wypełniane z usługi Azure AD, gdy adres URL metadanych jest rozpoznawany.

* **Adres URL wylogowania:** adres URL wylogowania z IdP. To pole jest wypełniane z usługi Azure AD, gdy adres URL metadanych jest rozpoznawany.

* **Certyfikat X.509:** Certyfikat X.509 twojego IdP. To pole jest wypełniane z usługi Azure AD, gdy adres URL metadanych jest rozpoznawany.

* **Nazwa przycisku logowania:** nazwa przycisku logowania, który organizacja chce, aby użytkownicy mogli zobaczyć na stronie logowania.

* **Lokalizacje identyfikatora użytkownika SAML:** Lokalizacja, w której w odpowiedzi SAML oczekuje się identyfikatora użytkownika Jira lub Confluence. Może to być **nameid** lub w niestandardowej nazwie atrybutu.

* **Nazwa atrybutu:** Nazwa atrybutu, w którym oczekiwano identyfikatora użytkownika.

* **Włącz odnajdowanie obszaru macierzystego:** Wybór, który należy wprowadzić, jeśli firma korzysta z logowania opartego na usługach federacyjnych Active Directory (AD FS).

* **Nazwa domeny:** Nazwa domeny, jeśli loguje się na podstawie usług AD FS.

* **Włącz wylogowanie jednokrotne:** Wybór, aby, jeśli chcesz wylogować się z usługi Azure AD, gdy użytkownik wyloguje się z Jira lub Confluence.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Pojawia się wiele błędów certyfikatów:** Zaloguj się do usługi Azure AD i usuń wiele certyfikatów, które są dostępne dla aplikacji. Upewnij się, że tylko jeden certyfikat jest obecny.

* **Certyfikat wkrótce wygaśnie w usłudze Azure AD:** dodatki zajmują się automatycznym przerzuceniem certyfikatu. Gdy certyfikat wkrótce wygaśnie, nowy certyfikat powinien być oznaczony jako aktywny, a nieużywane certyfikaty powinny zostać usunięte. Gdy użytkownik próbuje zalogować się do Jira w tym scenariuszu, wtyczka pobiera i zapisuje nowy certyfikat.

* **Chcesz wyłączyć WebSudo (wyłączyć bezpieczną sesję administratora)**:

  * W przypadku Jira domyślnie włączono bezpieczne sesje administratora (czyli potwierdzenie hasła przed uzyskaniem dostępu do funkcji administracyjnych). Jeśli chcesz usunąć tę możliwość w wystąpieniu Jira, określ następujący wiersz w pliku jira-config.properties:`ira.websudo.is.disabled = true`

  * W przypadku zbiegu wykonaj czynności opisane w [witrynie pomocy technicznej z confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pola, które mają być wypełniane przez adres URL metadanych, nie są wypełniane:**

  * Sprawdź, czy adres URL jest poprawny. Sprawdź, czy zamapowane jest prawidłowa dzierżawa i identyfikator aplikacji.

  * Wprowadź adres URL w przeglądarce i sprawdź, czy zostanie wyświetlony kod XML metadanych federacji.

* **Wystąpił wewnętrzny błąd serwera:** Przejrzyj dzienniki w katalogu dziennika instalacji. Jeśli pojawia się błąd, gdy użytkownik próbuje zalogować się przy użyciu logowania sytua usługi Azure AD, można udostępnić dzienniki zespołowi pomocy technicznej.

* **Jest błąd "Nie znaleziono identyfikatora użytkownika", gdy użytkownik próbuje się zalogować:** Utwórz identyfikator użytkownika w Jira lub Confluence.

* **W usłudze Azure AD występuje błąd "Nie znaleziono aplikacji"**— sprawdź, czy odpowiedni adres URL jest mapowany do aplikacji w usłudze Azure AD.

* **Potrzebujesz pomocy technicznej:** Skontaktuj się z [zespołem integracji sso usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zespół odpowiada w ciągu 24-48 godzin pracy.

  Możesz również podnieść bilet pomocy technicznej za pośrednictwem firmy Microsoft za pośrednictwem kanału portalu platformy Azure.

## <a name="plug-in-faq"></a>Często zadawane pytania dotyczące wtyczki

Jeśli masz jakieś pytania dotyczące tej wtyczki, zapoznaj się z poniższymi często zadawanymi pytaniami.

### <a name="what-does-the-plug-in-do"></a>Do czego działa wtyczka?

Wtyczka zapewnia funkcję logowania jednokrotnego (SSO) dla oprogramowania atlassian Jira (w tym Jira Core, Jira Software, Jira Service Desk) i confluence w środowisku lokalnym. Wtyczka współpracuje z usługą Azure Active Directory (Azure AD) jako dostawcą tożsamości (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Z którymi produktami atlassian współpracuje wtyczka?

Wtyczka współpracuje z lokalnymi wersjami Jira i Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Czy wtyczka działa w wersjach w chmurze?

Nie. Wtyczka obsługuje tylko lokalne wersje Jira i Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Które wersje Jira i Confluence obsługuje wtyczkę?

Wtyczka obsługuje następujące wersje:

* Jira Core i oprogramowanie: 6.0 do 7.12
* Jira Service Desk: 3.0.0 do 3.5.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Zbieg: 5,0 do 5,10
* Zbieg: 6.0.1
* Zbieg: 6.1.1
* Zbieg: 6.2.1
* Zbieg: 6.3.4
* Zbieg: 6.4.0
* Zbiegu: 6.5.0
* Zbiegu: 6.6.2
* Zbieg: 6.7.0
* Zbieg: 6.8.1
* Zbieg: 6.9.0
* Zbieg: 6.10.0
* Zbieg: 6.11,0
* Zbieg: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Czy wtyczka jest bezpłatna czy płatna?

Jest to darmowy dodatek.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Czy muszę ponownie uruchomić Jira lub Confluence po wdrożeniu wtyczki?

Ponowne uruchomienie nie jest wymagane. Możesz natychmiast rozpocząć korzystanie z wtyczki.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Jak uzyskać pomoc techniczną dla wtyczki?

Możesz skontaktować się z [zespołem integracji sygn.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) Zespół odpowiada w ciągu 24-48 godzin pracy.

Możesz również podnieść bilet pomocy technicznej za pośrednictwem firmy Microsoft za pośrednictwem kanału portalu platformy Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Czy plug-in działa na Mac lub Ubuntu instalacji Jira i Confluence?

Przetestowaliśmy wtyczkę tylko na 64-bitowych instalacjach systemu Windows Server Jira i Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Czy wtyczka działa z identyfikatorami innymi niż usługa Azure AD?

Nie. Działa tylko z usługą Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Z jaką wersją saml współpracuje wtyczka?

Współpracuje z SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Czy wtyczka wykonuje inicjowanie obsługi administracyjnej użytkownika?

Nie. Wtyczka zapewnia tylko samouszce samoso oparte na SAML 2.0. Użytkownik musi być aprowizowana w aplikacji przed logowanie się logowania.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Czy wtyczka obsługuje wersje klastra Jira i Confluence?

Nie. Wtyczka współpracuje z lokalnymi wersjami Jira i Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Czy wtyczka działa z wersjami HTTP Jira i Confluence?

Nie. Wtyczka działa tylko z instalacjami obsługującymi protokół HTTPS.
