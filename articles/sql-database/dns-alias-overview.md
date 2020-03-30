---
title: Alias DNS
description: Aplikacje mogą łączyć się z aliasem dla nazwy serwera usługi Azure SQL Database. W międzyczasie można zmienić bazę danych SQL alias wskazuje na dowolnym czasie, aby ułatwić testowanie i tak dalej.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820615"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS dla bazy danych SQL usługi Azure

Usługa Azure SQL Database ma serwer DNS (Domain Name System). Interfejsy API programu PowerShell i REST akceptują [wywołania tworzenia aliasów DNS i zarządzania nimi](#anchor-powershell-code-62x) dla nazwy serwera bazy danych SQL.

Alias *DNS* może być używany zamiast nazwy serwera usługi Azure SQL Database. Programy klienckie mogą używać aliasu w swoich ciągach połączeń. Alias DNS udostępnia warstwę tłumaczenia, która może przekierować programy klienckie na różne serwery. Ta warstwa oszczędza trudności związane z koniecznością znalezienia i edycji wszystkich klientów i ich ciągów połączeń.

Typowe zastosowania aliasu DNS obejmują następujące przypadki:

- Tworzenie łatwej do zapamiętania nazwy dla programu Azure SQL Server.
- Podczas wstępnego opracowywania alias może odwoływać się do testowego serwera bazy danych SQL. Po uruchomieniu aplikacji można zmodyfikować alias, aby odwoływać się do serwera produkcyjnego. Przejście z testu do produkcji nie wymaga żadnych modyfikacji konfiguracji kilku klientów, którzy łączą się z serwerem bazy danych.
- Załóżmy, że jedyna baza danych w aplikacji jest przenoszona do innego serwera bazy danych SQL. W tym miejscu można zmodyfikować alias bez konieczności modyfikowania konfiguracji kilku klientów.
- Podczas regionalnej awarii można użyć przywracania geograficznego, aby odzyskać bazę danych na innym serwerze i regionie. Istniejący alias można zmodyfikować, aby wskazywał nowy serwer, tak aby istniejąca aplikacja kliencka mogła ponownie się z nim połączyć. 

## <a name="domain-name-system-dns-of-the-internet"></a>System nazw domen (DNS) w Internecie

Internet opiera się na systemie DNS. System DNS tłumaczy przyjazne nazwy na nazwę serwera usługi Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scenariusze z jednym aliasem DNS

Załóżmy, że należy przełączyć system na nowy serwer usługi Azure SQL Database. W przeszłości trzeba było znaleźć i zaktualizować każdy ciąg połączenia w każdym programie klienckim. Ale teraz, jeśli parametry połączenia używają aliasu DNS, należy zaktualizować tylko właściwość aliasu.

Funkcja aliasu DNS usługi Azure SQL Database może pomóc w następujących scenariuszach:

### <a name="test-to-production"></a>Test do produkcji

Po rozpoczęciu tworzenia programów klienckich poproś je o użycie aliasu DNS w swoich ciągach połączeń. Właściwości aliasu wskazują na testową wersję serwera usługi Azure SQL Database.

Później, gdy nowy system zostanie wyeksliowany w produkcji, można zaktualizować właściwości aliasu, aby wskazać na produkcyjny serwer bazy danych SQL. Nie jest konieczna żadna zmiana programów klienckich.

### <a name="cross-region-support"></a>Wsparcie międzyregionami

Odzyskiwanie po awarii może przenieść serwer bazy danych SQL do innego regionu geograficznego. W przypadku systemu, który używał aliasu DNS, można uniknąć konieczności znalezienia i zaktualizowania wszystkich ciągów połączeń dla wszystkich klientów. Zamiast tego można zaktualizować alias, aby odwoływać się do nowego serwera bazy danych SQL, który teraz hostuje bazę danych.

## <a name="properties-of-a-dns-alias"></a>Właściwości aliasu DNS

Następujące właściwości mają zastosowanie do każdego aliasu DNS dla serwera bazy danych SQL:

- *Unikalna nazwa:* Każda nazwa aliasu, który tworzysz jest unikatowa na wszystkich serwerach usługi Azure SQL Database, podobnie jak nazwy serwerów.
- *Wymagany jest serwer:* Nie można utworzyć aliasu DNS, chyba że odwołuje się on dokładnie do jednego serwera, a serwer musi już istnieć. Zaktualizowany alias musi zawsze odwoływać się do dokładnie jednego istniejącego serwera.
  - Po upuszczeniu serwera bazy danych SQL system Azure porzuca również wszystkie aliasy DNS, które odwołują się do serwera.
- *Nie jest związany z żadnym regionem:* Aliasy DNS nie są powiązane z regionem. Wszystkie aliasy DNS można zaktualizować w celu odwoływania się do serwera usługi Azure SQL Database, który znajduje się w dowolnym regionie geograficznym.
  - Jednak podczas aktualizowania aliasu w celu odwoływania się do innego serwera oba serwery muszą istnieć w tej samej *subskrypcji*platformy Azure .
- *Uprawnienia:* Aby zarządzać aliasem DNS, użytkownik musi mieć uprawnienia *współautora serwera* lub wyższe. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do kontroli dostępu opartej na rolach w witrynie Azure portal](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Zarządzanie aliasami DNS

Dostępne są zarówno interfejsy API REST, jak i polecenia cmdlet programu PowerShell umożliwiające programowe zarządzanie aliasami DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>INTERFEJSY API REST do zarządzania aliasami DNS

Dokumentacja interfejsów API REST jest dostępna w pobliżu następującej lokalizacji sieci Web:

- [Interfejs API REST bazy danych SQL platformy Azure](https://docs.microsoft.com/rest/api/sql/)

Ponadto interfejsy API REST można zobaczyć w usłudze GitHub pod adresem:

- [Serwer bazy danych SQL platformy Azure, alias DNS REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>Program PowerShell do zarządzania aliasami DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Polecenia cmdlet programu PowerShell są dostępne, które wywołują interfejsy API REST.

Przykład kodu poleceń cmdlet programu PowerShell używanych do zarządzania aliasami DNS jest udokumentowany na poziomie:

- [PowerShell dla aliasu DNS do bazy danych SQL usługi Azure](dns-alias-powershell.md)

Polecenia cmdlet używane w przykładzie kodu są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Tworzy nowy alias DNS w systemie usługi Azure SQL Database. Alias odnosi się do serwera 1 bazy danych SQL Azure.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobierz i wyświetl listę wszystkich aliasów DNS przypisanych do serwera bazy danych SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modyfikuje nazwę serwera, do których odnosi się alias, z serwera 1 do serwera BAZY DANYCH SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń alias DNS z serwera bazy danych SQL 2, używając nazwy aliasu.

## <a name="limitations-during-preview"></a>Ograniczenia podczas podglądu

Obecnie alias DNS ma następujące ograniczenia:

- *Opóźnienie do 2 minut:* Aktualizacja lub usunięcie aliasu DNS trwa do 2 minut.
  - Niezależnie od krótkiego opóźnienia alias natychmiast zatrzymuje odwoływanie połączeń klienta do starszego serwera.
- *Wyszukiwanie DNS:* Na razie jedynym miarodajnym sposobem sprawdzenia, do jakiego serwera odwołuje się dany alias DNS, jest wykonanie [wyszukiwania DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Inspekcja tabel nie jest obsługiwana:_ Nie można użyć aliasu DNS na serwerze usługi Azure SQL Database z włączoną *inspekcją tabel* w bazie danych.
  - Inspekcja tabel jest przestarzała.
  - Zaleca się przejście do inspekcji [obiektów blob](sql-database-auditing.md).

## <a name="related-resources"></a>Powiązane zasoby

- [Omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md), w tym odzyskiwania po awarii.

## <a name="next-steps"></a>Następne kroki

- [PowerShell dla aliasu DNS do bazy danych SQL usługi Azure](dns-alias-powershell.md)
