---
title: Funkcje zabezpieczeń Azure, które umożliwiają zarządzanie tożsamościami | Dokumentacja firmy Microsoft
description: " Ten artykuł zawiera omówienie podstawowe funkcje zabezpieczeń platformy Azure, które ułatwiają zarządzanie tożsamościami. Pomocy firmy Microsoft tożsamościami i dostępem zarządzania rozwiązań IT ochrony dostępu do aplikacji i zasobów w centrum danych firmy i w chmurze, włączanie dodatkowe poziomy sprawdzania poprawności, takich jak uwierzytelnianie wieloskładnikowe i dostępu warunkowego zasady. "
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
ms.openlocfilehash: 22b233e23c5c4609990bb6ba6148fe24d3d82c4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641353"
---
# <a name="azure-identity-management-security-overview"></a>Przegląd zabezpieczeń zarządzania tożsamość platformy Azure
Pomocy firmy Microsoft tożsamościami i dostępem zarządzania rozwiązań IT ochronę dostępu do aplikacji i zasobów w centrum danych firmy i w chmurze. Taką ochronę umożliwia dodatkowe poziomy sprawdzania poprawności, takich jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Monitorowania podejrzanych działań przez zaawansowane zabezpieczenia raportowania, inspekcji i alerty, pomaga ograniczyć potencjalne problemy. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) umożliwia logowanie jednokrotne (SSO) do tysięcy oprogramowania chmury jako aplikacje usługi (SaaS) i dostęp do aplikacji sieci web, że można uruchomić lokalnie.

Korzystając z zabezpieczeń oferowanych przez usługi Azure Active Directory (Azure AD), można:

* Utwórz i Zarządzaj jednej tożsamości dla każdego użytkownika w przedsiębiorstwie hybrydowego Synchronizacja użytkowników, grup i urządzeń.
* Zapewnienia logowania jednokrotnego dostępu do aplikacji, w tym tysięcy wstępnie zintegrowanych aplikacji SaaS.
* Włącz zabezpieczenia dostępu do aplikacji, wymuszając uwierzytelnianie wieloskładnikowe opartych na regułach, które zarówno lokalnie i aplikacji w chmurze.
* Udostępnić bezpieczny dostęp zdalny do lokalnych aplikacji sieci web za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

Celem tego artykułu jest zapewnienie omówienie podstawowe funkcje zabezpieczeń platformy Azure, które ułatwiają zarządzanie tożsamościami. Firma Microsoft udostępnia również łącza do artykułów, które zapewniają szczegółowe informacje dotyczące każdej funkcji, aby dowiedzieć się więcej.  

Artykuł skupia się wokół następujących funkcji zarządzania Azure tożsamości core:

* Logowanie jednokrotne
* Zwrotny serwer proxy
* Multi-Factor Authentication
* Monitorowanie zabezpieczeń, alertów i raportów na podstawie learning maszyny
* Zarządzanie tożsamościami i dostępem klientów
* Rejestracja urządzenia
* Zarządzanie tożsamościami uprzywilejowanymi
* Ochrona tożsamości
* Hybrydowe Zarządzanie tożsamościami

## <a name="single-sign-on"></a>Logowanie jednokrotne
Usługa rejestracji Jednokrotnej oznacza dostępowi do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji bez konieczności uwierzytelnienia (na przykład wpisz hasło) po raz drugi.

W wielu organizacjach opierają się na aplikacji SaaS, takie jak usługi Office 365, pole i Salesforce na wydajność pracy użytkownika. W przeszłości personel działu informatycznego jest niezbędne do indywidualnie tworzenie i aktualizowanie kont użytkowników w każdej aplikacji SaaS, a użytkownicy będą musieli Zapamiętaj hasło dla każdej aplikacji SaaS.

Lokalnych środowisk usługi Active Directory do chmury, stanowi rozszerzenie usługi Azure AD umożliwieniem użytkownikom korzystania swoje konta organizacyjne podstawowego do logowania się nie tylko do urządzeń przyłączonych do domeny i zasobów firmy, ale także do wszystkich sieci web i aplikacji SaaS muszą w przypadku ich zadań.

Nie tylko użytkowników nie trzeba zarządzać wiele zestawów nazwy użytkowników i hasła, można udostępnić lub anulować aprowizację dostęp do aplikacji automatycznie, na podstawie ich grupy organizacyjne i ich stan pracownika. Usługi Azure AD wprowadza zabezpieczeń i kontroli dostępu ładu, z którymi mogą centralnie zarządzać dostępem użytkowników dla aplikacji SaaS.

Więcej informacji:

* [Omówienie logowania jednokrotnego](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrowanie usługi Azure Active Directory logowania jednokrotnego dla aplikacji SaaS](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Zwrotny serwer proxy
Serwera Proxy aplikacji usługi Azure AD umożliwia publikowanie aplikacji lokalnych, takich jak [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) witryn, [aplikacji Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), i [IIS](http://www.iis.net/)— na podstawie aplikacji w sieci prywatnej i zapewnia bezpieczny dostęp do użytkowników spoza sieci. Serwer Proxy aplikacji zapewnia dostęp zdalny i logowanie Jednokrotne dla wielu typów lokalnej sieci web aplikacji z tysiącami aplikacji SaaS, które obsługuje usługę Azure AD. Pracownicy mogą logować się do aplikacji z domu na ich własnych urządzeń i uwierzytelniania za pośrednictwem tego serwera proxy oparte na chmurze.

Więcej informacji:

* [Włączanie serwera Proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-enable.md)
* [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Logowanie jednokrotne przy użyciu serwera Proxy aplikacji](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Praca z dostępu warunkowego](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Usługa Azure Multi-Factor Authentication to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Usługi Multi-Factor Authentication ułatwia zabezpieczenie dostępu do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji: połączeń telefonicznych, wiadomości SMS lub powiadomienia z aplikacji mobilnej lub kodów weryfikacyjnych i tokenów OAuth innych firm.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak działa usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alertów i raportów na podstawie learning maszyny
Monitorowanie zabezpieczeń, alertów i machine learning raportów na podstawie identyfikujące niespójne wzorce dostępu ułatwia ochronę firmy. Można użyć usługi Azure AD dostępu i użycia raporty do wgląd we integralności i bezpieczeństwa katalogu organizacji. Dzięki tym informacjom administrator katalogu może lepiej określić, gdzie to możliwe zagrożenia bezpieczeństwa może być tak, aby ich odpowiednio zaplanować ich eliminowania.

W portalu Azure raporty można podzielić na następujące kategorie:

* **Raporty anomalii**: zawiera zdarzenia logowania, znajdujące się nietypowych. Naszym celem jest uświadomić możesz takiego działania i umożliwiają określenie, czy zdarzenie jest podejrzana.
* **Zintegrowane raportów programu Application**: wgląd w sposób aplikacji w chmurze są używane w organizacji. Usługi Azure AD, oferuje integrację z tysiącami aplikacji w chmurze.
* **Raporty o błędach**: wskazują błędy, które mogą wystąpić podczas obsługi administracyjnej kont do aplikacji zewnętrznych.
* **Raporty dotyczące użytkownika**: wyświetlanie danych logowania działanie urządzenia dla określonego użytkownika.
* **Dzienniki aktywności**: zawiera rekord wszystkich zdarzeń inspekcji w ostatnich 24 godzinach, ostatnich 7 dni, lub ostatnich 30 dni i grupy działania zmiany i działanie resetowania i rejestracji hasła.

Więcej informacji:

* [Wyświetlanie raportów dostępu i użycia](../active-directory/active-directory-view-access-usage-reports.md)
* [Rozpoczynanie pracy z raportowaniem usługi Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Azure Przewodnik po raportach usługi Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem klientów
Usługa Azure AD B2C jest wysokiej dostępności, globalnych, tożsamość usługi zarządzania dla aplikacji dla użytkowników, która może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Użytkownicy mogą rejestrować do wszystkich aplikacji za pomocą środowiska można dostosować przy użyciu istniejących kont społecznościowych lub tworząc nowe poświadczenia.

W przeszłości deweloperów, którzy chcieli odbiorców a Zaloguj do aplikacji, musieli napisać własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Usługa Azure AD B2C oferuje organizacji lepszy sposób integracji zarządzania tożsamością użytkowników w aplikacjach za pomocą bezpiecznej, spełniającej standardy platformy i dużego zestawu rozszerzalnych zasad.

Gdy używasz usługi Azure AD B2C, użytkownicy mogą rejestrować dla aplikacji, przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło lub nazwa użytkownika i hasło).

Więcej informacji:

* [Co to jest Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C w wersji zapoznawczej: zarejestrować i logowanie użytkowników w twoich aplikacjach](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C w wersji zapoznawczej: Typy aplikacji](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Rejestracja urządzenia
Rejestracja urządzenia w usłudze Azure AD jest podstawą oparta na urządzeniach [dostępu warunkowego](../active-directory/active-directory-conditional-access-device-registration-overview.md) scenariuszy. Po zarejestrowaniu urządzenia rejestracji urządzeń usługi Azure AD zapewnia urządzenia przy użyciu tożsamości, używany do uwierzytelniania urządzenia, gdy użytkownik zaloguje się. Uwierzytelnionego urządzenia i atrybutów urządzenia można następnie używane do wymuszania zasad dostępu warunkowego dla aplikacji, które znajdują się w chmurze i lokalnie.

W połączeniu z rozwiązania do zarządzania urządzeniami przenośnymi, takie jak usługi Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Następnie możesz utworzyć zasady dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności.

Więcej informacji:

* [Wprowadzenie do rejestracji urządzeń usługi Azure AD](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatycznej rejestracji urządzeń z usługą Azure AD dla urządzeń z systemem Windows przyłączonych do domeny](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Konfigurowanie automatycznej rejestracji systemu Windows przyłączone do domeny urządzenia z usługą Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Zarządzanie tożsamościami uprzywilejowanymi
Z usługi Azure AD Privileged Identity Management można zarządzanie, sterowanie i monitorowanie tożsamości uprzywilejowanych oraz dostęp do zasobów w usłudze Azure AD, a także innych usług Microsoft online, takich jak usługi Office 365 i Microsoft Intune.

Czasami użytkownicy muszą wykonać operacje uprzywilejowane zasobów platformy Azure lub usługi Office 365 lub innych aplikacji SaaS. Tę potrzebę często oznacza, że organizacje muszą być trwałe uprzywilejowanego dostępu użytkowników w usłudze Azure AD. Taki dostęp jest rosnącym zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, co robią użytkownicy z ich uprawnieniami administratora. Ponadto w przypadku złamania zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego naruszenia co może wpłynąć na ogólne bezpieczeństwo chmurze organizacji. Azure AD Privileged Identity Management pomaga w celu ograniczenia tego ryzyka.

Za pomocą usługi Azure AD Privileged Identity Management można:

* Zobacz, użytkowników, którzy są administratorami usługi Azure AD.
* Włącz na żądanie just in time (JIT) administracyjne dostęp do usług firmy Microsoft, takich jak Office 365 i Intune.
* Pobierz raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratora.
* Uzyskiwanie alertów dotyczących dostępu do ról uprzywilejowanych.

Więcej informacji:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Role w usłudze Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management: Jak dodać lub usunąć rolę użytkownika](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrona tożsamości
Azure AD Identity Protection to usługa zabezpieczeń, która udostępnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych lukach, które mają wpływ na tożsamości organizacji. Identity Protection wykorzystuje istniejące możliwości wykrywania anomalii usługi Azure AD, które są dostępne za pośrednictwem raportów nietypowe działanie usługi Azure AD. Ochronę tożsamości wprowadza również nowe typy zdarzeń ryzyka, które można wykrywania anomalii w czasie rzeczywistym.

Więcej informacji:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Kanał 9: Usługi Azure AD i Pokaż tożsamości: Podgląd ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybrydowe Zarządzanie tożsamościami
Podejście firmy Microsoft do tożsamości obejmuje lokalnie i w chmurze, tworzenie tożsamością jednego użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od lokalizacji.

Więcej informacji:

* [Hybrydowe tożsamości oficjalny dokument](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog zespołu w usłudze Azure AD](https://blogs.technet.microsoft.com/ad/)
