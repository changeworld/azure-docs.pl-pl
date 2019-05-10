---
title: 'Samouczek: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego na platformie Azure | Microsoft Docs'
description: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 1f5c404e410ded2714be761e35060f3c07379bd3
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508096"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Samouczek: Symulowanie błędu podczas uzyskiwania dostępu do odczytu do magazynu nadmiarowego

Ten samouczek jest drugą częścią serii. Poznasz w nim korzyści wynikające z [dostępu do odczytu z magazynu geograficznie nadmiarowego](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) poprzez symulowanie błędu.

Aby symulować błąd, możesz użyć [routingu statycznego](#simulate-a-failure-with-an-invalid-static-route) lub [Fiddler](#simulate-a-failure-with-fiddler). Obie metody umożliwi symulacji błędu żądań kierowanych do podstawowego punktu końcowego usługi [geograficznie nadmiarowego](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) konta magazynu (RA-GRS), powodując aplikacji zamiast tego odczyt z pomocniczego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uruchamianie i zatrzymywanie aplikacji
> * Symulowanie błędu przy użyciu [nieprawidłowej trasy statycznej](#simulate-a-failure-with-an-invalid-static-route) lub [programu Fiddler](#simulate-a-failure-with-fiddler)
> * Symulowanie przywracania podstawowego punktu końcowego

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem ukończ poprzedni samouczek: [Uzyskiwanie wysokiej dostępności danych aplikacji przy użyciu usługi Azure Storage][previous-tutorial].

Symulowanie błędu przy użyciu routingu statycznego, zostanie użyty wiersz polecenia z podwyższonym.

Symulowanie błędu przy użyciu programu Fiddler, należy pobrać i [Zainstaluj program Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Symulowanie błędu przy użyciu nieprawidłowej trasy statycznej

Możesz utworzyć nieprawidłową trasę statyczną dla wszystkich żądań kierowanych do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). W tym samouczku host lokalny jest używany jako brama na potrzeby kierowania żądań do konta magazynu. Dzięki użyciu hosta lokalnego brama powoduje, że wszystkie żądania kierowane do podstawowego punktu końcowego konta magazynu wywołują sprzężenie zwrotne wewnątrz hosta, co następnie prowadzi do wystąpienia błędu. Wykonaj poniższe kroki, aby symulować błąd i przywracanie podstawowego punktu końcowego przy użyciu nieprawidłowej trasy statycznej.

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Postępuj zgodnie z instrukcjami w [poprzedniego samouczka] [ previous-tutorial] do uruchomienia przykładu i Pobierz plik testu, potwierdzenie, że pochodzi on z magazynu głównego. W zależności od platformy docelowej możesz ręcznie wstrzymać próbki lub oczekiwania w wierszu.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, otwórz wiersz polecenia na Windows jako administrator lub uruchom terminal jako katalog główny w systemie Linux.

Uzyskać informacje na temat domeny podstawowego punktu końcowego konta magazynu, wprowadzając następujące polecenie w wierszu polecenia terminala, zastępując `STORAGEACCOUNTNAME` nazwą konta magazynu.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Skopiuj adres IP konta magazynu do edytora tekstów w celu późniejszego użycia.

Aby uzyskać adres IP hosta lokalnego, wpisz `ipconfig` w wierszu polecenia systemu Windows lub `ifconfig` w terminalu systemu Linux.

Aby dodać trasę statyczną hosta docelowego, wpisz następujące polecenie w wierszu polecenia Windows lub terminalu systemu Linux, zastępując `<destination_ip>` za pomocą adresu IP konta magazynu i `<gateway_ip>` za pomocą adresu IP hosta lokalnego.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

W oknie Uruchamianie przykład wznowić działanie aplikacji, lub naciśnij odpowiedni klawisz, aby pobrać przykładowy plik i upewnij się, że pochodzi on z magazynu pomocniczego. Następnie można ponownie wstrzymać próbki lub oczekiwania w wierszu.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

Aby zasymulować podstawowego punktu końcowego, staje się ponownie funkcjonalności, należy usunąć z tabeli routingu nieprawidłowej trasy statycznej. Dzięki temu wszystkie żądania do podstawowego punktu końcowego będą kierowane za pośrednictwem bramy domyślnej. Wpisz następujące polecenie w wierszu polecenia Windows lub terminalu systemu Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Następnie można wznowić aplikacji lub naciśnij odpowiedni klawisz, aby pobrać próbkę plik ponownie, ten czas potwierdzenie, że ponownie pochodzą z magazynu głównego.

## <a name="simulate-a-failure-with-fiddler"></a>Symulowanie błędu przy użyciu programu Fiddler

Aby symulować błąd przy użyciu programu Fiddler, należy dodać odpowiedź oznaczającą błąd dla żądań kierowanych do podstawowego punktu końcowego konta magazynu RA-GRS.

Poniższe sekcje pokazują, jak zasymulować błąd i przywrócić podstawowy punkt końcowy przy użyciu programu Fiddler.

### <a name="launch-fiddler"></a>Uruchamianie programu Fiddler

Otwórz program Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły**.

![Dostosowywanie zasad programu Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Zostanie uruchomiony edytor ScriptEditor programu Fiddler i wyświetli plik **SampleRules.js**. Ten plik jest używany do dostosowywania programu Fiddler.

Wklej następujący przykładowy kod w `OnBeforeResponse` funkcjonować i zastępując `STORAGEACCOUNTNAME` nazwą konta magazynu. W zależności od próbki, konieczne może być Zastąp `HelloWorld` o nazwie pliku testowego (lub prefiks, takie jak `sampleFile`) pobierany. Nowy kod jest oznaczone jako komentarz, aby upewnić się, że nie działa natychmiast.

Po zakończeniu wybierz pozycję **Plik** i **Zapisz**, aby zapisać swoje zmiany. Zamykaj okna ScriptEditor do użycia w kolejnych krokach.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Wklejanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Postępuj zgodnie z instrukcjami w [poprzedniego samouczka] [ previous-tutorial] do uruchomienia przykładu i Pobierz plik testu, potwierdzenie, że pochodzi on z magazynu głównego. W zależności od platformy docelowej możesz ręcznie wstrzymać próbki lub oczekiwania w wierszu.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, przełącz się do programu Fiddler i usuń znaczniki komentarza reguły niestandardowej zapisanej w `OnBeforeResponse` funkcji. Pamiętaj o wybraniu **pliku** i **Zapisz** Aby zapisać zmiany, aby reguła zaczną obowiązywać. Ten kod wyszukuje żądania do konta magazynu RA-GRS i jeśli ścieżka zawiera nazwę przykładowego pliku zwraca kod odpowiedzi `503 - Service Unavailable`.

W oknie Uruchamianie przykład wznowić działanie aplikacji, lub naciśnij odpowiedni klawisz, aby pobrać przykładowy plik i upewnij się, że pochodzi on z magazynu pomocniczego. Następnie można ponownie wstrzymać próbki lub oczekiwania w wierszu.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

W narzędziu Fiddler należy usunąć lub ponownie komentarz reguły niestandardowej. Wybierz **pliku** i **Zapisz** aby upewnić się, zasady nie będą już obowiązywały.

W oknie Uruchamianie przykład wznowić działanie aplikacji, lub naciśnij odpowiedni klucz Pobierz przykładowy plik i upewnij się, że pochodzi on z magazynu głównego jeszcze raz. Następnie możesz wyjść próbki.

## <a name="next-steps"></a>Kolejne kroki

W drugiej części serii omówiono czynności dotyczące symulowania błędu w celu przetestowania magazynu geograficznie nadmiarowego do odczytu.

Aby dowiedzieć się więcej na temat sposobu działania magazynu RA-GRS i powiązanych z nim czynników ryzyka, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Projektowanie aplikacji HA przy użyciu magazynu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
