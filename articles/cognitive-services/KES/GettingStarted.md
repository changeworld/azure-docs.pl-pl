---
title: Rozpoczynanie pracy z usługą eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Użyj wiedzy eksploracji usługi (KES) można utworzyć aparatu wyszukiwania interakcyjne wystąpić między academic publikacji w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 02dc9368eef02d6fa507335ef3171e923412acca
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347625"
---
<a name="getting-started"></a>
# <a name="get-started-with-the-knowledge-exploration-service"></a>Rozpoczynanie pracy z usługą eksploracji wiedzy
W tym przewodniku usługi eksploracji wiedzy (KES) służy do tworzenia aparatu dla środowiska wyszukiwania interakcyjne academic publikacji. Narzędzia wiersza polecenia można zainstalować [ `kes.exe` ](CommandLine.md)i wszystkich plików w przykładzie z [zestawu SDK usług eksploracji wiedzy](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Przykład academic publikacji zawiera przykładowe 1000 dokumentów academic opublikowanych przez pracowników naukowo-badawczych firmy Microsoft.  Każdy dokument jest skojarzona z tytułu, roku publikacji, autorzy i słów kluczowych. Autor każdego jest reprezentowany przez identyfikator, nazwę i przynależności w momencie publikacji. Każde słowo kluczowe może być skojarzony z zestawem synonimy (na przykład słowa kluczowego "Obsługa wektor maszyny" może być skojarzony z synonim "svm").

<a name="defining-schema"></a>
## <a name="define-the-schema"></a>Zdefiniuj schematu
Schemat opisuje Struktura atrybutu obiektów w domenie. Określa nazwę i typ danych dla każdego atrybutu w formacie JSON. Poniższy przykład znajduje się odpowiednia zawartość pliku *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

W tym miejscu Zdefiniuj *tytuł*, *roku*, i *— słowo kluczowe* jako ciąg, liczba całkowita i ciąg atrybutu, odpowiednio. Ponieważ autorów są reprezentowane przez identyfikator, nazwę i przynależności, zdefiniuj *autora* jako atrybut złożonego z trzech atrybutów podrzędne: *Author.Id*, *Author.Name*, i *Author.Affiliation*.

Domyślnie atrybutów obsługuje wszystkie operacje dostępne dla jego typu danych, w tym *jest równe*, *starts_with*, i *is_between*. Ponieważ identyfikator autora jest używana tylko wewnętrznie jako identyfikator, zastąpić domyślną i określ *jest równe* jako jedynym indeksowane operacji.

Dla *— słowo kluczowe* atrybutu, a także zezwalanie synonimy do dopasowania wartości kanonicznej — słowo kluczowe, określając pliku synonim *Keyword.syn* w definicji atrybutu. Ten plik zawiera listę kanonicznej i pary wartości synonim:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Aby uzyskać dodatkowe informacje o definicji schematu, zobacz [Format schematu](SchemaFormat.md).

<a name="generating-data"></a>
## <a name="generate-data"></a>Generowanie danych
Plik danych w tym artykule opisano listę publikacji do indeksu dla każdego wiersza określania wartości atrybutów papieru w [formatu JSON](http://json.org/).  Poniższy przykład jest jednym wierszu z pliku danych *Academic.data*, sformatowany dla czytelności:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

W tym fragmencie, należy określić *tytuł* i *roku* atrybutu papieru jako ciągu JSON i numeru, odpowiednio. Wiele wartości są reprezentowane przez użycie tablic JSON. Ponieważ *autora* jest atrybutem złożonego, każda wartość jest reprezentowany przez obiekt JSON składające się z jego atrybuty podrzędne. Atrybuty z brakujących wartości, takich jak *— słowo kluczowe* w takim przypadku można wykluczyć z reprezentacja JSON.

Rozróżnianie prawdopodobieństwo różnych dokumentów, określania prawdopodobieństwa względną dziennika przy użyciu wbudowanych *logprob* atrybutu. Podane prawdopodobieństwo *p* pomiędzy 0 a 1 obliczeniowe prawdopodobieństwo dziennika logowania (*p*), gdzie log() jest funkcja logarytm naturalny.

Aby uzyskać więcej informacji, zobacz [Format danych](DataFormat.md).

<a name="building-index"></a>
## <a name="build-a-compressed-binary-index"></a>Tworzenie indeksu skompresowanego binarne
Po umieszczeniu pliku schematu i plik danych, można utworzyć skompresowany Indeks binarny obiektów danych przy użyciu [ `kes.exe build_index` ](CommandLine.md#build_index-command). W tym przykładzie kompilacji pliku indeksu *Academic.index* z pliku wejściowego schematu *Academic.schema* i plik danych *Academic.data*. Użyj następującego polecenia:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Szybkie tworzenie prototypów poza platformą Azure, aby uzyskać [ `kes.exe build_index` ](CommandLine.md#build_index-command) małych indeksów lokalnie, można utworzyć pliki danych zawierające do 10 000 obiektów. Dla większych plików danych, możesz uruchomić polecenie z poziomu [maszyny Wirtualnej systemu Windows na platformie Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), lub wykonaj kompilacji zdalnej na platformie Azure. Aby uzyskać więcej informacji, zobacz [skalowaniu](#scaling-up).

<a name="authoring-grammar"></a>
## <a name="use-an-xml-grammar-specification"></a>Użyć specyfikacji gramatyki XML
Gramatyka określa zestaw języka naturalnego zapytań, które usługa może interpretować oraz jak te zapytania języka naturalnego przetłumaczyć wyrażenia zapytania semantycznego. W tym przykładzie użyj gramatyki określone w *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Aby uzyskać więcej informacji na temat składni specyfikacji gramatyki, zobacz [formatu gramatyki](GrammarFormat.md).

<a name="compiling-grammar"></a>
## <a name="compile-the-grammar"></a>Kompiluj gramatyki
Po utworzeniu specyfikacji gramatyki XML można kompilować go do gramatyka binarna przy użyciu [ `kes.exe build_grammar` ](CommandLine.md#build_grammar-command). Należy pamiętać, że jeśli gramatyki Importuje schemat, plik schematu musi znajdować się w tej samej ścieżce jako gramatyki XML. W tym przykładzie kompilacji pliku binarnego gramatyki *Academic.grammar* z pliku wejściowego gramatyki XML *Academic.xml*. Użyj następującego polecenia:

`kes.exe build_grammar Academic.xml Academic.grammar`

<a name="hosting-index"></a>
## <a name="host-the-grammar-and-index-in-a-web-service"></a>Host gramatyki i indeksu w usłudze sieci web
Dla szybkie tworzenie prototypów, można hostować gramatyki i indeksu w usłudze sieci web na komputerze lokalnym przy użyciu [ `kes.exe host_service` ](CommandLine.md#host_service-command). Następnie można uzyskać dostępu do usługi za pośrednictwem [interfejsów API sieci web](WebAPI.md) do sprawdzania poprawności danych projektu prawidłowości i gramatyki. W tym przykładzie hosta pliku gramatyki *Academic.grammar* i pliku indeksu *Academic.index* na http://localhost:8000/. Użyj następującego polecenia:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Powoduje to zainicjowanie lokalne wystąpienie usługi sieci web. Interaktywnie można testować usługę odwiedzając `http::localhost:<port>` w przeglądarce. Aby uzyskać więcej informacji, zobacz [testowania usługi](#testing-service).

Można również bezpośrednio wywoływać różnych [interfejsów API sieci web](WebAPI.md) do testowania interpretacji języka naturalnego, wykonywania kwerend, oceny structured query i histogramu obliczeń. Aby zatrzymać usługę, wejścia "Zamknij" `kes.exe host_service` wiersza polecenia lub naciśnij klawisze Ctrl + C. Oto kilka przykładów:

* [http://localhost:8000/interpret?query=papers przez susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers t susan d & pełną = 1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=="susan dumais t)" & attributes=Title,Year,Author.Name,Author.Id & count = 2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=="susan t dumais"), rok > = 2013) & atrybuty = roku, słowo kluczowe & count = 4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Poza platformą Azure [ `kes.exe host_service` ](CommandLine.md#host_service-command) jest ograniczona do wskaźników do 10 000 obiektów. Inne ograniczenia obejmują 10 żądań na sekundę szybkość interfejsu API i łączną liczbę żądań 1000, zanim automatycznie kończy proces. Aby pominąć te ograniczenia, uruchom polecenie z poziomu [maszyny Wirtualnej systemu Windows na platformie Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), lub wdrożyć do usługi w chmurze platformy Azure przy użyciu [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) polecenia. Aby uzyskać więcej informacji, zobacz [wdrażania usługi](#deploying-service).

<a name="scaling-up"></a>
## <a name="scale-up-to-host-larger-indices"></a>Skalowanie w górę do indeksy większe hosta
Jeśli używasz `kes.exe` poza platformą Azure, indeks może zawierać maksymalnie 10 000 obiektów. Możesz skompilować i obsługi większych wskaźników przy użyciu usługi Azure. Zaloguj się do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). Jeśli jesteś subskrybentem Visual Studio lub MSDN, można też [aktywować korzyści dla subskrybentów](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Niektóre kredytów systemu Azure oferuje każdego miesiąca.

Aby umożliwić `kes.exe` dostęp do konta platformy Azure, [pobieranie pliku ustawień publikowania platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) z portalu Azure. W przypadku wyświetlenia monitu zaloguj się do żądanego konta platformy Azure. Zapisz plik jako *AzurePublishSettings.xml* w katalogu roboczym, skąd `kes.exe` działa.

Istnieją dwa sposoby tworzenia oraz obsługiwać duże indeksów. Pierwsza to przygotować pliki schematu i danych na maszynie Wirtualnej z systemu Windows Azure. Następnie uruchom [ `kes.exe build_index` ](#building-index) utworzyć indeks lokalnie na maszynie Wirtualnej, bez żadnych ograniczeń rozmiaru. Wynikowy indeks może być hostowana lokalnie na maszynie Wirtualnej za pomocą [ `kes.exe host_service` ](#hosting-service) dla szybkie tworzenie prototypów, ponownie bez ograniczeń. Aby uzyskać szczegółowe instrukcje, zobacz [samouczek maszyny Wirtualnej Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

Drugi sposób polega na wykonać kompilacji zdalnej platformy Azure przy użyciu [ `kes.exe build_index` ](CommandLine.md#build_index-command) z `--remote` parametru. Określa rozmiar maszyny Wirtualnej platformy Azure. Gdy `--remote` parametr jest określony, polecenie tworzy tymczasowej maszyny Wirtualnej platformy Azure z tym rozmiarze. Następnie tworzy indeks na maszynie Wirtualnej, przekazuje indeks docelowy magazyn obiektów blob i usuwa maszynę Wirtualną po zakończeniu. Subskrypcji platformy Azure jest pobierana kosztów maszyny Wirtualnej, gdy indeks jest kompilowany.

Ta możliwość kompilacji zdalnej platformy Azure pozwala [ `kes.exe build_index` ](CommandLine.md#build_index-command) do uruchomienia w każdym środowisku. Podczas przeprowadzania zdalnej kompilacji argumenty wejściowe: schemat i dane mogą być lokalne ścieżki do plików lub [magazynu obiektów blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) adresów URL. Argument indeksu danych wyjściowych musi być URL magazynu obiektów blob. Aby utworzyć konto magazynu platformy Azure, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md). Aby wydajnie do kopiowania plików i z magazynu obiektów blob, użyj [AzCopy](../../storage/common/storage-use-azcopy.md) narzędzia.

W tym przykładzie można założyć, że został już utworzony następujące kontenera magazynu obiektów blob: http://&lt;*konta*&gt;.blob.core.windows.net/&lt;*kontenera* &gt;/. Zawiera schemat *Academic.schema*, plik przywoływanego synonim *Keywords.syn*, a plikiem danych dobrym przybliżeniem pełnego *Academic.full.data*. Pełna indeksu można tworzyć zdalnie za pomocą następującego polecenia:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Należy pamiętać, że może upłynąć 5 – 10 minut, aby było możliwe temporay maszyny Wirtualnej, aby utworzyć indeks. Aby uzyskać szybkie tworzenie prototypów można:
- Opracowywania mniejszy zestaw danych lokalnie na dowolnym komputerze.
- Ręcznie [tworzenia maszyny Wirtualnej platformy Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [nawiązać z nią](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) za pośrednictwem pulpitu zdalnego, należy zainstalować [zestawu SDK usług eksploracji wiedzy](https://www.microsoft.com/en-us/download/details.aspx?id=51488)i uruchom [ `kes.exe` ](CommandLine.md) z poziomu maszyny Wirtualnej.

Stronicowanie spowalnia proces kompilacji. Aby uniknąć stronicowania, za pomocą maszyny Wirtualnej trzy razy ilość pamięci RAM jako rozmiar pliku danych wejściowych dla tworzenia indeksu. Użyj maszyn wirtualnych z 1 GB pamięci RAM więcej niż rozmiar indeksu dla hostingu. Aby uzyskać listę dostępnych rozmiarów maszyny Wirtualnej, zobacz [rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

<a name="deploying-service"></a>
## <a name="deploy-the-service"></a>Wdróż usługę
Po utworzeniu gramatyki i indeksu można przystąpić do wdrażania usługi do usługi w chmurze Azure. Aby utworzyć nową usługę w chmurze platformy Azure, zobacz [sposobu tworzenia i wdrażania usługi w chmurze](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Nie określaj pakietu wdrożeniowego w tym momencie.  

Po utworzeniu usługi w chmurze, można użyć [ `kes.exe deploy_service` ](CommandLine.md#deploy_service-command) do wdrożenia usługi. Usługi w chmurze Azure ma dwa miejsc wdrożenia: produkcyjnego i przejściowego. Do usługi, która odbiera ruch użytkownika na żywo należy wstępnie wdrażać do miejsca przemieszczania. Poczekaj, aż usługa uruchamiania i zainicjowania. Następnie możesz wysłać kilka żądań do sprawdzania poprawności wdrożenia i sprawdź przechodzą podstawowe testy.

[Zamień](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) zawartość przejściowym gniazdo z miejscem produkcyjnym, tak aby ruch na żywo jest teraz kierowane do nowo wdrożonej usługi. Ten proces można powtarzać w przypadku wdrażania zaktualizowaną wersję usługi nowymi danymi. Jak z wszystkich innych usług w chmurze Azure, można opcjonalnie Użyj portalu Azure do skonfigurowania [automatyczne skalowanie](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

W tym przykładzie wdrożeniem *Academic* indeks miejsca przemieszczania istniejącej usługi w chmurze z *< vm_size >* maszyn wirtualnych. Użyj następującego polecenia:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Aby uzyskać listę dostępnych rozmiarów maszyny Wirtualnej, zobacz [rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Po wdrożeniu usługi, należy wywołać różnych [interfejsów API sieci web](WebAPI.md) do testowania interpretacji języka naturalnego, wykonywania kwerend, oceny structured query i histogramu obliczeń.  

<a name="testing-service"></a>
## <a name="test-the-service"></a>Testowanie usługi
Do debugowania na żywo usługi, przejdź do komputera hosta z przeglądarki sieci web. Dla lokalnej usługi wdrażane za pomocą [host_service](#hosting-service), odwiedź stronę `http://localhost:<port>/`.  Platformy Azure wdrożone za pośrednictwem usługi w chmurze [deploy_service](#deploying-service), odwiedź stronę `http://<serviceName>.cloudapp.net/`.

Ta strona zawiera link do informacji na temat podstawowe statystyki wywołania interfejsu API, a także gramatyki i indeksu obsługiwana w tej usłudze. Ta strona zawiera również interfejs wyszukiwania interakcyjne prezentującą użycie interfejsów API sieci web. Wprowadź w polu wyszukiwania, aby wyświetlić wyniki zapytania [interpretowania](interpretMethod.md), [oceny](evaluateMethod.md), i [calchistogram](calchistogramMethod.md) wywołań interfejsu API. Na przykład integrowanie interfejsów API sieci web w aplikacji, aby utworzyć środowisko wyszukiwania rozbudowanych, interakcyjnych służy również źródle HTML strony.


