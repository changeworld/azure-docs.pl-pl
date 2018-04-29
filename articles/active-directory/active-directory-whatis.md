---
title: Co to jest usługa Azure Active Directory (Azure AD)? | Microsoft Docs
description: Użyj usługi Azure Active Directory można rozszerzyć Twoje istniejące tożsamości lokalnych do chmury lub usługi Azure AD zintegrowane aplikacje.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 01183a2c85b748d593146f6b52a7d4dbd34524cb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory"></a>Co to jest usługa Azure Active Directory?
Azure Active Directory (Azure AD) jest katalogu wielodostępnej, oparte na chmurze firmy Microsoft i usługa identity management, łączącą usługi katalogowe core, zarządzania dostępem do aplikacji i ochronę tożsamości w ramach jednego rozwiązania. Usługi Azure AD oferuje rozbudowane, opartych na standardach platforma, która umożliwia deweloperom tworzenie kontroli dostępu do aplikacji, na podstawie scentralizowanych zasad i reguł.

![Stos programu Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Dla administratorów IT.** Usługa Azure AD zapewnia bardziej bezpieczne rozwiązanie dla organizacji przy użyciu silniejszych Zarządzanie tożsamościami i dostępem w rejestracji jednokrotnej (SSO) do tysięcy [chmurowych aplikacji SaaS](active-directory-saas-tutorial-list.md) i lokalnych aplikacji. Za pomocą tych aplikacji również uzyskasz zabezpieczeń aplikacji opartej na chmurze, bezproblemowy dostęp rozszerzonej współpracy i automatyzacji życia tożsamości dla pracowników, dzięki czemu można zwiększyć, zabezpieczeń i zgodności.

    Ponadto z dokładnie [cztery kliknięć](./connect/active-directory-aadconnect-get-started-express.md), usługi Azure AD można zintegrować z istniejącego systemu Windows Server Active Directory, dzięki czemu organizacji, użyj istniejącego lokalnego dostęp inwestycji tożsamości do zarządzania oparta na chmurze aplikacji SaaS .

- **Dla deweloperów aplikacji.** Azure AD pozwala skupić się na tworzeniu aplikacji przez umożliwienie zintegrować z rozwiązaniem do zarządzania tożsamościami używanym przez miliony organizacji na całym świecie.

- **W przypadku klientów usługi Office 365, Azure lub usługi Dynamics CRM Online.** Już używasz usługi Azure AD. Poszczególne dzierżawy usługi Office 365, Azure i usługi Dynamics CRM Online jest rzeczywiście dzierżawa usługi Azure AD, dzięki czemu można natychmiast rozpocząć zarządzanie pracownika dostęp do aplikacji w chmurze zintegrowanego.

## <a name="how-reliable-is-azure-ad"></a>Jak niezawodnych jest Azure AD?
Podczas projektowania wielodostępne, rozproszona geograficznie o wysokiej dostępności usługi Azure AD oznacza, że mogą polegać na nim do najważniejszych potrzeb biznesowych. Kończy centrów danych 28 na świecie z automatycznego trybu failover, konieczne będzie wygodnie wiedząc, że usługi Azure AD jest wysoce niezawodne i że nawet jeśli Centrum danych ulegnie awarii, kopie danych katalogu są na żywo w co najmniej dwóch więcej regionalnie rozproszone danych w centrach i dostępny dla natychmiastowy dostęp.

Aby uzyskać więcej informacji na temat umów dotyczących poziomu usług, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Wybierz edition
Wszystkie usługi biznesowe w witrynie Microsoft Online zależą od usługi Azure Active Directory (Azure AD) do logowania i musi innych tożsamości. Jeśli subskrybujesz usług firm Online firmy Microsoft (na przykład usługi Office 365 lub Microsoft Azure), możesz uzyskać usługi Azure AD dostęp do wszystkich funkcji wolne. Z usługi Azure Active Directory bezpłatnej wersji można zarządzać użytkownikami i grupami, synchronizować z lokalnymi katalogami, Pobierz rejestracji jednokrotnej na platformie Azure, Office 365 i tysiące popularnych aplikacji SaaS, takich jak Salesforce, produktu Workday, Concur, DocuSign, Google Aplikacje, okno usługi ServiceNow, Dropbox i więcej. 

Aby ulepszyć usługi Azure Active Directory, możesz dodać płatnych funkcji przy użyciu wersji usługi Azure Active Directory — wersja podstawowa, Premium P1 i Premium P2. Usługi Azure Active Directory płatnej wersje są zbudowane na podstawie istniejącego katalogu bezpłatne, zapewnia enterprise możliwości klasy spanning samoobsługi, rozszerzone monitorowanie, zabezpieczenia raportowania usługi Multi-Factor Authentication (MFA) i bezpieczny dostęp do sieci pracowników mobilnych.

> [!NOTE]
> Do cenników tych wersji, zobacz [Azure Active Directory cennik](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1 Premium P2 i Azure Active Directory — wersja podstawowa nie są obecnie obsługiwane w Chinach. Aby uzyskać więcej informacji na temat cen usługi Azure AD można skontaktować się z Forum usługi Azure Active Directory.
>

* **Azure Active Directory — wersja podstawowa** -przeznaczone dla pracowników zadań musi najpierw chmury, tej wersji zawiera tożsamości aplikacji skoncentrowane na chmurze dostępu i samoobsługi rozwiązań do zarządzania. W ramach wersji Podstawowa usługi Azure Active Directory uzyskujesz dostęp do funkcji zwiększających produktywność oraz redukujących koszty, takich jak zarządzanie dostępem opartym na grupach, samoobsługowe resetowanie haseł do aplikacji w chmurze oraz serwer proxy aplikacji usługi Azure Active Directory (na potrzeby publikowania lokalnych aplikacji sieci Web przy użyciu usługi Azure Active Directory), z którymi powiązana jest umowa SLA na poziomie przedsiębiorstwa gwarantująca brak awarii przez 99,9% czasu.
* **Azure Active Directory Premium P1** — mające na celu zwiększenie możliwości dostępnych dla organizacji mających więcej wymagających wymagania w zakresie zarządzania tożsamościami i dostępem, Azure Active Directory — wersja Premium dodaje tożsamości przedsiębiorstw bogate możliwości zarządzania i Umożliwia użytkownikom hybrydowego bezproblemowo uzyskiwać dostęp do lokalnych i możliwości w chmurze. Ta wersja zawiera wszystko, co jest potrzebne dla pracowników przetwarzających informacje i administratorów tożsamości w środowiskach hybrydowych na dostęp do aplikacji, sklep internetowy, Zarządzanie tożsamościami i dostępem (IAM) ochrony tożsamości i zabezpieczeń w chmurze. Obsługuje zaawansowane administracji i delegowanie zasobów, takich jak grupami dynamicznymi i Samoobsługowe zarządzanie grupami. Zawiera program Microsoft Identity Manager (lokalne tożsamości i dostępu do zarządzania pakiet), a zapewnia chmury możliwości zapisu włączania rozwiązań, takich jak samoobsługowego resetowania haseł dla użytkowników lokalnych.
* **Azure Active Directory Premium P2** -zaprojektowany z zaawansowaną ochronę dla wszystkich użytkowników i administratorów, to nową ofertę obejmuje wszystkie funkcje Azure AD Premium P1 oraz ochrony tożsamości i Privileged Identity Management. Azure Active Directory Identity Protection wykorzystuje miliardów sygnałów w celu zapewnienia ryzyka warunkowego dostępu do aplikacji i najważniejsze dane firmy. Możemy też pomóc chronić kont uprzywilejowanych z usługi Azure Active Directory Privileged Identity Management, więc można wyszukiwać i zarządzać nimi ograniczyć i monitorować administratorów oraz ich dostępu do zasobów i zapewnić dostęp w czasie, gdy jest wymagane.  

> [!NOTE]
> Wiele możliwości usługi Azure Active Directory są dostępne wersje "płatności zgodnie z rzeczywistym użyciem":
>
> * Active Directory B2C to rozwiązanie zarządzania tożsamościami i dostępem dla aplikacji dla użytkownika. Aby uzyskać więcej informacji, zobacz [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Usługa Azure Multi-Factor Authentication może służyć za pośrednictwem dla użytkownika lub dla dostawców uwierzytelniania. Aby uzyskać więcej informacji, zobacz [co to jest uwierzytelnianie wieloskładnikowe Azure?](authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Jak można rozpocząć pracę?

**Jeśli administrator IT:**

* [Wypróbuj](https://azure.microsoft.com/trial/get-started-active-directory/) — można Załóż bezpłatne 30-dniowej wersji próbnej już dzisiaj i wdrażać pierwsze rozwiązanie chmury w obszarze pięć minut za pomocą tego łącza

* Odczyt [wprowadzenie do korzystania z usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) dla dzierżawy i uruchomić szybkie porady i wskazówki dotyczące konfigurowania usługi Azure AD

**Jeśli jesteś deweloperem:**
 
* Zapoznaj się z [przewodnik dla deweloperów](active-directory-developers-guide.md) do usługi Azure Active Directory

* [Rozpocznij okres próbny](https://azure.microsoft.com/trial/get-started-active-directory/) — Załóż bezpłatne 30-dniowej wersji próbnej już dzisiaj i rozpocząć Integrowanie aplikacji z usługą Azure AD

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o podstawowe informacje na temat zarządzania tożsamościami i dostępem platformy Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
