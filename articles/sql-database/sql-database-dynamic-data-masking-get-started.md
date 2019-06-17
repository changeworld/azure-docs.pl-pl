---
title: Usługa Azure SQL Database dynamiczne maskowanie danych | Dokumentacja firmy Microsoft
description: Baza danych SQL dynamiczne maskowanie danych ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2019
ms.openlocfilehash: 1db1535779d180994c9ce4350d11f4c696da9e3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721531"
---
# <a name="sql-database-dynamic-data-masking"></a>Baza danych SQL dynamiczne maskowanie danych

Baza danych SQL dynamiczne maskowanie danych ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. 

Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając klientom wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

Na przykład z przedstawicielem w Centrum połączenia może identyfikować obiekty wywołujące kilka cyfry numeru karty kredytowej, ale te elementy danych nie należy całkowicie uwidaczniać do przedstawiciela biura obsługi. Czy maski wszystkie, ale cztery ostatnie cyfry wszelkie numer karty kredytowej, w wyniku zestaw dowolnego zapytania można zdefiniować regułę maskowania. Inny przykład można zdefiniować maskę odpowiednie dane do ochrony danych identyfikowalne dane osobowe (PII), dzięki czemu deweloper może zapytania środowisk produkcyjnych na potrzeby rozwiązywania problemów bez naruszania kryteria zgodności z przepisami.

## <a name="sql-database-dynamic-data-masking-basics"></a>Baza danych SQL dynamiczne maskowanie podstawowe informacje o danych

Dane dynamiczne maskowanie zasady w witrynie Azure portal, wybierając dane dynamiczne maskowanie operacji w bloku konfiguracji bazy danych SQL lub w bloku ustawienia należy skonfigurować.

### <a name="dynamic-data-masking-permissions"></a>Dane dynamiczne maskowanie uprawnień

Dynamiczne maskowanie danych mogą być konfigurowane przez administratora usługi Azure SQL Database, administrator serwera lub [Menedżer zabezpieczeń SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ról.

### <a name="dynamic-data-masking-policy"></a>Dane dynamiczne maskowanie zasad

* **Użytkownicy SQL wykluczeni z maskowania** — A zbiór użytkowników SQL lub tożsamości usługi AAD, które pobierają dane pozbawiony maskowania w SQL wyniki zapytania. Użytkownicy z uprawnieniami administratora są zawsze wykluczeni z maskowania i zobaczyć oryginalne dane bez żadnych maski.
* **Reguły maskowania** — zestaw reguł, które definiują wyznaczonych polach maskowane i funkcji maskowania, który jest używany. Można zdefiniować wyznaczonych polach przy użyciu nazwy schematu bazy danych, nazwę tabeli i nazwę kolumny.
* **Maskowanie funkcje** — zestaw metod, które kontrolują ujawnienia danych dla różnych scenariuszy.

| Funkcja maskowania | Maskowanie logiki |
| --- | --- |
| **Domyślne** |**Pełne maskowania zgodnie z typów danych w wyznaczonych polach**<br/><br/>• Użycie XXXX lub mniej Xs, jeśli rozmiar pola jest mniejszy niż 4 znaki dla danych typu ciąg (nchar, ntext, nvarchar).<br/>• Wykorzystanie wartości 0 dla typów numerycznych (bigint, bit, decimal, int, pieniądze, numeryczne, smallint, smallmoney, tinyint, float, rzeczywistym).<br/>• Wykorzystanie 01-01-1900 dla typów danych daty/godziny (Data, datetime2, datetime, datetimeoffset, smalldatetime, czasu).<br/>• Dla wariantu języka SQL, wartością domyślną bieżącego typu jest używany.<br/>• Dla formatu XML w dokumencie \<maskowane / > jest używane.<br/>• Na użytek pustą wartość specjalne typy danych (tabeli sygnatury czasowej, hierarchyid, GUID, plik binarny, obraz, varbinary typów przestrzennych). |
| **Karta kredytowa** |**Maskowanie metody, która udostępnia cztery ostatnie cyfry wyznaczonych polach** i dodaje ciąg stałej jako prefiks w postaci karty kredytowej.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Wiadomość e-mail** |**Maskowanie metody, która udostępnia pierwszą literę i zamienia domeny XXX.com** przy użyciu prefiksu stałym ciągiem w postaci adresu e-mail.<br/><br/>aXX@XXXX.com |
| **Liczby losowe** |**Maskowanie metody, która generuje losową liczbę** zgodnie z wybranych granicach i typy danych rzeczywistych. Jeśli wyznaczonych granicach są równe, funkcji maskowania jest stałej liczbowej.<br/><br/>![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Niestandardowy tekst** |**Metoda, która udostępnia pierwszy i ostatni znak maskowania** i dodaje dopełnienie niestandardowy ciąg w środku. Jeśli oryginalny ciąg jest krótszy niż ujawniany prefiks i sufiks, tylko ciąg wypełnienia jest używany. <br/>prefix[padding]suffix<br/><br/>![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Zalecane pola do zamaskowania

Aparat zaleceń DDM flag określonych pól z bazy danych jako potencjalnie poufnych pola, które mogą być odpowiednimi obiektami do maskowania. W bloku funkcja dynamiczne maskowanie danych w portalu zobaczysz zalecane kolumny bazy danych. To wszystko, czego potrzebujesz, aby zrobić, kliknij przycisk **Dodaj maskę** dla co najmniej jednej kolumny i następnie **Zapisz** do zastosowania do maski dla tych pól.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Skonfigurować dynamiczne maskowanie danych dla bazy danych przy użyciu poleceń cmdlet programu PowerShell

Zobacz [polecenia cmdlet usługi Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurowanie dynamicznego maskowania danych dla bazy danych przy użyciu interfejsu API REST

Zobacz [operacji dla usługi Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
