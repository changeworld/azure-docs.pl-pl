---
title: 'Samouczek: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego na platformie Azure | Microsoft Docs'
description: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650244"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Samouczek: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego

Ten samouczek jest drugą częścią serii. Poznasz w nim korzyści wynikające z [dostępu do odczytu z magazynu geograficznie nadmiarowego](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) poprzez symulowanie błędu.

Aby zasymulować błąd, możesz użyć programu [Fiddler](#simulate-a-failure-with-fiddler) lub [routingu statycznego](#simulate-a-failure-with-an-invalid-static-route). Każda metoda umożliwi symulację błędu żądań kierowanych do podstawowego punktu końcowego konta magazynu [dostępu do odczytu z magazynu geograficznie nadmiarowego](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS), powodując, że aplikacja zamiast tego będzie odczytywać z pomocniczego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uruchamianie i zatrzymywanie aplikacji
> * Symulowanie błędu przy użyciu programu [Fiddler](#simulate-a-failure-with-fiddler) lub [nieprawidłowej trasy statycznej](#simulate-a-failure-with-an-invalid-static-route) 
> * Symulowanie przywracania podstawowego punktu końcowego

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem ukończ poprzedni samouczek: [Uzyskiwanie wysokiej dostępności danych aplikacji przy użyciu usługi Azure Storage][previous-tutorial].

Aby przeprowadzić symulację błędu przy użyciu programu Fiddler: 

* Pobierz i [zainstaluj program Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Symulowanie błędu przy użyciu programu Fiddler

Aby symulować błąd przy użyciu programu Fiddler, należy dodać odpowiedź oznaczającą błąd dla żądań kierowanych do podstawowego punktu końcowego konta magazynu RA-GRS.

Poniższe sekcje pokazują, jak zasymulować błąd i przywrócić podstawowy punkt końcowy przy użyciu programu Fiddler.

### <a name="launch-fiddler"></a>Uruchamianie programu Fiddler

Otwórz program Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły**.

![Dostosowywanie zasad programu Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Zostanie uruchomiony edytor ScriptEditor programu Fiddler i wyświetli plik **SampleRules.js**. Ten plik jest używany do dostosowywania programu Fiddler.

Wklej następujący przykładowy kod w funkcji `OnBeforeResponse`. Nowy kod jest oznaczony jako komentarz, co gwarantuje, że tworzona przez niego logika nie natychmiast implementowana.

Po zakończeniu wybierz pozycję **Plik** i **Zapisz**, aby zapisać swoje zmiany.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Wklejanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="interrupting-the-application"></a>Zakłócania pracy aplikacji

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python i Java w wersji 7](#tab/dotnet-python-java-v7)

Uruchom aplikację w swoim edytorze IDE lub powłoce.

Gdy aplikacja rozpocznie odczyt z podstawowego punktu końcowego, naciśnij **dowolny klawisz** w oknie konsoli, aby wstrzymać jej działanie.

![Aplikacja scenariusza](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java w wersji 10](#tab/Java-v10)

Uruchom aplikację w swoim edytorze IDE lub powłoce.

Ponieważ możesz kontrolować aplikację przykładową, nie musisz jej przerywać, aby zasymulować błąd. Upewnij się, że plik został przekazany do Twojego konta magazynu, uruchamiając aplikację przykładową i wprowadzając **P**.

![Aplikacja scenariusza](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, usuń komentarz reguły niestandardowej, którą wcześniej zapisaliśmy w programie Fiddler.

Przykładowy kod wyszukuje żądania kierowane do konta magazynu RA-GRS i jeśli ścieżka zawiera nazwę pliku `HelloWorld`, zwraca kod odpowiedzi `503 - Service Unavailable`.

Przejdź do programu Fiddler i wybierz kolejno pozycje **Reguły** -> **Dostosuj reguły...**.

Usuń komentarz z poniższych wierszy i zastąp element `STORAGEACCOUNTNAME` nazwą swojego konta magazynu. Wybierz kolejno pozycje **Plik** -> **Zapisz**. 

> [!NOTE]
> Jeśli używasz przykładowej aplikacji w systemie Linux, musisz ponownie uruchomić program Fiddler po każdej edycji pliku **CustomRule.js**, aby program Fiddler zainstalował logikę niestandardową.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python i Java w wersji 7](#tab/dotnet-python-java-v7)

Aby wznowić działanie aplikacji, naciśnij **dowolny klawisz**.

Po ponownym uruchomieniu aplikacji zaczną występować błędy żądań kierowanych do podstawowego punktu końcowego. Aplikacja próbuje ponownie połączyć się z podstawowym punktem końcowym 5 razy. Po osiągnięciu progu błędów w pięciu próbach żąda ona obrazu od pomocniczego punktu końcowego tylko do odczytu. Po pomyślnym 20-krotnym pobraniu obrazu z pomocniczego punktu końcowego aplikacja spróbuje połączyć się z podstawowym punktem końcowym. Jeśli podstawowy punkt końcowy jest nadal nieosiągalny, aplikacja wznawia odczyt z pomocniczego punktu końcowego.

Jest to wzorzec [wyłącznika](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) opisany w poprzednim samouczku.

![Wklejanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java w wersji 10](#tab/Java-v10)

Teraz, gdy został wprowadzony błąd, wprowadź **G**, aby go przetestować.

Uzyskasz wtedy informacje, że korzysta on z dodatkowego potoku, w przeciwieństwie do podstawowego potoku.

---

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python i Java w wersji 7](#tab/dotnet-python-java-v7)

Po ustawieniu niestandardowej reguły programu Fiddler w poprzednim kroku żądania kierowane do podstawowego punktu końcowego kończą się niepowodzeniem.

Aby symulować ponowne działanie podstawowego punktu końcowego, usuń logikę w celu przeprowadzenia iniekcji błędu `503`.

Aby zatrzymać aplikację, naciśnij **dowolny klawisz**.

Przejdź do programu Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły...**. 

Dodaj komentarz do logiki niestandardowej lub usuń ją z funkcji `OnBeforeResponse`, pozostawiając funkcję domyślną.

Wybierz kolejno pozycje **Plik** i **Zapisz**, aby zapisać zmiany.

![Usuwanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Po zakończeniu naciśnij **dowolny klawisz**, aby wznowić działanie aplikacji. Aplikacja będzie kontynuować odczytywanie z podstawowego punktu końcowego do momentu osiągnięcia 999 trafień.

![Wznawianie działania aplikacji](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java w wersji 10](#tab/Java-v10)

Po ustawieniu niestandardowej reguły programu Fiddler w poprzednim kroku żądania kierowane do podstawowego punktu końcowego kończą się niepowodzeniem.

Aby symulować ponowne działanie podstawowego punktu końcowego, usuń logikę w celu przeprowadzenia iniekcji błędu `503`.

Przejdź do programu Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły...**.  Dodaj komentarz do logiki niestandardowej lub usuń ją z funkcji `OnBeforeResponse`, pozostawiając funkcję domyślną.

Wybierz kolejno pozycje **Plik** i **Zapisz**, aby zapisać zmiany.

Po zakończeniu wprowadź **G**, aby przetestować pobieranie. Aplikacja będzie zgłaszać, że teraz ponownie używa podstawowego potoku.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Symulowanie błędu przy użyciu nieprawidłowej trasy statycznej

Możesz utworzyć nieprawidłową trasę statyczną dla wszystkich żądań kierowanych do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). W tym samouczku host lokalny jest używany jako brama na potrzeby kierowania żądań do konta magazynu. Dzięki użyciu hosta lokalnego brama powoduje, że wszystkie żądania kierowane do podstawowego punktu końcowego konta magazynu wywołują sprzężenie zwrotne wewnątrz hosta, co następnie prowadzi do wystąpienia błędu. Wykonaj poniższe kroki, aby symulować błąd i przywracanie podstawowego punktu końcowego przy użyciu nieprawidłowej trasy statycznej. 

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python i Java w wersji 7](#tab/dotnet-python-java-v7)

Uruchom aplikację w swoim edytorze IDE lub powłoce. Gdy aplikacja rozpocznie odczyt z podstawowego punktu końcowego, naciśnij **dowolny klawisz** w oknie konsoli, aby wstrzymać jej działanie.

# <a name="java-v10tabjava-v10"></a>[Java w wersji 10](#tab/Java-v10)

Ponieważ możesz kontrolować aplikację przykładową, nie musisz jej przerywać, aby przetestować błąd.

Upewnij się, że plik został przekazany do Twojego konta magazynu, uruchamiając aplikację przykładową i wprowadzając **P**.

---

### <a name="simulate-failure"></a>Symulowanie błędu

Po wstrzymaniu działania aplikacji uruchom wiersz polecenia w systemie Windows jako administrator lub uruchom terminal jako katalog główny w systemie Linux.

Uzyskaj informacje na temat domeny podstawowego punktu końcowego konta magazynu, wprowadzając poniższe polecenie w wierszu polecenia lub terminalu.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Zastąp wartość `STORAGEACCOUNTNAME` nazwą konta magazynu. Skopiuj adres IP konta magazynu do edytora tekstów w celu późniejszego użycia.

Aby uzyskać adres IP hosta lokalnego, wpisz `ipconfig` w wierszu polecenia systemu Windows lub `ifconfig` w terminalu systemu Linux. 

Aby dodać trasę statyczną dla hosta docelowego, wpisz poniższe polecenie w wierszu polecenia systemu Windows lub terminalu systemu Linux. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route add <destination_ip> <gateway_ip>`

Zastąp element `<destination_ip>` adresem IP konta magazynu, a element `<gateway_ip>` adresem IP hosta lokalnego.

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python i Java w wersji 7](#tab/dotnet-python-java-v7)

Aby wznowić działanie aplikacji, naciśnij **dowolny klawisz**.

Po ponownym uruchomieniu aplikacji zaczną występować błędy żądań kierowanych do podstawowego punktu końcowego. Aplikacja próbuje ponownie połączyć się z podstawowym punktem końcowym pięć razy. Po osiągnięciu progu błędów w pięciu próbach żąda ona obrazu od pomocniczego punktu końcowego tylko do odczytu. Po pomyślnym pobraniu obrazu 20 razy z pomocniczego punktu końcowego aplikacja próbuje połączyć się z podstawowym punktem końcowym. Jeśli podstawowy punkt końcowy jest nadal nieosiągalny, aplikacja wznawia odczyt z pomocniczego punktu końcowego. Jest to wzorzec [wyłącznika](/azure/architecture/patterns/circuit-breaker) opisany w poprzednim samouczku.

# <a name="java-v10tabjava-v10"></a>[Java w wersji 10](#tab/Java-v10)

Teraz, gdy został wprowadzony błąd, wprowadź **G**, aby go przetestować. Uzyskasz wtedy informacje, że korzysta on z dodatkowego potoku, w przeciwieństwie do podstawowego potoku.

---

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

Aby symulować ponowne działanie podstawowego punktu końcowego, usuń trasę statyczną podstawowego punktu końcowego z tabeli routingu. Dzięki temu wszystkie żądania do podstawowego punktu końcowego będą kierowane za pośrednictwem bramy domyślnej.

Aby usunąć trasę statyczną hosta docelowego, konto magazynu, wpisz poniższe polecenie w wierszu polecenia systemu Windows lub terminalu systemu Linux.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a>Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python i Java w wersji 7](#tab/dotnet-python-java-v7)

Naciśnij **dowolny klawisz**, aby wznowić działanie aplikacji. Aplikacja będzie kontynuować odczytywanie z podstawowego punktu końcowego do momentu osiągnięcia 999 trafień.

![Wznawianie działania aplikacji](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java w wersji 10](#tab/Java-v10)

Wprowadź **G**, aby przetestować pobieranie. Aplikacja będzie zgłaszać, że teraz ponownie używa podstawowego potoku.

![Wznawianie działania aplikacji](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>Kolejne kroki

W drugiej części serii omówiono czynności dotyczące symulowania błędu w celu przetestowania magazynu geograficznie nadmiarowego do odczytu.

Aby dowiedzieć się więcej na temat sposobu działania magazynu RA-GRS i powiązanych z nim czynników ryzyka, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Projektowanie aplikacji HA przy użyciu magazynu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
