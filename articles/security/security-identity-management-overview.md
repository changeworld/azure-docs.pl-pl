---
title: Zabezpieczenia platformy Azure funkcje ułatwiające zarządzanie tożsamościami | Dokumentacja firmy Microsoft
description: " Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które pomoc dotycząca zarządzania tożsamościami. Pomocy firmy Microsoft tożsamości i dostępu do zarządzania rozwiązaniami IT ochrona dostępu do aplikacji i zasobów w firmowym centrum danych i w chmurze, umożliwiając dodatkowe poziomy walidacji, takimi jak uwierzytelnianie wieloskładnikowe i dostępu warunkowego zasady. "
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
ms.openlocfilehash: cbf6ac736db9935f5ec52fa4507dab1e56bcde43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611191"
---
# <a name="azure-identity-management-security-overview"></a>Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure
Microsoft tożsamości i dostępu do zarządzania rozwiązania pomagają działowi IT chronić dostęp do aplikacji i zasobów w firmowym centrum danych i w chmurze. Taka ochrona umożliwia dodatkowe poziomy walidacji, takimi jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Monitorowania podejrzanych działań przy użyciu zaawansowanych zabezpieczeń, raportowanie, inspekcji i alerty, że pomaga jej uniknąć potencjalnych problemów z zabezpieczeniami. [Usługa Azure Active Directory — wersja Premium](../active-directory/active-directory-editions.md) umożliwia logowanie jednokrotne (SSO) do tysięcy oprogramowaniu w chmurze jako usługi (SaaS) i uzyskać dostęp do aplikacji sieci web, jak uruchomić lokalnie.

Dzięki wykorzystaniu zalet usługi Azure Active Directory (Azure AD), możesz wykonywać następujące czynności:

* Tworzenie i zarządzanie jednej tożsamości dla każdego użytkownika w przedsiębiorstwie hybrydowe, synchronizacja użytkowników, grup i urządzeń.
* Zapewniają dostęp logowania jednokrotnego do aplikacji, w tym tysiące wstępnie zintegrowanych aplikacji SaaS.
* Włącz zabezpieczenia dostępu do aplikacji przez wymuszenie uwierzytelniania wieloskładnikowego opartego na regułach, które zarówno lokalnie i aplikacji w chmurze.
* Zapewnij bezpieczny dostęp zdalny do aplikacji sieci web w środowisku lokalnym za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

Celem tego artykułu jest omówiono podstawowe funkcje zabezpieczeń platformy Azure, które pomagają za pomocą funkcji zarządzania tożsamościami. Udostępniamy również linki do artykułów, które zapewniają szczegółowe informacje o każdej funkcji, dzięki czemu możesz dowiedzieć się więcej.  

Artykuł koncentruje się na następujących funkcji zarządzania usługi Azure Identity core:

* Logowanie jednokrotne
* Zwrotny serwer proxy
* Multi-Factor Authentication
* Monitorowanie zabezpieczeń i alerty oraz raporty oparte na uczeniu maszynowym
* Zarządzanie tożsamościami i dostępem klientów
* Rejestracja urządzenia
* Usługa Privileged identity management
* Ochrona tożsamości
* Hybrydowe Zarządzanie tożsamościami

## <a name="single-sign-on"></a>Logowanie jednokrotne
Logowanie Jednokrotne oznacza możliwość dostępu do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko wtedy, gdy za pomocą jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji bez konieczności uwierzytelnienia (na przykład, wpisz hasło) po raz drugi.

Wiele organizacji korzystają z aplikacji SaaS, takich jak usługi Office 365, Box i Salesforce dla wydajność pracy użytkowników. W przeszłości pracowników wsparcia informatycznego potrzebne do indywidualnie tworzenia i aktualizowania kont użytkowników w każdej aplikacji SaaS i użytkownicy musieli Zapamiętaj hasło dla każdej aplikacji SaaS.

Usługi Azure AD rozszerzenie dla usługi Active Directory środowisk lokalnych do chmury, umożliwieniem użytkownikom korzystania swoje konta organizacyjne podstawowego do logowania, nie tylko do urządzeń przyłączonych do domeny i zasobów firmy, ale także do sieci web i aplikacji SaaS potrzebują w przypadku ich zadań.

Nie tylko użytkowników nie trzeba zarządzać wiele zestawów nazwy użytkowników i hasła, możesz aprowizować lub anulować aprowizację dostęp do aplikacji automatycznie na podstawie ich grup organizacyjnych i ich stanu. Usługa Azure AD wprowadza zabezpieczeń i kontroli dostępu do zarządzania, za pomocą których można centralnie zarządzać dostępem użytkowników w aplikacjach SaaS.

Więcej informacji:

* [Omówienie logowania jednokrotnego](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrowanie usługi Azure Active Directory logowania jednokrotnego z aplikacjami SaaS](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Zwrotny serwer proxy
Serwer Proxy aplikacji usługi Azure AD umożliwia publikowanie lokalnych aplikacji, takich jak [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) witryn, [aplikacji Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), i [IIS](http://www.iis.net/)— na podstawie aplikacji w sieci prywatnej i zapewnia bezpieczny dostęp dla użytkowników spoza sieci. Serwer Proxy aplikacji udostępnia dostępu zdalnego i logowania jednokrotnego dla wielu typów lokalnych aplikacji sieci web z tysiącami aplikacji SaaS, które obsługuje usługę Azure AD. Pracownicy mogą logować się do aplikacji z domu na ich własnych urządzeń i Uwierzytelnij się za pośrednictwem tego serwera proxy oparte na chmurze.

Więcej informacji:

* [Włączanie serwera Proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-enable.md)
* [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Logowanie jednokrotne przy użyciu serwera Proxy aplikacji](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Praca z dostępem warunkowym](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Usługa Azure Multi-Factor Authentication to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. Multi-Factor Authentication zabezpiecza dostęp do danych i aplikacji, jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia ona silne uwierzytelnianie za pomocą szeregu opcji weryfikacji: połączeń telefonicznych, wiadomości SMS lub powiadomienia aplikacji mobilnej lub kody weryfikacyjne i tokenów protokołu OAuth innych firm.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak działa usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń i alerty oraz raporty oparte na uczeniu maszynowym
Monitorowanie zabezpieczeń i alerty oraz oparte na nauce maszynowej raporty maszynowej, które identyfikują niespójne wzorce dostępu może pomóc chronić Twoją firmę. Umożliwia dostęp do usługi Azure AD i raporty użycia, aby uzyskać wgląd w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administratora katalogu można lepiej zidentyfikować potencjalne zagrożenia bezpieczeństwa może być tak, aby ich odpowiednio zaplanować ograniczyć te zagrożenia.

W witrynie Azure portal raporty można podzielić na następujące kategorie:

* **Raporty anomalii**: zawiera zdarzenia logowania, które znaleźliśmy jako nietypowe. Naszym celem jest na należy pamiętać o takiej działalności i umożliwiają określenie, czy zdarzenie jest podejrzane.
* **Zintegrowane raporty aplikacji**: dają wgląd w sposób są używane w aplikacjach w chmurze w Twojej organizacji. Usługa Azure AD oferuje integrację z tysięcy aplikacji w chmurze.
* **Raporty o błędach**: informują o błędach, które mogą wystąpić podczas aprowizowania kont do aplikacji zewnętrznych.
* **Raporty dotyczące poszczególnych użytkowników**: wyświetlanie danych aktywności logowania urządzeń dla określonego użytkownika.
* **Dzienniki aktywności**: zawiera rekord wszystkich zdarzeń inspekcji w ciągu ostatnich 24 godzin, ostatnich 7 dni, lub ostatnich 30 dni i działania zmiany w grupie i działanie resetowania i rejestracji hasła.

Więcej informacji:

* [Wyświetlanie raportów dostępu i użycia](../active-directory/active-directory-view-access-usage-reports.md)
* [Rozpocznij pracę dzięki raportom usługi Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Przewodnik raportowania usługi Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem klientów
Usługa Azure AD B2C jest wysoce dostępnej, globalnej, Usługa zarządzania tożsamościami dla aplikacji dla użytkowników może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Klientów można zarejestrować się do wszystkich aplikacji przy użyciu w pełni dostosowywanego procesu przy użyciu istniejących kont sieci społecznościowych lub tworząc nowe poświadczenia.

W przeszłości deweloperzy aplikacji, którzy chcieli Rejestracja odbiorców i loguje go ze swoimi aplikacjami, musieli napisać własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Usługa Azure AD B2C oferuje organizacji lepszy sposób integracji funkcji zarządzania tożsamościami konsumentów w aplikacjach za pomocą platformy bezpieczne i oparte na standardach i szerokiej gamy rozszerzalnych zasad.

Gdy używasz usługi Azure AD B2C, użytkownicy mogą rejestrować dla aplikacji, za pomocą istniejących kont sieci społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło lub nazwa użytkownika i hasło).

Więcej informacji:

* [Co to jest Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C w wersji zapoznawczej: Zaloguj się i logowanie użytkowników w aplikacjach](../active-directory-b2c/active-directory-b2c-overview.md)
* [Usługa Azure Active Directory B2C w wersji zapoznawczej: Typy aplikacji](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Rejestracja urządzenia
Rejestracja urządzenia w usłudze Azure AD jest podstawą dla opartego na urządzeniach [dostępu warunkowego](../active-directory/active-directory-conditional-access-device-registration-overview.md) scenariuszy. Po zarejestrowaniu urządzenia usługa rejestracja urządzeń w usłudze Azure AD zapewnia urządzenia przy użyciu tożsamości służący do uwierzytelniania urządzenia podczas logowania użytkownika. Uwierzytelnionego urządzenia i atrybutów urządzenia można następnie służyć do wymuszania zasad dostępu warunkowego dla aplikacji, które są hostowane w chmurze i lokalnych.

W połączeniu z rozwiązaniem do zarządzania urządzeniami przenośnymi, takie jak usługi Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Następnie można utworzyć zasady dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności.

Więcej informacji:

* [Rozpoczynanie pracy z usługą rejestracji urządzeń w usłudze Azure AD](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatycznej rejestracji urządzeń w usłudze Azure AD w przypadku urządzeń przyłączonych do domeny Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Konfigurowanie automatycznej rejestracji Windows przyłączone do domeny urządzenia z usługą Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Usługa Privileged identity management
Za pomocą usługi Azure AD Privileged Identity Management można zarządzanie, sterowanie i monitorowanie tożsamości uprzywilejowanych i dostęp do zasobów w usłudze Azure AD, a także innych usług Microsoft online services, takich jak usługi Office 365 i Microsoft Intune.

Czasami użytkownicy muszą przeprowadzić uprzywilejowanych operacji korzystających z zasobów platformy Azure lub usługi Office 365 lub innych aplikacji SaaS. Te wymagania często oznacza, że organizacje konieczne jest nadanie użytkownikom trwałego dostępu uprzywilejowanego w usłudze Azure AD. Taki dostęp jest rosnący zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, co użytkownicy robią z ich uprawnieniami administratora. Ponadto w przypadku naruszenia zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego jednego naruszenia mogą mieć wpływ na ogólne bezpieczeństwo chmury w organizacji. Usługa Azure AD Privileged Identity Management pomaga zmniejszyć to zagrożenie.

Za pomocą usługi Azure AD Privileged Identity Management możesz wykonywać następujące czynności:

* Zobacz użytkowników, którzy są administratorami usługi Azure AD.
* Włącz na żądanie just-in-time (JIT) dostępu administracyjnego do usług firmy Microsoft, takich jak Office 365 i Intune.
* Uzyskaj raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratora.
* Uzyskiwanie alertów dotyczących dostępu do ról uprzywilejowanych.

Więcej informacji:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Role w usłudze Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-roles.md)
* [Usługa Azure AD Privileged Identity Management: Jak dodać lub usunąć rolę użytkownika](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrona tożsamości
Usługa Azure AD Identity Protection to usługa zabezpieczeń, która zapewnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Identity Protection wykorzystuje istniejące możliwości wykrywania anomalii usługi Azure AD, które są dostępne w raportach usługi Azure AD nietypowych działań. Ochrona tożsamości wprowadza również nowe typy zdarzeń o podwyższonym ryzyku, które mogą wykrywać anomalie w czasie rzeczywistym.

Więcej informacji:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Witrynie Channel 9: Usługi Azure AD i wyświetlanie tożsamości: Identity Protection w wersji zapoznawczej](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybrydowe Zarządzanie tożsamościami
Podejście firmy Microsoft do tożsamości zakresów w środowisku lokalnym i w chmurze, tworzenia pojedynczej tożsamości użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od lokalizacji.

Więcej informacji:

* [Oficjalny dokument tożsamości hybrydowej](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog zespołu usługi Azure AD](https://blogs.technet.microsoft.com/ad/)
