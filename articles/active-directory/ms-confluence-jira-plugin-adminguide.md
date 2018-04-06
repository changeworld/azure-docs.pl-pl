---
title: Podręcznik administratora programu Azure Active Directory SSO wtyczki | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Jira/zlewiska.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Podręcznik administratora programu Azure Active Directory SSO wtyczki

## <a name="overview"></a>Przegląd

Azure Active Directory (Azure AD) rejestracji jednokrotnej (SSO) wtyczki umożliwia klientom usługi Microsoft Azure AD Użyj swojego konta firmowego lub szkolnego do logowania się na Atlassian Jira i na serwerze zlewiska produktów. Implementuje SAML 2.0 na podstawie logowania jednokrotnego.

## <a name="how-it-works"></a>Jak to działa

Gdy użytkownik chce zalogować się do aplikacji Atlassian Jira lub zlewiska, zostanie wyświetlona **Zaloguj się za pomocą usługi Azure AD** przycisk na stronie logowania. Podczas wybierania go, są one wymagane do Zaloguj się przy użyciu usługi Azure AD organizacja logowania strony (to znaczy swojego konta firmowego lub szkolnego).

Po uwierzytelnieniu, ich należy zalogować się do aplikacji. Jeśli zostali już uwierzytelnieni ID i hasło dla swojego konta firmowego lub szkolnego, a następnie bezpośrednio zalogowaniu się do aplikacji. 

Logowanie działa między Jira i zlewiska. Jeśli użytkownicy są zalogowani do aplikacji Jira zlewiska jest otwarty w oknie przeglądarki, nie posiadają Podaj poświadczenia dla innych aplikacji. 

Użytkownicy można również uzyskać dostęp do produktu Atlassian za pośrednictwem Moje aplikacje przy użyciu konta służbowego. Powinny one podpisane bez ponownego podawania poświadczeń.

> [!NOTE]
> Inicjowanie obsługi użytkowników nie jest realizowane za pomocą wtyczki.

## <a name="audience"></a>Grupy odbiorców

Administratorzy Jira i zlewiska służy wtyczki do włączenia funkcji logowania jednokrotnego przy użyciu usługi Azure AD.

## <a name="assumptions"></a>Wartości domyślne

* Jira i zlewiska wystąpienia są obsługujące protokół HTTPS.
* Użytkownicy, zostały już utworzone w Jira lub zlewiska.
* Użytkownicy mają przypisane w Jira lub zlewiska ról.
* Administratorzy mają dostęp do informacji niezbędnych do skonfigurowania wtyczki.
* Jira lub zlewiska znajduje się poza z siecią firmową.
* Wtyczka współpracuje z wersją w lokalnej Jira i zlewiska.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku typu plug-in, należy uwzględnić następujące informacje:

* Jira i zlewiska są instalowane w Windows 64-bitowej wersji.
* Wersje Jira i zlewiska są obsługujące protokół HTTPS.
* Jira i zlewiska są dostępne w Internecie.
* Poświadczenia administratora są stosowane dla Jira i zlewiska.
* Poświadczenia administratora są stosowane dla usługi Azure AD.
* WebSudo jest wyłączony w Jira i zlewiska.

## <a name="supported-versions-of-jira-and-confluence"></a>Obsługiwane wersje Jira i zlewiska

Wtyczka obsługuje następujące wersje Jira i zlewiska:

* Podstawowe Jira i oprogramowania: 6.0 do 7.2.0
* Jira działu: 3.2 do 3.0
* Zlewiska: 5.10 do 5.0

## <a name="installation"></a>Instalacja

Aby zainstalować wtyczkę, wykonaj następujące kroki:

1. Zaloguj się do wystąpienia Jira lub zlewiska konta administratora.
    
2. Przejdź do konsoli administracyjnej Jira/zlewiska i wybierz **dodatki**.
    
3. Z witryny Atlassian Marketplace, wyszukaj **dodatek logowania jednokrotnego SAML Microsoft**.
 
   Odpowiednią wersję dodatku jest wyświetlana w wynikach wyszukiwania.
 
5. Wybierz wtyczki, a uniwersalnych menedżera wtyczek (UPM) instaluje go.
 
Po zainstalowaniu dodatku typu plug-in pojawia się w **użytkownika zainstalowane dodatki** sekcji **Zarządzanie dodatkami**.
    
## <a name="plug-in-configuration"></a>Konfiguracja wtyczki

Przed rozpoczęciem pracy za pomocą wtyczki, należy go skonfigurować. Wybierz wtyczki, kliknij **Konfiguruj** przycisk i udostępnienia szczegółów konfiguracji.

Na poniższej ilustracji przedstawiono na ekranie konfiguracji w Jira i zlewiska:
    
![Ekran konfiguracji dodatku plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Adres URL metadanych**: adres URL można pobrać metadanych federacji z usługą Azure AD.
 
*   **Identyfikatory**: adres URL tej usługi Azure AD używa do walidacji źródła żądania. Mapowania **identyfikator** elementu w usłudze Azure AD. Wtyczka automatycznie pochodzi ten adres URL jako https://*< domeny: port >*/.
 
*   **Adres URL odpowiedzi**: adres URL odpowiedzi w dostawcy tożsamości (IdP), który inicjuje SAML logowania. Mapowania **adres URL odpowiedzi** elementu w usłudze Azure AD. Wtyczka automatycznie pochodzi ten adres URL jako https://*< domeny: port >*/plugins/servlet/saml/auth.
 
*   **Zaloguj się na adres URL**: adres URL logowania jednokrotnego w sieci IdP, który inicjuje SAML logowania. Mapowania **Sign On** elementu w usłudze Azure AD. Wtyczka automatycznie pochodzi ten adres URL jako https://*< domeny: port >*/plugins/servlet/saml/auth.
 
*   **Identyfikator jednostki IdP**: identyfikator jednostki, który korzysta z dostawców tożsamości. To pole jest wypełniane podczas adres URL został rozwiązany.
 
*   **Adres URL logowania**: adres URL logowania z użytkownika IdP. To pole jest wypełniane z usługi Azure AD, gdy adres URL został rozwiązany.
 
*   **Adres URL wylogowania**: adres URL wylogowania z Twojej IdP. To pole jest wypełniane z usługi Azure AD, gdy adres URL został rozwiązany.
 
*   **Certyfikat X.509**: IdP swój certyfikat X.509. To pole jest wypełniane z usługi Azure AD, gdy adres URL został rozwiązany.
 
*   **Nazwa przycisku logowania**: Nazwa logowania przycisku, który organizacja chce użytkowników, aby wyświetlić na stronie logowania.
 
*   **Lokalizacje identyfikator użytkownika SAML**: lokalizacja, w których można oczekiwać Jira lub zlewiska identyfikator użytkownika w odpowiedzi SAML. Można ją w **NameID** lub w nazwie atrybutu niestandardowego.
 
*   **Nazwa atrybutu**: Nazwa atrybutu, gdy oczekiwano Identyfikatora użytkownika.
 
*   **Włączanie odnajdowania obszaru macierzystego**: zaznaczenie, aby upewnić się, jeśli firma korzysta z usługi Active Directory Federation Services (AD FS), na znak — w.
 
*   **Nazwa domeny**: nazwę domeny, jeśli logowanie na podstawie usługi AD FS.
 
*   **Włącz pojedynczego wylogowanie**: zaznaczenie, aby upewnić się, aby wylogować się z usługą Azure AD po wylogowaniu się użytkownika z Jira lub zlewiska.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Otrzymujesz wiele błędów certyfikatów**: Zaloguj się do usługi Azure AD i usunąć wielu certyfikatów, które są dostępne dla aplikacji. Upewnij się, że istnieje tylko jeden certyfikat.

* **Certyfikat jest zbliża się data wygaśnięcia w usłudze Azure AD**: dodatki zajmie się automatycznego przerzucania certyfikatu. W przypadku certyfikat wygaśnie, nowego certyfikatu powinna być oznaczona jako aktywny i nieużywanych certyfikaty powinny zostać usunięte. Gdy użytkownik próbuje zalogować się do Jira w tym scenariuszu pobiera wtyczki i zapisuje nowego certyfikatu.

* **Aby wyłączyć WebSudo (Wyłącz administratora bezpiecznej sesji)**:
    
  * Dla Jira administrator bezpiecznej sesji (to znaczy potwierdzenie hasła przed uzyskaniem dostępu do funkcji administracyjnych) są domyślnie włączone. Jeśli chcesz usunąć tę możliwość wystąpienia Jira, należy wprowadzić następujący wiersz w pliku jira config.properties: `ira.websudo.is.disabled = true`
    
  * Dla zlewiska, wykonaj kroki opisane [witrynę pomocy technicznej zlewiska](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pola, które mają zostać wypełnione przez adres URL metadanych nie są pobierania wypełnione**:
    
  * Sprawdź, czy adres URL jest poprawny. Sprawdź, czy zamapowaniu poprawny identyfikator dzierżawy i aplikacji.
    
  * Wprowadź adres URL w przeglądarce i zobacz, jeśli zostanie wyświetlony XML metadanych federacji.

* **Jest to wewnętrzny błąd serwera**: Przejrzyj dzienniki w katalogu dziennika instalacji. Jeśli otrzymujesz błąd, gdy użytkownik próbuje się zalogować za pomocą usługi Azure AD z logowania jednokrotnego, można udostępniać dzienniki z zespołem pomocy technicznej.

* **Występuje błąd "Nie znaleziono Identyfikatora użytkownika", gdy użytkownik próbuje zalogować**: Utwórz nazwę użytkownika w Jira lub zlewiska.

* **Występuje błąd "Nie można odnaleźć aplikacji" w usłudze Azure AD**: Zobacz, jeśli odpowiedni adres URL jest mapowany do aplikacji w usłudze Azure AD.

* **Potrzebna jest Pomoc**: dotrzeć do [zespołu integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zespół reaguje w 24-48 godzin.
    
  Może też wiązać biletu pomocy technicznej w firmie Microsoft za pośrednictwem kanału portalu Azure.