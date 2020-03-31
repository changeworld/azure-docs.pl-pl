---
title: Usługi partycjonowania usługi sieci szkieletowej
description: W tym artykule opisano, jak partycjonować usługi stanowe sieci szkieletowej usług. Partycje umożliwia przechowywanie danych na komputerach lokalnych, dzięki czemu dane i obliczenia mogą być skalowane razem.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 1f3ee2196bad8b8a0c992ed498d40b4cf5820f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258618"
---
# <a name="partition-service-fabric-reliable-services"></a>Partition Service Fabric reliable services (Partycjonowanie usług Reliable Services w ramach usługi Service Fabric)
Ten artykuł zawiera wprowadzenie do podstawowych pojęć partycjonowania usługi azure service fabric niezawodne. Kod źródłowy użyty w artykule jest również dostępny na [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Partycjonowanie
Partycjonowanie nie jest unikatowe dla sieci szkieletowej usług. W rzeczywistości jest to podstawowy wzorzec budowania skalowalnych usług. W szerszym znaczeniu możemy myśleć o partycjonowaniu jako koncepcji podziału stanu (danych) i obliczania na mniejsze dostępne jednostki, aby poprawić skalowalność i wydajność. Dobrze znaną formą partycjonowania jest [partycjonowanie danych,][wikipartition]znane również jako dzielenie na fragmenty.

### <a name="partition-service-fabric-stateless-services"></a>Usługi bezstanowe usługi usługi partycji
W przypadku usług bezstanowych można myśleć o partycji jako jednostki logicznej, która zawiera jedno lub więcej wystąpień usługi. Rysunek 1 przedstawia usługę bezstanową z pięciu wystąpień rozproszonych w klastrze przy użyciu jednej partycji.

![Usługa bezstanowa](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Istnieją naprawdę dwa rodzaje rozwiązań serwisowych bezstanowych. Pierwszy z nich jest usługą, która utrzymuje swój stan zewnętrznie, na przykład w bazie danych SQL platformy Azure (jak w witrynie sieci Web, która przechowuje informacje o sesji i dane). Drugi to usługi tylko do obliczeń (takie jak kalkulator lub miniatury obrazów), które nie zarządzają żadnym stanem trwałym.

W obu przypadkach partycjonowanie usługi bezstanowej jest bardzo rzadkim scenariuszem — skalowalność i dostępność są zwykle osiągane przez dodanie większej liczby wystąpień. Tylko wtedy, gdy chcesz wziąć pod uwagę wiele partycji dla wystąpień usługi bezstanowej jest, gdy trzeba spełnić specjalne żądania routingu.

Na przykład należy wziąć pod uwagę przypadek, w którym użytkownicy z identyfikatorami w określonym zakresie powinny być obsługiwane tylko przez wystąpienie określonej usługi. Innym przykładem, kiedy można podzielić usługę bezstanową jest, gdy masz prawdziwie podzielonych na partycje wewnętrznej bazy danych (np. podzielonej bazy danych SQL) i chcesz kontrolować, które wystąpienie usługi należy zapisać do niezależnego fragmentu bazy danych - lub wykonać inne prace przygotowawcze w ramach usługa bezstanowa, która wymaga tych samych informacji partycjonowania, które są używane w wewnętrznej bazy danych. Te typy scenariuszy można również rozwiązać na różne sposoby i niekoniecznie wymagają partycjonowania usługi.

Pozostała część tego przewodnika koncentruje się na usługi stanowe.

### <a name="partition-service-fabric-stateful-services"></a>Usługi stanowe usługi usługi partycji
Usługa Fabric ułatwia tworzenie skalowalnych usług stanowych, oferując pierwszorzędny sposób partycjonowania stanu (danych). Koncepcyjnie można myśleć o partycji usługi stanowej jako jednostki skalowania, która jest wysoce niezawodne za pośrednictwem replik, które są [rozproszone](service-fabric-availability-services.md) i zrównoważone w węzłach w klastrze.

Partycjonowanie w kontekście usługi Stanowe sieci szkieletowej usług usługi odwołuje się do procesu określania, że partycja określonej usługi jest odpowiedzialny za część pełnego stanu usługi. (Jak wspomniano wcześniej, partycja jest zestawem [replik](service-fabric-availability-services.md)). Wspaniałą rzeczą w sieci szkieletowej usług jest to, że umieszcza partycje na różnych węzłach. Dzięki temu mogą rosnąć do limitu zasobów węzła. W miarę wzrostu potrzeb danych partycje rosną, a usługa Fabric równoważe partycje między węzłami. Zapewnia to dalsze efektywne wykorzystanie zasobów sprzętowych.

Aby podać przykład, załóżmy, że należy rozpocząć od klastra 5 węzłów i usługi, która jest skonfigurowana do 10 partycji i docelowego trzech replik. W takim przypadku sieci szkieletowej usług będzie równoważenia i dystrybucji replik w klastrze - i skończy się z dwóch [replik](service-fabric-availability-services.md) podstawowych na węzeł.
Jeśli teraz trzeba skalować w poziomie klastra do 10 węzłów, sieci szkieletowej usług będzie równoważenia [replik](service-fabric-availability-services.md) podstawowych we wszystkich 10 węzłów. Podobnie, jeśli przeskalowane z powrotem do 5 węzłów, sieci szkieletowej usług będzie równoważenia wszystkich replik w 5 węzłów.  

Rysunek 2 przedstawia rozkład 10 partycji przed i po skalowaniu klastra.

![Służba stanowa](./media/service-fabric-concepts-partitioning/partitions.png)

W rezultacie skalowano w poziomie jest osiągane, ponieważ żądania od klientów są dystrybuowane między komputerami, ogólna wydajność aplikacji jest lepsza, a rywalizacja o dostęp do fragmentów danych jest zmniejszona.

## <a name="plan-for-partitioning"></a>Planowanie partycjonowania
Przed zaimplementowanie usługi, należy zawsze wziąć pod uwagę strategii partycjonowania, który jest wymagany do skalowania w poziomie. Istnieją różne sposoby, ale wszystkie z nich koncentrują się na tym, co aplikacja musi osiągnąć. W kontekście tego artykułu rozważmy niektóre z ważniejszych aspektów.

Dobrym podejściem jest myślenie o strukturze stanu, który musi zostać podzielony na partycje, jako pierwszy krok.

Weźmy prosty przykład. Jeśli chcesz zbudować usługę dla ankiety w całym hrabstwie, można utworzyć partycję dla każdego miasta w powiecie. Następnie można przechowywać głosy dla każdej osoby w mieście w podziale, który odpowiada temu miastu. Rysunek 3 przedstawia zbiór ludzi i miasto, w którym mieszkają.

![Prosta partycja](./media/service-fabric-concepts-partitioning/cities.png)

Ponieważ populacja miast jest bardzo zróżnicowana, możesz mieć pewne partycje, które zawierają dużo danych (np. Więc jaki jest wpływ posiadania partycji z nierównych ilości stanu?

Jeśli myślisz o przykładzie ponownie, można łatwo zobaczyć, że partycja, która posiada głosy na Seattle dostanie więcej ruchu niż Kirkland jeden. Domyślnie sieci szkieletowej usług upewnia się, że istnieje o tej samej liczbie replik podstawowych i pomocniczych w każdym węźle. Więc może skończyć się z węzłów, które przechowują repliki, które obsługują więcej ruchu i innych, które obsługują mniejszy ruch. Najlepiej byłoby uniknąć gorących i zimnych miejsc, takich jak ten w klastrze.

Aby tego uniknąć, należy wykonać dwie czynności, z punktu widzenia partycjonowania:

* Spróbuj podzielić stan tak, aby był równomiernie rozłożony na wszystkie partycje.
* Zgłoś obciążenie z każdej replik dla usługi. (Aby uzyskać informacje na temat sposobu, zapoznaj się z tym artykułem na [Metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md)). Sieci szkieletowej usług zapewnia możliwość raportowania obciążenia zużywane przez usługi, takie jak ilość pamięci lub liczba rekordów. Na podstawie zgłoszonych metryk usługa Fabric wykrywa, że niektóre partycje obsługują wyższe obciążenia niż inne i równoważy klaster, przenosząc repliki do bardziej odpowiednich węzłów, dzięki czemu ogólny węzeł nie jest przeciążony.

Czasami nie można wiedzieć, ile danych będzie w danej partycji. Tak więc ogólne zalecenie jest zrobić zarówno - po pierwsze, przyjmując strategii partycjonowania, który rozprzestrzenia dane równomiernie na partycje i po drugie, zgłaszając obciążenia.  Pierwsza metoda zapobiega sytuacji opisanych w przykładzie głosowania, podczas gdy druga pomaga wygładzić tymczasowe różnice w dostępie lub obciążeniu w czasie.

Innym aspektem planowania partycji jest wybranie odpowiedniej liczby partycji na początek.
Z punktu widzenia sieci szkieletowej usług nie ma nic, co uniemożliwia rozpoczęcie z większą liczbą partycji niż przewidywano w przypadku scenariusza.
W rzeczywistości przy założeniu, że maksymalna liczba partycji jest prawidłowym podejściem.

W rzadkich przypadkach może się okazać, że potrzebujesz więcej partycji niż początkowo wybrano. Ponieważ nie można zmienić liczbę partycji po fakcie, należy zastosować niektóre zaawansowane podejścia partycji, takie jak tworzenie nowego wystąpienia usługi tego samego typu usługi. Należy również zaimplementować niektóre logiki po stronie klienta, który kieruje żądania do wystąpienia poprawnej usługi, na podstawie wiedzy po stronie klienta, że kod klienta musi obsługiwać.

Inną kwestią dla planowania partycjonowania są dostępne zasoby komputera. Ponieważ stan musi być dostępny i przechowywane, jesteś zobowiązany do naśladowania:

* Limity przepustowości sieci
* Limity pamięci systemowej
* Limity magazynowania dysku

Co się stanie, jeśli napotkasz ograniczenia zasobów w uruchomionym klastrze? Odpowiedź jest taka, że można po prostu skalować w poziomie klastra, aby dostosować się do nowych wymagań.

[Przewodnik planowania pojemności](service-fabric-capacity-planning.md) zawiera wskazówki dotyczące określania liczby węzłów, które wymagają klastra.

## <a name="get-started-with-partitioning"></a>Wprowadzenie do partycjonowania
W tej sekcji opisano, jak rozpocząć pracę z partycjonowania usługi.

Usługa Service Fabric oferuje wybór trzech schematów partycji:

* Partycjonowanie z dystansem (inaczej znane jako UniformInt64Partition).
* Nazwane partycjonowanie. Aplikacje korzystające z tego modelu zwykle mają dane, które mogą być wiadro, w ramach ograniczonego zestawu. Niektóre typowe przykłady pól danych używanych jako nazwane klucze partycji to regiony, kody pocztowe, grupy odbiorców lub inne granice biznesowe.
* Partycjonowanie singleton. Singleton partycje są zwykle używane, gdy usługa nie wymaga żadnych dodatkowych routingu. Na przykład usługi bezstanowe domyślnie używają tego schematu partycjonowania.

Nazwane i Singleton partycjonowania schematy są specjalne formy partycji dystansowych. Domyślnie szablony programu Visual Studio dla sieci szkieletowej usług używają partycjonowania z zakresu, ponieważ jest to najbardziej typowe i przydatne. Poniżej dalsza część artykułu Koncentruje się na schemat partycjonowania zakresu.

### <a name="ranged-partitioning-scheme"></a>Schemat partycjonowania z dystansem
Służy do określania zakresu liczb całkowitych (identyfikowanych za pomocą klucza niskiego i wysokiego) oraz liczby partycji (n). Tworzy partycje n, każdy odpowiedzialny za nienakładający się podzakresu ogólnego zakresu klucza partycji. Na przykład schemat partycjonowania dystansowego z niskim kluczem 0, wysokim kluczem 99 i liczbą 4 utworzy cztery partycje, jak pokazano poniżej.

![Partycjonowanie zakresu](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Typowym podejściem jest utworzenie skrótu na podstawie unikatowego klucza w zestawie danych. Niektóre typowe przykłady kluczy to numer identyfikacyjny pojazdu (VIN), identyfikator pracownika lub unikatowy ciąg znaków. Za pomocą tego unikatowego klucza, należy następnie wygenerować kod skrótu, moduł zakres klucza, aby użyć jako klucz. Można określić górną i dolną granicę dozwolonego zakresu kluczy.

### <a name="select-a-hash-algorithm"></a>Wybieranie algorytmu mieszania
Ważną częścią mieszania jest wybór algorytmu mieszania. Uwagę należy rozważyć, czy celem jest grupowanie podobnych kluczy blisko siebie (lokalizacja zależne hashing)-- lub jeśli działanie powinno być rozłożone szeroko na wszystkie partycje (mieszanie dystrybucji), który jest bardziej powszechne.

Charakterystyka dobrego algorytmu mieszania dystrybucji jest to, że jest łatwy do obliczenia, ma kilka kolizji i równomiernie rozprowadza klucze. Dobrym przykładem wydajnego algorytmu mieszania jest algorytm mieszania [FNV-1.](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)

Dobrym źródłem ogólnych opcji algorytmu kodu skrótu jest [strona Wikipedii dotycząca funkcji mieszania.](https://en.wikipedia.org/wiki/Hash_function)

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Tworzenie usługi stanowej z wieloma partycjami
Utwórzmy pierwszą niezawodną usługę stanową z wieloma partycjami. W tym przykładzie utworzą bardzo prostą aplikację, w której chcesz przechowywać wszystkie nazwiska, które zaczynają się od tej samej litery w tej samej partycji.

Przed napisaniem kodu należy pomyśleć o partycjach i kluczach partycji. Potrzebujesz 26 partycji (po jednej dla każdej litery w alfabecie), ale co z niskimi i wysokimi klawiszami?
Ponieważ dosłownie chcemy mieć jedną partycję na literę, możemy użyć 0 jako niskiego klucza i 25 jako wysokiego klawisza, ponieważ każda litera jest własnym kluczem.

> [!NOTE]
> Jest to uproszczony scenariusz, ponieważ w rzeczywistości dystrybucja byłaby nierówna. Nazwiska zaczynające się od liter "S" lub "M" są bardziej powszechne niż te zaczynające się od "X" lub "Y".
> 
> 

1. Otwórz nowy**projekt****pliku programu** > **New** >  **Visual Studio** > .
2. W oknie dialogowym **Nowy projekt** wybierz aplikację sieci szkieletowej usług.
3. Zadzwoń do projektu "AlphabetPartitions".
4. W oknie **dialogowym Tworzenie usługi** wybierz pozycję **Usługa stanowa** i nazwij ją "Alphabet.Processing".
5. Ustaw liczbę partycji. Otwórz plik Applicationmanifest.xml znajdujący się w folderze ApplicationPackageRoot projektu AlphabetPartitions i zaktualizuj parametr Processing_PartitionCount do 26, jak pokazano poniżej.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Należy również zaktualizować LowKey i HighKey właściwości StatefulService elementu w ApplicationManifest.xml, jak pokazano poniżej.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Aby usługa była dostępna, otwórz punkt końcowy na porcie, dodając element punktu końcowego ServiceManifest.xml (znajdujący się w folderze PackageRoot) dla usługi Alphabet.Processing, jak pokazano poniżej:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Teraz usługa jest skonfigurowana do nasłuchiwać wewnętrznego punktu końcowego z 26 partycjami.
7. Następnie należy zastąpić `CreateServiceReplicaListeners()` metodę Processing klasy.
   
   > [!NOTE]
   > W tym przykładzie zakładamy, że używasz prostego HttpCommunicationListener. Aby uzyskać więcej informacji na temat niezawodnej komunikacji serwisowej, zobacz [Model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Zalecany wzorzec adresu URL, na który nasłuchuje replika, jest następujący format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Dlatego chcesz skonfigurować odbiornik komunikacji, aby nasłuchiwać na poprawne punkty końcowe i z tego wzorca.
   
    Wiele replik tej usługi może być hostowanych na tym samym komputerze, więc ten adres musi być unikatowy dla repliki. Dlatego identyfikator partycji + identyfikator repliki znajdują się w adresie URL. HttpListener może słuchać na wielu adresach na tym samym porcie, o ile prefiks adresu URL jest unikatowy.
   
    Dodatkowy identyfikator GUID jest tam dla zaawansowanego przypadku, w którym repliki pomocnicze również nasłuchują żądań tylko do odczytu. W takim przypadku chcesz upewnić się, że nowy unikatowy adres jest używany podczas przechodzenia z podstawowego do pomocniczego, aby zmusić klientów do ponownego rozpoznania adresu. "+" jest używany jako adres tutaj tak, że replika nasłuchuje na wszystkich dostępnych hostów (IP, FQDN, localhost, itp.) Poniższy kod przedstawia przykład.
   
    ```csharp
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
   
    Warto również zauważyć, że opublikowany adres URL różni się nieco od prefiksu adresu URL nasłuchiwania.
    Adres URL słuchania jest podany do HttpListener. Opublikowany adres URL to adres URL publikowany w usłudze nazewnictwa sieci szkieletowej usług, która jest używana do odnajdowania usług. Klienci będą prosić o ten adres za pośrednictwem tej usługi odnajdywania. Adres, który otrzymują klienci, musi mieć rzeczywisty adres IP lub FQDN węzła, aby nawiązać połączenie. Musisz więc zastąpić "+" adresem IP lub FQDN węzła, jak pokazano powyżej.
9. Ostatnim krokiem jest dodanie logiki przetwarzania do usługi, jak pokazano poniżej.
   
    ```csharp
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
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`odczytuje wartości parametru ciągu zapytania używanego `AddUserAsync` do wywoływania partycji i wywołuje `dictionary`dodanie nazwy lastname do niezawodnego słownika .
10. Dodajmy usługę bezstanową do projektu, aby zobaczyć, jak można wywołać określoną partycję.
    
    Ta usługa służy jako prosty interfejs sieci web, który akceptuje lastname jako parametr ciągu zapytania, określa klucz partycji i wysyła go do usługi Alphabet.Processing do przetwarzania.
11. W oknie **dialogowym Tworzenie usługi** wybierz pozycję **Usługa bezstanowa** i nazwij ją "Alphabet.Web", jak pokazano poniżej.
    
    ![Zrzut ekranu przedstawiający usługę bezstanową](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Zaktualizuj informacje o punkcie końcowym w servicemanifest.xml usługi Alphabet.WebApi, aby otworzyć port, jak pokazano poniżej.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Należy zwrócić kolekcję ServiceInstanceListeners w klasie sieci Web. Ponownie, można wybrać do zaimplementowania prostego HttpCommunicationListener.
    
    ```csharp
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
14. Teraz musisz zaimplementować logikę przetwarzania. Wywołanie `ProcessInputRequest` httpcommunicationListener, gdy pojawi się żądanie. Więc chodźmy dalej i dodać kod poniżej.
    
    ```csharp
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
    
    Przejdźmy przez to krok po kroku. Kod odczytuje pierwszą literę `lastname` parametru ciągu zapytania do char. Następnie określa klucz partycji dla tej litery, odejmując wartość `A` szesnastkową od wartości szesnastkowej pierwszej litery imion.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Pamiętaj, w tym przykładzie używamy 26 partycji z jednym kluczem partycji na partycję.
    Następnie uzyskujemy partycję `partition` usługi dla tego `ResolveAsync` klucza `servicePartitionResolver` przy użyciu metody na obiekcie. `servicePartitionResolver`jest definiowany jako
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    Metoda `ResolveAsync` przyjmuje identyfikator URI usługi, klucz partycji i token anulowania jako parametry. Identyfikator URI usługi dla `fabric:/AlphabetPartitions/Processing`usługi przetwarzania jest . Następnie otrzymujemy punkt końcowy partycji.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Na koniec tworzymy adres URL punktu końcowego oraz querystring i wywołają usługę przetwarzania.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po zakończeniu przetwarzania zapisujemy dane wyjściowe z powrotem.
15. Ostatnim krokiem jest przetestowanie usługi. Visual Studio używa parametrów aplikacji do wdrażania lokalnego i chmury. Aby przetestować usługę z 26 partycjami lokalnie, należy zaktualizować `Local.xml` plik w folderze ApplicationParameters projektu AlphabetPartitions, jak pokazano poniżej:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po zakończeniu wdrażania można sprawdzić usługę i wszystkie jej partycje w Eksploratorze sieci szkieletowej usług.
    
    ![Explorer sieci szkieletowej usług zrzut ekranu](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. W przeglądarce można przetestować logikę partycjonowania, wprowadzając `http://localhost:8081/?lastname=somename`. Zobaczysz, że każde nazwisko, które zaczyna się od tej samej litery, jest przechowywane w tej samej partycji.
    
    ![Zrzut ekranu przeglądarki](./media/service-fabric-concepts-partitioning/samplerunning.png)

Cały kod źródłowy próbki jest dostępny w [usłudze GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Niezawodne usługi i podprocesy rozwidlenia aktora
Sieci szkieletowej usług nie obsługuje niezawodnych usług, a następnie niezawodne podmioty rozwidlenia podprocesów. Przykładem dlaczego jego nie jest obsługiwany jest [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nie może służyć do rejestracji nieobsługiconych podprocesów i tokeny anulowania są wysyłane tylko do zarejestrowanych procesów; powoduje różnego rodzaju problemy, takie jak błędy uaktualniania, gdy podprocesy nie zamykają się po odebraniu tokenu anulowania przez proces nadrzędny. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat koncepcji sieci szkieletowej usług, zobacz następujące elementy:

* [Dostępność usług sieci szkieletowej usług](service-fabric-availability-services.md)
* [Skalowalność usług sieci szkieletowej usług](service-fabric-concepts-scalability.md)
* [Planowanie pojemności dla aplikacji sieci szkieletowej usług](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
