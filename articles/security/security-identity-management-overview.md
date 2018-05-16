---
title: Funkcje zabezpieczeń Azure, które umożliwiają zarządzanie tożsamościami | Dokumentacja firmy Microsoft
description: " Ten artykuł zawiera omówienie podstawowe funkcje zabezpieczeń platformy Azure, które ułatwiają zarządzanie tożsamościami. Pomocy firmy Microsoft tożsamościami i dostępem zarządzania rozwiązań IT ochrony dostępu do aplikacji i zasobów w firmowym centrum danych i w chmurze, włączanie dodatkowe poziomy sprawdzania poprawności, takich jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d07ad69b3a1f6023cf70bb0b9a82cd54defc5c49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-identity-management-security-overview"></a>Przegląd zabezpieczeń zarządzania tożsamość platformy Azure
Pomocy firmy Microsoft tożsamościami i dostępem zarządzania rozwiązań IT ochrony dostępu do aplikacji i zasobów w firmowym centrum danych i w chmurze, włączanie dodatkowe poziomy sprawdzania poprawności, takich jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Monitorowania podejrzanych działań przez zaawansowane zabezpieczenia raportowania, inspekcji i alerty, pomaga ograniczyć potencjalne problemy. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) zapewnia jednokrotnego do tysięcy chmury aplikacji (SaaS) i dostęp do aplikacji sieci web, możesz uruchomić lokalnie.

Korzyści w zakresie zabezpieczeń dla usługi Azure Active Directory (AD) mogą być następujące:

* Tworzenie i zarządzanie nimi jednej tożsamości dla każdego użytkownika w przedsiębiorstwie hybrydowego Synchronizacja użytkowników, grup i urządzeń
* Udostępniany pojedynczego logowania jednokrotnego do tysięcy wstępnie zintegrowanych aplikacji SaaS w tym aplikacji
* Włącz zabezpieczenia dostępu do aplikacji, wymuszając uwierzytelnianie wieloskładnikowe opartych na regułach, które zarówno lokalnie i aplikacji w chmurze
* Bezpieczny dostęp zdalny należy do lokalnej aplikacji sieci web za pośrednictwem serwera Proxy aplikacji usługi Azure AD

Celem tego artykułu jest zapewnienie omówienie podstawowe funkcje zabezpieczeń platformy Azure, które ułatwiają zarządzanie tożsamościami. Firma Microsoft udostępnia również łącza do artykułów, które zapewniają szczegółowe informacje dotyczące każdej funkcji, aby dowiedzieć się więcej.  

Artykuł skupia się wokół następujących funkcji zarządzania Azure tożsamości core:

* Logowanie jednokrotne
* Zwrotny serwer proxy
* Uwierzytelnianie wieloskładnikowe
* Monitorowanie zabezpieczeń, alertów i raportów na podstawie learning maszyny
* Zarządzanie tożsamościami i dostępem klientów
* Rejestracja urządzenia
* Zarządzanie tożsamościami uprzywilejowanymi
* Ochrona tożsamości
* Hybrydowe Zarządzanie tożsamościami

## <a name="single-sign-on"></a>Logowanie jednokrotne
Pojedynczy logowania jednokrotnego (SSO) oznacza, że możliwość dostępu do aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji bez konieczności uwierzytelnienia (na przykład wpisz hasło) po raz drugi.

W wielu organizacjach opierają się na oprogramowania jako aplikacje usługi (SaaS), takich jak Office 365, pole i Salesforce na produktywność użytkownika końcowego. W przeszłości personel działu informatycznego jest niezbędne do indywidualnie tworzenie i aktualizowanie kont użytkowników w każdej aplikacji SaaS, a użytkownicy będą musieli Zapamiętaj hasło dla każdej aplikacji SaaS.

Lokalnych środowisk usługi Active Directory do chmury, stanowi rozszerzenie usługi Azure AD umożliwieniem użytkownikom korzystania swoje konta organizacyjne podstawowego nie tylko zalogować się na urządzeniach przyłączonych do domeny i zasobów firmy, ale również wszystkich aplikacji sieci web i aplikacji SaaS potrzebne do ich zadania.

Nie tylko użytkowników nie trzeba zarządzać wiele zestawów nazwy użytkowników i hasła, dostęp do aplikacji mogą być automatycznie udostępnione lub cofnąć elastycznie na podstawie grupy organizacyjne i ich stan jako pracownika. Usługi Azure AD wprowadza zabezpieczeń i kontroli dostępu ładu, umożliwiające centralne zarządzanie dostępem użytkowników do aplikacji SaaS.

Więcej informacji:

* [Omówienie logowania jednokrotnego](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
* [Integrowanie usługi Azure Active Directory logowania jednokrotnego dla aplikacji SaaS](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Zwrotny serwer proxy
Serwera Proxy aplikacji usługi Azure AD umożliwia publikowanie aplikacji lokalnych, takich jak [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) witryn, [aplikacji Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), i [IIS](http://www.iis.net/)— na podstawie aplikacji w sieci prywatnej i zapewnia bezpieczny dostęp do użytkowników spoza sieci. Serwer Proxy aplikacji udostępnia dostępu zdalnego i rejestracji jednokrotnej (SSO) dla różnych typów aplikacji sieci web lokalnie z tysiącami aplikacji SaaS, które obsługuje usługę Azure AD. Pracownicy mogą zalogować się do aplikacji z domu na ich własnych urządzeń i uwierzytelniania za pośrednictwem tego serwera proxy oparte na chmurze.

Więcej informacji:

* [Włączanie serwera Proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-enable.md)
* [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Single-sign-on z serwerem Proxy aplikacji](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Praca z dostępu warunkowego](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe
Usługa Azure Multi-Factor authentication (MFA) jest metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. MFA ułatwia zabezpieczenie dostępu do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji — połączenie telefoniczne, wiadomość tekstowa lub aplikacja mobilna weryfikacji lub powiadamiania o kodzie i innych firm tokenów OAuth.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak działa usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alertów i raportów na podstawie learning maszyny
Monitorowanie zabezpieczeń i alertów i machine learning raportów na podstawie identyfikujące niespójne wzorce dostępu ułatwia ochronę firmy. Dostęp do usługi Azure Active Directory i raporty użycia umożliwia wgląd we integralności i bezpieczeństwa katalogu organizacji. Dzięki tym informacjom administratora katalogu można lepiej określić, gdzie może znajdować się możliwe zagrożenia bezpieczeństwa, tak aby ich odpowiednio zaplanować ich eliminowania.

W portalu Azure raporty są podzielone na następujące sposoby:

* Raporty anomalii — zawiera zdarzenia znajdujące się nietypowe logowania. Naszym celem jest uświadomić możesz takiego działania i umożliwiają mieć możliwość określenia, czy jest podejrzane zdarzenia.
* Raporty aplikacji zintegrowanego — wgląd w sposób aplikacji w chmurze są używane w organizacji. Usługa Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.
* Raporty o błędach — wskazują błędy, które mogą wystąpić podczas inicjowania obsługi administracyjnej kont do aplikacji zewnętrznych.
* Raporty dotyczące użytkownika — wyświetlać urządzenia/symbol w dane o aktywności dla określonego użytkownika.
* Dzienniki aktywności — zawiera rekord wszystkich zdarzeń inspekcji w ostatnich 24 godzinach, ostatnich 7 dni lub ostatnich 30 dni i grupy działania zmiany i działanie resetowania i rejestracji hasła.

Więcej informacji:

* [Wyświetlanie raportów dostępu i użycia](../active-directory/active-directory-view-access-usage-reports.md)
* [Wprowadzenie do korzystania z usługi Azure Active Directory raportowania](../active-directory/active-directory-reporting-getting-started.md)
* [Przewodnik po raportach usługi Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem klientów
Usługa Azure Active Directory B2C jest wysokiej dostępności, globalnych, tożsamość usługi zarządzania dla aplikacji dla użytkowników, która może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą logować się do wszystkich aplikacji przy użyciu w pełni dostosowywanego procesu, używając istniejących kont sieci społecznościowych lub tworząc nowe poświadczenia.

W przeszłości deweloperzy aplikacji, którzy chcieli wprowadzić funkcje tworzenia kont i logowania użytkowników w swoich aplikacjach, musieli napisać własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Usługa Azure Active Directory B2C oferuje organizacji lepszy sposób integracji zarządzania tożsamością użytkowników w aplikacjach za pomocą bezpiecznej, spełniającej standardy platformy i dużego zestawu rozszerzalnych zasad.

Gdy używasz usługi Azure Active Directory B2C użytkownicy mogą rejestrować dla aplikacji, przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło lub nazwa użytkownika i hasło).

Więcej informacji:

* [Co to jest Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C w wersji zapoznawczej: zarejestrować i logowanie użytkowników w twoich aplikacjach](../active-directory-b2c/active-directory-b2c-overview.md)
* [W wersji zapoznawczej usługi Azure Active Directory B2C: Typy aplikacji](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Rejestracja urządzenia
Rejestracja urządzenia w usłudze Azure AD jest podstawą oparta na urządzeniach [dostępu warunkowego](../active-directory/active-directory-conditional-access-device-registration-overview.md) scenariuszy. Po zarejestrowaniu urządzenia rejestracji urządzeń usługi Azure Active Directory zapewnia urządzenia przy użyciu tożsamości, który służy do uwierzytelniania urządzenia podczas logowania się użytkownika. Uwierzytelnionego urządzenia i atrybutów urządzenia można następnie użyć do wymuszania zasad dostępu warunkowego dla aplikacji hostowanych w chmurze i lokalnych.

W połączeniu z rozwiązania do zarządzania urządzeniami Przenośnymi urządzeniami przenośnymi, takie jak usługa Intune, atrybuty urządzenia w usłudze Azure Active Directory są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Umożliwia to tworzenie reguł dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności.

Więcej informacji:

* [Wprowadzenie do rejestracji urządzeń usługi Azure Active Directory](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatycznej rejestracji urządzeń z urządzeń dołączonych do domeny usługi Azure Active Directory dla systemu Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Konfigurowanie automatycznej rejestracji systemu Windows przyłączone do domeny urządzenia z usługi Azure Active Directory](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Zarządzanie tożsamościami uprzywilejowanymi
Korzystając z aplikacji Azure Active Directory (AD) Privileged Identity Management, można zarządzać tożsamościami uprzywilejowanymi oraz kontrolować i monitorować je, a także uzyskiwać dostęp do zasobów w usłudze Azure AD i innych usługach online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

Czasami użytkownicy muszą wykonać operacje uprzywilejowane w zasobów platformy Azure lub usługi Office 365 lub innych aplikacji SaaS. Często oznacza to, że organizacje mają nadanie im stałe uprzywilejowanego dostępu w usłudze Azure AD. Jest to zagrożenie bezpieczeństwa rosnącym zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, co robią tych użytkowników, używając swoich uprawnień administratora. Ponadto w przypadku złamania zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego naruszenia co może mieć wpływ na ich ogólne bezpieczeństwo chmury. Azure AD Privileged Identity Management pomaga rozwiązywać to ryzyko.

Azure AD Privileged Identity Management umożliwia:

* Zobacz użytkowników, którzy są administratorami usługi Azure AD
* Włącz na żądanie "just in time" dostęp administracyjny do usługi Online firmy Microsoft, takich jak Office 365 i Intune
* Uzyskaj raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratora
* Uzyskiwanie alertów dotyczących dostępu do ról uprzywilejowanych

Więcej informacji:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Role w usłudze Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management: Jak dodać lub usunąć rolę użytkownika](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrona tożsamości
Azure AD Identity Protection to usługa zabezpieczeń, która udostępnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach wpływających na tożsamości organizacji. Identity Protection wykorzystuje funkcje wykrywania anomalii istniejących Azure usługi Active Directory (dostępne za pośrednictwem usługi Azure AD nietypowe działanie raportów) i wprowadza nowe typy zdarzeń ryzyka, które można wykrywania anomalii w czasie rzeczywistym.

Więcej informacji:

* [Ochronę tożsamości usługi Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Kanał 9: Usługi Azure AD i Pokaż tożsamości: Podgląd ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybrydowe Zarządzanie tożsamościami
Podejście firmy Microsoft do tożsamości obejmuje lokalnie i w chmurze, tworzenie tożsamością jednego użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od lokalizacji.

Więcej informacji:

* [Hybrydowe tożsamości oficjalny dokument](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog zespołu usługi Active Directory](https://blogs.technet.microsoft.com/ad/)
