---
title: Kit wdrażania dostępu warunkowego — usługi Azure Active Directory
description: Wdrażanie dostępu warunkowego usługi Azure AD do uzyskiwania dostępu do zasobów
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387575"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Wdrażanie dostępu warunkowego usługi Azure AD

W świecie urządzeń przenośnych i chmurze — użytkownicy mogą uzyskiwać dostęp do zasobów Twojej organizacji z dowolnego miejsca przy użyciu różnych rodzajów urządzeń i aplikacji. W rezultacie po prostu koncentrujących się na kto ma dostęp do zasobu nie jest już wystarczającej ilości. Można kontrolować, kto ma dostęp i zidentyfikować, gdzie użytkownik jest i używanego urządzenia i wiele innych.

Aby zapewnić tę kontrolkę **dostępu warunkowego usługi Azure Active Directory (AD)** pozwala określić warunki, każdy użytkownik musi spełniać, aby dostęp do aplikacji, takich jak uwierzytelnianie wieloskładnikowe (MFA). Za pomocą zasad dostępu warunkowego kontroluje sposób autoryzowanych użytkowników (użytkowników, którym przyznano dostęp do aplikacji w chmurze) dostęp do aplikacji w chmurze w określonych warunkach. Zapoznaj się [co to jest dostęp warunkowy w usłudze Azure Active Directory](overview.md#conditional-access-policies) Aby uzyskać więcej informacji.

## <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety stosowania dostępu warunkowego usługi Azure AD są:

* **Zwiększ produktywność:** Zasady dostępu warunkowego dostępu do urzędu certyfikacji umożliwiają docelowy punkt, w którym użytkownicy są monitowani o używać uwierzytelniania Wieloskładnikowego, mieć zablokowany dostęp lub wymagane do używania zaufanego urządzenia. Na przykład można ustawić zasady, np. tylko wymaganie od użytkowników uwierzytelniania Wieloskładnikowego do aplikacji, gdy poza siecią firmową. Zmniejszenie żądań uwierzytelniania Wieloskładnikowego zapewnia wydajniejsze niż w przypadku ich do uwierzytelniania Wieloskładnikowego każdorazowo, gdy zalogują się w użytkowników. Ponadto dostępu warunkowego usługi Azure AD umożliwia określenie poszczególnych użytkowników w poszczególnych zasad i tworzy również zasady specyficzne dla aplikacji.
* **Zarządzanie ryzykiem:** Włączenie zasad dostępu warunkowego umożliwia ochronę tożsamości w skali chmury, funkcje kontroli dostępu na podstawie ryzyka i obsługa natywnego uwierzytelniania wieloskładnikowego. Sprzężenia dostępu warunkowego usługa identity protection umożliwia definiowanie, gdy dostęp do aplikacji jest zablokowany lub warunkowych.
* **Adres zgodności i nadzoru:** Inspekcję żądań dostępu i zatwierdzenia dla aplikacji i zrozumienie wykorzystania cała aplikacja jest łatwiejsze dzięki usłudze Azure AD, ponieważ obsługuje on dzienników inspekcji natywne dla każdego żądania dostępu do aplikacji, wykonywane. Inspekcja obejmuje tożsamości żądającego, żądana data, uzasadnienie biznesowe, stan zatwierdzenia i tożsamość osoby zatwierdzającej. Te dane są również dostępne z interfejsu API, który umożliwi przywozu te dane do zdarzenia zabezpieczeń i podstawowy system monitorowanie zdarzeń (SIEM).
* **Zarządzanie kosztami:** Przenoszenie zasady dostępu do usługi Azure AD zmniejsza poleganie na niestandardowy lub lokalnych rozwiązań takich jak Active Directory Federation Services (ADFS) dla dostępu warunkowego, zmniejszenie kosztów obsługi tej infrastruktury.

## <a name="customer-case-studies"></a>Analizy przypadków klientów

Dowiedz się, jak większość organizacji za pomocą dostępu warunkowego usługi Azure AD definiować ani implementować decyzji dotyczących kontroli dostępu zautomatyzowane, aby dostęp do aplikacji w chmurze na podstawie warunków. Następujące polecanymi historiami pokazują, jak są spełnione tych potrzeb klienta.

* [**Wipro** dyski produktywnością rozwiązań mobilnych za pomocą narzędzia zabezpieczeń firmy Microsoft w chmurze w celu podczas pracy z klientami.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zasady dostępu warunkowego w usłudze Azure AD włączono firmy udostępniać zaufane jednostki poza---dokumentów, zasobów i aplikacji, którzy mogą korzystać z własnych poświadczeń---przy zachowaniu kontroli nad swoimi danymi firmy.
* [**Accenture** zabezpiecza jego przejście do chmury z usługą Microsoft Cloud App security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) dokonanie oceny oprogramowania Accenture [kontroli dostępu warunkowego aplikacji](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) funkcja Cloud App Security, która używa usługi Azure Active Katalog dostęp warunkowy dostęp do aplikacji bramy na podstawie określonych warunków. LePenske mówi, że ta funkcja może być przydatne w przypadku, powiedz, umożliwiające dostęp do plików tylko do odczytu podczas zabronienia pliki do pobrania.
* [**Aramex** dostarczania ograniczony - firma globalna logistyki i transport tworzy office połączonych z chmurą przy użyciu rozwiązania do zarządzania tożsamościami i dostępem](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Zapewnianie bezpiecznego dostępu została szczególnie trudne, za pomocą Aramex przez pracowników zdalnych. Firma jest teraz stosowanie dostępu warunkowego, aby umożliwić tych pracowników zdalnych dostępu do swoich aplikacji SaaS pochodzących od spoza sieci. Reguły dostępu warunkowego podejmie decyzję, czy można wymusić uwierzytelnianie wieloskładnikowe, dzięki czemu tylko osoby odpowiednich uprawnień dostępu.

Aby dowiedzieć się więcej na temat środowiska klientów i partnerów na dostępu warunkowego usługi Azure AD, odwiedź stronę - [Zobacz robią zdumiewające rzeczy z platformą Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Anonse

Usługa Azure AD odbiera ulepszenia w sposób ciągły. Aby uzyskać najnowsze informacje o najnowszych zmianach, zobacz [What's new in Azure Active Directory?](../fundamentals/whats-new.md)

Ostatnie blogi przez społeczność techniczna i dzielenia tożsamości firmy Microsoft:

* 24 września 2018 roku [dostępu warunkowego usługi Azure Active Directory w usłudze Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 września 2018 r. [dostępu warunkowego usługi Azure AD, które Kontrolki niestandardowe są w publicznej wersji zapoznawczej](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 września 2018 r. [Obsługa dostępu warunkowego usługi Azure AD dla ograniczonego dostępu za pomocą Microsoft Cloud App Security jest teraz dostępna](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 września 2018 r. [dostęp warunkowy usługi Azure AD: Zarządzana przeglądarka obsługę platform iOS i Android teraz w wersji zapoznawczej](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 września 2018 r. [dostępu warunkowego usługi Azure AD dla kodu kraju jest w publicznej wersji zapoznawczej](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 września 2018 r. [usługi Azure AD warunki użytkowania teraz dostępne](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Zasoby szkoleniowe

Skorzystaj z linków poniżej, aby uzyskać omówienie funkcji, jak usługa Azure AD warunkowego dostępu.

* Dowiedz się więcej "[co to jest dostęp warunkowy w usłudze Azure Active Directory?](overview.md)"
* Znasz "[jakie są warunki w usłudze Azure Active Directory dostępu warunkowego?](conditions.md)"
* Znasz "[co to jest warunek lokalizacji w usłudze Azure Active Directory dostępu warunkowego?](location-condition.md)"
* Znasz "[co to jest dostęp do formantów w usłudze Azure Active Directory dostępu warunkowego?](controls.md)"
* Znajdź "[co to jest co jeśli narzędzie jest dostępne w usłudze Azure Active Directory dostępu warunkowego?"](what-if-tool.md)
* Postępuj zgodnie z [najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md)

Ponadto można znaleźć następujących linków, aby uzyskać wskazówki chronić dostęp do wszystkich usług, które są zintegrowane z usługą Azure Active Directory.

* [Co to jest ochrona linii bazowej (wersja zapoznawcza)?](baseline-protection.md) Ochrona linii bazowej gwarantuje, że masz co najmniej poziomu linii bazowej zabezpieczeń włączone w danym środowisku usługi Azure Active Directory.
* [Konfiguracje tożsamości i urządzeń dostępu](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). W tym artykule opisano jak skonfigurować bezpieczny dostęp do usług w chmurze za pośrednictwem pakietu Enterprise Mobility + Security produktów przez zaimplementowanie zalecane środowiska i konfiguracji, w tym realizowania zestaw zasad dostępu warunkowego i powiązanych możliwości.
* [Informacje dotyczące ustawień usługi Azure Active Directory dostępu warunkowego](technical-reference.md). Dowiedz się więcej:
   * Jakie aplikacje używają dostępu warunkowego?
   * Jakie usługi są włączane przy użyciu dostępu warunkowego?
* [Włącz dostęp warunkowy usługi Azure Active Directory dla użytkownikom bezpieczny dostęp](https://www.youtube.com/watch?v=eLAYBwjCGoA). Obejrzyj ten film, aby dowiedzieć się, jak dostęp warunkowy odgrywa rolę w innych organizacji i Mobility Suite obciążeń.

### <a name="training-videos"></a>Filmy szkoleniowe

**Dostęp warunkowy w pakiecie Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Dostęp warunkowy oparty na urządzeniu**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Włączanie usługi Azure Active Directory dla dostępu warunkowego dla użytkownikom bezpieczny dostęp**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Kursy online

Dotyczą następujących kursów dostępu warunkowego i nie tylko na [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Zarządzanie tożsamościami projekt na platformie Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Ten kurs przeprowadzi Cię przez kluczowe elementy, o których trzeba wiedzieć, aby zaprojektować rozwiązanie zarządzania tożsamością w usłudze Azure AD." Dostęp warunkowy usługi Azure AD zostało omówione w "Using role i kontrola dostępu w usłudze Azure AD" modułu.

* Pluralsight.com: [Uwierzytelnianie projektu dla platformy Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Ten kurs wyjaśnia, jak używać usługi Azure AD, aby rozwiązać wszystkie wymagania uwierzytelniania chmury". Dostęp warunkowy usługi Azure AD zostało omówione w module "Wymagania dla różnych scenariuszy uwierzytelniania".

* Pluralsight.com: [Autoryzacja projektu dla platformy Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Ten kurs Omawia autoryzacji i opcje platformy Azure i usługi Azure AD." Dostęp warunkowy usługi Azure AD zostało omówione w module "Autoryzacja przy użyciu usługi Azure Resource Manager i usługi Azure AD".

### <a name="books"></a>Książki

* O'Reilly - [wdrażanie rozwiązań platformy Azure — wydanie.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Szybkie rozpoczynanie pracy z usługami platformy Azure i Dowiedz się, jak je wdrożyć w Twojej organizacji. Dostęp warunkowy usługi Azure AD zostało omówione w tym rozdziale [wdrażanie i synchronizacji usługi Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [doskonalenie znajomości usług infrastruktury platformy Microsoft Azure](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "W tym miejscu to wszystko czego potrzebujesz, aby zrozumieć, oceniać, wdrażania i obsługi środowisk, które wykorzystują Microsoft Azure".

## <a name="white-papers"></a>Oficjalne dokumenty

* Opublikowane 18 grudnia 2018 r [Tworzenie strategii zarządzania kontroli dostępu odporne na błędy przy użyciu usługi Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Niniejszy dokument zawiera wskazówki dotyczące strategii wiadomość organizacji może przyjąć zapewnienie odporności, aby zmniejszyć ryzyko blokady podczas nieprzewidziane przerw w działaniu.

* Opublikowane 18 września 2018 r. [zasoby dotyczące migrowania aplikacji do usługi Azure Active Directory](../manage-apps/migration-resources.md)
   * Ten dokument zawiera listę zasobów ułatwiających migrację do usługi Azure Active Directory (Azure AD) dostęp do aplikacji i uwierzytelniania.

* Opublikowana 12 lipca 2018 r. [zabezpieczeń platformy Azure i zgodności planu: Hosting w przypadku obciążeń oficjalne UK aplikacji sieci Web PaaS](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Plany usługi Azure składają się z dokumentów ze wskazówkami dotyczącymi i szablonów do automatyzacji wdrażania architektury oparte na chmurze oferowanie rozwiązań do scenariuszy, które mają akredytacji lub wymagań dotyczących zgodności.

## <a name="guidance-for-it-administrators"></a>Wskazówki dla administratorów IT

Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego. Zapoznaj się [uprawnienia roli administratora w usłudze Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Administrator IT za pomocą [dostępu warunkowego usługi Azure AD](overview.md) użytkownicy będą musieli przeprowadzić uwierzytelnienie przy użyciu usługi Azure Multi-Factor Authentication, zaloguj się za pomocą zaufanych sieci lub zaufanego urządzenia.

Oto przydatne linki, które pomogą Ci rozpocząć pracę:

* [Najlepsze rozwiązania w zakresie dostępu warunkowego w usłudze Azure Active Directory](best-practices.md)
* [Przeglądy dostępu usługa Azure AD do zarządzania użytkownikami, które zostały wykluczone z zasad dostępu warunkowego](../governance/conditional-access-exclusion.md)
* [Instrukcje: Planowanie wdrożenia dostępu warunkowego w usłudze Azure Active Directory](plan-conditional-access.md)
* [Szybki start: Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-based-mfa.md)
* [Szybki start: Wymagane warunki użytkowania, należy zaakceptować przed uzyskaniem dostępu do aplikacji w chmurze](require-tou.md)
* [Szybki start: Zablokuj dostęp po wykryciu zagrożenia sesji przy użyciu usługi Azure Active Directory dostępu warunkowego](app-sign-in-risk.md)
* [Często zadawane pytania dotyczące programu Azure AD warunkowego dostępu](faqs.md)
   * Dodatkowe pytania, możesz również wyświetlić [forum MSDN dotyczącym](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Jeśli nie możesz znaleźć odpowiedź na problem, nasze zespoły pomocy technicznej są zawsze dostępne ułatwić dalsze. Użyj [skontaktuj się z pomocą techniczną](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Samouczki

* [**Szybki start: Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego**](app-based-mfa.md)
   * Ten przewodnik Szybki Start przedstawia sposób konfigurowania zasad dostępu warunkowego usługi Azure AD, który wymaga uwierzytelniania wieloskładnikowego dla wybranej chmury aplikacji w danym środowisku.

* [**Szybki start: Wymagane warunki użytkowania, należy zaakceptować przed uzyskaniem dostępu do aplikacji w chmurze**](require-tou.md)
   * Ten przewodnik Szybki Start pokazano, jak skonfigurować zasady dostępu warunkowego usługi Azure AD, które wymagają użytkowania, należy zaakceptować dla wybranej chmury aplikacji w danym środowisku.

* [**Szybki start: Zablokuj dostęp po wykryciu zagrożenia sesji przy użyciu usługi Azure Active Directory dostępu warunkowego**](app-sign-in-risk.md)
   * Ten przewodnik Szybki Start przedstawia sposób konfigurowania zasad dostępu warunkowego, która zablokuje logowania wykryto poziom skonfigurowany ryzyka logowania.

* [Samouczek: **Migrowanie zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal**](policy-migration-mfa.md)
   * W tym samouczku pokazano, jak przeprowadzić migracji zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego (MFA) dla aplikacji w chmurze.

## <a name="end-user-readiness-and-communication"></a>Gotowość użytkownika końcowego i komunikacja

Funkcja dostępu warunkowego używa innych funkcji usługi Azure AD, które mogą mieć wpływ na środowisko użytkownika końcowego. Na przykład umożliwia uwierzytelnianie wieloskładnikowe Azure Włączanie silnego uwierzytelniania dla użytkowników. W takim przypadku zostanie użyty przez użytkownika końcowego szablonów usługi Azure MFA.

## <a name="next-steps"></a>Kolejne kroki

* Uruchom wdrożenie za pomocą [dokumentacji do planowania wdrożenia dostępu warunkowego](plan-conditional-access.md).
