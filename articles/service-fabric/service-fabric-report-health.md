---
title: Dodawanie niestandardowych raportów o kondycji usługi Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wysyłania niestandardowych raportów o kondycji do jednostki kondycji usługi Azure Service Fabric. Oferuje rekomendacje dotyczące projektowania i implementowania jakości raportów o kondycji.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 49ebf4ab95816a3da2f74a464b12b46de6228456
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058615"
---
# <a name="add-custom-service-fabric-health-reports"></a>Dodawanie niestandardowych raportów kondycji usługi Service Fabric
Usługa Azure Service Fabric wprowadza [modelu kondycji](service-fabric-health-introduction.md) przeznaczone do Flaga złej kondycji klastra i warunków określonych jednostek aplikacji. Używa modelu kondycji **Raporty kondycji** (składników systemu i watchdogs). Celem jest łatwe i szybkie diagnozowanie i naprawy. Moduły zapisujące usługi należy traktować ponoszonych z góry o kondycji. Należy podać dowolny warunek, który może mieć wpływ na kondycję, zwłaszcza, jeśli może pomóc problemów flagi blisko głównego. Informacje o kondycji można oszczędzić czas i wysiłek, debugowania i analizy. Użyteczność jest szczególnie oczywiste, gdy usługa jest uruchomiona na dużą skalę w chmurze (prywatnej lub na platformie Azure).

Monitor raporty usługi Service Fabric określone warunki zainteresowania. One raport na temat tych warunków, na podstawie ich widoku lokalnego. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) agreguje dane kondycji wysyłane przez wszystkie raporty w celu ustalenia, czy jednostki są globalnie w dobrej kondycji. Model jest przeznaczony jako zaawansowane, elastyczne i łatwe w użyciu. Jakość raportów kondycji określa dokładność widoku kondycji klastra. Wyniki fałszywie dodatnie, które błędnie pokazują problemy w złej kondycji może niekorzystnie wpłynąć na uaktualnienia lub innych usług używających danych dotyczących kondycji. Przykłady takich usług to naprawy usługi i mechanizmy generowania alertów. W związku z tym rozwagą, konieczne są sporządzanie raportów, które przechwytują warunki publicznej w możliwie najlepszy sposób.

Aby zaprojektować i zaimplementować raportowania kondycji watchdogs i składników systemu musi:

* Zdefiniuj warunek, z którymi są zainteresowani, sposób, który jest monitorowany i wpływ na funkcjonalności klastra lub aplikacji. Na podstawie tych informacji, wybrać właściwość raportów kondycji i stan kondycji.
* Określić [jednostki](service-fabric-health-introduction.md#health-entities-and-hierarchy) , dotyczy raport.
* Określić, gdzie raportowania gotowe, z poziomu usługi lub strażnika wewnętrzne lub zewnętrzne.
* Zdefiniuj źródło używany do identyfikowania zgłaszającą.
* Wybierz strategię raportowania, okresowo lub na przejścia. Zalecaną metodą jest okresowo, ponieważ wymaga uproszczenia kodu i jest mniej podatne na błędy.
* Określ, ile raportów dla warunków w złej kondycji powinno pozostać w magazynie kondycji i jak powinno być wyczyszczone. Korzystając z tych informacji, zdecyduj, czas wygaśnięcia raportu i zachowanie remove na wygaśnięcia.

Jak wspomniano wcześniej, raportowanie może odbywać się od:

* Monitorowane replika usługi Service Fabric.
* Wewnętrzny watchdogs wdrożony jako Usługa Service Fabric (na przykład usługi bezstanowej usługi Service Fabric monitoruje warunków, która wysyła raporty). Watchdogs mogą być wdrażane wszystkie węzły lub być skoligaconym do monitorowanej usługi.
* Wewnętrzny watchdogs, uruchomienia w węzłach usługi Service Fabric, które są *nie* zaimplementowane jako usługi Service Fabric.
* Watchdogs zewnętrznych, które sondowania zasób z *poza* klastra usługi Service Fabric (na przykład Usługa monitorowania takich jak Gomez).

> [!NOTE]
> Natychmiast po klastra jest wypełniana przy użyciu raportów kondycji wysyłane przez składniki systemu. Dowiedz się więcej o [przy użyciu kondycji systemu raportów do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Raporty użytkownika musi zostać wysłany [jednostki kondycji](service-fabric-health-introduction.md#health-entities-and-hierarchy) które już zostały utworzone przez system.
> 
> 

Raz zdrowia, raportowanie, że projekt jest wyczyszczone, raportów o kondycji mogą być wysyłane łatwe. Możesz użyć [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) raportów kondycji, jeśli klaster nie jest [bezpiecznego](service-fabric-cluster-security.md) lub jeśli klient sieci szkieletowej ma uprawnienia administratora. Raportowanie może odbywać się za pośrednictwem interfejsu API, za pomocą [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), za pomocą programu PowerShell lub za pośrednictwem interfejsu REST. Pokrętła konfiguracji partii raportów w celu zwiększenia wydajności.

> [!NOTE]
> Raport kondycji jest synchroniczne i reprezentuje pracę w weryfikacji po stronie klienta. Fakt, że raport jest akceptowany przez klienta usługi kondycji lub `Partition` lub `CodePackageActivationContext` obiektów nie oznacza, że jest ono stosowane w magazynie. Jest wysyłane asynchronicznie i prawdopodobnie wsad z innych raportów. Przetwarzanie na serwerze nadal może zakończyć się niepowodzeniem: numer sekwencji mogą być nieaktualne, jednostki, na którym musi zostać zastosowana raport został usunięty, itp.
> 
> 

## <a name="health-client"></a>Kondycja klienta
Raporty o kondycji są wysyłane do Menedżera kondycji za pomocą klienta kondycji, które znajdują się wewnątrz klienta sieci szkieletowej. Menedżer kondycji raporty są zapisywane w magazynie kondycji. Kondycji klienta można skonfigurować następujące ustawienia:

* **HealthReportSendInterval**: Opóźnienie między czas, który raport zostanie dodany do klienta i są wysyłane do Menedżera kondycji. Używany do raportów usługi batch w pojedynczym komunikacie, zamiast wysyłania jeden komunikat dla każdego raportu. Przetwarzanie wsadowe poprawia wydajność. Domyślne: 30 sekund.
* **HealthReportRetrySendInterval**: Interwał, jaką kondycji klienta umożliwia ponowne wysłanie kondycji zebranych raportów do Menedżera kondycji. Domyślne: 30 sekund, minimalna: 1 sekunda.
* **HealthOperationTimeout**: Limit czasu dla raportu komunikat wysyłany do Menedżera kondycji. Jeśli upłynie limit czasu wiadomości, kondycji klient ponawia próbę go do momentu Menedżera kondycji potwierdza, że raport został przetworzony. Wartość domyślna: dwie minuty.

> [!NOTE]
> Jeśli raporty są przetwarzane wsadowo, klient sieci szkieletowej muszą być przechowywane w aktywności dla co najmniej HealthReportSendInterval, aby upewnić się, że są wysyłane. Jeśli komunikat zostanie utracony lub Menedżera kondycji nie może zastosować je z powodu błędów przejściowych, klient sieci szkieletowej musi życiu już celu nadania mu szansę, aby spróbować ponownie.
> 
> 

Buforowanie na kliencie zajmuje unikatowości raportów pod uwagę. Na przykład określonego reportera zły zgłoszenie 100 raportów na sekundę na tę samą właściwość tego samego obiektu, raporty są zastępowane najnowszej wersji. Istnieje co najwyżej jeden taki raport w kolejce klienta. Jeśli skonfigurowano dzielenia na partie, liczbę raportów wysyłanych do Menedżera kondycji jest tylko jeden na interwał wysyłania. Raport ten stanowi ostatni raport dodano najbardziej aktualny stan jednostki.
Określ parametry konfiguracji podczas `FabricClient` jest tworzony przez przekazanie [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) z odpowiednie wartości dla wpisów z kondycją.

Poniższy przykład tworzy klienta sieci szkieletowej i określa, czy raporty mają być wysyłane, gdy zostaną one dodane. Przekroczeń limitu czasu i błędów, które mogą być ponawiane próby się zdarzyć, co 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Zaleca się pozostawienie ustawienia, które Ustaw domyślnego klienta sieci szkieletowej `HealthReportSendInterval` 30 sekund. To ustawienie zapewnia optymalną wydajność ze względu na partie. W przypadku krytycznych raportów, które muszą zostać przesłane tak szybko, jak to możliwe, użyj `HealthReportSendOptions` z bezpośrednim `true` w [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) interfejsu API. Raporty bezpośredniego obejścia interwał przetwarzania wsadowego. Tej flagi należy używać ostrożnie; Chcemy móc korzystać z klienta kondycji, przetwarzanie wsadowe, jeśli to możliwe. Natychmiastowe wysyłania jest również przydatne, zamykając klienta sieci szkieletowej (na przykład proces wykrył nieprawidłowy stan i wymaga zamknięcia, aby zapobiec efekty uboczne). Zapewnia wysłanie optymalnych zebranych raportów. Po dodaniu jeden raport z flagą natychmiastowego kondycji klienta partii skumulowana raporty od ostatniego wysłania.

Te same parametry można określić po utworzeniu połączenia z klastrem przy użyciu programu PowerShell. Poniższy przykład tworzone jest połączenie z lokalnym klastrem:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Podobnie do interfejsu API, raporty można wysyłać przy użyciu `-Immediate` przełącznika wysłanych bezpośrednio, bez względu na to `HealthReportSendInterval` wartość.

Resztę raporty są wysyłane do bramy usługi Service Fabric, która ma klienckie wewnętrznej sieci szkieletowej. Domyślnie ten klient jest skonfigurowany do wysyłania raportów wsadowej co 30 sekund. Można zmienić interwał usługi batch za pomocą ustawienia konfiguracji klastra `HttpGatewayHealthReportSendInterval` na `HttpGateway`. Jak wspomniano wcześniej, lepszym rozwiązaniem jest na wysyłanie raportów o `Immediate` wartość true. 

> [!NOTE]
> Aby upewnić się, że nieautoryzowane usługi nie można raportować kondycję, względem jednostek w klastrze, należy skonfigurować serwer do akceptowania żądań tylko z bezpiecznego klientów. `FabricClient` Używane na potrzeby raportowania musi mieć z włączonymi zabezpieczeniami mogli komunikować się z klastrem (na przykład przy użyciu protokołu Kerberos lub uwierzytelniania certyfikatu). Przeczytaj więcej na temat [klastra zabezpieczeń](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Raport z w ramach usług z obniżonymi uprawnieniami
Jeśli nie ma dostępu administratora do klastra usługi Service Fabric, można raportować kondycję na jednostkach z bieżącego kontekstu przy użyciu `Partition` lub `CodePackageActivationContext`.

* W przypadku usług bezstanowych użyj [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) do raportu w bieżącym wystąpieniu usługi.
* W przypadku usług stanowych, użyj [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) Aby sporządzić raport na temat bieżącej repliki.
* Użyj [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) do raportu w bieżącej jednostce partycji.
* Użyj [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) Aby sporządzić raport na temat bieżącej aplikacji.
* Użyj [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) Aby sporządzić raport na temat bieżącej aplikacji wdrożonych w bieżącym węźle.
* Użyj [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) Aby sporządzić raport na temat pakietu usług dla aplikacji wdrożonej w bieżącym węźle.

> [!NOTE]
> Wewnętrznie `Partition` i `CodePackageActivationContext` przechowywania skonfigurowane przy użyciu ustawień domyślnych klienta kondycji. Zgodnie z objaśnieniem dla [kondycji klienta](service-fabric-report-health.md#health-client), raporty są partii i wysyłanych przez czasomierz. Obiekty powinny być przechowywane w aktywności mogły wysłać ten raport.
> 
> 

Można określić `HealthReportSendOptions` podczas wysyłania raportów za pośrednictwem `Partition` i `CodePackageActivationContext` kondycji interfejsów API. Jeśli masz krytyczne raporty, które muszą zostać przesłane tak szybko, jak to możliwe, należy użyć `HealthReportSendOptions` z bezpośrednim `true`. Raporty bezpośredniego obejścia interwał przetwarzania wsadowego klienckie wewnętrznej kondycji. Jak wspomniano wcześniej, należy użyć tej flagi, ostrożnie; Chcemy móc korzystać z klienta kondycji, przetwarzanie wsadowe, jeśli to możliwe.

## <a name="design-health-reporting"></a>Projektowanie, raportowanie stanu
Pierwszym krokiem podczas generowania wysokiej jakości raporty identyfikuje warunki, które mogą mieć wpływ na kondycję usługi. Dowolny warunek, ułatwiający flagi problemów w usłudze lub klastra podczas jego uruchamiania — lub jeszcze lepiej, zanim problem występuje — może potencjalnie zapisać miliardy dolarów. Korzyści wynikające z mniej czas przestoju, mniej godzinach nocnych poświęcony na wykrywanie i naprawianie problemów i wyższych zadowolenie klientów.

Po zidentyfikowaniu warunki autorzy strażnika trzeba ustalić najlepszy sposób Monitoruj je uzyskać równowagę między kosztów i użyteczność. Rozważmy na przykład usługi, który wykonuje złożone obliczenia, które używają niektóre pliki tymczasowe w udziale. Strażnika może monitorować udziału, aby upewnić się, że dostępna jest wystarczająca ilość miejsca. Może ona nasłuchiwanie powiadomienia o zmianach pliku lub katalogu. Można go raportować ostrzeżenie, jeśli próg ponoszonych z góry, a zgłosić błąd, jeśli udział jest pełny. W przypadku ostrzeżenia systemu naprawy można uruchomić, czyszczenie starszych plików w udziale. W przypadku błędu system naprawy przenieść repliki usługi do innego węzła. Należy zauważyć, jak Stany warunek jest opisywana w kategoriach kondycji: stan warunek, który jest uznawana za dobrej kondycji (ok) lub złej kondycji (ostrzeżenia lub błędu).

Po ustawieniu szczegółów monitorowania Edytor strażnika musi ustalić sposób implementacji strażnika. Jeśli warunki można ustalić na podstawie poziomu usługi, strażnika może być częścią samej monitorowanej usługi. Na przykład kod usługi można sprawdzić użycie udziału, a następnie zgłaszanie za każdym razem, gdy próbuje zapisać plik. Zaletą tego podejścia jest raportowanie proste. Należy uważać, aby uniemożliwić błędy strażnika wywierania wpływu na funkcjonalność usługi.

Raportowanie w programie monitorowanej usługi nie zawsze jest opcjonalnym. Strażnika w ramach usługi nie można wykryć warunków. Może nie mieć logiki lub dane, które umożliwiają określenie. Obciążenie monitorowania warunki mogą być wysokie. Warunki również może nie być właściwe dla usługi, ale zamiast tego wpływają na interakcje między usługami. Innym rozwiązaniem jest zapewnienie watchdogs w klastrze jako oddzielne procesy. Watchdogs monitorować warunki i raportów, bez wywierania wpływu na główne usług w jakikolwiek sposób. Na przykład te watchdogs można zaimplementować jako bezstanowej usługi w tej samej aplikacji, wdrożone we wszystkich węzłach lub na tym samym węzłów jako usługi.

Czasami strażnika, uruchomione w klastrze nie jest opcją albo. Jeśli warunek monitorowanych jest dostępności lub funkcji usługi użytkownicy widzą ją, jest najlepszym rozwiązaniem jest watchdogs w tym samym miejscu, co klienci użytkownika. Ich testowania operacji w taki sam sposób, ich wywoływania przez użytkowników. Na przykład można mieć strażnika, który znajduje się poza klastrem, wysyła żądania do usługi i sprawdza, czy opóźnienia i poprawność wyniku. (Dla usługi Kalkulator, na przykład, czy 2 + 2 zwraca 4 w rozsądnym czasie?)

Gdy sfinalizowany szczegóły strażnika, należy podjąć decyzję dotyczącą, na identyfikator źródła, który unikatowo identyfikuje go. Jeśli wiele watchdogs tego samego typu mieszkających w klastrze, należy raportować różnymi jednostkami lub, ich raport na temat tej samej jednostki, użyj Identyfikatora innego źródła lub właściwość. W ten sposób ich raporty mogą współistnieć. Właściwość raport o kondycji należy przechwytywać monitorowanych warunku. (Na przykład powyżej, właściwość może być **ShareSize**.) Jeśli wiele raportów dotyczy ten sam stan, właściwość powinna zawierać pewne informacje dynamicznych, które umożliwia raportów, które będą mogły nadal współistnieć. Na przykład, jeśli wiele udziałów muszą być monitorowane, nazwa właściwości może być **ShareSize sharename**.

> [!NOTE]
> Czy *nie* Użyj magazynu kondycji, aby zachować informacje o stanie. Tylko informacje dotyczące kondycji powinny być raportowane jako kondycji, ponieważ te informacje ma wpływ na ocenę kondycji jednostki. Magazynu kondycji nie został zaprojektowany jako magazyn ogólnego przeznaczenia. Używa logiki oceny kondycji agregacji wszystkich danych do stanu kondycji. Wysyłanie informacji niezwiązanych ze sobą na kondycji (np. raportowanie stanu ze stanem kondycji OK) nie ma wpływu na stan kondycji zagregowane, ale może to negatywnie wpłynąć na wydajność magazynu kondycji.
> 
> 

Dalej podjęcie decyzji włączeniu jakiej encji do raportu. W większości przypadków, warunek jednoznacznie identyfikuje obiekt. Wybierz jednostkę z najlepszą dokładnością możliwe. Jeśli warunek ma wpływ na wszystkie repliki w partycji, zgłoś się na partycji, nie w usłudze. Istnieją przypadki brzegowe, gdzie potrzebna jest więcej myślenia, mimo że. Jeśli warunek ma wpływ na jednostki, takie jak repliki, ale jest używany przede wszystkim wtedy warunek oflagowani w związku z więcej niż czas trwania cyklu życia repliki, a następnie powinny być raportowane na partycji. W przeciwnym razie po usunięciu repliki magazynu kondycji czyści wszystkie jego raporty. Autorzy strażnika należy wziąć pod uwagę okresy istnienia jednostki i raport. Musi być jasne po raportu należy oczyścić ze Sklepu (na przykład, gdy ma już zastosowania błędu zgłoszonego na jednostkę).

Przyjrzyjmy się przykładowi, który umieszcza razem punktów I opisem. Należy wziąć pod uwagę, że aplikacja usługi Service Fabric składa się z głównej usługi stanowej trwałe i dodatkowych usług bezstanowych wdrożone we wszystkich węzłach (jeden typ usługi pomocnicze dla każdego typu zadania). Wzorzec ma kolejki przetwarzania, która zawiera polecenia, które ma być wykonane przez pomocnicze bazy danych. Pomocnicze bazy danych wykonaj żądań przychodzących i wysyłać sygnały wstecz potwierdzenia. Jeden warunek, który może być monitorowany jest długości kolejki przetwarzania wzorca. Jeśli długość kolejki głównej osiąga próg, ostrzeżenie jest zgłaszane. To ostrzeżenie wskazuje, że pomocnicze bazy danych nie może obsłużyć obciążenie. Jeśli kolejka osiągnie maksymalną długość, a polecenia są odrzucane, błąd zostanie zgłoszony jako usługi nie można odzyskać. Raporty mogą znajdować się na właściwość **QueueStatus**. Strażnika, który znajduje się w usłudze i jest okresowo przesyłane w replice podstawowej wzorca. Czas wygaśnięcia wynosi dwie minuty, a wysłaniem okresowo co 30 sekund. Jeśli podstawowy ulegnie awarii, raport jest automatycznie czyszczone z magazynu. Jeśli replika usługa działa, ale zakleszczenie wywoływanej lub inne problemy, raport wygasa w magazynie kondycji. W takim przypadku jednostki jest oceniany na błąd.

Inny warunek, który może być monitorowany jest czas wykonywania zadania. Wzorzec rozdziela zadania podrzędne do pomocniczych baz danych, w oparciu o typ zadania. W zależności od projektu główną można sondować pomocnicze bazy danych do stanu zadania. Można również poczekać, aż pomocnicze bazy danych wysyłać sygnały potwierdzenie Wstecz, gdy są one wykonywane. W drugim przypadku należy uważać, aby wykryć sytuacje, w którym struktury pomocnicze bazy danych lub wiadomości zostaną utracone. Jedną z opcji jest wzorca wysłać żądanie ping do tej samej pomocniczego, który wysyła z powrotem jego stan. Odebranie Brak stanu wzorca uzna to za błąd i zmienia harmonogram zadania. To zachowanie przyjęto założenie, że zadania są idempotentne.

Stan monitorowanych mogą być tłumaczone jako ostrzeżenie, jeśli zadanie nie jest wykonywane w określonym czasie (**t1**, na przykład 10 minut). Jeśli zadanie nie zostało ukończone w czasie (**t2**, na przykład 20 minut), mogą być tłumaczone monitorowanych warunek jako błąd. Nagrodę może odbywać się na wiele sposobów:

* Główny repliki podstawowej okresowo raporty na siebie. Może mieć jedną właściwość dla wszystkich zadań oczekujących w kolejce. Jeśli co najmniej jedno zadanie trwa dłużej, stan tego raportu we właściwości **PendingTasks** jest ostrzeżenia lub błędu, zgodnie z potrzebami. Jeśli nie ma żadnych oczekujących zadań lub wszystkich zadań rozpoczął wykonywanie sekwencji, stan raportu jest OK. Zadania są trwałe. Jeśli podstawowy ulegnie awarii poprawnie zgłosić nadal nowo wypromowaną podstawową.
* Inny proces strażnika (w chmurze lub zewnętrznego) sprawdza, czy zadania (z zewnątrz, na podstawie wyniku odpowiednie zadanie) aby zobaczyć, jeśli są one ukończone. Jeśli nie respektują progów, raport jest wysyłany na główną usługą. Raport wysyłany również dla każdego zadania, który zawiera identyfikator zadania, takie jak **PendingTask + taskId**. Raporty mają być wysyłane tylko w złej kondycji stanów. Ustaw czas na żywo na kilka minut i oznacz raporty, które ma zostać usunięty, gdy wygasają, aby upewnić się, czyszczenia.
* Pomocniczy, który wykonuje zadania raportów, gdy trwa dłużej niż oczekiwano, aby go uruchomić. Wysyła raporty do wystąpienia usługi na właściwość **PendingTasks**. Raport określa wystąpienie usługi, który ma problemy, ale nie odzwierciedla sytuacji, w których pada wystąpienia. Raporty są następnie czyszczone. Można go raport na temat usługi dodatkowej. Jeśli zadanie zakończy się w pomocniczej, dodatkowej wystąpienia czyści raportu z magazynu. Raport nie przechwytuje sytuacji, w którym komunikat potwierdzenia zostanie utracony i zadanie nie zostało zakończone z punktu widzenia głównego.

Jednak raportowanie będzie odbywać się w przypadkach opisanych powyżej, raporty są przechwytywane kondycji aplikacji podczas szacowania kondycji.

## <a name="report-periodically-vs-on-transition"></a>Raport okresowo a na przejście
Przy użyciu modelu raportowania kondycji watchdogs raporty można wysyłać okresowe lub przejścia. Zalecaną metodą strażnika raportowania jest okresowo, ponieważ kod jest znacznie prostsza i mniej podatne na błędy. Watchdogs należy dążyć do być bardzo proste, jak to możliwe uniknąć błędów, które mogą powodować nieprawidłowe raporty. Niepoprawne *złej kondycji* wpływ na raporty oceny kondycji i scenariuszy, w oparciu o kondycji, w tym uaktualnienia. Niepoprawne *dobrej kondycji* raporty ukrycie problemów w klastrze, co nie jest wymagana.

Okresowe raportowania strażnika może być zaimplementowany z licznikiem. Dla czasomierza wywołania zwrotnego strażnika można sprawdzić stan i wysłać raport na podstawie bieżącego stanu. Nie ma potrzeby zobaczyć, który raport został wysłany wcześniej lub wprowadzić wszelkie optymalizacji pod kątem obsługi komunikatów. Klient kondycji ma, przetwarzanie wsadowe logiki, aby poprawić wydajność. Gdy klient kondycji jest życiu, ponowną próbą wewnętrznie aż do raportu zostało potwierdzone przez sklep kondycji lub strażnika generuje raport nowszej za pomocą tego samego obiektu, właściwości i źródła.

Raporty dotyczące przejścia wymaga starannego obsługi stanu. Strażnika monitoruje niektóre warunki i raporty, zmienić tylko wtedy, gdy warunki. Wzrost tego podejścia jest, że są potrzebne mniejszą liczbę raportów. Minusem jest złożoną logikę strażnika. Strażnika posiadania warunków lub raportów, dzięki czemu mogą być kontrolowane na określanie zmian stanu. W trybie failover należy uważać za pomocą raportów dodane, ale jeszcze nie są wysyłane do magazynu kondycji. Numer sekwencyjny musi być coraz większe. Jeśli nie, raporty są odrzucane jako nieaktualne. W rzadkich przypadkach, gdzie jest naliczany utraty danych mogą być potrzebne synchronizacji, między stan zgłaszającą i stan magazynu kondycji.

Raporty dotyczące przejścia ma sens dla usług raportowania na siebie, za pośrednictwem `Partition` lub `CodePackageActivationContext`. Gdy lokalnego obiektu (repliki lub wdrożony pakiet usługi / wdrożono aplikację) jest usunięty, jego raporty są także usuwane. To automatyczne oczyszczanie zwalnia potrzebę synchronizacji między reporter i magazynie danych kondycji. Jeśli raport jest partycji nadrzędnej lub aplikacji nadrzędnej, należy uważać w trybie failover w celu uniknięcia stare raportów w magazynie kondycji. Logika należy dodać do zarządzania stanem poprawne, a następnie wyczyść raport ze sklepu, gdy nie jest już potrzebne.

## <a name="implement-health-reporting"></a>Implementowanie raportowania stanu
Po Wyczyść dane jednostki i raportów, wysyłanie raportów o kondycji może odbywać się za pośrednictwem interfejsu API, programu PowerShell lub REST.

### <a name="api"></a>Interfejs API
Aby zgłosić za pomocą interfejsu API, musisz utworzyć raport o kondycji specyficzne dla typu jednostki, które mają być sporządzić raport na temat. Nadaj Raport kondycji klienta. Ewentualnie utworzyć dodatkowe informacje o kondycji i przekazać ją do Popraw metod raportowania na `Partition` lub `CodePackageActivationContext` Aby sporządzić raport na temat bieżącej jednostki.

Poniższy przykład pokazuje okresowych raportów z strażnika w klastrze. Strażnika sprawdza, czy zasób zewnętrzny jest możliwy z w obrębie węzła. Zasób jest wymagane przez manifestu usługi, w ramach aplikacji. Jeśli zasób jest niedostępny, innych usług w aplikacji, mogą nadal działać prawidłowo. W związku z tym raport jest wysyłany na jednostce pakietu wdrożonej usługi co 30 sekund.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Wysyłanie raportów o kondycji z **ServiceFabric wysyłania*EntityType*HealthReport**.

Poniższy przykład pokazuje okresowe zgłaszanie wartości procesora CPU w węźle. Raporty powinny być przesyłane co 30 sekund i mają czas wygaśnięcia wynosi dwie minuty. Jeśli wygasną, zgłaszającą ma problemy, więc węzeł jest oceniany na błąd. Gdy Procesora jest powyżej wartości progowej, raport ma stan kondycji ostrzeżenia. Jeśli Procesor pozostaje powyżej wartości progowej przekracza skonfigurowany czas, będzie zgłaszane jako błąd. W przeciwnym razie zgłaszającą wysyła kondycja OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Poniższy przykład zgłosi ostrzeżenie przejściowe na replikę. Usługi, który jest zainteresowany danych otrzymuje najpierw Identyfikatora partycji, a następnie identyfikator repliki. Następnie wysyła raport z **PowershellWatcher** we właściwości **ResourceDependency**. Raport ma znaczenie tylko dwóch minut, a zostanie on automatycznie usunięty z magazynu.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Wysyłanie raportów o kondycji przy użyciu usługi REST za pomocą żądania POST, przejdź do odpowiedniej jednostki, które mają w treści opis raportu kondycji. Na przykład, zobacz, jak wysyłać REST [klastra raportów o kondycji](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) lub [usługi raportów o kondycji](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Obsługiwane są wszystkie jednostki.

## <a name="next-steps"></a>Kolejne kroki
Na podstawie danych kondycji modułów zapisujących usługi i administratorów aplikacji/klastra można traktować sposobów, aby skorzystać z informacji. Na przykład ich można skonfigurować alerty na podstawie stanu kondycji wychwycić poważnych problemów, zanim mogą powodować awarie. Administratorzy mogą również skonfigurować systemów naprawa automatycznie rozwiązać problemy.

[Wprowadzenie do kondycji usługi Service Fabric monitorowanie](service-fabric-health-introduction.md)

[Wyświetlanie raportów o kondycji usługi Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Użytek rozwiązywania problemów z raportami kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)

