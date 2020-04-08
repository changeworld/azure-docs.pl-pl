---
title: Przenoszenie danych między skalowanymi bazami danych w chmurze
description: Wyjaśniono, jak manipulować fragmentami i przenosić dane za pośrednictwem usługi hostowanego samodzielnie przy użyciu interfejsów API elastycznej bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c7eb1670ee911895bdba23921845b8795f4998af
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811301"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Przenoszenie danych między skalowanymi bazami danych w chmurze

Jeśli jesteś programistą oprogramowania jako usługa i nagle aplikacja ulega ogromnemu zapotrzebowaniu, musisz dostosować się do wzrostu. Więc dodać więcej baz danych (fragmenty). Jak redystrybuować dane do nowych baz danych bez zakłócania integralności danych? Użyj **narzędzia scalania podziału,** aby przenieść dane z ograniczonych baz danych do nowych baz danych.  

Narzędzie do scalania podziału działa jako usługa sieci web platformy Azure. Administrator lub deweloper używa narzędzia do przenoszenia shardlets (dane z niezależnego fragmentu) między różnymi bazami danych (fragmenty). Narzędzie używa zarządzania mapami niezależnego fragmentu do obsługi bazy danych metadanych usługi i zapewnienia spójnych mapowań.

![Omówienie][1]

## <a name="download"></a>Pobierz

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentacja

1. [Samouczek narzędzia Dzielenie i scalanie elastycznej bazy danych](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfiguracja zabezpieczeń scalania podzielonego](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Zagadnienia dotyczące zabezpieczeń scalania podziału](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Zarządzanie mapami fragmentów](sql-database-elastic-scale-shard-map-management.md)
5. [Migrowanie istniejących baz danych w celu skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Narzędzia elastycznej bazy danych](sql-database-elastic-scale-introduction.md)
7. [Słowniczek narzędzi elastycznej bazy danych](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Dlaczego warto skorzystać z narzędzia scalania dzielenia

- **Elastyczność**

  Aplikacje muszą rozciągać się elastycznie poza granice pojedynczej bazy danych usługi Azure SQL DB. Użyj narzędzia, aby przenieść dane w razie potrzeby do nowych baz danych przy zachowaniu integralności.

- **Split rośnie**

  Aby zwiększyć ogólną pojemność do obsługi wzrostu wybuchowego, należy utworzyć dodatkową pojemność przez dzielenie na fragmenty danych i rozdzielanie ich w przyrostowo większej liczbie baz danych, dopóki nie zostaną spełnione potrzeby związane z pojemnością. Jest to doskonały przykład operacji **podziału.**

- **Scalanie w celu zmniejszenia**

  Zapotrzebowanie na moce produkcyjne zmniejsza się ze względu na sezonowy charakter działalności gospodarczej. Narzędzie umożliwia skalowanie w dół do mniejszej liczby jednostek skalowania, gdy działalność spowalnia. Funkcja "scalanie" w usłudze scalania dzielonego skali elastycznej obejmuje to wymaganie.

- **Zarządzanie punktami aktywnymi przez przenoszenie shardletów**

  Z wielu dzierżawców na bazę danych alokacji shardlets do fragmentów może prowadzić do wąskich gardeł pojemności na niektórych fragmentów. Wymaga to ponownego przydzielania shardlets lub przenoszenie zajęty shardlets do nowych lub mniej używanych fragmentów.

## <a name="concepts--key-features"></a>Pojęcia & kluczowych funkcji

- **Usługi hostowane przez klienta**

  Scalanie podziału jest dostarczane jako usługa hostowana przez klienta. Należy wdrożyć i hostować usługę w ramach subskrypcji platformy Microsoft Azure. Pakiet pobrany z NuGet zawiera szablon konfiguracji, aby uzupełnić informacje dotyczące określonego wdrożenia. Zobacz [samouczek scalania podziału, aby](sql-database-elastic-scale-configure-deploy-split-and-merge.md) uzyskać szczegółowe informacje. Ponieważ usługa działa w ramach subskrypcji platformy Azure, można kontrolować i konfigurować większość aspektów zabezpieczeń usługi. Szablon domyślny zawiera opcje konfigurowania protokołu TLS, uwierzytelnianie klienta oparte na certyfikatach, szyfrowanie przechowywanych poświadczeń, ochronę DoS i ograniczenia ip. Więcej informacji na temat aspektów zabezpieczeń można znaleźć w następującej [konfiguracji zabezpieczeń scalania podzielonego](sql-database-elastic-scale-split-merge-security-configuration.md)dokumentu .

  Domyślna wdrożona usługa jest uruchamiana z jednym pracownikiem i jedną rolą sieci web. Każdy używa rozmiaru maszyny Wirtualnej A1 w usługach w chmurze azure. Chociaż nie można zmodyfikować tych ustawień podczas wdrażania pakietu, można je zmienić po pomyślnym wdrożeniu w uruchomionej usłudze w chmurze (za pośrednictwem witryny Azure portal). Należy zauważyć, że rola procesu roboczego nie może być skonfigurowana dla więcej niż jednego wystąpienia z przyczyn technicznych.

- **Integracja mapy niezależnego fragmentu**

  Usługa scalania podziału współdziała z mapą niezależnego fragmentu aplikacji. Podczas korzystania z usługi scalania podziału do dzielenia lub scalania zakresów lub przenoszenia shardlets między fragmentami, usługa automatycznie przechowuje mapy niezależnego fragmentu aktualne. W tym celu usługa łączy się z bazą danych menedżera map niezależnego fragmentu aplikacji i przechowuje zakresy i mapowania jako postęp żądań podziału/scalania/przenoszenia. Gwarantuje to, że mapa niezależnego fragmentu zawsze przedstawia aktualny widok, gdy operacje scalania podziału są w toku. Operacje dzielenia, scalania i przenoszenia shardlet są implementowane przez przeniesienie partii shardlets z niezależnego fragmentu do niezależnego fragmentu docelowego. Podczas operacji przenoszenia shardlet shardlet shardlets przedmiotem bieżącej partii są oznaczone jako w trybie offline na mapie niezależnego fragmentu i są niedostępne dla połączeń routingu zależnych od danych przy użyciu interfejsu API **OpenConnectionForKey.**

- **Spójne połączenia shardlet**

  Po rozpoczęciu przenoszenia danych dla nowej partii shardlets, wszelkie mapy niezależnego od fragmentu pod warunkiem, że zależne od danych połączeń routingu do niezależnego fragmentu przechowywania shardlet są zabijane i kolejne połączenia z interfejsów API mapy niezależnego fragmentu do shardlets są blokowane, gdy ruch danych jest w toku w celu uniknięcia niespójności. Połączenia z innymi shardlets na tym samym fragment również zostanie zabity, ale zakończy się ponownie natychmiast po ponawianie próby. Po przeniesieniu partii shardlets są oznaczone w trybie online ponownie dla niezależnego fragmentu docelowego i dane źródłowe są usuwane z niezależnego fragmentu. Usługa przechodzi przez te kroki dla każdej partii, dopóki wszystkie shardlets zostały przeniesione. Doprowadzi to do kilku operacji zabicia połączenia w trakcie pełnej operacji podziału/scalania/przenoszenia.  

- **Zarządzanie dostępnością shardletu**

  Ograniczenie zabijania połączenia do bieżącej partii shardlets, jak wspomniano powyżej ogranicza zakres niedostępności do jednej partii shardlets w czasie. Jest to preferowane w stosunku do podejścia, w którym pełny niezależnego fragmentu pozostanie w trybie offline dla wszystkich jego shardlets w trakcie operacji podziału lub scalania. Rozmiar partii, zdefiniowane jako liczba różnych shardlets do przenoszenia w czasie, jest parametr konfiguracji. Można go zdefiniować dla każdej operacji podziału i scalania w zależności od potrzeb aplikacji w zakresie dostępności i wydajności. Należy zauważyć, że zakres, który jest zablokowany na mapie niezależnego fragmentu może być większy niż określony rozmiar partii. Dzieje się tak, ponieważ usługa wybiera rozmiar zakresu w taki sposób, że rzeczywista liczba wartości klucza dzielenia na fragmenty w danych w przybliżeniu odpowiada rozmiarowi partii. Jest to ważne, aby pamiętać w szczególności dla słabo zaludnionych klawiszy dzielenia na fragmenty.

- **Przechowywanie metadanych**

  Usługa scalania podziału używa bazy danych do utrzymania jej stanu i przechowywania dzienników podczas przetwarzania żądania. Użytkownik tworzy tę bazę danych w swojej subskrypcji i udostępnia dla niej parametry połączenia w pliku konfiguracyjnym dla wdrożenia usługi. Administratorzy z organizacji użytkownika mogą również łączyć się z tą bazą danych, aby przejrzeć postęp żądania i zbadać szczegółowe informacje dotyczące potencjalnych awarii.

- **Świadomość dzielenia na fragmenty**

  Usługa scalania podziału rozróżnia (1) tabele podzielone, (2) tabele odwołań i (3) normalne tabele. Semantyka operacji podziału/scalania/przenoszenia zależy od typu używanej tabeli i jest zdefiniowana w następujący sposób:

  - **Tabele z dzieleniem na fragmenty**

    Operacje dzielenia, scalania i przenoszenia przenoszą shardlets ze źródła do obiektu docelowego. Po pomyślnym zakończeniu ogólnego żądania te shardlets nie są już obecne w źródle. Należy zauważyć, że tabele docelowe muszą istnieć na niezależnego fragmentu docelowego i nie może zawierać danych w zakresie docelowym przed przetworzeniem operacji.

  - **Tabele odwołań**

    W przypadku tabel odwołań operacje dzielenia, scalania i przenoszenia kopiują dane ze źródła do niezależnego fragmentu docelowego. Należy jednak zauważyć, że żadne zmiany występują na niezależnego fragmentu docelowego dla danej tabeli, jeśli dowolny wiersz jest już obecny w tej tabeli na docelowej. Tabela musi być pusta dla każdej operacji kopiowania tabeli odwołań, aby uzyskać przetworzone.

  - **Inne tabele**

    Inne tabele mogą być obecne w źródle lub miejscu docelowym operacji podziału i scalania. Usługa scalania podziału nie uwzględnia tych tabel dla wszelkich operacji przenoszenia lub kopiowania danych. Należy jednak pamiętać, że mogą one kolidować z tych operacji w przypadku ograniczeń.

    Informacje o tabelach referencyjnych i podzielonych `SchemaInfo` na fragmenty są dostarczane przez interfejsy API na mapie niezależnego fragmentu. Poniższy przykład ilustruje użycie tych interfejsów API w danym obiekcie menedżera mapy niezależnego fragmentu:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Tabele "region" i "nation" są definiowane jako tabele odwołań i będą kopiowane za pomocą operacji podziału/scalania/przenoszenia. "klient" i "zamówienia" są z kolei definiowane jako tabele podzielone. `C_CUSTKEY`i `O_CUSTKEY` służyć jako klucz odłamków.

- **Integralność referencyjna**

  Usługa scalania podziału analizuje zależności między tabelami i używa relacji klucza obcego klucza podstawowego do etapu operacji przenoszenia tabel odwołań i shardlets. Ogólnie rzecz biorąc tabele odwołań są kopiowane najpierw w kolejności zależności, a następnie shardlets są kopiowane w kolejności ich zależności w każdej partii. Jest to konieczne, aby ograniczenia FK-PK na shard docelowy są honorowane w miarę nadejścia nowych danych.

- **Spójność mapy niezależnego fragmentu i ostateczne zakończenie**

  W przypadku awarii usługa scalania podziału wznawia operacje po każdej awarii i ma na celu ukończenie wszelkich żądań w toku. Jednak mogą wystąpić sytuacje nieodwracalne, na przykład, gdy obiekt docelowy zostanie utracony lub naruszony nie do naprawienia. W tych okolicznościach niektóre shardlets, które miały zostać przeniesione może nadal znajdować się na niezależnego fragmentu źródła. Usługa zapewnia, że mapowania shardlet są aktualizowane tylko po pomyślnym skopiowaniu niezbędnych danych do obiektu docelowego. Shardlets są usuwane w źródle tylko wtedy, gdy wszystkie ich dane zostały skopiowane do obiektu docelowego i odpowiednie mapowania zostały pomyślnie zaktualizowane. Operacja usuwania odbywa się w tle, gdy zakres jest już w trybie online na niezależnego fragmentu docelowego. Usługa scalania podziału zawsze zapewnia poprawność mapowań przechowywanych na mapie niezależnego fragmentu.

## <a name="the-split-merge-user-interface"></a>Interfejs użytkownika scalania dzielonego

Pakiet usługi scalania podziału zawiera rolę procesu roboczego i rolę sieci web. Rola sieci web służy do przesyłania żądań scalania podziału w sposób interaktywny. Główne składniki interfejsu użytkownika są następujące:

- **Typ operacji**

  Typ operacji jest przyciskiem radiowym, który steruje rodzajem operacji wykonywanej przez usługę dla tego żądania. Można wybrać między scenariuszami podziału, scalania i przenoszenia. Można również anulować wcześniej przesłaną operację. Można użyć dzielenia, scalania i przenoszenia żądań dla map niezależnego fragmentu zakresu. Mapy niezależnego fragmentu listy obsługują tylko operacje przenoszenia.

- **Mapa odłamków**

  Następna sekcja parametrów żądania zawiera informacje o mapie niezależnego fragmentu i bazie danych obsługującej mapę niezależnego fragmentu. W szczególności należy podać nazwę serwera bazy danych Azure SQL Database i bazy danych obsługujących mapy niezależnego fragmentu, poświadczenia, aby połączyć się z bazą danych mapy niezależnego fragmentu i na koniec nazwę mapy niezależnego fragmentu. Obecnie operacja akceptuje tylko jeden zestaw poświadczeń. Te poświadczenia muszą mieć wystarczające uprawnienia do wykonywania zmian na mapie niezależnego fragmentu, a także do danych użytkownika na fragmentach.

- **Zakres źródłowy (dzielenie i scalanie)**

  Operacja podziału i scalania przetwarza zakres przy użyciu niskiego i wysokiego klucza. Aby określić operację o niezwiązanej wartości wysokiego klucza, zaznacz pole wyboru "Wysoki klucz jest maks." i pozostaw pole wysokiego klucza puste. Określone wartości klucza zakresu nie muszą dokładnie odpowiadać mapowaniu i jego obwiedniom na mapie niezależnego fragmentu. Jeśli nie określisz żadnych granic zakresu w ogóle usługa będzie wywnioskować najbliższy zakres dla Ciebie automatycznie. Skrypt Programu GetMappings.ps1 PowerShell służy do pobierania bieżących mapowań na danej mapie niezależnego fragmentu.

- **Zachowanie źródła podziału (podzielone)**

  W przypadku operacji podziału należy zdefiniować punkt podziału zakresu źródłowego. Można to zrobić, podając klucz dzielenia na fragmenty, gdzie chcesz podziału występuje. Użyj przycisku opcji określ, czy dolna część zakresu (z wyłączeniem klawisza podziału) ma się poruszać, czy też ma się poruszać górna część (łącznie z kluczem podziału).

- **Shardlet źródła (przenieść)**

  Operacje przenoszenia różnią się od operacji dzielenia lub scalania, ponieważ nie wymagają zakresu do opisania źródła. Źródło przenoszenia jest po prostu identyfikowane przez wartość klucza dzielenia na fragmenty, którą zamierzasz przenieść.

- **Odłamek celu (podział)**

  Po podaniu informacji o źródle operacji podziału, należy zdefiniować, gdzie chcesz dane mają być kopiowane do podając serwer usługi Azure SQL Db i nazwę bazy danych dla obiektu docelowego.

- **Zakres docelowy (scalanie)**

  Operacje scalania przenieść shardlets do istniejącego fragmentu. Zidentyfikować istniejący fragment, podając granice zakresu istniejącego zakresu, z którymi chcesz scalić.

- **Rozmiar partii**

  Rozmiar partii kontroluje liczbę shardlets, które będą przechodzić do trybu offline w czasie podczas przenoszenia danych. Jest to wartość całkowita, w której można użyć mniejszych wartości, gdy są wrażliwe na długie okresy przestojów dla shardlets. Większe wartości zwiększy czas, że dany shardlet jest w trybie offline, ale może poprawić wydajność.

- **Identyfikator operacji (anuluj)**

  Jeśli masz bieżącą operację, która nie jest już potrzebna, możesz anulować operację, podając jej identyfikator operacji w tym polu. Identyfikator operacji można pobrać z tabeli stanu żądania (patrz sekcja 8.1) lub z danych wyjściowych w przeglądarce internetowej, w której przesłano żądanie.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Bieżąca implementacja usługi scalania podziału podlega następującym wymaganiom i ograniczeniom:

- Fragmenty muszą istnieć i być zarejestrowane na mapie niezależnego fragmentu przed operacją scalania podziału na tych fragmentach mogą być wykonywane.
- Usługa nie tworzy tabel lub innych obiektów bazy danych automatycznie w ramach swoich operacji. Oznacza to, że schemat dla wszystkich tabel podzielonych na fragmenty i tabele odwołań musi istnieć na niezależnego fragmentu docelowego przed każdą operacją podziału/scalania/przenoszenia. Tabele podzielone na fragmenty w szczególności muszą być puste w zakresie, w którym nowe shardlets mają być dodawane przez operację podziału/scalania/przenoszenia. W przeciwnym razie operacja zakończy się niepowodzeniem początkowego sprawdzania spójności na docelowych fragmentów. Należy również zauważyć, że dane referencyjne są kopiowane tylko wtedy, gdy tabela odwołań jest pusta i że nie ma żadnych gwarancji spójności w odniesieniu do innych równoczesnych operacji zapisu w tabelach odwołań. Zaleca się to: podczas uruchamiania operacji podziału/scalania żadne inne operacje zapisu nie wprowadzać zmian w tabelach odwołań.
- Usługa opiera się na tożsamości wiersza ustanowione przez unikatowy indeks lub klucz, który zawiera klucz dzielenia na fragmenty, aby zwiększyć wydajność i niezawodność dla dużych shardlets. Dzięki temu usługa do przenoszenia danych z jeszcze większą szczegółowością niż tylko wartość klucza dzielenia na fragmenty. Pomaga to zmniejszyć maksymalną ilość miejsca w dzienniku i blokad, które są wymagane podczas operacji. Należy rozważyć utworzenie unikatowego indeksu lub klucza podstawowego, w tym klucza dzielenia na fragmenty w danej tabeli, jeśli chcesz użyć tej tabeli z żądaniami podziału/scalania/przenoszenia. Ze względu na wydajność klucz dzielenia na fragmenty powinien być wiodącą kolumną w kluczu lub indeksie.
- W trakcie przetwarzania żądania niektóre dane shardlet może być obecny zarówno na źródłowym i obiektem docelowym. Jest to konieczne do ochrony przed awariami podczas ruchu shardlet. Integracja scalania podziału z mapą niezależnego fragmentu zapewnia, że połączenia za pośrednictwem interfejsów API routingu zależne od danych przy użyciu **Metody OpenConnectionForKey** na mapie niezależnego fragmentu nie widzą żadnych niespójnych stanów pośrednich. Jednak podczas łączenia się ze źródłem lub odłamkami docelowych bez użycia **metody OpenConnectionForKey** niespójne stany pośrednie mogą być widoczne, gdy żądania podziału/scalania/przenoszenia są w toku. Te połączenia mogą wykazywać częściowe lub zduplikowane wyniki w zależności od czasu lub fragmentu leżącego u podstaw połączenia. To ograniczenie obejmuje obecnie połączenia wykonane przez elastyczne skalowanie multi-Shard-Queries.
- Baza danych metadanych dla usługi scalania podziału nie mogą być współużytkowane przez różne role. Na przykład rola usługi scalania podziału uruchomionej w przemieszczania musi wskazywać inną bazę danych metadanych niż rola produkcyjna.

## <a name="billing"></a>Rozliczenia

Usługa scalania podzielonego jest uruchamiana jako usługa w chmurze w ramach subskrypcji platformy Microsoft Azure. W związku z tym opłaty za usługi w chmurze mają zastosowanie do wystąpienia usługi. O ile często nie wykonujesz operacji podziału/scalania/przenoszenia, zalecamy usunięcie usługi w chmurze scalania podziału. Pozwala to zaoszczędzić koszty uruchamiania lub wdrażania wystąpień usługi w chmurze. Można ponownie wdrożyć i uruchomić konfigurację łatwo uruchamialne, gdy trzeba wykonać operacje podziału lub scalania.

## <a name="monitoring"></a>Monitorowanie

### <a name="status-tables"></a>Tabele stanu

Usługa scalania podziału udostępnia **requeststatus** tabeli w bazie danych magazynu metadanych do monitorowania zakończonych i bieżących żądań. Tabela zawiera wiersz dla każdego żądania scalania podziału, które zostało przesłane do tego wystąpienia usługi scalania podziału. Zawiera następujące informacje dla każdego żądania:

- **Sygnatura czasowa**

  Godzina i data rozpoczęcia żądania.

- **OperationId (Ida)**

  Identyfikator GUID, który jednoznacznie identyfikuje żądanie. To żądanie może również służyć do anulowania operacji, gdy jest ona nadal w toku.

- **Stan**

  Bieżący stan żądania. W przypadku bieżących żądań wymieniono również bieżący etap, w którym znajduje się żądanie.

- **Anuluj odwołanie**

  Flaga wskazująca, czy żądanie zostało anulowane.

- **Postępu**

  Procentowy szacunek ukończenia operacji. Wartość 50 wskazuje, że operacja jest około 50% zakończona.

- **Szczegóły**

  Wartość XML, która zawiera bardziej szczegółowy raport postępu. Raport postępu jest okresowo aktualizowany, gdy zestawy wierszy są kopiowane ze źródła do obiektu docelowego. W przypadku awarii lub wyjątków ta kolumna zawiera również bardziej szczegółowe informacje o awarii.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Usługa scalania podzielonego używa usługi Azure Diagnostics opartej na platformie Azure SDK 2.5 do monitorowania i diagnostyki. Możesz kontrolować konfigurację diagnostyki, jak wyjaśniono poniżej: [Włączanie diagnostyki w usługach w chmurze i maszynach wirtualnych platformy Azure.](../cloud-services/cloud-services-dotnet-diagnostics.md) Pakiet pobierania zawiera dwie konfiguracje diagnostyki - jedną dla roli sieci web i jedną dla roli procesu roboczego. Zawiera definicje do rejestrowania liczników wydajności, dzienniki usług IIS, dzienniki zdarzeń systemu Windows i dzienniki zdarzeń aplikacji korespondencji seryjnej.

## <a name="deploy-diagnostics"></a>Wdrażanie diagnostyki

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby włączyć monitorowanie i diagnostykę przy użyciu konfiguracji diagnostycznej dla ról sieci web i procesu roboczego dostarczonych przez pakiet NuGet, uruchom następujące polecenia przy użyciu programu Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Więcej informacji na temat konfigurowania i wdrażania ustawień diagnostycznych można znaleźć tutaj: [Włączanie diagnostyki w usługach w chmurze Azure i maszynach wirtualnych.](../cloud-services/cloud-services-dotnet-diagnostics.md)

## <a name="retrieve-diagnostics"></a>Pobieranie diagnostyki

Dostęp do diagnostyki można łatwo uzyskać za pomocą Eksploratora serwera programu Visual Studio w części platformy Azure drzewa Eksploratora serwera. Otwórz wystąpienie programu Visual Studio, a na pasku menu kliknij pozycję Widok i Eksplorator serwera. Kliknij ikonę platformy Azure, aby połączyć się z subskrypcją platformy Azure. Następnie przejdź do platformy Azure -> Storage `<your storage account>` -> -> Tables -> WADLogsTable. Aby uzyskać więcej informacji, zobacz [Eksplorator serwerów](https://msdn.microsoft.com/library/x603htbk.aspx).

![Tabela WADLogsTable][2]

WADLogsTable wyróżniony na rysunku powyżej zawiera szczegółowe zdarzenia z dziennika aplikacji usługi scalania podziału. Należy zauważyć, że domyślna konfiguracja pobranego pakietu jest dostosowana do wdrożenia produkcyjnego. W związku z tym interwał, w którym dzienniki i liczniki są pobierane z wystąpień usługi jest duży (5 minut). W przypadku testowania i programowania należy zmniejszyć interwał, dostosowując ustawienia diagnostyki sieci Web lub roli procesu roboczego do potrzeb. Kliknij prawym przyciskiem myszy rolę w Eksploratorze serwera programu Visual Studio (patrz wyżej), a następnie dostosuj okres transferu w oknie dialogowym ustawień konfiguracji diagnostyki:

![Konfigurowanie][3]

## <a name="performance"></a>Wydajność

Ogólnie rzecz biorąc należy oczekiwać lepszej wydajności od wyższych, bardziej wydajnych warstw usług w usłudze Azure SQL Database. Wyższe alokacje we/wy, procesora CPU i pamięci dla wyższych warstw usług korzystają z operacji kopiowania zbiorczego i usuwania używanych przez usługę scalania podziału. Z tego powodu zwiększ warstwę usług tylko dla tych baz danych przez określony, ograniczony okres czasu.

Usługa wykonuje również kwerendy sprawdzania poprawności w ramach swoich normalnych operacji. Te kwerendy sprawdzania poprawności sprawdzić nieoczekiwane obecności danych w zakresie docelowym i upewnij się, że wszelkie split/merge/move operacji rozpoczyna się od stanu spójnego. Wszystkie te kwerendy działają za pomocą zakresów kluczy dzielenia na fragmenty zdefiniowanych przez zakres operacji i rozmiar partii podany jako część definicji żądania. Te kwerendy działają najlepiej, gdy indeks jest obecny, który ma klucz dzielenia na fragmenty jako wiodącą kolumnę.

Ponadto właściwość unikatowości z kluczem dzielenia na fragmenty jako kolumna wiodąca umożliwi usłudze użycie zoptymalizowanego podejścia, które ogranicza zużycie zasobów pod względem miejsca w dzienniku i pamięci. Ta właściwość unikatowość jest wymagana do przenoszenia dużych rozmiarów danych (zazwyczaj powyżej 1 GB).

## <a name="how-to-upgrade"></a>Jak uaktualnić

1. Wykonaj kroki opisane w [części Wdrażanie usługi scalania podzielonego](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Zmień plik konfiguracji usługi w chmurze dla wdrożenia scalania podzielonego, aby odzwierciedlić nowe parametry konfiguracji. Nowy wymagany parametr to informacje o certyfikacie używanym do szyfrowania. Łatwym sposobem jest porównanie nowego pliku szablonu konfiguracji z pobierania z istniejącą konfiguracją. Upewnij się, że dodano ustawienia dla "DataEncryptionPrimaryCertificateThumbprint" i "DataEncryptionPrimary" dla sieci Web i roli procesu roboczego.
3. Przed wdrożeniem aktualizacji na platformie Azure upewnij się, że wszystkie aktualnie uruchomione operacje scalania podziału zostały zakończone. Można to łatwo zrobić, odpytując requeststatus i pendingworkflows tabel w bazie danych metadanych korespondencji seryjnej dla bieżących żądań.
4. Zaktualizuj istniejące wdrożenie usługi w chmurze w celu scalenia podzielonego w ramach subskrypcji platformy Azure za pomocą nowego pakietu i zaktualizowanego pliku konfiguracji usługi.

Nie trzeba aprowizować nową bazę danych metadanych dla scalania podziału do uaktualnienia. Nowa wersja automatycznie uaktualni istniejącą bazę danych metadanych do nowej wersji.

## <a name="best-practices--troubleshooting"></a>Najlepsze rozwiązania i rozwiązywanie problemów

- Zdefiniuj dzierżawę testową i wykaż najważniejsze operacje podziału/scalania/przenoszenia z dzierżawą testową w kilku fragmentach. Upewnij się, że wszystkie metadane są poprawnie zdefiniowane na mapie niezależnego fragmentu i że operacje nie naruszają ograniczeń ani kluczy obcych.
- Zachowaj rozmiar danych dzierżawy testowej powyżej maksymalnego rozmiaru danych największej dzierżawy, aby upewnić się, że nie występują problemy związane z rozmiarem danych. Pomaga to ocenić górną granicę w czasie potrzebnym do przeniesienia pojedynczej dzierżawy wokół.
- Upewnij się, że schemat umożliwia usuwanie. Usługa scalania podziału wymaga możliwość usunięcia danych z niezależnego fragmentu źródłowego po pomyślnym skopiowaniu danych do obiektu docelowego. Na przykład **wyzwalacze usuwania** może uniemożliwić usługi usunięcie danych w źródle i może spowodować niepowodzenie operacji.
- Klucz dzielenia na fragmenty powinien być wiodącą kolumną w kluczu podstawowym lub unikatową definicję indeksu. Zapewnia to najlepszą wydajność dla zapytań sprawdzania poprawności podziału lub korespondencji seryjnej oraz dla rzeczywistych operacji przenoszenia i usuwania danych, które zawsze działają na zakresach kluczy dzielenia na fragmenty.
- Sortowanie usługi scalania podzielonego w regionie i centrum danych, w którym znajdują się bazy danych.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
