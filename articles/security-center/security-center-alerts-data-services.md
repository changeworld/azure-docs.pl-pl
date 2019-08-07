---
title: Wykrywanie zagrożeń dla usług danych w Azure Security Center | Microsoft Docs
description: W tym temacie przedstawiono alerty usług danych dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 8812ac325e6bfd5ee019f6ddd6bf86c846ed5c10
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782461"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Wykrywanie zagrożeń dla usług danych w Azure Security Center

 Security Center analizuje dzienniki usług magazynu danych i wyzwala alerty w przypadku wykrycia zagrożenia dla zasobów danych. Ten temat zawiera listę alertów generowanych przez Security Center dla następujących usług:

* [Azure SQL Database i SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Azure SQL Database i SQL Data Warehouse<a name="data-sql"></a>

Wykrywanie zagrożeń SQL wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Security Center analizuje dzienniki inspekcji SQL i działa natywnie w aparacie SQL.

|Alerty|Opis|
|---|---|
|**Luka w zabezpieczeniach dla iniekcji SQL**|Aplikacja wygenerowała błędną instrukcję SQL w bazie danych. Może to wskazywać na ewentualną lukę w zabezpieczeniach ataków iniekcji SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji: Usterka w kodzie aplikacji konstruuje błędną instrukcję języka SQL. Lub kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika podczas konstruowania nieprawidłowej instrukcji SQL, która może zostać wykorzystana do iniekcji SQL.|
|**Potencjalna iniekcja SQL**|Nastąpiło aktywne wykorzystanie oprogramowania w odniesieniu do zidentyfikowanych aplikacji narażonych na wstrzyknięcie kodu SQL. Oznacza to, że osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL przy użyciu kodu aplikacji lub procedur składowanych.|
|**Dostęp z nietypowej lokalizacji**|Wzorzec dostępu został zmieniony na program SQL Server, w którym ktoś zalogował się do serwera SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).|
|**Dostęp z nieznanego podmiotu zabezpieczeń**|Wzorzec dostępu został zmieniony na serwer SQL — ktoś zalogował się do programu SQL Server przy użyciu nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).|
|**Dostęp z potencjalnie szkodliwej aplikacji**|Do uzyskiwania dostępu do bazy danych jest używana potencjalnie szkodliwa aplikacja. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.|
|**Wymuszanie poświadczeń SQL**|Wystąpił nietypowo dużą liczbę nieudanych logowań z innymi poświadczeniami. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.|

Aby uzyskać więcej informacji na temat alertów dotyczących wykrywania zagrożeń SQL, zobacz[Azure SQL Database wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)i przeglądanie sekcji alerty dotyczące wykrywania zagrożeń. Zobacz również [, jak Azure Security Center pomaga odsłonić cybernetycznego,](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) aby zobaczyć przykład sposobu, w jaki Security Center użyć złośliwego wykrywania działań SQL w celu odnalezienia ataku.

## Usługa Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępna tylko dla Blob Storage.

Usługa Advanced Threat Protection dla usługi Azure Storage zapewnia dodatkową warstwę analizy bezpieczeństwa, która wykrywa niezwykłe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania kont magazynu. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności posiadania eksperta zabezpieczeń oraz zarządzania systemami monitorowania zabezpieczeń.

Security Center analizuje dzienniki diagnostyczne żądań odczytu, zapisu i usuwania do magazynu obiektów BLOB w celu wykrywania zagrożeń i wyzwala alerty w przypadku wystąpienia anomalii w działaniu. Aby uzyskać więcej informacji, zobacz Aby [skonfigurować rejestrowanie analityka magazynu](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) , aby uzyskać więcej informacji.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Anomalia nietypowego dostępu do lokalizacji**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Remote Desktop Protocol (RDP) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zabezpieczenia zostały naruszone i są teraz używane do rozważenia zewnętrznego punktu końcowego RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Anomalia dostępu do aplikacji**|Wskazuje, że nietypowa aplikacja uzyskuje dostęp do tego konta magazynu. Potencjalną przyczyną jest to, że osoba atakująca uzyskuje dostęp do konta magazynu przy użyciu nowej aplikacji.|
|**Anomalia dostępu anonimowego**|Wskazuje, że istnieje zmiana wzorca dostępu do konta magazynu. Na przykład konto jest uzyskiwane anonimowo (bez uwierzytelniania), które jest nieoczekiwane w porównaniu z ostatnim wzorcem dostępu na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca korzystała z publicznego dostępu do odczytu do kontenera, który przechowuje magazyn obiektów BLOB.|
|**Anomalia tor**|Wskazuje, że do tego konta uzyskano dostęp pomyślnie z adresu IP, który jest znany jako aktywny węzeł zakończenia tor (anonymizing serwer proxy). Ważność tego alertu uwzględnia używany typ uwierzytelniania (jeśli istnieje), a także to, czy jest to pierwszy przypadek takiego dostępu. Potencjalnymi przyczynami może być osoba atakująca, która uzyskała dostęp do konta magazynu przy użyciu sieci Tor lub dostęp do konta magazynu za pośrednictwem zaufanego użytkownika przy użyciu tor.|
|**Anomalia Eksfiltracjii danych**|Wskazuje, że nietypowo duża ilość danych została wyodrębniona w porównaniu do ostatnich działań w tym kontenerze magazynu. Potencjalną przyczyną jest to, że atakujący wyodrębnił dużą ilość danych z kontenera, który przechowuje magazyn obiektów BLOB.|
|**Nieoczekiwana usuwanie anomalii**|Wskazuje, że co najmniej jedna Nieoczekiwana operacja usuwania została wystąpiła na koncie magazynu w porównaniu z ostatnią aktywnością na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca usunęła dane z konta magazynu.|
|**Przekaż pakiet usługi w chmurze platformy Azure**|Wskazuje, że pakiet usługi w chmurze platformy Azure (plik. cspkg) został przekazany do konta magazynu w nietypowy sposób w porównaniu z ostatnią aktywnością na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca przygotowuje się do wdrożenia złośliwego kodu z konta magazynu w usłudze w chmurze platformy Azure.|
|**Anomalia dostępu do uprawnień**|Wskazuje, że uprawnienia dostępu do tego kontenera magazynu zostały zmienione w nietypowy sposób. Potencjalną przyczyną jest to, że osoba atakująca zmieniła uprawnienia kontenera, aby osłabić stan zabezpieczeń lub uzyskać trwałość.|
|**Anomalia dostępu do inspekcji**|Wskazuje, że uprawnienia dostępu do konta magazynu zostały sprawdzone w nietypowy sposób w porównaniu z ostatnią aktywnością na tym koncie. Potencjalną przyczyną jest to, że atakujący wykonał rekonesans w celu przyszłego ataku.|
|**Anomalia eksploracji danych**|Wskazuje, że obiekty blob lub kontenery na koncie magazynu zostały wyliczone w nietypowy sposób, w porównaniu do ostatnich działań na tym koncie. Potencjalną przyczyną jest to, że atakujący wykonał rekonesans w celu przyszłego ataku.|

>[!NOTE]
>Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie niedostępna w regionach w chmurze platformy Azure dla instytucji rządowych.

Aby uzyskać więcej informacji na temat alertów dotyczących magazynu, zobacz artykuł [Advanced Threat Protection for Azure Storage](../storage/common/storage-advanced-threat-protection.md) i zapoznaj się z sekcją alerty dotyczące ochrony.

## Cosmos DB<a name="cosmos-db"></a>

Następujące alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont Azure Cosmos DB lub ich wykorzystania:

|Alerty|Opis|
|---|---|
|**Dostęp z nietypowej lokalizacji**|Wskazuje, że w wzorcu dostępu wprowadzono zmianę dla konta Cosmos DB. Ktoś uzyskał dostęp do tego konta z nieznanego adresu IP w porównaniu z ostatnio używanymi działaniami. Osoba atakująca uzyskała dostęp do konta Cosmos DB lub uprawniony użytkownik uzyskał dostęp do konta Cosmos DB z nowej i nietypowej lokalizacji geograficznej. Na przykład: Nowa aplikacja lub konserwacja dewelopera ze zdalnego.|
|**Nietypowe eksfiltracji danych**|Wskazuje, że wystąpił zmiana wzorca wyodrębniania danych z konta Cosmos DB. Ktoś wyodrębnił nietypową ilość danych w porównaniu do ostatnich działań. Osoba atakująca wyodrębnił dużą ilość danych z bazy danych Cosmos DB. Na przykład: eksfiltracji danych/wyciek, nieautoryzowany transfer danych. Lub uprawniony użytkownik lub aplikacja wyodrębnił nietypową ilość danych z kontenera. Na przykład: działanie tworzenia kopii zapasowej konserwacji.|

Aby uzyskać więcej informacji, zobacz [Advanced Threat Protection for Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).