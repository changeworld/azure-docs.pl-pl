---
title: Partycjonowanie usługi sieć szkieletowa usług | Dokumentacja firmy Microsoft
description: Opisuje sposób partycji usługi sieć szkieletowa usług stanowych. Partycje umożliwia przechowywanie danych na komputerach lokalnych, danych i zasobów obliczeniowych mogą być skalowane razem.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: msfussell
ms.openlocfilehash: bc6f25c7a8a779d949fbd09f9a9a9a37ec83f56a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206537"
---
# <a name="partition-service-fabric-reliable-services"></a>Niezawodne usługi Service Fabric partycji
Ten artykuł zawiera wprowadzenie do podstawowych pojęć dotyczących partycjonowania niezawodne usługi sieć szkieletowa usług Azure. Kod źródłowy, używany w artykule jest również dostępna w [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Partycjonowanie
Partycjonowanie nie jest unikatowy dla sieci szkieletowej usług. W rzeczywistości jest wzorzec core tworzenie skalowalnych usług. W szerszym znaczeniu możemy pomyśleć o partycjonowania jako koncepcji podziału stanu (dane) i obliczeniowe na mniejsze dostępne jednostki poprawiające skalowalność i wydajność. Dobrze znane formularz partycjonowania [partycjonowanie danych][wikipartition], znanej również jako dzielenia na fragmenty.

### <a name="partition-service-fabric-stateless-services"></a>Usługi bezstanowej partycji sieci szkieletowej usług
W przypadku usług bezstanowych należy zwrócić uwagę partycji trwa jednostki logicznej, która zawiera co najmniej jedno wystąpienie usługi. Rysunek 1 pokazuje usługi bezstanowej wystąpieniami pięć dystrybuowana do klastra przy użyciu jednej partycji.

![Usługi bezstanowej](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Naprawdę są dwa typy rozwiązań usług bezstanowych. Pierwsza z nich jest usługa, która będzie się powtarzał stanu zewnętrznie, na przykład w bazie danych Azure SQL (np. witryny internetowej, która przechowuje informacje o sesji i danych). Drugim jest tylko do obliczenia usług (takich jak obraz lub Kalkulator tworzenie miniatur) nie zarządzających każdy stan trwały.

W przypadku partycjonowania usługi bezstanowej jest bardzo rzadko scenariusza — skalowalność i dostępność zwykle są osiągane przez dodanie więcej wystąpień. Tylko wtedy, aby wziąć pod uwagę wiele partycji dla wystąpień usługi bezstanowej jest, gdy trzeba spełniać specjalne routingu żądań.

Na przykład należy wziąć pod uwagę przypadek, w którym użytkownicy z identyfikatorami w pewnym powinien być obsługiwany tylko przez wystąpienie określonej usługi. Innym przykładem po można partycji usługi bezstanowej jest, gdy masz naprawdę partycjonowanej wewnętrznej bazy danych (np. podzielony na niezależne fragmenty bazy danych SQL), a użytkownik chce kontrolować, które wystąpienie usługi należy zapisać niezależnych bazy danych — lub wykonywać inne zadania przygotowywania w ramach usługi bezstanowej, który wymaga tych samych informacji partycjonowania, ponieważ jest używany w wewnętrznej bazie danych. Tego typu scenariusze można także rozwiązać na różne sposoby i nie wymagają partycjonowania usługi.

W pozostałej części tego przewodnika koncentruje się na usług stanowych.

### <a name="partition-service-fabric-stateful-services"></a>Usługi stanowej partycji sieci szkieletowej usług
Sieć szkieletowa usług ułatwia tworzenie skalowalnych usług stanowych oferując najwyższej jakości sposobem stanu partycji (dane). Koncepcyjnie, należy zwrócić uwagę partycji usługi stanowej jako jednostkę skalowania, która jest wysoce niezawodne za pośrednictwem [replik](service-fabric-availability-services.md) rozproszonych, się równoważone między węzłami w klastrze.

Podział na partycje w kontekście usługi sieć szkieletowa usług stanowych odwołuje się do proces określania, czy partycja określonej usługi jest odpowiedzialny za część pełny stan usługi. (Jak wspomniano wcześniej, partycji jest zestawem [replik](service-fabric-availability-services.md)). Największą zaletą sieci szkieletowej usług jest umieszczenie partycji, w różnych węzłach. Umożliwia im to Zwiększ limit zasobów węzła. Dane wymogów, partycje wzrostu i sieci szkieletowej usług rebalances partycji w węzłach. Dzięki temu ciągłego efektywne wykorzystanie zasobów sprzętowych.

Aby podać przykład, załóżmy, że rozpoczyna 5 węzłów klastra i usługi, który skonfigurowano do 10 partycji i elementem docelowym trzy repliki. W takim przypadku usługi sieć szkieletowa może równoważyć i rozpowszechniają replik klastra — i pojawiłyby się dwóch podstawowych [replik](service-fabric-availability-services.md) na węzeł.
Teraz należy do skalowania w poziomie do 10 węzłów klastra usługi sieć szkieletowa będzie ponowne zrównoważenie podstawowej [replik](service-fabric-availability-services.md) we wszystkich węzłach 10. Podobnie w przypadku zmiany skali do 5 węzłów sieci szkieletowej usług będzie ponowne zrównoważenie wszystkich replik w węzłach 5.  

Rysunek 2 przedstawia rozkład 10 partycji przed i po skalowanie klastra.

![Usługi stanowej](./media/service-fabric-concepts-partitioning/partitions.png)

W związku z tym uzyskuje się skalowalne w poziomie, ponieważ żądania klientów są rozproszone na komputerach, poprawia ogólną wydajność aplikacji i Rywalizacja o dostęp do dużych ilości danych, zostanie zmniejszona.

## <a name="plan-for-partitioning"></a>Planowanie podziału na partycje
Przed wdrożeniem usługi, zawsze należy rozważyć strategii partycjonowania, który jest wymagany do skalowania w poziomie. Istnieją różne sposoby, ale wszystkie z nich skupić się na aplikacji należy osiągnąć. Dla kontekstu tego artykułu teraz należy wziąć pod uwagę niektórych aspektów większe znaczenie.

Dobre rozwiązanie jest myśleć o strukturze stanu, który musi zostać podzielona na partycje, co w pierwszym kroku.

Spójrzmy prosty przykład. W przypadku tworzenia usługi dla countywide sondowania można utworzyć partycji każdemu miastu w województwo. Następnie można przechowywać głosów dla każdej osoby w mieście w partycji, która odpowiada tego miasta. Rysunek 3 przedstawia zestaw osób i Miasto, w którym znajdują się.

![Proste partycji](./media/service-fabric-concepts-partitioning/cities.png)

Jako różni populacji miast, może to spowodować niektóre partycje, które zawierają dużą ilość danych (np. Seattle) i innych partycji z bardzo mało stanu (np. Kirkland). Dlatego co to jest wpływ partycji z nierówna ilości stan?

Jeśli myślisz o przykładzie ponownie, można łatwo zobaczyć, czy partycji, która przechowuje głosów dla Seattle otrzymają więcej ruchu niż Kirkland, jeden. Domyślnie sieci szkieletowej usług upewnia się, że istnieje o takiej samej liczby podstawowych i pomocniczych replik w każdym węźle. Dlatego użytkownik może pozostać z węzłami zawierających repliki obsługujących więcej ruchu oraz inne osoby, które obsługiwać mniej ruchu. Najlepiej będzie chce się uniknąć gorącego i zimnych miejsc podobny do tego klastra.

Aby tego uniknąć, należy wykonać dwie czynności z punktu widzenia partycjonowania:

* Spróbuj stan partycji, dzięki czemu jest rozmieszczana równomiernie wzdłuż wszystkie partycje.
* Załaduj raport z każdej repliki usługi. (Aby uzyskać informacje na temat, zapoznaj się w tym artykule na [metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md)). Sieć szkieletowa usług oferuje możliwość raportu obciążenia wykorzystanych w ramach usług, takich jak ilość pamięci lub liczbę rekordów. Na podstawie metryk zgłoszone, sieci szkieletowej usług wykryje, że niektóre partycje służy obciążeń wyższe niż inne i rebalances klastra przenosząc replik odpowiedniejsze węzłów, dzięki czemu ogólne żaden węzeł nie jest przeciążona.

Czasami nie wiedzieć, jak dużo danych będzie w danej partycji. Dlatego obu — ogólne zalecenia najpierw, przyjmując strategii partycjonowania które rozprzestrzeniają się dane równomiernie na partycje i sekundę, przez raportowania obciążenia.  Pierwsza metoda zapobiega sytuacji opisanych w tym przykładzie głosu podczas drugiego pomaga smooth różnic tymczasowego dostępu lub obciążenia wraz z upływem czasu.

Innym aspektem planowania partycji ma rozpoczynać się od znaku wybierz poprawną liczbę partycji.
Z punktu widzenia usługi sieć szkieletowa nie ma nic uniemożliwiający uruchamiania o większej liczby partycji niż zakładano dla danego scenariusza.
W rzeczywistości przy założeniu maksymalną liczbę partycji jest prawidłowy podejście.

W rzadkich przypadkach może to spowodować wymagające partycje więcej niż początkowo wybrana. Jak liczba partycji nie można zmienić po fakcie, należy zastosować niektórych metod partycji zaawansowanych, takich jak tworzenie nowego wystąpienia usługi typu usługi. Należy również wdrożyć logikę niektórych po stronie klienta, który przekierowuje żądania do wystąpienia usługi poprawne, na podstawie wiedzy po stronie klienta, który musi obsługiwać kodu klienta.

Inną ważną kwestią dotyczącą partycjonowania planowania jest zasoby dostępne komputera. Jako stan musi mieć dostęp i przechowywane, są powiązane należy wykonać:

* Limity przepustowości sieci
* Limity pamięci systemu
* Limity magazynu na dysku

Dlatego co się stanie po uruchomieniu na ograniczenia zasobów w klastrze uruchomione? Odpowiedzią jest, że możesz po prostu skalować w poziomie klaster, aby uwzględnić nowe wymagania.

[Przewodnik planowania pojemności](service-fabric-capacity-planning.md) zawiera wskazówki dotyczące sposobu określania liczby węzłów klastra musi.

## <a name="get-started-with-partitioning"></a>Wprowadzenie do partycjonowania
W tej sekcji opisano, jak rozpocząć pracę z usługą partycjonowania.

Sieć szkieletowa usług oferuje możliwość wyboru trzy schemat partycji:

* W granicach partycjonowanie (znany także jako UniformInt64Partition).
* O nazwie partycjonowania. Aplikacje zazwyczaj przy użyciu tego modelu istnieją dane, które można bucketed w ramach ograniczonego zestawu. Typowe przykłady pola danych używane jako klucze partycji o nazwie będzie regionów, kodów pocztowych, grupy odbiorców lub granice innych firm.
* Pojedyncze partycjonowania. Pojedyncze partycje są zazwyczaj używane do usługi nie wymaga żadnych dodatkowych routingu. Na przykład usług bezstanowych Użyj ten schemat partycjonowania domyślnie.

O nazwie i schematy partycjonowania Singleton to specjalne rodzaje ranged partycji. Domyślnie szablony Visual Studio do użytku w sieci szkieletowej usług w granicach partycje, ponieważ jest to najbardziej typowe i przydatne. W dalszej części tego artykułu koncentruje się na ranged schemat partycjonowania.

### <a name="ranged-partitioning-scheme"></a>Schemat partycjonowania w granicach
Służy do określania liczbą całkowitą (określone przez niska wartość klucza i wysoka wartość klucza) i liczba partycji (n). Tworzy partycje n, każdy odpowiedzialny za Podzakres nienakładający całego zakresu klucza partycji. Na przykład ranged schemat partycjonowania kluczem niski 0, wysoka wartość klucza 99 oraz liczbę 4 utworzyć cztery partycje, jak pokazano poniżej.

![Zakres partycji](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Typowym podejściem jest tworzenie skrótów, oparte na unikatowy klucz w zestawie danych. Typowe przykłady klucze będą numer identyfikacyjny (VIN), identyfikator pracownika i unikatowym ciągiem. Za pomocą tego Unikatowy klucz, czy następnie wygenerować skrótu, modulo klucza zakresu, można użyć jako klucza. Można określić górne i dolne granice klawiszy dozwolonego zakresu.

### <a name="select-a-hash-algorithm"></a>Wybierz algorytm wyznaczania wartości skrótu
Ważnym elementem wyznaczania wartości skrótu jest wybranie sieci algorytmu wyznaczania wartości skrótu. To, czy celem jest do grupowania podobnych kluczy obok siebie (miejscowości poufnych wyznaczania wartości skrótu) — lub jeśli działanie powinien zostać przekazany szeroko wszystkich partycji (dystrybucji mieszania), która jest najczęściej.

Właściwości dobrej algorytmu wyznaczania wartości skrótu są łatwo obliczeniowe, składa się z kilku kolizji i rozpowszechnia klucze równomiernie. Dobrym przykładem algorytmu wyznaczania wartości skrótu wydajne [FNV 1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) algorytmu wyznaczania wartości skrótu.

Jest dobrym zasobu dla skrótu ogólne kod algorytmu opcji [Wikipedia strony na funkcje skrótu](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Tworzenie usługi stanowej z wieloma partycjami
Utwórz pierwszy niezawodnej usługi stanowej z wieloma partycjami. W tym przykładzie utworzysz bardzo prostą aplikację, której chcesz przechowywać wszystkich nazwisk rozpoczynających się od tej samej litery w tej samej partycji.

Przed przystąpieniem do napisania kodu, należy zastanowić, partycji i kluczy partycji. Potrzebne są 26 partycje (po jednym dla każdej litery alfabetu), ale co o niski i wysoki klucz?
Ponieważ dosłownie chcemy mieć jedną partycję na literę, możemy użyć 0 jako niska wartość klucza i 25 jako wysoka wartość klucza, jak litera każdego jest własnego klucza.

> [!NOTE]
> To jest uproszczone scenariusz, w rzeczywistości byłoby nierównomierny rozkład. Nazwisko, począwszy od litery "S" lub "M" są częściej niż począwszy od "X" lub "Y".
> 
> 

1. Otwórz **programu Visual Studio** > **pliku** > **nowe** > **projektu**.
2. W **nowy projekt** oknie dialogowym Wybierz aplikacji sieci szkieletowej usług.
3. Wywołanie "AlphabetPartitions" projektu.
4. W **Tworzenie usługi** oknie dialogowym wybierz **Stateful** usługi i wywołać go "Alphabet.Processing", jak pokazano na poniższej ilustracji.
       ![Okno dialogowe nowej usługi w programie Visual Studio][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. Ustaw liczbę partycji. Otwórz pliku Applicationmanifest.xml znajdujące się w folderze ApplicationPackageRoot AlphabetPartitions projektu i zaktualizuj parametru Processing_PartitionCount 26, jak pokazano poniżej.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Należy również zaktualizować właściwości LowKey i HighKey klasy StatefulService elementu w pliku ApplicationManifest.xml, jak pokazano poniżej.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Usługi jest niedostępna otwarcie punktu końcowego na porcie, dodając element punktu końcowego w pliku ServiceManifest.xml (znajdujące się w folderze elementu PackageRoot) dla usługi Alphabet.Processing, jak pokazano poniżej:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Obecnie usługa jest skonfigurowana do nasłuchiwania na wewnętrzny punkt końcowy z partycjami 26.
7. Następnie należy zastąpić `CreateServiceReplicaListeners()` metody klasy przetwarzania.
   
   > [!NOTE]
   > Dla tego przykładu przyjęto założenie, używasz HttpCommunicationListener proste. Aby uzyskać więcej informacji dotyczących komunikacji niezawodnej usługi, zobacz [model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Zalecane wzorca adresu URL, który nasłuchuje repliki jest następujący format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Dlatego użytkownik chce skonfigurować Twoje odbiornik komunikacji do nasłuchiwania na poprawną punktów końcowych i z tego wzorca.
   
    Wiele replik tej usługi może być obsługiwany na tym samym komputerze, dlatego ten adres musi być unikatowa dla repliki. Jest to, dlaczego identyfikator partycji: + identyfikator repliki są w adresie URL. HttpListener może nasłuchiwać na wielu adresów w tym samym porcie, tak długo, jak prefiksu adresu URL jest unikatowa.
   
    Nadmiarowe identyfikator GUID jest przypadku zaawansowane gdzie również nasłuchiwanie żądań tylko do odczytu replikach pomocniczych. W przypadku, która ma być upewnij się, że nowy unikatowy adres jest używany podczas przejścia z podstawowej do dodatkowej wymuszenie klientom ponownie rozpoznania adresu. "+" jest używany jako adres, w tym miejscu, aby funkcja replica nasłuchuje na wszystkich dostępnych hostów (IP, FQDM, localhost itp.) Poniższy kod przedstawia przykład.
   
    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Warto również zauważyć, że adres URL opublikowanej jest nieco inne niż nasłuchiwania prefiksu adresu URL.
    Adres URL nasłuchiwania otrzymuje HttpListener. Adres URL opublikowanej jest adres URL, który jest opublikowany w Usługa nazewnictwa sieci szkieletowej usług, który służy do odnajdywania usługi. Ten adres za pomocą usługi odnajdywania poprosi klientów. Adres, którego klienci uzyskują musi mieć rzeczywisty adres IP lub nazwę FQDN węzła, aby można było połączyć. Dlatego należy zastąpić "+" z węzła adres IP lub nazwę FQDN, jak pokazano powyżej.
9. Ostatnim krokiem jest dodanie logiki przetwarzania do usługi, jak pokazano poniżej.
   
    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "sucessfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` odczytuje wartości parametru ciągu zapytania, które są używane do wywoływania partycji i wywołania `AddUserAsync` można dodać nazwisko do słownika niezawodnej `dictionary`.
10. Dodajmy usługi bezstanowej do projektu, aby zobaczyć, jak może wywołać określonej partycji.
    
    Ta usługa służy jako prosty interfejs sieci web akceptuje lastname, jako parametr ciągu zapytania, określa klucz partycji i wysyła je z usługą Alphabet.Processing do przetworzenia.
11. W **Tworzenie usługi** oknie dialogowym wybierz **Stateless** usługi i nadaj mu "Alphabet.Web", jak pokazano poniżej.
    
    ![Zrzut ekranu usługi bezstanowej](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Zaktualizuj informacje punktu końcowego w pliku ServiceManifest.xml usługi Alphabet.WebApi do otwarcia portu, jak pokazano poniżej.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Należy zwracać kolekcji ServiceInstanceListeners w klasie sieci Web. Ponownie można zaimplementować HttpCommunicationListener proste.
    
    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Teraz należy wdrożyć logikę przetwarzania. Wywołania HttpCommunicationListener `ProcessInputRequest` gdy nadejdzie żądanie. Dlatego spróbuj teraz i Dodaj poniższy kod.
    
    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Przejdźmy krok po kroku. Ten kod odczytuje pierwszą literę parametru ciągu zapytania `lastname` na wartość typu char. Następnie określa klucz partycji na list przez odjęcie wartości szesnastkowej `A` z wartość szesnastkową nazwisk pierwszą literę.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Pamiętaj, że w tym przykładzie używamy 26 partycje z jedną partycję kluczem dla każdej partycji.
    Następnie możemy uzyskać partycji usługi `partition` dla tego klucza przy użyciu `ResolveAsync` metoda `servicePartitionResolver` obiektu. `servicePartitionResolver` nie zdefiniowano jako
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync` Metoda ma identyfikator URI usługi, klucz partycji i anulowania token jako parametry. Usługa jest identyfikator URI usługi przetwarzania `fabric:/AlphabetPartitions/Processing`. Następnie Pobierz punktu końcowego partycji.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Na koniec możemy zbudować adresu URL punktu końcowego plus ciąg zapytania i wywołać usługę przetwarzania.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po zakończeniu przetwarzania możemy zapisywać dane wyjściowe.
15. Ostatnim krokiem jest testować usługę. Visual Studio korzysta parametry aplikacji dla lokalnych i chmurze wdrożenia. Aby przetestować usługę z partycjami 26 lokalnie, musisz zaktualizować `Local.xml` plików w folderze ApplicationParameters projektu AlphabetPartitions, jak pokazano poniżej:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po zakończeniu wdrażania możesz sprawdzić usługi i wszystkie jego partycji w narzędziu Service Fabric Explorer.
    
    ![Zrzut ekranu Eksploratora usługi sieć szkieletowa](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. W przeglądarce, wprowadzając można przetestować partycjonowania logiki `http://localhost:8081/?lastname=somename`. Zobaczysz, że każdy nazwisko, która rozpoczyna się od tej samej litery są przechowywane w tej samej partycji.
    
    ![Zrzut ekranu przeglądarki](./media/service-fabric-concepts-partitioning/samplerunning.png)

Kod źródłowy całej próbki jest dostępna w [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>Kolejne kroki
Dla informacji na temat pojęć sieci szkieletowej usług zobacz następujące tematy:

* [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
* [Skalowalność usługi sieci szkieletowej usług](service-fabric-concepts-scalability.md)
* [Planowanie wydajności dla aplikacji sieci szkieletowej usług](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png