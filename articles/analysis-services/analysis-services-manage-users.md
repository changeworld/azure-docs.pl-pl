---
title: Uwierzytelnianie i uprawnienia użytkownika w usługach Azure Analysis Services | Dokumentacja firmy Microsoft
description: Więcej informacji o uwierzytelnianie i uprawnienia użytkownika w usługach Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7673b115a3ad2e6ca7aec34b1cfabfb38d2a16f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859213"
---
# <a name="authentication-and-user-permissions"></a>Uwierzytelnianie i uprawnienia użytkownika

Usługa Azure Analysis Services używa usługi Azure Active Directory (Azure AD) do uwierzytelniania tożsamości zarządzania i użytkownika. Dowolny użytkownik, tworzenia, zarządzania lub nawiązywanie połączeń z usług Azure Analysis Services server musi mieć tożsamości prawidłowego użytkownika w [dzierżawy usługi Azure AD](../active-directory/fundamentals/active-directory-administer.md) w tej samej subskrypcji.

Usługa Azure Analysis Services obsługują [współpracy B2B usługi Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Przy użyciu B2B użytkowników spoza organizacji mogą być zapraszani jako goście w katalogu usługi Azure AD. Goście mogą pochodzić z innego katalogu dzierżawy usługi Azure AD lub dowolnego adresu e-mail. Gdy zaproszenie i użytkownika o akceptacji wysłane zaproszenie za pośrednictwem poczty e-mail z platformy Azure, tożsamość użytkownika jest dodawany do katalogu dzierżawy. Tych tożsamości można dodawać do grup zabezpieczeń lub jako członkowie roli administratora lub bazy danych serwera.

![Architektura uwierzytelniania w usłudze Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication

Wszystkie aplikacje klienckie i narzędzia używania jednej lub kilku usług Analysis Services [biblioteki klienckie](analysis-services-data-providers.md) (MSOLAP, AMO ADOMD), aby nawiązać połączenie z serwerem. 

Wszystkie trzy biblioteki klienta obsługują usługi Azure AD interaktywnego przepływu i uwierzytelnianie nieinterakcyjne metody. Dwie metody-interactive, Active Directory hasła i zintegrowane uwierzytelnianie usługi Active Directory metody może służyć w aplikacjach, wykorzystując AMOMD i MSOLAP. Te dwie metody nigdy nie powoduje wyskakujących okien dialogowych.

Aplikacje klienckie, takie jak program Excel i Power BI Desktop i narzędzi, takich jak program SSMS i SSDT zainstaluj najnowsze wersje bibliotek podczas aktualizacji do najnowszej wersji. Power BI Desktop, program SSMS i SSDT są aktualizowane co miesiąc. Program Excel jest [aktualizowane przy użyciu usługi Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizacje usługi Office 365 są dłuższe interwały i niektóre organizacje używają odroczonego kanału, znaczenie mają być opóźniane aktualizacje się do trzech miesięcy.

W zależności od aplikacji klienckiej lub narzędzia, których używasz typ uwierzytelniania i sposób logowania mogą być inne. Każda aplikacja może obsługiwać różnych funkcji do łączenia się z usług cloud services, takich jak Azure Analysis Services.

Usługa Power BI Desktop, SSDT i SSMS obsługuje uniwersalne uwierzytelnianie usługi Active Directory, w przypadku metody interakcyjnej obsługującego usługi Azure Multi-Factor Authentication (MFA). Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji przy jednoczesnym zapewnieniu prostoty procesu logowania. Zapewnia ona silne uwierzytelnianie za pomocą kilku opcji weryfikacji (połączenie telefoniczne, wiadomość SMS, kart inteligentnych przy użyciu numeru pin lub powiadomienie aplikacji mobilnej). Interaktywne MFA za pomocą usługi Azure AD może spowodować wyskakujące okno dialogowe do sprawdzania poprawności. **Uniwersalne uwierzytelnianie jest zalecane**.

Jeśli logowanie do platformy Azure przy użyciu konta Windows i uniwersalne uwierzytelnianie nie jest zaznaczona lub dostępne (Excel), [Active Directory Federation Services (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) jest wymagana. Z Federacją, usługi Azure AD i użytkowników usługi Office 365 są uwierzytelniane przy użyciu poświadczeń lokalnych i dostęp do zasobów platformy Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Serwery usługi Azure Analysis Services obsługują połączenia z [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub nowszej za pomocą uwierzytelniania Windows, uwierzytelnianie hasłem usługi Active Directory i Active Directory Universal Authentication. Ogólnie rzecz biorąc zalecane jest, że używasz uwierzytelniania usługi Active Directory Universal, ponieważ:

*  Obsługuje uwierzytelnianie interakcyjne i nieinterakcyjnym metody.

*  Obsługuje użytkowników-gości B2B w usłudze Azure zapraszani do dzierżawy usługi AS platformy Azure. Podczas nawiązywania połączenia z serwerem, użytkownicy-goście, musisz wybrać uniwersalne uwierzytelnianie usługi Active Directory, podczas nawiązywania połączenia z serwerem.

*  Obsługuje uwierzytelnianie wieloskładnikowe (MFA). Azure MFA zabezpiecza dostęp do danych i aplikacji z użyciem wielu opcje weryfikacji: połączenie telefoniczne, wiadomość SMS, kart inteligentnych przy użyciu numeru pin lub powiadomienie aplikacji mobilnej. Interaktywne MFA za pomocą usługi Azure AD może spowodować wyskakujące okno dialogowe do sprawdzania poprawności.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)

Program SSDT łączy z usług Azure Analysis Services przy użyciu uwierzytelniania usługi Active Directory Universal Obsługa usługi MFA. Użytkownicy są monitowani o logowanie do platformy Azure, przy pierwszym wdrożeniu. Użytkownicy muszą logować na platformie Azure przy użyciu konta z uprawnieniami administratora serwera na serwerze, ich wdrażania. Podczas logowania się do platformy Azure po raz pierwszy, token jest przypisany. Program SSDT buforuje tokenu w pamięci dla przyszłych Ponowne podłączenia.

### <a name="power-bi-desktop"></a>Power BI Desktop

Usługa Power BI Desktop łączy się z obsługą uwierzytelniania Wieloskładnikowego przy użyciu uwierzytelniania usługi Active Directory Universal usług Azure Analysis Services. Użytkownicy są monitowani o logowanie do platformy Azure, w przypadku pierwszego połączenia. Użytkownicy muszą logować się do platformy Azure przy użyciu konta, które są objęte administrator serwera lub roli bazy danych.

### <a name="excel"></a>Excel

Użytkownicy programu Excel można nawiązać połączenia serwera przy użyciu konta Windows, identyfikator organizacji (adres e-mail) lub adresu zewnętrznego konta e-mail. Tożsamości zewnętrznego konta e-mail, musi istnieć w usłudze Azure AD jako użytkownik-Gość.

## <a name="user-permissions"></a>Uprawnienia użytkowników

**Administratorzy serwera** są specyficzne dla wystąpienia serwera usług Azure Analysis Services. Połączenie jest nawiązywane za pomocą narzędzi takich jak witryny Azure portal, program SSMS i SSDT do wykonywania zadań takich jak dodawanie baz danych i zarządzanie rolami użytkowników. Domyślnie użytkownik, który tworzy serwer jest automatycznie dodawany jako administrator serwera usług Analysis Services. Można dodawać innych administratorów za pomocą witryny Azure portal lub programu SSMS. Administratorzy serwera, musisz mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji. Aby dowiedzieć się więcej, zobacz [Zarządzanie administratorami serwerów](analysis-services-server-admins.md). 

**Bazy danych użytkowników** łączenie z bazami danych modelu za pomocą aplikacji klienckich, takich jak Excel lub Power BI. Należy dodać użytkowników do ról bazy danych. Role bazy danych, zdefiniuj administratora, procesu lub uprawnień do odczytu dla bazy danych. Należy zrozumieć użytkowników bazy danych w roli z uprawnieniami administratora jest inny niż administratorzy serwera. Domyślnie administratorzy serwera są jednak także administratorów baz danych. Aby dowiedzieć się więcej, zobacz [zarządzania ról bazy danych i użytkownikami](analysis-services-database-users.md).

**Właściciele zasobów platformy Azure**. Właściciele zasobów zarządzania zasobami subskrypcji platformy Azure. Właściciele zasobów można dodać tożsamości użytkownika usługi Azure AD do właściciela lub współautora ról w ramach subskrypcji za pomocą **kontroli dostępu** w witrynie Azure portal lub za pomocą szablonów usługi Azure Resource Manager. 

![Kontrola dostępu w witrynie Azure portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na tym poziomie mają zastosowanie do użytkowników lub konta, które trzeba wykonać zadania, które można wykonać w portalu lub przy użyciu szablonów usługi Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Role bazy danych

 Role zdefiniowane w modelu tabelarycznego są role bazy danych. Oznacza to role zawierają członków, składający się z usługi Azure AD użytkownicy i grupy zabezpieczeń, które mają określone uprawnienia, które definiują działania tych członków, można wykonać na bazę danych modelu. Rola bazy danych jest tworzona jako oddzielny obiekt w bazie danych i ma zastosowanie tylko do bazy danych, w której została utworzona.   
  
 Domyślnie podczas tworzenia nowego projektu modelu tabelarycznego projektu modelu nie ma żadnych ról. Role można zdefiniować przy użyciu okno dialogowe Menedżer ról w programie SSDT. Gdy role są definiowane w modelu projektu, są one stosowane tylko do bazy danych obszaru roboczego modelu. Po wdrożeniu modelu te same role są stosowane do wdrożony model. Po wdrożeniu modelu serwera i bazy danych Administratorzy mogą zarządzać rolami i elementów członkowskich przy użyciu programu SSMS. Aby dowiedzieć się więcej, zobacz [zarządzania ról bazy danych i użytkownikami](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie dostępem do zasobów przy użyciu grup usługi Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Zarządzanie rolami bazy danych i użytkowników](analysis-services-database-users.md)  
[Zarządzanie administratorami serwerów](analysis-services-server-admins.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  