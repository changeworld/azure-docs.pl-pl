---
title: Usługa Azure Functions Event Grid debugowanie lokalne
description: Dowiedz się, jak lokalnie debugowanie usługi Azure functions, wyzwoleniu przez zdarzenie usługi Event Grid
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Usługa Azure functions, functions, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 82e3744c8129a076bbae34314b1f829c6f48d548
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479854"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Debugowanie funkcji platformy Azure zdarzeń siatki wyzwalacza lokalnego

W tym artykule przedstawiono sposób debugowania lokalnego funkcję, która obsługuje usługi Azure Event Grid zdarzenia wygenerowane przez konto magazynu. 

## <a name="prerequisites"></a>Wymagania wstępne

- Tworzenie lub użyć istniejącej aplikacji funkcji
- Utwórz lub użyj istniejącego konta magazynu
- Pobierz [ngrok](https://ngrok.com/) się zezwolenie platformie Azure do wywołania funkcji lokalnej

## <a name="create-a-new-function"></a>Tworzenie nowej funkcji

Otwórz aplikację funkcji w programie Visual Studio, kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij przycisk **Dodaj > Nowa funkcja platformy Azure**.

W *nowej funkcji platformy Azure* wybierz **wyzwalacza usługi Event Grid** i kliknij przycisk **OK**.

![Tworzenie nowej funkcji](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Po utworzeniu funkcji Otwórz plik kodu i kopiowania skrzynki adres URL komentarzami w górnej części pliku. Ta lokalizacja jest używana podczas konfigurowania wyzwalacza usługi Event Grid.

![Lokalizacja kopii](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Następnie ustaw punkt przerwania w wierszu, który rozpoczyna się od `log.LogInformation`.

![Ustaw punkt przerwania](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Następnie **naciśnij klawisz F5,** do uruchamiania sesji debugowania.

## <a name="allow-azure-to-call-your-local-function"></a>Zezwalaj na platformy Azure w celu wywołania funkcji lokalnej

Aby przerwać działanie funkcji debugowania na komputerze, należy włączyć to sposób na platformie Azure, nawiązać połączenia z lokalnym funkcji z poziomu chmury.

[Ngrok](https://ngrok.com/) narzędzie zapewnia sposób na platformie Azure wywołać funkcję uruchomiony na Twoim komputerze. Rozpocznij *ngrok* przy użyciu następującego polecenia:

```bash
ngrok http -host-header=localhost 7071
```
Jak skonfigurować narzędzie, okno polecenia powinien wyglądać podobnie do Poniższy zrzut ekranu:

![Rozpocznij ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopiuj **HTTPS** wygenerowany adres URL, gdy *ngrok* jest uruchamiany. Ta wartość jest używana podczas konfigurowania punktu końcowego zdarzeń siatki zdarzeń.

## <a name="add-a-storage-event"></a>Dodaj zdarzenie magazynu

Otwórz witrynę Azure portal, przejdź do konta magazynu i kliknij pozycję **zdarzenia** opcji.

![Dodaj zdarzenie do konta magazynu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

W *zdarzenia* okna, kliknij pozycję **subskrypcji zdarzeń** przycisku. W *nawet subskrypcji* okna, kliknij pozycję *typ punktu końcowego* listy rozwijanej i wybierz **Webhook**.

![Wybierz typ subskrypcji](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Po skonfigurowaniu typ punktu końcowego kliknij **wybierz punkt końcowy** skonfigurować wartości punktu końcowego.

![Wybierz typ punktu końcowego](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

*Punkt końcowy subskrybenta* wartość składa się z trzech różnych wartości. Prefiks jest adres URL HTTPS, generowane przez *ngrok*. W pozostałej części adresu URL jest dostarczany z adresu URL znajdującą się w pliku kodu funkcja, dodany na końcu nazwy funkcji. Począwszy od adresu URL z pliku kodu funkcji, *ngrok* zastępuje adres URL `http://localhost:7071` i funkcji, nazwy zastępuje `{functionname}`.

Poniższy zrzut ekranu pokazuje, jak powinna wyglądać końcowy adres URL:

![Punkt końcowy zaznaczenia](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Po wprowadzeniu odpowiednią wartość, kliknij przycisk **Potwierdź wybór**.

> [!IMPORTANT]
> Podczas każdego uruchamiania *ngrok*wygenerowania adresu URL HTTPS i zmianie wartości. W związku z tym należy utworzyć nową subskrypcję zdarzeń każdorazowo udostępnienia funkcji na platformie Azure za pośrednictwem *ngrok*.

## <a name="upload-a-file"></a>Przekazywanie pliku

Teraz możesz przekazać plik do swojego konta magazynu, aby wyzwolić zdarzenie usługi Event Grid lokalnej funkcji do obsługi. 

Otwórz [Eksploratora usługi Storage](https://azure.microsoft.com/features/storage-explorer/) i nawiąż połączenie z kontem magazynu. 

- Rozwiń **kontenerach obiektów Blob** 
- Kliknij prawym przyciskiem myszy i wybierz **Utwórz kontener obiektów Blob**.
- Nazwa kontenera **testu**
- Wybierz *test* kontenera
- Kliknij przycisk **przekazywanie** przycisku
- Kliknij przycisk **przekazywanie plików**
- Wybierz plik, a następnie przekaż go do kontenera obiektów blob

## <a name="debug-the-function"></a>Debugowanie funkcji

Gdy usługi Event Grid rozpoznaje, że nowy plik zostanie przekazany do kontenera magazynu, punkt przerwania zostanie osiągnięty w lokalnych funkcji.

![Rozpocznij ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym artykule, należy usunąć **test** kontenera na koncie magazynu.

## <a name="next-steps"></a>Kolejne kroki

- [Automatyzowanie zmiany rozmiaru obrazów przekazanych obrazów za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Wyzwalacz siatki zdarzeń dla usługi Azure Functions](./functions-bindings-event-grid.md)
