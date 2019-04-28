---
title: Podręcznik administratora Atlassian Jira/Confluence — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Podręcznik administratora Atlassian Jira i Confluence za pomocą usługi Azure Active Directory (Azure AD)...
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2f06b884cb1213e9d2cabff4e6e2b97a60339a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935775"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian Jira i Confluence podręczniku administratora usługi Azure Active Directory

## <a name="overview"></a>Omówienie

Usługi Azure Active Directory (Azure AD) logowanie jednokrotne (SSO) wtyczka umożliwia klientom usługi Microsoft Azure AD na potrzeby logowania się do Atlassian Jira i produktów serwerowych Confluence swojego konta firmowego lub szkolnego. Implementuje rejestracji Jednokrotnej opartej na SAML 2.0.

## <a name="how-it-works"></a>Jak to działa

Gdy użytkownik chce zarejestrować się w aplikacji Atlassian Jira lub Confluence, widzą **Zaloguj się przy użyciu usługi Azure AD** przycisk na stronie logowania. Po wybraniu jego są zobowiązani do logowania przy użyciu usługi Azure AD organizacja strony logowania (czyli swoje konto służbowe).

Po uwierzytelnieniu użytkowników należy mogli logować się do aplikacji. Jeśli zostali już uwierzytelnieni przy użyciu Identyfikatora i hasła dla konta firmowego lub szkolnego, a następnie ich bezpośrednio Zaloguj się do aplikacji. 

Logowania działa w usłudze Jira i Confluence. Jeśli użytkownicy są zalogowani do aplikacji programu Jira Confluence jest otwarty w oknie przeglądarki, nie muszą podać poświadczenia dla innych aplikacji. 

Użytkowników można również uzyskać z produktem Atlassian za pośrednictwem Moje aplikacje przy użyciu konta służbowego lub szkolnego. Powinny one podpisane bez pytany o poświadczenia.

> [!NOTE]
> Inicjowanie obsługi użytkowników nie odbywa się za pośrednictwem dodatku typu plug-in.

## <a name="audience"></a>Grupy odbiorców

Administratorzy programu Jira i Confluence można użyć wtyczki do włączenia funkcji logowania jednokrotnego przy użyciu usługi Azure AD.

## <a name="assumptions"></a>Założenia

* Jira i Confluence są obsługujące protokół HTTPS.
* Użytkownicy, zostały już utworzone w usłudze Jira lub Confluence.
* Użytkownicy mają przypisane w usłudze Jira lub Confluence role.
* Administratorzy mają dostęp do informacji wymaganych do skonfigurowania wtyczki.
* Jira lub Confluence jest dostępna spoza do sieci firmowej.
* Wtyczka działa z wersją w lokalnych programu Jira i Confluence.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku typu plug-in, należy zwrócić uwagę na następujące informacje:

* Jira i Confluence są instalowane w wersji Windows 64-bitowych.
* Wersje programu Jira i Confluence są obsługujące protokół HTTPS.
* Jira i Confluence są dostępne w Internecie.
* Poświadczenia administratora zostały spełnione dla programu Jira i Confluence.
* Poświadczenia administratora są spełnione dla usługi Azure AD.
* WebSudo jest wyłączona w usłudze Jira i Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Obsługiwane wersje programu Jira i Confluence

Wtyczka obsługuje następujące wersje programu Jira Confluence:

* Jira Core i oprogramowania: wersja od 6.0 do 7.12
* Jira Service Desk: 3.0.0 lub 3.5.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5.0 do 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Instalacja

Aby zainstalować dodatek typu plug-in, wykonaj następujące kroki:

1. Zaloguj się do swojej Jira lub Confluence wystąpienia jako administrator.

2. Przejdź do konsoli administracyjnej programu Jira/Confluence i wybierz **dodatki**.

3. Z Microsoft Download Center, Pobierz [wtyczkę logowania jednokrotnego SAML firmy Microsoft dla programu Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [wtyczkę logowania jednokrotnego SAML firmy Microsoft dla Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   Odpowiednią wersję dodatku typu plug-in, zostanie wyświetlony w wynikach wyszukiwania.

4. Wybierz wtyczkę, a Universal dodatku typu Plug-in Manager (UPM) instaluje go.

Po zainstalowaniu dodatku typu plug-in pojawia się w **użytkownika zainstalowane dodatki** części **Zarządzanie dodatkami**.

## <a name="plug-in-configuration"></a>Konfiguracja dodatku typu plug-in

Przed rozpoczęciem za pomocą wtyczki, należy skonfigurować. Wybierz pozycję wtyczki **Konfiguruj** przycisk, a następnie podaj szczegóły konfiguracji.

Na poniższej ilustracji przedstawiono ekran konfiguracji w usłudze Jira i Confluence:

![Ekran konfiguracji dodatku typu plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Adres URL metadanych**: Adres URL można uzyskać metadanych federacji z usługi Azure AD.

* **Identyfikatory**: Adres URL usługi Azure AD używa do sprawdzania poprawności źródła żądania. Jest on mapowany **identyfikator** elementu w usłudze Azure AD. Wtyczka automatycznie dziedziczy ten adres URL jako https://*\<domeny: port >*/.

* **Adres URL odpowiedzi**: Adres URL odpowiedzi, inicjuje logowania języka SAML dostawcy tożsamości (IdP). Jest on mapowany **adres URL odpowiedzi** elementu w usłudze Azure AD. Wtyczka automatycznie dziedziczy ten adres URL jako https://*\<domeny: port >*/plugins/servlet/saml/auth.

* **Adres URL logowania**: Adres URL logowania w inicjujący logowania języka SAML dostawcy tożsamości. Jest on mapowany **rejestracji jednokrotnej** elementu w usłudze Azure AD. Wtyczka automatycznie dziedziczy ten adres URL jako https://*\<domeny: port >*/plugins/servlet/saml/auth.

* **Identyfikator dostawcy tożsamości jednostki**: Identyfikator jednostki, która używa dostawcy tożsamości. To pole jest wypełniana po usunięciu adres URL metadanych.

* **Adres URL logowania**: Zaloguj się adres URL z dostawcy tożsamości. To pole jest wypełniane z usługi Azure AD, gdy adres URL metadanych został rozwiązany.

* **Adres URL wylogowania**: Adres URL wylogowania dostawcy tożsamości. To pole jest wypełniane z usługi Azure AD, gdy adres URL metadanych został rozwiązany.

* **Certyfikat X.509**: Certyfikat X.509 dostawcy tożsamości. To pole jest wypełniane z usługi Azure AD, gdy adres URL metadanych został rozwiązany.

* **Nazwa przycisku logowania**: Nazwa logowania przycisk, który organizacja chce, aby użytkownicy, aby wyświetlić na stronie logowania.

* **Lokalizacje identyfikator użytkownika SAML**: Lokalizacja, w którym identyfikator użytkownika w usłudze Jira lub Confluence jest oczekiwany w odpowiedzi SAML. Może być w **NameID** lub w nazwie atrybutu niestandardowego.

* **Atrybut nazwy**: Nazwa atrybutu, gdy jest oczekiwany identyfikator użytkownika.

* **Strona główna Włącz odnajdowanie obszaru**: Zaznaczenie, aby upewnić się, jeśli firma korzysta z usługi Active Directory Federation Services (ADFS) — na podstawie logowania — w.

* **Nazwa domeny**: Nazwa domeny, jeśli logowanie jest usług AD FS, na podstawie.

* **Włącz pojedynczego wylogowanie**: Zaznaczenie, aby upewnić się, jeśli chcesz wylogować się z usługi Azure AD, gdy użytkownik wylogowuje się z programu Jira lub Confluence.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Otrzymujesz wiele błędów certyfikatu**: Zaloguj się do usługi Azure AD i usunąć wiele certyfikatów, które są dostępne dla aplikacji. Upewnij się, że występuje tylko jeden certyfikat.

* **Certyfikat wkrótce wygaśnie w usłudze Azure AD**: Dodatki zajmie się automatyczne Przerzucanie certyfikatu. Gdy certyfikat wkrótce wygaśnie, nowego certyfikatu powinna być oznaczona jako aktywny i nieużywanych certyfikaty powinny zostać usunięte. Gdy użytkownik próbuje zalogować się do programu Jira, w tym scenariuszu wtyczki odczyty i zapisywany nowy certyfikat.

* **Aby wyłączyć WebSudo (Wyłącz sesji bezpiecznego administratora)**:

  * Dla programu Jira administrator bezpiecznej sesji (oznacza to potwierdzenie hasła przed uzyskaniem dostępu do funkcji administracyjnych) są domyślnie włączone. Jeśli chcesz usunąć tę możliwość w wystąpieniu programu Jira, należy wprowadzić następujący wiersz w pliku jira config.properties: `ira.websudo.is.disabled = true`

  * Aby uzyskać Confluence, postępuj zgodnie z instrukcjami [witrynie pomocy technicznej Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pola, które mogą być wypełnione przez adres URL metadanych nie są wprowadzenie uzupełniane**:

  * Sprawdź, czy adres URL jest poprawny. Sprawdź, jeśli zmapowano poprawny identyfikator dzierżawy i aplikacji.

  * Wprowadź adres URL w przeglądarce i zobacz, jeśli zostanie wyświetlony kod XML metadanych federacji.

* **Wystąpił błąd wewnętrzny serwera**: Przejrzyj dzienniki w katalogu dzienników instalacji. Jeśli otrzymujesz błąd, gdy użytkownik próbuje zarejestrować się przy użyciu logowania jednokrotnego usługi Azure AD, możesz udostępniać dzienniki z zespołem pomocy technicznej.

* **Występuje błąd "Nie znaleziono Identyfikatora użytkownika", gdy użytkownik próbuje zarejestrować się w**: Utwórz identyfikator użytkownika w usłudze Jira lub Confluence.

* **Występuje błąd "Nie można odnaleźć aplikacji" w usłudze Azure AD**: Zobacz, czy odpowiedni adres URL jest mapowany do aplikacji w usłudze Azure AD.

* **Potrzebujesz pomocy technicznej**: Dotrzyj do [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zespół odpowiada w 24-48 godzin pracy.

  Może również zgłosić bilet pomocy technicznej w firmie Microsoft za pośrednictwem kanału portalu Azure.

## <a name="plug-in-faq"></a>Wtyczka — często zadawane pytania

Zapoznaj się poniżej — często zadawane pytania, jeśli masz dowolną kwerendę w sprawie tej wtyczki.

### <a name="what-does-the-plug-in-do"></a>Jak działa dodatku typu plug-in?

Dodatek zapewnia możliwość logowania jednokrotnego (SSO) Atlassian Jira (w tym Core programu Jira, Jira Software, pomocy technicznej programu Jira) i Confluence oprogramowania lokalnego. Wtyczka działa z usługą Azure Active Directory (Azure AD) jako dostawcy tożsamości (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Które produkty Atlassian działają wtyczki za pomocą?

Wtyczka działa z programu Jira i Confluence wersji w środowisku lokalnym.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Działa wtyczki jest w wersji chmury?

Nie. Wtyczka obsługuje tylko lokalne wersje programu Jira i Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Które wersje programu Jira i Confluence jest wtyczki pomocy technicznej?

Wtyczka obsługuje następujące wersje:

* Jira Core i oprogramowania: wersja od 6.0 do 7.12
* Jira Service Desk: 3.0.0 lub 3.5.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5.0 do 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Wtyczka jest bezpłatne lub płatne?

Jest bezpłatnym dodatkiem.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Należy o ponowne uruchomienie programu Jira lub Confluence, po czy mogę wdrożyć dodatku typu plug-in?

Ponowne uruchomienie komputera nie jest wymagane. Możesz rozpocząć od razu korzystać do wtyczki.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Jak uzyskać pomoc techniczną dla dodatku typu plug-in?

Można skontaktowanie się z [zespołu integracja logowania jednokrotnego usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) dla pomocy technicznej potrzebnych dla tej wtyczki. Zespół odpowiada w 24-48 godzin pracy.

Może również zgłosić bilet pomocy technicznej w firmie Microsoft za pośrednictwem kanału portalu Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Czy wtyczki prac na komputerze Mac lub systemu Ubuntu instalację programu Jira i Confluence?

Przetestowaliśmy dodatku typu plug-in tylko w 64-bitowej instalacji systemu Windows Server Jira i Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Wykonuje pracę wtyczki za pomocą dostawców tożsamości innych niż Usługa Azure AD?

Nie. Działa tylko z usługą Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Jakie wersje protokołu SAML działają wtyczki za pomocą?

Działa z języka SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Wtyczka robi aprowizacji użytkowników?

Nie. Dodatek zapewnia tylko rejestracji Jednokrotnej opartej na SAML 2.0. Użytkownik będzie musiał można udostępnić w aplikacji przed logowania SSO.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Czy obsługa wtyczki wersje klastra programu Jira i Confluence?

Nie. Wtyczka działa z programu Jira i Confluence wersji w środowisku lokalnym.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Wykonuje pracę wtyczkę z programu Jira i Confluence wersji protokołu HTTP?

Nie. Wtyczka działa przy użyciu tylko urządzenia z włączonym protokołem HTTPS.
