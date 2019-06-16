---
title: Partycjonowanie usługi Service Fabric | Dokumentacja firmy Microsoft
description: Opisuje sposób partycjonowania usługi Service Fabric usług stanowych. Partycje umożliwia przechowywanie danych na komputerach lokalnych danych i mocy obliczeniowej mogą być skalowane razem.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: atsenthi
ms.openlocfilehash: 833d87dab59890b9903ea8eecf2334d7dd1c7436
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711938"
---
# <a name="partition-service-fabric-reliable-services"></a>Partycjonowanie usług reliable services usługi Service Fabric
Ten artykuł zawiera wprowadzenie do podstawowych pojęć dotyczących partycjonowanie usług reliable services w usłudze Azure Service Fabric. Kodu źródłowego użytego w artykule jest dostępna również na [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Partycjonowanie
Partycjonowanie nie jest unikatowy dla usługi Service Fabric. W rzeczywistości jest wzorzec core tworzenie skalowalnych usług. W szerszym znaczeniu firma Microsoft myśleć o partycjonowanie jako koncepcji podział stanu (dane), a obliczenia na mniejsze jednostki dostępne w celu poprawy skalowalności i wydajności. Dobrze znane formą partycjonowania jest [partycjonowanie danych][wikipartition], znanego również jako dzielenia na fragmenty.

### <a name="partition-service-fabric-stateless-services"></a>Usługi bezstanowej usługi Service Fabric partycji
W przypadku usług bezstanowych należy rozważać partycji jest jednostki logicznej, która zawiera co najmniej jedno wystąpienie usługi. Rysunek 1 przedstawia usługę bezstanową z pięć wystąpień dystrybuowane w klastrze za pomocą jednej partycji.

![Usługi bezstanowej](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Istnieją dwa typy rozwiązań o bezstanowa usługa. Pierwsza z nich to usługa, która będzie się powtarzać jego stan na zewnątrz, na przykład w bazie danych Azure SQL (np. witrynę internetową, która przechowuje informacje o sesji i danych). Drugim jest tylko do obliczeń usług (np. tworzenia miniatur kalkulatora lub obraz), nie zarządzających dowolny stan trwały.

W przypadku partycjonowania usługi bezstanowej jest bardzo rzadki — skalowalności i dostępności są zazwyczaj wykonywane przez dodanie większej liczby wystąpień. Jedyną sytuacją, który chcesz wziąć pod uwagę wiele partycji dla wystąpień usługi bezstanowej jest, gdy musisz spełnić szczególne kierowania żądań.

Na przykład należy rozważyć przypadek, gdzie użytkowników za pomocą identyfikatorów w pewnym zakresie powinien być obsługiwany tylko przez utworzone wystąpienie określonej usługi. Inny przykład jeśli po utworzeniu partycji usługi bezstanowej to, gdy naprawdę partycjonowane wewnętrznej bazy danych (np. podzielonej na fragmenty bazy danych SQL), a użytkownik chce kontrolować, które wystąpienie usługi należy zapisywać do fragmentu bazy danych — lub wykonywać inne zadania przygotowywania w ramach bezstanowa usługa, która wymaga te same informacje partycjonowania, co jest używany w wewnętrznej bazie danych. Te typy scenariuszy można rozwiązać również w różny sposób i nie wymagają usługi partycjonowania.

W pozostałej części tego przewodnika koncentruje się na usług stanowych.

### <a name="partition-service-fabric-stateful-services"></a>Usługi stanowe usługi Service Fabric partycji
Usługa Service Fabric ułatwia tworzenie skalowalnych usług stanowych, udostępniając najwyższej jakości sposób stan partycji (dane). Model należy rozważać partycji usługi stanowej, jako jednostkę skalowania, która zapewnia wysoką niezawodność za pośrednictwem [replik](service-fabric-availability-services.md) są rozproszone oraz zrównoważone między węzłami w klastrze.

Partycjonowanie w kontekście usług stanowych usługi Service Fabric odnosi się do proces określania, że partycja określonej usługi jest odpowiedzialny za część pełny stan usługi. (Jak wspomniano wcześniej, partycji to zbiór [replik](service-fabric-availability-services.md)). Największą zaletą usługi Service Fabric jest umieszcza partycje, w różnych węzłach. Umożliwi to zwiększanie limitu zasobu węzła. Jak wzrostem wymagań danych partycji rozwoju i usługi Service Fabric rebalances partycji na węzłach. Dzięki temu ciągłej efektywne wykorzystanie zasobów sprzętowych.

Aby podać przykład, załóżmy, że start z klastra z 5 węzłami i to usługa, która jest skonfigurowana z 10 partycji oraz stanowią obiekty docelowe trzech replik. W takim przypadku równoważenie i dystrybucja repliki w klastrze — usługi Service Fabric i pojawiłyby się dwóch podstawowych [replik](service-fabric-availability-services.md) na węzeł.
Jeśli potrzebujesz teraz skalowanie w poziomie klaster 10 węzłów, Usługa Service Fabric będzie ponowne zrównoważenie podstawowego [replik](service-fabric-availability-services.md) we wszystkich węzłach 10. Podobnie jeśli skalowana do 5 węzłów usługi Service Fabric będzie ponowne zrównoważenie wszystkie repliki w 5 węzłach.  

Rysunek 2 przedstawia rozkład 10 partycji przed i po nim skalowanie klastra.

![Usługi stanowej](./media/service-fabric-concepts-partitioning/partitions.png)

W wyniku uzyskuje się skalowalne w poziomie, ponieważ żądania klientów są rozmieszczone na komputerach, Lepsza ogólną wydajność aplikacji i zmniejsza się rywalizacji o dostęp do dużych ilości danych.

## <a name="plan-for-partitioning"></a>Planowanie podziału na partycje
Przed wdrożeniem usługi, zawsze należy rozważyć strategii partycjonowania, który jest wymagany do skalowania w poziomie. Istnieją różne sposoby, ale wszystkie z nich skupić się na tym, co aplikacja do osiągnięcia. Dla tego kontekstu w tym artykule Rozważmy niektóre aspekty ważniejsze opcji.

To dobra metoda przemyśleć strukturę stan, który ma zostać podzielona na partycje, w pierwszym kroku.

Weźmy prosty przykład. W przypadku tworzenia usługi w ankiecie całej hrabstwa, można utworzyć w hrabstwie partycji dla każdego miasta. Następnie można przechowywać głosów dla każdej osoby w mieście w partycji, który odpowiada tym mieście. Rysunek 3 ilustruje zestaw osób i miasta, w którym się znajdują.

![Proste partycji](./media/service-fabric-concepts-partitioning/cities.png)

Jak ludności miasta wartość znacznie się zmienia, użytkownik może pozostać przy użyciu niektóre partycje, które zawierają dużą ilość danych (np. Seattle) i innych partycji z niewielkim stanu (np. Kirkland). Zatem czym jest wpływ partycji z nierównomierny ilości stanu?

Jeśli myślisz o przykład ponownie, możesz łatwo zobaczyć, że partycji, który zawiera głosy dla Seattle otrzyma więcej ruchu niż Kirkland, jeden. Domyślnie Usługa Service Fabric zapewnia się, że o takiej samej liczby podstawowych i pomocniczych replik w każdym węźle. Dlatego użytkownik może pozostać w przypadku węzłów, używane do przechowywania replik, które obsługują więcej ruchu i innym osobom, które obsługiwać mniej ruchu. Najlepiej należy unikać gorące i zimne chętnych następująco w klastrze.

Aby tego uniknąć, należy wykonać dwie czynności, z punktu widzenia partycjonowania:

* Spróbuj podzielić stanu tak, aby go jest równomiernie rozłożona na wszystkie partycje.
* Ładowanie raportu z każdej repliki dla usługi. (Aby uzyskać informacje na temat, zapoznaj się z tego artykułu na [metryki i obciążenia](service-fabric-cluster-resource-manager-metrics.md)). Usługa Service Fabric udostępnia możliwość ładowania raportów używane przez usługi, takie jak ilość pamięci lub liczbę rekordów. Na podstawie metryk zgłoszone, usługi Service Fabric wykrywa, że niektóre partycje obsługujących większych obciążeń niż inne i rebalances przenosząc replik na bardziej odpowiednie węzły klastra, tak, aby ogólna żaden węzeł nie jest przeciążona.

Czasami nie wiadomo, jak dużo danych będą znajdować się w danej partycji. Dlatego ogólnie zalecane jest, aby obie opcje — najpierw przez przyjęcie strategii partycjonowania które rozprzestrzeniają się dane równomiernie między partycjami i drugiego, przez raportowania obciążenia.  Pierwsza metoda zapobiega sytuacji, które opisano w przykładzie głosowania, a jednocześnie pomaga drugi wygładzenie tymczasowe różnice w dostępu lub obciążenia wraz z upływem czasu.

Innym aspektem planowania partycji ma rozpoczynać się wybrać poprawną liczbę partycji.
Z punktu widzenia usługi Service Fabric nie ma nic uniemożliwiający zaczynasz pracę z większej liczby partycji niż zakładano dla danego scenariusza.
W rzeczywistości zakładając, że maksymalna liczba partycji jest nieprawidłowy podejście.

W rzadkich przypadkach może być wniesienie partycje więcej niż początkowo wybrane. Jak liczba partycji nie można zmienić w późniejszym czasie, należy zastosować niektóre podejścia partycji zaawansowane, takie jak tworzenie nowego wystąpienia usługi typu usługi. Będziesz potrzebować także wdrożyć logikę po stronie klienta, która będzie przekierowywać żądania do wystąpienia prawidłowe usługi, na podstawie wiedzy po stronie klienta, który należy zachować kod klienta.

Inną ważną kwestią dotyczącą partycjonowanie planowania jest zasoby komputerowe dostępne. Ponieważ stan musi mieć dostęp i przechowywane, są powiązane z:

* Limity przepustowości sieci
* Limity pamięci systemu
* Limity przestrzeni dyskowej dysku

Dlatego co się dzieje w razie napotkania ograniczeń zasobów w działającego klastra? Odpowiedzią jest, czy użytkownik może po prostu skalować klastra w celu uwzględnienia nowych wymagań.

[Przewodnik planowania pojemności](service-fabric-capacity-planning.md) zawiera wskazówki dotyczące sposobu ustalania liczby węzłów, klaster potrzebuje.

## <a name="get-started-with-partitioning"></a>Rozpoczynanie pracy z podziałem na partycje
W tej sekcji opisano, jak rozpocząć pracę z podziałem na partycje usługi.

Usługa Service Fabric zapewnia szeroki wybór trzech schemat partycji:

* Zakres partycjonowania (zwanej także UniformInt64Partition).
* O nazwie partycjonowania. Aplikacje zazwyczaj przy użyciu tego modelu ma dane, które można bucketed w ramach ograniczonego zestawu. Typowe przykłady pola danych, używane jako klucze partycji o nazwie będzie regionów, kody pocztowe, grup odbiorców lub granice innych firm.
* Partycje pojedynczego wystąpienia. Partycje pojedynczego wystąpienia są zwykle używane, jeśli usługa nie wymaga dodatkowych routingu. Na przykład usług bezstanowych ten schemat partycjonowania domyślnie używają.

Nazwę i pojedyncze schematy partycjonowania są specjalne rodzaje ranged partycji. Domyślnie szablony programu Visual Studio do użycia usługi Service Fabric w granicach partycjonowanie, ponieważ jest to najbardziej typowe i przydatne. W dalszej części tego artykułu koncentruje się na ranged schematu partycjonowania.

### <a name="ranged-partitioning-scheme"></a>Zakres schematu partycjonowania
Służy do określania zakres liczb całkowitych (identyfikowanych na podstawie niska wartość klucza i wysoka wartość klucza) i liczbę partycji (n). Tworzy partycje n, każdego odpowiedzialny za nienakładający podzakresu ogólną zakres kluczy partycji. Na przykład ranged schematu partycjonowania kluczem niski 0, wysoka wartość klucza liczby 99 oraz liczbę 4 utworzyć cztery partycje, jak pokazano poniżej.

![Zakres, partycjonowanie](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Typowym podejściem jest tworzy skrót, w oparciu o Unikatowy klucz w zestawie danych. Typowe przykłady klucze będą numer identyfikacyjny (VIN), identyfikator pracownika lub unikatowego ciągu. Za pomocą tego unikatowego klucza, będzie następnie wygenerować wartość skrótu, modulo zakres kluczy, aby użyć jako klucza. Można określić górne i dolne granice dozwolony zakres kluczy.

### <a name="select-a-hash-algorithm"></a>Wybierz algorytm wyznaczania wartości skrótu
Ważnym elementem wyznaczania wartości skrótu jest wybór algorytm wyznaczania wartości skrótu. Jest brany pod uwagę to, czy celem jest do grupowania podobnych kluczy blisko siebie (Miejscowość poufnych wyznaczania wartości skrótu) — lub działania powinny być rozdzielone szeroko wszystkich partycji (dystrybucja Tworzenie skrótu), który jest bardziej powszechne.

Cechy dobrej algorytmu wyznaczania wartości skrótu są jest łatwy do obliczenia, ma ona kilka kolizje i dystrybuuje kluczy równomiernie. Dobrym przykładem algorytmu wyznaczania wartości skrótu w wydajny jest [FNV 1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) algorytmu wyznaczania wartości skrótu.

Dobry zasobem ogólne wyznaczania wartości skrótu kodu algorytm wyboru tych elementów jest [strony Wikipedii dla funkcji skrótu](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Tworzenie stanowej usługi z różnych partycji
Utwórzmy pierwszej niezawodne usługi stanowe z wielu partycji. W tym przykładzie utworzysz bardzo prostą aplikację, której chcesz przechowywać wszystkie ostatnie o nazwach zaczynających tę samą literę w tej samej partycji.

Przed przystąpieniem do napisania jakiegokolwiek kodu, należy wziąć pod uwagę partycji i klucze partycji. Należy o niski i wysoki klucz partycji 26, (po jednym dla każdej litery alfabetu), ale co zrobić?
Ponieważ chcemy dosłownie ma jedną partycję na literę, możemy użyć 0 jako niska wartość klucza i 25 jako wysoki klucz każdej litery jest własnego klucza.

> [!NOTE]
> Jest to uproszczony scenariuszu, ponieważ w rzeczywistości będzie nierównomierny rozkład. Nazwiska, rozpoczynając od litery "S" lub "M" jest częściej niż te, rozpoczynając od "X" lub "Y".
> 
> 

1. Otwórz **programu Visual Studio** > **pliku** > **nowe** > **projektu**.
2. W **nowy projekt** okno dialogowe, wybierz aplikację usługi Service Fabric.
3. Wywołaj projektu "AlphabetPartitions".
4. W **utworzyć usługę** okna dialogowego wybierz **stanowa** usługi, a następnie wywołaj ją "Alphabet.Processing".
5. Ustaw liczbę partycji. Otwórz plik Applicationmanifest.xml znajduje się w folderze ApplicationPackageRoot AlphabetPartitions projektu i zaktualizuj parametr Processing_PartitionCount do 26, jak pokazano poniżej.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Należy również zaktualizować właściwości LowKey i HighKey element klasy StatefulService ApplicationManifest.xml, jak pokazano poniżej.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Usługi dostępne Otwórz punkt końcowy na porcie przez dodanie punktu końcowego elementu ServiceManifest.xml (znajdujący się w folderze PackageRoot) dla usługi Alphabet.Processing, jak pokazano poniżej:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Teraz usługa jest skonfigurowana do nasłuchiwania wewnętrzny punkt końcowy z partycjami 26.
7. Następnie należy zastąpić `CreateServiceReplicaListeners()` metody klasy przetwarzania.
   
   > [!NOTE]
   > W tym przykładzie przyjęto założenie, używają HttpCommunicationListener proste. Aby uzyskać więcej informacji na temat komunikacji niezawodnej usługi, zobacz [model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Zalecany wzorzec adresu URL, który nasłuchuje repliki jest następujący format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Dlatego użytkownik chce skonfigurować z odbiornikiem komunikacji do nasłuchiwania na poprawne punkty końcowe i za pomocą tego wzorca.
   
    Wiele replik tej usługi może być obsługiwany na tym samym komputerze, więc ten adres musi być unikatowa dla repliki. Jest to, dlaczego identyfikator partycji: + identyfikator repliki są w adresie URL. HttpListener może nasłuchiwać na wiele adresów, w tym samym porcie, tak długo, jak prefiks adresu URL jest unikatowa.
   
    Nadmiarowe identyfikator GUID jest dostępna dla zaawansowanych przypadek, gdzie również nasłuchiwania żądań tylko do odczytu replikach pomocniczych. Jeśli tak jest rzeczywiście chcesz upewnij się, że nowy unikatowy adres jest używany podczas przenoszenia z podstawowej do dodatkowej Wymuś na klientach do ponownego rozpoznawania adresów. "+" jest używany jako adres, w tym miejscu, aby repliki nasłuchuje na wszystkich dostępnych hostów (adresu IP, nazwy FQDN, nazwy localhost, itp.) Poniższy kod przedstawia przykład.
   
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
   
    Warto również zauważyć, że adresie URL publikacji różni się nieco od nasłuchiwania prefiks adresu URL.
    Adres URL nasłuchiwania otrzymuje HttpListener. Opublikowane adres URL jest adres URL, który jest opublikowana usługa nazewnictwa usługi Service Fabric, który jest używany dla potrzeb odnajdowania usługi. Klienci, zostanie wyświetlony monit o tego adresu za pośrednictwem usługi odnajdywania. Adres, który klienci uzyskują musi mieć rzeczywistego adresu IP lub nazwa FQDN węzła w celu nawiązania połączenia. Dlatego należy zastąpić "+" przy użyciu adresu IP lub nazwa FQDN, jak pokazano powyżej węzła.
9. Ostatnim krokiem jest, aby dodać logikę przetwarzania do usługi, jak pokazano poniżej.
   
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
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` odczytuje wartości parametru ciągu zapytania, które są używane do wywoływania partycji i wywołania `AddUserAsync` dodać nazwisko do niezawodnego słownika `dictionary`.
10. Dodajmy bezstanowej usługi do projektu, aby zobaczyć, jak może wywołać określonej partycji.
    
    Ta usługa służy jako prosty interfejs sieci web akceptuje lastname jako parametr ciągu zapytania, który określa klucz partycji i wysyła je do usługi Alphabet.Processing do przetworzenia.
11. W **utworzyć usługę** okna dialogowego wybierz **Stateless** usługi, a następnie wywołaj ją "Alphabet.Web", jak pokazano poniżej.
    
    ![Zrzut ekranu usługi bezstanowej](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Zaktualizuj informacje punktu końcowego w ServiceManifest.xml usługi Alphabet.WebApi, aby otworzyć port, jak pokazano poniżej.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Należy zwracać kolekcją ServiceInstanceListeners w klasie sieci Web. Ponownie można zaimplementować prosty HttpCommunicationListener.
    
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
14. Teraz musisz zaimplementować logikę przetwarzania. Wywołania HttpCommunicationListener `ProcessInputRequest` gdy nadejdzie żądanie. Więc Rozpocznijmy i Dodaj poniższy kod.
    
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
    
    Przejdźmy teraz przez niego krok po kroku. Kod odczytuje pierwszą literę parametr ciągu zapytania `lastname` do znaku. Następnie określa klucza partycji dla tej litery przez odjęcie wartości szesnastkowej `A` z pierwszej litery nazwiska wartość szesnastkową.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Pamiętaj, że w tym przykładzie używamy 26 partycje z kluczem partycji co dla każdej partycji.
    Następnie możemy uzyskać partycji usługi `partition` dla tego klucza, używając `ResolveAsync` metody `servicePartitionResolver` obiektu. `servicePartitionResolver` jest zdefiniowany jako
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync` Metoda przyjmuje identyfikator URI usługi, klucz partycji i anulowania tokenu jako parametry. Usługa jest identyfikator URI usługi przetwarzania `fabric:/AlphabetPartitions/Processing`. Następnie Pobierz punkt końcowy partycji.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Na koniec adres URL punktu końcowego, a także ciąg zapytania do skompilowania i wywołać usługę przetwarzania.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Po zakończeniu przetwarzania możemy zapisywać dane wyjściowe.
15. Ostatnim krokiem jest do testowania usługi. Zastosowań aplikacji parametrach programu Visual Studio dla lokalnego i wdrażanie w chmurze. Aby przetestować usługę w 26 partycji lokalnie, musisz zaktualizować `Local.xml` plików w folderze ApplicationParameters projektu AlphabetPartitions, jak pokazano poniżej:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Po zakończeniu wdrożenia można sprawdzić usługę i wszystkie jego partycji w narzędziu Service Fabric Explorer.
    
    ![Zrzut ekranu narzędzia Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. W przeglądarce, możesz przetestować partycjonowania logiki, wprowadzając `http://localhost:8081/?lastname=somename`. Zobaczysz, że każdy nazwisko rozpoczyna się od tej samej litery są przechowywane w tej samej partycji.
    
    ![Zrzut ekranu przeglądarki](./media/service-fabric-concepts-partitioning/samplerunning.png)

Kod źródłowy całego próbki jest dostępny w [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Niezawodne usługi i aktora rozwidlenia procesy podrzędne
Usługa Service Fabric nie obsługuje usług reliable services, a następnie elementy Reliable actors, tworzenia rozwidlenia procesy podrzędne. Dlaczego nie jest obsługiwane na przykład [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nie może służyć do rejestrowania nieobsługiwanych proces podrzędny i przetwarza anulowania tokeny są wysyłane tylko do zarejestrowanych; skutkuje szerokiej gamy problemów, takich jak błędy uaktualnień, gdy procesy podrzędne nie zamykaj po otrzymaniu token anulowania dla procesu nadrzędnego. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacji na temat pojęć usługi Service Fabric zobacz następujące tematy:

* [Dostępność usługi Service Fabric](service-fabric-availability-services.md)
* [Skalowalność usługi Service Fabric](service-fabric-concepts-scalability.md)
* [Planowanie wydajności dla aplikacji usługi Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
