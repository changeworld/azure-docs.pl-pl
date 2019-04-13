---
title: 'Przykład: Wprowadzenie — interfejs API usługi Knowledge Exploration Service'
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API usługi Knowledge Exploration Service (KES), aby stworzyć aparat dla środowiska interaktywnego wyszukiwania publikacji akademickich.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 00c5ed3e3ea5c083f727d06c2ed305fe35ed03db
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523328"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Wprowadzenie do usługi Knowledge Exploration Service

W tym przewodniku użyjesz usługi Knowledge Exploration Service (KES) do utworzenia aparatu dla środowiska interaktywnego wyszukiwania publikacji akademickich. Narzędzie wiersza polecenia [`kes.exe`](CommandLine.md) i wszystkie pliki przykładowe możesz zainstalować z zestawu [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

Przykład dotyczący publikacji akademickich zawiera 1000 przykładowych publikacji naukowych opublikowanych przez pracowników naukowo-badawczych firmy Microsoft.  Do każdego dokumentu jest przypisany tytuł, rok publikacji, autorzy i słowa kluczowe. Każdy autor jest reprezentowany przez identyfikator, nazwę i przynależność w momencie publikacji. Każde słowo kluczowe można skojarzyć z zestawem synonimów (na przykład słowo kluczowe „support vector machine” można skojarzyć z synonimem „svm”).

## <a name="define-the-schema"></a>Definiowanie schematu

Schemat opisuje strukturę atrybutów obiektów w domenie. Określa nazwę i typ danych dla każdego atrybutu za pomocą pliku w formacie JSON. Poniższy przykład to zawartość pliku *Academic.schema*.

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

Tutaj definiuje się atrybuty *Title*, *Year* i *Keyword* jako, odpowiednio, ciąg, liczbę całkowitą i ciąg. Ponieważ autorzy są reprezentowani przez identyfikator, nazwę i przynależność, atrybut *Author* należy zdefiniować jako atrybut złożony z trzema atrybutami podrzędnymi: *Author.Id*, *Author.Name* i *Author.Affiliation*.

Domyślnie atrybuty obsługują wszystkie operacje dostępne dla ich typów danych, w tym *equals*, *starts_with* i *is_between*. Ponieważ identyfikator autora jest używany tylko wewnętrznie jako identyfikator, zastąp wartość domyślną i określ operację *equals* jako jedyną operację indeksowaną.

Dla atrybutu *Keyword* zezwól, aby synonimy pasowały do wartości kanonicznych słowa kluczowego, określając plik synonimów *Keyword.syn* w definicji atrybutu. Ten plik zawiera listę par „wartość kanoniczna-synonim”:

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

Aby uzyskać dodatkowe informacje na temat definicji schematu, zobacz [Format schematu](SchemaFormat.md).

## <a name="generate-data"></a>Generowanie danych

Plik danych opisuje listę publikacji do zaindeksowania, przy czym każdy wiersz określa wartości atrybutów publikacji w [formacie JSON](https://json.org/).  Poniższy przykład to pojedynczy wiersz z pliku danych *Academic.data* sformatowany tak, aby był czytelny:

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

W tym fragmencie kodu należy określić atrybuty *Title* i *Year* publikacji jako wartości JSON odpowiednio typu ciąg i liczba. Wiele wartości jest reprezentowanych za pomocą tablic JSON. Ponieważ *Author* to atrybut złożony, każda wartość jest reprezentowana przez obiekt JSON składający się z jego atrybutów podrzędnych. Atrybuty z brakującymi wartościami, w tym przypadku takie jak *Keyword*, można wykluczyć z reprezentacji JSON.

Aby rozróżnić prawdopodobieństwo dla różnych publikacji, określ względne logarytmy prawdopodobieństwa przy użyciu wbudowanego atrybutu *logprob*. Przy danym prawdopodobieństwie *p* z przedziału od 0 do 1 logarytm prawdopodobieństwa wynosi log(*p*), gdzie log() to funkcja logarytmu naturalnego.

Aby uzyskać więcej informacji, zobacz [Format danych](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Tworzenie skompresowanego indeksu binarnego

Gdy masz już plik schematu i plik danych, możesz utworzyć skompresowany indeks binarny obiektów danych przy użyciu polecenia [`kes.exe build_index`](CommandLine.md#build_index-command). W tym przykładzie utworzysz plik indeksu *Academic.index* z wejściowego pliku schematu *Academic.schema* i pliku danych *Academic.data*. Użyj następującego polecenia:

`kes.exe build_index Academic.schema Academic.data Academic.index`

W przypadku szybkiego prototypowania poza platformą Azure polecenie [`kes.exe build_index`](CommandLine.md#build_index-command) umożliwia lokalne utworzenie małych indeksów z plików danych, zawierających maksymalnie 10 000 obiektów. W przypadku większych plików danych musisz uruchomić polecenie na [maszynie wirtualnej platformy Azure z systemem Windows](../../../articles/virtual-machines/windows/quick-create-portal.md) lub wykonać zdalne budowanie na platformie Azure. Aby poznać szczegółowe informacje, zobacz Skalowanie w górę.

## <a name="use-an-xml-grammar-specification"></a>Użycie specyfikacji XML gramatyki

Gramatyka określa zbiór zapytań w języku naturalnym, które usługa może interpretować, a także sposób tłumaczenia tych zapytań w języku naturalnym na semantyczne wyrażenia zapytań. W tym przykładzie użyjesz gramatyki określonej w pliku *academic.xml*:

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

Aby uzyskać więcej informacji na temat składni specyfikacji gramatyki, zobacz [Format gramatyki](GrammarFormat.md).

## <a name="compile-the-grammar"></a>Kompilowanie gramatyki

Gdy specyfikacja XML gramatyki języka jest już określona, możesz skompilować ją do postaci binarnej za pomocą polecenia [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Należy pamiętać, że jeśli gramatyka importuje schemat, plik schematu musi znajdować się w tej samej ścieżce co plik XML gramatyki. W tym przykładzie utworzysz binarny plik gramatyki *Academic.grammar* na podstawie wejściowego pliku XML gramatyki *Academic.xml*. Użyj następującego polecenia:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Hostowanie gramatyki i indeksu w usłudze internetowej

W przypadku szybkiego prototypowania możesz hostować gramatykę i indeks w usłudze internetowej na maszynie lokalnej za pomocą polecenia [`kes.exe host_service`](CommandLine.md#host_service-command). Usługa będzie wtedy dostępna za pośrednictwem [internetowego interfejsu API](WebAPI.md), umożliwiając zweryfikowanie poprawności danych i projektu gramatyki. Ten przykład przedstawia hostowanie pliku gramatyki *Academic.grammar* i pliku indeksu *Academic.index* pod adresem `http://localhost:8000/`. Użyj następującego polecenia:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Spowoduje to zainicjowanie lokalnego wystąpienia usługi internetowej. Usługę możesz testować interaktywnie, otwierając adres `http::localhost:<port>` w przeglądarce. Aby uzyskać więcej informacji, zobacz Testowanie usługi.

Różne [internetowe interfejsy API](WebAPI.md) możesz także wywoływać bezpośrednio w celu testowania interpretacji języka naturalnego, wykonywania zapytań, oceniania zapytań ze strukturą i obliczania histogramu. Aby zatrzymać usługę, wpisz „quit” w wierszu polecenia `kes.exe host_service` lub naciśnij klawisze Ctrl+C. Oto kilka przykładów:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Poza platformą Azure polecenie [`kes.exe host_service`](CommandLine.md#host_service-command) jest ograniczone do indeksów zawierających maksymalnie 10 000 obiektów. Inne limity to maksymalnie 10 żądań interfejsu API na sekundę i łącznie 1000 żądań przed automatycznym przerwaniem procesu. Aby obejść te ograniczenia, uruchom polecenie z [maszyny wirtualnej platformy Azure z systemem Windows](../../../articles/virtual-machines/windows/quick-create-portal.md) lub wdróż do usługi w chmurze na platformie Azure przy użyciu polecenia [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Aby uzyskać szczegółowe informacje, zobacz Wdrażanie usługi.

## <a name="scale-up-to-host-larger-indices"></a>Skalowanie w górę na potrzeby hostowania większych indeksów

W przypadku uruchomienia polecenia `kes.exe` poza platformą Azure indeks jest ograniczony do 10 000 obiektów. Większe indeksy możesz tworzyć i hostować za pomocą platformy Azure. Zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). Jeśli jesteś subskrybentem programu Visual Studio lub MSDN, możesz też [aktywować korzyści dla subskrybentów](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Obejmują one pewną ilość środków platformy Azure każdego miesiąca.

Aby umożliwić poleceniu `kes.exe` dostęp do konta platformy Azure, [pobierz plik ustawień publikowania platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) z witryny Azure Portal. Jeśli zostanie wyświetlony monit, zaloguj się na wybrane konto platformy Azure. Zapisz plik pod nazwą *AzurePublishSettings.xml* w katalogu roboczym, z którego uruchomiono polecenie `kes.exe`.

Istnieją dwa sposoby tworzenia i hostowania dużych indeksów. Pierwszy to przygotowanie schematu i plików danych na maszynie wirtualnej platformy Azure z systemem Windows. Następnie należy uruchomić polecenie `kes.exe build_index`, aby utworzyć indeks lokalnie na maszynie wirtualnej bez żadnych ograniczeń rozmiaru. Wynikowy indeks można hostować lokalnie na maszynie wirtualnej za pomocą polecenia `kes.exe host_service` na potrzeby szybkiego prototypowania — ponownie, bez żadnych ograniczeń. Aby uzyskać szczegółowe instrukcje, zobacz [samouczek dotyczący maszyn wirtualnych platformy Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

Druga metoda to przeprowadzenie zdalnego budowania na platformie Azure przy użyciu polecenia [`kes.exe build_index`](CommandLine.md#build_index-command) z parametrem `--remote`. W ten sposób określa się rozmiar maszyny wirtualnej platformy Azure. Jeśli parametr `--remote` jest określony, polecenie tworzy tymczasową maszynę wirtualną platformy Azure o danym rozmiarze. Następnie tworzy indeks na maszynie wirtualnej, przekazuje indeks do docelowego magazynu obiektów blob i usuwa maszynę wirtualną po zakończeniu. Opłata dla subskrypcji platformy Azure jest naliczana za koszt maszyny wirtualnej podczas tworzenia indeksu.

Możliwość budowania zdalnego na platformie Azure pozwala na uruchomienie polecenia [`kes.exe build_index`](CommandLine.md#build_index-command) w każdym środowisku. Podczas budowania zdalnego schemat wejściowy i argumenty danych mogą być lokalnymi ścieżkami plików lub adresami URL [magazynu obiektów blob platformy Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Argument indeksu wyjściowego musi być adresem URL magazynu obiektów blob. Aby utworzyć konto magazynu platformy Azure, zobacz [Informacje o kontach magazynu Azure](../../storage/common/storage-create-storage-account.md). Aby wydajnie kopiować pliki z magazynu obiektów blob i do niego, należy użyć narzędzia [AzCopy](../../storage/common/storage-use-azcopy.md).

W tym przykładzie przyjęto, że następujący kontener magazynu obiektów blob został już utworzony: http://&lt;*konto*&gt;.blob.core.windows.net/&lt;*kontener*&gt;/. Zawiera on schemat *Academic.schema*, przywoływany plik synonimów *Keywords.syn* i pełny plik danych *Academic.full.data*. Pełny indeks możesz zbudować zdalnie przy użyciu następującego polecenia:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Należy pamiętać, że może zająć 5 – 10 minut, aby aprowizować tymczasowej maszyny Wirtualnej w celu tworzenia indeksu. W przypadku szybkiego prototypowania możesz:
- Opracowywać lokalnie przy użyciu mniejszego zestawu danych na dowolnej maszynie.
- Ręcznie [utworzyć maszynę wirtualną platformy Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [połączyć się z nią](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) za pomocą pulpitu zdalnego, zainstalować zestaw [Knowledge Exploration Service SDK](https://www.microsoft.com/en-us/download/details.aspx?id=51488) i uruchomić polecenie [`kes.exe`](CommandLine.md) z poziomu maszyny wirtualnej.

Stronicowanie spowalnia proces tworzenia. Aby uniknąć stronicowania, użyj maszyny wirtualnej z pamięcią RAM trzy razy większą od rozmiaru wejściowego pliku danych będącego podstawą tworzenia indeksu. Do hostowania użyj maszyny wirtualnej z pamięcią RAM o 1 GB większą niż rozmiar indeksu. Aby uzyskać listę dostępnych rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Wdrażanie usługi

Gdy masz już gramatykę i indeks, możesz wdrożyć usługę do usługi w chmurze platformy Azure. Aby utworzyć nową usługę w chmurze platformy Azure, zobacz [Jak utworzyć i wdrożyć usługę w chmurze](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Nie trzeba określać pakietu wdrożeniowego w tym momencie.  

Po utworzeniu usługi w chmurze możesz użyć polecenia [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) do wdrożenia usługi. Usługa w chmurze platformy Azure ma dwa miejsca wdrożenia: produkcyjne i przejściowe. Usługę odbierającą ruch użytkowników na żywo należy wstępnie wdrożyć do miejsca przejściowego. Poczekaj, aż usługa uruchomi się i zainicjuje. Następnie możesz wysłać kilka żądań, aby zwalidować wdrożenie i zweryfikować, czy przejdzie pomyślnie podstawowe testy.

[Zamień](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) zawartość miejsca przejściowego z miejscem produkcyjnym, tak aby ruch na żywo był teraz kierowany do nowo wdrożonej usługi. Ten proces można powtarzać w przypadku wdrażania zaktualizowanej wersji usługi z nowymi danymi. Podobnie jak w przypadku wszystkich innych usług w chmurze platformy Azure, możesz opcjonalnie użyć witryny Azure Portal do skonfigurowania [automatycznego skalowania](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

W tym przykładzie wdrożono *akademickich* indeksu, który ma miejsce na tymczasową istniejącej usługi w chmurze za pomocą  *\<vm_size >* maszyn wirtualnych. Użyj następującego polecenia:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Aby uzyskać listę dostępnych rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Po wdrożeniu usługi możesz wywoływać różne [internetowe interfejsy API](WebAPI.md) w celu testowania interpretacji języka naturalnego, wykonywania zapytań, oceniania zapytań ze strukturą i obliczania histogramu.  

## <a name="test-the-service"></a>Testowanie usługi

Aby debugować usługę na żywo, otwórz maszynę hostującą w przeglądarce internetowej. W przypadku usługi lokalnej wdrożonej za pomocą polecenia host_service odwiedź stronę `http://localhost:<port>/`.  W przypadku usługi w chmurze platformy Azure wdrożonej za pomocą polecenia deploy_service odwiedź stronę `http://<serviceName>.cloudapp.net/`.

Ta strona zawiera linki do informacji o podstawowych statystykach wywołań interfejsu API, a także o gramatyce i indeksie hostowanych w tej usłudze. Ta strona zawiera również interfejs interaktywnego wyszukiwania, który demonstruje użycie internetowych interfejsów API. Podaj zapytania w polu wyszukiwania, aby zobaczyć wyniki wywołań interfejsu API [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) i [calchistogram](calchistogramMethod.md). Bazowy kod HTML tej strony służy także jako przykład sposobu integrowania internetowych interfejsów API z aplikacją w celu utworzenia rozbudowanego interaktywnego środowiska wyszukiwania.


