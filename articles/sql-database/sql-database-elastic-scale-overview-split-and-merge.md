---
title: Przenoszenie danych między bazami danych w chmurze skalowalnych w poziomie | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak do manipulowania odłamków i przenoszenia danych za pośrednictwem usługi hostowania samoobsługowego przy użyciu interfejsów API elastycznej bazy danych.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c70279bd52f7b0b0e0cbc27742eca93d9af5e630
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Przenoszenie danych między skalowanymi bazami danych w chmurze
Jeśli jesteś oprogramowania jako deweloper usługi, i nagle aplikacji ulega ogromne żądanie, należy uwzględnić rozwój. Dlatego należy dodać więcej baz danych (odłamków). Jak wszystkie dane do nowych baz danych bez zakłócania integralność danych Użyj **narzędzia do scalania podziału** do przenoszenia danych z baz danych ograniczone do nowych baz danych.  

Narzędzia do scalania podziału działa jako usługa sieci web platformy Azure. Administratorem lub deweloperem używa narzędzia do przenoszenia shardlets (dane z niezależnego fragmentu) między różnych baz danych (odłamków). Narzędzie wykorzystuje niezależnego fragmentu mapy zarządzania do obsługi bazy danych metadanych usługi i zapewnić spójne mapowania.

![Przegląd][1]

## <a name="download"></a>Do pobrania
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentacja
1. [Samouczek narzędzi elastycznej bazy danych scalania podziału](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfiguracja zabezpieczeń scalania podziału](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Zagadnienia dotyczące zabezpieczeń scalania podziału](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Zarządzanie mapami fragmentów](sql-database-elastic-scale-shard-map-management.md)
5. [Migrowanie istniejących baz danych do skalowalnego w poziomie](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md)
7. [Elastyczne słownik Narzędzia bazy danych](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Dlaczego warto używać narzędzia do scalania podziału?
**Elastyczność**

Aplikacje muszą stretch elastycznie poza granicami pojedynczej bazy danych bazy danych SQL Azure. Użyj narzędzia do przenoszenia danych, w razie potrzeby do nowych baz danych, przy zachowaniu integralności.

**Podziel się rozrastać** 

Należy zwiększyć ogólną wydajność obsługi dynamiczny rozwój. Aby to zrobić, należy utworzyć jej większą pojemność dzielenia na fragmenty danych oraz dystrybucję w przyrostowo więcej baz danych, dopóki nie są spełnione wymagana pojemność. To jest przykład głównym funkcji "podział". 

**Scal, aby zmniejszyć rozmiar**

Pojemność musi zmniejszać ze względu na specyfikę okresach przedsiębiorstwa. Narzędzie pozwala skali mniejszej liczby jednostek skalowania podczas spowalnia biznesowych. Funkcja "merge" w usłudze scalania podziału elastycznego skalowania obejmuje to wymaganie. 

**Zarządzanie punktami HotSpot przenosząc shardlets**

Z wieloma dzierżawcami na bazę danych Alokacja shardlets do odłamków może prowadzić do wąskich gardeł wydajności na niektórych fragmentów. Wymaga to ponowne przydzielanie shardlets lub przeniesienie zajęty shardlets do nowych lub mniej wykorzystywanych fragmentów. 

## <a name="concepts--key-features"></a>Pojęcia dotyczące & najważniejsze funkcje
**Obsługiwana w środowisku klienta usługi**

Scalanie podziału jest dostarczana jako usługa hostowana przez klienta. Należy wdrożyć i obsługi usługi w subskrypcji platformy Microsoft Azure. Pakiet, który można pobrać z NuGet zawiera szablon konfiguracji do ukończenia wraz z informacjami dla określonego wdrożenia. Zobacz [samouczek scalania podziału](sql-database-elastic-scale-configure-deploy-split-and-merge.md) szczegółowe informacje. Ponieważ usługa jest uruchamiana w Twojej subskrypcji platformy Azure, można kontrolować i skonfigurować większość ustawień zabezpieczeń usługi. Szablon domyślny zawiera opcje, aby skonfigurować protokół SSL, uwierzytelnianie klienta oparte na certyfikatach, szyfrowania przechowywanych poświadczeń, ochrona systemu DoS i ograniczenia adresów IP. Więcej informacji na temat aspekty zabezpieczeń można znaleźć w następującym dokumencie [konfiguracji zabezpieczeń scalania podziału](sql-database-elastic-scale-split-merge-security-configuration.md).

Wartość domyślna wdrożona usługa działa z jednego procesu roboczego i jedną rolę sieci web. Rozmiar maszyna wirtualna A1 każdego używa usługi w chmurze Azure. Gdy nie można zmodyfikować te ustawienia podczas wdrażania pakietu, można je zmienić po pomyślnym wdrożeniu w uruchomionej usługi chmury (za pośrednictwem portalu Azure). Należy pamiętać, że roli proces roboczy nie mogą być skonfigurowane dla więcej niż jednego wystąpienia przyczyn technicznych. 

**Identyfikator niezależnego fragmentu integracji mapy**

Usługa scalania podziału współdziała z mapy niezależnych aplikacji. Podczas korzystania z usługi podziału scalania, aby dzielenie i scalanie zakresów lub shardlets między odłamków, usługa automatycznie aktualizuje mapy niezależnego fragmentu aktualne. Aby to zrobić, usługa łączy do bazy danych Menedżera mapy niezależnych aplikacji i przechowuje zakresy i mapowania postęp żądań move podziału/merge. Dzięki temu, że mapy niezależnego fragmentu zawsze przedstawia aktualny widok podczas operacji scalania podziału będą. Podzielić, operacje przenoszenia scalania i shardlet są implementowane przez przeniesienie partii shardlets z niezależnych źródła do niezależnych docelowej. Podczas operacji przenoszenia shardlet shardlets może ulec bieżącej partii są oznaczone w trybie offline w mapie niezależnego fragmentu i są dostępne na potrzeby połączeń routingu zależne od danych za pomocą **OpenConnectionForKey** interfejsu API. 

**Spójne shardlet połączeń**

Po uruchomieniu przenoszenie danych do nowej partii shardlets wszystkie podane mapowanie niezależnych zależne od danych routingu połączenia niezależnych przechowywania shardlet są zabitych i kolejnych połączeń z mapy niezależnego fragmentu interfejsów API do powyższej shardlets są blokowane podczas przenoszenia danych jest w toku, aby uniknąć niespójności. Połączenia z innymi shardlets na sam identyfikator niezależnego fragmentu również pobrać skasowane, ale powiedzie się ponownie od razu przy ponownej próbie. Po przeniesieniu partii shardlets oznaczonych jako online ponownie dla niezależnych docelowego, a źródło danych zostanie usunięta z niezależnych źródła. Usługa przechodzi przez te czynności dla każdej partii, dopóki wszystkie shardlets zostały przeniesione. To spowoduje kilka operacji kill połączenia w trakcie ukończenia operacji podziału/merge/przeniesienia.  

**Zarządzanie shardlet dostępności**

Ograniczanie połączenia skasowanie do bieżącej partii shardlets zgodnie z powyższym opisem ogranicza zakres niedostępności jednej partii shardlets naraz. Jest to preferowany nad rozwiązaniem gdzie pełny identyfikator niezależnego fragmentu pozostanie w trybie offline dla wszystkich jego shardlets w trakcie operacji dzielenie i scalanie. Rozmiar wsadu zdefiniowany jako liczbę unikatowych shardlets przenosić w czasie, jest parametru konfiguracji. Może być zdefiniowana dla każdej operacji dzielenie i scalanie w zależności od potrzeb dostępności i wydajności aplikacji. Należy pamiętać, że zakres, który jest zablokowany na mapie niezależnych może być większy niż określony rozmiar partii. Jest to spowodowane usługi wybiera rozmiar zakresu w taki sposób, że rzeczywista liczba wartości kluczy dzielenia na fragmenty danych około dopasowuje rozmiar partii. Jest to ważne do zapamiętania w szczególności kluczy słabo wypełnione dzielenia na fragmenty. 

**Magazynu metadanych**

Usługa scalania podziału korzysta z bazy danych, aby zachować stan i zachować dzienniki podczas przetwarzania żądania. Użytkownik tworzy tej bazy danych w swoich subskrypcji i zawiera parametry połączenia dla niego w pliku konfiguracji wdrożenia usługi. Administratorzy z organizacji użytkownika umożliwia też łączność z tej bazy danych, aby sprawdzić postęp żądania i do sprawdzania, czy szczegółowe informacje dotyczące potencjalnych błędów.

**Rozpoznawanie dzielenia na fragmenty**

Usługa scalania podziału oddzieli (1) podzielonej tabel, tabele odwołań (2) i (3) normalne tabel. Semantyka operacji przenoszenia podziału/merge są zależne od typu tabeli użyte i są zdefiniowane w następujący sposób: 

* **Tabele podzielonej**: podziału, scalania i operacji przenoszenia Przenieś shardlets ze źródła do docelowego niezależnego fragmentu. Po pomyślnym ukończeniu ogólną żądania shardlets te nie są już dostępne w źródle. Należy pamiętać, że tabel docelowych musi istnieć na docelowy identyfikator niezależnego fragmentu i nie może zawierać danych w zakresie docelowego przed wykonania operacji. 
* **Tabele odwołań dla**: tabele odwołań, podziału, Scal i przenieść operacje skopiować dane ze źródła do docelowego identyfikator niezależnego fragmentu. Należy jednak pamiętać, że żadne zmiany wystąpić na niezależnych docelowy dla danej tabeli, jeśli każdy wiersz znajduje się już w tej tabeli w elemencie docelowym. Tabela ma być puste dla operacji kopiowania tabeli wszelkie odwołania do przetworzenie.
* **Inne tabele**: inne tabele mogą być obecne na źródłowy lub docelowy operacji dzielenie i scalanie. Usługa scalania podziału pomija te tabele operacje kopiowania lub przenoszenia danych. Należy jednak pamiętać, że może zakłócać te operacje, w przypadku ograniczenia.

Informacje w odwołaniu a podzielonej tabele są udostępniane przez **SchemaInfo** interfejsów API na mapie niezależnego fragmentu. Poniższy przykład przedstawia użycie tych interfejsów API danego niezależnych Mapa menedżera obiektu smm: 

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

Tabele "region" i "uprzywilejowania" są zdefiniowane jako tabele odwołań i zostaną skopiowane z operacji przenoszenia podziału/merge. "klient" i "zamówienia" z kolei są definiowane jako podzielonej tabel. C_CUSTKEY i O_CUSTKEY służyć jako klucz dzielenia na fragmenty. 

**Integralność referencyjna**

Usługa scalania podziału analizuje zależności między tabelami i używa relacje klucza obcego klucza podstawowego, aby przemieścić operacji przenoszenia tabele odwołań i shardlets. Ogólnie rzecz biorąc tabele odwołań są kopiowane najpierw w kolejności zależności, a następnie shardlets są kopiowane w kolejności ich zależności w ramach każdej partii. Jest to konieczne, dzięki czemu ograniczenia klucza podstawowego klucza Obcego niezależnych docelowej są honorowane jako nadejścia nowych danych. 

**Spójność mapy niezależnego fragmentu i ostatecznego zakończenia**

Obecności awarie usługi scalania podziału wznawia działania po awarii dowolnego i ma na celu wykonywania w toku żądania. Jednak sytuacjach może być nieodwracalny, np. gdy niezależnych docelowy zostanie utracony lub naruszony naprawić. W tych warunkach niektórych shardlets, które były zaplanowane do przeniesienia może nadal znajdować się na niezależnych źródła. Usługę gwarantuje, że shardlet mapowania są aktualizowane wyłącznie po niezbędnych danych zostały pomyślnie skopiowane do obiektu docelowego. Shardlets są usuwane tylko w źródle po wszystkich swoich danych został skopiowany do obiektu docelowego i odpowiednie mapowania zostały pomyślnie zaktualizowane. Operacji usuwania odbywa się w tle, gdy zakres jest w trybie online na docelowy identyfikator niezależnego fragmentu. Usługi scalania podziału zawsze zapewnia poprawność mapowania przechowywane w mapie niezależnego fragmentu.

## <a name="the-split-merge-user-interface"></a>Interfejs użytkownika scalania podziału
Pakiet usługi scalania podziału obejmuje rolę procesu roboczego oraz roli sieci web. Rola sieci web jest używana do przesyłania żądań podziału scalania w sposób interaktywny. Główne składniki interfejsu użytkownika są następujące:

* Typ operacji: Typ operacji jest przycisku radiowego, który określa rodzaj operacji wykonywanych przez usługę dla tego żądania. Można wybrać podziału, scalanie i Przenieś scenariuszy. Można również anulować operację wcześniej zostało przesłane. Można użyć podziału, Scal i Przenieś żądania zakresu niezależnego fragmentu mapowania. Lista niezależnych mapuje tylko operacji przenoszenia pomocy technicznej.
* Mapa niezależnego fragmentu: W następnej sekcji Parametry żądania obejmują informacje dotyczące mapowania niezależnego fragmentu i hosting mapy niezależnego fragmentu bazy danych. W szczególności należy podać nazwę serwera bazy danych SQL Azure i obsługi shardmap, poświadczenia do połączenia z bazą danych mapowania niezależnego fragmentu, a na końcu Nazwa mapy niezależnego fragmentu bazy danych. Obecnie operację akceptuje tylko jednego zestawu poświadczeń. Wymagane są poświadczenia mają wystarczające uprawnienia do wykonania na odłamków zmiany do mapy niezależnego fragmentu, jak również danych użytkownika.
* Zakres źródła (dzielenie i scalanie): operacja dzielenie i scalanie przetwarza zakresu, używając swojego klucza niski i wysoki. Aby określić operację z niepowiązany wysoka wartość klucza, zaznacz pole wyboru "max jest wysoka wartość klucza" i pozostaw puste pole klucza wysoki. Podane wartości klucza zakresu musi być dokładnie zgodny mapowania i jego granice na mapie niezależnego fragmentu. Jeśli nie określono żadnych granic zakresu na wszystkich usługi wywnioskuje najbliższego zakresu można automatycznie. Skrypt programu GetMappings.ps1 PowerShell umożliwia pobrać bieżące mapowania na mapie danego niezależnego fragmentu.
* Zachowanie źródła podziału (podział): dla operacji podziału zdefiniować punkt podziału zakresu źródła. Aby to zrobić, podając klucza dzielenia na fragmenty, w miejscu podziału występuje. Użyj przycisku radiowego Określ, czy chcesz dolnej części zakresu (z wyjątkiem klucza podziału) przenieść lub czy ma górnej części, aby przenieść (w tym kluczu podziału).
* Źródło Shardlet (przenoszenia): Przenieś operacji różnią się od dzielenie i scalanie operacji ponieważ nie wymagają one zakres do opisywania źródła. Źródło przenoszenia po prostu jest identyfikowany przez wartość klucza dzielenia na fragmenty, który ma zostać przeniesiona.
* Docelowy identyfikator niezależnego fragmentu (podział): po informacji podano w źródle operacji podziału, należy określić, gdzie danych ma zostać skopiowany na, podając nazwę serwera i bazy danych bazy danych SQL Azure dla elementu docelowego.
* Zakres docelowy (scalanie): operacji scalania Przenieś shardlets do istniejących niezależnego fragmentu. Należy określić istniejącą niezależnego fragmentu zapewniając granice zakresu istniejących zakresu, który chcesz scalić z.
* Wielkość partii: Wielkość partii określa liczbę shardlets, które przechodzą w tryb offline w czasie przenoszenia danych. Jest wartością całkowitą, których można użyć mniejsze wartości po wrażliwe na długie okresy przestojów w przypadku shardlets. Większe wartości zwiększy się czas danego shardlet w trybie offline, ale może zwiększyć wydajność.
* Identyfikator operacji (anulować): Jeśli masz wykonywana operacja, która nie jest już potrzebne, można anulować operację, podając jego identyfikator operacji w tym polu. Identyfikator operacji można pobrać z tabeli stan żądania (zobacz sekcja 8.1) lub z danych wyjściowych w przeglądarce sieci web, w której zostało przesłane żądanie.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia
Bieżąca implementacja usługi scalania podziału podlega następujące wymagania i ograniczenia: 

* Odłamków muszą znajdować i być zarejestrowane w mapie niezależnego fragmentu, zanim będzie można wykonać operacji scalania podziału na tych fragmentów. 
* Usługa nie tworzy tabele lub inne obiekty bazy danych, które automatycznie w ramach operacji. To oznacza, że schemat dla podzielonej wszystkie tabele i tabele odwołań muszą istnieć na niezależnych docelowy wyewidencjonowywany przed każdą operacją podziału/merge/przeniesienia. Tabele podzielonej w szczególności muszą być w zakresie, gdzie mają być dodawane przez operację podziału/merge/przeniesienia shardlets nowy pusty. W przeciwnym razie operacja zakończy się niepowodzeniem Sprawdzanie spójności początkowej w niezależnych docelowej. Należy również zauważyć tego odwołania, które dane są kopiowane tylko, jeśli odwołanie tabela jest pusta, i czy nie występują żadne gwarancje spójności w odniesieniu do innych równoczesnych operacje zapisu w tabeli odniesienia. Jest to zalecane: podczas uruchamiania operacji podziału/merge, żadne inne operacje zapisu zmianę tabele odwołań.
* Usługa zależy od tożsamości wiersza unikatowy indeks lub klucz, który zawiera klucz dzielenia na fragmenty, aby zwiększyć wydajność i niezawodność shardlets duże. Dzięki temu usługa przenoszenia danych w jeszcze bardziej szczegółowy od tylko wartości klucza dzielenia na fragmenty. Pozwala to zmniejszyć maksymalną ilość miejsca w dzienniku blokad, które są wymagane podczas operacji. Należy rozważyć utworzenie unikatowego indeksu lub klucza podstawowego, w tym klucz dzielenia na fragmenty w danej tabeli, jeśli chcesz użyć tej tabeli z żądaniami podziału/merge/przeniesienia. Ze względu na wydajność klucz dzielenia na fragmenty powinien być wiodące kolumny klucza lub indeksu.
* W trakcie przetwarzania żądania niektóre dane shardlet mogą występować zarówno na serwerze źródłowym i docelowym identyfikator niezależnego fragmentu. Jest to konieczne chronić przed awariami podczas przemieszczania shardlet. Integracja podziału scalanie z mapą niezależnego fragmentu upewnia się, że połączenia w danych zależnych routingu interfejsów API przy użyciu **OpenConnectionForKey** metody na mapie niezależnego fragmentu nie ma żadnych niespójne pośrednich stanów. Jednak podczas nawiązywania połączenia źródłowy lub docelowy odłamków bez użycia **OpenConnectionForKey** metody niespójne pośrednich stanów mogą być widoczne, gdy będą żądań move podziału/merge. Połączenia te mogą być wyświetlane wyniki częściowe lub zduplikowane w zależności od czas lub fragmentacji, połączenie znajdujące się poniżej. To ograniczenie zawiera obecnie połączeń elastycznego skalowania kilku-Shard-zapytania.
* Bazy danych metadanych dla usługi podziału scalania nie może być udostępniany między różne role. Na przykład rola usługi scalania podziału uruchomiona w przejściowym musi wskazywać różnych metadanych bazy danych niż roli produkcji.

## <a name="billing"></a>Rozliczenia
Usługa podziału scalania działa jako usługa w chmurze w ramach subskrypcji Microsoft Azure. W związku z tym dla usługi w chmurze opłaty do Twojego wystąpienia usługi. Jeśli często wykonać operacji przenoszenia podziału/merge, zaleca się usunąć usługi w chmurze podziału scalania. Zapisuje kosztów uruchomiona albo wdrożyć wystąpienia usługi chmury. Można ponownie wdrożyć i rozpocząć konfigurację łatwo do uruchomienia, zawsze, gdy trzeba wykonać dzielenie i scalanie operacji. 

## <a name="monitoring"></a>Monitorowanie
### <a name="status-tables"></a>Stan tabel
Udostępnia usługi scalania podziału **stan żądania** tabeli w bazie danych magazynu metadanych do monitorowania została zakończona i bieżących żądań. W tabeli wymieniono wiersz dla każdego żądania podziału scalania, który został przesłany do tego wystąpienia usługi scalania podziału. Udostępnia następujące informacje dla każdego żądania:

* **Sygnatura czasowa**: godzinę i datę rozpoczęcia żądania.
* **OperationId**: A identyfikator GUID, który unikatowo identyfikuje żądania. To żądanie może również anulować operację, gdy jest nadal uruchomione.
* **Stan**: bieżący stan żądania. Bieżące żądania również wyświetlane Bieżąca faza, w którym jest żądanie.
* **CancelRequest**: flagę wskazującą, czy żądanie zostało anulowane.
* **Postęp**: oszacowanie przez procent wykonania operacji. Wartość 50 wskazuje, że operacja jest wykonano około 50%.
* **Szczegóły**: wartości XML, która zapewnia bardziej szczegółowy raport postępu. Raport dotyczący postępu jest okresowo aktualizowany jako zestawy wierszy są kopiowane z źródłowego do docelowego. W przypadku awarii lub wyjątki ta kolumna zawiera bardziej szczegółowe informacje o awarii.

### <a name="azure-diagnostics"></a>Diagnostyka Azure
Usługa scalania podziału używa diagnostyki Azure oparte na 2.5 zestawu SDK platformy Azure, monitorowania i diagnostyki. Konfiguracją diagnostyki opisane tutaj: [Włączanie diagnostyki w usług Azure Cloud Services i maszyn wirtualnych](../cloud-services/cloud-services-dotnet-diagnostics.md). Pakiet do pobrania zawiera dwie konfiguracje diagnostyki — jeden dla roli sieci web i jeden dla roli proces roboczy. Te konfiguracje diagnostyki dla usługi, postępuj zgodnie ze wskazówkami [podstawy usługi w chmurze w systemie Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Zawiera definicje, aby rejestrować liczniki wydajności, dzienniki programu IIS, dzienniki zdarzeń systemu Windows i dzienniki zdarzeń aplikacji podziału scalania. 

## <a name="deploy-diagnostics"></a>Wdrażanie diagnostyki
Aby włączyć, monitorowania i diagnostyki za pomocą konfiguracji diagnostycznych dla ról sieci web i proces roboczy udostępniane przez pakiet NuGet, uruchom następujące polecenia przy użyciu programu Azure PowerShell: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Można znaleźć więcej informacji na temat Konfigurowanie i wdrażanie ustawień diagnostycznych tutaj: [Włączanie diagnostyki w usług Azure Cloud Services i maszyn wirtualnych](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Pobrać diagnostyki
Diagnostycznej mogli łatwo uzyskiwać dostęp z Eksploratora serwera Visual Studio w Azure części drzewa Eksploratora serwera. Otwórz wystąpienie programu Visual Studio, a na pasku menu kliknij widok i Eksploratora serwera. Kliknij ikonę platformy Azure, aby nawiązać połączenia z subskrypcją platformy Azure. Następnie przejdź do usługi Azure -> magazynu -> <your storage account> -> WADLogsTable -> tabel. Aby uzyskać więcej informacji, zobacz [przeglądanie zasobami magazynu za pomocą Eksploratora serwera](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable wyróżnione na rysunku powyżej zawiera szczegółowe zdarzenia z dziennika aplikacji usługi podziału scalania. Należy pamiętać, że domyślna konfiguracja pobranego pakietu jest przeznaczone dla wdrożenia produkcyjnego. W związku z tym interwał, jaką dzienniki i liczniki są pobierane z wystąpień usługi jest duża (5 minut). Badań i rozwoju niższym interwał dostosowując ustawienia diagnostyki sieci web lub roli procesu roboczego do własnych potrzeb. Kliknij prawym przyciskiem myszy rolę w Eksploratorze serwera usługi Visual Studio (zobacz powyżej), a następnie Dostosuj okres transferu w oknie dialogowym ustawień konfiguracji diagnostyki: 

![Konfigurowanie][3]

## <a name="performance"></a>Wydajność
Ogólnie rzecz biorąc lepszą wydajność ma z tym wyższy więcej wydajności warstw usług w bazie danych SQL Azure. Wyższy alokacji we/wy, Procesor i pamięć dla wyższych warstw usługi korzystać kopiowania zbiorczego i usuwanie operacji używane przez usługę podziału scalania. Z tego powodu należy zwiększyć warstwy usługi tylko dla tych baz danych zdefiniowana, ograniczony okres czasu.

Usługa również wykonuje zapytania weryfikacji w ramach normalnych operacji. Te zapytania weryfikacji sprawdzić nieoczekiwany obecności danych w zakresie docelowego i upewnij się, że wszelkie operacje podziału/merge/przeniesienia zaczyna się w spójnym stanie. Te wszystkie zapytania pracować nad dzielenia na fragmenty zakresami kluczy zdefiniowane w zakresie operacji i rozmiar partii w ramach definicji żądania. Te zapytania wykonywane najlepiej, gdy indeks jest obecnie z kluczem dzielenia na fragmenty jako wiodących kolumn. 

Ponadto przy użyciu klucza wiodące kolumny dzielenia na fragmenty właściwość unikatowości umożliwi usługi do zoptymalizowane metody, która ogranicza zużycie zasobów w zakresie miejsca w dzienniku i pamięci. Ta właściwość unikatowości musi przenieść rozmiary dużej ilości danych (zazwyczaj ponad 1GB). 

## <a name="how-to-upgrade"></a>Jak uaktualnić
1. Postępuj zgodnie z instrukcjami [wdrożyć usługę scalania podziału](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Zmienianie pliku konfiguracji usługi chmury dla danego wdrożenia podziału scalania uwzględnić nowe parametry konfiguracji. Informacje o certyfikat używany do szyfrowania jest nowy parametr wymagany. Prosty sposób, w tym celu jest do porównania nowy plik szablonu konfiguracji z pobierania względem istniejącej konfiguracji. Upewnij się, że możesz dodać ustawienia "DataEncryptionPrimaryCertificateThumbprint" i "DataEncryptionPrimary" dla sieci web i roli proces roboczy.
3. Przed wdrożeniem aktualizacji na platformie Azure, upewnij się, że wszystkie operacje aktualnie uruchomione podziału scalanie zostało ukończone. Można łatwo tym badając stan żądania i PendingWorkflows tabele w bazie danych metadanych scalania podziału bieżących żądań.
4. Zaktualizowanie istniejącego wdrożenia usługi podziału scalanie w Twojej subskrypcji platformy Azure chmury o nowy pakiet i zaktualizowany plik konfiguracji.

Nie należy do udostępnienia nowej bazy danych metadanych scalania podziału do uaktualnienia. Nowej wersji automatycznie zaktualizuje istniejące bazy danych metadanych do nowej wersji. 

## <a name="best-practices--troubleshooting"></a>Najlepsze rozwiązania i rozwiązywanie problemów
* Zdefiniuj dzierżawcą testowym i wykonuje operacje podziału/merge/przeniesienia najważniejszych z dzierżawcą testowym między kilka fragmentów. Upewnij się, że wszystkie metadane jest zdefiniowany poprawnie na mapie niezależnego fragmentu i operacje nie naruszają ograniczeń lub klucze obce.
* Zachowaj dzierżawcą testowym problemy związane z rozmiar danych powyżej maksymalnego rozmiaru danych dzierżawy największy, aby upewnić się, pojawiły się nie rozmiar danych. Dzięki temu można ocenić górna granica na czas potrzebny do przenoszenia pojedynczej dzierżawy. 
* Upewnij się, że schemat zezwala na usuwanie. Usługa podziału scalania wymaga możliwość usunięcia danych z niezależnych źródła, gdy dane zostały pomyślnie skopiowane do obiektu docelowego. Na przykład **Usuwanie wyzwalaczy** można zapobiec usuwania danych w źródle i może spowodować niepowodzenie operacji.
* Klucz dzielenia na fragmenty należy wiodących kolumn w kluczu podstawowym lub definicji unikatowego indeksu. Dzięki temu najlepszą wydajność zapytań weryfikacji dzielenie i scalanie i rzeczywiste dane przenoszenie i usuwanie operacji zawsze działających na zakresami kluczy dzielenia na fragmenty.
* W ten sposób rozmieszczać usługi podziału scalania w centrum danych i regionu, gdzie znajdują się bazy danych. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

