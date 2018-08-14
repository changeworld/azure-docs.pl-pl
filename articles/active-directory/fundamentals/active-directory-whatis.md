---
title: Co to jest usługa Azure Active Directory (Azure AD)? | Microsoft Docs
description: Usługa Azure Active Directory umożliwia rozszerzenie istniejących tożsamości lokalnych do chmury oraz opracowywanie aplikacji zintegrowanych z usługą Azure AD.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449998"
---
# <a name="what-is-azure-active-directory"></a>Co to jest usługa Azure Active Directory?
Azure Active Directory (Azure AD) to wielodostępna, oparta na chmurze usługa firmy Microsoft do katalogowania tożsamości i zarządzaniami nimi, która w obrębie jednego rozwiązania łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji oraz ochronę tożsamości. Usługa Azure AD oferuje także rozbudowaną platformę opartą na standardach, która umożliwia deweloperom umieszczanie w aplikacjach funkcji kontroli dostępu opartych na scentralizowanych zasadach i regułach.

![Stos programu Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Dla administratorów IT.** Usługa Azure AD zwiększa bezpieczeństwo w organizacji, zapewniając silniejsze zarządzanie tożsamościami i dostęp z użyciem logowania jednokrotnego do tysięcy [aplikacji SaaS opartych na chmurze](../saas-apps/tutorial-list.md) oraz aplikacji lokalnych. Te aplikacje zapewniają także zabezpieczenia oparte na chmurze, bezproblemowy dostęp, ulepszoną współpracę, a także automatyzację cyklu życia tożsamości dla pracowników, co poprawia bezpieczeństwo i zgodność.

    Ponadto za pomocą tylko [czterech kliknięć](./../connect/active-directory-aadconnect-get-started-express.md) możesz zintegrować usługę Azure AD z istniejącą usługą Active Directory systemu Windows Server, co umożliwia Twojej organizacji zarządzanie dostępem do aplikacji SaaS opartych na chmurze z wykorzystaniem wcześniejszych inwestycji w lokalną obsługę tożsamości.

- **Dla deweloperów aplikacji.** Usługa Azure AD pozwala skupić się na tworzeniu aplikacji, które możesz zintegrować z rozwiązaniem do zarządzania tożsamościami używanym przez miliony organizacji na całym świecie.

- **Dla klientów usługi Office 365, platformy Azure lub usługi Dynamics CRM Online.** Korzystasz już z usługi Azure AD. Każda dzierżawa usługi Office 365, Dynamics CRM Online oraz platformy Azure to tak naprawdę dzierżawa usługi Azure AD. Dzięki temu możesz natychmiast rozpocząć zarządzanie dostępem pracowników do zintegrowanych aplikacji w chmurze.

## <a name="how-reliable-is-azure-ad"></a>Jak niezawodna jest usługa Azure AD?
Usługa Azure AD została zaprojektowana tak, aby była rozproszona geograficznie, wysoce dostępna i aby można było z niej korzystać z wieloma dzierżawami, co oznacza, że można na niej polegać, realizując większość kluczowych potrzeb biznesowych. Usługa Azure AD działa w 28 centrach danych na całym świecie i ma funkcję automatycznego wywoływania trybu failover. Dzięki temu możesz mieć pewność, że nawet w przypadku awarii w jednym centrum danych kopie danych katalogu są nadal aktywne w co najmniej dwóch centrach danych w różnych regionach, i że można do nich uzyskać natychmiastowy dostęp.

Aby uzyskać więcej informacji na temat umów dotyczących poziomu usług, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Wybieranie wersji
Wszystkie usługi biznesowe online firmy Microsoft korzystają z usługi Azure Active Directory (Azure AD) do logowania i realizacji innych potrzeb związanych z tożsamością. Jeśli zdecydujesz się na subskrypcję dowolnej usługi biznesowej online firmy Microsoft (na przykład usługi Office 365 lub platformy Microsoft Azure), otrzymasz usługę Azure AD z dostępem do wszystkich funkcji w wersji bezpłatnej. Dzięki usłudze Azure Active Directory w wersji bezpłatnej możesz zarządzać użytkownikami i grupami, dokonywać synchronizacji z katalogami lokalnymi oraz umożliwiać logowanie jednokrotne do platformy Azure, usługi Office 365 i tysięcy popularnych aplikacji SaaS, takich jak Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox i inne. 

Aby zwiększyć możliwości usługi Azure Active Directory i uzyskać dostęp do płatnych funkcji, możesz zdecydować się na wykupienie usługi Azure Active Directory w wersji Podstawowej, Premium P1 lub Premium P2. Płatne wersje usługi Azure Active Directory rozszerzają możliwości istniejącego bezpłatnego katalogu, udostępniając funkcje klasy korporacyjnej, które obejmują samoobsługę, rozszerzone monitorowanie, raportowanie zabezpieczeń, usługę Multi-Factor Authentication oraz bezpieczny dostęp dla pracowników mobilnych.

> [!NOTE]
> Aby zapoznać się z cennikami tych wersji, zobacz [Azure Active Directory — cennik](https://azure.microsoft.com/pricing/details/active-directory/). Usługi Azure Active Directory Premium P1, Premium P2 — oraz Azure Active Directory — wersja Podstawowa nie są obecnie obsługiwane w Chinach. Aby uzyskać więcej informacji na temat cennika usług Azure AD, możesz skorzystać z forum usługi Azure Active Directory.
>

* Usługa **Azure AD — wersja Podstawowa** została zaprojektowana na potrzeby procesów roboczych obsługujących zadania wymagające najpierw przetwarzania w chmurze. Ta wersja oferuje rozwiązania umożliwiające dostęp do aplikacji skoncentrowanych na chmurze oraz samoobsługowego zarządzania tożsamościami. W ramach wersji Podstawowa usługi Azure Active Directory uzyskujesz dostęp do funkcji zwiększających produktywność oraz redukujących koszty, takich jak zarządzanie dostępem opartym na grupach, samoobsługowe resetowanie haseł do aplikacji w chmurze oraz serwer proxy aplikacji usługi Azure Active Directory (na potrzeby publikowania lokalnych aplikacji internetowych przy użyciu usługi Azure Active Directory), z umową SLA klasy korporacyjnej, gwarantującą bezawaryjne działanie przez 99,9% czasu.
* Usługa **Azure Active Directory — wersja Premium P1** została zaprojektowana z myślą o wspieraniu organizacji w zaspokajaniu coraz większych potrzeb związanych z zarządzaniem dostępem i tożsamościami usługa Azure Active Directory. Wersja Premium oferuje dodatkowe, bogate w funkcje możliwości zarządzania tożsamościami na poziomie przedsiębiorstwa i umożliwia użytkownikom hybrydowym bezproblemowe uzyskiwanie dostępu do możliwości lokalnych i w chmurze. Ta wersja oferuje wszystko, czego potrzebujesz dla informacyjnych procesów roboczych oraz administratorów tożsamości w środowiskach hybrydowych w zakresie dostępu do aplikacji, samoobsługowego zarządzania tożsamościami i dostępem, ochrony tożsamości i bezpieczeństwa w chmurze. Obsługuje zaawansowane zasoby administracyjne i umożliwiające delegowanie, na przykład grupy dynamiczne oraz samoobsługowe zarządzanie grupami. Zawiera usługę Microsoft Identity Manager (lokalny pakiet narzędzi do zarządzania dostępem i tożsamościami) oraz zapewnia opcje zapisu zwrotnego, co umożliwia na przykład samoobsługowe resetowanie haseł przez użytkowników lokalnych.
* Usługa **Azure Active Directory Premium P2** została zaprojektowana z myślą o zaawansowanej ochronie wszystkich użytkowników i administratorów. Ta nowa wersja obejmuje wszystkie funkcje usługi Azure AD Premium P1, a także funkcje Identity Protection i Privileged Identity Management. Usługa Azure Active Directory Identity Protection przetwarza miliardy sygnałów, aby zapewnić dostęp warunkowy na podstawie ryzyka do Twoich aplikacji oraz kluczowych danych firmowych. Pomagamy Ci również zarządzać kontami uprzywilejowanymi i chronić je dzięki usłudze Azure Active Directory Privileged Identity Management. Dzięki niej możesz odkrywać, ograniczać i monitorować konta administratorów i ich dostęp do zasobów oraz zapewnić dostęp dokładnie wtedy, gdy jest potrzebny.  

> [!NOTE]
> Wiele funkcji usługi Azure Active Directory jest dostępnych w wersjach zakładających płatność zgodnie z rzeczywistym użyciem:
>
> * Usługa Active Directory B2C to rozwiązanie do zarządzania tożsamościami i dostępem w przypadku aplikacji udostępnianych klientom. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
> * Z usługi Azure Multi-Factor Authentication można korzystać za pośrednictwem dostawców „na użytkownika” i „na uwierzytelnienie”. Aby uzyskać więcej informacji, zobacz [What is Azure Multi-Factor Authentication? (Co to jest usługa Azure Multi-Factor Authentication?)](../authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Jak można zacząć korzystać z tej usługi?

**Jeśli jesteś administratorem IT:**

* [Czas wypróbować usługę.](https://azure.microsoft.com/trial/get-started-active-directory/) Możesz zarejestrować się w 30-dniowej wersji próbnej już dzisiaj i wdrożyć swoje pierwsze rozwiązanie w chmurze w niecałe pięć minut, korzystając z tego linku.

* Przeczytaj artykuł [Wprowadzenie do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium), aby uzyskać porady i wskazówki dotyczące szybkiego uruchamiania dzierżawy usługi Azure AD.

**Jeśli jesteś deweloperem:**
 
* Zapoznaj się z [przewodnikiem dla deweloperów](../develop/azure-ad-developers-guide.md) dotyczącym usługi Azure Active Directory.

* [Rozpocznij okres próbny](https://azure.microsoft.com/trial/get-started-active-directory/) — zarejestruj się w 30-dniowej wersji próbnej już dzisiaj i rozpocznij integrowanie aplikacji z usługą Azure AD.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej na temat podstawowych kwestii związanych z zarządzaniem tożsamościami i dostępem na platformie Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
