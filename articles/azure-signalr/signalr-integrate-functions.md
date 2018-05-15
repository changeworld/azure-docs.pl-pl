---
title: Samouczek integrowania usługi Azure Functions z usługą Azure SignalR Service | Microsoft Docs
description: Z tego samouczka dowiesz się, jak używać usługi Azure Functions z usługą Azure SignalR Service
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Samouczek: integracja usługi Azure Functions z usługą Azure SignalR Service

Ten samouczek opiera się na aplikacji pokoju rozmów utworzonej w poprzednich samouczkach. Jeśli nie zostały wykonane ćwiczenia [Tworzenie pokoju rozmów za pomocą usługi SignalR Service](signalr-quickstart-dotnet-core.md) i [Uwierzytelnianie usługi Azure SignalR Service](./signalr-authenticate-oauth.md), najpierw je wykonaj. 

Typowy scenariusz dla aplikacji działających w czasie rzeczywistym dotyczy aktualizacji zawartości pochodzących z serwera do opublikowania w klientach internetowych. Usługa [Azure Functions](../azure-functions/functions-overview.md) jest doskonałym kandydatem do generowania tych aktualizacji zawartości. Najważniejszą korzyścią z używania usługi Azure Functions jest możliwość uruchomienia kodu na żądanie bez obaw o architekturę całej aplikacji czy infrastrukturę do jej uruchomienia. Ponadto płacisz tylko za rzeczywisty czas działania kodu.  

Zazwyczaj ten scenariusz będzie stanowił problem podczas próby użycia usługi SignalR, ponieważ usługa SignalR próbuje utrzymać połączenie między klientem a serwerem w celu wypychania aktualizacji zawartości. Ponieważ kod działa tylko na żądanie, połączenie nie może być utrzymane. Jednak usługa Azure SignalR Service może obsługiwać ten scenariusz, ponieważ zarządza Twoimi połączeniami w środowisku uruchomieniowym.

W tym samouczku użyjesz usługi Azure Functions do generowania komunikatów za pomocą funkcji czasomierza na początku każdej minuty. Funkcja będzie publikować komunikaty do wszystkich klientów w pokoju rozmów utworzonym w poprzednich samouczkach. Aby uzyskać więcej informacji na temat funkcji czasomierza, zobacz [Funkcja czasomierza](../azure-functions/functions-create-scheduled-function.md).

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

Kod dla tego samouczka jest dostępny do pobrania w [repozytorium GitHub o nazwie AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Aplikacja rozmowy z komunikatami serwera](./media/signalr-integrate-functions/signalr-functions-complete.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej funkcji czasomierza usługi Azure Functions przy użyciu interfejsu wiersza polecenia platformy Azure.
> * Konfigurowanie funkcji czasomierza dla wdrożenia lokalnego repozytorium Git.
> * Łączenie czasomierza z Twoją usługą SignalR Service, aby wypychać aktualizacje co minutę.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są:

* [Usluga Git](https://git-scm.com/)
* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Skonfigurowana usługa Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Pobrane lub sklonowane repozytorium GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples)


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Musisz utworzyć aplikację funkcji, aby zdefiniować środowisko wykonawcze dla swoich funkcji. Aplikacja funkcja umożliwia także zgrupowanie wielu funkcji jako jednostki logicznej w celu ułatwienia zarządzania, wdrażania oraz udostępniania zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej funkcji przy użyciu interfejsu wiersza polecenia platformy Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md).

W tej sekcji użyjesz usługi Azure Cloud Shell do utworzenia nowej aplikacji usługi Azure Function skonfigurowanej do wdrożenia z lokalnego repozytorium Git. 

Podczas tworzenia zasobów aplikacji funkcji należy je utworzyć w tej samej grupie zasobów co w poprzednich samouczkach. Takie podejście ułatwia zarządzanie wszystkimi zasobami samouczka.

Skopiuj poniższy skrypt do edytora tekstu, a następnie zastąp wartości parametrów zmiennych wartościami dla Twoich zasobów. Skopiuj i wklej zaktualizowany skrypt do swojej usługi Azure Cloud Shell i naciśnij klawisz **Enter**, aby utworzyć konto magazynu i aplikację funkcji.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Parametr | Opis |
| -------------------- | --------------- |
| ResourceGroupName | Ta nazwa grupy zasobów została zasugerowana w poprzednich samouczkach. Warto zachować wszystkie zasoby samouczka zgrupowane razem. Użyj tej samej grupy zasobów co w poprzednich samouczkach. | 
| location | Zaktualizuj tę zmienną do tej samej lokalizacji, co użyta do utworzenia grupy zasobów w poprzednich samouczkach. | 
| functionappName | Zaktualizuj tę zmienną o unikatową nazwę nowej aplikacji funkcji. Na przykład signalrfunctionapp22665120. | 
| storageAccountName | Wprowadź nazwę nowego konta magazynu do przechowywania kodu i ustawień aplikacji funkcji. | 



## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

W tej sekcji skonfigurujesz aplikację funkcji za pomocą ustawień aplikacji zawierających parametry połączenia dla zasobu usługi Azure SignalR Service. Twój kod funkcji będzie używał tego ustawienia do łączenia z pokojem rozmów i publikowania tam komunikatów. Skonfigurujesz też aplikację funkcji do wdrożenia z lokalnego repozytorium Git.

Skopiuj poniższy skrypt i zastąp wartości parametrów. Wklej zaktualizowany skrypt do usługi Azure Cloud Shell i naciśnij klawisz **Enter**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Parametr | Opis |
| -------------------- | --------------- |
| ResourceGroupName | Ta nazwa grupy zasobów została zasugerowana w poprzednich samouczkach. Warto zachować wszystkie zasoby samouczka zgrupowane razem. Użyj tej samej grupy zasobów co w poprzednich samouczkach. | 
| functionappName | Zaktualizuj tę zmienną o unikatową nazwę nowej aplikacji funkcji. Na przykład signalrfunctionapp22665120. | 
| connstring | Wprowadź parametry połączenia dla swojego zasobu usługi SignalR Service. Te parametry połączenia możesz pobrać ze strony zasobów usługi SignalR Service w witrynie Azure Portal, klikając pozycję **Klucze** w obszarze **USTAWIENIA**. | 



Zanotuj adres URL wdrożenia Git zwrócony przez ostatnie polecenie. Tego adresu URL użyjesz do wdrożenia kodu funkcji.


## <a name="the-timer-function"></a>Funkcja czasomierza

Przykład funkcji czasomierza znajduje się w katalogu */samples/Timer* w pobranych plikach lub sklonowanym repozytorium GitHub o nazwie [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). Kod funkcji czasomierza znajduje się w pliku *TimerFunction.cs*. Ten kod używa parametrów połączenia przechowywanych z domyślnym kluczem konfiguracji (*Azure:SignalR:ConnectionString*) w celu utworzenia serwera proxy dla centrum. Ponieważ kod funkcji działa po stronie serwera, nie ma powodu, aby wymagać od niego uwierzytelniania jako zwykłego klienta. Kod jest zaufany do używania parametrów połączenia. Za pomocą tego serwera proxy centrum kod funkcji może wywołać dowolną z metod zdefiniowanych w Twoim centrum. Kod wywołuje metodę `BroadcastMessage`, aby opublikować komunikat zawierający bieżący czas uruchomienia wyzwalacza.

Wyzwalaczem dla kodu funkcji jest *timerTrigger* zdefiniowany w powiązaniach w pliku *TimerFunction/function.json*. Zawiera on [wyrażenie CRON](https://wikipedia.org/wiki/Cron#CRON_expression) do ustawienia uruchomienia wyzwalacza czasomierza na początku każdej minuty.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Tworzenie funkcji czasomierza

W poniższych krokach użyj [interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/), aby skompilować funkcję i przygotować ją do wdrożenia:

1. Przejdź do podkatalogu */samples/Timer* w pobranych plikach lub sklonowanym repozytorium GitHub o nazwie [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

2. Przywróć pakiety NuGet za pomocą następującego polecenia:

        dotnet restore

3. Skompiluj aplikację funkcji *Timer* przy użyciu następującego polecenia:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Tworzenie i wdrażanie lokalnego repozytorium Git

1. W powłoce Git przejdź do podkatalogu kompilacji */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Zainicjuj katalog jako nowe repozytorium Git przy użyciu następującego polecenia:

        git init

3. Dodaj nowe zatwierdzenie dla wszystkich plików w katalogu kompilacji.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Dodaj zdalny punkt końcowy dla adresu URL wdrożenia Git, który został zanotowany podczas konfigurowania Twojej aplikacji funkcji:

        git remote add Azure <enter your Git deployment URL>

5. Wdrażanie aplikacji funkcji

        git push Azure master

   Po wdrożeniu kodu czasomierz natychmiast rozpocznie wyzwalanie co minutę, aby wykonać Twój kod.

## <a name="test-the-chat-app"></a>Testowanie aplikacji rozmów

Przejdź do aplikacji rozmów, a funkcja czasomierza, która właśnie została utworzona, będzie teraz raportować czas na początku każdej minuty.

![Aplikacja rozmowy z komunikatami serwera](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować testowanie aplikacji, możesz zachować właśnie utworzone zasoby.

W przeciwnym razie po zakończeniu przykładowej aplikacji możesz usunąć zasoby platformy Azure, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
> 
> 

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *SignalRTestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

   
![Usuwanie](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.
   
Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób integracji z usługą Azure Function, aby wypychać aktualizacje do klientów w oparciu o wyzwalacze usługi Azure Function. Aby dowiedzieć się więcej o korzystaniu z serwera Azure SignalR Server, przejdź do przykładów interfejsu wiersza polecenia platformy Azure dla usługi SignalR Service.

> [!div class="nextstepaction"]
> [Przykłady Interfejsu wiersza polecenia usługi Azure SignalR](./signalr-cli-samples.md)



