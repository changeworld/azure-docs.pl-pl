---
title: Tożsamość i dostęp zabezpieczeń najlepsze rozwiązania platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepszych rozwiązań do zarządzania tożsamościami i dostępem do formantu za pomocą wbudowanych możliwości platformy Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: e3fe033de05ed42d221795159461048790e1cec8
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493306"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management i kontrolę dostępu najlepsze rozwiązania dotyczące zabezpieczeń

Wiele należy wziąć pod uwagę tożsamości jako nową warstwę granicy zabezpieczeń, zajmuje ponad tę rolę z tradycyjnych multimediach sieci perspektywy. Tej ewolucji przestawnej podstawowego dla uwagi bezpieczeństwa i inwestycje pochodzą z faktu, że sieci co stają się coraz bardziej porowaty i obrony ten obwód nie może być tak skuteczne, jak one raz zostały przed rozbicie [BYOD ](http://aka.ms/byodcg) urządzeń i aplikacji w chmurze.

W tym artykule omawiane jest kolekcją usługi Azure identity management i najlepsze rozwiązania zabezpieczeń kontroli dostępu. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeniach z [usługi Azure AD](../active-directory/fundamentals/active-directory-whatis.md) i procesy, przez klientów, takich jak samodzielnie.

Dla każdego najlepszym rozwiązaniem jest Wyjaśnijmy:

* Co to jest najlepsze rozwiązanie
* Dlaczego chcesz włączyć na tym najlepszym rozwiązaniem jest
* W przypadku awarii umożliwiające najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze rozwiązanie polegające na
* Jak można dowiesz się umożliwić najlepszym rozwiązaniem jest

Ten artykuł został napisany w tym zarządzania tożsamościami platformy Azure i kontrolę dostępu, najlepsze rozwiązania dotyczące jest oparta na opinię konsensu i funkcji platformy Azure i zestawy funkcji występujących w czasie. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule zostanie zaktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

Tożsamość platformy Azure zarządzania i dostęp do kontroli zabezpieczeń najlepsze rozwiązania omówione w tym artykule obejmują:

* Scentralizowanie zarządzania tożsamościami
* Włącz logowanie jednokrotne (SSO)
* Wdrożenie zarządzania hasłami
* Wymuszanie uwierzytelniania wieloskładnikowego (MFA) dla użytkowników
* Korzystanie z kontroli dostępu opartej na rolach (RBAC)
* Kontrolowanie lokalizacji, w której zasoby są tworzone przy użyciu usługi Resource Manager
* Przewodnik dotyczący deweloperom korzystanie z tożsamościami dla aplikacji SaaS
* Aktywne monitorowanie dla podejrzanych działań

## <a name="centralize-your-identity-management"></a>Scentralizowanie zarządzania tożsamościami

Jednym ważnym krokiem do zabezpieczania tożsamości jest upewnij się, że dział IT może zarządzać kontami z jednej lokalizacji dotyczące tworzona tego konta. Chociaż większość przedsiębiorstw organizacje z branży IT konta podstawowego katalogu lokalnych, hybrydowych wdrożeń w chmurze znajdują się na rośnie, a jest ważne, że rozumiesz sposób integracji środowiska lokalnego i katalogów w chmurze oraz zapewnia bezpośrednie przejście środowisko użytkownika końcowego.

W tym celu [tożsamości hybrydowej](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) scenariuszu zaleca się dwie opcje:

* Synchronizacji katalogu lokalnego z katalogiem w chmurze za pomocą usługi Azure AD Connect
* Włącz logowanie jednokrotne za pomocą [synchronizacji skrótów haseł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [uwierzytelniania przekazywanego](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) lub Federację tożsamości lokalnych przy użyciu chmury katalogu [usługi Active Directory Usługi federacyjne](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

Organizacje, które się nie powieść do integrowania tożsamości lokalnych z ich tożsamość w chmurze środowisko zwiększone obciążenie administracyjne w zarządzaniu kontami, co zwiększa prawdopodobieństwo wystąpienia błędów i naruszeń zabezpieczeń.

Aby uzyskać więcej informacji na temat synchronizacji usługi Azure AD, zobacz artykuł [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Włącz logowanie jednokrotne (SSO)

Jeśli masz wiele katalogów, zarządzanie, staje się on administracyjne problem, nie tylko IT, ale także dla użytkowników końcowych, które musiał zapamiętywać wiele haseł. Za pomocą [logowania jednokrotnego](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) zapewnić użytkownikom możliwość używać tego samego zestawu poświadczeń do logowania i dostępu do zasobów, których potrzebują, bez względu na to w przypadku, gdy ten zasób jest znajdujących się lokalnie lub w chmurze.

Za pomocą logowania jednokrotnego, aby umożliwić użytkownikom dostęp do ich [aplikacji SaaS](../active-directory/manage-apps/what-is-single-sign-on.md) oparte na swoje konta organizacyjne w usłudze Azure AD. Ma to zastosowanie nie tylko dla aplikacji SaaS firmy Microsoft, ale również innych aplikacji, takich jak [Google Apps](../active-directory/saas-apps/google-apps-tutorial.md) i [Salesforce](../active-directory/saas-apps/salesforce-tutorial.md). Aplikację można skonfigurować w celu użycia usługi Azure AD jako [tożsamości opartej na SAML](../active-directory/fundamentals-identity.md) dostawcy. Jako formant zabezpieczeń usługi Azure AD nie będzie wystawiać tokenu, umożliwiając im zalogować się do aplikacji, chyba że przyznano im dostępu przy użyciu usługi Azure AD. Użytkownik może udostępniać bezpośrednio lub za pośrednictwem grupy są członkami.

> [!NOTE]
> usługi za pomocą logowania jednokrotnego będzie miało wpływ na sposób integracji katalogu lokalnego z katalogiem w chmurze. Jeśli chcesz, aby usługa rejestracji Jednokrotnej, będzie należy użyć Federacji, ponieważ tylko zapewni synchronizacji katalogów [tego samego logowania jednokrotnego](../active-directory/active-directory-aadconnect.md).
>
>

Organizacje, które nie Wymuszaj logowania jednokrotnego dla swoich użytkowników i aplikacji są bardziej widoczne dla scenariuszy, w której użytkownicy mają wiele haseł, co bezpośrednio zwiększa prawdopodobieństwo użytkowników ponowne używanie haseł lub przy użyciu słabe hasła.

Możesz dowiedzieć się więcej na temat logowania jednokrotnego usługi Azure AD, przeczytaj artykuł [zarządzania usług AD FS i dostosowywania za pomocą usługi Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Wdrożenie zarządzania hasłami

W scenariuszach, gdzie występuje wielu dzierżawców lub chcesz umożliwić użytkownikom [zresetować własne hasło](../active-directory/user-help/active-directory-passwords-update-your-own-password.md), jest ważne, użycie zasad zabezpieczeń odpowiednich aby zapobiec nadużyciu. Na platformie Azure możesz korzystać z funkcji resetowania haseł i dostosowanie opcji zabezpieczeń, aby spełnić wymagania biznesowe.

Jest ważne, aby uzyskać opinie od tych użytkowników i Dowiedz się więcej z ich doświadczeń, jako użytkownik próbuje wykonać te kroki. Oparte na tych środowisk, opracowania planu, aby uniknąć potencjalnych problemów, które mogą wystąpić podczas wdrażania dla większej grupy. Zalecane jest również, że używasz [raport aktywności rejestracji resetowania hasła](../active-directory/active-directory-passwords-get-insights.md) monitorować użytkowników, którzy rejestrowania.

Organizacje, które chce się uniknąć interwencji obsługi zmiany hasła, ale umożliwianie użytkownikom resetowania swoich haseł są bardziej podatne na wyższe wolumin wywołań, aby pomoc techniczna ze względu na problemy z hasłem. W organizacjach, które mają wiele dzierżaw konieczne jest implementacji tego rodzaju możliwości i umożliwia użytkownikom dokonanie resetowania w granicach zabezpieczeń, które zostały utworzone w ramach zasad zabezpieczeń.

Dowiedz się więcej na temat resetowania, przeczytaj artykuł [wdrażanie zarządzania hasłami i szkolenie użytkowników z niej korzystać](../active-directory/authentication/howto-sspr-deployment.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Wymuszanie uwierzytelniania wieloskładnikowego (MFA) dla użytkowników

W przypadku organizacji, które muszą być zgodne ze standardami branżowymi, takie jak [PCI DSS w wersji 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), uwierzytelnianie wieloskładnikowe to ma możliwość uwierzytelniania użytkowników. Powyżej są zgodne ze standardami branżowymi, wymuszanie uwierzytelniania Wieloskładnikowego w celu uwierzytelniania użytkowników może również pomóc organizacji w celu ograniczenia kradzieży poświadczeń rodzaj ataku, takich jak [Pass--Hash (PtH)](http://aka.ms/PtHPaper).

Po włączeniu usługi Azure MFA dla użytkowników dodawany jest używana druga warstwa zabezpieczeń do logowania użytkowników i transakcji. W tym przypadku transakcji może uzyskiwać dostęp do dokumentu znajdującego się na serwerze plików lub usługi SharePoint Online. Usługa Azure MFA pomaga również IT, aby zmniejszyć prawdopodobieństwo, że ze złamanymi zabezpieczeniami poświadczeń ma dostęp do danych organizacji.

Na przykład: Wymuś uwierzytelnianie wieloskładnikowe Azure dla użytkowników i skonfigurować go do korzystania z połączenia telefonicznego lub wiadomości SMS jako weryfikacji. Poświadczenia użytkownika w przypadku naruszenia zabezpieczeń, osoba atakująca nie jest w stanie uzyskać dostęp do dowolnego zasobu, ponieważ nie mają dostępu do telefonu użytkownika. Organizacje, które nie należy dodawać dodatkowych warstw ochrony tożsamości są bardziej podatne na ataku kradzieży poświadczeń, co może prowadzić do naruszenia zabezpieczeń danych.

Jeden alternatywą dla organizacji, które chcesz zachować cały proces uwierzytelniania kontroli lokalnej jest użycie [serwer Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-deploy.md), nazywany również lokalnego uwierzytelniania Wieloskładnikowego. Za pomocą tej metody, nadal można wymusić uwierzytelnianie wieloskładnikowe, przy jednoczesnym zachowaniu lokalna Usługa MFA server.

Aby uzyskać więcej informacji na temat usługi Azure MFA, zobacz artykuł [wprowadzenie do usługi Azure Multi-Factor Authentication w chmurze](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Korzystanie z kontroli dostępu opartej na rolach (RBAC)

Ograniczanie dostępu na podstawie [trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych. Azure opartej na rolach kontrola dostępu (RBAC) może służyć do przypisywania uprawnień do użytkowników, grup i aplikacji w określonym zakresie. Zakres przypisania roli może być subskrypcją, grupą zasobów lub pojedynczy zasób.

Możesz wykorzystać [wbudowanej RBAC](../role-based-access-control/built-in-roles.md) ról na platformie Azure, aby przypisać uprawnienia do użytkowników. Należy rozważyć użycie *Współautor konta magazynu* dla operatorów chmury, które muszą zarządzać kontami magazynu i *klasycznego Współautor konta magazynu* roli, aby zarządzać kontami klasycznego magazynu. Operatorzy chmury, których potrzebuje do zarządzania maszynami wirtualnymi i konto magazynu, należy rozważyć dodanie ich do *Współautor maszyny wirtualnej* roli.

Organizacje, które nie wymusza kontrola dostępu do danych dzięki wykorzystaniu możliwości, takie jak RBAC może podając więcej uprawnień niż jest to konieczne do użytkowników. Może to prowadzić do danych naruszenia przez zezwolić użytkownikom na dostęp do niektórych typów danych (na przykład duże znaczenie biznesowe), które nie powinny mieć w pierwszej kolejności.

Znajdziesz więcej informacji na temat RBAC platformy Azure, przeczytaj artykuł [kontroli dostępu](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Kontrolowanie lokalizacji, w której zasoby są tworzone przy użyciu usługi Resource Manager

Ważne jest włączenie operatorom chmury do wykonywania zadań podczas uniemożliwia istotne konwencje, które są wymagane do zarządzania zasobami w Twojej organizacji. Organizacje, które ma być sterowana lokalizacje, w którym są tworzone zasoby powinny ciężko kodu te lokalizacje.

Aby to osiągnąć, organizacje mogą tworzyć zasady zabezpieczeń, które zawierają definicje, które opisują akcje lub zasobów, które zostaną odrzucone. Możesz przypisać te definicje zasad w określonym zakresie, np. subskrypcji, grupy zasobów lub poszczególnych zasobów.

> [!NOTE]
> to nie jest taka sama jak RBAC, rzeczywistości wykorzystuje RBAC do uwierzytelniania użytkowników, którzy mają uprawnienia do tworzenia tych zasobów.
>
>

Wykorzystaj [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) do tworzenia zasad niestandardowych również dla scenariuszy, w których organizacja chce Zezwalaj na operacje, tylko wtedy, gdy Centrum kosztu odpowiednich jest skojarzona; w przeciwnym razie mogą odrzucić żądanie.

Organizacje, które nie są kontrolowania, jak zasoby są tworzone są bardziej podatne na użytkowników, którzy mogą gróźb usługi, tworząc więcej zasobów niż jest to wymagane. Wzmacnianie ochrony procesu tworzenia zasobu jest ważnym krokiem do zabezpieczania scenariusza z wieloma dzierżawcami.

Dowiedz się więcej na temat tworzenia zasad z usługą Azure Resource Manager, zapoznając się z artykułem [co to jest usługa Azure Policy?](../azure-policy/azure-policy-introduction.md)

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Przewodnik dotyczący deweloperom korzystanie z tożsamościami dla aplikacji SaaS

Tożsamość użytkownika jest wykorzystywane w wielu sytuacjach, gdy użytkownicy uzyskują dostęp [aplikacji SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) , można zintegrować z lokalną lub katalogu w chmurze. Najpierw zalecane deweloperom używanie bezpiecznego metodologii programować te aplikacje, takie jak [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/default.aspx). Usługa Azure AD upraszcza uwierzytelniania dla deweloperów, zapewniając tożsamość jako usługa, za pomocą obsługują protokoły będące standardami branżowymi, takie jak [OAuth 2.0](http://oauth.net/2/) i [OpenID Connect](http://openid.net/connect/), a także jako typu open-source biblioteki języka dla różnych platform.

Upewnij się zarejestrować każda aplikacja, która outsources uwierzytelniania usługi Azure AD, jest to obowiązkowe procedury. Przyczyną tego jest, ponieważ usługa Azure AD wymaga do koordynowania komunikacji z aplikacją, gdy obsługa logowania jednokrotnego (SSO) lub wymiany tokenów. Sesja użytkownika wygasa po upływie okresu istnienia tokenu wystawionego przez usługę Azure AD. Zawsze należy przeprowadzić, jeśli Twoja aplikacja powinna używać tej chwili lub skróceniem tego czasu. Zmniejszenie okresu istnienia może działać ze względów bezpieczeństwa, która wymusi użytkownikom logowanie na podstawie w okresie braku aktywności.

Organizacje, które nie wymusza funkcji kontroli tożsamości dostępu do aplikacji i nie przewodnik deweloperów bezpiecznie zintegrować aplikacje z ich systemu zarządzania tożsamościami, może być bardziej podatne na credential theft rodzaj ataku, takich jak [słabe do zarządzania uwierzytelnianiem i sesji opisanego w 10 najważniejszych Otwórz sieci Web aplikacji Security Project (OWASP)](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Znajdziesz więcej informacji na temat scenariusze uwierzytelniania dla aplikacji SaaS, zapoznając się [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Aktywne monitorowanie dla podejrzanych działań

Zgodnie z opisem w [raport naruszenia danych 2016 Verizon](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), przejęcie poświadczeń są nadal rośnie i staje się jedną z najbardziej dochodowe firmom dla przestępców cybernetycznych. Z tego powodu ważne jest zapewnienie system monitor aktywną tożsamość w miejscu, które można szybko wykrywać podejrzane zachowanie działania i wyzwolić alert w celu bliższego zbadania problemu. Usługa Azure AD ma dwa główne możliwości, które pomaga organizacjom monitorować ich tożsamości: Azure AD Premium [raporty anomalii](../active-directory/active-directory-view-access-usage-reports.md) a usługą Azure AD [ochronę tożsamości](../active-directory/active-directory-identityprotection.md) możliwości.

Upewnij się używać raportów anomalii do identyfikowania próbuje zalogować [bez śledzone](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [atak siłowy](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataki względem określonego konta, próbuje zalogować się z wielu lokalizacji, zaloguj się za pomocą [ zainfekowanych urządzeń i podejrzane adresy IP. Należy pamiętać, że są to raporty. Innymi słowy musi mieć procesów i procedur w miejscu dla administratorów IT uruchomić te raporty, codziennie lub na żądanie (zwykle w scenariuszach reagowania na zdarzenia).

Z kolei usługi Azure AD identity protection jest aktywnego monitorowania systemu i jego flagi bieżące zagrożenia na swój własny pulpit nawigacyjny. Oprócz, otrzymasz także codzienne podsumowanie powiadomienia pocztą e-mail. Firma Microsoft zaleca, Dostosuj poziom ryzyka zgodnie z wymaganiami firmy. Poziom ryzyka dla zdarzenia o podwyższonym ryzyku to wskazanie (wysoki, średni lub niski) określające ważność zdarzenia o podwyższonym ryzyku. Poziom ryzyka ułatwiają użytkownikom ochronę tożsamości, priorytety akcje, które one muszą wykonać w celu zmniejszenia ryzyka dla organizacji.

Organizacje, które aktywnie monitoruje swoje systemy tożsamości są na ryzyko, że poświadczenia użytkownika naruszenia zabezpieczeń. Bez wiedzy, która podejrzanych działań są zbyt umieść przy użyciu tych poświadczeń, organizacje, nie będzie mógł rozwiązać tego rodzaju zagrożenia.
Możesz dowiedzieć się więcej na temat usługi Azure Identity protection, zapoznając się [usługi Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
