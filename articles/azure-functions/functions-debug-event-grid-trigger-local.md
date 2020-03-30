---
title: Debugowanie lokalne usługi Azure Functions Event Grid
description: Dowiedz się, jak lokalnie debugować funkcje platformy Azure wyzwalane przez zdarzenie w usłudze Event Grid
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227065"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure

W tym artykule pokazano, jak debugować funkcję lokalną, która obsługuje zdarzenie usługi Azure Event Grid wywoływane przez konto magazynu. 

## <a name="prerequisites"></a>Wymagania wstępne

- Tworzenie lub używanie istniejącej aplikacji funkcji
- Tworzenie lub używanie istniejącego konta magazynu
- Pobierz [ngrok,](https://ngrok.com/) aby umożliwić platformie Azure wywołanie funkcji lokalnej

## <a name="create-a-new-function"></a>Tworzenie nowej funkcji

Otwórz aplikację funkcji w programie Visual Studio i kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Dodaj > nową funkcję platformy Azure**.

W oknie *Nowa funkcja platformy Azure* wybierz pozycję **Wyzwalacz siatki zdarzeń** i kliknij przycisk **OK**.

![Tworzenie nowej funkcji](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Po utworzeniu funkcji otwórz plik kodu i skopiuj adres URL skomentowany w górnej części pliku. Ta lokalizacja jest używana podczas konfigurowania wyzwalacza siatki zdarzeń.

![Kopiuj lokalizację](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Następnie ustaw punkt przerwania w wierszu zaczynanym od `log.LogInformation`.

![Ustawianie punktu przerwania](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Następnie **naciśnij klawisz F5,** aby rozpocząć sesję debugowania.

## <a name="allow-azure-to-call-your-local-function"></a>Zezwalaj platformie Azure na wywoływanie funkcji lokalnej

Aby włamać się do funkcji debugowane na komputerze, należy włączyć sposób azure do komunikowania się z funkcją lokalną z chmury.

Narzędzie [ngrok](https://ngrok.com/) umożliwia platformie Azure wywołanie funkcji uruchomionej na komputerze. Rozpocznij *ngrok* za pomocą następującego polecenia:

```bash
ngrok http -host-header=localhost 7071
```
Gdy narzędzie jest skonfigurowane, okno polecenia powinno wyglądać podobnie do następującego zrzutu ekranu:

![Start ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Skopiuj adres URL **HTTPS** wygenerowany po uruchomieniu *ngrok.* Ta wartość jest używana podczas konfigurowania punktu końcowego zdarzenia siatki zdarzeń.

## <a name="add-a-storage-event"></a>Dodawanie zdarzenia magazynu

Otwórz witrynę Azure portal i przejdź do konta magazynu i kliknij opcję **Zdarzenia.**

![Zdarzenie Dodaj konto magazynu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

W oknie *Zdarzenia* kliknij przycisk **Subskrypcja zdarzeń.** W oknie *Parzysta subskrypcja* kliknij pozycję rozwijana *Typ punktu końcowego* i wybierz pozycję **Web Hook**.

![Wybierz typ subskrypcji](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Po skonfigurowaniu typu punktu końcowego kliknij wybierz **punkt końcowy,** aby skonfigurować wartość punktu końcowego.

![Wybieranie typu punktu końcowego](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Wartość *punktu końcowego subskrybenta* składa się z trzech różnych wartości. Prefiks to adres URL HTTPS generowany przez *ngrok*. Pozostała część adresu URL pochodzi z adresu URL znajdującego się w pliku kodu funkcji, z nazwą funkcji dodaną na końcu. Począwszy od adresu URL z pliku kodu funkcji, adres URL *ngrok* zastępuje `http://localhost:7071` i zastępuje nazwę funkcji `{functionname}`.

Poniższy zrzut ekranu pokazuje, jak powinien wyglądać końcowy adres URL:

![Wybór punktu końcowego](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Po wprowadzeniu odpowiedniej wartości kliknij przycisk **Potwierdź zaznaczenie**.

> [!IMPORTANT]
> Za każdym razem, gdy uruchamiasz *ngrok,* adres URL HTTPS jest generowany ponownie, a wartość zmienia się. W związku z tym należy utworzyć nową subskrypcję zdarzeń za każdym razem, gdy udostępniasz swoją funkcję na platformie Azure za pośrednictwem *ngrok*.

## <a name="upload-a-file"></a>Przekazywanie pliku

Teraz możesz przekazać plik na swoje konto magazynu, aby wyzwolić zdarzenie w siatce zdarzeń dla funkcji lokalnej do obsługi. 

Otwórz [Eksploratora magazynu](https://azure.microsoft.com/features/storage-explorer/) i połącz się z kontem magazynu. 

- Rozwiń **kontenery obiektów blob** 
- Kliknij prawym przyciskiem myszy i wybierz polecenie **Utwórz kontener obiektów blob**.
- Nadawanie nazwy **testowi kontenera**
- Wybierz pojemnik *testowy*
- Kliknij przycisk **Prześlij**
- Kliknij **pozycję Przekaż pliki**
- Wybierz plik i przekaż go do kontenera obiektów blob

## <a name="debug-the-function"></a>Debugowanie funkcji

Gdy siatka zdarzeń rozpoznaje nowy plik jest przekazyany do kontenera magazynu, punkt przerwania zostanie trafiony w funkcji lokalnej.

![Start ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym artykule, usuń kontener **testowy** na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

- [Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Wyzwalacz siatki zdarzeń dla funkcji platformy Azure](./functions-bindings-event-grid.md)
