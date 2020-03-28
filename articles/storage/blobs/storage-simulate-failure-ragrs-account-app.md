---
title: Samouczek — symulowanie błędu podczas odczytywania danych z regionu podstawowego
titleSuffix: Azure Storage
description: Symuluj błąd podczas odczytu danych z regionu podstawowego, gdy dla konta magazynu jest włączony magazyn geograficzny dostępu do odczytu (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061291"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Samouczek: symulowanie błędu podczas odczytywania danych z regionu podstawowego

Ten samouczek jest drugą częścią serii. W nim można dowiedzieć się o korzyściach płynących z [magazynu geograficznego dostępu do odczytu](../common/storage-redundancy.md) (RA-GRS) przez symulowanie awarii.

Aby symulować błąd, można użyć trasy [statycznej](#simulate-a-failure-with-an-invalid-static-route) lub [skrzypka](#simulate-a-failure-with-fiddler). Obie metody umożliwiają symulowanie awarii żądań do podstawowego punktu końcowego konta magazynu [geob nadmiarowego dostępu](../common/storage-redundancy.md) do odczytu (RA-GRS), powodując zamiast tego odczyt aplikacji z pomocniczego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uruchamianie i zatrzymywanie aplikacji
> * Symulowanie awarii z [nieprawidłową trasą statyczną](#simulate-a-failure-with-an-invalid-static-route) lub [skrzypkiem](#simulate-a-failure-with-fiddler)
> * Symulowanie przywracania podstawowego punktu końcowego

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj poprzedni samouczek: [Udostępnij dane aplikacji na wysokim obszarze za pomocą usługi Azure Storage][previous-tutorial].

Aby symulować błąd z routingiem statycznym, należy użyć wiersza polecenia z podwyższonym poziomem uprawnień.

Aby symulować błąd przy użyciu fiddlera, pobierz i [zainstaluj Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Symulowanie błędu przy użyciu nieprawidłowej trasy statycznej

Możesz utworzyć nieprawidłową trasę statyczną dla wszystkich żądań kierowanych do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (RA-GRS). W tym samouczku host lokalny jest używany jako brama na potrzeby kierowania żądań do konta magazynu. Dzięki użyciu hosta lokalnego brama powoduje, że wszystkie żądania kierowane do podstawowego punktu końcowego konta magazynu wywołują sprzężenie zwrotne wewnątrz hosta, co następnie prowadzi do wystąpienia błędu. Wykonaj poniższe kroki, aby symulować błąd i przywracanie podstawowego punktu końcowego przy użyciu nieprawidłowej trasy statycznej.

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Użyj instrukcji w [poprzednim samouczku,][previous-tutorial] aby uruchomić próbkę i pobrać plik testowy, potwierdzając, że pochodzi z magazynu podstawowego. W zależności od platformy docelowej można następnie ręcznie wstrzymać próbkę lub poczekać z monitem.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, otwórz wiersz polecenia w systemie Windows jako administrator lub uruchom terminal jako root w systemie Linux.

Uzyskaj informacje o domenie podstawowego punktu końcowego konta magazynu, wprowadzając `STORAGEACCOUNTNAME` następujące polecenie w wierszu polecenia lub terminalu, zastępując nazwą konta magazynu.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Skopiuj adres IP konta magazynu do edytora tekstów w celu późniejszego użycia.

Aby uzyskać adres IP hosta lokalnego, wpisz `ipconfig` w wierszu polecenia systemu Windows lub `ifconfig` w terminalu systemu Linux.

Aby dodać trasę statyczną dla hosta docelowego, wpisz następujące polecenie w `<destination_ip>` wierszu polecenia systemu `<gateway_ip>` Windows lub terminalu Systemu Linux, zastępując go adresem IP konta magazynu i adresem IP lokalnego hosta.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

W oknie z uruchomionym przykładem wznowić aplikację lub nacisnąć odpowiedni klawisz, aby pobrać przykładowy plik i potwierdzić, że pochodzi z magazynu pomocniczego. Następnie można ponownie wstrzymać próbkę lub poczekać na monit.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

Aby ponownie symulować ponowne stanie się funkcjonalne pierwotnego punktu końcowego, usuń nieprawidłową trasę statyczną z tabeli routingu. Dzięki temu wszystkie żądania do podstawowego punktu końcowego będą kierowane za pośrednictwem bramy domyślnej. Wpisz następujące polecenie w wierszu polecenia systemu Windows lub terminalu systemu Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Następnie można wznowić aplikację lub nacisnąć odpowiedni klucz, aby ponownie pobrać przykładowy plik, tym razem potwierdzając, że ponownie pochodzi z magazynu podstawowego.

## <a name="simulate-a-failure-with-fiddler"></a>Symulowanie błędu przy użyciu programu Fiddler

Aby symulować błąd przy użyciu programu Fiddler, należy dodać odpowiedź oznaczającą błąd dla żądań kierowanych do podstawowego punktu końcowego konta magazynu RA-GRS.

Poniższe sekcje pokazują, jak zasymulować błąd i przywrócić podstawowy punkt końcowy przy użyciu programu Fiddler.

### <a name="launch-fiddler"></a>Uruchamianie programu Fiddler

Otwórz program Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły**.

![Dostosowywanie zasad programu Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Zostanie uruchomiony edytor ScriptEditor programu Fiddler i wyświetli plik **SampleRules.js**. Ten plik jest używany do dostosowywania programu Fiddler.

Wklej następujący przykładowy `OnBeforeResponse` kod w `STORAGEACCOUNTNAME` funkcji, zastępując nazwą konta magazynu. W zależności od próbki może być `HelloWorld` również konieczne zastąpienie nazwy pobieranego pliku `sampleFile`testowego (lub prefiksu, takiego jak). Nowy kod jest komentowany, aby upewnić się, że nie działa natychmiast.

Po zakończeniu wybierz pozycję **Plik** i **Zapisz**, aby zapisać swoje zmiany. Pozostaw okno ScriptEditor otwarte do użycia w poniższych krokach.

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

Użyj instrukcji w [poprzednim samouczku,][previous-tutorial] aby uruchomić próbkę i pobrać plik testowy, potwierdzając, że pochodzi z magazynu podstawowego. W zależności od platformy docelowej można następnie ręcznie wstrzymać próbkę lub poczekać z monitem.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, przełącz się z powrotem do Fiddler i odkomentuj niestandardową regułę zapisaną `OnBeforeResponse` w funkcji. Pamiętaj, aby wybrać **opcję Plik** i **Zapisz,** aby zapisać zmiany, aby reguła została zaaucna. Ten kod wyszukuje żądania do konta magazynu RA-GRS i, jeśli ścieżka zawiera `503 - Service Unavailable`nazwę przykładowego pliku, zwraca kod odpowiedzi .

W oknie z uruchomionym przykładem wznowić aplikację lub nacisnąć odpowiedni klawisz, aby pobrać przykładowy plik i potwierdzić, że pochodzi z magazynu pomocniczego. Następnie można ponownie wstrzymać próbkę lub poczekać na monit.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

W fiddler, usuń lub skomentować regułę niestandardową ponownie. Wybierz **opcję Plik** i **Zapisz,** aby upewnić się, że reguła nie będzie już obowiązywać.

W oknie z uruchomionym przykładem wznowić aplikację lub nacisnąć odpowiedni klawisz, aby pobrać przykładowy plik i potwierdzić, że pochodzi z magazynu podstawowego po raz kolejny. Następnie można wyjść z próbki.

## <a name="next-steps"></a>Następne kroki

W drugiej części serii omówiono czynności dotyczące symulowania błędu w celu przetestowania magazynu geograficznie nadmiarowego do odczytu.

Aby dowiedzieć się więcej na temat sposobu działania magazynu RA-GRS i powiązanych z nim czynników ryzyka, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Projektowanie aplikacji HA przy użyciu magazynu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
