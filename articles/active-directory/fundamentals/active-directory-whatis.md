---
title: Co to jest usługa Azure Active Directory (Azure AD)? | Microsoft Docs
description: Dowiedz się, jak za pomocą usługi Azure Active Directory rozszerzyć istniejące tożsamości lokalne do chmury lub utworzyć aplikacje zintegrowane z usługą Azure AD.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 08d32a99e2f1a77bcc7a5900b47e6771c33ec356
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393325"
---
# <a name="what-is-azure-active-directory"></a>Co to jest usługa Azure Active Directory?
Azure Active Directory (Azure AD) to wielodostępna, oparta na chmurze usługa firmy Microsoft do zarządzania katalogiem i tożsamościami. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości w jednym rozwiązaniu, oferując opartą na standardach platformę, która pomaga deweloperom zapewniać kontrolę dostępu do swoich aplikacji na podstawie scentralizowanych zasad i reguł.

![Stos programu Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Korzyści z używania usługi Azure AD
Usługa Azure AD pomaga:

-   Utworzyć jedną tożsamość dla każdego użytkownika w całej firmie i zarządzać nią, synchronizując użytkowników, grupy i urządzenia dzięki programowi [Azure AD Connect](../connect/active-directory-aadconnect.md).

-   Zapewnić dostęp przy użyciu logowania jednokrotnego do aplikacji, w tym do tysięcy wstępnie zintegrowanych aplikacji SaaS, oraz zapewnić bezpieczniejszy dostęp zdalny do lokalnych aplikacji SaaS przy użyciu [serwera proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md).

-   Zabezpieczyć dostęp do aplikacji przez wymuszenie zasad [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md) na podstawie reguł zarówno dla aplikacji lokalnych, jak i dla aplikacji w chmurze.

-   Zwiększyć produktywność użytkowników dzięki [samoobsługowemu resetowaniu haseł](../user-help/user-help-reset-password.md) oraz żądaniom dostępu do aplikacji i grup przy użyciu portalu [MyApps](../user-help/active-directory-saas-access-panel-introduction.md).

-   Skorzystać z [wysokiej dostępności i niezawodności](https://docs.microsoft.com/azure/architecture/checklist/availability) globalnego rozwiązania klasy korporacyjnej umożliwiającego zarządzanie tożsamościami i dostępem w chmurze.

## <a name="who-uses-azure-ad"></a>Kto używa usługi Azure AD
Usługa Azure AD jest przeznaczona dla administratorów IT, deweloperów aplikacji oraz użytkowników usługi Office 365 lub Dynamics CRM Online albo platformy Azure.

- **Administratorzy IT.** Usługa Azure AD zwiększa bezpieczeństwo w organizacji, zapewniając silniejsze zarządzanie tożsamościami i dostęp z użyciem logowania jednokrotnego do tysięcy [aplikacji SaaS opartych na chmurze](../saas-apps/tutorial-list.md) oraz aplikacji lokalnych. Te aplikacje zapewniają także zabezpieczenia oparte na chmurze, bezproblemowy dostęp, ulepszoną współpracę, a także automatyzację cyklu życia tożsamości dla użytkowników, co poprawia bezpieczeństwo i zgodność.

    Ponadto za pomocą programu [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) możesz zintegrować usługę Azure AD z istniejącą usługą Active Directory systemu Windows Server, co umożliwia Twojej organizacji zarządzanie dostępem do aplikacji SaaS opartych na chmurze z wykorzystaniem wcześniejszych inwestycji w lokalną obsługę tożsamości.

- **Dla deweloperów aplikacji.** Usługa Azure AD pomaga skupić się na tworzeniu aplikacji przez zapewnienie integracji z rozwiązaniem do zarządzania tożsamościami używanym przez miliony organizacji na całym świecie.

- **Dla klientów usługi Office 365, platformy Azure lub usługi Dynamics CRM Online.** Korzystasz już z usługi Azure AD. Każda dzierżawa usługi Office 365 i Dynamics CRM Online oraz platformy Azure to tak naprawdę dzierżawa usługi Azure AD. Dzięki temu możesz natychmiast rozpocząć zarządzanie dostępem użytkowników do zintegrowanych aplikacji w chmurze.

## <a name="how-reliable-is-azure-ad"></a>Jak niezawodna jest usługa Azure AD?
Usługa Azure AD została zaprojektowana tak, aby była rozproszona geograficznie, wysoce dostępna i aby można było z niej korzystać z wieloma dzierżawami, co oznacza, że można na niej polegać, realizując swoje najważniejsze potrzeby biznesowe. Usługa Azure AD działa w 28 centrach danych na całym świecie i korzysta z automatycznego trybu failover. To oznacza, że nawet jeśli centrum danych ulegnie awarii, kopie Twoich danych katalogu będą aktywne w co najmniej dwóch dodatkowych centrach danych rozproszonych regionalnie i będzie można uzyskiwać do nich natychmiastowy dostęp.

Aby uzyskać więcej informacji na temat umów dotyczących poziomu usług, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Wybieranie wersji
Wszystkie usługi biznesowe online firmy Microsoft korzystają z usługi Azure AD na potrzeby logowania i realizacji innych potrzeb związanych z tożsamością. Jeśli zdecydujesz się na subskrypcję dowolnej usługi biznesowej online firmy Microsoft (na przykład usługi Office 365 lub platformy Microsoft Azure), automatycznie otrzymasz usługę Azure AD z dostępem do wszystkich funkcji w wersji bezpłatnej. Za pomocą usługi Azure Active Directory — wersja Bezpłatna możesz zarządzać użytkownikami i grupami, dokonywać synchronizacji z katalogami lokalnymi oraz umożliwiać logowanie jednokrotne do platformy Azure, usługi Office 365 i tysięcy popularnych aplikacji SaaS, takich jak Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox itd. 

Aby zwiększyć możliwości implementacji usługi Azure AD, możesz także dodać płatne funkcje, podnosząc poziom planu do wersji Podstawowa, Premium P1 lub Premium P2 usługi Azure Active Directory. Płatne wersje usługi Azure AD rozszerzają możliwości istniejącego bezpłatnego katalogu, udostępniając funkcje klasy korporacyjnej, które obejmują samoobsługę, rozszerzone monitorowanie, raportowanie zabezpieczeń, uwierzytelnianie wieloskładnikowe oraz bezpieczny dostęp dla pracowników mobilnych.

> [!NOTE]
> Aby zapoznać się z cennikami tych wersji, zobacz [Azure Active Directory — cennik](https://azure.microsoft.com/pricing/details/active-directory/). Usługi Azure Active Directory Premium P1, Premium P2 — oraz Azure Active Directory — wersja Podstawowa nie są obecnie obsługiwane w Chinach. Aby uzyskać więcej informacji na temat cennika usług Azure AD, możesz skorzystać z forum usługi Azure Active Directory.

- **Azure Active Directory — wersja Podstawowa.** Przeznaczona na potrzeby procesów roboczych obsługujących zadania wymagające najpierw przetwarzania w chmurze wersja oferuje rozwiązania umożliwiające dostęp do aplikacji skoncentrowanych na chmurze oraz samoobsługowe zarządzanie tożsamościami. W ramach wersji Podstawowa uzyskujesz dostęp do funkcji zwiększających produktywność oraz redukujących koszty, takich jak zarządzanie dostępem opartym na grupach, samoobsługowe resetowanie haseł do aplikacji w chmurze oraz serwer proxy aplikacji usługi Azure Active Directory (na potrzeby publikowania lokalnych aplikacji internetowych przy użyciu usługi Azure AD), z umową SLA klasy korporacyjnej, gwarantującą bezawaryjne działanie przez 99,9% czasu.

- **Azure Active Directory — wersja Premium P1.** Przeznaczona do wspierania organizacji w zaspokajaniu większych potrzeb związanych z zarządzaniem dostępem i tożsamościami usługa Azure Active Directory — wersja Premium oferuje dodatkowe, bogate w funkcje możliwości zarządzania tożsamościami na poziomie przedsiębiorstwa i umożliwia użytkownikom hybrydowym bezproblemowe uzyskiwanie dostępu do możliwości lokalnych i w chmurze. Ta wersja oferuje wszystko, czego potrzebujesz dla informacyjnych procesów roboczych oraz administratorów tożsamości w środowiskach hybrydowych w zakresie dostępu do aplikacji, samoobsługowego zarządzania tożsamościami i dostępem, ochrony tożsamości i bezpieczeństwa w chmurze. Obsługuje zaawansowane zasoby administracyjne i umożliwiające delegowanie, na przykład grupy dynamiczne oraz samoobsługowe zarządzanie grupami. Zawiera usługę Microsoft Identity Manager (lokalny pakiet narzędzi do zarządzania dostępem i tożsamościami) oraz zapewnia opcje zapisu zwrotnego, co umożliwia na przykład samoobsługowe resetowanie haseł przez użytkowników lokalnych.

- **Azure Active Directory — wersja Premium P2.** Zaprojektowana z myślą o zaawansowanej ochronie Twoich użytkowników i administratorów ta nowa oferta obejmuje wszystkie funkcje usługi Azure AD Premium P1, a także usługi Identity Protection i Privileged Identity Management. Usługa Azure Active Directory Identity Protection przetwarza miliardy sygnałów, aby zapewnić dostęp warunkowy na podstawie ryzyka do Twoich aplikacji oraz kluczowych danych firmowych. Pomagamy Ci również zarządzać kontami uprzywilejowanymi i chronić je dzięki usłudze Azure Active Directory Privileged Identity Management. Dzięki niej możesz odkrywać, ograniczać i monitorować konta administratorów i ich dostęp do zasobów oraz zapewnić dostęp dokładnie wtedy, gdy jest potrzebny.  

> [!NOTE]
> Wiele funkcji usługi Azure Active Directory jest dostępnych także w wersjach zakładających płatność zgodnie z rzeczywistym użyciem:<ul><li>**Azure Active Directory B2C.** Rozwiązanie do zarządzania tożsamościami i dostępem w aplikacjach udostępnianych klientom. Aby uzyskać więcej informacji, zobacz [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Azure Multi-Factor Authentication.** Używana dla poszczególnych użytkowników lub dostawców uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="as-an-admin-how-do-i-get-started"></a>Jak rozpocząć pracę jako administrator?
Zarejestruj się w celu skorzystania z 30-dniowej wersji próbnej i wdróż swoje pierwsze rozwiązanie w chmurze, zobacz [Azure Active Directory — wersja Premium (wersja próbna)](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>Jak rozpocząć pracę jako deweloper?
Zarejestruj się w celu skorzystania z 30-dniowej wersji próbnej i rozpocznij integrowanie aplikacji z usługą Azure AD, zobacz [Azure Active Directory — wersja Premium (wersja próbna)](https://azure.microsoft.com/trial/get-started-active-directory/). Aby uzyskać więcej informacji, możesz również przeczytać [Przewodnik dla deweloperów](../develop/azure-ad-developers-guide.md) usługi Azure Active Directory.

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej na temat podstawowych kwestii związanych z zarządzaniem tożsamościami i dostępem na platformie Azure](identity-fundamentals.md).

- [Integrate Azure AD with Windows Server Active directory (Integrowanie usługi Azure AD z usługą Active Directory systemu Windows Server)](../hybrid/how-to-connect-install-express.md).
