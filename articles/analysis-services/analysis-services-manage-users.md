---
title: Uwierzytelnianie usługi Azure Analysis Services i uprawnienia użytkowników| Dokumenty firmy Microsoft
description: W tym artykule opisano, jak usługi Azure Analysis Services używają usługi Azure Active Directory (Azure AD) do zarządzania tożsamościami i uwierzytelniania użytkowników.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572764"
---
# <a name="authentication-and-user-permissions"></a>Uwierzytelnianie i uprawnienia użytkownika

Usługa Azure Analysis Services używa usługi Azure Active Directory (Azure AD) do zarządzania tożsamościami i uwierzytelniania użytkowników. Każdy użytkownik tworzący serwer usług Azure Analysis Services, który tworzy, zarządza lub łączy się z nim, musi mieć prawidłową tożsamość użytkownika w [dzierżawie usługi Azure AD](../active-directory/fundamentals/active-directory-administer.md) w tej samej subskrypcji.

Usługi Azure Analysis Services obsługują [współpracę usługi Azure AD B2B.](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) Z B2B, użytkownicy spoza organizacji mogą być zapraszani jako użytkownicy-goście w katalogu usługi Azure AD. Goście mogą pochodzić z innego katalogu dzierżawy usługi Azure AD lub dowolnego prawidłowego adresu e-mail. Po zaproszeniu i użytkownik akceptuje zaproszenie wysłane pocztą e-mail z platformy Azure, tożsamość użytkownika jest dodawany do katalogu dzierżawy. Tożsamości te można dodać do grup zabezpieczeń lub jako członkowie administratora serwera lub roli bazy danych.

![Architektura uwierzytelniania usług Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Uwierzytelnianie

Wszystkie aplikacje i narzędzia klienckie używają co najmniej jednej [bibliotek klienta](analysis-services-data-providers.md) usług Analysis Services (AMO, MSOLAP, ADOMD) do łączenia się z serwerem. 

Wszystkie trzy biblioteki klienckie obsługują zarówno przepływ interaktywny usługi Azure AD, jak i metody uwierzytelniania nieinterakcyjnego. Dwie metody nieinterakcyjne, Metody haseł usługi Active Directory i Zintegrowane uwierzytelnianie usługi Active Directory mogą być używane w aplikacjach wykorzystujących AMOMD i MSOLAP. Te dwie metody nigdy nie powodują wyskakujących okienek dialogowych.

Aplikacje klienckie, takie jak Excel i Power BI Desktop, oraz narzędzia takie jak rozszerzenie projektów SSMS i Analysis Services dla programu Visual Studio instalują najnowsze wersje bibliotek po zaktualizowaniu do najnowszej wersji. Rozszerzenia projektów programu Power BI Desktop, SSMS i Analysis Services są aktualizowane co miesiąc. Program Excel jest [aktualizowany za pomocą usługi Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizacje usługi Office 365 są rzadsze, a niektóre organizacje korzystają z odroczonego kanału, co oznacza, że aktualizacje są odroczone do trzech miesięcy.

W zależności od używanej aplikacji klienckiej lub narzędzia typ uwierzytelniania i sposób logowania mogą być różne. Każda aplikacja może obsługiwać różne funkcje do łączenia się z usługami w chmurze, takimi jak usługi Azure Analysis Services.

Usługi Power BI Desktop, Visual Studio i SSMS obsługują uniwersalne uwierzytelnianie usługi Active Directory, interaktywną metodę, która obsługuje również uwierzytelnianie wieloskładnikowe usługi Azure (MFA). Usługa Azure MFA pomaga chronić dostęp do danych i aplikacji, zapewniając jednocześnie prosty proces logowania. Zapewnia silne uwierzytelnianie z kilkoma opcjami weryfikacji (rozmowa telefoniczna, wiadomość tekstowa, karty inteligentne z pinem lub powiadomienie o aplikacji mobilnej). Interaktywne uwierzytelnianie wieloskładnikowe z usługą Azure AD może spowodować wyświetlenie okna dialogowego wyskakującym okienkiem w celu weryfikacji. **Zaleca się uwierzytelnianie uniwersalne.**

Jeśli logowanie się do platformy Azure przy użyciu konta systemu Windows i uwierzytelnianie uniwersalne nie jest zaznaczone lub dostępne (Excel), [usługi federacyjne Active Directory (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) są wymagane. Dzięki federacyjnej usłudze Azure AD i usłudze Office 365 użytkownicy są uwierzytelniani przy użyciu poświadczeń lokalnych i mogą uzyskiwać dostęp do zasobów platformy Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Serwery usług Azure Analysis Services obsługują połączenia z [usługi SSMS v17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub nowszej przy użyciu uwierzytelniania systemu Windows, uwierzytelniania hasłem usługi Active Directory i uwierzytelniania uniwersalnego usługi Active Directory. Ogólnie rzecz biorąc zaleca się używanie uwierzytelniania uniwersalnego usługi Active Directory, ponieważ:

*  Obsługuje interaktywne i nieinterakcyjne metody uwierzytelniania.

*  Obsługuje użytkowników-gości platformy Azure B2B zaproszonych do dzierżawy usługi Azure AS. Podczas łączenia się z serwerem użytkownicy-goście muszą wybrać uniwersalne uwierzytelnianie usługi Active Directory podczas łączenia się z serwerem.

*  Obsługuje uwierzytelnianie wieloskładnikowe (MFA). Usługa Azure MFA pomaga chronić dostęp do danych i aplikacji za pomocą szeregu opcji weryfikacji: połączenia telefonicznego, wiadomości tekstowych, kart inteligentnych z pinem lub powiadomień aplikacji mobilnej. Interaktywne uwierzytelnianie wieloskładnikowe z usługą Azure AD może spowodować wyświetlenie okna dialogowego wyskakującym okienkiem w celu weryfikacji.

### <a name="visual-studio"></a>Visual Studio

Visual Studio łączy się z usługami Azure Analysis Services przy użyciu uwierzytelniania uniwersalnego usługi Active Directory z obsługą usługi MFA. Użytkownicy są monitowani o zalogowanie się na platformie Azure przy pierwszym wdrożeniu. Użytkownicy muszą zalogować się na platformę Azure przy za pomocą konta z uprawnieniami administratora serwera na serwerze, na który wdrażają. Podczas logowania się na platformie Azure po raz pierwszy token jest przypisywany. Token jest buforowany w pamięci dla przyszłych połączeń.

### <a name="power-bi-desktop"></a>Power BI Desktop

Program Power BI Desktop łączy się z usługami Azure Analysis Services przy użyciu uwierzytelniania uniwersalnego usługi Active Directory z obsługą usługi MFA. Użytkownicy są monitowani o zalogowanie się na platformie Azure przy pierwszym połączeniu. Użytkownicy muszą zalogować się na platformę Azure przy za pomocą konta, które znajduje się w roli administratora serwera lub bazy danych.

### <a name="excel"></a>Excel

Użytkownicy programu Excel mogą łączyć się z serwerem przy użyciu konta systemu Windows, identyfikatora organizacji (adresu e-mail) lub zewnętrznego adresu e-mail. Zewnętrzne tożsamości poczty e-mail muszą istnieć w usłudze Azure AD jako użytkownik-gość.

## <a name="user-permissions"></a>Uprawnienia użytkowników

**Administratorzy serwera** są specyficzne dla wystąpienia serwera usługi Azure Analysis Services. Łączą się z narzędziami, takimi jak Azure Portal, SSMS i Visual Studio, aby wykonywać zadania, takie jak dodawanie baz danych i zarządzanie rolami użytkowników. Domyślnie użytkownik, który tworzy serwer jest automatycznie dodawany jako administrator serwera usług Analysis Services. Inni administratorzy mogą być dodawane przy użyciu witryny Azure portal lub SSMS. Administratorzy serwera muszą mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji. Aby dowiedzieć się więcej, zobacz [Zarządzanie administratorami serwerów](analysis-services-server-admins.md). 

**Użytkownicy bazy danych** łączą się z modelowymi bazami danych przy użyciu aplikacji klienckich, takich jak Excel lub Power BI. Użytkownicy muszą zostać dodana do ról bazy danych. Role bazy danych definiują uprawnienia administratora, procesu lub odczytu bazy danych. Ważne jest, aby zrozumieć użytkowników bazy danych w roli z uprawnieniami administratora różni się od administratorów serwera. Jednak domyślnie administratorzy serwerów są również administratorami bazy danych. Aby dowiedzieć się więcej, zobacz [Zarządzanie rolami bazy danych i użytkownikami](analysis-services-database-users.md).

**Właściciele zasobów platformy Azure**. Właściciele zasobów zarządzają zasobami dla subskrypcji platformy Azure. Właściciele zasobów mogą dodawać tożsamości użytkowników usługi Azure AD do ról właściciela lub współautora w ramach subskrypcji przy użyciu **kontroli dostępu** w witrynie Azure portal lub za pomocą szablonów usługi Azure Resource Manager. 

![Kontrola dostępu w witrynie Azure portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na tym poziomie dotyczą użytkowników lub kont, które muszą wykonywać zadania, które można wykonać w portalu lub przy użyciu szablonów usługi Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Role bazy danych

 Role zdefiniowane dla modelu tabelaryczne są rolami bazy danych. Oznacza to, że role zawierają członków składających się z użytkowników usługi Azure AD i grup zabezpieczeń, które mają określone uprawnienia, które definiują akcję tych członków można podjąć w bazie danych modelu. Rola bazy danych jest tworzona jako oddzielny obiekt w bazie danych i ma zastosowanie tylko do bazy danych, w której została utworzona.   
  
 Domyślnie podczas tworzenia nowego projektu modelu tabelarycznego, projekt modelu nie ma żadnych ról. Role można zdefiniować za pomocą okna dialogowego Menedżer ról w programie Visual Studio. Gdy role są definiowane podczas projektowania projektu modelu, są one stosowane tylko do bazy danych obszaru roboczego modelu. Po wdrożeniu modelu te same role są stosowane do wdrożonego modelu. Po wdrożeniu modelu administratorzy serwerów i baz danych mogą zarządzać rolami i członkami za pomocą usługi SSMS. Aby dowiedzieć się więcej, zobacz [Zarządzanie rolami bazy danych i użytkownikami](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Następne kroki

[Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Zarządzanie rolami i użytkownikami bazy danych](analysis-services-database-users.md)  
[Zarządzanie administratorami serwerów](analysis-services-server-admins.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  