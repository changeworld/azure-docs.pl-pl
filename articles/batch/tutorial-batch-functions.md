---
title: Wyzwalacz zadania usługi Batch przy użyciu usługi Azure Functions
description: Samouczek — dotyczy optyczne rozpoznawanie znaków zeskanowane dokumenty ponieważ są one dodawane do magazynu obiektów blob
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342044"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Samouczek: Wyzwalacz zadania usługi Batch przy użyciu usługi Azure Functions

W tym samouczku dowiesz się, jak wyzwolić zadanie usługi Batch przy użyciu usługi Azure Functions. Omówimy przykładowi, w którym dokumenty dodawane do kontenera obiektów blob usługi Azure Storage są optyczne rozpoznawanie znaków (OCR) zastosowanych do nich za pośrednictwem usługi Azure Batch. Aby usprawnić przetwarzania optyczne rozpoznawanie znaków, firma Microsoft służy do konfigurowania funkcji platformy Azure, który uruchamia zadanie wsadowe optyczne rozpoznawanie znaków w każdym razem, gdy plik zostanie dodany do kontenera obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Batch i połączone konto usługi Azure Storage. Zobacz [Utwórz konto usługi Batch](quick-create-portal.md#create-a-batch-account) Aby uzyskać więcej informacji na temat sposobu tworzenia i łączenia kont.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Tworzenie puli usługi Batch i zadanie usługi Batch za pomocą Eksploratora usługi Batch

W tej sekcji użyjesz programu Batch Explorer do tworzenia puli usługi Batch i zadanie usługi Batch, które będą uruchamiane zadania optyczne rozpoznawanie znaków. 

### <a name="create-a-pool"></a>Tworzenie puli

1. Zaloguj się do Eksploratora usługi Batch przy użyciu swoich poświadczeń platformy Azure.
1. Utwórz pulę, wybierając **pule** na lewym pasku bocznym, a następnie **Dodaj** przycisku powyżej formularza wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Użyjemy `ocr-pool` w tym przykładzie.
    1. Ustaw typ skalowania **ustalony rozmiar**i ustaw liczbę węzłów dedykowanych do 3.
    1. Wybierz **Ubuntu 18.04-LTS** jako system operacyjny.
    1. Wybierz `Standard_f2s_v2` jako rozmiar maszyny wirtualnej.
    1. Włącz zadanie podrzędne uruchamiania i Dodaj polecenie `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Pamiętaj ustawić tożsamość użytkownika jako **zadań domyślny użytkownik (Administrator)** , co pozwala zadania uruchamiania uwzględnić poleceń `sudo`.
    1. Kliknij przycisk **OK**.
### <a name="create-a-job"></a>Tworzenie zadania

1. Utwórz zadanie w puli, wybierając **zadania** na lewym pasku bocznym, a następnie **Dodaj** przycisku powyżej formularza wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Użyjemy `ocr-job` w tym przykładzie.
    1. Wartość puli `ocr-pool`, lub niezależnie od rodzaju nadaj nazwę wybraną dla puli.
    1. Kliknij przycisk **OK**.


## <a name="create-blob-containers"></a>Tworzenie kontenerów obiektów blob

W tym miejscu utworzymy kontenery obiektów blob, które będą przechowywać dane wejściowe i wyjściowe pliki dla zadania usługi Batch optyczne rozpoznawanie znaków.

1. Zaloguj się do Eksploratora usługi Storage przy użyciu swoich poświadczeń platformy Azure.
1. Za pomocą konta magazynu, połączone z Twoim kontem usługi Batch, Utwórz dwa kontenery obiektów blob (dla plików wejściowych, jeden dla plików wyjściowych), wykonując kroki opisane w temacie [Utwórz kontener obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

W tym przykładzie nosi nazwę kontenera wejściowego `input` i to, gdzie wszystkie dokumenty bez optyczne rozpoznawanie znaków początkowo są przekazywane do przetworzenia. Nosi nazwę kontenera danych wyjściowych `output` i gdzie zapisuje przetworzonych dokumentów za pomocą oprogramowania OCR w zadaniu wsadowym.  
    * W tym przykładzie Zadzwonimy do naszych kontenera wejściowego `input`, a nasze kontenera danych wyjściowych `output`.  
    * Kontener danych wejściowych jest, którym początkowo zostały przekazane dokumenty bez optyczne rozpoznawanie znaków.  
    * Kontenera danych wyjściowych jest, gdzie zapisuje dokumentów za pomocą oprogramowania OCR w zadaniu wsadowym.  

W Eksploratorze usługi Storage, należy utworzyć sygnaturę dostępu współdzielonego dla kontenera danych wyjściowych. W tym celu kliknij prawym przyciskiem myszy kontenera danych wyjściowych i wybierając polecenie **Uzyskaj sygnaturę dostępu współdzielonego...** . W obszarze **uprawnienia**, sprawdź **zapisu**. Nie są konieczne żadne inne uprawnienia.  

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

W tej sekcji utworzysz funkcję platformy Azure, która wyzwala zadanie wsadowe optyczne rozpoznawanie znaków w każdym przypadku, gdy plik zostanie przekazany do kontenera wejściowego.

1. Postępuj zgodnie z instrukcjami w [Tworzenie funkcji wyzwalanej przez magazyn obiektów Blob Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) utworzyć funkcję.
    1. Po wyświetleniu monitu dla konta magazynu, należy użyć tego samego konta magazynu, z którą połączony z kontem usługi Batch.
    1. Aby uzyskać **stosu środowiska uruchomieniowego**, wybierz pozycję .NET. Będziemy pisać naszych funkcji C# korzystanie z zestawu SDK .NET usługi Batch.
1. Po utworzeniu funkcji wyzwalanej przez obiekt blob, użyj [ `run.csx` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) i [ `function.proj` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) z usługi GitHub w funkcji.
    * `run.csx` jest uruchamiany, gdy nowy obiekt blob zostanie dodany do kontenera wejściowych obiektów blob.
    * `function.proj` Wyświetla listę zewnętrznych bibliotekach w kodzie funkcji, na przykład SDK .NET usługi Batch.
1. Zmień wartości symboli zastępczych zmiennych w `Run()` funkcji `run.csx` pliku, aby uwzględnić swoje poświadczenia usługi Batch i storage. Poświadczenia konta usługi Batch i storage można znaleźć w witrynie Azure portal w **klucze** sekcji konta usługi Batch.
    * Pobieranie poświadczeń konta usługi Batch i storage w witrynie Azure portal w **klucze** sekcji konta usługi Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Wyzwalanie funkcji i pobrać wyniki

Przekaż wybranych lub wszystkich plików skanowanych od [ `input_files` ](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) katalog w witrynie GitHub do kontenera wejściowego. Monitorowanie Batch Explorer, aby upewnić się, że zadanie zostanie dodane do `ocr-pool` dla każdego pliku. Po kilku sekundach plików za pomocą oprogramowania OCR zastosowany zostanie dodany do kontenera danych wyjściowych. Plik jest następnie widoczne i możliwe do pobierania w Eksploratorze usługi Storage.

Ponadto możesz obejrzeć plików dzienników u dołu okna edytora sieci web usługi Azure Functions, w którym zostaną wyświetlone następujące komunikaty dla każdego pliku, możesz przekazać do kontenera wejściowego:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Aby pobrać pliki wyjściowe z Eksploratora usługi Storage na komputer lokalny, najpierw wybierz pliki, a następnie wybierz pozycję **Pobierz** na Wstążce najważniejsze. 

> [!TIP]
> Pobrane pliki są można wyszukiwać, jeśli otwarte w czytnika plików PDF.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Użyj programu Batch Explorer, aby utworzyć pule i zadania
> * Użyj Eksploratora usługi Storage, aby tworzyć kontenery obiektów blob i sygnatury dostępu współdzielonego (SAS)
> * Tworzenie funkcji wyzwalanej przez obiekt blob platformy Azure
> * Przekazywanie plików wejściowych do usługi Storage
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

* Aby uzyskać więcej przykładów planowania i przetwarzania obciążeń usługi Batch przy użyciu interfejsu API platformy .NET, zobacz [przykłady w witrynie GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Aby dowiedzieć się więcej wyzwalaczy usługi Azure Functions, które służą do uruchamiania obciążeń usługi Batch, zobacz [dokumentacji usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
