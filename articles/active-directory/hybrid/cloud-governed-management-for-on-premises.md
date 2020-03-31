---
title: Zarządzanie usługą Azure AD w chmurze dla obciążeń lokalnych — Azure
description: W tym temacie opisano zarządzanie zarządzaniem w chmurze dla obciążeń lokalnych.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109519"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Sposób, w jaki usługa Azure AD zapewnia zarządzanie zarządzanie w chmurze dla obciążeń lokalnych

Usługa Azure Active Directory (Azure AD) to kompleksowe rozwiązanie tożsamości jako usługi (IDaaS) używane przez miliony organizacji, które obejmują wszystkie aspekty tożsamości, zarządzania dostępem i zabezpieczeń. Usługa Azure AD przechowuje ponad miliard tożsamości użytkowników i pomaga użytkownikom zalogować się i bezpiecznie uzyskać dostęp do obu:

* Zasoby zewnętrzne, takie jak Microsoft Office 365, portal Azure i tysiące innych aplikacji typu Oprogramowanie jako usługa (SaaS).
* Zasoby wewnętrzne, takie jak aplikacje w sieci firmowej i intranecie organizacji, wraz z wszelkimi aplikacjami w chmurze opracowanymi przez tę organizację.

Organizacje mogą używać usługi Azure AD, jeśli są "czystą chmurą" lub jako wdrożenie "hybrydowe", jeśli mają obciążenia lokalne. Hybrydowe wdrożenie usługi Azure AD może być częścią strategii organizacji, aby przeprowadzić migrację swoich zasobów IT do chmury lub kontynuować integrację istniejącej infrastruktury lokalnej wraz z nowymi usługami w chmurze.

W przeszłości organizacje "hybrydowe" widziały usługę Azure AD jako rozszerzenie istniejącej infrastruktury lokalnej. W tych wdrożeniach lokalna administracja nadzoru tożsamości, usługa Windows Server Active Directory lub inne systemy katalogowe wewnętrzną są punktami kontrolnymi, a użytkownicy i grupy są synchronizowane z tych systemów do katalogu w chmurze, takiego jak usługa Azure AD. Gdy te tożsamości znajdują się w chmurze, mogą być udostępniane do usługi Office 365, platformy Azure i innych aplikacji.

![Cykl życia tożsamości](media/cloud-governed-management-for-on-premises//image1.png)

W miarę jak organizacje przenoszą większą część swojej infrastruktury IT wraz z aplikacjami do chmury, wiele z nich szuka ulepszonych zabezpieczeń i uproszczonych możliwości zarządzania zarządzaniem tożsamościami jako usługą. Funkcje IDaaS dostarczane w chmurze w usłudze Azure AD przyspieszają przejście do zarządzania podlega ławowego, udostępniając rozwiązania i możliwości, które umożliwiają organizacjom szybkie przyjęcie i przeniesienie większej liczby zarządzania tożsamościami z tradycyjnych lokalnych usług systemów do usługi Azure AD, przy jednoczesnym kontynuowaniu obsługi istniejących, jak i nowych aplikacji.

W tym artykule opisano strategię firmy Microsoft dla hybrydowego identyfikatoraS i opisano, jak organizacje mogą używać usługi Azure AD dla istniejących aplikacji.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Podejście usługi Azure AD do zarządzania tożsamościami w chmurze

W miarę przechodzenia organizacji do chmury, potrzebują gwarancji, że mają kontrolę nad swoim kompletnym środowiskiem - większe bezpieczeństwo i lepszy wgląd w działania, obsługiwane przez automatyzację i proaktywne spostrzeżenia. "Zarządzanie**zarządzane w chmurze"** opisuje, jak organizacje zarządzają i zarządzają swoimi użytkownikami, aplikacjami, grupami i urządzeniami z chmury.

W tym współczesnym świecie organizacje muszą być w stanie skutecznie zarządzać na dużą skalę, ze względu na rozprzestrzenianie się aplikacji SaaS i rosnącą rolę współpracy i tożsamości zewnętrznych. Nowy krajobraz ryzyka w chmurze oznacza, że organizacja musi być bardziej responsywna — złośliwy aktor, który naruszy użytkownika chmury, może mieć wpływ na aplikacje w chmurze i lokalne.

W szczególności organizacje hybrydowe muszą mieć możliwość delegowania i automatyzacji zadań, które historycznie it robił ręcznie. Aby zautomatyzować zadania, potrzebują interfejsów API i procesów, które organizują cykl życia różnych zasobów związanych z tożsamością (użytkowników, grup, aplikacji, urządzeń), aby mogli delegować codzienne zarządzanie tymi zasobami do większej liczby osób spoza podstawowych pracowników IT. Usługa Azure AD spełnia te wymagania za pomocą zarządzania kontami użytkowników i uwierzytelniania natywnego dla użytkowników bez konieczności infrastruktury tożsamości lokalnej. Niebudowanie infrastruktury lokalnej może przynieść korzyści organizacjom, które mają nowe społeczności użytkowników, takie jak partnerzy biznesowi, którzy nie pochodzą z ich katalogu lokalnego, ale których zarządzanie dostępem ma kluczowe znaczenie dla osiągnięcia wyników biznesowych.

Ponadto zarządzanie nie jest kompletne bez zarządzania, --- i zarządzanie w tym nowym świecie jest zintegrowaną częścią systemu tożsamości, a nie dodatkiem. Zarządzanie tożsamościami daje organizacjom możliwość zarządzania cyklem życia tożsamości i dostępu między pracownikami, partnerami biznesowymi i dostawcami oraz usługami i aplikacjami.

Włączenie zarządzania tożsamościami ułatwia organizacji przejście na zarządzanie w chmurze, umożliwia skalowanie, stawia czoła nowym wyzwaniom z gośćmi oraz zapewnia głębsze spostrzeżenia i automatyzację niż to, co mieli klienci infrastruktury lokalnej. Zarządzanie w tym nowym świecie oznacza możliwość dla organizacji mieć przejrzystość, widoczność i odpowiednie kontrole dostępu do zasobów w organizacji. Dzięki usłudze Azure AD operacje zabezpieczeń i zespoły inspekcji mają wgląd w to, kto ma --- i kto powinien mieć — dostęp do zasobów w organizacji (na jakich urządzeniach), co ci użytkownicy robią z tym dostępem i czy organizacja ma i używa odpowiednich kontroli w celu usunięcia lub ograniczenia dostępu zgodnie z polityką firmy lub polityki regulacyjnej.

Nowy model zarządzania przynosi korzyści organizacjom zarówno z aplikacjami SaaS, jak i biznesowymi (LOB), ponieważ łatwiej są one zarządzać tymi aplikacjami i zabezpieczać je. Dzięki integracji aplikacji z usługą Azure AD organizacje będą mogły konsekwentnie używać i zarządzać dostępem w tożsamościach pochodzących z chmury i lokalnie. Zarządzanie cyklem życia aplikacji staje się bardziej zautomatyzowane, a usługa Azure AD zapewnia zaawansowane informacje na temat użycia aplikacji, które nie było łatwe do osiągnięcia w lokalnym zarządzaniu tożsamościami. Za pośrednictwem usługi Azure AD, grup usługi Office 365 i funkcji samoobsługowych usługi Teams organizacje mogą łatwo tworzyć grupy do zarządzania dostępem i współpracy oraz dodawać lub usuwać użytkowników w chmurze, aby umożliwić współpracę i dostęp do wymagań dotyczących zarządzania.

Wybór odpowiednich możliwości usługi Azure AD do zarządzania w chmurze zależy od aplikacji, które mają być używane i jak te aplikacje będą zintegrowane z usługą Azure AD. W poniższych sekcjach opisano podejścia do stosowania w aplikacjach zintegrowanych z usługą AD oraz aplikacje korzystające z protokołów federacyjnych (na przykład SAML, OAuth lub OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Zarządzanie zarządzane w chmurze dla aplikacji zintegrowanych z usługą AD

Usługa Azure AD usprawnia zarządzanie lokalnymi aplikacjami zintegrowanymi z usługą Active Directory w organizacji za pośrednictwem bezpiecznego dostępu zdalnego i dostępu warunkowego do tych aplikacji. Ponadto usługa Azure AD zapewnia również zarządzanie cyklem życia konta i zarządzanie poświadczeniami dla istniejących kont usługi AD użytkownika, w tym:

* **Bezpieczny dostęp zdalny i dostęp warunkowy dla aplikacji lokalnych**

W przypadku wielu organizacji pierwszym krokiem w zarządzaniu dostępem z chmury dla lokalnych aplikacji internetowych zintegrowanych z usługą AD i zdalnych aplikacji opartych na pulpitu jest wdrożenie [serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) przed tymi aplikacjami w celu zapewnienia bezpiecznego dostępu zdalnego.

Po pojedynczym logowaniu do usługi Azure AD użytkownicy mogą uzyskiwać dostęp zarówno do aplikacji w chmurze, jak i lokalnych za pośrednictwem zewnętrznego adresu URL lub wewnętrznego portalu aplikacji. Na przykład serwer proxy aplikacji zapewnia dostęp zdalny i logowanie jednokrotne do pulpitu zdalnego, programu SharePoint, a także aplikacje, takie jak Tableau i Qlik, oraz aplikacje biznesowe (LOB). Ponadto zasady dostępu [warunkowego](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) mogą obejmować wyświetlanie warunków użytkowania i [upewnienie się, że użytkownik wyraził na nie zgodę,](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) zanim będzie mógł uzyskać dostęp do aplikacji.

![Architektura serwera proxy aplikacji](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatyczne zarządzanie cyklem życia dla kont usługi Active Directory**

Zarządzanie tożsamościami pomaga organizacjom osiągnąć równowagę między *wydajnością* --- jak szybko dana osoba może mieć dostęp do potrzebnych im zasobów, na przykład po dołączeniu do organizacji? --- i *bezpieczeństwo* ---, w jaki sposób ich dostęp powinien się zmieniać w czasie, na przykład w przypadku zmiany statusu zatrudnienia tej osoby? Zarządzanie cyklem życia tożsamości jest podstawą zarządzania tożsamościami, a skuteczne zarządzanie na dużą skalę wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

W przypadku wielu organizacji cykl życia tożsamości pracowników jest powiązany z reprezentacją tego użytkownika w systemie zarządzania kapitałem ludzkim (HCM). W przypadku organizacji korzystających z programu Workday jako systemu HCM usługa Azure AD może zapewnić, że konta użytkowników w usłudze AD są [automatycznie aprowizowane i usuwane z obsługi administracyjnej dla pracowników w workday.](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) Prowadzi to do zwiększenia produktywności użytkowników dzięki automatyzacji kont pierworodnych i zarządzania ryzykiem, zapewniając, że dostęp do aplikacji jest automatycznie aktualizowany, gdy użytkownik zmienia role lub opuszcza organizację. [Plan wdrażania inicjowania](https://aka.ms/WorkdayDeploymentPlan) obsługi administracyjnej użytkownika oparty na dniu roboczym to przewodnik krok po kroku, który przeprowadzi organizacje przez najlepsze rozwiązania implementacji rozwiązania workday do usługi Active Directory inicjowania obsługi administracyjnej w procesie pięcioetapowym.

Usługa Azure AD Premium obejmuje również usługę Microsoft Identity Manager, która może importować rekordy z innych lokalnych systemów HCM, w tym SAP, Oracle eBusiness i Oracle PeopleSoft.

Współpraca między przedsiębiorstwami w coraz większym stopniu wymaga udzielenia dostępu osobom spoza organizacji. Współpraca [usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) umożliwia organizacjom bezpieczne udostępnianie swoich aplikacji i usług użytkownikom-gościom i partnerom zewnętrznym przy zachowaniu kontroli nad własnymi danymi firmowymi.

Usługa Azure AD może [automatycznie tworzyć konta w usłudze AD dla użytkowników-gości](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) w razie potrzeby, umożliwiając gościom biznesowym dostęp do lokalnych aplikacji zintegrowanych z usługą AD bez konieczności posiadania innego hasła. Organizacje mogą skonfigurować [zasady uwierzytelniania wieloskładnikowego (MFA) dla użytkowników-gości,](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)aby sprawdzić usługi MFA podczas uwierzytelniania serwera proxy aplikacji. Ponadto wszelkie [przeglądy dostępu,](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) które są wykonywane na użytkowników B2B w chmurze stosuje się do użytkowników lokalnych. Na przykład jeśli użytkownik chmury zostanie usunięty za pomocą zasad zarządzania cyklem życia, użytkownik lokalny jest również usuwany.

**Zarządzanie poświadczeniami dla kont usługi Active Directory** Samoobsługowe resetowanie hasła usługi Azure AD umożliwia użytkownikom, którzy zapomnieli hasła, ponowne uwierzytelnienie i zresetowanie haseł, ze zmienionymi hasłami [zapisanymi w lokalnej usłudze Active Directory.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) Proces resetowania hasła może również korzystać z lokalnych zasad haseł usługi Active Directory: Gdy użytkownik resetuje swoje hasło, jest sprawdzany, aby upewnić się, że spełnia lokalne zasady usługi Active Directory przed zatwierdzeniem go do tego katalogu. Samoobsługowy [plan wdrażania](https://aka.ms/deploymentplans/sspr) resetowania haseł przedstawia najlepsze rozwiązania dotyczące samodzielnego resetowania hasła dla użytkowników za pośrednictwem sieci Web i środowiska zintegrowanego z systemem Windows.

![Architektura sspr usługi Azure AD](media/cloud-governed-management-for-on-premises/image3.png)

Na koniec dla organizacji, które zezwalają użytkownikom na zmianę haseł w usłudze AD, usługi AD można skonfigurować do używania tych samych zasad haseł, które organizacja używa w usłudze Azure AD za pośrednictwem [funkcji ochrony hasłem usługi Azure AD,](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)obecnie w publicznej wersji zapoznawczej.

Gdy organizacja jest gotowa przenieść aplikację zintegrowaną z usługą AD do chmury, przenosząc system operacyjny obsługujący aplikację na platformę Azure, [usługi domenowe usługi usługi azure AD domain services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) zapewniają usługi domeny zgodne z usługą AD (takie jak dołączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM). Usługi domenowe usługi Azure AD integruje się z istniejącą dzierżawą usługi Azure AD w organizacji, umożliwiając użytkownikom logowanie się przy użyciu poświadczeń firmowych. Ponadto istniejące grupy i konta użytkowników mogą służyć do bezpiecznego dostępu do zasobów, zapewniając płynniejsze "lift-and-shift" zasobów lokalnych do usług infrastruktury platformy Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Zarządzanie zarządzane w chmurze dla lokalnych aplikacji opartych na federacji

W przypadku organizacji, która już używa lokalnego dostawcy tożsamości, przenoszenie aplikacji na usługę Azure AD umożliwia bezpieczniejszy dostęp i łatwiejsze środowisko administracyjne dla zarządzania federacją. Usługa Azure AD umożliwia konfigurowanie szczegółowych formantów dostępu dla aplikacji, w tym uwierzytelniania wieloskładnikowego platformy Azure, przy użyciu dostępu warunkowego usługi Azure AD. Usługa Azure AD obsługuje więcej funkcji, w tym certyfikaty podpisywania tokenów specyficzne dla aplikacji i konfigurowalne daty wygaśnięcia certyfikatów. Te możliwości, narzędzia i wskazówki umożliwiają organizacjom wycofanie lokalnych dostawców tożsamości. Na przykład własny system INFORMATYCZNy firmy Microsoft przeniósł 17 987 aplikacji z wewnętrznych usług federacyjnych Active Directory (AD FS) firmy Microsoft do usługi Azure AD.

![Ewolucja usługi Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Aby rozpocząć migrację aplikacji federacyjnych do usługi Azure https://aka.ms/migrateapps AD jako dostawcy tożsamości, zapoznaj się z tym zawiera łącza do:

* W opracowaniu zawierającym oficjalny dokument [Migrowanie aplikacji do usługi Azure Active Directory przedstawiono](https://aka.ms/migrateapps/whitepaper)korzyści wynikające z migracji i opisano sposób planowania migracji w czterech wyraźnie opisanych fazach: odnajdowanie, klasyfikacja, migracja i bieżące zarządzanie. Będziesz prowadzony przez to, jak myśleć o procesie i rozbić swój projekt na łatwe do spożycia kawałki. W całym dokumencie znajdują się łącza do ważnych zasobów, które pomogą Ci po drodze.

* Przewodnik po [rozwiązaniach Migracja uwierzytelniania aplikacji z usług federacyjnych Active Directory do usługi Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) bada bardziej szczegółowo te same cztery etapy planowania i wykonywania projektu migracji aplikacji. W tym przewodniku dowiesz się, jak zastosować te fazy do określonego celu przenoszenia aplikacji z usług federacyjnych Active Directory (AD FS) do usługi Azure AD.

* [Skrypt gotowości do migracji usług federacyjnych Active Directory](https://aka.ms/migrateapps/adfstools) można uruchomić na istniejących lokalnych serwerach usług federacyjnych Active Directory (AD FS) w celu określenia gotowości aplikacji do migracji do usługi Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Bieżące zarządzanie dostępem w aplikacjach chmurowych i lokalnych

Organizacje potrzebują procesu do zarządzania dostępem, który jest skalowalny. Użytkownicy nadal gromadzić prawa dostępu i kończy się poza to, co zostało początkowo aprowied dla nich. Ponadto organizacje przedsiębiorstw muszą być w stanie efektywnie skalować się, aby na bieżąco rozwijać i egzekwować zasady dostępu i kontrole.

Zazwyczaj IT deleguje decyzje o zatwierdzeniu dostępu do decydentów biznesowych. Ponadto it może zaangażować samych użytkowników. Na przykład użytkownicy, którzy uzyskują dostęp do poufnych danych klientów w aplikacji marketingowej firmy w Europie, muszą znać zasady firmy. Użytkownicy-goście mogą również nie być świadomi wymagań dotyczących obsługi danych w organizacji, do której zostali zaproszeni.

Organizacje mogą zautomatyzować proces cyklu życia dostępu za pomocą technologii, takich jak [grupy dynamiczne,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)w połączeniu z inicjowania obsługi administracyjnej użytkowników [do aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)lub [aplikacji zintegrowanych przy użyciu standardu System for Cross-Domain Identity Management (SCIM).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Organizacje mogą również kontrolować, którzy [użytkownicy-goście mają dostęp do aplikacji lokalnych.](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) Te prawa dostępu można następnie regularnie przeglądać za pomocą cyklicznych [przeglądów dostępu usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="future-directions"></a>Przyszłe kierunki

W środowiskach hybrydowych strategia firmy Microsoft polega na włączaniu wdrożeń, w których **chmura jest płaszczyzną kontroli tożsamości,** a katalogi lokalne i inne systemy tożsamości, takie jak usługa Active Directory i inne aplikacje lokalne, są celem inicjowania obsługi administracyjnej użytkownikom z dostępem. Ta strategia będzie nadal zapewniać prawa, tożsamości i dostęp w tych aplikacjach i obciążeniach, które na nich polegają. W tym stanie końcowym organizacje będą mogły zwiększyć produktywność użytkowników końcowych całkowicie z chmury.

![Architektura usługi Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozpoczynania tej podróży, zobacz <https://aka.ms/deploymentplans> plany wdrażania usługi Azure AD, znajdujące się pod adresem . Zawierają one kompleksowe wskazówki dotyczące wdrażania funkcji usługi Azure Active Directory (Azure AD). Każdy plan wyjaśnia wartość biznesową, zagadnienia dotyczące planowania, projektowania i procedur operacyjnych potrzebnych do pomyślnego wdrożenia wspólnych funkcji usługi Azure AD. Firma Microsoft stale aktualizuje plany wdrażania o najlepsze rozwiązania, których nauczyły się wdrożenia klientów i inne opinie, gdy dodamy nowe możliwości do zarządzania z chmury za pomocą usługi Azure AD.
