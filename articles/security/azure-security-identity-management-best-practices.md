---
title: Azure tożsamościami i dostępem najlepszych rozwiązań dotyczących zabezpieczeń | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepsze rozwiązania w zakresie zarządzania tożsamościami i kontroli dostępu przy użyciu wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: ec8eb8759e310e31c7798c54cc95f0170e95cf50
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Zarządzanie tożsamościami i dostępem kontrolować najlepszych rozwiązań dotyczących zabezpieczeń
Rozważ wiele tożsamości jako nową warstwę granic zabezpieczeń przejęcia tej roli z tradycyjnego perspektywy skoncentrowane sieci. Ten rozwój pivot głównej dla uwagi bezpieczeństwa i inwestycje pochodzą z faktu, że stały się coraz bardziej porowaty strefy sieci i obrony ten obwód nie może być tak skuteczne, jak ich raz zostały przed rozbicie [BYOD ](http://aka.ms/byodcg) urządzenia i aplikacje w chmurze.

W tym artykule omówiono kolekcja zarządzania tożsamość platformy Azure i najlepsze rozwiązania zabezpieczeń kontroli dostępu. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z [usługi Azure AD](../active-directory/active-directory-whatis.md) i doświadczenia klientów, takich jak samodzielnie.

Dla każdego ze względów wyjaśniamy:

* Co to jest najlepszym rozwiązaniem
* Dlaczego chcesz włączyć tej najlepsze praktyki
* Jeśli nie zostanie włączone najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze praktyki
* Jak można znaleźć umożliwiające najlepsze praktyki

Ten zarządzania tożsamość platformy Azure i kontrolę dostępu, najlepsze rozwiązania dotyczące jest określany na podstawie opinii konsensu i funkcji platformy Azure i zestawy funkcji istniejących w chwili został napisany w tym artykule. Opinie i technologie ulegną zmianom, a ten artykuł będzie aktualizowany na bieżąco w celu odzwierciedlenia tych zmian.

Tożsamość platformy Azure zarządzania i dostęp do sterowania najlepsze rozwiązania omówione w tym artykule obejmują:

* Scentralizowane zarządzanie tożsamościami
* Włącz logowanie jednokrotne (SSO)
* Wdrażanie zarządzania hasłami
* Wymusić uwierzytelnianie wieloskładnikowe (MFA) dla użytkowników
* Kontrola dostępu (RBAC) oparta na rolach użycia
* Lokalizacje kontroli, gdy zasoby są tworzone za pomocą Menedżera zasobów
* Przewodnik deweloperów wykorzystać możliwości tożsamości dla aplikacji SaaS
* Aktywne monitorowanie dla podejrzanych działań

## <a name="centralize-your-identity-management"></a>Scentralizowane zarządzanie tożsamościami
Jeden ważnym krokiem do zabezpieczania tożsamości jest upewnij się, że dział IT zarządzanie kontami z jednej lokalizacji pojedynczego, o której utworzono to konto. Podczas gdy większość przedsiębiorstw IT organizacji będzie ich kontem podstawowym katalogu lokalnego, chmury hybrydowej są rośnie i jest, że rozumiesz, jak zintegrować lokalne i katalogi w chmurze oraz zapewnia bezpośrednie przejście środowisko użytkownika końcowego.

W tym celu [tożsamość hybrydowa](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) scenariuszu zaleca się dwie opcje:

* Synchronizacji katalogu lokalnego z katalogiem chmury, za pomocą programu Azure AD Connect
* Włącz logowanie jednokrotne z [synchronizacji skrótu hasła](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [uwierzytelniania przekazywanego](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) lub Federację tożsamości lokalnych z katalogu chmurze przy użyciu [usługi Active Directory Usługi federacyjne](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

Organizacje, które nie integrowanie tożsamości lokalnych z ich tożsamość w chmurze może wystąpić Zwiększa nakład pracy administracyjnej przy zarządzaniu kontami, co zwiększa prawdopodobieństwo wystąpienia błędów i naruszeń zabezpieczeń.

Więcej informacji o synchronizacji usługi Azure AD, przeczytaj artykuł [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Włącz logowanie jednokrotne (SSO)
Jeśli masz wiele katalogów, aby zarządzać staje się on administracyjne problem nie tylko w przypadku IT, ale także dla użytkowników końcowych, które będzie musiał zapamiętywać wiele haseł. Za pomocą [logowania jednokrotnego](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) będzie zapewnić użytkownikom możliwość użycia tego samego zestawu poświadczeń logowania i dostępu do zasobów, które potrzebują, bez względu na to w przypadku tego zasobu znajdujących się lokalnie lub w chmurze.

Użyj logowania jednokrotnego, aby umożliwić użytkownikom dostęp do ich [aplikacji SaaS](../active-directory/active-directory-appssoaccess-whatis.md) oparte na swoje konta organizacyjne w usłudze Azure AD. Ma to zastosowanie nie tylko dla aplikacji SaaS firmy Microsoft, ale także inne aplikacje, takie jak [usługi Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) i [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Aplikację można skonfigurować przy użyciu usługi Azure AD jako [tożsamość oparta na SAML](../active-directory/fundamentals-identity.md) dostawcy. Jako formant zabezpieczeń usługi Azure AD nie będzie wystawiać token, dzięki czemu mogą zalogować się do aplikacji, chyba że przyznano im dostęp przy użyciu usługi Azure AD. Możesz udzielić dostępu bezpośrednio lub za pośrednictwem grupy są członkami.

> [!NOTE]
> decyzji o użyciu rejestracji Jednokrotnej będzie mieć wpływ na sposób integracji katalogu lokalnego z katalogiem w chmurze. Jeśli chcesz, aby usługa rejestracji Jednokrotnej, zostanie należy użyć Federacji, ponieważ udostępni jedynie synchronizacji katalogów [tego samego środowiska logowania jednokrotnego](../active-directory/active-directory-aadconnect.md).
>
>

Organizacje, które nie wymuszają logowania jednokrotnego dla swoich użytkowników i aplikacje są widoczne więcej różnych scenariuszy, w których użytkownicy będą mieć wiele haseł, które bezpośrednio zwiększa prawdopodobieństwo użytkownikom ponowne użycie hasła lub przy użyciu słabe hasła.

Możesz można dowiedzieć się więcej o rejestracji Jednokrotnej programu Azure AD przeczytaj artykuł [zarządzania usług AD FS i dostosowywania z programem Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Wdrażanie zarządzania hasłami
W scenariuszach, w którym masz wielu dzierżawców lub chcesz umożliwić użytkownikom [zresetować swoje hasła](../active-directory/active-directory-passwords-update-your-own-password.md), należy użyć zasad zabezpieczeń odpowiednich aby uniemożliwić nadużycia. W systemie Azure można korzystać z możliwości resetowania hasła samoobsługi i dostosować opcje zabezpieczeń w celu spełnienia wymagań biznesowych.

Jest szczególnie ważne uzyskać opinie od tych użytkowników i Dowiedz się ze swoimi doświadczeniami jako próby wykonania tych czynności. W oparciu o te funkcje, opracowania planu, aby ograniczyć potencjalne problemy, które mogą wystąpić podczas wdrażania większa grupa. Zalecane jest również, że używasz [raport aktywności rejestracji resetowania haseł](../active-directory/active-directory-passwords-get-insights.md) monitorowanie użytkowników, którzy rejestracji.

Organizacje, aby uniknąć wywołań obsługi zmiany hasła, które umożliwiają użytkownikom resetować swoje hasła są bardziej narażony na wyższe wezwań do działu z powodu problemów z hasłem. W organizacjach z wieloma dzierżawcami konieczne jest wdrożenie tego typu możliwości i umożliwia użytkownikom dokonanie resetowania w granicach zabezpieczeń, które zostały określone w zasadach zabezpieczeń hasła.

Dowiedz się więcej o Resetowanie hasła przy przeczytaniu artykułu [wdrażanie Zarządzanie hasłami oraz szkolenie użytkowników, aby użyć go](../active-directory/active-directory-passwords-best-practices.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Wymusić uwierzytelnianie wieloskładnikowe (MFA) dla użytkowników
W przypadku organizacji, które muszą być zgodne ze standardami branżowymi, takich jak [PCI DSS w wersji 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), uwierzytelnianie wieloskładnikowe jest koniecznością ma możliwość uwierzytelniania użytkowników. Poza jest zgodny ze standardami branżowymi, wymuszając uwierzytelnianie wieloskładnikowe w celu uwierzytelniania użytkowników może również pomóc organizacji w celu ograniczenia typu kradzieży poświadczeń ataku, takich jak [Pass--Hash (PtH)](http://aka.ms/PtHPaper).

Przez włączenie usługi Azure MFA dla użytkowników, dodajesz drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. W takim przypadku transakcji może uzyskiwać dostęp do dokumentu znajdującego się na serwerze plików lub w trybie Online programu SharePoint. Usługa Azure MFA ułatwia również IT, aby zmniejszyć prawdopodobieństwo, że przejęciem poświadczeń dostępu do danych organizacji.

Na przykład: Wymuszanie usługi Azure MFA dla użytkowników i skonfigurować go do korzystania z połączenia telefonicznego lub wiadomości tekstowej jako weryfikacji. Naruszenie poświadczenia użytkownika, atakujący będzie mógł dostęp do dowolnych zasobów, ponieważ on nie ma dostępu do telefonu użytkownika. Organizacje, które nie należy dodawać dodatkowych warstw ochrony tożsamości są bardziej narażony na atak kradzieży poświadczeń, co może prowadzić do naruszenia danych.

Jeden alternatywą dla organizacji, które mają być zachowane cały proces uwierzytelniania sterowania lokalnej jest użycie [serwera usługi Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), nazywany również lokalne usługi MFA. Za pomocą tej metody, nadal będzie mógł wymusić uwierzytelnianie wieloskładnikowe, przy zachowaniu MFA serwera lokalnego.

Aby uzyskać więcej informacji dotyczących usługi Azure MFA, przeczytaj artykuł [wprowadzenie do korzystania z usługi Azure Multi-Factor Authentication w chmurze](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Kontrola dostępu (RBAC) oparta na rolach użycia
Ograniczanie dostępu na podstawie [, trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które mają zostać wymuszone zasady zabezpieczeń dla dostępu do danych. Azure opartej na rolach kontroli dostępu (RBAC) może służyć do przypisywania uprawnień do użytkowników, grup i aplikacji w określonego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji.

Można wykorzystać [wbudowane RBAC](../role-based-access-control/built-in-roles.md) ról na platformie Azure, aby przypisać uprawnienia do użytkowników. Należy rozważyć użycie *współautora konta magazynu* dla operatorów chmury, które muszą zarządzać kontami magazynu i *klasycznego współautora konta magazynu* roli do zarządzania klasycznych kont magazynu. Operatorzy chmury, które musi zarządzać maszynami wirtualnymi i konto magazynu, należy rozważyć dodanie ich do *Współautor·maszyny·wirtualnej* roli.

Organizacje, które nie wymusić kontrolę dostępu danych dzięki wykorzystaniu możliwości, takie jak RBAC może nadanie więcej uprawnień niż jest to niezbędne do ich użytkowników. Może to prowadzić do danych naruszenia przez zezwolić użytkownikom na dostęp do niektórych typów danych (np. duże znaczenie biznesowe), które nie powinny mieć w pierwszej kolejności.

Użytkownik może dowiedzieć się więcej o Azure RBAC przeczytaj artykuł [kontroli dostępu](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Lokalizacje kontroli, gdy zasoby są tworzone za pomocą Menedżera zasobów
Bardzo ważne jest włączenie operatorom chmury do wykonywania zadań podczas uniemożliwia zasady, które są wymagane do zarządzania zasobami organizacji dzielenia. Organizacje, które chcesz kontrolować lokalizacje, w którym zasoby są tworzone twarde powinny kodu tych lokalizacji.

Aby to osiągnąć, organizacje mogą tworzyć zasady zabezpieczeń, które zawierają definicje, które opisują akcje lub zasobów, które nie są specjalnie. Można przypisać te definicje zasad na żądany zakres, takich jak subskrypcji, grupy zasobów lub pojedynczych zasobów.

> [!NOTE]
> to nie jest taka sama jak RBAC, faktycznie wykorzystuje RBAC do uwierzytelniania użytkowników, którzy mają uprawnienia do tworzenia tych zasobów.
>
>

Wykorzystywanie [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) do tworzenia niestandardowych zasad, również dla scenariuszy, w którym organizacja chce zezwolić operacje, tylko wtedy, gdy skojarzone Centrum kosztów odpowiedniego; w przeciwnym razie zostanie odrzuciła żądanie.

Organizacje, które nie są kontrolowanie sposobu tworzenia zasobów są bardziej narażony na użytkowników, którzy mogą nadużyć usługi przez utworzenie więcej zasobów niż jest to wymagane. Wzmacnianie ochrony procesu tworzenia zasobu jest ważnym krokiem do zabezpieczania scenariusza z wieloma dzierżawcami.

Dowiedz się więcej na temat tworzenia zasad za pomocą Menedżera zasobów Azure, przeczytaj artykuł [co to jest Azure zasad?](../azure-policy/azure-policy-introduction.md).

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Przewodnik deweloperów wykorzystać możliwości tożsamości dla aplikacji SaaS
Tożsamość użytkownika będzie można użyć w wielu scenariuszach, gdy użytkownicy uzyskują dostęp do [aplikacji SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) którego można zintegrować z lokalnymi lub katalogu w chmurze. Najpierw i, zalecane jest deweloperom użycie bezpiecznego metodologii stworzono te aplikacje, takie jak [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Usługi Azure AD upraszcza uwierzytelniania dla deweloperów, podając tożsamości jako usługa, obsługuje protokoły przemysłowe oraz protokoły takich jak [OAuth 2.0](http://oauth.net/2/) i [OpenID Connect](http://openid.net/connect/), a także typu open source biblioteki dla różnych platform.

Upewnij się zarejestrować każda aplikacja, która outsources uwierzytelniania do usługi Azure AD, jest to procedura obowiązkowe. Przyczyna za to jest, ponieważ wymaga usługi Azure AD do koordynowania komunikacji z aplikacją, gdy obsługa logowania jednokrotnego (SSO) lub wymiana tokenów. Sesja użytkownika wygasa po wygaśnięciu ważności tokenu wystawiony przez usługę Azure AD. Zawsze należy ocenić, jeśli aplikacja powinna używać tej chwili lub ten czas można skrócić. Zmniejszenie okresu istnienia może działać jako zabezpieczenie, które zostanie wymuszone użytkownikom logowanie na podstawie w okresie braku aktywności.

Organizacje, które nie wymuszają tożsamości kontroli dostępu do aplikacji i nie przewodnik deweloperów w bezpieczny sposób Integrowanie aplikacji z ich tożsamości systemu zarządzania może być bardziej narażony na poświadczeń kradzieży rodzaj ataku, takich jak [słabe uwierzytelnianie i sesji zarządzania opisane w otwartych sieci Web aplikacji zabezpieczeń projektu (OWASP) pierwszych 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Użytkownik może dowiedzieć się więcej o scenariusze uwierzytelniania dla aplikacji SaaS odczytując [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Aktywne monitorowanie dla podejrzanych działań
Zgodnie z [raport naruszenia danych 2016 Verizon](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), przejęcie poświadczeń jest nadal się wzrostu i jedną z najbardziej dochodowe firm dla przez przestępców. Z tego powodu warto mieć system monitor aktywnej tożsamości w miejscu, które można szybko wykrywać podejrzane działania i wywoływać alert dla dalszego postępowania. Usługa Azure AD ma dwa główne możliwości, które pomaga organizacjom monitorować ich tożsamości: Azure AD Premium [raporty anomalii](../active-directory/active-directory-view-access-usage-reports.md) i Azure AD [ochronę tożsamości](../active-directory/active-directory-identityprotection.md) możliwości.

Upewnij się, że umożliwia zidentyfikowanie próbuje zarejestrować się w raportach anomalii [bez śledzone](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [siłowych](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataków do określonego konta, próbuje zalogować się w różnych lokalizacjach, należy zalogować się z [zainfekowanych urządzeń](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) i podejrzane adresy IP. Należy pamiętać, że są one raportów. Innymi słowy musi mieć procesy i procedury w miejscu dla administratorów IT uruchomić te raporty na codziennie lub na żądanie (zazwyczaj w scenariuszu odpowiedzi na zdarzenia).

Z kolei usługi Azure AD identity protection jest aktywnego monitorowania systemu i będzie flaga bieżącego ryzyka na jego własnej pulpitu nawigacyjnego. Oprócz, otrzymasz również dzienne podsumowanie powiadomienia pocztą e-mail. Firma Microsoft zaleca dostosować poziom ryzyka zgodnie z wymaganiami firmy. Poziom ryzyka dla zdarzenia ryzyka będzie wskazywać ważności zdarzenia ryzyka (wysoki, średni lub niski). Poziom ryzyka ułatwia użytkownikom ochronę tożsamości priorytety akcje, które one należy wykonać, aby zmniejszyć ryzyko w organizacji.

Organizacje, które aktywnie nie monitorowania systemów tożsamości się ryzyko naruszenia zabezpieczeń poświadczeń użytkownika. Bez wiedzy, która podejrzanych działań są zbyt umieść przy użyciu tych poświadczeń, organizacje nie będzie można ograniczyć zagrożenie tego typu.
Możesz można dowiedzieć się więcej o ochronę tożsamości Azure odczytując [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
