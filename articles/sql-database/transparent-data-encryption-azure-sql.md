---
title: Transparent Data Encryption
description: Omówienie przezroczystego szyfrowania danych dla bazy danych SQL i synapse SQL w usłudze Azure Synapse Analytics. Dokument obejmuje jego zalety i opcje konfiguracji, która obejmuje zarządzane przez usługę przezroczyste szyfrowanie danych i Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/06/2020
ms.openlocfilehash: 982a59f1eb8717e2fe2d86728cdae731c919aaf0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476971"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Przezroczyste szyfrowanie danych dla bazy danych SQL i usługi Azure Synapse

Przezroczyste szyfrowanie danych (TDE) pomaga chronić usługę Azure SQL Database, wystąpienie zarządzanego sql azure i synapse SQL w usłudze Azure Synapse Analytics przed zagrożeniem złośliwą aktywnością w trybie offline przez szyfrowanie danych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności wprowadzania jakichkolwiek zmian w aplikacji. Technologia TDE jest domyślnie włączona dla wszystkich nowo wdrożonych baz danych usługi Azure SQL Database. TDE nie może służyć do szyfrowania logicznej **głównej** bazy danych w bazie danych SQL.  Główna **master** baza danych zawiera obiekty, które są potrzebne do wykonywania operacji TDE w bazach danych użytkowników.

TDE musi być ręcznie włączone dla starszych baz danych usługi Azure SQL Database, Azure SQL Managed Instance lub Azure Synapse.
Bazy danych wystąpienia zarządzanego utworzone za pomocą przywracania dziedziczą stan szyfrowania ze źródłowej bazy danych.

Przezroczyste szyfrowanie danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucz szyfrowania bazy danych. Ten klucz szyfrowania bazy danych jest chroniony przez ochronę szyfrowania przezroczystych danych. Protector jest certyfikatem zarządzanym przez usługę (szyfrowanie przezroczystych danych zarządzanych przez usługę) lub kluczem asymetrycznym przechowywanym w usłudze Azure Key Vault (Bring Your Own Key). Ustaw przezroczysty protector szyfrowania danych na poziomie serwera dla usługi Azure SQL Database i Azure Synapse oraz poziom wystąpienia dla wystąpienia zarządzanego sql platformy Azure. Termin *serwer* odnosi się zarówno do serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

Podczas uruchamiania bazy danych zaszyfrowany klucz szyfrowania bazy danych jest odszyfrowywane, a następnie używany do odszyfrowywania i ponownego szyfrowania plików bazy danych w procesie aparatu baz danych programu SQL Server. Przezroczyste szyfrowanie danych wykonuje szyfrowanie we/wy w czasie rzeczywistym i odszyfrowywanie danych na poziomie strony. Każda strona jest odszyfrowywana podczas wczytywania do pamięci, a następnie szyfrowana przed zapisaniem na dysku. Aby uzyskać ogólny opis przezroczystego szyfrowania danych, zobacz [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

Sql Server uruchomiony na maszynie wirtualnej platformy Azure również można użyć klucza asymetrycznego z usługi Key Vault. Kroki konfiguracji różnią się od używania klucza asymetrycznego w bazie danych SQL i wystąpieniu zarządzanym SQL. Aby uzyskać więcej informacji, zobacz [Rozszerzalne zarządzanie kluczami przy użyciu usługi Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Szyfrowanie przezroczystych danych zarządzane przez usługę

Na platformie Azure domyślnym ustawieniem szyfrowania danych przezroczystych jest to, że klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera, a algorytm szyfrowania używany jest AES 256. Jeśli baza danych znajduje się w relacji replikacji geograficznej, zarówno podstawowa, jak i pomocnicza baza danych są chronione przez nadrzędny klucz serwera podstawowej bazy danych. Jeśli dwie bazy danych są połączone z tym samym serwerem, mają również ten sam wbudowany certyfikat.  Firma Microsoft automatycznie obraca te certyfikaty zgodnie z wewnętrznymi zasadami zabezpieczeń, a klucz główny jest chroniony przez wewnętrzny tajny magazyn firmy Microsoft.  Klienci mogą weryfikować zgodność bazy danych SQL z wewnętrznymi zasadami zabezpieczeń w niezależnych raportach inspekcji innych firm dostępnych w [Centrum zaufania firmy Microsoft](https://servicetrust.microsoft.com/).

Firma Microsoft bezproblemowo przenosi klucze i zarządza nimi w razie potrzeby do replikacji geograficznej i przywracania.

> [!IMPORTANT]
> Wszystkie nowo utworzone bazy danych SQL i bazy danych wystąpienia zarządzanego są domyślnie szyfrowane przy użyciu szyfrowania przezroczystych danych zarządzanych przez usługę. Istniejące bazy danych SQL utworzone przed majem 2017 r. i bazy danych SQL utworzone za pomocą przywracania, replikacji geograficznej i kopii bazy danych nie są domyślnie szyfrowane. Istniejące bazy danych wystąpienia zarządzanego utworzone przed lutym 2019 r. nie są domyślnie szyfrowane. Bazy danych wystąpienia zarządzanego utworzone za pomocą stanu przywracania dziedziczą stan szyfrowania ze źródła.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Zarządzane przez klienta przezroczyste szyfrowanie danych - Bring Your Own Key

[TDE z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) umożliwia szyfrowanie klucza szyfrowania bazy danych (DEK) za pomocą klucza asymetrycznego zarządzanego przez klienta o nazwie TDE Protector.  Jest to również ogólnie określane jako Bring Your Own Key (BYOK) wsparcie dla przezroczystego szyfrowania danych. W scenariuszu BYOK ochrony TDE jest przechowywany w klienta własnością i [zarządzanej usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), systemu zarządzania kluczami zewnętrznymi platformy Azure w chmurze. Ochrona TDE może być [generowana przez magazyn kluczy lub przeniesiona do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) z lokalnego urządzenia HSM. TDE DEK, który jest przechowywany na stronie rozruchowej bazy danych, jest szyfrowany i odszyfrowywane przez TDE Protector, który jest przechowywany w usłudze Azure Key Vault i nigdy nie opuszcza magazynu kluczy.  Baza danych SQL musi mieć uprawnienia do magazynu kluczy należących do klienta, aby odszyfrować i zaszyfrować kod DEK. Jeśli uprawnienia logicznego serwera SQL do magazynu kluczy zostaną odwołane, baza danych będzie niedostępna, a wszystkie dane będą szyfrowane. W przypadku usługi Azure SQL Database ochrona TDE jest ustawiona na poziomie serwera SQL logicznej i jest dziedziczona przez wszystkie bazy danych skojarzone z tym serwerem. Dla [wystąpienia zarządzanego sql platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)ochrony TDE jest ustawiona na poziomie wystąpienia i jest dziedziczona przez wszystkie *zaszyfrowane* bazy danych w tym wystąpieniu. Termin *serwer* odnosi się zarówno do serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

Dzięki integracji usługi TDE z usługą Azure Key Vault użytkownicy mogą kontrolować zadania zarządzania kluczami, w tym rotacje kluczy, uprawnienia do magazynu kluczy, tworzenie kopii zapasowych kluczy i włączać inspekcję/raportowanie wszystkich programów ochrony TDE przy użyciu funkcji usługi Azure Key Vault. Usługa Key Vault zapewnia centralne zarządzanie kluczami, wykorzystuje ściśle monitorowane sprzętowe moduły zabezpieczeń (HSM) i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami a danymi w celu zapewnienia zgodności z zasadami zabezpieczeń.
Aby dowiedzieć się więcej na temat przezroczystego szyfrowania danych za pomocą integracji usługi Azure Key Vault (Bring Your Own Key support) dla usługi Azure SQL Database, wystąpienia zarządzanego SQL i usługi Azure Synapse, zobacz [Przezroczyste szyfrowanie danych za pomocą integracji usługi Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Aby rozpocząć korzystanie z przezroczystego szyfrowania danych z integracją usługi Azure Key Vault (Bring Your Own Key support), zobacz poradnik włączanie [przezroczystego szyfrowania danych przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Przenoszenie przezroczystej bazy danych chronionej szyfrowaniem danych

Nie trzeba odszyfrowywać baz danych dla operacji na platformie Azure. Przezroczyste ustawienia szyfrowania danych w źródłowej bazie danych lub podstawowej bazie danych są w sposób przezroczysty dziedziczone na docelowych. Operacje, które są zawarte obejmują:

- Przywracanie geograficzne
- Samoobsługowe przywracanie punktu w czasie
- Przywracanie usuniętej bazy danych
- Aktywna replikacja geograficzna
- Tworzenie kopii bazy danych
- Przywracanie pliku kopii zapasowej do wystąpienia zarządzanego sql usługi Azure

> [!IMPORTANT]
> Wykonywanie ręcznej kopii zapasowej tylko bazy danych zaszyfrowane przez usługi zarządzane TDE nie jest dozwolone w usłudze SQL Wystąpienie zarządzane, ponieważ certyfikat używany do szyfrowania nie jest dostępny. Użyj funkcji przywracania punktu w czasie, aby przenieść ten typ bazy danych do innego wystąpienia zarządzanego.

Podczas eksportowania przezroczystej bazy danych chronionej szyfrowaniem danych eksportowana zawartość bazy danych nie jest szyfrowana. Ta eksportowana zawartość jest przechowywana w niezaszyfrowanych plikach BACPAC. Pamiętaj, aby odpowiednio chronić pliki BACPAC i włączyć przezroczyste szyfrowanie danych po zakończeniu importowania nowej bazy danych.

Na przykład jeśli plik BACPAC jest eksportowany z lokalnego wystąpienia programu SQL Server, zaimportowana zawartość nowej bazy danych nie jest automatycznie szyfrowana. Podobnie jeśli plik BACPAC jest eksportowany do lokalnego wystąpienia programu SQL Server, nowa baza danych również nie jest automatycznie szyfrowana.

Jedynym wyjątkiem jest podczas eksportowania do i z bazy danych SQL. Przezroczyste szyfrowanie danych jest włączone w nowej bazie danych, ale sam plik BACPAC nadal nie jest zaszyfrowany.


## <a name="manage-transparent-data-encryption"></a>Zarządzanie przezroczystym szyfrowaniem danych
# <a name="portal"></a>[Portal](#tab/azure-portal)
Zarządzanie przezroczystym szyfrowaniem danych w witrynie Azure portal.

Aby skonfigurować przezroczyste szyfrowanie danych za pośrednictwem witryny Azure portal, użytkownik musi być połączony jako właściciel platformy Azure, współautor lub menedżer zabezpieczeń SQL.

Szyfrowanie przezroczystych danych jest włączane i wyłączane na poziomie bazy danych. Aby włączyć przezroczyste szyfrowanie danych w bazie danych, przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się za pomocą konta administratora platformy Azure lub współautora. Znajdź przezroczyste ustawienia szyfrowania danych w bazie danych użytkowników. Domyślnie używane jest szyfrowanie danych przezroczystych zarządzanych przez usługę. Certyfikat szyfrowania danych przezroczystych jest generowany automatycznie dla serwera zawierającego bazę danych. W przypadku wystąpienia zarządzanego sql platformy Azure użyj T-SQL, aby włączyć i wyłączyć szyfrowanie danych przezroczystych w bazie danych.

![Szyfrowanie przezroczystych danych zarządzane przez usługę](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

Na poziomie serwera można ustawić klucz główny szyfrowania danych przezroczyste, znany również jako ochrona szyfrowania danych przezroczystych. Aby użyć przezroczystego szyfrowania danych z obsługą bring your own key i chronić bazy danych za pomocą klucza z usługi Key Vault, otwórz przezroczyste ustawienia szyfrowania danych na serwerze.

![Przejrzyste szyfrowanie danych dzięki obsłudze bring your own key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Zarządzanie przezroczystym szyfrowaniem danych przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby skonfigurować przezroczyste szyfrowanie danych za pośrednictwem programu PowerShell, użytkownik musi być połączony jako właściciel platformy Azure, współautor lub menedżer zabezpieczeń SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Polecenia cmdlet dla usługi Azure SQL Database i Azure Synapse

Użyj następujących poleceń cmdlet dla usługi Azure SQL Database i Azure Synapse:

| Polecenie cmdlet | Opis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Pobiera stan szyfrowania przezroczystych danych dla bazy danych |
| [Get-AzSqlDatabaseTransparentDataEncryptionAkcyjność](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Sprawdza postęp szyfrowania bazy danych |
| [Klucz dodawania AzSqlServerKeyVault](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Dodaje klucz magazynu kluczy do wystąpienia programu SQL Server |
| [Klucz Dostępu AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Pobiera klucze magazynu kluczy dla serwera bazy danych SQL azure  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ustawia przezroczysty program ochrony szyfrowania danych dla wystąpienia programu SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Pobiera ochronę szyfrowania przezroczystych danych |
| [Usuń przycisk AzSqlServerKeyVault](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Usuwa klucz magazynu kluczy z wystąpienia programu SQL Server |
|  | |

> [!IMPORTANT]
> W przypadku wystąpienia zarządzanego programu Azure SQL użyj polecenia T-SQL [ALTER DATABASE,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) aby włączyć i wyłączyć szyfrowanie przezroczystych danych na poziomie bazy danych i sprawdź [przykładowy skrypt programu PowerShell,](transparent-data-encryption-byok-azure-sql-configure.md) aby zarządzać przezroczystym szyfrowaniem danych na poziomie wystąpienia.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Zarządzanie przezroczystym szyfrowaniem danych przy użyciu funkcji Transact-SQL.

Połącz się z bazą danych przy użyciu logowania, który jest administratorem lub członkiem **roli dbmanager** w głównej bazie danych.

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE (usługa Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | USTAWIANIE SZYFROWANIA ON/OFF szyfruje lub odszyfrowuje bazę danych |
| [Sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania bazy danych i skojarzonych z nią kluczy szyfrowania bazy danych |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania każdego węzła magazynu danych i skojarzonych z nim kluczy szyfrowania bazy danych |
|  | |

Nie można przełączyć ochrony szyfrowania przezroczystych danych na klucz z usługi Key Vault przy użyciu funkcji Transact-SQL. Użyj programu PowerShell lub witryny Azure portal.

# <a name="rest-api"></a>[Interfejs API REST](#tab/azure-RESTAPI)
Zarządzanie przezroczystym szyfrowaniem danych przy użyciu interfejsu API REST.

Aby skonfigurować przezroczyste szyfrowanie danych za pośrednictwem interfejsu API REST, użytkownik musi być połączony jako właściciel platformy Azure, współautor lub menedżer zabezpieczeń SQL.
Użyj następującego zestawu poleceń dla usługi Azure SQL Database i Azure Synapse:

| Polecenie | Opis |
| --- | --- |
|[Tworzenie lub aktualizowanie serwera](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Dodaje tożsamość usługi Azure Active Directory do wystąpienia programu SQL Server (używanego do udzielania dostępu do usługi Key Vault)|
|[Tworzenie lub aktualizowanie klucza serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Dodaje klucz magazynu kluczy do wystąpienia programu SQL Server|
|[Usuń klucz serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Usuwa klucz magazynu kluczy z wystąpienia programu SQL Server|
|[Pobierz klucze serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Pobiera określony klucz magazynu kluczy z wystąpienia programu SQL Server|
|[Lista kluczy serwera według serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Pobiera klucze magazynu kluczy dla wystąpienia programu SQL Server |
|[Tworzenie lub aktualizowanie ochrony szyfrowania](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Ustawia przezroczysty program ochrony szyfrowania danych dla wystąpienia programu SQL Server|
|[Pobierz ochronę szyfrowania](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Pobiera przezroczysty program ochrony szyfrowania danych dla wystąpienia programu SQL Server|
|[Lista protectors szyfrowania według serwera](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Pobiera przezroczyste osłony szyfrowania danych dla wystąpienia programu SQL Server |
|[Tworzenie lub aktualizowanie konfiguracji szyfrowania danych przezroczystych](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
|[Uzyskaj konfigurację szyfrowania przezroczystych danych](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Pobiera konfigurację szyfrowania przezroczystych danych dla bazy danych|
|[Lista wyników konfiguracji szyfrowania przezroczystych danych](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Pobiera wynik szyfrowania bazy danych|

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólny opis przezroczystego szyfrowania danych, zobacz [Przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Aby dowiedzieć się więcej na temat przezroczystego szyfrowania danych za pomocą obsługi programu Bring Your Own Key dla usługi Azure SQL Database, azure SQL Managed Instance i Azure Synapse, zobacz [Przezroczyste szyfrowanie danych za pomocą obsługi kluczy własnych](transparent-data-encryption-byok-azure-sql.md).
- Aby rozpocząć korzystanie z przezroczystego szyfrowania danych za pomocą obsługi funkcji Bring Your Own Key, zobacz przewodnik Włączanie [przezroczystego szyfrowania danych przy użyciu własnego klucza z usługi Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Aby uzyskać więcej informacji na temat usługi Key Vault, zobacz [stronę dokumentacji magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
