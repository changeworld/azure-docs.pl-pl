---
title: Dynamiczne maskowanie danych
description: Dynamiczne maskowanie danych ogranicza ekspozycję poufnych danych, maskując ją użytkownikom nieuprzywilejowanym dla bazy danych SQL i platformy Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192918"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dynamiczne maskowanie danych dla usługi Azure SQL Database i usługi Azure Synapse Analytics

Funkcja dynamicznego maskowania danych w bazie danych SQL ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. 

Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając klientom wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

Na przykład przedstawiciel usługi w centrum obsługi może identyfikować dzwoniących za pomocą kilku cyfr numeru karty kredytowej, ale te elementy danych nie powinny być w pełni narażone na przedstawiciela usługi. Można zdefiniować regułę maskowania maskującą, która maskuje wszystkie z tych ostatnich czterech cyfr dowolnego numeru karty kredytowej w zestawie wyników dowolnego zapytania. Innym przykładem można zdefiniować odpowiednią maskę danych w celu ochrony danych umożliwiających identyfikację użytkownika, dzięki czemu deweloper może wysyłać zapytania do środowisk produkcyjnych w celu rozwiązywania problemów bez naruszania przepisów dotyczących zgodności.

## <a name="dynamic-data-masking-basics"></a>Podstawowe informacje o maskowaniu danych dynamicznych

Konfigurowanie zasad maskowania danych dynamicznych w witrynie Azure portal, wybierając operację maskowania danych dynamicznych w bloku konfiguracji bazy danych SQL lub bloku ustawień. Tej funkcji nie można ustawić przy użyciu portalu dla platformy Azure Synapse (użyj powershell lub REST API)

### <a name="dynamic-data-masking-permissions"></a>Uprawnienia do maskowania danych dynamicznych

Dynamiczne maskowanie danych może być skonfigurowane przez administratora usługi Azure SQL Database, administratora serwera lub menedżera [zabezpieczeń SQL.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Zasady dynamicznego maskowania danych

* **Użytkownicy SQL wykluczeni z maskowania** — zestaw użytkowników SQL lub tożsamości usługi AAD, które otrzymują zdemaskowane dane w wynikach kwerendy SQL. Użytkownicy z uprawnieniami administratora są zawsze wykluczeni z maskowania i widzą oryginalne dane bez maski.
* **Reguły maskowania** — zestaw reguł definiujących wyznaczone pola do maskowania i funkcję maskowania, która jest używana. Wyznaczone pola można zdefiniować przy użyciu nazwy schematu bazy danych, nazwy tabeli i nazwy kolumny.
* **Funkcje maskowania** — zestaw metod, które kontrolują ekspozycję danych dla różnych scenariuszy.

| Funkcja maskowania | Logika maskowania |
| --- | --- |
| **Domyślny** |**Pełne maskowanie zgodnie z typami danych wyznaczonych pól**<br/><br/>• Użyj XXXX lub mniej Xs, jeśli rozmiar pola jest mniejszy niż 4 znaki dla typów danych ciągów (nchar, ntext, nvarchar).<br/>• Użyj wartości zerowej dla typów danych liczbowych (bigint, bit, dziesiętny, int, pieniądze, numeryczne, smallint, smallmoney, tinyint, float, real).<br/>• Użyj 01-01-1900 dla typów danych daty / godziny (data, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• W przypadku wariantu SQL używana jest wartość domyślna bieżącego typu.<br/>• W przypadku \<formatu XML używany jest dokument zamaskowany/>.<br/>• Użyj pustej wartości dla specjalnych typów danych (tabela sygnatury czasowej, hierarchyid, GUID, binarny, obraz, varbinary typów przestrzennych). |
| **Karta kredytowa** |**Metoda maskowania, która udostępnia ostatnie cztery cyfry wyznaczonych pól** i dodaje stały ciąg jako prefiks w postaci karty kredytowej.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Adres e-mail** |**Metoda maskowania, która udostępnia pierwszą literę i zastępuje domenę XXX.com** przy użyciu stałego prefiksu ciągu w postaci adresu e-mail.<br/><br/>aXX@XXXX.com |
| **Liczba losowa** |**Metoda maskowania, która generuje liczbę losową** zgodnie z wybranymi granicami i rzeczywistymi typami danych. Jeśli wyznaczone granice są równe, funkcja maskowania jest stałą liczbą.<br/><br/>![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Tekst niestandardowy** |**Metoda maskowania, która udostępnia pierwsze i ostatnie znaki** i dodaje niestandardowy ciąg dopełnienia w środku. Jeśli oryginalny ciąg jest krótszy niż narażony prefiks i sufiks, używany jest tylko ciąg dopełnienie. <br/>prefiks[dopełnienie]sufiks<br/><br/>![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Zalecane pola do maskowania

Aparat zaleceń DDM, flagi niektórych pól z bazy danych jako potencjalnie poufnych pól, które mogą być dobrymi kandydatami do maskowania. W bloku dynamiczne maskowanie danych w portalu zostaną wyświetlona polecane kolumny bazy danych. Wszystko, co musisz zrobić, to kliknąć przycisk **Dodaj maskę** dla jednej lub więcej kolumn, a następnie **Zapisz,** aby zastosować maskę dla tych pól.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurowanie dynamicznego maskowania danych bazy danych przy użyciu poleceń cmdlet programu PowerShell

Zobacz [polecenia cmdlet bazy danych SQL platformy Azure](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurowanie dynamicznego maskowania danych dla bazy danych przy użyciu interfejsu REST API

Zobacz [Operacje dla usługi Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
