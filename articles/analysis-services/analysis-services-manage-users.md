---
title: Uprawnienia do uwierzytelniania i użytkownika w usłudze Azure Analysis Services | Dokumentacja firmy Microsoft
description: Więcej informacji na temat uwierzytelniania i uprawnień użytkowników w usłudze Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 47846af015bf02940da7a7b8a4154113aab57b08
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597634"
---
# <a name="authentication-and-user-permissions"></a>Uprawnienia do uwierzytelniania i użytkownika
Azure Analysis Services używa usługi Azure Active Directory (Azure AD) do uwierzytelniania użytkowników i zarządzania tożsamościami. Każdy użytkownik, tworzenia, zarządzania lub nawiązywania połączenia z usług Azure Analysis Services serwer musi mieć tożsamość prawidłowego użytkownika w [dzierżawy usługi Azure AD](../active-directory/active-directory-administer.md) w tej samej subskrypcji.

Azure Analysis Services obsługuje [współpracy B2B usługi Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Z B2B użytkowników spoza organizacji mogą być zapraszani jako goście w katalogu usługi Azure AD. Goście mogą pochodzić z innego katalogu dzierżawy usługi Azure AD lub dowolnego adresu e-mail. Zaproszenie raz i zaakceptowania zaproszenia wysyłane pocztą e-mail z platformy Azure, tożsamość użytkownika jest dodawane do katalogu dzierżawcy. Tych tożsamości można dodać do grupy zabezpieczeń lub jako członkowie roli administratora lub bazy danych serwera.

![Architektura uwierzytelniania w usłudze Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication
Wszystkie aplikacje klienckie i narzędzia użyj jednej lub kilku usług Analysis Services [bibliotek klienckich](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD), aby połączyć się z serwerem. 

Wszystkie trzy klienta biblioteki obsługują usługi Azure AD interaktywnego przepływu i metody nieinterakcyjnej uwierzytelniania. W aplikacjach przy użyciu AMOMD i MSOLAP można dwie metody nieinterakcyjnej, hasła usługi Active Directory i metod zintegrowane uwierzytelnianie usługi Active Directory. Te dwie metody nigdy nie powoduje podręcznych oknach dialogowych.

Aplikacje klienckie, takie jak program Excel i Power BI Desktop, i narzędzi, takich jak SSMS i narzędzi SSDT zainstaluj najnowsze wersje bibliotek podczas aktualizacji do najnowszej wersji. Power BI Desktop, SSMS i narzędzi SSDT są aktualizowane co miesiąc. Program Excel jest [aktualizacji z usługi Office 365](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Aktualizacje usługi Office 365 są rzadziej, a niektóre organizacje korzystają odroczonego kanału, znaczenie aktualizacje są odroczone górę do trzech miesięcy.

W zależności od aplikacji klienckiej lub narzędzia, których używasz typ uwierzytelniania i jak możesz się zalogować, mogą być inne. Każda aplikacja może obsługiwać różne funkcje do połączenia usługi w chmurze jak usług Azure Analysis Services.

Power BI Desktop, narzędzi SSDT i SSMS obsługuje uniwersalnych uwierzytelnianie usługi Active Directory, metody interakcyjnej, który również obsługuje Azure Multi-Factor Authentication (MFA). Azure MFA ułatwia zabezpieczenie dostępu do danych i aplikacji, zapewniając prostych procesu logowania. Zapewnia silne uwierzytelnianie za pomocą opcji weryfikacji (Rozmowa telefoniczna, wiadomość tekstowa, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej). Interakcyjne MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe do weryfikacji. **Zaleca się stosowanie uwierzytelniania uniwersalnego**.

Jeśli logowanie do platformy Azure, korzystając z konta systemu Windows i uwierzytelnianiem uniwersalnym nie jest zaznaczone lub dostępne (Excel), [Active Directory Federation Services (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md) jest wymagana. Z Federacją, usługi Azure AD i użytkowników usługi Office 365 są uwierzytelniane przy użyciu poświadczeń lokalnych i dostęp do zasobów platformy Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Serwery usług Analysis Services Azure obsługuje połączenia z [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i wyższych przy użyciu uwierzytelniania systemu Windows, uwierzytelniania hasła w usłudze Active Directory i uniwersalnych uwierzytelnianie usługi Active Directory. Ogólnie rzecz biorąc zalecane jest, że używasz uniwersalnych uwierzytelnianie usługi Active Directory, ponieważ:

*  Obsługuje metod uwierzytelniania interakcyjnym i nieinterakcyjnym.

*  Obsługuje zaproszenie do dzierżawy usługi Azure jako goście B2B usługi Azure. Podczas nawiązywania połączenia z serwerem, gości, musisz wybrać uniwersalnych uwierzytelnianie usługi Active Directory podczas nawiązywania połączenia z serwerem.

*  Obsługuje uwierzytelnianie wieloskładnikowe (MFA). Azure MFA ułatwia zabezpieczenie dostępu do danych i aplikacji z szeroką gamę opcji weryfikacji: połączenie telefoniczne, wiadomość tekstowa, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej. Interakcyjne MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe do weryfikacji.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
Program SSDT łączy do usług Azure Analysis Services za pomocą uwierzytelniania uniwersalnego usługi Active Directory z obsługą uwierzytelniania Wieloskładnikowego. Użytkownicy są monitowani o logowanie do platformy Azure przy pierwszym wdrożeniu. Użytkownicy muszą logowanie do platformy Azure przy użyciu konta z uprawnieniami administratora serwera na serwerze są wdrażania. Po zalogowaniu się do platformy Azure po raz pierwszy, token jest przypisany. Program SSDT buforuje tokenu w pamięci dla przyszłych Ponowne podłączenia.

### <a name="power-bi-desktop"></a>Power BI Desktop
Power BI Desktop łączy się za pomocą uwierzytelniania uniwersalnego usługi Active Directory z obsługą uwierzytelniania Wieloskładnikowego usług Azure Analysis Services. Użytkownicy są monitowani o logowanie do platformy Azure w przypadku pierwszego połączenia. Użytkownicy muszą logowanie do platformy Azure przy użyciu konta, który znajduje się w administrator serwera lub roli bazy danych.

### <a name="excel"></a>Excel
Użytkownicy programu Excel można łączyć z serwerem przy użyciu konta systemu Windows, identyfikator organizacji (adres e-mail) lub adres e-mail zewnętrznych. Tożsamość zewnętrzna wiadomość e-mail, musi istnieć w usłudze Azure AD jako Gość.

## <a name="user-permissions"></a>Uprawnienia użytkowników

**Administratorzy serwera** są specyficzne dla wystąpienia serwera usług Azure Analysis Services. Łączą się z takich narzędzi jak portalu Azure, programu SSMS i narzędzi SSDT do wykonania zadania, takie jak dodawanie baz danych i zarządzanie rolami użytkowników. Domyślnie użytkownik, który tworzy serwer jest automatycznie dodawane jako administrator serwera usług Analysis Services. Za pomocą portalu Azure lub SSMS można dodawać innych administratorów. Administratorzy serwera musi mieć konto w dzierżawie usługi Azure AD w ramach tej samej subskrypcji. Aby dowiedzieć się więcej, zobacz [administratorom serwerów zarządzania](analysis-services-server-admins.md). 

**Bazy danych użytkowników** nawiązać baz danych modelu przy użyciu aplikacji klienckich, takich jak Excel lub Power BI. Należy dodać użytkowników do ról bazy danych. Role bazy danych definiuje administratora, proces ani uprawnienia do odczytu bazy danych. Należy zrozumieć bazy danych użytkowników w roli z uprawnieniami administratora różni się od administratorów serwera. Jednak domyślnie administratorom serwerów są również administratorami baz danych. Aby dowiedzieć się więcej, zobacz [Zarządzanie ról bazy danych i użytkowników](analysis-services-database-users.md).

**Właściciele zasobów platformy Azure**. Właścicieli zasobów, zarządzanie zasobami subskrypcji platformy Azure. Właścicieli zasobów można dodać tożsamości użytkowników usługi Azure AD do właściciela lub współautora ról w ramach subskrypcji przy użyciu **kontrola dostępu** w portalu Azure lub za pomocą szablonów usługi Azure Resource Manager. 

![Kontrola dostępu w portalu Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Role na tym poziomie dotyczą użytkowników lub kont, które należy wykonać zadania, które można wykonać w portalu lub przy użyciu szablonów usługi Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Role bazy danych

 Role zdefiniowane dla modelu tabelarycznego są role bazy danych. Oznacza to role zawierają elementy Członkowskie składające się z usługi Azure AD użytkownicy i grupy zabezpieczeń, które mają określone uprawnienia, które definiują akcję elementów członkowskich może potrwać w bazie danych modelu. Rola bazy danych zostanie utworzona jako oddzielny obiekt w bazie danych i ma zastosowanie tylko do bazy danych, w której jest tworzona tej roli.   
  
 Domyślnie podczas tworzenia nowego projektu modelu tabelarycznego, projektu modelu nie ma żadnych ról. Role można zdefiniować przy użyciu okna dialogowego menedżera ról w program SSDT. Gdy role są zdefiniowane podczas tworzenia projektu modelu, są one stosowane tylko do bazy danych obszaru roboczego modelu. Po wdrożeniu modelu ról są stosowane do wdrożonej modelu. Po wdrożeniu modelu serwera i bazy danych Administratorzy mogą zarządzać rolami i elementów członkowskich za pomocą narzędzia SSMS. Aby dowiedzieć się więcej, zobacz [Zarządzanie ról bazy danych i użytkowników](analysis-services-database-users.md).
  


## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie dostępem do zasobów przy użyciu grup usługi Azure Active Directory](../active-directory/active-directory-manage-groups.md)   
[Zarządzanie ról bazy danych i użytkowników](analysis-services-database-users.md)  
[Zarządzanie administratorami serwerów](analysis-services-server-admins.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  