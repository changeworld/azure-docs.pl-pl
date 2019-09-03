---
title: Tworzenie i wdrażanie Azure Functions w języku Python z Visual Studio Code
description: Jak używać rozszerzenia Visual Studio Code Azure Functions do tworzenia funkcji bezserwerowych w języku Python i wdrażania ich na platformie Azure.
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 43fee2ce25e358bbcff915d2fbef96bf4b7c1a0c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233108"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Wdrażanie języka Python w celu Azure Functions z Visual Studio Code

W tym samouczku użyjesz Visual Studio Code i rozszerzenia Azure Functions, aby utworzyć bezserwerowy punkt końcowy HTTP z językiem Python oraz dodać do magazynu połączenie (lub "powiązanie"). Azure Functions uruchamia kod w środowisku bezserwerowym bez konieczności inicjowania obsługi administracyjnej maszyny wirtualnej ani publikowania aplikacji sieci Web. Azure Functions rozszerzenie dla Visual Studio Code znacznie upraszcza proces używania funkcji przez automatyczne obsługiwanie wielu problemów z konfiguracją.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zainstaluj rozszerzenie Azure Functions
> * Tworzenie funkcji wyzwalanej przez protokół HTTP
> * Debuguj lokalnie
> * Synchronizuj ustawienia aplikacji
> * Wyświetlanie dzienników przesyłania strumieniowego
> * Nawiązywanie połączenia z usługą Azure Storage

Jeśli wystąpią problemy z którymkolwiek z kroków opisanych w tym samouczku, chcielibyśmy poznać szczegóły. Aby przesłać szczegółową opinię, użyj przycisku **mam problem** na końcu każdej sekcji.

## <a name="prerequisites"></a>Wymagania wstępne

- [Subskrypcji platformy Azure](#azure-subscription).
- [Visual Studio Code z rozszerzeniem Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension) .
- [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Subskrypcja platformy Azure

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się teraz](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) , aby skorzystać z bezpłatnego konta 30-dniowego z $200 na platformie Azure, aby wypróbować dowolną kombinację usług.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python i rozszerzenie Azure Functions

Zainstaluj następujące oprogramowanie:

- Środowisko Python 3.6. x wymagane przez Azure Functions. [3.6.8 języka Python](https://www.python.org/downloads/release/python-368/) to Najnowsza wersja wersji 3.6. x.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) zgodnie z opisem w [samouczku Visual Studio Code Python — wymagania wstępne](https://code.visualstudio.com/docs/python/python-tutorial).
- [Rozszerzenie Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Aby uzyskać ogólne informacje, odwiedź [repozytorium GitHub programu vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Postępuj zgodnie z instrukcjami dotyczącymi systemu operacyjnego w [pracy z Azure Functions Core Tools](functions-run-local.md#v2). Narzędzia są zapisywana w środowisku .NET Core, a pakiet podstawowych narzędzi jest najlepiej instalowany przy użyciu Menedżera pakietów środowiska Node. js, npm, co oznacza, że w tym momencie należy zainstalować program .NET Core i środowisko Node. js, nawet w przypadku kodu Python. Można jednak ominąć wymagania dotyczące platformy .NET Core przy użyciu "pakietów rozszerzeń", jak opisano w wyżej wymienionej dokumentacji. Niezależnie od tego, co się dzieje, należy zainstalować te składniki tylko raz, po którym Visual Studio Code automatycznie Monituj o zainstalowanie aktualizacji.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Po zainstalowaniu rozszerzenia funkcji Zaloguj się do konta platformy Azure, przechodząc do **platformy Azure: Eksplorator** funkcji, wybierz pozycję **Zaloguj się do platformy Azure**i postępuj zgodnie z monitami.

![Zaloguj się do platformy Azure za pomocą Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Po zalogowaniu Sprawdź, czy konto e-mail subskrypcji platformy Azure jest wyświetlane na pasku stanu:

![Visual Studio Code pasek stanu z kontem platformy Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Nazwa przypisana do subskrypcji zostanie również wyświetlona na **platformie Azure: Eksplorator** funkcji ("podstawowy" na poniższej ilustracji):

![Visual Studio Code Azure App Service Explorer przedstawiające subskrypcje](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Jeśli wystąpi błąd **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"** , może to być spowodowane tym, że jesteś za serwerem proxy i nie można skontaktować się z interfejsem API platformy Azure. Skonfiguruj `HTTP_PROXY` i`HTTPS_PROXY` zmienne środowiskowe za pomocą informacji o serwerze proxy w terminalu:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych

Aby sprawdzić, czy są zainstalowane wszystkie narzędzia Azure Functions, otwórz Visual Studio Code paletę poleceń (F1), wybierz **Terminal: Utwórz nowe polecenie zintegrowanego terminalu** i po otwarciu terminalu uruchom polecenie: `func`

![Sprawdzanie wymagań wstępnych narzędzi Azure Functions Core](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Dane wyjściowe, które zaczynają się od logo Azure Functions (należy przewinąć dane wyjściowe w górę), wskazuje, że Azure Functions Core Tools są obecne.

`func` Jeśli polecenie nie zostanie rozpoznane, sprawdź, czy folder, w którym zainstalowano Azure Functions Core Tools, znajduje się w zmiennej środowiskowej PATH.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Tworzenie funkcji

1. Kod dla Azure Functions jest zarządzany w ramach _projektu_usługi Functions, który tworzysz najpierw przed utworzeniem kodu. Na **platformie Azure: Eksplorator** funkcji (otwarty przy użyciu ikony platformy Azure po lewej stronie), wybierz ikonę polecenia **Nowy projekt** lub Otwórz paletę poleceń (F1) i wybierz **Azure Functions: Utwórz nowy projekt**.

    ![Przycisk Utwórz nowy projekt w Eksploratorze funkcji](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. W następujących monitach:

    | Monit | Value | Opis | 
    | --- | --- | --- |
    | Określ folder dla projektu | Bieżący otwarty folder | Folder, w którym ma zostać utworzony projekt. Możesz chcieć utworzyć projekt w podfolderze. |
    | Wybierz język projektu aplikacji funkcji | **Python** | Język, który ma być używany dla funkcji, która określa szablon użyty dla kodu. |
    | Wybierz szablon dla pierwszej funkcji projektu | **Wyzwalacz HTTP** | Funkcja, która używa wyzwalacza HTTP, jest uruchamiana za każdym razem, gdy istnieje żądanie HTTP wysłane do punktu końcowego funkcji. (Istnieją różne wyzwalacze dla Azure Functions. Aby dowiedzieć się więcej, zobacz artykuł [co mogę zrobić z usługą Functions?](functions-overview.md#what-can-i-do-with-functions).) |
    | Podaj nazwę funkcji | HttpExample | Nazwa jest używana dla podfolderu, który zawiera kod funkcji wraz z danymi konfiguracyjnymi, a także definiuje nazwę punktu końcowego HTTP. Użyj "HttpExample" zamiast zaakceptowania domyślnego "HTTPTrigger", aby odróżnić samą funkcję od wyzwalacza. |
    | Poziom autoryzacji | **Function** | Wywołania wykonywane do punktu końcowego funkcji wymagają [klucza funkcji](functions-bindings-http-webhook.md#authorization-keys). |
    | Wybierz, w jaki sposób chcesz otworzyć projekt | **Otwórz w bieżącym oknie** | Otwiera projekt w bieżącym oknie Visual Studio Code. |

1. Po krótkim czasie zostanie wyświetlony komunikat wskazujący, że nowy projekt został utworzony. W **Eksploratorze**znajduje się podfolder utworzony dla tej funkcji. 

1. Jeśli nie jest jeszcze otwarty, Otwórz  *\_ \_plik\_init\_. PR* zawierający domyślny kod funkcji:

    [![Wynik tworzenia nowego projektu funkcji w języku Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Gdy Visual Studio Code informuje, że nie masz wybranego interpretera języka Python po **otwarciu  *\_ \_elementu\_init\_. PR*, Otwórz paletę poleceń (F1), wybierz język Python: Wybierz** polecenie interpreter, a następnie wybierz środowisko wirtualne w folderze lokalnym `.env` (który został utworzony jako część projektu). Środowisko musi być oparte na języku Python 3.6 x, jak wspomniano wcześniej w sekcji [wymagania wstępne](#prerequisites).
    >
    > ![Wybieranie środowiska wirtualnego utworzonego przy użyciu projektu](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Sprawdzanie plików kodu

W nowo utworzonym podfolderze funkcji _HttpExample_ są trzy pliki  *\_:\_ \_\_init. PR* zawiera kod funkcji *Function. JSON* opisuje funkcję na platformie Azure Functions i *Sample. dat* to przykładowy plik danych. Jeśli chcesz, możesz usunąć *przykład. dat* , ponieważ istnieje tylko, aby można było dodać inne pliki do podfolderu.

Najpierw przyjrzyjmy się *funkcji Function. JSON* , a następnie w  *\_ \_kodzie\_init\_. PR*.

### <a name="functionjson"></a>function.json

Plik Function. JSON zawiera informacje o konfiguracji niezbędne do Azure Functions punktu końcowego:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Właściwość identyfikuje plik startowy dla kodu i ten kod musi zawierać funkcję języka Python o nazwie `main`. `scriptFile` Możesz umieścić kod w wielu plikach, tak długo, jak określony tutaj plik zawiera `main` funkcję.

`bindings` Element zawiera dwa obiekty, jeden do opisywania żądań przychodzących, a drugi do opisywania odpowiedzi HTTP. W przypadku żądań przychodzących`"direction": "in"`() funkcja reaguje na żądania HTTP GET lub post i wymaga podania klucza funkcji. Odpowiedź (`"direction": "out"`) jest odpowiedzią http, która zwraca każdą wartość zwróconą `main` z funkcji języka Python.

### <a name="__initpy__"></a>\_\_init.py\_\_

Podczas tworzenia nowej funkcji Azure Functions udostępnia domyślny kod języka Python w  *\_ \_\_init\_. PR*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Ważne części kodu są następujące:

- Należy zaimportować `func` z `azure.functions`; Importowanie modułu rejestrowania jest opcjonalne, ale zalecane.
- Wymagana `main` funkcja języka Python `func.request` otrzymuje obiekt o nazwie `req`i zwraca wartość typu `func.HttpResponse`. Możesz dowiedzieć się więcej o możliwościach tych obiektów w funkcji [Func. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) i [Func. Odwołania HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) .
- Treść `main` następnie przetwarza żądanie i generuje odpowiedź. W tym przypadku kod szuka `name` parametru w adresie URL. Niepowodzenie, sprawdza, czy treść żądania zawiera kod JSON ( `func.HttpRequest.get_json`using) i czy JSON `name` zawiera wartość (przy użyciu `get` metody obiektu JSON zwracanego przez `get_json`).
- Jeśli zostanie znaleziona nazwa, kod zwraca ciąg "Hello" z nazwą dołączoną; w przeciwnym razie zwraca komunikat o błędzie.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Debuguj lokalnie

1. Podczas tworzenia projektu Functions rozszerzenie Visual Studio Code tworzy również konfigurację uruchamiania w programie `.vscode/launch.json` , która zawiera pojedynczą konfigurację o nazwie dołączanie **do funkcji języka Python**. Ta konfiguracja oznacza, że można po prostu wybrać **F5** lub użyć Eksploratora debugowania do uruchomienia projektu:

    ![Eksplorator debugowania pokazujący konfigurację uruchamiania funkcji](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. Po uruchomieniu debugera zostanie otwarty terminal zawierający dane wyjściowe z Azure Functions, w tym podsumowanie dostępnych punktów końcowych. Adres URL może być inny, jeśli użyto nazwy innej niż "HttpExample":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. **Naciśnij kombinację klawiszy Ctrl + kliknięcie** lub **cmd i kliknij** adres URL w oknie **danych wyjściowych** Visual Studio Code, aby otworzyć przeglądarkę do tego adresu, lub Uruchom przeglądarkę i wklej ją w tym samym adresie URL. W obu przypadkach punkt końcowy to `api/<function_name>`, w tym przypadku. `api/HttpExample` Jednak ponieważ ten adres URL nie zawiera parametru nazwy, okno przeglądarki powinno po prostu pokazać, "przekazać nazwę w ciągu zapytania lub w treści żądania" zgodnie z odpowiednią ścieżką w kodzie.

1. Teraz spróbuj dodać parametr name do użycia, na przykład `http://localhost:7071/api/HttpExample?name=VS%20Code`, a w oknie przeglądarki powinien zostać wyświetlony komunikat "Hello Visual Studio Code!", pokazując, że uruchomiono tę ścieżkę kodu.

1. Aby przekazać wartość nazwy w treści żądania JSON, można użyć narzędzia, takiego jak zwinięcie, w wbudowanym kodzie JSON:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Alternatywnie Utwórz plik, taki jak *Data. JSON* `{"name":"Visual Studio Code"}` , zawierający i użyj polecenia `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Aby debugować funkcję, należy ustawić punkt przerwania w wierszu, który `name = req.params.get('name')` odczytuje i ponownie wysłać żądanie do adresu URL. Debuger Visual Studio Code powinien zostać zatrzymany w tym wierszu, co pozwala na badanie zmiennych i przechodzenie przez kod. (Aby zapoznać się z krótkim przewodnikiem dotyczącym debugowania podstawowego, zobacz [samouczek Visual Studio Code — Konfigurowanie i uruchamianie debugera](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger)).

1. Po upewnieniu się, że funkcja została dokładnie przetestowana lokalnie, Zatrzymaj debuger (za pomocą polecenia **Debuguj** > **zatrzymywanie debugowania** menu lub polecenie **Disconnect** na pasku narzędzi debugowania).

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Wdrażanie w usłudze Azure Functions

W tych krokach użyjesz rozszerzenia Functions, aby utworzyć aplikację funkcji na platformie Azure wraz z innymi wymaganymi zasobami platformy Azure. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. Wymaga również konta usługi Azure Storage na potrzeby danych i [planu hostingu](functions-scale.md#hosting-plan-support). Wszystkie te zasoby są zorganizowane w ramach jednej grupy zasobów.

1. **Na platformie Azure: Eksplorator** funkcji, wybierz polecenie **Wdróż do aplikacja funkcji** lub Otwórz paletę poleceń (F1 **) i wybierz Azure Functions: Wdróż w aplikacja funkcji** polecenie. Ponownie aplikacja funkcji to miejsce, w którym projekt języka Python działa na platformie Azure.

    ![Wdróż w aplikacja funkcji polecenie](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Po wyświetleniu monitu wybierz pozycję **Utwórz nowe aplikacja funkcji na platformie Azure**i podaj nazwę, która jest unikatowa na platformie Azure (zazwyczaj przy użyciu nazwy prywatnej lub firmy oraz innych unikatowych identyfikatorów). możesz użyć liter, cyfr i łączników). Jeśli wcześniej utworzono aplikacja funkcji, jego nazwa zostanie wyświetlona na liście opcji.

1. Rozszerzenie wykonuje następujące akcje, które można obserwować w Visual Studio Code wyskakujących komunikatów i oknie **danych wyjściowych** (proces trwa kilka minut):

    - Utwórz grupę zasobów przy użyciu podaną nazwę (usuwając łączniki).
    - W tej grupie zasobów Utwórz konto magazynu, plan hostingu i aplikację funkcji. Domyślnie tworzony jest [Plan zużycia](functions-scale.md#consumption-plan) . Aby uruchamiać funkcje w ramach dedykowanego planu, należy [włączyć Publikowanie przy użyciu opcji Zaawansowane tworzenie](functions-develop-vs-code.md).
    - Wdróż swój kod w aplikacji funkcji.

    **Platforma Azure: Eksplorator** funkcji pokazuje także postęp:

    ![Wskaźnik postępu wdrażania na platformie Azure: Eksplorator funkcji](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Po zakończeniu wdrażania rozszerzenie Azure Functions wyświetla komunikat z przyciskami dla trzech dodatkowych akcji:

    ![Komunikat wskazujący pomyślne wdrożenie z dodatkowymi akcjami](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Aby **rejestrować strumieniowo** i **przekazywać ustawienia**, zobacz Następne sekcje. Aby **wyświetlić dane wyjściowe**, zobacz krok 5 poniżej.

1. Po wdrożeniu okno **danych wyjściowych** zawiera również publiczny punkt końcowy na platformie Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Użyj tego punktu końcowego, aby uruchomić te same testy lokalnie, przy użyciu parametrów adresu URL i/lub żądań z danymi JSON w treści żądania. Wyniki publicznego punktu końcowego powinny być zgodne z wynikami lokalnego punktu końcowego, który został wcześniej przetestowany.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Obsługa przesyłania strumieniowego dzienników jest obecnie w trakcie opracowywania, zgodnie z opisem w artykule [problem 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) dla rozszerzenia Azure Functions. W oknie podręcznym komunikat wdrożenia przycisk **dzienniki przesyłania strumieniowego** będzie ostatecznie łączył dane wyjściowe dziennika na platformie Azure w celu Visual Studio Code. Możesz również uruchomić i zatrzymać strumień dziennika w Eksploratorze **Azure Functions** , klikając prawym przyciskiem myszy projekt Functions i wybierając pozycję **Uruchom dzienniki przesyłania strumieniowego** lub **Zatrzymaj dzienniki przesyłania strumieniowego**.

Jednak te polecenia nie działają jeszcze. Funkcja przesyłania strumieniowego dzienników jest dostępna w przeglądarce, uruchamiając następujące polecenie, `<app_name>` zastępując nazwę aplikacji usługi Functions na platformie Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Synchronizowanie ustawień lokalnych z platformą Azure

Przycisk **Ustawienia przekazywania** w oknie podręcznym komunikat wdrożenia stosuje zmiany wprowadzone do pliku *Local. Settings. JSON* na platformie Azure. Możesz również wywołać polecenie w Eksploratorze **Azure Functions** , rozwijając węzeł projektu Functions, klikając prawym przyciskiem myszy pozycję **Ustawienia aplikacji**, a następnie wybierając polecenie **Przekaż ustawienia lokalne..** .. Możesz również użyć palety poleceń, aby wybrać **Azure Functions: Przekaż ustawienia** lokalne polecenia.

Przekazywanie ustawień aktualizuje wszystkie istniejące ustawienia i dodaje nowe ustawienia zdefiniowane w pliku *Local. Settings. JSON*. Przekazywanie nie usuwa żadnych ustawień z platformy Azure, które nie są wymienione w pliku lokalnym. Aby usunąć te ustawienia, rozwiń węzeł **Ustawienia aplikacji** w Eksploratorze **Azure Functions** , kliknij prawym przyciskiem myszy ustawienie i wybierz pozycję **Usuń ustawienie.** ... Możesz również edytować ustawienia bezpośrednio na Azure Portal.

Aby zastosować zmiany wprowadzane przez portal lub za pomocą **Eksploratora platformy Azure** do pliku *Local. Settings. JSON* , kliknij prawym przyciskiem myszy węzeł **Ustawienia aplikacji** i wybierz polecenie **Pobierz Ustawienia zdalne...** . Możesz również użyć palety poleceń, aby wybrać **Azure Functions: Pobierz polecenie ustawienia** zdalne.

## <a name="add-a-second-function"></a>Dodaj drugą funkcję

Po pierwszym wdrożeniu można wprowadzać zmiany w kodzie, takie jak dodawanie dodatkowych funkcji, i ponowne wdrażanie w tej samej aplikacji funkcji.

1. **Na platformie Azure: Eksplorator** funkcji, wybierz polecenie **Utwórz funkcję** lub Użyj **Azure Functions: Utwórz funkcję** za pomocą palety poleceń. Określ następujące szczegóły dla funkcji:

    - Szablon: Wyzwalacz HTTP
    - Nazwa: "DigitsOfPi"
    - Poziom autoryzacji: Anonimowe

1. W Eksploratorze plików Visual Studio Code jest podfolderem o nazwie funkcji, który ponownie zawiera pliki o nazwie  *\_ \_init\_\_. PR*, *Function. JSON*i *Sample. dat*.

1. Zastąp zawartość  *\_ \_init.PR,\_aby dopasować następujący kod, który generuje ciąg zawierający wartość pi do liczby cyfr określonych w adresie URL (ten kod używa tylko parametru adresu URL)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Ponieważ kod obsługuje tylko HTTP GET, Modify *Function. JSON* , tak aby `"methods"` kolekcja zawierała tylko `"get"` (czyli usuń `"post"`). Cały plik powinien wyglądać w następujący sposób:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Uruchom Debuger, wybierając klawisz **F5** lub wybierając polecenie **Debuguj** > **Rozpocznij debugowanie** menu. W oknie **dane wyjściowe** powinny teraz być widoczne oba punkty końcowe w projekcie:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. W przeglądarce lub przed zwinięciem wprowadź żądanie `http://localhost:7071/api/DigitsOfPi?digits=125` i obserwuj dane wyjściowe. (Możesz zauważyć, że algorytm kodu nie jest całkowicie dokładny, ale pozostawimy ulepszenia!) Zatrzymaj debuger po zakończeniu.

1. Wdróż ponownie kod przy użyciu polecenia **Wdróż do aplikacja funkcji** na **platformie Azure: Eksplorator** funkcji. Jeśli zostanie wyświetlony monit, wybierz aplikacja funkcji utworzone wcześniej.

1. Po zakończeniu wdrażania (trwa kilka minut) okno **dane wyjściowe** zawiera publiczne punkty końcowe, w których można powtórzyć testy.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Dodawanie powiązania w celu zapisywania komunikatów w usłudze Azure Storage

_Powiązanie_ pozwala połączyć kod funkcji z zasobami, takimi jak usługa Azure Storage, bez konieczności pisania kodu dostępu do danych. Powiązanie jest zdefiniowane w pliku *Function. JSON* i może reprezentować zarówno dane wejściowe, jak i wyjściowe. Funkcja może używać wielu powiązań wejściowych i wyjściowych, ale tylko jednego wyzwalacza. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tej sekcji dodasz powiązanie magazynu do funkcji HttpExample utworzonej wcześniej w tym samouczku. Funkcja używa tego powiązania do zapisywania komunikatów do magazynu przy użyciu każdego żądania. Dany magazyn używa tego samego domyślnego konta magazynu, które jest używane przez aplikację funkcji. Jeśli jednak planujesz duże wykorzystanie magazynu, warto rozważyć utworzenie oddzielnego konta.

1. Zsynchronizuj ustawienia zdalne dla projektu Azure Functions do pliku *Local. Settings. JSON* , otwierając paletę poleceń i wybierając **Azure Functions: Pobierz ustawienia**zdalne. Otwórz plik *Local. Settings. JSON* i sprawdź, czy zawiera on wartość `AzureWebJobsStorage`. Ta wartość jest ciągiem połączenia dla konta magazynu.

1. W folderze kliknij prawym przyciskiem myszy plik *Function. JSON*, a następnie wybierz polecenie **Dodaj powiązanie:** `HttpExample`

    ![Dodaj polecenie powiązania w Eksploratorze Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. W monitach, które obserwują Visual Studio Code, wybierz lub podaj następujące wartości:

    | Monit | Wartość do udostępnienia |
    | --- | --- |
    | Ustaw kierunek powiązania | określoną |
    | Wybieranie powiązania z kierunkiem | Usługa Azure Queue Storage |
    | Nazwa używana do identyfikowania tego powiązania w kodzie | msg |
    | Kolejka, do której zostanie wysłany komunikat | outqueue |
    | Wybierz opcję Ustawienia z pliku *Local. Settings. JSON* (z prośbą o połączenie z magazynem) | AzureWebJobsStorage |

1. Po dokonaniu wyboru tych opcji Sprawdź, czy następujące powiązanie zostało dodane do pliku *Function. JSON* :

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Teraz, po skonfigurowaniu powiązania, można użyć go w kodzie funkcji. Nowo zdefiniowane powiązanie pojawia się w kodzie jako `main` argument funkcji w  *\_ \_\_init\_. PR*. Na przykład można `msg` zmodyfikować  *\_ \_plik\_init\_. PR* w HttpExample, aby pasował do poniższego, który pokazuje przy użyciu argumentu, aby napisać komunikat z sygnaturą czasową o nazwie użytej w żądając. Komentarze wyjaśniają określone zmiany:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Aby przetestować te zmiany lokalnie, uruchom debuger ponownie w Visual Studio Code, wybierając klawisz **F5** lub wybierając polecenie **Debuguj** > **Rozpocznij debugowanie** menu. Jak przed wyświetleniem okna **danych wyjściowych** punkty końcowe w projekcie.

1. W przeglądarce przejdź do adresu URL `http://localhost:7071/api/HttpExample?name=VS%20Code` , aby utworzyć żądanie do punktu końcowego HttpExample, co powinno również napisać komunikat do kolejki.

1. Aby sprawdzić, czy wiadomość została zapisywana w kolejce "Moja kolejka" (nazywana w powiązaniu), można użyć jednej z trzech metod:

    1. Zaloguj się do [Azure Portal](https://portal.azure.com)i przejdź do grupy zasobów zawierającej projekt funkcji. W tej grupie zasobów Znajdź i Otwórz konto magazynu dla projektu, a następnie przejdź do pozycji **kolejki**. Na tej stronie przejdź do pozycji "Moja kolejka", która powinna wyświetlać wszystkie zarejestrowane komunikaty.

    1. Otwórz i zapoznaj się z kolejką, korzystając z Eksplorator usługi Azure Storage, która integruje się z programem Visual Studio, zgodnie z opisem w sekcji [łączenie funkcji z usługą Azure Storage przy użyciu Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), w szczególności z sekcją [Badanie kolejki wyjściowej](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Użyj interfejsu wiersza polecenia platformy Azure, aby wykonać zapytanie do kolejki magazynu, zgodnie z opisem w temacie [zapytanie do kolejki magazynu](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. Aby przetestować w chmurze, ponownie Wdróż kod przy użyciu polecenia **Wdróż do aplikacja funkcji** na **platformie Azure: Eksplorator** funkcji. Jeśli zostanie wyświetlony monit, wybierz aplikacja funkcji utworzone wcześniej. Po zakończeniu wdrażania (trwa kilka minut) okno **dane wyjściowe** ponownie pokazuje publiczne punkty końcowe, w których można powtarzać testy.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Utworzona aplikacja funkcji obejmuje zasoby, które mogą ponosić minimalne koszty (zobacz [Cennik funkcji](https://azure.microsoft.com/pricing/details/functions/)). Aby wyczyścić zasoby, kliknij prawym przyciskiem myszy aplikacja funkcji na **platformie Azure: Eksplorator** funkcji i wybierz pozycję **Usuń aplikacja funkcji**. Możesz również odwiedzić [Azure Portal](https://portal.azure.com), wybrać **grupy zasobów** w okienku nawigacji po lewej stronie, wybrać grupę zasobów, która została utworzona w procesie tego samouczka, a następnie użyć polecenia **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Gratulujemy wykonania tego przewodnika po wdrożeniu kodu w języku Python do Azure Functions! Teraz możesz utworzyć wiele innych funkcji bezserwerowych.

Jak wspomniano wcześniej, możesz dowiedzieć się więcej o rozszerzeniu funkcji, odwiedzając jego repozytorium GitHub, [programu vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Problemy i wkłady są również powitania.

Zapoznaj się z [omówieniem Azure Functions](functions-overview.md) , aby poznać różne wyzwalacze, których można użyć.

Aby dowiedzieć się więcej na temat usług platformy Azure, których można użyć z poziomu języka Python, w tym magazynu danych oraz usług AI i Machine Learning, odwiedź [Centrum deweloperów języka Python platformy Azure](/azure/python/?view=azure-python).

Istnieją również inne rozszerzenia platformy Azure dla Visual Studio Code, które mogą być przydatne. Po prostu wyszukaj ciąg "Azure" w Eksploratorze rozszerzeń:

![Rozszerzenia platformy Azure dla Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Oto kilka popularnych rozszerzeń:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Usługa Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
