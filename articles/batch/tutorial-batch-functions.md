---
title: Wyzwalanie zadania wsadowego przy użyciu funkcji platformy Azure
description: Samouczek — stosowanie funkcji OCR do zeskanowanych dokumentów podczas ich dodawania do obiektu blob magazynu
author: LauraBrenner
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: a967fdc14b85f294ee11cbcc57a8d2280dba38e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017194"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Samouczek: Wyzwalanie zadania wsadowego przy użyciu funkcji platformy Azure

W tym samouczku dowiesz się, jak wyzwolić zadanie wsadowe przy użyciu usługi Azure Functions. Przejdziemy przez przykład, w którym dokumenty dodane do kontenera obiektów blob usługi Azure Storage mają optyczne rozpoznawanie znaków (OCR) stosowane do nich za pośrednictwem usługi Azure Batch. Aby usprawnić przetwarzanie OCR, skonfigurujemy funkcję platformy Azure, która uruchamia zadanie usługi Batch OCR za każdym razem, gdy plik jest dodawany do kontenera obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Batch i połączone konto usługi Azure Storage. Aby uzyskać więcej informacji na temat tworzenia i łączenia kont, zobacz [Tworzenie konta usługi Batch.](quick-create-portal.md#create-a-batch-account)
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Tworzenie puli partii i zadania wsadowego przy użyciu Eksploratora wsadowego

W tej sekcji użyjesz Eksploratora wsadowego do utworzenia puli partii i zadania wsadowego, w które będą uruchamiać zadania OCR. 

### <a name="create-a-pool"></a>Tworzenie puli

1. Zaloguj się do Eksploratora wsadowego przy użyciu poświadczeń platformy Azure.
1. Utwórz pulę, wybierając **pozycję Pule** na pasku po lewej stronie, a następnie przycisk **Dodaj** nad formularzem wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Użyjemy `ocr-pool` w tym przykładzie.
    1. Ustaw typ skali na **Stały rozmiar**i ustaw liczbę dedykowanych węzłów na 3.
    1. Wybierz **Ubuntu 18.04-LTS** jako system operacyjny.
    1. Wybierz `Standard_f2s_v2` jako rozmiar maszyny wirtualnej.
    1. Włącz zadanie startowe i `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`dodaj polecenie . Pamiętaj, aby ustawić tożsamość użytkownika jako **domyślnego użytkownika zadania (Administrator),** który umożliwia uruchamianie zadań dołączania poleceń z programem `sudo`.
    1. Kliknij przycisk **OK**.
### <a name="create-a-job"></a>Tworzenie zadania

1. Utwórz zadanie w puli, wybierając pozycję **Zadania** na pasku po lewej stronie, a następnie przycisk **Dodaj** nad formularzem wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Użyjemy `ocr-job` w tym przykładzie.
    1. Ustaw pulę na `ocr-pool`, lub dowolną nazwę, którą wybrałeś dla puli.
    1. Kliknij przycisk **OK**.


## <a name="create-blob-containers"></a>Tworzenie kontenerów obiektów blob

W tym miejscu utworzysz kontenery obiektów blob, które będą przechowywać pliki wejściowe i wyjściowe dla zadania Wsadowego OCR.

1. Zaloguj się do Eksploratora magazynu przy użyciu poświadczeń platformy Azure.
1. Korzystając z konta magazynu połączonego z kontem usługi Batch, utwórz dwa kontenery obiektów blob (jeden dla plików wejściowych, jeden dla plików wyjściowych), wykonując kroki opisane w [witrynie Utwórz kontener obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

W tym przykładzie kontener `input` wejściowy ma nazwę i jest, gdzie wszystkie dokumenty bez OCR są początkowo przekazywane do przetwarzania. Kontener danych wyjściowych jest nazwany `output` i jest, gdzie zadanie batch zapisuje przetworzone dokumenty z OCR.  
    * W tym przykładzie zadzwonimy do `input`naszego kontenera wejściowego i naszego kontenera wyjściowego. `output`  
    * Kontener wejściowy jest, gdzie wszystkie dokumenty bez OCR są początkowo przekazywane.  
    * Kontener danych wyjściowych jest, gdzie batch zadanie zapisuje dokumenty z OCR.  

Utwórz podpis dostępu współdzielonego dla kontenera wyjściowego w Eksploratorze magazynu. W tym celu kliknij prawym przyciskiem myszy kontener danych wyjściowych i wybierz pozycję **Pobierz podpis dostępu współdzielonego...**. W obszarze **Uprawnienia**zaznacz pole **wyboru Napisz**. Żadne inne uprawnienia nie są wymagane.  

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

W tej sekcji utworzysz funkcję platformy Azure, która wyzwala zadanie partii OCR za każdym razem, gdy plik zostanie przekazany do kontenera wejściowego.

1. Wykonaj kroki opisane w [tworzenie funkcji wyzwalane przez usługę Azure Blob storage,](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) aby utworzyć funkcję.
    1. Po wyświetleniu monitu o konto magazynu użyj tego samego konta magazynu, które zostało połączone z kontem usługi Batch.
    1. W przypadku **stosu środowiska wykonawczego**wybierz opcję .NET. Napiszemy naszą funkcję w języku C#, aby wykorzystać sdk usługi Batch .NET.
1. Po utworzeniu funkcji wyzwalane obiekty blob, należy użyć [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) i [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) z GitHub w funkcji.
    * `run.csx`jest uruchamiany po dodaniu nowego obiektu blob do kontenera wejściowego obiektu blob.
    * `function.proj`wyświetla listę bibliotek zewnętrznych w kodzie funkcji, na przykład batch .NET SDK.
1. Zmień wartości zastępcze zmiennych w `Run()` funkcji `run.csx` pliku, aby odzwierciedlić dane usługi Batch i poświadczenia magazynu. Poświadczenia konta usługi Batch i magazynu można znaleźć w witrynie Azure portal w sekcji **Klucze** konta usługi Batch.
    * Pobieranie poświadczeń konta usługi Batch i magazynu w witrynie Azure portal w sekcji **Klucze** konta usługi Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Wyzwalanie funkcji i pobieranie wyników

Przekaż dowolne lub wszystkie zeskanowane pliki z [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) katalogu w usłudze GitHub do kontenera wejściowego. Monitoruj Eksploratora wsadowego, aby potwierdzić, że zadanie zostanie dodane dla `ocr-pool` każdego pliku. Po kilku sekundach plik z zastosowanym ocr jest dodawany do kontenera wyjściowego. Plik jest następnie widoczny i można go pobrać w Eksploratorze magazynu.

Ponadto można obejrzeć plik dzienników w dolnej części okna edytora sieci Web usługi Azure Functions, gdzie zobaczysz komunikaty takie jak ten dla każdego pliku, który zostanie przekazany do kontenera wejściowego:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Aby pobrać pliki wyjściowe z Eksploratora magazynu na komputer lokalny, najpierw wybierz żądane pliki, a następnie wybierz **opcję Pobierz** na górnej wstążce. 

> [!TIP]
> Pobrane pliki można przeszukiwać, jeśli są otwierane w czytniku PLIKÓW PDF.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie pul i zadań za pomocą Eksploratora wsadowego
> * Tworzenie kontenerów obiektów blob za pomocą Eksploratora magazynu oraz sygnatury dostępu współdzielonego (SAS)
> * Tworzenie funkcji platformy Azure wyzwalanych przez obiekt blob
> * Przekazywanie plików wejściowych do usługi Storage
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

* Aby uzyskać więcej przykładów używania interfejsu API platformy .NET do planowania i przetwarzania obciążeń wsadowych, zobacz [przykłady w usłudze GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Aby wyświetlić więcej wyzwalaczy usługi Azure Functions, których można użyć do uruchamiania obciążeń wsadowych, zobacz [dokumentację usługi Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)
