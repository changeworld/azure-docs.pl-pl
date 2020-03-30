---
title: Funkcje zabezpieczeń platformy Azure ułatwiające zarządzanie tożsamościami | Dokumenty firmy Microsoft
description: " Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które pomagają w zarządzaniu tożsamościami. Rozwiązania firmy Microsoft do zarządzania tożsamościami i dostępem pomagają działowi IT chronić dostęp do aplikacji i zasobów w firmowym centrum danych i w chmurze, umożliwiając dodatkowe poziomy sprawdzania poprawności, takie jak uwierzytelnianie wieloskładnikowe i dostęp warunkowy Zasady działalności. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: f61b6193a0d2082296a17128b41d7220f9b7e05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565897"
---
# <a name="azure-identity-management-security-overview"></a>Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure

 Zarządzanie tożsamościami to proces uwierzytelniania i autoryzowania [podmiotów zabezpieczeń.](/windows/security/identity-protection/access-control/security-principals) Obejmuje również kontrolowanie informacji o tych zleceniodawców (tożsamości). Podmioty zabezpieczeń (tożsamości) mogą obejmować usługi, aplikacje, użytkowników, grupy itp. Rozwiązania firmy Microsoft do zarządzania tożsamościami i dostępem pomagają działowi IT chronić dostęp do aplikacji i zasobów w firmowym centrum danych i w chmurze. Taka ochrona umożliwia dodatkowe poziomy sprawdzania poprawności, takie jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Monitorowanie podejrzanych działań za pomocą zaawansowanego raportowania zabezpieczeń, inspekcji i alertów pomaga ograniczyć potencjalne problemy z zabezpieczeniami. [Usługa Azure Active Directory Premium](/azure/active-directory/active-directory-editions) zapewnia logowanie jednokrotne (SSO) do tysięcy aplikacji w chmurze jako usługi (SaaS) i dostępu do aplikacji sieci web, które są uruchamiane lokalnie.
 
Korzystając z zalet zabezpieczeń usługi Azure Active Directory (Azure AD), można:

* Utwórz jedną tożsamość dla każdego użytkownika w całym przedsiębiorstwie hybrydowym i zarządzaj nią, synchronizując użytkowników, grupy i urządzenia. 
* Zapewnij dostęp do aplikacji SSO, w tym tysiące wstępnie zintegrowanych aplikacji SaaS.
* Zabezpieczyć dostęp do aplikacji przez wymuszenie uwierzytelniania wieloskładnikowego na podstawie zasad zarówno dla aplikacji lokalnych, jak i dla aplikacji w chmurze.
* Aprowizuj bezpieczny dostęp zdalny do lokalnych aplikacji sieci web za pośrednictwem serwera proxy aplikacji usługi Azure AD.

Celem tego artykułu jest zapewnienie omówienia podstawowych funkcji zabezpieczeń platformy Azure, które pomagają w zarządzaniu tożsamościami. Udostępniamy również linki do artykułów, które zawierają szczegółowe informacje o każdej funkcji, dzięki czemu możesz dowiedzieć się więcej.  

W tym artykule skupiono się na następujących podstawowych funkcjach zarządzania tożsamościami platformy Azure:

* Logowanie jednokrotne
* Zwrotny serwer proxy
* Multi-Factor Authentication
* Kontrola dostępu oparta na rolach (RBAC)
* Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym
* Zarządzanie tożsamością i dostępem konsumentów
* Rejestracja urządzenia
* Privileged Identity Management
* Ochrona tożsamości
* Zarządzanie tożsamościami hybrydowymi/połączenie usługi Azure AD
* Przeglądy dostępu w usłudze Azure AD

## <a name="single-sign-on"></a>Logowanie jednokrotne

Logowanie jednokrotne oznacza możliwość uzyskania dostępu do wszystkich aplikacji i zasobów, które są potrzebne do prowadzenia działalności gospodarczej, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu można uzyskać dostęp do wszystkich potrzebnych aplikacji bez konieczności uwierzytelniania (na przykład wpisywać hasło) po raz drugi.

Wiele organizacji korzysta z aplikacji SaaS, takich jak Office 365, Box i Salesforce, aby zwiększyć produktywność użytkowników. W przeszłości pracownicy IT musieli indywidualnie tworzyć i aktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli zapamiętać hasło dla każdej aplikacji SaaS.

Usługa Azure AD rozszerza lokalne środowiska usługi Active Directory na chmurę, umożliwiając użytkownikom korzystanie z podstawowego konta organizacyjnego do logowania się nie tylko do urządzeń i zasobów firmy przyłączonych do domeny, ale także do wszystkich potrzebnych aplikacji sieci Web i SaaS. pracy.

Użytkownicy nie tylko nie muszą zarządzać wieloma zestawami nazw użytkowników i haseł, ale także automatycznie aprowizować lub usuwać z aplikacji dostęp na podstawie ich grup organizacyjnych i stanu pracownika. Usługa Azure AD wprowadza mechanizmy kontroli nadzoru zabezpieczeń i dostępu, za pomocą których można centralnie zarządzać dostępem użytkowników w aplikacjach SaaS.

Więcej informacji:

* [Omówienie logowania jednokrotnego](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integracja logowania jednokrotnego usługi Azure Active Directory z aplikacjami SaaS](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Zwrotny serwer proxy

Usługa Azure AD Application Proxy umożliwia publikowanie aplikacji lokalnych, takich jak witryny [programu SharePoint,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) [aplikacja Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)i [aplikacje iis](https://www.iis.net/)w sieci prywatnej i zapewnia bezpieczny dostęp do użytkowników spoza sieci. Serwer proxy aplikacji zapewnia dostęp zdalny i funkcję SSO dla wielu typów lokalnych aplikacji sieci web z tysiącami aplikacji SaaS, które obsługuje usługę Azure AD. Pracownicy mogą logować się do aplikacji z domu na własnych urządzeniach i uwierzytelniać się za pośrednictwem tego chmurowego serwera proxy.

Więcej informacji:

* [Włączanie serwera proxy aplikacji usługi Azure AD](/azure/active-directory/manage-apps/application-proxy-enable)
* [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](/azure/active-directory/active-directory-application-proxy-publish)
* [Logowanie jednokrotne przy użyciu serwera proxy aplikacji](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Praca z dostępem warunkowym](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Uwierzytelnianie wieloskładnikowe platformy Azure to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje krytyczną drugą warstwę zabezpieczeń do logowania i transakcji użytkowników. Uwierzytelnianie wieloskładnikowe pomaga chronić dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie użytkowników na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą wielu opcji weryfikacji: połączeń telefonicznych, wiadomości tekstowych lub powiadomień aplikacji mobilnych lub kodów weryfikacyjnych i tokenów OAuth innych firm.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak działa usługa Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach stanowi system autoryzacji oparty na usłudze Azure Resource Manager, umożliwiający szczegółowe zarządzanie dostępem do zasobów platformy Azure. RBAC pozwala na szczegółowe sterowanie poziomem dostępu, który mają użytkownicy. Na przykład można ograniczyć użytkownika do zarządzania tylko sieciami wirtualnymi i innym użytkownikiem do zarządzania wszystkimi zasobami w grupie zasobów. Na platformie Azure można korzystać z kilku ról wbudowanych. Poniżej wymieniono cztery podstawowe role wbudowane. Pierwsze trzy są stosowane do wszystkich typów zasobów.

Więcej informacji:

* [Czym jest kontrola dostępu oparta na rolach (RBAC)?](/azure/role-based-access-control/overview)
* [Wbudowane role dla zasobów platformy Azure](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym

Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym, które identyfikują niespójne wzorce dostępu, mogą pomóc w ochronie firmy. Raporty dostępu i użycia usługi Azure AD umożliwiają wgląd w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administrator katalogu może lepiej określić, gdzie mogą leżeć możliwe zagrożenia bezpieczeństwa, aby mógł odpowiednio zaplanować ograniczenie tych zagrożeń.

W witrynie Azure portal raporty dzielą się na następujące kategorie:

* **Raporty anomalii:** zawierają zdarzenia logowania, które okazały się nietypowe. Naszym celem jest uświadomienie ci takiej aktywności i umożliwienie ci ustalenia, czy zdarzenie jest podejrzane.
* **Raporty zintegrowanych aplikacji:** Zapewnij wgląd w sposób, w jaki aplikacje w chmurze są używane w twojej organizacji. Usługa Azure AD oferuje integrację z tysiącami aplikacji w chmurze.
* **Raporty o błędach:** Wskaż błędy, które mogą wystąpić podczas inicjowania obsługi administracyjnej kont do aplikacji zewnętrznych.
* **Raporty specyficzne dla użytkownika**: Wyświetlanie danych aktywności logowania urządzenia dla określonego użytkownika.
* **Dzienniki aktywności:** zawierają rekord wszystkich skontrolowanych zdarzeń w ciągu ostatnich 24 godzin, ostatnich 7 dni lub ostatnich 30 dni oraz zmiany aktywności grupowej oraz resetowanie hasła i rejestrowanie aktywności.

Więcej informacji:

* [Wyświetlanie raportów dostępu i użycia](/azure/active-directory/active-directory-view-access-usage-reports)
* [Wprowadzenie do raportowania usługi Azure Active Directory](/azure/active-directory/active-directory-reporting-getting-started)
* [Przewodnik po raportowaniu usługi Azure Active Directory](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamością i dostępem konsumentów

Usługa Azure AD B2C to wysoce dostępna, globalna usługa zarządzania tożsamościami dla aplikacji przeznaczonych dla konsumentów, która skaluje się do setek milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą logować się do wszystkich aplikacji za pomocą konfigurowalnych środowisk przy użyciu istniejących kont społecznościowych lub tworząc nowe poświadczenia.

W przeszłości deweloperzy aplikacji, którzy chcieli zarejestrować klientów i zalogować się do swoich aplikacji, napisaliby własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Usługa Azure AD B2C oferuje twojej organizacji lepszy sposób integracji zarządzania tożsamościami konsumenta z aplikacjami za pomocą bezpiecznej platformy opartej na standardach i dużego zestawu rozszerzalnych zasad.

Korzystając z usługi Azure AD B2C, konsumenci mogą zarejestrować się w aplikacjach przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło lub nazwę użytkownika i hasło).

Więcej informacji:

* [Co to jest usługa Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Usługa Azure Active Directory B2C - przegląd: rejestrowanie i logowanie użytkowników w Twoich aplikacjach](../../active-directory-b2c/overview.md)
* [Usługa Azure Active Directory B2C Preview: typy aplikacji](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Rejestracja urządzenia

Rejestracja urządzeń usługi Azure AD jest podstawą scenariuszy [dostępu warunkowego](/azure/active-directory/active-directory-conditional-access-device-registration-overview) opartego na urządzeniach. Gdy urządzenie jest zarejestrowane, rejestracja urządzenia usługi Azure AD zapewnia urządzeniu tożsamość, której używa do uwierzytelniania urządzenia, gdy użytkownik się zaloguje. Uwierzytelnione urządzenie i atrybuty urządzenia mogą być następnie używane do wymuszania zasad dostępu warunkowego dla aplikacji hostowanych w chmurze i lokalnie.

W połączeniu z rozwiązaniem do zarządzania urządzeniami przenośnymi, takim jak Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane o dodatkowe informacje o urządzeniu. Następnie można utworzyć reguły dostępu warunkowego, które wymuszają dostęp z urządzeń, aby spełnić standardy zabezpieczeń i zgodności.

Więcej informacji:

* [Wprowadzenie do rejestracji urządzeń usługi Azure AD](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatyczna rejestracja urządzeń za pomocą usługi Azure AD dla urządzeń przyłączonych do domeny systemu Windows](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Konfigurowanie automatycznej rejestracji urządzeń przyłączonych do domeny systemu Windows za pomocą usługi Azure AD](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Dzięki uprzywilejowanemu zarządzaniu tożsamościami usługi Azure AD możesz zarządzać, kontrolować i monitorować uprzywilejowane tożsamości oraz dostęp do zasobów w usłudze Azure AD, a także innych usług online firmy Microsoft, takich jak Office 365 i Microsoft Intune.

Użytkownicy czasami muszą wykonywać uprzywilejowane operacje w zasobach platformy Azure lub Usługi Office 365 lub w innych aplikacjach SaaS. Często oznacza to, że organizacje muszą przyznać użytkownikom stały uprzywilejowany dostęp w usłudze Azure AD. Taki dostęp stanowi rosnące zagrożenie bezpieczeństwa zasobów hostowanych w chmurze, ponieważ organizacje nie mogą wystarczająco monitorować, co użytkownicy robią z ich uprawnieniami administratora. Ponadto jeśli konto użytkownika z uprzywilejowanym dostępem zostanie naruszone, to jedno naruszenie może mieć wpływ na ogólne bezpieczeństwo w chmurze w organizacji. Zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD pomaga ograniczyć to ryzyko.

Za pomocą uprzywilejowanego zarządzania tożsamościami usługi Azure AD można:

* Zobacz, którzy użytkownicy są administratorami usługi Azure AD.
* Włącz dostęp administracyjny na żądanie i just-in-time (JIT) do usług firmy Microsoft, takich jak Office 365 i Intune.
* Otrzymywać raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratorów.
* Otrzymuj alerty dotyczące dostępu do roli uprzywilejowanej.

Więcej informacji:

* [Co to jest usługa Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrona tożsamości

Usługa Azure AD Identity Protection to usługa zabezpieczeń, która zapewnia skonsolidowany widok wykrywania ryzyka i potencjalnych luk w zabezpieczeniach, które wpływają na tożsamość organizacji. Usługa Identity Protection korzysta z istniejących funkcji wykrywania anomalii usługi Azure AD, które są dostępne za pośrednictwem raportów działania typu anomalous usługi Azure. Ochrona tożsamości wprowadza również nowe typy wykrywania ryzyka, które mogą wykrywać anomalie w czasie rzeczywistym.

Więcej informacji:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Kanał 9: Usługa Azure AD i pokaz tożsamości: wersja zapoznawcza ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Zarządzanie tożsamościami hybrydowymi/połączenie usługi Azure AD

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe, tworząc jedną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Nazywamy to tożsamością hybrydową. Azure AD Connect to narzędzie firmy Microsoft, które umożliwia spełnienie wymagań związanych z tożsamością hybrydową. Dzięki temu użytkownicy mogą posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD. Oferuje ono następujące funkcje:

* Synchronizacja
* Ad FS i integracja federacji
* Przekazywanie przez uwierzytelnianie
* Monitorowanie kondycji

Więcej informacji:

* [Oficjalny dokument tożsamości hybrydowej](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog zespołu usługi Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Przeglądy dostępu w usłudze Azure AD

Przeglądy dostępu w usłudze Azure Active Directory (Azure AD) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji przedsiębiorstwa i przypisaniami ról uprzywilejowanych.

Więcej informacji:

* [Przeglądy dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)
