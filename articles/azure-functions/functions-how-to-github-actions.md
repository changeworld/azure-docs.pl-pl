---
title: Korzystanie z akcji usługi GitHub w celu wprowadzania aktualizacji kodu w Azure Functions
description: Dowiedz się, jak używać akcji usługi GitHub do definiowania przepływu pracy do kompilowania i wdrażania projektów Azure Functions w usłudze GitHub.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 486033ef4120d721458add7f23cdf9b78a44a388
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928351"
---
# <a name="continuous-delivery-by-using-github-action"></a>Ciągłe dostarczanie za pomocą akcji GitHub

[Akcje GitHub](https://github.com/features/actions) umożliwiają zdefiniowanie przepływu pracy w celu automatycznego kompilowania i wdrażania kodu funkcji w aplikacji funkcji na platformie Azure. 

> [!IMPORTANT]  
> Akcje usługi GitHub są obecnie dostępne w wersji beta. Musisz najpierw [utworzyć konto, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta usługi GitHub.

W akcjach usługi GitHub [przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) to zautomatyzowany proces zdefiniowany w repozytorium GitHub. Ten proces zawiera informacje dotyczące kompilowania i wdrażania projektu aplikacji usługi Functions w witrynie GitHub. 

Przepływ pracy jest definiowany przez plik YAML (. yml) w ścieżce `/.github/workflows/` w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy. 

W przypadku przepływu pracy Azure Functions plik ma trzy sekcje: 

| Sekcja | Zadania |
| ------- | ----- |
| **Uwierzytelnianie** | <ol><li>Zdefiniuj nazwę główną usługi.</li><li>Pobierz profil publikowania.</li><li>Utwórz wpis tajny usługi GitHub.</li></ol>|
| **Utworzenia** | <ol><li>Skonfiguruj środowisko.</li><li>Kompiluj aplikację funkcji.</li></ol> |
| **Wdrażanie** | <ol><li>Wdróż aplikację funkcji.</li></ol>|

> [!NOTE]
> Nie trzeba tworzyć jednostki usługi, jeśli zdecydujesz się na użycie profilu publikowania na potrzeby uwierzytelniania.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Nazwę główną usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

W tym przykładzie Zastąp symbole zastępcze w zasobie IDENTYFIKATORem subskrypcji, grupą zasobów i nazwą aplikacji funkcji. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do aplikacji funkcji. Skopiuj ten obiekt JSON, którego możesz użyć do uwierzytelniania z usługi GitHub.

> [!IMPORTANT]
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Dlatego zakres w poprzednim przykładzie jest ograniczony do określonej aplikacji funkcji, a nie całej grupy zasobów.

## <a name="download-the-publishing-profile"></a>Pobieranie profilu publikowania

Możesz pobrać profil publikacji functionapp, przechodząc do strony **Przegląd** swojej aplikacji, a następnie klikając pozycję **Pobierz profil publikowania**.

   ![Pobierz profil publikowania](media/functions-how-to-github-actions/get-publish-profile.png)

Skopiuj zawartość pliku.

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

1. W witrynie [GitHub](https://github.com)Przejrzyj repozytorium, wybierz pozycję **Ustawienia** > wpisy **tajne** > **Dodaj nowe hasło**.

   ![Dodaj klucz tajny](media/functions-how-to-github-actions/add-secret.png)

1. Użyj `AZURE_CREDENTIALS` dla **nazwy** i skopiowanych danych wyjściowych polecenia dla **wartości**, jeśli następnie wybierz pozycję **Dodaj klucz tajny**. Jeśli używasz profilu publikowania, użyj `SCM_CREDENTIALS` dla **nazwy** i zawartości pliku dla **wartości**.

Usługa GitHub umożliwia teraz uwierzytelnianie w aplikacji funkcji na platformie Azure.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska 

Konfigurowanie środowiska można wykonać przy użyciu jednej z akcji instalacji publikowania.

|Język | Akcja konfiguracji |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

W poniższych przykładach przedstawiono część przepływu pracy, który konfiguruje środowisko dla różnych obsługiwanych języków:

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>Tworzenie aplikacji funkcji

Jest to zależne od języka i języków obsługiwanych przez Azure Functions Ta sekcja powinna być standardowym etapem kompilacji każdego języka.

W poniższych przykładach pokazano część przepływu pracy, która kompiluje aplikację funkcji, w różnych obsługiwanych językach.:

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```

## <a name="deploy-the-function-app"></a>Wdrażanie aplikacji funkcji

Aby wdrożyć kod w aplikacji funkcji, musisz użyć akcji `Azure/functions-action`. Ta akcja ma dwa parametry:

|Parametr |Wyjaśnienie  |
|---------|---------|
|**_Nazwa aplikacji_** | Wypełnione Nazwa aplikacji funkcji. |
|_**Nazwa gniazda**_ | Obowiązkowe Nazwa [miejsca wdrożenia](functions-deployment-slots.md) , które ma zostać wdrożone. Gniazdo musi być już zdefiniowane w aplikacji funkcji. |


W poniższym przykładzie użyta zostanie wersja 1 `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić kompletny przepływ pracy. YAML, zobacz jeden z plików w [repozytorium przykładów przepływu pracy akcji usługi Azure GitHub](https://github.com/Azure/actions-workflow-samples) , który ma `functionapp` w nazwie. Możesz użyć tych przykładów jako punktu wyjścia dla przepływu pracy.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o akcjach usługi GitHub](https://help.github.com/en/articles/about-github-actions)
