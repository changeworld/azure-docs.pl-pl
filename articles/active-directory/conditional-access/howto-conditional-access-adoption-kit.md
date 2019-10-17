---
title: Zestaw metod wdrażania dostępu warunkowego — Azure Active Directory
description: Przyjmowanie dostępu warunkowego usługi Azure AD w celu uzyskania dostępu do zasobów
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
ms.openlocfilehash: c34f59c3e9f679adf8ae410f648cb7de6dba6447
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430039"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Przyjmowanie dostępu warunkowego usługi Azure AD

W świecie w chmurze mobilnej, użytkownicy mogą uzyskiwać dostęp do zasobów organizacji z dowolnego miejsca przy użyciu różnych rodzajów urządzeń i aplikacji. W rezultacie skoncentrowanie się na tym, kto może uzyskać dostęp do zasobu, nie jest już wystarczające. Można kontrolować, kto ma dostęp, oraz określić, gdzie użytkownik jest i jakie urządzenie jest używane i wiele innych.

Aby zapewnić tę kontrolkę, **Azure Active Directory (AD) dostęp warunkowy** pozwala określić warunki, jakie każdy użytkownik musi spełnić, aby uzyskać dostęp do aplikacji, na przykład Multi-Factor Authentication (MFA). Korzystanie z zasad dostępu warunkowego kontroluje, jak autoryzowani użytkownicy (Użytkownicy, którym udzielono dostępu do aplikacji w chmurze) uzyskują dostęp do aplikacji w chmurze w określonych warunkach. Aby uzyskać więcej informacji, zobacz [temat co to jest dostęp warunkowy w Azure Active Directory](overview.md) .

## <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety korzystania z dostępu warunkowego usługi Azure AD:

* **Zwiększ produktywność:** Zasady dostępu warunkowego (CA) umożliwiają kierowanie punktów, w których użytkownicy są monitowani o korzystanie z usługi MFA, mają zablokowany dostęp lub są wymagane do korzystania z zaufanego urządzenia. Można na przykład ustawić zasady, takie jak wymaganie, aby użytkownicy korzystali z uwierzytelniania wieloskładnikowego w aplikacji w przypadku wyłączenia sieci firmowej. Zmniejszenie żądań usługi MFA zapewnia użytkownikom większą produktywność niż w przypadku konieczności zalogowania się do usługi MFA. Ponadto dostęp warunkowy usługi Azure AD umożliwia określenie zasad dla poszczególnych użytkowników, a także tworzy zasady specyficzne dla aplikacji.
* **Zarządzanie ryzykiem:** Włączenie zasad dostępu warunkowego zapewnia ochronę tożsamości w skali chmury, możliwości kontroli dostępu oparte na ryzyku i natywną obsługę uwierzytelniania wieloskładnikowego. Sprzężenie dostępu warunkowego z usługą Identity Protection pozwala określić, kiedy dostęp do aplikacji jest blokowany lub bramkowo.
* **Zgodność i zarządzanie adresami:** Inspekcja żądań dostępu i zatwierdzeń aplikacji oraz zrozumienie ogólnego użycia aplikacji jest łatwiejsze w przypadku usługi Azure AD, ponieważ obsługuje ona natywne dzienniki inspekcji dla każdego wykonanego żądania dostępu do aplikacji. Inspekcja obejmuje tożsamość żądającego, żądaną datę, uzasadnienie biznesowe, stan zatwierdzenia i tożsamość osoby zatwierdzającej. Te dane są również dostępne w interfejsie API, co umożliwi przeimportowanie tych danych do wybranego systemu zdarzeń zabezpieczeń i monitorowania zdarzenia (SIEM).
* **Zarządzaj kosztami:** Przeniesienie zasad dostępu do usługi Azure AD zmniejsza się w zależności od rozwiązań niestandardowych lub lokalnych, takich jak Active Directory Federation Services (ADFS) dla dostępu warunkowego, zmniejszając koszt uruchamiania infrastruktury.

## <a name="customer-case-studies"></a>Analizy przypadków klientów

Odkryj, jak większość organizacji używa dostępu warunkowego usługi Azure AD, aby definiować i implementować zautomatyzowane decyzje dotyczące kontroli dostępu w celu uzyskania dostępu do aplikacji w chmurze na podstawie warunków. Poniższe proponowane historie pokazują, jak są spełnione te wymagania klientów.

* [**Wipro** dyski przenośne z narzędziami zabezpieczeń w chmurze firmy Microsoft, aby usprawnić zaangażowanie klientów.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zasady dostępu warunkowego w usłudze Azure AD umożliwiły firmie udostępnianie dokumentów, zasobów i aplikacji zaufanym podmiotom zewnętrznym---, którzy mogą korzystać z własnych poświadczeń---przy zachowaniu kontroli nad własnymi danymi firmowymi.
* [**Accenture** chroni swój przechodzenie do chmury przy użyciu usługi Microsoft Cloud App Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture ocenia funkcję [Kontrola dostępu warunkowego aplikacji](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) Cloud App Security, która używa Azure Active Directory warunkowego dostępu do bramy dostęp do aplikacji na podstawie określonych warunków. LePenske oznacza, że ta funkcja może być przydatna dla programu, na przykład podczas włączania dostępu do plików tylko do odczytu przy zablokowaniu plików do pobrania.
* Usługa [ **Aramex** Delivery Limited — globalna i transportowa firmy tworzy biuro połączone z chmurą z rozwiązaniem do zarządzania tożsamościami i dostępem](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Zapewnienie bezpiecznego dostępu było szczególnie trudne dzięki zdalnym pracownikom Aramex. Firma stosuje teraz dostęp warunkowy, aby umożliwić tym pracownikom zdalnym dostęp do swoich aplikacji SaaS spoza sieci. Reguła dostępu warunkowego decyduje, czy wymusić Multi-Factor Authentication, dając tylko właściwym osobom prawo dostępu.

Aby dowiedzieć się więcej na temat środowiska klienta i partnera w przypadku dostępu warunkowego usługi Azure AD, odwiedź stronę z [niezwykłymi osobami korzystającymi z platformy Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Ogłoszenia

Usługa Azure AD otrzymuje ulepszenia na bieżąco. Aby zachować aktualne informacje o najnowszych zmianach, zobacz [co nowego w Azure Active Directory?](../fundamentals/whats-new.md)

Najnowsze blogi przez społeczność techniczną i Wydział tożsamości firmy Microsoft:

* 24 września 2018, [Azure Active Directory dostępu warunkowego w Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 września 2018, [niestandardowe kontrolki dostępu warunkowego usługi Azure AD znajdują się w publicznej wersji zapoznawczej](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 września 2018 [jest dostępna obsługa dostępu warunkowego usługi Azure AD dla ograniczonego dostępu za pomocą Microsoft Cloud App Security](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/) .
* 21 września 2018, [dostęp warunkowy usługi Azure AD: Obsługa przeglądarki zarządzanej dla platform iOS/Android teraz w wersji zapoznawczej](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 września 2018, [dostęp warunkowy usługi Azure AD dla kodów krajów jest w publicznej wersji zapoznawczej](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 września 2018, [dostępne są warunki użytkowania usługi Azure AD](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Zasoby szkoleniowe

Skorzystaj z poniższych linków, aby zapoznać się z omówieniem funkcji dostępu warunkowego usługi Azure AD.

* Zapoznaj się[z tematem "co to jest dostęp warunkowy w Azure Active Directory?](overview.md)"
* Poznaj "[co to są warunki w Azure Active Directory dostęp warunkowy?](conditions.md)"
* Poznaj "[co to jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](location-condition.md)"
* Dowiedz się "[jakie są kontrole dostępu w Azure Active Directory dostęp warunkowy?](controls.md)"
* Znajdź "[co to jest narzędzie do działania w Azure Active Directory dostęp warunkowy?"](what-if-tool.md)
* Postępuj zgodnie z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md)

Ponadto Skorzystaj z poniższych linków, aby uzyskać wskazówki dotyczące ochrony dostępu do wszystkich usług zintegrowanych z usługą Azure Active Directory.

* [Konfiguracje i dostęp do urządzeń](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Opisuje sposób konfigurowania bezpiecznego dostępu do usług w chmurze za pomocą produktów Enterprise Mobility + Security, implementując zalecane środowisko i konfigurację, w tym określony zestaw zasad dostępu warunkowego i powiązane funkcje.
* [Azure Active Directory informacje o ustawieniach dostępu warunkowego](technical-reference.md). Dowiedz się
   * Jakie aplikacje korzystają z dostępu warunkowego?
   * Jakie usługi są włączone przy użyciu dostępu warunkowego?
* [Włącz Azure Active Directory dostępu warunkowego, aby zapewnić bezpieczny dostęp użytkowników](https://www.youtube.com/watch?v=eLAYBwjCGoA). Obejrzyj ten film wideo, aby dowiedzieć się, jak dostęp warunkowy odgrywa rolę w innych obciążeniach przedsiębiorstwa i pakietu mobilności.

### <a name="training-videos"></a>Filmy szkoleniowe

**Dostęp warunkowy w Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Dostęp warunkowy oparty na urządzeniach**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Włącz Azure Active Directory dostępu warunkowego dla bezpiecznego dostępu użytkowników**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Kursy online

Zapoznaj się z następującymi kursami dostępu warunkowego i więcej w witrynie [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [projektowanie zarządzania tożsamościami w Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Ten kurs przeprowadzi Cię przez najważniejsze elementy, które należy znać, aby zaprojektować rozwiązanie do zarządzania tożsamościami w usłudze Azure AD". Dostęp warunkowy usługi Azure AD został omówiony w module "Korzystanie z ról i Access Control w usłudze Azure AD".

* Pluralsight.com: [Projektuj uwierzytelnianie dla Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Ten kurs wyjaśnia, jak używać usługi Azure AD do rozwiązywania wszystkich wymagań związanych z uwierzytelnianiem w chmurze". Dostęp warunkowy usługi Azure AD jest objęty modułem "wymagania dotyczące uwierzytelniania dla różnych scenariuszy".

* Pluralsight.com: [Projektuj autoryzację dla Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Ten kurs uczy opcje autoryzacji dostępne w przypadku platformy Azure i usługi Azure AD". Dostęp warunkowy usługi Azure AD został omówiony w module "autoryzacja za pomocą Azure Resource Manager i usługi Azure AD".

### <a name="books"></a>Książki

* O'Reilly — [Implementowanie rozwiązań platformy Azure — Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Rozpocznij i zacznij korzystać z usług platformy Azure i Dowiedz się, jak wdrożyć je w organizacji. Dostęp warunkowy usługi Azure AD został omówiony w rozdziale [wdrażanie i synchronizowanie Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)".

* Wiley [usługi infrastruktury Microsoft Azure](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Oto wszystko, czego potrzebujesz, aby zrozumieć, oszacować, wdrożyć i zachować środowiska, które używają Microsoft Azure".

## <a name="white-papers"></a>Dokumentacja

* Opublikowano 18 grudnia 2018, aby [utworzyć odporną strategię zarządzania kontrolą dostępu za pomocą Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Ten dokument zawiera wskazówki dotyczące strategii, które organizacja może podjąć w celu zapewnienia odporności na zmniejszenie ryzyka związanego z blokadą w przypadku nieprzewidzianych przerw.

* Opublikowano 18 września, 2018 [zasobów na potrzeby migrowania aplikacji do Azure Active Directory](../manage-apps/migration-resources.md)
   * Ten oficjalny dokument zawiera listę zasobów, które ułatwiają migrowanie dostępu do aplikacji i ich uwierzytelniania do Azure Active Directory (Azure AD).

* Opublikowano 12 lipca 2018 [strategia zabezpieczeń i zgodności z przepisami platformy Azure: PaaS aplikacje sieci Web obsługujące oficjalne obciążenia w Wielkiej Brytanii](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Plany platformy Azure składają się z wskazówek dotyczących dokumentów i szablonów automatyzacji, które wdrażają architektury oparte na chmurze w celu oferowania rozwiązań do scenariuszy, które mają akredytacji lub wymagania dotyczące zgodności.

## <a name="guidance-for-it-administrators"></a>Wskazówki dla administratorów IT

Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego. Zapoznaj się z [uprawnieniami roli administrator w Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Jako administrator IT Użyj [dostępu warunkowego usługi Azure AD](overview.md) , aby wymagać od użytkowników uwierzytelniania przy użyciu usługi Azure Multi-Factor Authentication, logowania się z zaufanej sieci lub zaufanego urządzenia.

Oto przydatne linki ułatwiające rozpoczęcie pracy:

* [Najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md)
* [Korzystanie z przeglądów dostępu usługi Azure AD do zarządzania użytkownikami, którzy zostali wykluczeni z zasad dostępu warunkowego](../governance/conditional-access-exclusion.md)
* [Instrukcje: Planowanie wdrożenia dostępu warunkowego w Azure Active Directory](plan-conditional-access.md)
* [Szybki Start: Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji przy użyciu Azure Active Directory dostępu warunkowego](app-based-mfa.md)
* [Szybki Start: Wymagaj akceptacji warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze](require-tou.md)
* [Szybki Start: Blokuj dostęp w przypadku wykrycia ryzyka sesji przy użyciu Azure Active Directory dostępu warunkowego](app-sign-in-risk.md)
* [Często zadawane pytania dotyczące dostępu warunkowego usługi Azure AD](faqs.md)
   * Aby uzyskać dodatkowe pytania, możesz również wyświetlić [forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Jeśli nie możesz znaleźć odpowiedzi na problem, nasze zespoły pomocy technicznej są zawsze dostępne, aby pomóc Ci w dalszej próbie. Użyj [skontaktuj się z pomocą techniczną firmy Microsoft](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Samouczki

* [**Szybki Start: Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji przy użyciu Azure Active Directory dostępu warunkowego**](app-based-mfa.md)
   * Ten przewodnik Szybki Start przedstawia sposób konfigurowania zasad dostępu warunkowego usługi Azure AD, które wymagają uwierzytelniania wieloskładnikowego w przypadku wybranej aplikacji w chmurze w danym środowisku.

* [**Szybki Start: Wymagaj akceptacji warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze**](require-tou.md)
   * Ten przewodnik Szybki Start przedstawia sposób konfigurowania zasad dostępu warunkowego usługi Azure AD, które wymagają zaakceptowania warunków użytkowania dla wybranej aplikacji w chmurze w danym środowisku.

* [**Szybki Start: Blokuj dostęp w przypadku wykrycia ryzyka sesji przy użyciu Azure Active Directory dostępu warunkowego**](app-sign-in-risk.md)
   * Ten przewodnik Szybki Start przedstawia sposób konfigurowania zasad dostępu warunkowego, które blokują logowanie, gdy wykryto skonfigurowany poziom ryzyka związanego z logowaniem.

* [Samouczek: **Migrowanie zasad klasycznych, które wymagają uwierzytelniania wieloskładnikowego w Azure Portal**](policy-migration-mfa.md)
   * W tym samouczku pokazano, jak przeprowadzić migrację klasycznych zasad, które wymagają uwierzytelniania wieloskładnikowego (MFA) dla aplikacji w chmurze.

## <a name="end-user-readiness-and-communication"></a>Gotowość i komunikacja z użytkownikami końcowymi

Dostęp warunkowy korzysta z innych możliwości usługi Azure AD, które mogą mieć wpływ na środowisko użytkownika końcowego. Można na przykład użyć usługi Azure MFA Authentication, aby umożliwić użytkownikom silne uwierzytelnianie. W takim przypadku będziesz używać szablonów użytkowników końcowych usługi Azure MFA.

## <a name="next-steps"></a>Następne kroki

* Rozpocznij wdrażanie za pomocą [dokumentacji planowania wdrożenia dostępu warunkowego](plan-conditional-access.md).
