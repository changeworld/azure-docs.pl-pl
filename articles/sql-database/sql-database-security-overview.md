---
title: Omówienie zabezpieczeń usługi Azure SQL Database | Microsoft Docs
description: Więcej informacji na temat zabezpieczeń usługi Azure SQL Database i programu SQL Server, włącznie z różnicami między chmurą i programu SQL Server w środowisku lokalnym.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, ronitr
manager: craigg
ms.date: 10/11/2018
ms.openlocfilehash: b8bb9cbf53b297d8dca1ac67bae8765edcc2c9f4
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311205"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Omówienie funkcji zabezpieczeń usługi Azure SQL Database

W tym artykule przedstawiono podstawowe informacje o zabezpieczeniach warstwy danych aplikacji przy użyciu usługi Azure SQL Database. W szczególności ten artykuł ułatwia rozpoczęcie pracy z zasobami na potrzeby ochrony danych, kontrolowania dostępu i aktywnego monitorowania.

Pełne omówienie funkcji zabezpieczeń dostępnych we wszystkich wersjach bazy danych SQL znajduje się w artykule [Security Center for SQL Server Database Engine and Azure SQL Database](https://msdn.microsoft.com/library/bb510589) (Centrum zabezpieczeń dla aparatu bazy danych SQL Server Database i usługi Azure SQL Database). Dodatkowe informacje są także dostępne w artykule [Security and Azure SQL Database technical white paper](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (Oficjalny dokument dotyczący zabezpieczeń usługi Azure SQL Database) (w formacie PDF).

## <a name="protect-data"></a>Ochrona danych

### <a name="encryption"></a>Szyfrowanie

Usługa SQL Database zabezpiecza dane, szyfrując przesyłane dane za pomocą protokołu [Transport Layer Security](https://support.microsoft.com/kb/3135244), dane magazynowane za pomocą funkcji [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), a używane dane za pomocą funkcji [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

> [!IMPORTANT]
> Wszystkie połączenia z usługą Azure SQL Database wymagają szyfrowania (SSL/TLS) podczas całego okresu, w którym dane są przesyłane do i z bazy danych. W parametrach połączenia aplikacji należy tak określić parametry, aby szyfrować połączenia i *nie* można ufać certyfikatowi serwera (jest to wykonywane automatycznie po skopiowaniu parametrów połączenia z witryny Azure portal), w przeciwnym razie połączenie nie weryfikuje tożsamość serwera i jest podatny na ataki "man-in--middle". Na przykład w przypadku sterownika ADO.NET te parametry połączenia mają wartość **Encrypt = True** i **TrustServerCertificate = False**. Aby uzyskać informacji na temat protokołów TLS i łączność, zobacz [zagadnienia dotyczące protokołu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

Można również rozważyć inne sposoby szyfrowania danych:

- [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-  Jeśli potrzebujesz technologii sprzętowego modułu zabezpieczeń lub Bring Your Own Key (BYOK) dla funkcji Transparent Data Encryption, rozważ użycie [przezroczyste szyfrowanie danych SQL Azure: Obsługa Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).

### <a name="data-discovery--classification"></a>Odnajdywanie i klasyfikacja danych

Odnajdywanie i klasyfikacja (obecnie w wersji zapoznawczej) danych zapewnia zaawansowane możliwości wbudowane w usłudze Azure SQL Database na potrzeby odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Odnajdywanie i klasyfikacja priorytetowe poufne dane (biznesowe i finansowe, opieki zdrowotnej, dane osobowe, itp.) można odtworzyć zasadniczą rolę w Twojej organizacji stature ochrony informacji. Może służyć jako infrastruktury:

- Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując nietypowego dostępu do poufnych danych.
- Kontrolowanie dostępu do i wzmacniania ochrony bezpieczeństwa, baz danych zawierającego bardzo ważne dane.
- Pomaga zaspokoić potrzeby dotyczące standardów ochrony prywatności danych i wymagania dotyczące zgodności z przepisami.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą SQL DB odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md).

## <a name="control-access"></a>Kontrola dostępu

Usługa SQL Database zabezpiecza dane przez ograniczenie dostępu do bazy danych przy użyciu reguł zapory, mechanizmów uwierzytelniania wymagających od użytkowników potwierdzenia tożsamości oraz funkcji autoryzacji do danych za pośrednictwem członkostw i uprawnień opartych na rolach, a także zabezpieczeń na poziomie wiersza i dynamicznego maskowania danych. Omówienie korzystania z funkcji kontroli dostępu w usłudze SQL Database zawiera artykuł dotyczący [kontroli dostępu](sql-database-control-access.md).

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami logicznymi na platformie Azure jest kontrolowane przez przypisania do ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [kontroli dostępu opartej na rolach w witrynie Azure portal](../role-based-access-control/overview.md).

### <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

Aby ułatwić ochronę danych, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia, za pomocą [reguł zapory](sql-database-firewall-configure.md). Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

### <a name="authentication"></a>Authentication

Uwierzytelnianie w usłudze SQL Database to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła. Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń można wybrać metodę uwierzytelniania w każdej innej bazie danych na tym serwerze jako jej właściciel, czyli „dbo”.

- **Uwierzytelnianie usługi Azure Active Directory**

  Ta metoda uwierzytelniania korzysta z tożsamości zarządzanej przez usługę Azure Active Directory i jest obsługiwany w przypadku zarządzanych i zintegrowanych domen. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](https://msdn.microsoft.com/library/ms144284.aspx). Aby skorzystać z uwierzytelniania przy użyciu usługi Azure Active Directory, należy utworzyć innego administratora serwera o nazwie „Azure AD admin”, co umożliwi administrowanie użytkownikami i grupami usługi Azure AD. Ten administrator może również wykonywać wszystkie operacje, które może wykonać zwykły administrator serwera. Zobacz artykuł [Connecting to SQL Database By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Łączenie z usługą SQL Database przy użyciu uwierzytelniania usługi Azure Active Directory), aby poznać krok po kroku sposób tworzenia administratora usługi Azure AD w celu włączenia uwierzytelniania usługi Azure Active Directory.

### <a name="authorization"></a>Autoryzacja

Autoryzacja określa, co użytkownik może zrobić w usłudze Azure SQL Database, i jest kontrolowana za pomocą członkostw roli bazy danych konta użytkownika i uprawnień na poziomie obiektu. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację.

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza

Zabezpieczenia na poziomie wiersza umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (np. członkostwa w grupie lub kontekstu wykonania). Aby uzyskać więcej informacji, zobacz [Zabezpieczenia na poziomie wierszy](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

Baza danych SQL dynamiczne maskowanie danych ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dane dynamiczne maskowanie automatycznie wykrywa potencjalnie poufne dane w bazie danych SQL Azure i zapewnia wiarygodne zalecenia z tych pól, z minimalnym wpływem na warstwę aplikacji. Jego działanie polega na zaciemnianiu poufnych danych w wyznaczonych polach bazy danych w zestawie wyników zapytania, przy czym dane w bazie danych pozostają bez zmian. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą SQL Database dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md).

## <a name="proactive-monitoring"></a>Aktywne monitorowanie

Usługa SQL Database zabezpiecza dane, udostępniając możliwości inspekcji i wykrywania zagrożeń.

### <a name="auditing"></a>Inspekcja

Funkcja inspekcji usługi SQL Database śledzi działania bazy danych i pozwala zachować zgodność z przepisami, rejestrując zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage. Inspekcja umożliwia zrozumienie trwających działań bazy danych, a także analizowanie i badanie działań historycznych w celu zidentyfikowania potencjalnych zagrożeń, podejrzanych nadużyć i naruszeń zabezpieczeń. Aby uzyskać więcej informacji, zobacz artykuł z [wprowadzeniem do funkcji inspekcji usługi SQL Database](sql-database-auditing.md).  

### <a name="threat-detection"></a>Wykrywanie zagrożeń

Wykrywanie zagrożeń uzupełnia funkcję inspekcji, stanowiąc dodatkową warstwę analizy zabezpieczeń wbudowaną w usługę Azure SQL Database, które wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania baz danych. Alerty o podejrzanych działaniach, potencjalnych luk w zabezpieczeniach i ataki przez wstrzyknięcie kodu SQL, a także database nietypowe wzorce dostępu. Alerty wykrywania zagrożeń, mogą być wyświetlane z [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/) zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia. Koszt wykrywania zagrożeń to 15 USD/serwera/miesiąc. Jest to bezpłatne przez pierwsze 60 dni. Aby uzyskać więcej informacji, zobacz artykuł [Get started with SQL Database Threat Detection](sql-database-threat-detection.md) (Wprowadzenie do usługi SQL Database Threat Detection).

## <a name="compliance"></a>Zgodność

Oprócz powyższych funkcji i funkcjonalności, które mogą pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń, usługa Azure SQL Database również uczestniczy w regularnych inspekcjach i posiada certyfikaty na liczbie standardów zgodności. Aby uzyskać więcej informacji, zobacz witrynę [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/), w której można znaleźć aktualną listę [certyfikatów zgodności usługi SQL Database](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

SQL Database pomaga w zarządzaniu bezpieczeństwo danych, zapewniając skanowania bazy danych i pulpit nawigacyjny scentralizowanych zabezpieczeń przy użyciu [oceny luk w zabezpieczeniach SQL](sql-vulnerability-assessment.md).

**Ocena luk w zabezpieczeniach**: [oceny luk w zabezpieczeniach SQL](sql-vulnerability-assessment.md) (obecnie w wersji zapoznawczej) jest łatwy do skonfigurować narzędzie wbudowane w usłudze Azure SQL Database, które mogą pomóc odkryć, śledzenie i korygowanie potencjalnych bazy danych luki w zabezpieczeniach. Ocena wykonuje skanowanie luk w zabezpieczeniach w bazie danych, a następnie generuje raport, który zapewnia wgląd w stan zabezpieczeń, w tym kroki z możliwością działania Aby rozwiązać problemy z zabezpieczeniami i zwiększ bezpieczeństwo bazy danych. Raport oceny można dostosować dla danego środowiska, ustawiając dopuszczalne linii bazowej konfiguracji uprawnień, konfiguracji funkcji i ustawień bazy danych. Może to ułatwić Ci:

- Spełnia wymagania dotyczące zgodności, które wymagają raporty skanowania bazy danych.
- Spełnia standardy ochrony prywatności danych.
- Monitorowanie środowiska dynamicznej bazy danych, której zmiany są trudne do wykrycia.

Aby uzyskać więcej informacji, zobacz [oceny luk w zabezpieczeniach SQL](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>Kolejne kroki

- Omówienie korzystania z funkcji kontroli dostępu w usłudze SQL Database zawiera artykuł dotyczący [kontroli dostępu](sql-database-control-access.md).
- Omówienie bazy danych inspekcji, zobacz [inspekcji usługi SQL Database](sql-database-auditing.md).
- Omówienie wykrywania zagrożeń, zobacz [wykrywania zagrożeń bazy danych SQL](sql-database-threat-detection.md).
