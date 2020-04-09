---
title: Wprowadzanie aktualizacji kodu w usłudze Azure Functions za pomocą akcji GitHub
description: Dowiedz się, jak używać akcji GitHub do definiowania przepływu pracy do tworzenia i wdrażania projektów usługi Azure Functions w usłudze GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878208"
---
# <a name="continuous-delivery-by-using-github-action"></a>Ciągłe dostarczanie za pomocą akcji GitHub

[Akcje GitHub](https://github.com/features/actions) umożliwia zdefiniowanie przepływu pracy, aby automatycznie tworzyć i wdrażać kod funkcji do aplikacji funkcji na platformie Azure. 

W usłudze GitHub [akcje przepływu pracy](https://help.github.com/articles/about-github-actions#workflow) jest zautomatyzowany proces, który można zdefiniować w repozytorium GitHub. Ten proces informuje GitHub, jak tworzyć i wdrażać projekt aplikacji funkcji w usłudze GitHub. 

Przepływ pracy jest definiowany przez plik YAML (.yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składają się na przepływ pracy. 

W przypadku przepływu pracy usługi Azure Functions plik składa się z trzech sekcji: 

| Sekcja | Zadania |
| ------- | ----- |
| **Authentication** | <ol><li>Zdefiniuj jednostkę usługi.</li><li>Pobierz profil publikowania.</li><li>Tworzenie klucza tajnego Usługi GitHub.</li></ol>|
| **Kompilacja** | <ol><li>Skonfiguruj środowisko.</li><li>Tworzenie aplikacji funkcji.</li></ol> |
| **Wdróż** | <ol><li>Wdrażanie aplikacji funkcji.</li></ol>|

> [!NOTE]
> Nie trzeba tworzyć jednostki usługi, jeśli zdecydujesz się użyć profilu publikowania do uwierzytelniania.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Jednostkę usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć za pomocą polecenia [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [usłudze Azure CLI](/cli/azure/). To polecenie można uruchomić przy użyciu [usługi Azure Cloud Shell](https://shell.azure.com) w witrynie Azure portal lub wybierając przycisk **Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

W tym przykładzie zastąp symbole zastępcze w zasobie identyfikatorem subskrypcji, grupą zasobów i nazwą aplikacji funkcji. Dane wyjściowe to poświadczenia przypisania roli, który zapewnia dostęp do aplikacji funkcji. Skopiuj ten obiekt JSON, którego można użyć do uwierzytelniania z usługi GitHub.

> [!IMPORTANT]
> Zawsze dobrą praktyką jest przyznanie minimalnego dostępu. Dlatego zakres w poprzednim przykładzie jest ograniczony do określonej aplikacji funkcji, a nie do całej grupy zasobów.

## <a name="download-the-publishing-profile"></a>Pobierz profil publikowania

Możesz pobrać profil publikowania aplikacji funkcyjnej, przechodząc do strony **Przegląd** aplikacji i klikając **pozycję Pobierz profil publikowania**.

   ![Pobierz profil publikowania](media/functions-how-to-github-actions/get-publish-profile.png)

Skopiuj zawartość pliku.

## <a name="configure-the-github-secret"></a>Konfigurowanie klucza tajnego usługi GitHub

1. W [usłudze GitHub](https://github.com)przejdź do repozytorium, wybierz pozycję **Ustawienia** > **Sekrety** > Dodaj nowy klucz**tajny.**

   ![Dodaj klucz tajny](media/functions-how-to-github-actions/add-secret.png)

1. Dodaj nowy klucz tajny.

   * Jeśli używasz jednostki usługi utworzonej przy użyciu interfejsu `AZURE_CREDENTIALS` wiersza polecenia platformy Azure, użyj **name**. Następnie wklej skopiowany wynik obiektu JSON dla **wartości**i wybierz pozycję **Dodaj klucz tajny**.
   * Jeśli używasz profilu publikowania, `SCM_CREDENTIALS` użyj **nazwy**. Następnie użyj zawartości pliku profilu publikowania dla **wartości**i wybierz pozycję **Dodaj klucz tajny**.

GitHub można teraz uwierzytelnić do aplikacji funkcji na platformie Azure.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska 

Konfigurowanie środowiska odbywa się przy użyciu akcji konfiguracji publikowania specyficznej dla języka.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia część przepływu pracy, `actions/setup-node` która używa akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia część przepływu pracy, `actions/setup-python` która używa akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład przedstawia część przepływu pracy, `actions/setup-dotnet` która używa akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład przedstawia część przepływu pracy, `actions/setup-java` która używa akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Tworzenie aplikacji funkcji

To zależy od języka i dla języków obsługiwanych przez usługi Azure Functions, ta sekcja powinna być standardowe kroki kompilacji każdego języka.

W poniższym przykładzie przedstawiono część przepływu pracy, która tworzy aplikację funkcji, która jest specyficzna dla języka:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="java"></a>[Java](#tab/java)

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
---

## <a name="deploy-the-function-app"></a>Wdrażanie aplikacji funkcji

Aby wdrożyć kod do aplikacji funkcji, należy `Azure/functions-action` użyć akcji. Ta akcja ma dwa parametry:

|Parametr |Wyjaśnienie  |
|---------|---------|
|**_nazwa aplikacji_** | (Obowiązkowe) Nazwa aplikacji funkcji. |
|_**nazwa gniazda**_ | (Opcjonalnie) Nazwa gniazda [wdrażania,](functions-deployment-slots.md) które chcesz wdrożyć. Gniazdo musi być już zdefiniowane w aplikacji funkcji. |


W poniższym przykładzie użyto `functions-action`wersji 1:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić pełny przepływ pracy .yaml, zobacz jeden z plików w [usłudze Azure GitHub Actions próbkowania ponownego losowania,](https://aka.ms/functions-actions-samples) które mają `functionapp` w nazwie. Można użyć tych przykładów punkt wyjścia dla przepływu pracy.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o akcjach Usługi GitHub](https://help.github.com/en/articles/about-github-actions)
