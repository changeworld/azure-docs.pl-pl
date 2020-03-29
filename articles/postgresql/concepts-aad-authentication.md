---
title: Uwierzytelnianie usługi Active Directory — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się więcej o pojęciach usługi Azure Active Directory do uwierzytelniania za pomocą usługi Azure Database for PostgreSQL — single server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ec853657d6dd1f3b019d8a414cfa28edc1083b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769918"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Uwierzytelnianie za pomocą usługi PostgreSQL za pomocą usługi Azure Active Directory

Uwierzytelnianie usługi Microsoft Azure Active Directory (Azure AD) to mechanizm łączenia się z usługą Azure Database for PostgreSQL przy użyciu tożsamości zdefiniowanych w usłudze Azure AD.
Za pomocą uwierzytelniania usługi Azure AD można zarządzać tożsamościami użytkowników bazy danych i innymi usługami firmy Microsoft w centralnej lokalizacji, co upraszcza zarządzanie uprawnieniami.

> [!IMPORTANT]
> Uwierzytelnianie usługi Azure AD dla usługi Azure Database for PostgreSQL jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Korzyści z korzystania z usługi Azure AD obejmują:

- Uwierzytelnianie użytkowników w usługach platformy Azure w jednolity sposób
- Zarządzanie zasadami haseł i rotacją haseł w jednym miejscu
- Wiele form uwierzytelniania obsługiwanych przez usługę Azure Active Directory, co może wyeliminować konieczność przechowywania haseł
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Uwierzytelnianie usługi Azure AD używa ról bazy danych PostgreSQL do uwierzytelniania tożsamości na poziomie bazy danych
- Obsługa uwierzytelniania opartego na tokenach dla aplikacji łączących się z usługą Azure Database for PostgreSQL

Aby skonfigurować i używać uwierzytelniania usługi Azure Active Directory, należy użyć następującego procesu:

1. W razie potrzeby utwórz i wypełnij usługę Azure Active Directory tożsamościami użytkowników.
2. Opcjonalnie skojarzyć lub zmienić usługę Active Directory obecnie skojarzone z subskrypcją platformy Azure.
3. Utwórz administratora usługi Azure AD dla usługi Azure Database dla serwera PostgreSQL.
4. Tworzenie użytkowników bazy danych w bazie danych mapowane do tożsamości usługi Azure AD.
5. Połącz się z bazą danych, pobierając token dla tożsamości usługi Azure AD i logując się.

> [!NOTE]
> Aby dowiedzieć się, jak tworzyć i wypełniać usługę Azure AD, a następnie konfigurować usługę Azure AD za pomocą usługi Azure Database for PostgreSQL, zobacz [Konfigurowanie i logowanie się za pomocą usługi Azure AD dla usługi Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Architektura

Poniższy diagram wysokiego poziomu podsumowuje, jak działa uwierzytelnianie usługi Azure AD przy użyciu usługi Azure Database for PostgreSQL. Strzałki wskazują ścieżki komunikacyjne.

![przepływ uwierzytelniania][1]

## <a name="administrator-structure"></a>Struktura administratora

Podczas korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratora dla serwera PostgreSQL; pierwotnego administratora PostgreSQL i administratora usługi Azure AD. Tylko administrator na podstawie konta usługi Azure AD można utworzyć pierwszy użytkownik bazy danych zawierającej usługę Azure AD w bazie danych użytkowników. Logowanie administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Gdy administrator jest kontem grupy, może być używany przez dowolnego członka grupy, włączając wielu administratorów usługi Azure AD dla serwera PostgreSQL. Używanie konta grupy jako administratora zwiększa łatwość zarządzania, umożliwiając centralne dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników lub uprawnień na serwerze PostgreSQL. Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować w dowolnym momencie.

![struktura administracyjna][2]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, którzy mogą uwierzytelniać `azure_ad_admin` się za pomocą usługi Azure AD, musisz mieć rolę w bazie danych. Ta rola jest przypisywana przez skonfigurowanie konta administratora usługi Azure AD dla określonej bazy danych platformy Azure dla serwera PostgreSQL.

Aby utworzyć nowego użytkownika bazy danych usługi Azure AD, należy połączyć się jako administrator usługi Azure AD. Zostało to zademonstrowane w [temacie Konfigurowanie i logowanie za pomocą usługi Azure AD dla usługi Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla usługi Azure Database for PostgreSQL. Jeśli administrator usługi Azure Active Directory został usunięty z serwera, istniejący użytkownicy usługi Azure Active Directory utworzone wcześniej nie mogą już łączyć się z bazą danych przy użyciu poświadczeń usługi Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Łączenie przy użyciu tożsamości usługi Azure AD

Uwierzytelnianie usługi Azure Active Directory obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Hasło usługi Azure Active Directory
- Zintegrowana usługa Azure Active Directory
- Uniwersalna usługa Azure Active Directory z usługą MFA
- Używanie certyfikatów aplikacji usługi Active Directory lub wpisów tajnych klienta

Po uwierzytelnieniu względem usługi Active Directory, a następnie pobrać token. Ten token jest twoim hasłem do logowania.

> [!NOTE]
> Aby uzyskać więcej informacji na temat łączenia się z tokenem usługi Active Directory, zobacz [Konfigurowanie i logowanie się za pomocą usługi Azure AD dla usługi Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Aby zwiększyć łatwość zarządzania, zaleca się aprowizowanie dedykowanej grupy usługi Azure AD jako administratora.
- Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować dla usługi Azure Database dla serwera PostgreSQL w dowolnym momencie.
- Tylko administrator usługi Azure AD dla postgreSQL może początkowo połączyć się z usługą Azure Database for PostgreSQL przy użyciu konta usługi Azure Active Directory. Administrator usługi Active Directory może konfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Jeśli użytkownik zostanie usunięty z usługi Azure AD, ten użytkownik nie będzie już mógł uwierzytelnić się za pomocą usługi Azure AD i dlatego nie będzie już można uzyskać tokenu dostępu dla tego użytkownika. W takim przypadku mimo że pasująca rola nadal będzie znajdować się w bazie danych, nie będzie można połączyć się z serwerem z tą rolą.
> [!NOTE]
> Logowanie z usuniętym użytkownikiem usługi Azure AD można nadal wykonywać do wygaśnięcia tokenu (do 60 minut od wystawienia tokenu).  Jeśli również usunąć użytkownika z usługi Azure Database dla PostgreSQL ten dostęp zostanie odwołany natychmiast.
- Jeśli administrator usługi Azure AD zostanie usunięty z serwera, serwer nie będzie już skojarzony z dzierżawą usługi Azure AD i dlatego wszystkie logowania usługi Azure AD zostaną wyłączone dla serwera. Dodanie nowego administratora usługi Azure AD z tej samej dzierżawy będzie ponownie można pozycjować logowania usługi Azure AD.
- Usługa Azure Database for PostgreSQL dopasowuje tokeny dostępu do roli usługi Azure Database for PostgreSQL przy użyciu unikatowego identyfikatora użytkownika usługi Azure AD użytkownika użytkownika, w przeciwieństwie do używania nazwy użytkownika. Oznacza to, że jeśli użytkownik usługi Azure AD zostanie usunięty w usłudze Azure AD, a nowy użytkownik utworzony o tej samej nazwie, usługa Azure Database for PostgreSQL uzna, że inny użytkownik. W związku z tym jeśli użytkownik zostanie usunięty z usługi Azure AD, a następnie nowy użytkownik o tej samej nazwie dodane, nowy użytkownik nie będzie mógł połączyć się z istniejącą rolą. Aby na to zezwolić, administrator usługi Azure Database for PostgreSQL Azure AD musi odwołać, a następnie udzielić użytkownikowi roli "azure_ad_user" w celu odświeżenia identyfikatora użytkownika usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak tworzyć i wypełniać usługę Azure AD, a następnie konfigurować usługę Azure AD za pomocą usługi Azure Database for PostgreSQL, zobacz [Konfigurowanie i logowanie się za pomocą usługi Azure AD dla usługi Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Aby uzyskać omówienie loginów, użytkowników i ról bazy danych usługi Azure Database for PostgreSQL, zobacz [Tworzenie użytkowników w usłudze Azure Database for PostgreSQL — Single Server](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
