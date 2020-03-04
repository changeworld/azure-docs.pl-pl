---
title: Publikowanie funkcji na platformie Azure przy użyciu języka Java i Maven
description: Utwórz i Opublikuj funkcję wyzwalaną przez protokół HTTP na platformie Azure przy użyciu języka Java i Maven.
author: rloutlaw
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 262afc2aa51aea260d5bd810b12e09de60b0c371
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249598"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Szybki Start: używanie języka Java i Maven do tworzenia i publikowania funkcji na platformie Azure

W tym artykule opisano sposób kompilowania i publikowania funkcji języka Java w celu Azure Functions przy użyciu narzędzia wiersza polecenia Maven. Gdy wszystko będzie gotowe, kod funkcji jest uruchamiany na platformie Azure w [planie hostingu bezserwerowym](functions-scale.md#consumption-plan) i jest wyzwalany przez żądanie HTTP.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- Zestaw [Java Developer Kit](https://aka.ms/azure-jdks), wersja 8
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
- [Interfejs wiersza polecenia platformy Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.6.666 lub nowszej

Potrzebna jest również aktywna subskrypcja platformy Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="generate-a-new-functions-project"></a>Generowanie nowego projektu usługi Functions

W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Jeśli występują problemy z uruchomieniem polecenia, należy zapoznać się z używaną wersją `maven-archetype-plugin`. Ponieważ uruchamiasz polecenie w pustym katalogu bez pliku `.pom`, może być podjęta próba użycia wtyczki starszej wersji od `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, jeśli uaktualniono Maven ze starszej wersji. Jeśli tak, spróbuj usunąć katalog `maven-archetype-plugin` i ponownie uruchomić polecenie.

### <a name="windows"></a>System Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

Maven prosi o podanie wartości, które są potrzebne, aby zakończyć Generowanie projektu przy wdrożeniu. Po wyświetleniu monitu podaj następujące wartości:

| Wartość | Opis |
| ----- | ----------- |
| **groupId** | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. Przykłady w tym przewodniku szybki start używają `com.fabrikam.functions`. |
| **artifactId** | Wartość, która jest nazwą jar, bez numeru wersji. Przykłady w tym przewodniku szybki start używają `fabrikam-functions`. |
| **Wersja** | Wybierz wartość domyślną `1.0-SNAPSHOT`. |
| **Package** | Wartość, która jest pakiet języka Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. Przykłady w tym przewodniku szybki start używają `com.fabrikam.functions`. |
| **Argumentu** | Globalnie unikatowa nazwa identyfikująca nową aplikację funkcji na platformie Azure. Użyj wartości domyślnej, która jest _artifactId_ dołączona z liczbą losową. Zanotuj tę wartość, która będzie potrzebna później. |
| **appRegion** | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. Wartość domyślna to `westus`. Uruchom to polecenie [Interfejs wiersza polecenia platformy Azure] , aby uzyskać listę wszystkich regionów:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Nazwa nowej [grupy zasobów](../azure-resource-manager/management/overview.md) , w której ma zostać utworzona aplikacja funkcji. Użyj `myResourceGroup`, który jest używany w przykładach w tym przewodniku Szybki Start. Grupa zasobów musi być unikatowa dla Twojej subskrypcji platformy Azure.|

Wpisz `Y` lub naciśnij klawisz ENTER, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, w tym przykładzie `fabrikam-functions`. 

Otwórz plik New Function. Java ze ścieżki *src/Main/Java* w edytorze tekstów i przejrzyj wygenerowany kod. Ten kod jest funkcją [wyzwalaną przez protokół http](functions-bindings-http-webhook.md) , która umożliwia echo treści żądania. 

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom następujące polecenie, które zmienia katalog na nowo utworzony folder projektu, a następnie kompiluje i uruchamia projekt funkcji:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Poniższe dane wyjściowe są wyświetlane w Azure Functions Core Tools podczas lokalnego uruchamiania projektu:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Wyzwól funkcję z wiersza polecenia przy użyciu zwinięcia w nowym oknie terminalu:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```output
Hello AzureFunctions!
```
[Klucz funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys) nie jest wymagany w przypadku uruchamiania lokalnego. Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl+C` w oknie terminala.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Aplikacja funkcji i powiązane zasoby są tworzone na platformie Azure podczas pierwszego wdrożenia aplikacji funkcji. Przed wdrożeniem programu Użyj polecenia [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI, aby zalogować się do subskrypcji platformy Azure. 

```azurecli
az login
```

> [!TIP]
> Jeśli Twoje konto ma dostęp do wielu subskrypcji, użyj polecenie [AZ Account Set](/cli/azure/account#az-account-set) , aby ustawić domyślną subskrypcję tej sesji. 

Użyj następującego polecenia Maven, aby wdrożyć projekt w nowej aplikacji funkcji. 

```console
mvn azure-functions:deploy
```

Ten `azure-functions:deploy` element docelowy Maven tworzy następujące zasoby na platformie Azure:

+ Grupa zasobów. Nazwana z _podaną w podanej_ nazwie.
+ Konto magazynu. Wymagane przez funkcje. Nazwa jest generowana losowo na podstawie wymagań dotyczących nazw kont magazynu.
+ Plan usługi App Service. Hosting bezserwerowy dla aplikacji funkcji w określonym _appRegion_. Nazwa jest generowana losowo.
+ Aplikacja funkcji. Aplikacja funkcji jest jednostką wdrażania i wykonywania dla funkcji. Nazwa jest _nazwą użytkownika,_ dołączona przy użyciu losowo wygenerowanego numeru. 

Wdrożenie obejmuje również pakiety plików projektu i wdraża je w nowej aplikacji funkcji przy użyciu [wdrożenia zip](functions-deployment-technologies.md#zip-deploy)z włączonym trybem uruchamiania z pakietu.

Po zakończeniu wdrażania zobaczysz adres URL, za pomocą którego możesz uzyskiwać dostęp do punktów końcowych aplikacji funkcji. Ponieważ wyzwalany przez siebie wyzwalacz protokołu HTTP używa `authLevel = AuthorizationLevel.FUNCTION`, należy uzyskać klucz funkcji, aby wywołać punkt końcowy funkcji za pośrednictwem protokołu HTTP. Najprostszym sposobem uzyskania klucza funkcji jest z [Azure Portal].

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Pobierz adres URL wyzwalacza HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Możesz uzyskać adres URL wymagany do wyzwolenia funkcji przy użyciu klucza funkcji z Azure Portal. 

1. Przejdź do [Azure Portal], zaloguj się, _wpisz nazwę aplikacji funkcji w polu_ **wyszukiwania** w górnej części strony, a następnie naciśnij klawisz ENTER.
 
1. W aplikacji funkcji rozwiń pozycję **funkcje (tylko do odczytu)** , wybierz funkcję, a następnie wybierz opcję **</> Pobierz adres URL funkcji** w prawym górnym rogu. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Wybierz pozycję **domyślne (klawisz funkcyjny)** i wybierz pozycję **Kopiuj**. 

Możesz teraz użyć skopiowanego adresu URL, aby uzyskać dostęp do funkcji.

## <a name="verify-the-function-in-azure"></a>Weryfikowanie funkcji na platformie Azure

Aby sprawdzić aplikację funkcji działającą na platformie Azure przy użyciu `cURL`, Zastąp adres URL z poniższego przykładu adresem URL skopiowanym z portalu.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Spowoduje to wysłanie żądania POST do punktu końcowego funkcji z `AzureFunctions` w treści żądania. Zostanie wyświetlona następująca odpowiedź.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Następne kroki

Utworzono projekt funkcji języka Java z funkcją wyzwalaną przez protokół HTTP, uruchom go na komputerze lokalnym i wdrożony na platformie Azure. Teraz możesz rozłożyć funkcję przez...

> [!div class="nextstepaction"]
> [Dodawanie powiązania danych wyjściowych kolejki usługi Azure Storage](functions-add-output-binding-storage-queue-java.md)


[Interfejs wiersza polecenia platformy Azure]: /cli/azure
[Azure Portal]: https://portal.azure.com
