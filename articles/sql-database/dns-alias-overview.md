---
title: Alias systemu DNS dla usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Aplikacje mogą łączyć się alias dla nazwy serwera Azure SQL Database. W tym samym czasie można zmienić bazy danych SQL, alias wskazuje w dowolnym momencie, aby ułatwić testowanie i tak dalej.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,ayolubek, jrasnick
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 9704acee2ca8bad7437ae22ff5041e2253916dce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160801"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias systemu DNS dla usługi Azure SQL Database

Usługa Azure SQL Database zawiera serwer systemu nazw domen (DNS, Domain Name System). Zaakceptuj programu PowerShell i interfejsów API REST [wywołania do tworzenia i zarządzania nimi aliasów DNS](#anchor-powershell-code-62x) dla nazwy serwera bazy danych SQL.

A *DNS alias* można użyć zamiast nazwy serwera Azure SQL Database. Programy klienckie można użyć w swoich parametrów połączenia. DNS alias zapewnia warstwę tłumaczenia, który można przekierować programów klienckich do różnych serwerów. Ta warstwa zaoszczędzi trudności z konieczności wyszukiwanie i edytowanie wszystkich klientów i ich parametry połączenia.

Najczęstsze zastosowania dla aliasu DNS obejmują następujących przypadkach:

- Utwórz, które są łatwe do zapamiętania nazwę serwera SQL Azure.
- Podczas początkowego tworzenia aliasu mogą odwoływać się do testu bazy danych programu SQL server. Gdy aplikacja przechodzi na żywo, można zmodyfikować alias do odwoływania się do serwera produkcyjnego. Przejście od testów do środowiska produkcyjnego nie wymaga żadnych modyfikacji konfiguracji kilku klientów łączących się z serwerem bazy danych.
- Załóżmy, że bazy danych tylko w aplikacji zostanie przeniesiony do innego serwera bazy danych SQL. W tym miejscu możesz zmodyfikować aliasu, bez konieczności modyfikowania konfiguracji kilku klientów.

## <a name="domain-name-system-dns-of-the-internet"></a>System nazw domen (DNS) w Internecie

Internet opiera się na DNS. DNS przetwarza przyjazne nazwy na nazwę serwera usługi Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scenariusze obejmujące jeden alias systemu DNS

Załóżmy, że chcesz zmienić system na nowy serwer usługi Azure SQL Database. W przeszłości trzeba było znaleźć i zaktualizować parametry połączenia, co w każdym programie klienta. Ale teraz, jeśli parametry połączenia Użyj aliasu DNS, właściwością alias musi zostać zaktualizowany.

Funkcja alias DNS usługi Azure SQL Database może pomóc w następujących scenariuszach:

### <a name="test-to-production"></a>Testowanie w środowisku produkcyjnym

Po rozpoczęciu tworzenia programów klienckich, poproś Użyj aliasu DNS w ich parametry połączenia. Właściwości punktu alias dokonać kopię serwera usługi Azure SQL Database.

Później po nowy system przechodzi w środowisku produkcyjnym, można zaktualizować właściwości aliasu, aby wskazywały serwer bazy danych SQL w produkcji. Niezbędne jest Brak zmian programów klienckich.

### <a name="cross-region-support"></a>Obsługa między regionami

Odzyskiwanie po awarii może być shift serwera usługi SQL Database do innego regionu geograficznego. System nie został używa aliasu DNS, trzeba znaleźć i zaktualizować parametry połączenia dla wszystkich klientów można uniknąć. Zamiast tego należy zaktualizować alias do odwoływania się do nowego serwera bazy danych SQL, który teraz udostępnia bazę danych.

## <a name="properties-of-a-dns-alias"></a>Właściwości alias systemu DNS

Następujące właściwości mają zastosowanie do każdego alias DNS serwera usługi SQL Database:

- *Unikatowa nazwa:* Każda nazwa aliasu, tworzonych jest unikatowa na wszystkich serwerach usługi Azure SQL Database, po prostu nazwy serwera.
- *Wymagany jest serwer:* DNS alias nie można utworzyć chyba że odwołuje się do dokładnie jednego serwera, a serwer musi już istnieć. Zaktualizowano alias zawsze musi odwoływać się dokładnie jeden serwer.
  - Gdy upuścisz suszarkę serwera usługi SQL Database, Azure system również porzuca wszystkie aliasy DNS, które odwołują się do serwera.
- *Nie jest powiązany z dowolnego regionu:* Aliasy DNS nie są powiązane z obszarem. Aliasy DNS może zostać zaktualizowana do odwoływania się do serwera Azure SQL Database, która znajduje się w dowolnym regionie geograficznym.
  - Jednak podczas aktualizowania alias do odwoływania się do innego serwera, oba serwery muszą być umieszczone w tym samym *subskrypcji*.
- *Uprawnienia:* Aby zarządzać alias systemu DNS, użytkownik musi mieć *Współautor serwera* uprawnienia, lub nowszej. Aby uzyskać więcej informacji, zobacz [wprowadzenie opartej na rolach kontrola dostępu w witrynie Azure portal](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Zarządzanie aliasy DNS

Polecenia cmdlet programu PowerShell i interfejsów API REST są dostępne dla umożliwiają programowe zarządzanie aliasy DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>Interfejsów API REST zarządzania aliasy DNS

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

W dokumentacji dotyczącej interfejsów API REST jest dostępny w następującej lokalizacji w sieci web:

- [Usługa Azure SQL Database interfejsu API REST](https://docs.microsoft.com/rest/api/sql/)

Ponadto interfejsów API REST są widoczne w witrynie GitHub pod:

- [Serwer bazy danych SQL Azure, DNS alias interfejsów API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>Program PowerShell do zarządzania aliasy DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Polecenia cmdlet programu PowerShell są dostępne, wywołania interfejsów API REST.

Przykładowy kod poleceń cmdlet programu PowerShell jest używany do zarządzania aliasów DNS jest udokumentowany na:

- [Program PowerShell dla aliasu DNS do usługi Azure SQL Database](dns-alias-powershell.md)

Polecenia cmdlet używanych w przykładzie kodu są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Tworzy nowy alias DNS w systemie usługi Azure SQL Database. Alias odwołuje się do serwera Azure SQL Database 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobieranie i wyświetlanie wszystkie aliasy DNS, które są przypisane do serwera bazy danych SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Zmienia nazwę serwera, którą skonfigurowano aliasu dotyczą z serwera 1 serwer bazy danych SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń DNS alias z serwera bazy danych SQL 2, przy użyciu nazwy aliasu.

## <a name="limitations-during-preview"></a>Ograniczenia wersji zapoznawczej

Obecnie systemie DNS alias ma następujące ograniczenia:

- *Opóźnienie do 2 minut:* Trwa maksymalnie 2 minuty, alias DNS należy zaktualizować lub usunąć.
  - Niezależnie od wszelkich krótkie opóźnienie alias natychmiast zatrzymuje odwołujące się połączeń klientów do starszej wersji serwera.
- *Wyszukiwanie DNS:* Obecnie tylko autorytatywne sposób, aby sprawdzić, jakiego serwera DNS danego alias odwołuje się do, wykonując [wyszukiwania DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- *[Inspekcja tabeli nie jest obsługiwana.](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* Nie można użyć aliasu DNS na serwerze usługi Azure SQL Database, która ma *inspekcji tabel* włączona w bazie danych.
  - Inspekcja tabel jest przestarzała.
  - Firma Microsoft zaleca, aby przenieść się do [inspekcji obiektów Blob](sql-database-auditing.md).

## <a name="related-resources"></a>Powiązane zasoby

- [Omówienie ciągłości działania usługi Azure SQL Database](sql-database-business-continuity.md), w tym odzyskiwania po awarii.

## <a name="next-steps"></a>Kolejne kroki

- [Program PowerShell dla aliasu DNS do usługi Azure SQL Database](dns-alias-powershell.md)
