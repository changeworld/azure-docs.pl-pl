---
title: Przezroczyste szyfrowanie danych dla Azure SQL Database i magazynu danych | Microsoft Docs
description: Przegląd przezroczystego szyfrowania danych dla SQL Database i magazynu danych. Ten dokument zawiera zalety i opcje konfiguracji, w tym zarządzane Bring Your Own Key i niejawne szyfrowanie danych przez usługę.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: b63a8c9defdf154f35a847f29182b49ff94ff3a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933202"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Przezroczyste szyfrowanie danych dla SQL Database i magazynu danych

Usługa transparent Data Encryption (TDE) pomaga chronić Azure SQL Database, wystąpienie zarządzane Azure SQL i magazyn danych Azure przed zagrożeniem złośliwego działania w trybie offline przez szyfrowanie danych przechowywanych w spoczynku. Wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w czasie rzeczywistym bez konieczności wprowadzania zmian w aplikacji. Domyślnie TDE jest włączony dla wszystkich nowo wdrożonych baz danych SQL Azure. Nie można użyć TDE do szyfrowania logicznej **głównej** bazy danych w SQL Database.  Baza danych **Master** zawiera obiekty, które są konieczne do wykonywania operacji TDE w bazach danych użytkowników.

TDE musi być ręcznie włączona dla starszych baz danych Azure SQL Database, wystąpienia zarządzanego Azure SQL lub Azure SQL Data Warehouse.
Zarządzane bazy danych wystąpień utworzonych za pośrednictwem przywracania dziedziczą stan szyfrowania ze źródłowej bazy danych.

Przezroczyste szyfrowanie danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych. Ten klucz szyfrowania bazy danych jest chroniony przez funkcję ochrony przezroczystego szyfrowania danych. Funkcja ochrony jest certyfikatem zarządzanym przez usługę (niejawnym szyfrowaniem danych zarządzanym przez usługę) lub kluczem asymetrycznym przechowywanym w Azure Key Vault (Bring Your Own Key). Należy ustawić funkcję ochrony przezroczystego szyfrowania danych na poziomie serwera dla Azure SQL Database i magazynu danych oraz poziomu wystąpienia dla wystąpienia zarządzanego Azure SQL. Termin " *serwer* " dotyczy zarówno serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

Po uruchomieniu bazy danych zaszyfrowany klucz szyfrowania bazy danych jest odszyfrowywany, a następnie używany do odszyfrowywania i ponownego szyfrowania plików bazy danych w procesie aparatu bazy danych SQL Server. Szyfrowanie danych przezroczystych wykonuje szyfrowanie we/wy czasu rzeczywistego i odszyfrowywanie danych na poziomie strony. Każda Strona jest odszyfrowywana, gdy jest odczytywana do pamięci, a następnie szyfrowana przed zapisaniem na dysku. Aby uzyskać ogólny opis przezroczystego szyfrowania danych, zobacz [przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server uruchomione na maszynie wirtualnej platformy Azure mogą również używać klucza asymetrycznego z Key Vault. Czynności konfiguracyjne różnią się od używania klucza asymetrycznego w SQL Database i wystąpieniu zarządzanym SQL. Aby uzyskać więcej informacji, zobacz [rozszerzalne zarządzanie kluczami za pomocą Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Zarządzane szyfrowanie danych przez usługę

Na platformie Azure domyślne ustawienie przezroczystego szyfrowania danych polega na tym, że klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera, a używany algorytm szyfrowania to AES 256. Jeśli baza danych znajduje się w relacji replikacji geograficznej, zarówno podstawowa, jak i pomocnicza baza danych są chronione kluczem nadrzędnego serwera bazy danych. Jeśli dwie bazy danych są połączone z tym samym serwerem, współużytkują także ten sam certyfikat wbudowany.  Firma Microsoft automatycznie obraca te certyfikaty zgodnie z wewnętrznymi zasadami zabezpieczeń, a klucz główny jest chroniony przez wewnętrzny magazyn tajny firmy Microsoft.  Klienci mogą weryfikować SQL Database zgodności z wewnętrznymi zasadami zabezpieczeń w niezależnych raportach inspekcji innych firm, które są dostępne w [Centrum zaufania firmy Microsoft](https://servicetrust.microsoft.com/).

Firma Microsoft bezproblemowo przenosi klucze i zarządza nimi w zależności od potrzeby replikacji geograficznej i przywracania.

> [!IMPORTANT]
> Wszystkie nowo utworzone bazy danych SQL i bazy danych wystąpień zarządzanych domyślnie są szyfrowane za pomocą funkcji przezroczystego szyfrowania danych zarządzanego przez usługę. Istniejące bazy danych SQL utworzone przed 2017mi i bazami danych SQL utworzonych przy użyciu funkcji przywracania, replikacji geograficznej i kopii bazy danych nie są szyfrowane domyślnie. Istniejące bazy danych wystąpienia zarządzanego utworzone przed 2019 lutego nie są szyfrowane domyślnie. Zarządzane bazy danych wystąpień utworzonych za pośrednictwem przywracania dziedziczą stan szyfrowania ze źródła.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Szyfrowanie danych przezroczystych zarządzanych przez klienta — Bring Your Own Key

[TDE z kluczami zarządzanymi przez klienta w programie Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) umożliwia szyfrowanie klucza szyfrowania bazy danych (.  Jest to również ogólnie określane jako Bring Your Own Key (BYOK) obsługa Transparent Data Encryption. W scenariuszu BYOK funkcja ochrony TDE jest przechowywana w [Azure Key Vaultach](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)należących do klienta i zarządzanych przez chmurę systemu zarządzania Kluczami na platformie Azure. Funkcja ochrony TDE może być [generowana przez Magazyn kluczy lub transferowana do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) z lokalnego urządzenia HSM. TDE, który jest przechowywany na stronie rozruchowej bazy danych, jest szyfrowany i odszyfrowywany przez funkcję ochrony TDE, która jest przechowywana w Azure Key Vault i nigdy nie pozostawia magazynu kluczy.  SQL Database musi mieć przyznane uprawnienia do magazynu kluczy należącego do klienta, aby odszyfrować i zaszyfrować klucz szyfrowania danych. Jeśli uprawnienia logicznego serwera SQL do magazynu kluczy zostaną odwołane, baza danych będzie niedostępna, a wszystkie dane są szyfrowane. W przypadku Azure SQL Database funkcja ochrony TDE jest ustawiana na poziomie logicznego serwera SQL i jest dziedziczona przez wszystkie bazy danych skojarzone z tym serwerem. W przypadku [wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) (funkcji BYOK w wersji zapoznawczej) funkcja ochrony TDE jest ustawiona na poziomie wystąpienia i jest dziedziczona przez wszystkie *zaszyfrowane* bazy danych w tym wystąpieniu. Termin " *serwer* " dotyczy zarówno serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

Dzięki funkcji TDE z integracją Azure Key Vault użytkownicy mogą kontrolować zadania zarządzania kluczami, w tym kluczowe rotacje, uprawnienia do magazynu kluczy, kopie zapasowe kluczy, a także włączać inspekcje/raportowanie we wszystkich funkcjach Azure Key Vault ochrony TDE. Key Vault udostępnia centralne zarządzanie kluczami, korzysta ze ściśle monitorowanych sprzętowych modułów zabezpieczeń (sprzętowych modułów zabezpieczeń) i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami i danymi, aby zapewnić zgodność z zasadami zabezpieczeń.
Aby dowiedzieć się więcej na temat funkcji przezroczystego szyfrowania danych z integracją Azure Key Vault (obsługa Bring Your Own Key) dla Azure SQL Database, wystąpienia zarządzanego SQL (funkcja BYOK w wersji zapoznawczej) i magazyn danych, zobacz [przezroczyste szyfrowanie danych za pomocą Azure Key Vault Integracja](transparent-data-encryption-byok-azure-sql.md).

Aby rozpocząć korzystanie z funkcji przezroczystego szyfrowania danych z integracją Azure Key Vault (obsługa Bring Your Own Key), zapoznaj się z tematem jak [włączyć funkcję przezroczystego szyfrowania danych przy użyciu własnego klucza z Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Przenoszenie przezroczystej bazy danych chronionej przez szyfrowanie danych

Nie trzeba odszyfrowywać baz danych dla operacji na platformie Azure. Ustawienia przezroczystego szyfrowania danych w źródłowej bazie danych lub podstawowej bazie danych są w niewidoczny sposób dziedziczone w miejscu docelowym. Uwzględnione operacje obejmują:

- Przywracanie geograficzne
- Przywracanie do punktu w czasie samoobsługowym
- Przywracanie usuniętej bazy danych
- Aktywna replikacja geograficzna
- Tworzenie kopii bazy danych
- Przywracanie pliku kopii zapasowej do wystąpienia zarządzanego usługi Azure SQL

> [!IMPORTANT]
> Pobieranie ręcznie kopii zapasowej bazy danych zaszyfrowanej przez TDE zarządzane przez usługę nie jest dozwolone w wystąpieniu zarządzanym Azure SQL, ponieważ certyfikat używany do szyfrowania jest niedostępny. Funkcja przywracania do punktu w czasie służy do przenoszenia tego typu bazy danych do innego wystąpienia zarządzanego.

Po wyeksportowaniu nieszyfrowanej bazy danych chronionej przez szyfrowanie danych wyeksportowana zawartość bazy danych nie jest zaszyfrowana. Ta wyeksportowana zawartość jest przechowywana w niezaszyfrowanych plikach BACPAC. Pamiętaj, aby odpowiednio chronić pliki BACPAC i włączać przezroczyste szyfrowanie danych po zakończeniu importowania nowej bazy danych.

Na przykład, jeśli plik BACPAC zostanie wyeksportowany z wystąpienia SQL Server lokalnego, zaimportowana zawartość nowej bazy danych nie zostanie automatycznie zaszyfrowana. Podobnie, jeśli plik BACPAC zostanie wyeksportowany do wystąpienia SQL Server lokalnego, Nowa baza danych również nie zostanie automatycznie zaszyfrowana.

Jedynym wyjątkiem jest wyeksportowanie do i z bazy danych SQL. Funkcja transparent Data Encryption jest włączona w nowej bazie danych, ale sam plik BACPAC nadal nie jest szyfrowany.


## <a name="manage-transparent-data-encryption"></a>Zarządzanie przezroczystym szyfrowaniem danych
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Zarządzaj przezroczystym szyfrowaniem danych w Azure Portal.

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą Azure Portal, musisz mieć połączenie jako właściciel, współautor lub program SQL Security Manager platformy Azure.

Na poziomie bazy danych włączać i wyłączać przezroczyste szyfrowanie danych. Aby włączyć funkcję przezroczystego szyfrowania danych w bazie danych, przejdź do [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta administratora platformy Azure lub współautora. Znajdź ustawienia przezroczystego szyfrowania danych w bazie danych użytkownika. Domyślnie jest używane zarządzane szyfrowanie danych w ramach usługi. Certyfikat przezroczystego szyfrowania danych jest generowany automatycznie dla serwera, który zawiera bazę danych. W przypadku wystąpienia zarządzanego Azure SQL Użyj języka T-SQL do włączenia i wyłączenia przezroczystego szyfrowania danych w bazie danych.

![Zarządzane szyfrowanie danych przez usługę](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Należy ustawić klucz główny szyfrowania danych przezroczystych, znany również jako funkcja ochrony przezroczystego szyfrowania danych na poziomie serwera. Aby można było używać funkcji przezroczystego szyfrowania danych z Bring Your Own Key obsługi i ochrony baz danych przy użyciu klucza z Key Vault, należy otworzyć ustawienia przezroczystego szyfrowania danych na serwerze.

![Niewidoczne szyfrowanie danych z obsługą Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Zarządzanie przezroczystym szyfrowaniem danych przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą programu PowerShell, musisz mieć połączenie jako właściciel, współautor lub program SQL Security Manager platformy Azure.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Polecenia cmdlet dla Azure SQL Database i magazynu danych

Użyj następujących poleceń cmdlet dla Azure SQL Database i magazynu danych:

| Polecenie cmdlet | Opis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Pobiera stan przezroczystego szyfrowania danych dla bazy danych |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Sprawdza postęp szyfrowania bazy danych |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Dodaje klucz Key Vault do wystąpienia SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Pobiera klucze Key Vault dla serwera Azure SQL Database  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ustawia funkcję ochrony przezroczystego szyfrowania danych dla wystąpienia SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Pobiera funkcję ochrony przezroczystego szyfrowania danych |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Usuwa klucz Key Vault z wystąpienia SQL Server |
|  | |

> [!IMPORTANT]
> W przypadku wystąpienia zarządzanego usługi Azure SQL należy użyć polecenia [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) języka T-SQL, aby włączyć i wyłączyć funkcję przezroczystego szyfrowania danych na poziomie bazy danych, i sprawdź [przykładowy skrypt programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) , aby zarządzać przezroczystym szyfrowaniem danych na poziomie wystąpienia.

# <a name="transact-sqltabazure-transactsql"></a>[Język Transact-SQL](#tab/azure-TransactSQL)
Zarządzanie przezroczystym szyfrowaniem danych przy użyciu języka Transact-SQL.

Nawiąż połączenie z bazą danych za pomocą nazwy logowania, która jest administratorem lub członkiem roli **DBManager** w bazie danych Master.

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | USTAWIENIE Włącz/wyłącz szyfrowanie powoduje szyfrowanie lub odszyfrowanie bazy danych |
| [sys. DM _database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania bazy danych i skojarzonych z nimi kluczy szyfrowania bazy danych |
| [sys. DM _pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania poszczególnych węzłów magazynu danych i skojarzonych z nimi kluczy szyfrowania bazy danych |
|  | |

Nie można przełączyć funkcji ochrony przezroczystego szyfrowania danych do klucza z Key Vault przy użyciu języka Transact-SQL. Użyj programu PowerShell lub Azure Portal.

# <a name="rest-apitabazure-restapi"></a>[Interfejs API REST](#tab/azure-RESTAPI)
Zarządzanie przezroczystym szyfrowaniem danych przy użyciu interfejsu API REST.

Aby skonfigurować przezroczyste szyfrowanie danych za pomocą interfejsu API REST, należy połączyć się jako właściciel, współautor lub program SQL Security Manager platformy Azure.
Użyj następującego zestawu poleceń dla Azure SQL Database i magazynu danych:

| Polecenie | Opis |
| --- | --- |
|[Utwórz lub zaktualizuj serwer](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Dodaje Azure Active Directory tożsamość do wystąpienia SQL Server (służy do udzielania dostępu do Key Vault)|
|[Utwórz lub zaktualizuj klucz serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Dodaje klucz Key Vault do wystąpienia SQL Server|
|[Usuń klucz serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Usuwa klucz Key Vault z wystąpienia SQL Server|
|[Pobierz klucze serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Pobiera określony klucz Key Vault z wystąpienia SQL Server|
|[Wyświetl listę kluczy serwera według serwera](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Pobiera klucze Key Vault dla wystąpienia SQL Server |
|[Utwórz lub zaktualizuj ochronę szyfrowania](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Ustawia funkcję ochrony przezroczystego szyfrowania danych dla wystąpienia SQL Server|
|[Pobierz ochronę szyfrowania](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Pobiera funkcję ochrony przezroczystego szyfrowania danych dla wystąpienia SQL Server|
|[Wyświetlanie listy funkcji ochrony przed szyfrowaniem według serwera](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Pobiera przezroczyste funkcje ochrony szyfrowania danych dla wystąpienia SQL Server |
|[Utwórz lub zaktualizuj konfigurację Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych|
|[Pobierz konfigurację Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Pobiera przezroczystą konfigurację szyfrowania danych dla bazy danych|
|[Lista wyników konfiguracji Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Pobiera wynik szyfrowania dla bazy danych|

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólny opis przezroczystego szyfrowania danych, zobacz [przezroczyste szyfrowanie danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Aby dowiedzieć się więcej na temat funkcji przezroczystego szyfrowania danych z obsługą Bring Your Own Key dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i magazynu danych, zobacz [przezroczyste szyfrowanie danych za pomocą obsługi Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Aby rozpocząć korzystanie z funkcji przezroczystego szyfrowania danych z obsługą Bring Your Own Key, zapoznaj się z tematem jak [włączyć funkcję przezroczystego szyfrowania danych przy użyciu własnego klucza z Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Więcej informacji o Key Vault można znaleźć na [stronie dokumentacji Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
