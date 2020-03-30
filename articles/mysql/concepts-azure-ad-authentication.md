---
title: Uwierzytelnianie usługi Active Directory — usługa Azure Database dla mysql
description: Dowiedz się więcej o pojęciach usługi Azure Active Directory do uwierzytelniania za pomocą usługi Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299026"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory za pomocą usługi MySQL

Uwierzytelnianie usługi Microsoft Azure Active Directory (Azure AD) to mechanizm łączenia się z usługą Azure Database for MySQL przy użyciu tożsamości zdefiniowanych w usłudze Azure AD.
Za pomocą uwierzytelniania usługi Azure AD można zarządzać tożsamościami użytkowników bazy danych i innymi usługami firmy Microsoft w centralnej lokalizacji, co upraszcza zarządzanie uprawnieniami.

> [!IMPORTANT]
> Uwierzytelnianie usługi Azure AD dla usługi Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Korzyści z korzystania z usługi Azure AD obejmują:

- Uwierzytelnianie użytkowników w usługach platformy Azure w jednolity sposób
- Zarządzanie zasadami haseł i rotacją haseł w jednym miejscu
- Wiele form uwierzytelniania obsługiwanych przez usługę Azure Active Directory, co może wyeliminować konieczność przechowywania haseł
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Uwierzytelnianie usługi Azure AD używa użytkowników bazy danych MySQL do uwierzytelniania tożsamości na poziomie bazy danych
- Obsługa uwierzytelniania opartego na tokenach dla aplikacji łączących się z usługą Azure Database for MySQL

Aby skonfigurować i używać uwierzytelniania usługi Azure Active Directory, należy użyć następującego procesu:

1. W razie potrzeby utwórz i wypełnij usługę Azure Active Directory tożsamościami użytkowników.
2. Opcjonalnie skojarzyć lub zmienić usługę Active Directory obecnie skojarzone z subskrypcją platformy Azure.
3. Utwórz administratora usługi Azure AD dla usługi Azure Database dla serwera MySQL.
4. Tworzenie użytkowników bazy danych w bazie danych mapowane do tożsamości usługi Azure AD.
5. Połącz się z bazą danych, pobierając token dla tożsamości usługi Azure AD i logując się.

> [!NOTE]
> Aby dowiedzieć się, jak tworzyć i wypełniać usługę Azure AD, a następnie konfigurować usługę Azure AD za pomocą usługi Azure Database dla mysql, zobacz [Konfigurowanie i logowanie się za pomocą usługi Azure AD dla usługi Azure Database dla mysql.](howto-configure-sign-in-azure-ad-authentication.md)

## <a name="architecture"></a>Architektura

Poniższy diagram wysokiego poziomu podsumowuje, jak działa uwierzytelnianie usługi Azure AD przy użyciu usługi Azure Database dla mysql. Strzałki wskazują ścieżki komunikacyjne.

![przepływ uwierzytelniania][1]

## <a name="administrator-structure"></a>Struktura administratora

Podczas korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratora dla serwera MySQL; pierwotnego administratora MySQL i administratora usługi Azure AD. Tylko administrator na podstawie konta usługi Azure AD można utworzyć pierwszy użytkownik bazy danych zawierającej usługę Azure AD w bazie danych użytkowników. Logowanie administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Gdy administrator jest kontem grupy, może być używany przez dowolnego członka grupy, włączając wielu administratorów usługi Azure AD dla serwera MySQL. Używanie konta grupy jako administratora zwiększa łatwość zarządzania, umożliwiając centralne dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników lub uprawnień na serwerze MySQL. Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować w dowolnym momencie.

![struktura administracyjna][2]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, którzy mogą uwierzytelniać się za pomocą usługi Azure AD, musisz być zaprojektowanym administratorem usługi Azure AD. Ten użytkownik jest przypisywany przez skonfigurowanie konta administratora usługi Azure AD dla określonej bazy danych platformy Azure dla serwera MySQL.

Aby utworzyć nowego użytkownika bazy danych usługi Azure AD, należy połączyć się jako administrator usługi Azure AD. Zostało to zademonstrowane w [temacie Konfigurowanie i logowanie za pomocą usługi Azure AD dla usługi Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla usługi Azure Database for MySQL. Jeśli administrator usługi Azure Active Directory został usunięty z serwera, istniejący użytkownicy usługi Azure Active Directory utworzone wcześniej nie mogą już łączyć się z bazą danych przy użyciu poświadczeń usługi Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Łączenie przy użyciu tożsamości usługi Azure AD

Uwierzytelnianie usługi Azure Active Directory obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Hasło usługi Azure Active Directory
- Zintegrowana usługa Azure Active Directory
- Uniwersalna usługa Azure Active Directory z usługą MFA
- Używanie certyfikatów aplikacji usługi Active Directory lub wpisów tajnych klienta

Po uwierzytelnieniu względem usługi Active Directory, a następnie pobrać token. Ten token jest twoim hasłem do logowania.

> [!NOTE]
> Aby uzyskać więcej informacji na temat łączenia się z tokenem usługi Active Directory, zobacz [Konfigurowanie i logowanie się za pomocą usługi Azure AD dla usługi Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Tylko jeden administrator usługi Azure AD można skonfigurować dla usługi Azure Database dla serwera MySQL w dowolnym momencie.
- Tylko administrator usługi Azure AD dla MySQL może początkowo połączyć się z usługą Azure Database for MySQL przy użyciu konta usługi Azure Active Directory. Administrator usługi Active Directory może konfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Jeśli użytkownik zostanie usunięty z usługi Azure AD, ten użytkownik nie będzie już mógł uwierzytelnić się za pomocą usługi Azure AD i dlatego nie będzie już można uzyskać tokenu dostępu dla tego użytkownika. W takim przypadku mimo że pasujący użytkownik nadal będzie w bazie danych, nie będzie można połączyć się z serwerem z tym użytkownikiem.
> [!NOTE]
> Logowanie z usuniętym użytkownikiem usługi Azure AD można nadal wykonywać do wygaśnięcia tokenu (do 60 minut od wystawienia tokenu).  Jeśli również usunąć użytkownika z usługi Azure Database dla MySQL ten dostęp zostanie natychmiast odwołany.
- Jeśli administrator usługi Azure AD zostanie usunięty z serwera, serwer nie będzie już skojarzony z dzierżawą usługi Azure AD i dlatego wszystkie logowania usługi Azure AD zostaną wyłączone dla serwera. Dodanie nowego administratora usługi Azure AD z tej samej dzierżawy spowoduje ponowne włączenie logowania usługi Azure AD.
- Usługa Azure Database for MySQL dopasowuje tokeny dostępu do usługi Azure Database dla użytkownika MySQL przy użyciu unikatowego identyfikatora użytkownika usługi Azure AD użytkownika użytkownika, w przeciwieństwie do używania nazwy użytkownika. Oznacza to, że jeśli użytkownik usługi Azure AD zostanie usunięty w usłudze Azure AD, a nowy użytkownik utworzony o tej samej nazwie, usługa Azure Database for MySQL uzna, że inny użytkownik. W związku z tym jeśli użytkownik zostanie usunięty z usługi Azure AD, a następnie nowy użytkownik o tej samej nazwie dodane, nowy użytkownik nie będzie mógł połączyć się z istniejącym użytkownikiem.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak tworzyć i wypełniać usługę Azure AD, a następnie konfigurować usługę Azure AD za pomocą usługi Azure Database dla mysql, zobacz [Konfigurowanie i logowanie się za pomocą usługi Azure AD dla usługi Azure Database dla mysql.](howto-configure-sign-in-azure-ad-authentication.md)
- Aby uzyskać omówienie loginów i użytkowników bazy danych dla usługi Azure Database for MySQL, zobacz [Tworzenie użytkowników w usłudze Azure Database dla MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
