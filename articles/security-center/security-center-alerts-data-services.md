---
title: Wykrywanie dla usług danych w usłudze Azure Security Center zagrożeń | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono alertów usług danych dostępnych w usłudze Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626291"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Wykrywanie zagrożeń dla usług danych w usłudze Azure Security Center

 Usługa Security Center analizuje dzienniki usługi magazynu danych i wyzwalania alertów po wykryciu zagrożeniem dla zasobów danych. Ten temat zawiera listę alertów, które usługa Security Center generuje następujące usługi:

* [Usługa Azure SQL Database i SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)

## Usługa Azure SQL Database i SQL Data Warehouse <a name="data-sql"></a>

Zagrożeń SQL wykrywa nietypowe działania wskazujące na nietypowe i potencjalnie szkodliwe, wykrywanie podejmie próbę dostępu lub wykorzystania baz danych. Usługa Security Center analizuje dzienniki inspekcji SQL i działa natywnie w aparacie programu SQL.

|Alerty|Opis|
|---|---|
|**Podatność na iniekcję SQL**|Aplikacja wygenerowała błędnej instrukcji SQL w bazie danych. Może to oznaczać możliwe luki w zabezpieczeniach umożliwiające ataki przez wstrzyknięcie kodu SQL. Istnieją dwie możliwe przyczyny generowania błędnej instrukcji: Albo Usterka w kodzie aplikacji skonstruowane błędnej instrukcji SQL. Możesz też kod aplikacji lub procedury składowane nie oczyszczają danych wejściowych użytkownika, podczas tworzenia błędnej instrukcji SQL, które można wykorzystać do iniekcji SQL.|
|**Potencjalna iniekcja SQL**|Aktywnego wykorzystania wystąpił w odniesieniu do określonych aplikacji podatny na iniekcji SQL. Oznacza to, osoba atakująca próbuje wstrzyknąć złośliwe instrukcje SQL przy użyciu podatnego kodu aplikacji lub procedur składowanych.|
|**Dostęp z nietypowej lokalizacji**|Nastąpiła w przypadku zmiany wzorca dostępu do programu SQL server, gdy ktoś zalogował się z serwerem SQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).|
|**Dostęp z nieznanego podmiotu zabezpieczeń**|Wprowadzono w przypadku zmiany wzorca dostępu do programu SQL server — ktoś zalogował się do programu SQL server za pomocą nietypowego podmiotu zabezpieczeń (użytkownika SQL). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).|
|**Dostęp z potencjalnie szkodliwej aplikacji**|Potencjalnie szkodliwej aplikacji został użyty do dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.|
|**Atak siłowy na poświadczenia SQL**|Wystąpił błąd nietypowo dużej liczby nieudanych prób logowania przy użyciu różnych poświadczeń. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.|

Aby uzyskać więcej informacji na temat, zobacz alerty wykrywania zagrożeń SQL[wykrywania zagrożeń usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)i zapoznaj się z sekcją alerty wykrywania zagrożeń. Zobacz też [jak usługa Azure Security Center pomaga ujawnić Cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) Aby wyświetlić przykład sposobu użycia usługi Security Center wykrywanie złośliwego SQL do wykrywania ataków.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępny dla magazynu obiektów Blob tylko. 

Usługa Advanced Threat Protection dla usługi Azure Storage zapewnia dodatkową warstwę analizy bezpieczeństwa, która wykrywa niezwykłe i potencjalnie szkodliwe próby uzyskania dostępu lub wykorzystania kont magazynu. Ta warstwa ochrony pozwala reagowanie na zagrożenia bez konieczności dysponowania eksperta zabezpieczeń i zarządzania systemami monitorowania bezpieczeństwa.

Usługa Security Center analizuje dzienniki diagnostyczne, odczytu, zapisu i żądań delete do magazynu obiektów Blob w celu wykrywania zagrożeń i wyzwalania alertów, gdy wystąpią nieprawidłowości w działaniu. Aby uzyskać więcej informacji, zobacz do [skonfigurować rejestrowanie dla usługi Storage Analytics](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) Aby uzyskać więcej informacji.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Anomalii dostępu do nietypowych lokalizacji**|Analiza ruchu próbkowanych sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop) pochodzące z zasobów w danym wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zasób bezpieczeństwo zostało naruszone i obecnie jest używana do atak siłowy jest zewnętrzny punkt końcowy protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Anomalii dostępu do aplikacji**|Wskazuje, że aplikacja nietypowe uzyskał dostęp do tego konta magazynu. Potencjalną przyczyną jest to, że osoba atakująca uzyskał dostęp do konta magazynu przy użyciu nowej aplikacji.|
|**Dostęp anonimowy anomalii**|Wskazuje, że w przypadku zmiany wzorca dostępu do konta magazynu. Na przykład konto uzyskano dostęp anonimowo (bez żadnego uwierzytelniania), który jest nieoczekiwany w porównaniu z ostatnich wzorzec dostępu do tego konta. Potencjalną przyczyną jest, czy osoba atakująca luki publicznego dostępu do odczytu do kontenera, że przechowuje obiektów blob magazynu.|
|**Anomalii wykradanie danych**|Wskazuje, że został wyodrębniony nietypowo dużej ilości danych w porównaniu do ostatnią aktywność, w tym kontenerze magazynu. Potencjalną przyczyną jest osoba atakująca ma wyodrębnione dużej ilości danych z kontenera, że przechowuje obiektów blob magazynu.|
|**Nieoczekiwane usunięcie anomalii**|Wskazuje, że na koncie magazynu, w porównaniu z ostatnią aktywność na tym koncie wystąpił co najmniej jednej operacji usuwania nieoczekiwany. Potencjalną przyczyną jest osoba atakująca ma usunięte dane z konta magazynu.|
|**Przekaż pakiet usługi w chmurze Azure**|Wskazuje, czy pakiet usługi w chmurze Azure (plik cspkg) został przekazany do konta magazynu w nietypowy sposób, w porównaniu do ostatnią aktywność na tym koncie. Potencjalną przyczyną jest to, że osoba atakująca ma zostały przygotowywanie do wdrożenia złośliwy kod z konta magazynu w usłudze w chmurze platformy Azure.|
|**Uprawnienie dostępu anomalii**|Wskazuje, że uprawnienia dostępu do tego kontenera magazynu zostały zmienione w nietypowy sposób. Potencjalną przyczyną jest, że osoba atakująca zmieniła uprawnień kontenera obniżać jej poziom bezpieczeństwa lub uzyskaj trwałości.|
|**Kontrola dostępu anomalii**|Wskazuje, że uprawnienia dostępu do konta magazynu zostały poddane w nietypowy sposób, w porównaniu do ostatnią aktywność na tym koncie. Potencjalną przyczyną jest osoba atakująca ma wykonać Rekonesans przyszłych ataku.|
|**Anomalii Eksploracja danych**|Wskazuje, że obiekty BLOB i kontenery na koncie usługi storage została wyliczane w nietypowy sposób i w porównaniu z ostatnią aktywność na tym koncie. Potencjalną przyczyną jest osoba atakująca ma wykonać Rekonesans przyszłych ataku.|

>[!NOTE]
>Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage nie jest obecnie dostępna w regionach należących do suwerennej chmury i platformy Azure dla instytucji rządowych.

Aby uzyskać więcej informacji na temat alertów dotyczących magazynu, zobacz [zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) artykuł i zapoznaj się z sekcją alertów ochrony.
