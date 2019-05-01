---
title: Zarządzanie współbieżnością w usłudze Microsoft Azure Storage
description: Jak zarządzać współbieżności dla usługi obiektów Blob, kolejka, tabela i plik
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.subservice: common
ms.openlocfilehash: 9e786aed031d528b8ae574444b71753ac538cf47
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728304"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Zarządzanie współbieżnością w usłudze Microsoft Azure Storage
## <a name="overview"></a>Omówienie
Nowoczesne aplikacje internetowe na podstawie zwykle mają wielu użytkownikom wyświetlanie i aktualizowanie danych jednocześnie. Wymaga to deweloperom aplikacji należy dobrze przemyśleć sposób zapewnić przewidywalne doświadczenia własnych użytkowników końcowych, zwłaszcza w przypadku scenariuszy, w których wielu użytkowników można zaktualizować te same dane. Istnieją trzy Strategie współbieżności główne dane, które deweloperzy zazwyczaj należy wziąć pod uwagę:  

1. Optymistycznej współbieżności — aplikacja działa który aktualizację w ramach jego aktualizacji sprawdzi, jeśli dane zostały zmienione od czasu jej ostatniego odczytu danych. Na przykład jeśli dwóch użytkowników, wyświetlanie strony typu wiki dokonać aktualizacji do tej samej strony platformy wiki musi zapewnić drugi aktualizacja nie zastępuje pierwszą aktualizacją — i że zarówno użytkownicy wiedzą, czy ich aktualizacji zakończyła się powodzeniem. Ta strategia jest najczęściej używana w aplikacjach sieci web.
2. Współbieżność pesymistyczna — wyszukiwanie do przeprowadzenia aktualizacji aplikacji potrwa blokadę na obiekcie uniemożliwi innym użytkownikom aktualizowanie danych, dopóki blokada jest zwalniana. Na przykład w przypadku replikacji danych typu nadrzędny/podrzędny, gdzie tylko wzorcu wykona aktualizacje wzorca będzie zazwyczaj przechowują blokady na wyłączność przez dłuższy czas na danych, aby upewnić się, że nikt inny nie mogli go zaktualizować.
3. Ostatni składnik zapisywania usługi wins — metody, która zezwala na wszystkie operacje aktualizacji kontynuować bez sprawdzenia, jeśli wszystkie inne aplikacje zaktualizował dane ponieważ aplikacji najpierw odczytywać dane. Tej strategii (lub brak formalne strategii) jest zwykle używany, gdzie dane są partycjonowane w taki sposób, że nie istnieje prawdopodobieństwo czy wielu użytkowników będą miały dostęp tych samych danych. Można również go przydatne gdzie przetwarzania strumieni danych krótkotrwałych.  

Ten artykuł zawiera omówienie, jak platforma Azure Storage upraszcza tworzenie, zapewniając obsługę pierwszej klasy w ramach wszystkich trzech z następujących strategii współbieżności.  

## <a name="azure-storage--simplifies-cloud-development"></a>Usługa Azure Storage — upraszcza projektowanie aplikacji w chmurze
Usługa Azure storage obsługuje wszystkie trzy strategie, mimo że szczególne zapewniają pełne wsparcie dla optymistyczne i pesymistycznej współbieżności, ponieważ został on zaprojektowany z modelu silnej spójności, co gwarantuje, że w przypadku wykorzystać możliwości Usługa Storage zatwierdza wstawiania danych lub operacji aktualizacji wszystkich dodatkowo uzyskuje dostęp w celu czy danych spowoduje zwrócenie najnowszej aktualizacji. Platformy magazynu, które używają modelu spójności ostatecznej ma opóźnienie między podczas zapisu jest wykonywane przez jednego użytkownika i zaktualizowane dane mogą być widoczne dla innych użytkowników, w związku z tym komplikowania kodu języka programowania aplikacji klienckich, aby uniknąć niespójności z wpływając na użytkowników końcowych.  

Wybierz opcję strategii odpowiednie współbieżności deweloperów należy również pamiętać o jak platformy magazynu izoluje zmian — szczególnie zmiany do tego samego obiektu w transakcji. Usługa Azure storage korzysta z izolacji migawki, Zezwalaj na operacje odczytu, które ma być wykonywana równolegle operacji zapisu w obrębie jednej partycji. W przeciwieństwie do innych poziomów izolacji izolacji migawki gwarantuje, że wszystkie operacje odczytu Zobacz spójnej migawki danych nawet wtedy, gdy występują aktualizacje — zasadniczo, zwracając ostatnie wartości zatwierdzone, podczas aktualizacji przetwarzania transakcji.  

## <a name="managing-concurrency-in-blob-storage"></a>Zarządzanie współbieżnością w usłudze Blob storage
Możesz zdecydować się na zarządzanie dostępem do obiektów blob i kontenery w usłudze obiektów blob za pomocą obu modeli optymistycznego lub pesymistycznego współbieżności. Jeśli nie zostanie jawnie wins strategii z ostatniego zapisu jest ustawieniem domyślnym.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optymistycznej współbieżności dla kontenerów i obiektów blob
Usługa Magazyn przypisuje identyfikator do każdego obiektu przechowywanego. Ten identyfikator jest aktualizowany za każdym razem, gdy aktualizacja jest wykonywane na obiekcie. Identyfikator jest zwracana do klienta jako część odpowiedzi HTTP GET, przy użyciu nagłówka ETag (tag jednostki), która jest zdefiniowana w ramach protokołu HTTP. Użytkownik przeprowadzania aktualizacji na taki obiekt można wysłać w oryginalny element ETag wraz z nagłówkiem warunkowe, aby upewnić się, że aktualizacja tylko wówczas, gdy określony warunek został spełniony — w tym przypadku warunek jest nagłówek "If-Match", który wymaga t usługi Storage o upewnij się, że wartość elementu ETag określony w żądaniu aktualizacji jest taka sama jak przechowywany w usłudze Storage.  

Zarys tego procesu jest następująca:  

1. Pobieranie obiektu blob z usługi storage, odpowiedź zawiera wartość nagłówka ETag HTTP, która określa bieżącą wersję obiektu w usłudze storage.
2. Po zaktualizowaniu obiektu blob zawierają wartość elementu ETag odebrane w kroku 1 w **If-Match** warunkowego nagłówka żądania wysyłane do usługi.
3. Usługa porównuje wartość elementu ETag w żądaniu z bieżącą wartością elementu ETag obiektu blob.
4. Jeśli bieżąca wartość elementu ETag obiektu blob jest inna wersja niż elementu ETag w **If-Match** warunkowego nagłówka w żądaniu, usługa zwraca błąd 412 do klienta. Oznacza to, do klienta inny proces został zaktualizowany obiekt blob, ponieważ klient pobrano go.
5. Jeśli bieżąca wartość elementu ETag obiektu blob jest tej samej wersji, co element ETag w **If-Match** warunkowego nagłówka w żądaniu usługi, wykonuje żądaną operację i aktualizuje bieżącą wartość elementu ETag obiektów blob, aby pokazać, że została utworzona Nowa wersja.  

Poniższy fragment C# (przy użyciu biblioteki klienta usługi Storage 4.2.0) przedstawiono prosty przykład sposobu konstruowania **AccessCondition If-Match** na podstawie wartości element ETag, który jest dostępny z właściwości obiektu blob, który był wcześniej albo pobrać lub wstawiona. Następnie używa **AccessCondition** obiektu, kiedy aktualizuje obiekt blob: **AccessCondition** dodaje obiekt **If-Match** nagłówka żądania. Jeśli inny proces był aktualizowany obiekt blob, usługę blob service zwraca komunikat stanu HTTP 412 (niepowodzenie warunku wstępnego). Można pobrać pełny przykład: [Zarządzanie współbieżnością za pomocą usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
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

Usługa magazynu obejmuje również obsługę dodatkowych nagłówków warunkowych takich jak **If-Modified-Since**, **If w niezmienionej postaci od** i **If-None-Match** także ich kombinacji. Aby uzyskać więcej informacji, zobacz [Określanie warunkowego nagłówki dla operacji usługi obiektów Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx) w witrynie MSDN.  

Poniższa tabela zawiera podsumowanie operacji kontenera, które akceptowanie nagłówków warunkowych, takich jak **If-Match** w żądaniu, które zwracają wartość elementu ETag w odpowiedzi.  

| Operacja | Zwraca wartość elementu ETag kontenera | Akceptuje nagłówki warunkowe |
|:--- |:--- |:--- |
| Tworzenie kontenera |Yes |Nie |
| Pobierz właściwości kontenera |Yes |Nie |
| Pobranie metadanych kontenera |Yes |Nie |
| Metadane kontenera zestawu |Yes |Yes |
| Pobieranie listy ACL kontenera |Yes |Nie |
| Ustaw ACL kontenera |Yes |Tak (*) |
| Usuwanie kontenera |Nie |Yes |
| Dzierżawa kontenera |Yes |Yes |
| Wyświetlanie listy obiektów blob |Nie |Nie |

(*) Uprawnienia określone przez SetContainerACL są buforowane i aktualizacji tych uprawnień potrwać 30 sekund do propagowania okresie, które aktualizacje są nie musi być zgodne.  

W poniższej tabeli przedstawiono operacje obiektów blob, które akceptowanie nagłówków warunkowych, takich jak **If-Match** w żądaniu, które zwracają wartość elementu ETag w odpowiedzi.

| Operacja | Zwraca wartość elementu ETag | Akceptuje nagłówki warunkowe |
|:--- |:--- |:--- |
| Put Blob |Yes |Yes |
| Get Blob |Yes |Yes |
| Pobierz właściwości obiektu Blob |Yes |Yes |
| Ustaw właściwości obiektu Blob |Yes |Yes |
| Pobierz metadane obiektu blob |Yes |Yes |
| Ustaw metadane obiektu Blob |Yes |Yes |
| Dzierżawienie obiektu Blob (*) |Yes |Yes |
| Wykonywanie migawki obiektu Blob |Yes |Yes |
| Copy Blob |Yes |Tak (w przypadku źródłowego i docelowego obiektu blob) |
| Przerwij obiektu Blob kopiowania |Nie |Nie |
| Usuwanie obiektu Blob |Nie |Yes |
| Umieść bloku |Nie |Nie |
| Umieść zablokowanych |Yes |Yes |
| Pobierz listę zablokowanych |Yes |Nie |
| Umieść strony |Yes |Yes |
| Pobieranie zakresów stron |Yes |Yes |

(*) Dzierżawienie obiektu Blob nie zmienia się tag ETag na obiekcie blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Współbieżność pesymistyczna dla obiektów blob
Aby zablokować obiektu blob do wyłącznego użytku, mogą nabyć [dzierżawy](https://msdn.microsoft.com/library/azure/ee691972.aspx) na nim. Po użytkownik uzyskuje dzierżawę, można określić, jak długo mają być dzierżawy: może to być od 15 do 60 sekund lub nieskończone, który wynosi blokady na wyłączność. Można odnowić dzierżawę skończoną rozszerzać go, a można zwolnić wszystkie dzierżawy, po zakończeniu pracy z nim. Usługa blob automatycznie zwalnia skończoną dzierżaw, gdy wygasają.  

Dzierżawy Włącz synchronizacji różne strategie są obsługiwane, w tym zapisu na wyłączność / udostępnione na wyłączność, odczytu, zapisu / wyłącznie odczytu i zapisu do udostępnionego / odczyt wyłącznie. W przypadku, gdy istnieje dzierżawa usługi storage wymusza wyłączne operacje zapisu (put, ustaw i operacje usuwania) jednak zapewnienie na wyłączność dla operacji odczytu wymaga deweloperowi upewnij się, że wszystkie aplikacje klienckie używanie Identyfikatora dzierżawy w danym momencie tylko jeden klient ma Identyfikator prawidłową dzierżawę. Operacje odczytu, które nie obejmują wynik identyfikator dzierżawy w udostępnionej odczytów.  

Poniższy fragment kodu języka C# pokazano przykład Uzyskiwanie dzierżawy wyłączności przez 30 sekund na obiekcie blob, aktualizowania zawartości obiektu blob, a następnie zwolnić dzierżawy. Jeśli istnieje już prawidłową dzierżawę w obiekcie blob podczas próby uzyskania nową dzierżawę, usługę blob service zwraca wynik stanu "Konflikt HTTP (409)". Następujący fragment kodu używa **AccessCondition** obiektu do hermetyzacji informacje o dzierżawach, gdy kieruje żądanie do zaktualizowania obiektu blob w usłudze storage.  Można pobrać pełny przykład: [Zarządzanie współbieżnością za pomocą usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Jeśli spróbujesz operacji zapisu dla dzierżawy obiektu blob bez przekazywania identyfikator dzierżawy, żądanie kończy się błędem 412. Należy pamiętać, że jeśli wygaśnięcia dzierżawy przed wywołaniem **UploadText** metoda, ale nadal przekazywać identyfikator dzierżawy, żądanie nie powiedzie się także z **412** błędu. Aby uzyskać więcej informacji o zarządzaniu czas wygaśnięcia dzierżawy i dzierżawy identyfikatorów, zobacz [dzierżawienie obiektu Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) dokumentacji rozwiązania REST.  

Następujące operacje obiektów blob umożliwia zarządzanie Współbieżność pesymistyczna dzierżawy:  

* Put Blob
* Get Blob
* Pobierz właściwości obiektu Blob
* Ustaw właściwości obiektu Blob
* Pobierz metadane obiektu blob
* Ustaw metadane obiektu Blob
* Usuwanie obiektu Blob
* Umieść bloku
* Umieść zablokowanych
* Pobierz listę zablokowanych
* Umieść strony
* Pobieranie zakresów stron
* Wykonywanie migawki obiektu Blob — identyfikator dzierżawy jest opcjonalne, jeśli istnieje dzierżawa
* Wymagany identyfikator dzierżawy obiektu Blob kopiowania — czy dzierżawa istnieje w docelowym obiekcie blob
* Wymagany identyfikator dzierżawy obiektu Blob kopiowania przerwania — Jeśli nieskończonej dzierżawa istnieje w docelowym obiekcie blob
* Dzierżawienie obiektu Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Współbieżność pesymistyczna dla kontenerów
Dzierżawy w kontenerach Włącz ale opisane strategie synchronizacji są obsługiwane na obiekty BLOB (wyłącznie zapisu / udostępnione na wyłączność, odczytu, zapisu / wyłącznie odczytu i zapisu do udostępnionego / odczyt wyłącznie) jednak w przeciwieństwie do obiektów blob usługi storage tylko wymusza wyłączności w operacje usuwania. Aby usunąć kontener z aktywną dzierżawę, klient musi zawierać identyfikator aktywną dzierżawę za pomocą żądania usunięcia. Wszystkie inne operacje dotyczące kontenera pominięciem na kontenerze dzierżawy w tym identyfikator dzierżawy w tym przypadku są one udostępniane operacji. Jeśli wymagana jest wyłączności aktualizacji (put lub zestaw) lub operacji odczytu następnie deweloperzy upewnić się, że wszyscy klienci korzystali Identyfikatora dzierżawy oraz że tylko jednego klienta w danym momencie ma prawidłową dzierżawę identyfikator.  

Następujące operacje kontenerów umożliwia zarządzanie Współbieżność pesymistyczna dzierżawy:  

* Usuwanie kontenera
* Pobierz właściwości kontenera
* Pobranie metadanych kontenera
* Metadane kontenera zestawu
* Pobieranie listy ACL kontenera
* Ustaw ACL kontenera
* Dzierżawa kontenera  

Aby uzyskać więcej informacji, zobacz:  

* [Określanie warunkowego nagłówki dla operacji usługi obiektów Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Dzierżawa kontenera](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Dzierżawienie obiektu Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Zarządzanie współbieżnością w usłudze Table Service
Usługa table service używa optymistycznej współbieżności kontroli jako domyślne zachowanie podczas pracy z jednostkami, w przeciwieństwie do usługi obiektów blob, w którym jawnie należy wybrać do wykonywania kontroli optymistycznej współbieżności. Różnica między usługami tabel i obiektów blob jest, że można zarządzać tylko zachowanie współbieżności jednostek należy za pomocą usługi obiektów blob można zarządzać współbieżności kontenerów i obiektów blob.  

Używaj optymistycznej współbieżności i sprawdzanie, jeśli inny proces zmodyfikowane jednostką, ponieważ został pobrany z usługi table storage, można użyć wartość elementu ETag, który pojawi się po usłudze table service zwraca jednostkę. Zarys tego procesu jest następująca:  

1. Pobrania jednostki z usługi table storage, odpowiedź zawiera wartość elementu ETag, która określa bieżący identyfikator skojarzony z tej jednostki w usłudze storage.
2. Po zaktualizowaniu jednostki zawierają wartość elementu ETag odebrane w kroku 1 w obowiązkowy **If-Match** nagłówka żądania wysyłane do usługi.
3. Usługa porównuje wartość elementu ETag w żądaniu z bieżącą wartością elementu ETag jednostki.
4. Jeśli bieżąca wartość elementu ETag jednostki różni się od elementu ETag w obowiązkowy **If-Match** nagłówka w żądaniu, usługa zwraca błąd 412 do klienta. Oznacza to, do klienta inny proces został zaktualizowany jednostkę od czasu jej pobrania. klienta.
5. Jeśli bieżąca wartość elementu ETag jednostki jest taka sama jak element ETag w obowiązkowy **If-Match** nagłówka w żądaniu lub **If-Match** nagłówek zawiera symbol wieloznaczny (*), usługa wykonuje żądanej operacji, a następnie aktualizuje bieżącą wartość elementu ETag jednostkę którą chcesz pokazać, że został zaktualizowany.  

Pamiętaj, że w przeciwieństwie do usługi blob service tabeli wymaga klienta do uwzględnienia **If-Match** nagłówek żądania aktualizacji. Istnieje możliwość wymuszenia bezwarunkowe aktualizacji (ostatni składnik zapisywania usługi wins strategii) i obejście kontrolach współbieżności, jeśli klient ustawia **If-Match** nagłówka znak symbolu wieloznacznego (*) w żądaniu.  

Poniższy fragment kodu języka C# zawiera jednostki Klient, która wcześniej została utworzona lub pobrać o swój adres e-mail, aktualizowane. Początkowy wstawiania lub pobrać magazynów operacji wartość elementu ETag w obiekcie klienta, a ponieważ w przykładzie użyto tego samego wystąpienia obiektu podczas wykonywania operacji zamieniania, powoduje automatyczne wysyłanie wartość elementu ETag do usługi tabel, włączenie usługi Sprawdź, czy naruszenie współbieżności. Jeśli inny proces był aktualizowany jednostki w usłudze table storage, usługa zwraca komunikat stanu HTTP 412 (niepowodzenie warunku wstępnego).  Można pobrać pełny przykład: [Zarządzanie współbieżnością za pomocą usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Aby jawnie wyłączyć sprawdzania współbieżności, należy ustawić **ETag** właściwość **pracowników** do obiektu "*" przed wykonaniem operacji Zamień.  

```csharp
customer.ETag = "*";  
```

W poniższej tabeli przedstawiono, jak operacje jednostki tabeli używać wartości tagu ETag:

| Operacja | Zwraca wartość elementu ETag | Wymaga nagłówka żądania If-Match |
|:--- |:--- |:--- |
| Wykonywanie zapytań dotyczących jednostek |Yes |Nie |
| Wstaw jednostkę |Yes |Nie |
| Aktualizuj jednostki |Yes |Yes |
| Scal jednostkę |Yes |Yes |
| Usuń jednostkę |Nie |Yes |
| Wstaw lub zastąp jednostkę |Yes |Nie |
| Wstaw lub scal jednostkę |Yes |Nie |

Należy pamiętać, że **Insert lub Zastąp jednostki** i **Insert lub scalić jednostki** wykonaj operacje *nie* wykonać wszystkie testy współbieżności, ponieważ nie wysyłaj wartość elementu ETag do tabeli Usługa.  

Ogólnie rzecz biorąc deweloperzy korzystający z tabel będą miały optymistycznej współbieżności podczas tworzenia skalowalnych aplikacji. W razie pesymistycznego blokowania jest jednym z podejść deweloperzy mogą korzystać z podczas uzyskiwania dostępu do tabel jest przypisywanie wyznaczonym obiektu blob dla każdej tabeli, a następnie spróbuj przyjmą dzierżawę obiektu blob przed wykonywaniem operacji na tabeli. Takie podejście wymaga aplikacji, aby upewnić się, wszystkie ścieżki dostępu do danych uzyskiwania dzierżawy przed wykonywaniem operacji na tabeli. Należy również zauważyć, czas trwania dzierżawy minimalna to 15 sekund, co wymaga szczególną uwagę w przypadku skalowalności.  

Aby uzyskać więcej informacji, zobacz:  

* [Operacje na jednostkach](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Zarządzanie współbieżnością w usłudze kolejki
Jeden scenariusz, w których współbieżności jest istotna w usłudze kolejkowania jest, której wielu klientów są pobieranie komunikatów z kolejki. Po pobraniu wiadomość z kolejki odpowiedź zawiera komunikat i wartości potwierdzenia pop, która jest wymagana do usuwania komunikatu. Komunikat nie zostanie automatycznie usunięta z kolejki, ale po jej pobraniu, nie jest widoczny dla innych klientów dla interwału czasu określonym przez parametr visibilitytimeout. Klient, który pobiera komunikat powinien usunąć komunikat po przetworzeniu i przed upływem czasu określonego przez TimeNextVisible element odpowiedzi, który jest obliczany na podstawie wartości parametru visibilitytimeout. Wartość visibilitytimeout jest dodawana do czasu, jaką wiadomość zostanie pobrana do określenia wartości TimeNextVisible.  

Usługa kolejki nie jest obsługiwane optymistycznego lub pesymistycznego współbieżności i w tym klientów Przyczyna przetwarzania wiadomości odebrane z kolejką upewnić się, że komunikaty są przetwarzane w sposób idempotentne. Strategii wins ostatni składnik zapisywania jest używany do operacji aktualizacji, takich jak SetQueueServiceProperties, SetQueueMetaData, SetQueueACL i UpdateMessage.  

Aby uzyskać więcej informacji, zobacz:  

* [Interfejs API REST usługi kolejek](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Pobieranie wiadomości](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Zarządzanie współbieżnością w usłudze plików
Usługa plików jest możliwy przy użyciu dwóch różnych punktów końcowych protokołu — SMB i REST. Usługa REST nie jest obsługiwane optymistyczne blokowanie lub pesymistycznego blokowania i wszystkie aktualizacje będą się odbywać w strategii wins ostatni składnik zapisywania. Klienci SMB, które instalować udziałów plików można wykorzystać mechanizmy blokowania systemu plików do zarządzania dostępem do udostępnionych plików — w tym możliwość wykonywania pesymistycznego blokowania. Po otwarciu pliku klienta SMB określa zarówno do uzyskiwania dostępu do plików udziału i trybu. Ustawianie opcji dostępu do plików, "Write" lub "Odczytu/zapisu" wraz z trybu udziału plików "None" spowoduje plik jest zablokowany przez klienta protokołu SMB, przed zamknięciem pliku. Jeśli próba wykonania operacji REST w pliku, w której klient SMB ma plik jest zablokowany usługi REST zwróci stan kodu 409 (konflikt) z kodem błędu SharingViolation.  

Po otwarciu pliku do usunięcia klienta SMB oznacza pliku jako oczekujące usunięcie, aż inny klient SMB otwartymi dojściami w tym pliku zostaną zamknięte. Gdy plik jest oznaczone jako oczekujące na usunięcie, żadnych operacji REST dla tego pliku i zostanie zwrócony kod stanu 409 (konflikt) z kodem błędu SMBDeletePending. Ponieważ istnieje możliwość, że klient SMB do usuwania flagi oczekiwanie na usunięcie przed zamknięciem pliku nie zwróciła kod stanu 404 (nie znaleziono). Innymi słowy kod stanu 404 (nie znaleziono) są oczekiwane tylko, jeśli plik został usunięty. Należy pamiętać o tym, czy plik jest w protokole SMB do czasu usunięcia stanu, zostanie nie uwzględniony w wynikach listę plików. Należy również zauważyć, że operacje REST usuwania plików i katalogów Usuń REST niepodzielne dokłada wszelkich starań i nie powodują w stanie oczekiwanie na usunięcie.  

Aby uzyskać więcej informacji, zobacz:  

* [Zarządzanie plikiem blokad](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Podsumowanie i następne kroki
Usługa Microsoft Azure Storage została zaprojektowana w celu zaspokojenia potrzeb najbardziej złożonych aplikacji online bez wymuszania deweloperów do naruszenia bezpieczeństwa lub zmusza założenia kluczy, takie jak współbieżność i wyjaśnienie pojęcia spójności danych nadchodzące podejmowane dla udzielone.  

Dla aplikacji pełny przykład, do którego odwołuje się ten blog:  

* [Zarządzanie współbieżnością za pomocą usługi Azure Storage — Przykładowa aplikacja](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Aby uzyskać więcej informacji na temat usługi Azure Storage, zobacz:  

* [Strona główna programu Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
* [Wprowadzenie do usługi Azure Storage](storage-introduction.md)
* Wprowadzenie do magazynu [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabeli](../../cosmos-db/table-storage-how-to-use-dotnet.md), [kolejek](../storage-dotnet-how-to-use-queues.md), i [plików](../storage-dotnet-how-to-use-files.md)
* Architektura magazynu — [usługi Azure Storage: Usługi magazynu w chmurze o wysokiej dostępności przy użyciu silnej spójności](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

