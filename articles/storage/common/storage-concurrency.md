---
title: Zarządzanie współbieżnością
titleSuffix: Azure Storage
description: Dowiedz się, jak zarządzać współbieżnością dla usług obiektów blob, queue, table i file.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255303"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Zarządzanie współbieżnością w usłudze Microsoft Azure Storage

Nowoczesne aplikacje internetowe zazwyczaj mają wielu użytkowników przeglądających i aktualizujących dane jednocześnie. Wymaga to od deweloperów aplikacji, aby dokładnie przemyśleć, jak zapewnić przewidywalne środowisko dla swoich użytkowników końcowych, szczególnie w scenariuszach, w których wielu użytkowników może zaktualizować te same dane. Istnieją trzy główne strategie współbieżności danych, które deweloperzy zazwyczaj uważają:  

1. Optymistyczna współbieżność — aplikacja wykonująca aktualizację będzie w ramach swojej aktualizacji sprawdzić, czy dane uległy zmianie od czasu ostatniej odczytu tych danych przez aplikację. Na przykład, jeśli dwóch użytkowników przeglądających stronę typu wiki dokona aktualizacji na tej samej stronie, platforma wiki musi upewnić się, że druga aktualizacja nie zastąpi pierwszej aktualizacji — i że obaj użytkownicy rozumieją, czy ich aktualizacja zakończyła się pomyślnie, czy nie. Ta strategia jest najczęściej używana w aplikacjach sieci web.
2. Współbieżność pesymistyczne — aplikacja, która chce przeprowadzić aktualizację, spowoduje zablokowanie obiektu uniemożliwiającego innym użytkownikom aktualizowanie danych do czasu wydania blokady. Na przykład w scenariuszu replikacji danych wzorca/podrzędnego, w którym tylko wzorzec będzie wykonywać aktualizacje, wzorzec zazwyczaj będzie przechowywać blokadę wyłączności przez dłuższy czas na danych, aby upewnić się, że nikt inny nie może go zaktualizować.
3. Wygrywa ostatni moduł zapisujący — podejście, które umożliwia wszelkie operacje aktualizacji, aby kontynuować bez sprawdzania, czy jakakolwiek inna aplikacja zaktualizowała dane od czasu, gdy aplikacja po raz pierwszy odczytała dane. Ta strategia (lub brak formalnej strategii) jest zwykle używana, gdy dane są podzielone na partycje w taki sposób, że nie ma prawdopodobieństwa, że wielu użytkowników będzie uzyskiwać dostęp do tych samych danych. Może być również przydatne, gdy przetwarzane są krótkotrwałe strumienie danych.  

Ten artykuł zawiera omówienie sposobu, w jaki platforma Azure Storage upraszcza tworzenie, zapewniając pierwszą klasę pomocy technicznej dla wszystkich trzech tych strategii współbieżności.  

## <a name="azure-storage-simplifies-cloud-development"></a>Usługa Azure Storage upraszcza tworzenie chmury

Usługa azure storage obsługuje wszystkie trzy strategie, chociaż wyróżnia się zdolnością do zapewnienia pełnego wsparcia dla optymistycznej i pesymistycznej współbieżności, ponieważ została zaprojektowana w taki sposób, aby objąć model silnej spójności, który gwarantuje, że gdy Usługa magazynu zatwierdza operacji wstawiania lub aktualizacji danych wszystkie dalsze dostępy do tych danych zobaczą najnowszą aktualizację. Platformy pamięci masowej, które używają modelu spójności ostatecznej, mają opóźnienie między tym, kiedy zapis jest wykonywany przez jednego użytkownika, a kiedy zaktualizowane dane mogą być widoczne dla innych użytkowników, co komplikuje rozwój aplikacji klienckich, aby zapobiec niespójnościom. użytkowników końcowych.  

Oprócz wyboru odpowiedniej strategii współbieżności deweloperzy powinni być również świadomi, jak platforma magazynu izoluje zmiany — szczególnie zmiany do tego samego obiektu między transakcjami. Usługa magazynu platformy Azure używa izolacji migawki, aby umożliwić operacje odczytu odbywa się jednocześnie z operacjami zapisu w ramach jednej partycji. W przeciwieństwie do innych poziomów izolacji izolacji migawki gwarantuje, że wszystkie odczyty zobacz spójną migawkę danych, nawet gdy aktualizacje występują — zasadniczo przez zwrócenie ostatnich wartości popełnione podczas przetwarzania transakcji aktualizacji.  

## <a name="managing-concurrency-in-blob-storage"></a>Zarządzanie współbieżnością w magazynie obiektów Blob

Można wybrać użycie modeli współbieżności optymistyczne lub pesymistyczne do zarządzania dostępem do obiektów blob i kontenerów w usłudze obiektów Blob. Jeśli nie zostanie jawnie określić strategii ostatni zapisuje wygrywa jest ustawieniem domyślnym.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optymistyczna współbieżność obiektów blob i kontenerów

Usługa Storage przypisuje identyfikator do każdego przechowywanego obiektu. Ten identyfikator jest aktualizowany za każdym razem, gdy operacja aktualizacji jest wykonywana na obiekcie. Identyfikator jest zwracany do klienta jako część odpowiedzi HTTP GET przy użyciu nagłówka ETag (tag jednostki), który jest zdefiniowany w protokole HTTP. Użytkownik wykonujący aktualizację takiego obiektu może wysłać w oryginalnym etagu wraz z nagłówkiem warunkowym, aby upewnić się, że aktualizacja nastąpi tylko wtedy, gdy spełniony został określony warunek — w tym przypadku warunkiem jest nagłówek "If-Match", który wymaga usługi magazynu aby upewnić się, że wartość etagu określona w żądaniu aktualizacji jest taka sama, jak wartość przechowywana w usłudze storage.  

Zarys tego procesu jest następujący:  

1. Pobierz obiekt blob z usługi magazynu, odpowiedź zawiera wartość nagłówka HTTP ETag, która identyfikuje bieżącą wersję obiektu w usłudze magazynu.
2. Podczas aktualizowania obiektu blob, należy uwzględnić wartość ETag otrzymane w kroku 1 w **if-match** warunkowego nagłówka żądania wysyłane do usługi.
3. Usługa porównuje wartość ETag w żądaniu z bieżącą wartością ETag obiektu blob.
4. Jeśli bieżąca wartość ETag obiektu blob jest inna wersja niż ETag w **If-Match** nagłówka warunkowego w żądaniu, usługa zwraca błąd 412 do klienta. Oznacza to dla klienta, że inny proces zaktualizował obiekt blob, ponieważ klient go pobrać.
5. Jeśli bieżąca wartość ETag obiektu blob jest taka sama wersja jak ETag w **If-Match** nagłówka warunkowego w żądaniu, usługa wykonuje żądaną operację i aktualizuje bieżącą wartość ETag obiektu blob, aby pokazać, że utworzono nową wersję.  

Poniższy fragment kodu języka C# (przy użyciu biblioteki magazynu klienta 4.2.0) zawiera prosty przykład sposobu konstruowania **If-Match AccessCondition** na podstawie wartości ETag, który jest dostępny z właściwości obiektu blob, który został wcześniej pobrany lub wstawiony. Następnie używa **AccessCondition** obiektu podczas aktualizacji obiektu blob: **AccessCondition** obiekt dodaje **If-Match** nagłówek do żądania. Jeśli inny proces zaktualizował obiekt blob, usługa obiektów Blob zwraca komunikat o stanie HTTP 412 (warunek wstępny nie powiodło się). Pełną próbkę można pobrać tutaj: [Zarządzanie współbieżnością przy użyciu usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Usługa Azure Storage zawiera również obsługę dodatkowych nagłówków warunkowych, takich jak **If-Modified-Since**, **If-Unmodified-Since** i **If-None-Match,** a także ich kombinacji. Aby uzyskać więcej informacji, zobacz [Określanie nagłówków warunkowych dla operacji usługi obiektów blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

W poniższej tabeli podsumowano operacje kontenera, które akceptują nagłówki warunkowe, takie jak **If-Match** w żądaniu i które zwracają wartość ETag w odpowiedzi.  

| Operacja | Zwraca wartość ETag kontenera | Akceptuje nagłówki warunkowe |
|:--- |:--- |:--- |
| Tworzenie kontenera |Tak |Nie |
| Pobierz właściwości kontenera |Tak |Nie |
| Pobierz metadane kontenera |Tak |Nie |
| Ustawianie metadanych kontenera |Tak |Tak |
| Pobierz acl kontenera |Tak |Nie |
| Ustawianie listy ACL kontenera |Tak |Tak (*) |
| Usuwanie kontenera |Nie |Tak |
| Kontener dzierżawy |Tak |Tak |
| Lista obiektów blob |Nie |Nie |

(*) Uprawnienia zdefiniowane przez SetContainerACL są buforowane i aktualizacje tych uprawnień trwać 30 sekund do propagacji, podczas którego okres aktualizacje nie są gwarantowane być spójne.  

W poniższej tabeli podsumowano operacje obiektu blob, które akceptują nagłówki warunkowe, takie jak **If-Match** w żądaniu i które zwracają wartość ETag w odpowiedzi.

| Operacja | Zwraca wartość ETag | Akceptuje nagłówki warunkowe |
|:--- |:--- |:--- |
| Wstawianie obiektu blob |Tak |Tak |
| Pobierz obiekt blob |Tak |Tak |
| Pobieranie właściwości obiektu blob |Tak |Tak |
| Ustawianie właściwości obiektu blob |Tak |Tak |
| Pobierz metadane obiektów blob |Tak |Tak |
| Ustawianie metadanych obiektów blob |Tak |Tak |
| Dzierżawa obiektu blob (*) |Tak |Tak |
| Wykonywanie migawki obiektu blob |Tak |Tak |
| Kopiowanie obiektu blob |Tak |Tak (dla źródłowego i docelowego obiektu blob) |
| Przerwanie kopiowania obiektu blob |Nie |Nie |
| Usuwanie obiektu blob |Nie |Tak |
| Umieść blok |Nie |Nie |
| Umieść listę zablokowanych |Tak |Tak |
| Pobierz listę zablokowanych |Tak |Nie |
| Umieść stronę |Tak |Tak |
| Uzyskaj zakresy stron |Tak |Tak |

(*) Dzierżawa obiektu blob nie zmienia ETag na obiekcie blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Współbieżność pesymistyczna dla obiektów blob

Aby zablokować obiekt blob do wyłącznego użytku, można nabyć [dzierżawy](https://msdn.microsoft.com/library/azure/ee691972.aspx) na nim. Po nabyciu dzierżawy należy określić, jak długo potrzebna jest dzierżawa: może to trwać od 15 do 60 sekund lub nieskończona, co stanowi blokadę wyłączności. Można odnowić dzierżawę skończoną, aby ją przedłużyć, i możesz zwolnić dowolną dzierżawę po zakończeniu. Usługa obiektów Blob automatycznie zwalnia dzierżawy skończone po ich wygaśnięciu.  

Dzierżawy umożliwiają różne strategie synchronizacji, które mają być obsługiwane, w tym wyłączne zapis / wspólny odczyt, wyłączny zapis / wyłączny odczyt i wspólny zapis / wyłączny odczyt. W przypadku istnienia dzierżawy usługa magazynowania wymusza zapisy wyłączne (operacje umieszczania, ustawiania i usuwania), jednak zapewnienie wyłączności dla operacji odczytu wymaga od dewelopera upewnienia się, że wszystkie aplikacje klienckie używają identyfikatora dzierżawy i że tylko jeden klient naraz ma prawidłowy identyfikator dzierżawy. Operacje odczytu, które nie zawierają identyfikatora dzierżawy, powodują odczyty udostępnione.  

Poniższy fragment kodu języka C# zawiera przykład nabycia wyłącznej dzierżawy na 30 sekund w obiekcie blob, aktualizowanie zawartości obiektu blob, a następnie zwalnianie dzierżawy. Jeśli podczas próby uzyskania nowej dzierżawy istnieje już prawidłowa dzierżawa obiektu blob, usługa obiektów Blob zwraca wynik stanu "KONFLIKT HTTP (409) . Poniższy fragment kodu używa **accessCondition** obiektu do hermetyzacji informacji dzierżawy, gdy sprawia, że żądanie aktualizacji obiektu blob w usłudze magazynu.  Pełną próbkę można pobrać tutaj: [Zarządzanie współbieżnością przy użyciu usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Jeśli spróbujesz wykonać operację zapisu na dzierżawionym obiekcie blob bez przekazywania identyfikatora dzierżawy, żądanie kończy się niepowodzeniem z błędem 412. Należy zauważyć, że jeśli dzierżawa wygaśnie przed wywołaniem **Metody UploadText,** ale nadal przekazujesz identyfikator dzierżawy, żądanie również kończy się niepowodzeniem z błędem **412.** Aby uzyskać więcej informacji na temat zarządzania czasem wygaśnięcia dzierżawy i identyfikatorów dzierżawy, zobacz dokumentację [dzierżawy obiektów blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST.  

Następujące operacje obiektu blob można użyć dzierżawy do zarządzania współbieżności pesymistyczne:  

* Wstawianie obiektu blob
* Pobierz obiekt blob
* Pobieranie właściwości obiektu blob
* Ustawianie właściwości obiektu blob
* Pobierz metadane obiektów blob
* Ustawianie metadanych obiektów blob
* Usuwanie obiektu blob
* Umieść blok
* Umieść listę zablokowanych
* Pobierz listę zablokowanych
* Umieść stronę
* Uzyskaj zakresy stron
* Migawka blob - identyfikator dzierżawy opcjonalne, jeśli istnieje dzierżawa
* Kopiowanie obiektu blob — identyfikator dzierżawy wymagany, jeśli istnieje dzierżawa w docelowym obiekcie blob
* Abort Copy Blob - identyfikator dzierżawy wymagany, jeśli istnieje nieskończona dzierżawa w docelowym obiekcie blob
* Dzierżawienie obiektu blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pesymistyczne współbieżności dla kontenerów

Dzierżawy na kontenerach umożliwiają te same strategie synchronizacji, które mają być obsługiwane jak w obiektach blob (wyłączny zapis / współdzielony odczyt, wyłączny zapis / wyłączny odczyt i wspólny zapis / odczyt wyłączny), jednak w przeciwieństwie do obiektów blob usługa magazynu wymusza wyłączność tylko na operacjach usuwania. Aby usunąć kontener z aktywną dzierżawą, klient musi dołączyć aktywny identyfikator dzierżawy z żądaniem usunięcia. Wszystkie inne operacje kontenera zakończyć się pomyślnie na dzierżawiony kontener bez uwzględnienia identyfikatora dzierżawy, w którym to przypadku są one operacje współużytkowane. Jeśli wymagana jest wyłączność operacji aktualizacji (put lub set) lub odczytu, deweloperzy powinni upewnić się, że wszyscy klienci używają identyfikatora dzierżawy i że tylko jeden klient naraz ma prawidłowy identyfikator dzierżawy.  

Następujące operacje kontenera można użyć dzierżawy do zarządzania współbieżności pesymistyczne:  

* Usuwanie kontenera
* Pobierz właściwości kontenera
* Pobierz metadane kontenera
* Ustawianie metadanych kontenera
* Pobierz acl kontenera
* Ustawianie listy ACL kontenera
* Kontener dzierżawy  

Aby uzyskać więcej informacji, zobacz:  

* [Określanie nagłówków warunkowych dla operacji usługi Blob Service](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Kontener dzierżawy](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Dzierżawienie obiektu blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Zarządzanie współbieżnością w magazynie tabel

Usługa tabela używa optymistycznych kontroli współbieżności jako domyślne zachowanie podczas pracy z jednostkami, w przeciwieństwie do usługi obiektów Blob, gdzie należy jawnie wybrać do wykonywania optymistycznych kontroli współbieżności. Inną różnicą między usługami tabeli i obiektów Blob jest to, że można zarządzać tylko zachowanie współbieżności jednostek, podczas gdy z usługą obiektów Blob można zarządzać współbieżności zarówno kontenerów i obiektów blob.  

Aby użyć optymistycznej współbieżności i sprawdzić, czy inny proces zmodyfikował jednostkę od momentu pobrania jej z usługi magazynu tabel, można użyć wartości ETag otrzymanej, gdy usługa tabel zwraca encję. Zarys tego procesu jest następujący:  

1. Pobierz jednostkę z usługi magazynu tabel, odpowiedź zawiera wartość ETag, która identyfikuje bieżący identyfikator skojarzony z tą jednostką w usłudze magazynu.
2. Podczas aktualizowania jednostki należy uwzględnić wartość ETag otrzymaną w kroku 1 w nagłówku obowiązkowe **if-match** żądania wysyłanego do usługi.
3. Usługa porównuje wartość ETag w żądaniu z bieżącą wartością ETag jednostki.
4. Jeśli bieżąca wartość ETag jednostki jest inna niż ETag w obowiązkowym nagłówku **If-Match** w żądaniu, usługa zwraca klientowi błąd 412. Oznacza to klientowi, że inny proces zaktualizował jednostkę, ponieważ klient ją odebrał.
5. Jeśli bieżąca wartość ETag jednostki jest taka sama jak ETag w obowiązkowym nagłówku **If-Match** w żądaniu lub w nagłówku **If-Match** zawiera symbol wieloznaczny (*), usługa wykonuje żądaną operację i aktualizuje bieżącą wartość ETag jednostki, aby pokazać, że została zaktualizowana.  

Należy zauważyć, że w przeciwieństwie do usługi obiektów Blob, usługa tabel wymaga klienta do uwzględnienia **if-match** nagłówka w żądaniach aktualizacji. Jednak istnieje możliwość wymuszenia bezwarunkowej aktualizacji (strategia ostatniego modułu zapisującego wygrywa) i ominięcia kontroli współbieżności, jeśli klient ustawia nagłówek **If-Match** na symbol wieloznaczny (*) w żądaniu.  

Poniższy fragment kodu języka C# pokazuje jednostkę klienta, która została wcześniej utworzona lub pobrana po zaktualizowaniu ich adresu e-mail. Początkowa operacja wstawiania lub pobierania przechowuje wartość ETag w obiekcie klienta, a ponieważ próbka używa tego samego wystąpienia obiektu podczas wykonywania operacji zastępowania, automatycznie wysyła wartość ETag z powrotem do usługi tabeli, umożliwiając usługę sprawdzić, czy nie ma naruszeń współbieżności. Jeśli inny proces zaktualizował jednostkę w magazynie tabel, usługa zwraca komunikat o stanie HTTP 412 (Warunek wstępny nie powiodło się).  Pełną próbkę można pobrać tutaj: [Zarządzanie współbieżnością przy użyciu usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Aby jawnie wyłączyć sprawdzanie współbieżności, należy ustawić **ETag** właściwość obiektu **pracownika** na "*" przed wykonaniem operacji wymiany.  

```csharp
customer.ETag = "*";  
```

W poniższej tabeli podsumowano sposób, w jaki operacje jednostki tabeli używają wartości ETag:

| Operacja | Zwraca wartość ETag | Wymaga nagłówka żądania if-match |
|:--- |:--- |:--- |
| Encje kwerend |Tak |Nie |
| Wstaw element |Tak |Nie |
| Aktualizuj encję |Tak |Tak |
| Jednostka scalania |Tak |Tak |
| Usuń encję |Nie |Tak |
| Wstawianie lub zamienianie elementu |Tak |Nie |
| Wstawianie lub scalanie jednostki |Tak |Nie |

Należy zauważyć, że **operacje Wstawianie lub zamienianie jednostki** i **wstawiania lub scalania** *jednostki nie* są przeprowadzane żadne kontrole współbieżności, ponieważ nie wysyłają wartości ETag do usługi tabel.  

Ogólnie deweloperzy przy użyciu tabel należy polegać na optymistycznej współbieżności podczas tworzenia skalowalnych aplikacji. Jeśli pesymistyczne blokowanie jest potrzebne, jedno podejście deweloperzy mogą podjąć podczas uzyskiwania dostępu do tabel jest przypisanie wyznaczonego obiektu blob dla każdej tabeli i spróbuj wziąć dzierżawę na obiekt blob przed działając na stole. Takie podejście wymaga aplikacji, aby upewnić się, że wszystkie ścieżki dostępu do danych uzyskać dzierżawy przed działając w tabeli. Należy również pamiętać, że minimalny czas dzierżawy wynosi 15 sekund, co wymaga starannego rozważenia skalowalności.  

Aby uzyskać więcej informacji, zobacz:  

* [Operacje na jednostkach](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Zarządzanie współbieżnością w usłudze kolejek

Jednym ze scenariuszy, w którym współbieżność jest problemem w usłudze kolejkowania jest, gdy wielu klientów pobiera wiadomości z kolejki. Po pobraniu wiadomości z kolejki, odpowiedź zawiera wiadomość i wartość potwierdzenia pop, który jest wymagany do usunięcia wiadomości. Wiadomość nie jest automatycznie usuwana z kolejki, ale po jej pobraniu nie jest widoczna dla innych klientów dla przedziału czasu określonego przez parametr visibilitytimeout. Klient, który pobiera wiadomość oczekuje się usunięcia wiadomości po jej przetworzyniu i przed czasem określonym przez TimeNextVisible element odpowiedzi, który jest obliczany na podstawie wartości parametru visibilitytimeout. Wartość visibilitytimeout jest dodawany do czasu, w którym wiadomość jest pobierana w celu określenia wartości TimeNextVisible.  

Usługa kolejki nie obsługuje współbieżności optymistycznej lub pesymistycznej i z tego powodu klienci przetwarzający wiadomości pobrane z kolejki powinni upewnić się, że wiadomości są przetwarzane w sposób idempotentny. Strategia ostatniego modułu zapisującego jest używana do operacji aktualizacji, takich jak Właściwości SetQueueServiceProperties, SetQueueMetaData, SetQueueACL i UpdateMessage.  

Aby uzyskać więcej informacji, zobacz:  

* [Interfejs API REST usługi Queue (Kolejka)](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Pobierz wiadomości](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Zarządzanie współbieżnością w plikach platformy Azure

Dostęp do usługi plików można uzyskać przy użyciu dwóch różnych punktów końcowych protokołu — SMB i REST. Usługa REST nie obsługuje ani optymistycznego blokowania, ani pesymistycznego blokowania, a wszystkie aktualizacje będą zgodne ze strategią ostatniego pisarza. Klienci SMB, którzy montują udziały plików, mogą korzystać z mechanizmów blokowania systemu plików w celu zarządzania dostępem do udostępnionych plików , w tym możliwości wykonywania pesymistycznego blokowania. Gdy klient SMB otwiera plik, określa zarówno tryb dostępu do pliku, jak i trybu udostępniania. Ustawienie opcji dostępu do plików "Zapis" lub "Odczyt/Zapis" wraz z trybem udziału plików "Brak" spowoduje zablokowanie pliku przez klienta SMB do momentu zamknięcia pliku. Jeśli operacja REST jest podejmowana w pliku, w którym klient SMB ma zablokowany plik, usługa REST zwróci kod stanu 409 (Konflikt) z kodem błędu SharingViolation.  

Gdy klient SMB otwiera plik do usunięcia, oznacza plik jako oczekujący na usunięcie, dopóki wszystkie inne dojścia otwarte dla klienta SMB w tym pliku nie zostaną zamknięte. Gdy plik jest oznaczony jako oczekujące usunięcie, każda operacja REST w tym pliku zwróci kod stanu 409 (Konflikt) z kodem błędu SMBDeletePending. Kod stanu 404 (nie znaleziono) nie jest zwracany, ponieważ klient SMB może usunąć oczekującą flagę usuwania przed zamknięciem pliku. Innymi słowy kod stanu 404 (nie znaleziono) jest oczekiwany tylko wtedy, gdy plik został usunięty. Należy zauważyć, że gdy plik jest w stanie oczekiwania na usunięcie SMB, nie zostanie uwzględniony w wynikach listy plików. Należy również zauważyć, że rest delete file i REST Delete Directory operacje są zatwierdzone niepodzielnie i nie powodują oczekujące go usunąć stan.  

Aby uzyskać więcej informacji, zobacz:  

* [Zarządzanie blokadami plików](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Następne kroki

Dla pełnej przykładowej aplikacji, do których odwołuje się ten blog:  

* [Zarządzanie współbieżnością przy użyciu usługi Azure Storage — przykładowa aplikacja](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Aby uzyskać więcej informacji na temat usługi Azure Storage, zobacz:  

* [Strona główna magazynu platformy Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Wprowadzenie do usługi Azure Storage](storage-introduction.md)
* Wprowadzenie do magazynu dla [obiektów Blob,](../blobs/storage-dotnet-how-to-use-blobs.md) [Tabela,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [Kolejki](../storage-dotnet-how-to-use-queues.md)i [Pliki](../storage-dotnet-how-to-use-files.md)
* Architektura magazynu — [usługa Azure Storage: usługa magazynu w chmurze o wysokiej dostępności o silnej spójności](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

