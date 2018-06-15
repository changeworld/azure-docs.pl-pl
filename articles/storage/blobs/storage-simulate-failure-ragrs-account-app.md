---
title: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego na platformie Azure | Microsoft Docs
description: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: tamram
ms.openlocfilehash: a86f54d580db6e577b878cb1701c7b969d23c129
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30835606"
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego

Niniejszy samouczek jest drugą częścią serii.  W tym samouczku możesz użyć programu [Fiddler](#simulate-a-failure-with-fiddler) lub [routingu statycznego](#simulate-a-failure-with-an-invalid-static-route) do symulacji błędu żądań kierowanych do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu ](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) — aplikacja będzie odczytywać dane z pomocniczego punktu końcowego.

![Aplikacja scenariusza](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Aby ukończyć ten samouczek, należy wcześniej wykonać czynności opisane w poprzednim samouczku dotyczącym magazynu: [Uzyskiwanie wysokiej dostępności danych aplikacji przy użyciu usługi Azure Storage][previous-tutorial].

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uruchamianie i zatrzymywanie aplikacji
> * Symulowanie błędu przy użyciu programu [Fiddler](#simulate-a-failure-with-fiddler) lub [nieprawidłowej trasy statycznej](#simulate-a-failure-with-an-invalid-static-route) 
> * Symulowanie przywracania podstawowego punktu końcowego


## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić symulację błędu przy użyciu programu Fiddler: 

* Pobierz i [zainstaluj program Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Symulowanie błędu przy użyciu programu Fiddler

Aby symulować błąd przy użyciu programu Fiddler, należy dodać odpowiedź oznaczającą błąd dla żądań kierowanych do podstawowego punktu końcowego konta magazynu RA-GRS w celu symulowania błędu.

Wykonaj poniższe kroki, aby symulować niepowodzenie i przywracanie podstawowego punktu końcowego przy użyciu programu Fiddler.

### <a name="launch-fiddler"></a>Uruchamianie programu Fiddler

Otwórz program Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły**.

![Dostosowywanie zasad programu Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Zostanie uruchomiony edytor ScriptEditor programu Fiddler z plikiem **SampleRules.js**. Ten plik jest używany do dostosowywania programu Fiddler. Wklej następujący przykładowy kod w funkcji `OnBeforeResponse`. Nowy kod jest oznaczony jako komentarz, co gwarantuje, że tworzona przez niego logika nie natychmiast implementowana. Po zakończeniu wybierz pozycję **Plik** i **Zapisz**, aby zapisać zmiany.

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

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Uruchom aplikację w edytorze IDE lub edytorze tekstów. Gdy aplikacja rozpocznie odczyt z podstawowego punktu końcowego, naciśnij **dowolny klawisz** w oknie konsoli, aby wstrzymać jej działanie.

### <a name="simulate-failure"></a>Symulowanie błędu

Po wstrzymaniu aplikacji można usunąć oznaczenie jako komentarza reguły niestandardowej zapisanej w programie Fiddler w poprzednim kroku. Przykładowy kod wyszukuje żądania kierowane do konta magazynu RA-GRS i jeśli ścieżka zawiera nazwę obrazu, `HelloWorld`, zwraca kod odpowiedzi `503 - Service Unavailable`.

Przejdź do programu Fiddler i wybierz kolejno pozycje **Reguły** -> **Dostosuj reguły...**.  Usuń komentarz z poniższych wierszy i zastąp element `STORAGEACCOUNTNAME` nazwą konta magazynu. Wybierz kolejno pozycje **Plik** -> **Zapisz**. 

> [!NOTE]
> Jeśli używasz przykładowej aplikacji w systemie Linux, musisz ponownie uruchomić program Fiddler po każdej edycji pliku **CustomRule.js**, aby program Fiddler zainstalował logikę niestandardową. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Aby wznowić działanie aplikacji, naciśnij **dowolny klawisz**.

Po ponownym uruchomieniu aplikacji zaczną występować błędy żądań kierowanych do podstawowego punktu końcowego. Aplikacja próbuje ponownie połączyć się z podstawowym punktem końcowym 5 razy. Po osiągnięciu progu błędów w pięciu próbach żąda ona obrazu od pomocniczego punktu końcowego tylko do odczytu. Po pomyślnym pobraniu obrazu 20 razy z pomocniczego punktu końcowego aplikacja próbuje połączyć się z podstawowym punktem końcowym. Jeśli podstawowy punkt końcowy jest nadal nieosiągalny, aplikacja wznawia odczyt z pomocniczego punktu końcowego. Jest to wzorzec [wyłącznika](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) opisany w poprzednim samouczku.

![Wklejanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

Po ustawieniu niestandardowej reguły programu Fiddler w poprzednim kroku żądania kierowane do podstawowego punktu końcowego kończą się niepowodzeniem. Aby symulować ponowne działanie podstawowego punktu końcowego, usuń logikę w celu przeprowadzenia iniekcji błędu `503`.

Aby zatrzymać aplikację, naciśnij **dowolny klawisz**.

Przejdź do programu Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły...**.  Dodaj komentarz do logiki niestandardowej lub usuń ją z funkcji `OnBeforeResponse`, pozostawiając funkcję domyślną. Wybierz kolejno pozycje **Plik** i **Zapisz**, aby zapisać zmiany.

![Usuwanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Po zakończeniu naciśnij **dowolny klawisz**, aby wznowić działanie aplikacji. Aplikacja będzie kontynuować odczytywanie z podstawowego punktu końcowego do momentu osiągnięcia 999 trafień.

![Wznawianie działania aplikacji](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Symulowanie błędu przy użyciu nieprawidłowej trasy statycznej 
Możesz utworzyć nieprawidłową trasę statyczną dla wszystkich żądań kierowanych do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). W tym samouczku host lokalny jest używany jako brama na potrzeby kierowania żądań do konta magazynu. Dzięki użyciu hosta lokalnego brama powoduje, że wszystkie żądania kierowane do podstawowego punktu końcowego konta magazynu wywołują sprzężenie zwrotne wewnątrz hosta, co następnie prowadzi do wystąpienia błędu. Wykonaj poniższe kroki, aby symulować błąd i przywracanie podstawowego punktu końcowego przy użyciu nieprawidłowej trasy statycznej. 

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Uruchom aplikację w edytorze IDE lub edytorze tekstów. Gdy aplikacja rozpocznie odczyt z podstawowego punktu końcowego, naciśnij **dowolny klawisz** w oknie konsoli, aby wstrzymać jej działanie. 

### <a name="simulate-failure"></a>Symulowanie błędu

Po wstrzymaniu działania aplikacji uruchom wiersz polecenia w systemie Windows jako administrator lub uruchom terminal jako katalog główny w systemie Linux. Aby uzyskać informacje na temat domeny podstawowego punktu końcowego konta magazynu, wprowadź poniższe polecenie w wierszu polecenia lub terminalu.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Zastąp wartość `STORAGEACCOUNTNAME` nazwą konta magazynu. Skopiuj adres IP konta magazynu do edytora tekstów w celu późniejszego użycia. Aby uzyskać adres IP hosta lokalnego, wpisz `ipconfig` w wierszu polecenia systemu Windows lub `ifconfig` w terminalu systemu Linux. 

Aby dodać trasę statyczną dla hosta docelowego, wpisz poniższe polecenie w wierszu polecenia systemu Windows lub terminalu systemu Linux. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Zastąp element `<destination_ip>` adresem IP konta magazynu, a element `<gateway_ip>` adresem IP hosta lokalnego. Aby wznowić działanie aplikacji, naciśnij **dowolny klawisz**.

Po ponownym uruchomieniu aplikacji zaczną występować błędy żądań kierowanych do podstawowego punktu końcowego. Aplikacja próbuje ponownie połączyć się z podstawowym punktem końcowym 5 razy. Po osiągnięciu progu błędów w pięciu próbach żąda ona obrazu od pomocniczego punktu końcowego tylko do odczytu. Po pomyślnym pobraniu obrazu 20 razy z pomocniczego punktu końcowego aplikacja próbuje połączyć się z podstawowym punktem końcowym. Jeśli podstawowy punkt końcowy jest nadal nieosiągalny, aplikacja wznawia odczyt z pomocniczego punktu końcowego. Jest to wzorzec [wyłącznika](/azure/architecture/patterns/circuit-breaker.md) opisany w poprzednim samouczku.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

Aby symulować ponowne działanie podstawowego punktu końcowego, usuń trasę statyczną podstawowego punktu końcowego z tabeli routingu. Dzięki temu wszystkie żądania do podstawowego punktu końcowego będą kierowane za pośrednictwem bramy domyślnej. 

Aby usunąć trasę statyczną hosta docelowego, konto magazynu, wpisz poniższe polecenie w wierszu polecenia systemu Windows lub terminalu systemu Linux. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip>

---

Naciśnij **dowolny klawisz**, aby wznowić działanie aplikacji. Aplikacja będzie kontynuować odczytywanie z podstawowego punktu końcowego do momentu osiągnięcia 999 trafień.

![Wznawianie działania aplikacji](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>Następne kroki

W drugiej części serii omówiono czynności dotyczące symulowania błędu w celu przetestowania magazynu geograficznie nadmiarowego do odczytu, takie jak:

> [!div class="checklist"]
> * Uruchamianie i zatrzymywanie aplikacji
> * Symulowanie błędu przy użyciu programu [Fiddler](#simulate-a-failure-with-fiddler) lub [nieprawidłowej trasy statycznej](#simulate-a-failure-with-an-invalid-static-route) 
> * Symulowanie przywracania podstawowego punktu końcowego

Przeczytaj poniższy artykuł, aby dowiedzieć się więcej na temat sposobu działania magazynu RA-GRS (i powiązanych czynników ryzyka).

> [!div class="nextstepaction"]
> [Projektowanie aplikacji HA przy użyciu magazynu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md