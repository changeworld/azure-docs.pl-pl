---
title: Przenoszenie danych między bazami danych w chmurze skalowanych w poziomie | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak do manipulowania fragmentów i przenoszenie danych za pomocą samodzielnie hostowanej usługi przy użyciu elastycznych baz danych interfejsów API.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2127c05d7e52b0103d91ecfac4fb5977a4815f31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66123400"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Przenoszenie danych między skalowanymi bazami danych w chmurze

To oprogramowanie, usługi dla deweloperów i nagle ulega ogromne popytu w swojej aplikacji, musisz uwzględnić rozwój. Dlatego należy dodać większej liczby baz danych (fragmentami). Sposób rozpowszechniania danych do nowych baz danych bez zakłócania integralność danych Użyj **narzędzie do dzielenia i scalania** do przenoszenia danych z baz danych, ograniczone do nowych baz danych.  

Narzędzie do dzielenia i scalania działa jako usługa sieci web platformy Azure. Administratorem lub deweloperem używa narzędzia do przenoszenia podfragmentów (dane z fragmentu) między różnych baz danych (fragmentami). Narzędzie wykorzystuje procesu zarządzania mapą fragmentów obsługa bazy danych metadanych usługi w celu zapewnienia spójnego mapowania.

![Omówienie][1]

## <a name="download"></a>Do pobrania

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentacja

1. [Samouczek dotyczący narzędzi elastycznej bazy danych dzielenia i scalania](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfiguracja zabezpieczenia dzielenia i scalania](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Zagadnienia dotyczące zabezpieczeń dzielenia i scalania](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Zarządzanie mapami fragmentów](sql-database-elastic-scale-shard-map-management.md)
5. [Migrowanie istniejących baz danych do skalowania w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Narzędzia elastycznych baz danych](sql-database-elastic-scale-introduction.md)
7. [Słownik narzędzi usługi elastycznych baz danych](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Dlaczego warto używać narzędzia do dzielenia i scalania

- **Elastyczność**

  Aplikacje muszą przeprowadzić rozciąganie elastycznie poza granicami pojedynczej bazy danych Azure SQL DB. Użyj narzędzia do przenoszenia danych, zgodnie z potrzebami do nowych baz danych przy zachowaniu integralności.

- **Podzielona na rozwój**

  Aby zwiększyć ogólną pojemności umożliwiającej obsługę szybkiego wzrostu liczby użytkowników, utworzyć dodatkową pojemność przy fragmentowanie danych i, ich dystrybucję w przyrostowo większej liczby baz danych, dopóki nie wymaga pojemności są spełnione. Jest to podstawowy przykład **podziału** funkcji.

- **Scal do zmniejszania**

  Pojemność musi zmniejszyć ze względu na charakter sezonowej działalności biznesowej. Narzędzie pozwala skalować w dół do mniejszej liczby jednostek skalowania podczas spowalnia biznesowych. Funkcja "merge", elastyczne skalowanie usługi dzielenia i scalania obejmuje to wymaganie.

- **Zarządzanie hotspotami, przenosząc podfragmentów**

  Z wieloma dzierżawami na bazę danych Alokacja podfragmentów do fragmentów może spowodować wąskie gardła wydajności na niektórych fragmentów. Wymagane jest ponowne przydzielanie podfragmentów, lub przenoszenia zajęty podfragmentów do nowych lub mniej wykorzystywanych fragmentów.

## <a name="concepts--key-features"></a>Pojęcia i kluczowe funkcje

- **Usługi hostowane przez klienta**

  Dzielenia i scalania są dostarczane jako usługa hostowana przez klienta. Należy wdrożyć i Hostuj usługi w Twojej subskrypcji Microsoft Azure. Pakiet, który możesz pobrać z pakietów NuGet zawiera szablon konfiguracji, aby ukończyć informacjami dla określonego wdrożenia. Zobacz [samouczek dzielenia i scalania](sql-database-elastic-scale-configure-deploy-split-and-merge.md) Aby uzyskać szczegółowe informacje. Ponieważ ta usługa działa w ramach subskrypcji platformy Azure, można kontrolować i skonfigurować większość ustawień zabezpieczeń usługi. Szablon domyślny zawiera opcje służące do konfigurowania protokołu SSL, uwierzytelnianie klienta oparte na certyfikatach, szyfrowanie przechowywanych poświadczeń, ochrona systemu DoS i ograniczenia adresów IP. Więcej informacji na temat aspekty zabezpieczeń można znaleźć w następującym dokumencie [konfiguracji zabezpieczenia dzielenia i scalania](sql-database-elastic-scale-split-merge-security-configuration.md).

  Wartość domyślna wdrożyć uruchomienia usługi za pomocą jednego procesu roboczego i jednej roli sieci web. Rozmiar maszyn wirtualnych A1 każdej używane w usługach Azure Cloud Services. Chociaż nie można zmodyfikować te ustawienia podczas wdrażania pakietu, można je zmienić, po pomyślnym wdrożeniu w usłudze działającej w chmurze, (za pośrednictwem witryny Azure portal). Należy pamiętać, że rola procesu roboczego nie musi być skonfigurowany dla więcej niż jedno wystąpienie przyczyn technicznych.

- **Integracja mapy fragmentów**

  Usługi dzielenia i scalania wchodzi w interakcję z mapą fragmentów w aplikacji. Korzystając z usługi dzielenia i scalania do dzielenia lub łączenia zakresów lub podfragmentów między fragmentami, usługa automatycznie aktualizuje mapowania fragmentów aktualne. Aby to zrobić, usługa nawiązanie połączenia z bazy danych Menedżera mapowań fragmentów aplikacji i przechowuje zakresów i mapowania postęp żądań split/scalania/przenoszenia. Daje to gwarancję, że mapowania fragmentów zawsze przedstawia aktualny widok, podczas operacji dzielenia i scalania są przesyłane. Podziel, scalania i podfragmentów operacje przenoszenia są implementowane, przenosząc partii podfragmentów z fragmentu źródła do docelowego fragmentu. Podczas operacji przenoszenia podfragmentu podfragmentów z zastrzeżeniem bieżącej partii są oznaczone w trybie offline, w ramach mapowania fragmentów i nie są dostępne dla połączeń routingu zależnego od danych przy użyciu **OpenConnectionForKey** interfejsu API.

- **Spójne podfragmentu połączeń**

  Po uruchomieniu przepływu danych dla nowej partii podfragmentów wszystkie mapowania fragmentów, pod warunkiem połączenia routingu zależnego od danych do przechowywania fragmentów są zabite podfragmentu i kolejnych połączeń z mapy fragmentów, które interfejsy API w celu podfragmentach są blokowane podczas przenoszenia danych w toku, aby uniknąć niespójności. Połączenia podfragmenty na tym samym fragmencie również pobrać zabite, ale zostanie wykonana pomyślnie ponownie natychmiast po ponowieniu. Po przeniesieniu partii podfragmentach są oznaczone w trybie online ponownie fragmentu docelowego i źródła danych zostanie usunięty z fragmentu źródła. Usługa przechodzi przez te kroki dla każdej partii, dopóki wszystkie podfragmentów zostały przeniesione. Spowoduje to kilka operacji kill połączenie w trakcie ukończenia operacji podziału/scalania/przenoszenia.  

- **Zarządzanie dostępnością podfragmentu**

  Ograniczanie połączenia zabijanie do bieżącej partii podfragmentów zgodnie z powyższym opisem ogranicza zakres niedostępność jednej partii podfragmentów w danym momencie. To jest preferowana nad podejście gdzie pełną fragment może pozostawać w trybie offline dla wszystkich jego podfragmentów w trakcie operacji dzielenia ani scalania. Rozmiar partii, zdefiniowane jako liczba różnych podfragmentów można przenieść w czasie, jest parametru konfiguracji. Mogą być definiowane dla każdej operacji dzielenia i scalania, w zależności od potrzeb dostępność i wydajność aplikacji. Należy zauważyć, że zakres, który jest zablokowany w ramach mapowania fragmentów może być większy niż określony rozmiar partii. Jest to spowodowane usługi wybiera rozmiar zakresu w taki sposób, że rzeczywista liczba wartości klucza fragmentowania danych odpowiada około rozmiar partii. Jest to w szczególności pamiętać kluczy fragmentowania słabo wypełnionych.

- **Magazynu metadanych**

  Usługi dzielenia i scalania korzysta z bazy danych, aby zachować stan i na potrzeby przechowywania dzienników podczas przetwarzania żądania. Użytkownik tworzy tę bazę danych w ramach swojej subskrypcji i zawiera parametry połączenia dla niego w pliku konfiguracji dla wdrożenia usługi. Administratorzy z organizacji użytkownika można także nawiązać tej bazy danych, aby sprawdzić postęp żądań i do badania szczegółowe informacje dotyczące potencjalnych awarii.

- **Rozpoznawanie dzielenia na fragmenty**

  Usługi dzielenia i scalania rozróżnia między (1) podzielonej na fragmenty tabel, tabele odwołań (2) i (3) normalnych tabel. Semantyka operacji podziału/scalania/Przenieś zależą od typu Tabela używana i są definiowane w następujący sposób:

  - **Tabele podzielonej na fragmenty**

    Dzielenie, scalanie i operacji przenoszenia należy przenieść podfragmentów ze źródła do docelowego fragmentu. Po pomyślnym zakończeniu żądania ogólnego podfragmentów te nie są już dostępne w źródle. Należy pamiętać, że tabel docelowych musi istnieć na docelowym fragmencie i nie mogą zawierać dane w zakresie docelowym, przed wykonania operacji.

  - **Tabele odwołań**

    Odwołanie do tabel, podziału, operacje scalenie i przeniesienie kopiowanie danych ze źródła do docelowego fragmentu. Należy jednak pamiętać, że żadne zmiany wystąpić na fragmentu docelowy dla danej tabeli, jeśli dowolnego wiersza znajduje się już w tej tabeli w elemencie docelowym. Tabela ma być pusta dla operacji kopiowania tabeli wszelkie odwołania do są przetwarzane.

  - **Inne tabele**

    Inne tabele mogą być obecne na źródłowy lub docelowy operacji dzielenia i scalania. Usługi dzielenia i scalania pomija te tabele wszelkie operacje kopiowania lub przenoszenia danych. Należy jednak pamiętać, że może zakłócać tych operacji w przypadku ograniczenia.

    Informacje dla odwołania, a tabele podzielonej na fragmenty są udostępniane przez `SchemaInfo` interfejsów API w ramach mapowania fragmentów. Poniższy przykład ilustruje użycie tych interfejsów API na obiekt menedżera map fragmentów danego:

    ```c#
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // Reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // Sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));
    // Publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Tabele "region" i "kraj" są zdefiniowane jako tabele odwołań i zostaną skopiowane z operacji dzielenia/scalania/przenoszenia. "klient" i "orders" z kolei są definiowane jako tabele podzielonej na fragmenty. `C_CUSTKEY` i `O_CUSTKEY` służyć jako klucz fragmentowania.

- **Integralność referencyjną**

  Usługi dzielenia i scalania analizuje zależności między tabelami i używa relacje klucza obcego klucz podstawowy do przygotowania operacji przenoszenia tabele odwołań i podfragmentów. Ogólnie rzecz biorąc tabele odwołań są najpierw kopiowane w kolejności wg zależności, a następnie podfragmentów są kopiowane w kolejności ich zależności w ramach każdej partii. Jest to konieczne, tak aby ograniczenia klucza Obcego PK fragmentu docelowej są honorowane w miarę nadchodzenia nowych danych.

- **Spójność mapy fragmentów i ostateczną uzupełniania**

  Obecności błędy usługi dzielenia i scalania Wznawia operacje po ewentualnej awarii i ma na celu wykonaj dowolny w żądaniach postępu. Jednak mogą wystąpić nieodwracalny sytuacje, np. przypadku utracenia lub naruszony naprawienia fragmentu docelowego. W tych okolicznościach podfragmenty, które zostały powinien zostać przeniesiona może nadal znajdować się na fragmentu źródła. Usługa gwarantuje, że podfragmentu mapowania są aktualizowane tylko po niezbędnych danych zostały pomyślnie skopiowane do docelowego. Podfragmentów tylko zostaną usunięte w źródle, gdy wszystkie swoje dane zostały skopiowane do obiektu docelowego i odpowiednie mapowania zostały pomyślnie zaktualizowane. Operacji usuwania odbywa się w tle, gdy zakres jest w trybie online na fragmentu docelowego. Usługi dzielenia i scalania zawsze gwarantuje poprawność mapowania przechowywanych w ramach mapowania fragmentów.

## <a name="the-split-merge-user-interface"></a>Interfejs użytkownika dzielenia i scalania

Pakiet usługi dzielenia i scalania obejmuje rolę procesu roboczego oraz roli sieci web. Rola sieci web jest używana do przesyłania żądań dzielenia i scalania w sposób interaktywny. Główne składniki interfejsu użytkownika są następujące:

- **Typ operacji**

  Typ operacji jest przycisku radiowego, który określa rodzaj operacji wykonywanych przez usługę dla tego żądania. Możesz wybrać między podziału, scalania i Przenieś scenariuszy. Można również anulować operację wcześniej przesłane. Możesz użyć podziału, scalania i przenieść żądań dotyczących mapowań fragmentów w postaci zakresu. Lista dzielenie map na tylko obsługi operacji przenoszenia.

- **Mapy fragmentów**

  Następna sekcja Parametry żądania podano informacje dotyczące mapowania fragmentów i bazę danych hostingu mapy fragmentów. W szczególności musisz podać nazwę serwera Azure SQL Database i obsługi shardmap, poświadczenia do połączenia z bazy danych mapowań fragmentów, a na końcu nazwę mapowania fragmentów bazy danych. Obecnie operację akceptuje tylko jeden zestaw poświadczeń. Te poświadczenia muszą mieć wystarczające uprawnienia do wykonywania zmian do mapy fragmentów, jak również do danych użytkownika na fragmenty.

- **Zakres źródłowy (dzielenie i scalanie)**

  Operacja dzielenia i scalania przetwarza zakresu, używając swojego klucza niski i wysoki. Aby określić operację z niepowiązanych wysoka wartość klucza, zaznacz pole wyboru "wysoka wartość klucza jest maksymalna" i pozostaw puste pole klucza wysokiej. Zakres wartości klucza, należy określić, które nie są dokładnie dopasowana do mapowania i jego granic na mapie fragmentu. Jeśli nie określisz granice z zakresem w ogóle usługi zakres najbliższej dla Ciebie automatycznie wywnioskuje. Można użyć skryptu programu GetMappings.ps1 PowerShell można pobrać bieżących mapowań w mapie danego fragmentu.

- **Zachowanie źródła podziału (podział)**

  W przypadku operacji podziału zdefiniować punkt podziału zakresu źródłowego. Można to zrobić, podając klucz fragmentowania, w którym ma być podziału wystąpić. Użyj przycisku radiowego Określ, czy użytkownik zamierza dolnej części zakresu (z wyjątkiem klucza podziału) przenieść lub czy ma być górnej części, aby przenieść (w tym klucza podziału).

- **Źródło Podfragmentu (przenoszenie)**

  Operacji przenoszenia różnią się od operacji dzielenia ani scalania, ponieważ nie wymagają one zakres opisujący źródła. Źródło do przeniesienia jest po prostu identyfikowany przez wartość klucza fragmentowania, która ma zostać przeniesiona.

- **Docelowy fragmentów (podział)**

  Po podaniu informacji w źródle operacji podziału, należy zdefiniować, którego dane mają być kopiowane do, zapewniając serwera bazy danych SQL Azure i nazwę bazy danych dla obiektu docelowego.

- **Zakres docelowy (scalenie)**

  Scal podfragmentów przenoszenia operacji do istniejących fragmentu. Możesz zidentyfikować istniejące fragmentów, zapewniając granice zakresu istniejącego zakresu, który ma zostać scalony z.

- **Batch Size**

  Wielkość partii określa liczbę podfragmentów, który będzie przejdą w tryb offline w momencie podczas przenoszenia danych. Jest wartością całkowitą z zakresu, gdzie można użyć mniejszej wartości, gdy wszystko jest wrażliwa na długie okresy przestojów podfragmentów. Większe wartości spowoduje zwiększenie czasu, która jest dany podfragment w trybie offline, ale może zwiększyć wydajność.

- **Identyfikator operacji (anulowanie)**

  Jeśli wykonywana operacja, która nie jest już potrzebny, możesz anulować operację, podając jego identyfikator operacji, w tym polu. Identyfikator operacji można pobrać z tabeli stan żądania (patrz sekcja 8.1) lub z danych wyjściowych w przeglądarce sieci web, do którego zostały przesłane żądanie.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Bieżąca implementacja usługi dzielenia i scalania podlega następujące wymagania i ograniczenia:

- Fragmenty muszą się znajdować i być zarejestrowane w ramach mapowania fragmentów, przed wykonaniem operacji dzielenia i scalania, na tych fragmentach.
- Usługa nie tworzy tabele lub innych obiektów bazy danych, które automatycznie jako część jej operacji. Oznacza to, że schematu dla wszystkich podzielonych tabel i tabele odwołań musi istnieć na fragmentu docelowej przed każdą operacją split/scalania/przenoszenia. Tabele podzielonej na fragmenty w szczególności muszą być pusty w zakresie, gdzie nowe podfragmenty mają być dodawane przez operację podziału/scalania/przenoszenia. W przeciwnym razie operacja zakończy się niepowodzeniem Sprawdzanie spójności początkowej w docelowym fragmencie. Należy również zauważyć, że odwołanie, które dane zostaną skopiowane tylko, jeśli odwołanie tabela jest pusta, i że nie istnieją żadne gwarancje spójności, w odniesieniu do innych równoczesnych operacje zapisu w tabelach odwołań. Jest to zalecane: podczas uruchamiania operacji dzielenia i scalania, inne operacje zapisu dokonać zmian w tabelach odwołań.
- Usługa zależy od tożsamość wiersza ustanowione przez unikatowego indeksu lub klucza, który zawiera klucz fragmentowania tak, aby zwiększyć wydajność i niezawodność dużych podfragmentów. Dzięki temu usługi do przenoszenia danych dokładniej nawet niż tylko wartości klucza fragmentowania. Pozwala to zmniejszyć maksymalną ilość miejsca w dzienniku i blokad, które są wymagane podczas operacji. Należy rozważyć utworzenie unikatowego indeksu lub klucza podstawowego, w tym klucz fragmentowania na danej tabeli, jeśli chcesz używać tej tabeli z żądaniami split/scalania/przenoszenia. Ze względu na wydajność klucz fragmentowania powinien być wiodących kolumny klucza lub indeksu.
- W trakcie przetwarzania żądania niektórych danych podfragmentu może występować zarówno na źródłowym i docelowym fragmencie. Jest to konieczne zapewnić ochronę przed awariami podczas przemieszczania podfragmentu. Integracja dzielenia i scalania z mapą fragmentów, masz pewność, że połączenia za pośrednictwem zależne od danych routingu interfejsów API przy użyciu **OpenConnectionForKey** metody w ramach mapowania fragmentów nie ma żadnych niespójne pośrednich stanów. Jednak podczas nawiązywania połączenia źródła lub fragmenty docelowej bez użycia **OpenConnectionForKey** metody niespójne pośrednich stanów mogą być widoczne, gdy żądania podziału/scalania/Przenieś przechodzenia. Połączenia te mogą być wyświetlane wyniki częściowe lub zduplikowane w zależności od czas lub fragmentu połączenie znajdujące się poniżej. To ograniczenie obecnie dotyczy to połączeń elastycznego skalowania wielu-Shard — zapytania.
- Baza danych metadanych dla usługi dzielenia i scalania nie może być udostępniany między różne role. Na przykład rola usługi dzielenia i scalania, uruchomiony w środowisku tymczasowym musi wskazywać innych metadanych bazy danych niż roli w środowisku produkcyjnym.

## <a name="billing"></a>Rozliczenia

Usługi dzielenia i scalania działa jako usługa w chmurze w Twojej subskrypcji Microsoft Azure. W związku z tym dla usług w chmurze opłaty do swojego wystąpienia usługi. Chyba że często wykonywania operacji podziału/scalania/przenoszenia, zalecamy usuwanie usługi w chmurze dzielenia i scalania. Które zmniejszyć koszty do uruchomienia lub wdrożonych wystąpień usług w chmurze. Można ponownie wdrożyć i uruchomić konfigurację łatwo możliwy do uruchomienia w każdym przypadku, gdy trzeba wykonać operacji dzielenia ani scalania.

## <a name="monitoring"></a>Monitorowanie

### <a name="status-tables"></a>Stan tabel

Udostępnia usługi dzielenia i scalania **stanem żądania** tabeli w bazie danych magazynu metadanych do monitorowania ukończone i bieżących żądań. W tabeli podano wierszy dla każdego żądania dzielenia i scalania, który został przesłany do tego wystąpienia usługi dzielenia i scalania. Daje ona następujące informacje dla każdego żądania:

- **Sygnatura czasowa**

  Data i godzina rozpoczęcia żądania.

- **OperationId**

  Identyfikator GUID, który unikatowo identyfikuje żądanie. To żądanie może również anulować operację, gdy są one postępujące.

- **Stan**

  Bieżący stan żądania. Trwającą żądań zawiera również listę Bieżąca faza, w którym jest żądanie.

- **CancelRequest**

  Flaga, która wskazuje, czy żądanie zostało anulowane.

- **Postęp**

  Szacowany procent ukończenia operacji. Wartość 50 wskazuje, czy operacja się około 50% ukończone.

- **Szczegóły**

  Wartości XML, który zawiera bardziej szczegółowy raport o postępie. Raport postęp jest okresowo aktualizowany, ponieważ zestawy wierszy są kopiowane ze źródła do docelowego. W razie błędów lub wyjątków ta kolumna zawiera bardziej szczegółowe informacje o błędzie.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Usługi dzielenia i scalania korzysta z usługi Azure Diagnostics opartego na platformie Azure SDK 2.5 dla monitorowania i diagnostyki. Konfiguracja diagnostyki możesz kontrolować, jak wyjaśniono poniżej: [Włączanie diagnostyki w usług Azure Cloud Services i Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). Pakiet pobierania zawiera dwie konfiguracje Diagnostyka — jeden dla roli sieci web i jeden dla roli procesu roboczego. Zawiera definicje służące do logowania, liczniki wydajności, dzienniki usług IIS, Windows, dzienniki zdarzeń i dzienniki zdarzeń aplikacji dzielenia i scalania.

## <a name="deploy-diagnostics"></a>Wdrażanie diagnostyki

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Aby włączyć monitorowania i diagnostyki przy użyciu konfiguracji diagnostycznych dla ról sieć web i proces roboczy, dostarczone przez pakiet NuGet, uruchom następujące polecenia, za pomocą programu Azure PowerShell:

```powershell
    $storage_name = "<YourAzureStorageAccount>"
    $key = "<YourAzureStorageAccountKey"
    $storageContext = New-AzStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb"
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker"
```

Można znaleźć więcej informacji na temat sposobu konfigurowania i wdrażania ustawień diagnostyki, w tym miejscu: [Włączanie diagnostyki w usług Azure Cloud Services i Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Pobieranie diagnostyki

Diagnostycznej można łatwo uzyskać dostęp z poziomu programu Visual Studio Eksploratora serwera w usłudze Azure części drzewa Eksploratora serwera. Otwórz wystąpienie programu Visual Studio, a na pasku menu kliknij widok i Eksploratora serwera. Kliknij ikonę platformy Azure, nawiązać połączenia z subskrypcją platformy Azure. Następnie przejdź do usługi Azure -> Storage -> `<your storage account>` -> tabel -> WADLogsTable. Aby uzyskać więcej informacji, zobacz [Eksploratora serwera](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

WADLogsTable wyróżnione na rysunku powyżej zawiera szczegółowe zdarzenia z dziennika aplikacji usługi dzielenia i scalania. Należy pamiętać, że domyślna konfiguracja pobranego pakietu jest skierowana do wdrożenia produkcyjnego. W związku z tym interwał, w którym dzienniki i liczniki są ściągane z wystąpienia usługi jest duży (5 minut). Do projektowania i testowania zmniejszyć interwał przez dostosowanie ustawień diagnostyki sieci web lub roli procesu roboczego do Twoich potrzeb. Kliknij prawym przyciskiem myszy rolę w Eksploratorze serwera usługi Visual Studio (zobacz powyżej), a następnie Dostosuj okres transferu w oknie dialogowym ustawień konfiguracji diagnostyki:

![Konfigurowanie][3]

## <a name="performance"></a>Wydajność

Ogólnie rzecz biorąc lepszej wydajności jest można oczekiwać od im większa więcej wydajne warstwy usług w usłudze Azure SQL Database. Wyższe alokacje We/Wy i procesora CPU i pamięci dla wyższej warstwy usługi dodatkowe korzyści kopiowania masowego i Usuń operacje, których używa usługi dzielenia i scalania. Z tego powodu należy zwiększyć warstwy usług, tylko dla tych baz danych zdefiniowana, ograniczony okres czasu.

Usługa wykonuje również weryfikacji zapytań w ramach normalnych operacji. Te zapytania weryfikacji sprawdzić obecność nieoczekiwane dane w zakresie docelowym i upewnij się, że każdej operacji podziału/scalania/Przenieś zaczyna się w spójnym stanie. Tych zapytań, wszystkie pracować nad zakresów kluczy fragmentowania, definiowane przez zakres operacji i rozmiar partii w ramach definicji żądania. Te zapytania działają najlepiej, gdy indeks jest obecny, który ma klucz fragmentowania jako wiodących kolumn.

Ponadto właściwość unikatowości o klucz fragmentowania jako kolumna wiodących umożliwi usługi w celu używania zoptymalizowanego podejście, który ogranicza zużycie zasobów w zakresie miejsca w dzienniku i pamięci. Ta właściwość unikatowości jest wymagany do przenoszenia dużych ilości danych (zwykle ponad 1GB).

## <a name="how-to-upgrade"></a>Jak uaktualnić

1. Postępuj zgodnie z instrukcjami w [wdrażanie usługi dzielenia i scalania](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Zmienianie pliku konfiguracji usługi chmury dla danego wdrożenia dzielenia i scalania odzwierciedlić nowe parametry konfiguracji. Nowy parametr wymagane to informacje o certyfikat używany do szyfrowania. Prosty sposób, w tym celu jest porównanie nowy plik szablonu konfiguracji z pobrania względem istniejącej konfiguracji. Upewnij się, że należy dodać ustawienia "DataEncryptionPrimaryCertificateThumbprint" i "DataEncryptionPrimary" dla sieci web i roli procesu roboczego.
3. Przed wdrożeniem aktualizacji na platformie Azure, upewnij się, że wszystkie operacje uruchomioną obecnie dzielenia i scalania została zakończona. Możesz to łatwo zrobić, badając stan żądania i PendingWorkflows tabele w bazie danych metadanych dzielenia i scalania dla bieżących żądań.
4. Zaktualizuj istniejące wdrożenie usługi dzielenia i scalania w ramach subskrypcji Azure cloud przy użyciu nowego pakietu i zaktualizowanego pliku konfiguracji.

Nie trzeba aprowizować nową bazę danych metadanych do dzielenia i scalania uaktualnić. Nowa wersja zostanie automatycznie uaktualniona istniejących metadanych bazy danych do nowej wersji.

## <a name="best-practices--troubleshooting"></a>Najlepsze praktyki i rozwiązywanie problemów

- Zdefiniuj dzierżawcą testowym i wykonywania operacji podziału/scalania/Przenieś najważniejszych z dzierżawcą testowym kilka fragmentów. Upewnij się, że wszystkie metadane jest zdefiniowany poprawnie za mapy fragmentów i że operacje naruszają ograniczenia lub klucze obce.
- Zachowaj dzierżawcą testowym problemów dotyczących rozmiaru danych powyżej maksymalną wielkość największego dzierżawy upewnij się, nie występują rozmiar danych. Dzięki temu można ocenić górną granicę na czas potrzebny do przenoszenia pojedynczej dzierżawy.
- Upewnij się, że schemat zezwala na usunięcia. Usługi dzielenia i scalania wymaga możliwości danych przed usunięcie fragmentów źródła danych zostały pomyślnie skopiowane do obiektu docelowego. Na przykład **Usuń wyzwalacze** może uniemożliwić usłudze usuwania danych w źródle i może spowodować niepowodzenie operacji.
- Klucz fragmentowania należy wiodących kolumn klucza podstawowego lub unikatowego indeksu definicji. Dzięki temu najlepszą wydajność zapytań weryfikacji dzielenia ani scalania i rzeczywiste dane przenoszenie i usuwanie działań, które zawsze działają na zakresach klucza fragmentowania.
- W ten sposób rozmieszczać usługi dzielenia i scalania w centrum danych i region, gdzie znajdują się bazy danych.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
