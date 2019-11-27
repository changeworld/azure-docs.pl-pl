---
title: Wykrywanie zagrożeń dla usług danych w Azure Security Center | Microsoft Docs
description: W tym artykule przedstawiono alerty usług danych dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 8b99d89e8895cd1c8d8e9fe3961f0db9855fb7ee
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196128"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Wykrywanie zagrożeń dla usług danych w Azure Security Center

 Azure Security Center analizuje dzienniki usług magazynu danych i wyzwala alerty w przypadku wykrycia zagrożenia dla zasobów danych. W tym artykule wymieniono alerty, które Security Center wygenerowały dla następujących usług:

* [Azure SQL Database i Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database i SQL Data Warehouse<a name="data-sql"></a>

Wykrywanie zagrożeń SQL identyfikuje anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. 

|Alerty|Opis|
|---|---|
|**Możliwa Luka w zabezpieczeniach iniekcji SQL**|Aplikacja wygenerowała błędną instrukcję SQL w bazie danych. Może to wskazywać na ewentualną lukę w zabezpieczeniach ataków iniekcji SQL. Istnieją dwie możliwe przyczyny błędnej instrukcji. Usterka w kodzie aplikacji mogła skonstruować błędną instrukcję języka SQL. Lub kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas konstruowania nieprawidłowej instrukcji SQL, która może zostać wykorzystana do iniekcji SQL.|
|**Potencjalna iniekcja SQL**|Nastąpiło aktywne wykorzystanie oprogramowania w odniesieniu do zidentyfikowanych aplikacji narażonych na wstrzyknięcie kodu SQL. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL, korzystając z kodu aplikacji lub procedur składowanych.|
|**Logowanie się z nietypowej lokalizacji**|Wzorzec dostępu został zmieniony na SQL Server, w którym ktoś zalogował się na serwerze z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach alert wykrywa złośliwe działanie (byłego pracownika lub ataku zewnętrznego).|
|**Logowanie za pomocą nieznanego podmiotu zabezpieczeń**|W wzorcu dostępu wprowadzono zmianę w celu SQL Server. Ktoś zalogował się na serwerze przy użyciu nietypowego podmiotu zabezpieczeń (użytkownika). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach alert wykrywa złośliwe działanie (byłego pracownika lub ataku zewnętrznego).|
|**Próba logowania przez potencjalnie szkodliwą aplikację**|Do uzyskiwania dostępu do bazy danych jest używana potencjalnie szkodliwa aplikacja. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak wykorzystujący typowe narzędzia.|
|**Potencjalna próba nawiązania instrukcji SQL**|Wystąpił nietypowo wysoki numer nieudanych logowań z innymi poświadczeniami. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak z wykorzystaniem pełnego wymuszania.|

Aby uzyskać więcej informacji na temat alertów dotyczących wykrywania zagrożeń SQL, zobacz [Azure SQL Database wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). W szczególności zapoznaj się z sekcją alerty dotyczące wykrywania zagrożeń. Zobacz również [, jak Azure Security Center pomaga odsłonić cybernetycznego,](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) aby zobaczyć przykład sposobu, w jaki Security Center użyć złośliwego wykrywania działań SQL w celu odnalezienia ataku.

## Usługa Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Zaawansowana ochrona przed zagrożeniami dla magazynu jest obecnie dostępna tylko dla usługi BLOB Storage.

Zaawansowana ochrona przed zagrożeniami dla magazynu stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont magazynu lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności posiadania eksperta zabezpieczeń oraz zarządzania systemami monitorowania zabezpieczeń.

Security Center analizuje dzienniki diagnostyczne żądań odczytu, zapisu i usuwania do magazynu obiektów BLOB w celu wykrywania zagrożeń i wyzwala alerty w przypadku wystąpienia anomalii w działaniu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rejestrowania analityka magazynu](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Dostęp z nietypowej lokalizacji do konta magazynu**|Wskazuje, że wprowadzono zmianę wzorca dostępu do konta usługi Azure Storage. Ktoś uzyska dostęp do tego konta z adresu IP uznanego za nieznane w porównaniu z ostatnim działaniem. Osoba atakująca uzyskała dostęp do konta lub uprawniony użytkownik nawiązał połączenie z nowej lub nietypowej lokalizacji geograficznej. Przykładem tej ostatniej usługi jest zdalna konserwacja z nowej aplikacji lub dewelopera.|
|**Nietypowa aplikacja uzyskała dostęp do konta magazynu**|Wskazuje, że nietypowa aplikacja uzyskuje dostęp do tego konta magazynu. Potencjalną przyczyną jest to, że osoba atakująca uzyskuje dostęp do konta magazynu przy użyciu nowej aplikacji.|
|**Anonimowy dostęp do konta magazynu**|Wskazuje, że istnieje zmiana wzorca dostępu do konta magazynu. Na przykład konto jest uzyskiwane anonimowo (bez uwierzytelniania), które jest nieoczekiwane w porównaniu z ostatnim wzorcem dostępu na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca korzystała z publicznego dostępu do odczytu do kontenera, który przechowuje magazyn obiektów BLOB.|
|**Dostęp z węzła wyjścia tor do konta magazynu**|Wskazuje, że do tego konta uzyskano dostęp pomyślnie z adresu IP, który jest znany jako aktywny węzeł zakończenia tor (anonymizing serwer proxy). Ważność tego alertu uwzględnia używany typ uwierzytelniania (jeśli istnieje), a także to, czy jest to pierwszy przypadek takiego dostępu. Potencjalnymi przyczynami może być osoba atakująca, która uzyskała dostęp do konta magazynu przy użyciu sieci Tor lub uprawnionych użytkowników, którzy uzyskali dostęp do konta magazynu przy użyciu sieci Tor.|
|**Nietypowa ilość danych wyodrębnionych z konta magazynu**|Wskazuje, że nietypowo duża ilość danych została wyodrębniona w porównaniu do ostatnich działań w tym kontenerze magazynu. Potencjalną przyczyną jest to, że atakujący wyodrębnił dużą ilość danych z kontenera, który przechowuje magazyn obiektów BLOB.|
|**Nietypowe usuwanie na koncie magazynu**|Wskazuje, że co najmniej jedna Nieoczekiwana operacja usuwania została wystąpiła na koncie magazynu w porównaniu z ostatnią aktywnością na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca usunęła dane z konta magazynu.|
|**Nietypowe przekazywanie elementu. cspkg do konta magazynu**|Wskazuje, że pakiet Cloud Services platformy Azure (plik. cspkg) został przekazany do konta magazynu w nietypowy sposób w porównaniu z ostatnią aktywnością na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca przygotowuje się do wdrożenia złośliwego kodu z konta magazynu w usłudze w chmurze platformy Azure.|
|**Nietypowa zmiana uprawnień dostępu na koncie magazynu**|Wskazuje, że uprawnienia dostępu do tego kontenera magazynu zostały zmienione w nietypowy sposób. Potencjalną przyczyną jest to, że osoba atakująca zmieniła uprawnienia kontenera, aby osłabić stan zabezpieczeń lub uzyskać trwałość.|
|**Nietypowa Inspekcja dostępu na koncie magazynu**|Wskazuje, że uprawnienia dostępu do konta magazynu zostały sprawdzone w nietypowy sposób w porównaniu z ostatnią aktywnością na tym koncie. Potencjalną przyczyną jest to, że atakujący wykonał rekonesans w celu przyszłego ataku.|
|**Nietypowa Eksploracja danych na koncie magazynu**|Wskazuje, że obiekty blob lub kontenery na koncie magazynu zostały wyliczone w nietypowy sposób, w porównaniu do ostatnich działań na tym koncie. Potencjalną przyczyną jest to, że atakujący wykonał rekonesans w celu przyszłego ataku.|
|**WERSJA zapoznawcza — potencjalne złośliwe oprogramowanie przekazane do konta magazynu**|Wskazuje, że obiekt BLOB zawierający potencjalne złośliwe oprogramowanie zostało przekazane do konta magazynu. Potencjalne przyczyny mogą obejmować zamierzone złośliwe oprogramowanie przez osobę atakującą lub niezamierzone przekazywanie, potencjalnie złośliwego obiektu BLOB przez uprawnionego użytkownika.|

>[!NOTE]
>Zaawansowana ochrona przed zagrożeniami dla magazynu nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

Aby uzyskać więcej informacji na temat alertów dotyczących magazynu, zobacz [Advanced Threat Protection for Azure Storage](../storage/common/storage-advanced-threat-protection.md). W szczególności zapoznaj się z sekcją "alerty ochrony".

## Azure Cosmos DB<a name="cosmos-db"></a>

Następujące alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont Azure Cosmos DB lub ich wykorzystania:

|Alerty|Opis|
|---|---|
|**Dostęp z nietypowej lokalizacji do konta Cosmos DB**|Wskazuje, że w wzorcu dostępu wprowadzono zmianę dla konta Azure Cosmos DB. Ktoś uzyskał dostęp do tego konta z nieznanego adresu IP w porównaniu z ostatnio używanymi działaniami. Osoba atakująca uzyskała dostęp do konta lub uzyskała dostęp do niego z nowej i nietypowej lokalizacji geograficznej. Przykładem tej ostatniej usługi jest zdalna konserwacja z nowej aplikacji lub dewelopera.|
|**Nietypowa ilość danych wyodrębnionych z konta Cosmos DB**|Wskazuje, że wystąpił zmiana wzorca wyodrębniania danych z konta Azure Cosmos DB. Ktoś wyodrębnił nietypową ilość danych w porównaniu do ostatnich działań. Osoba atakująca mogła wyodrębnić znaczną ilość danych z bazy danych Azure Cosmos DB (na przykład eksfiltracji danych lub wyciek lub nieautoryzowany transfer danych). Lub uprawniony użytkownik lub aplikacja wykryła nietypową ilość danych z kontenera (na przykład dla działania tworzenia kopii zapasowej konserwacji).|

Aby uzyskać więcej informacji, zobacz [Advanced Threat Protection for Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).
