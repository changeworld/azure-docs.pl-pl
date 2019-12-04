---
title: Zarządzanie wpisami tajnymi podczas pracy z obszarem dev platformy Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery
ms.openlocfilehash: b184f72dfbbfe093443ab8a9b79bafbece3a3d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790174"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Zarządzanie wpisami tajnymi podczas pracy z obszarem dev platformy Azure

Twoje usługi mogą wymagać pewnych haseł, parametrów połączenia i innych wpisów tajnych, takich jak bazy danych lub inne bezpieczne usługi platformy Azure. Ustawiając wartości tych kluczy tajnych w plikach konfiguracji, można udostępnić je w kodzie jako zmienne środowiskowe.  Należy zachować ostrożność, aby uniknąć naruszenia bezpieczeństwa wpisów tajnych.

Azure Dev Spaces oferuje dwie zalecane, Usprawnione opcje przechowywania wpisów tajnych na wykresach Helm generowanych przez narzędzia klienta Azure Dev Spaces: w pliku `values.dev.yaml` i wbudowane bezpośrednio w `azds.yaml`. Nie zaleca się przechowywania wpisów tajnych w `values.yaml`. Poza dwoma podejściami dla wykresów Helm generowanych przez narzędzia klienta zdefiniowane w tym artykule, jeśli utworzysz własny wykres Helm, możesz użyć wykresu Helm bezpośrednio do zarządzania i przechowywania wpisów tajnych.

## <a name="method-1-valuesdevyaml"></a>Metoda 1: Values. YAML
1. Otwórz VS Code w projekcie, w którym włączono obsługę Azure Dev Spaces.
2. Dodaj plik o nazwie _Values. dev. YAML_ w tym samym folderze co istniejące _azds. YAML_ i zdefiniuj klucz tajny oraz wartości, jak w poniższym przykładzie:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. YAML_ już odwołuje się do _wartości. dev. YAML_ plik, jeśli istnieje. Jeśli wolisz inną nazwę pliku, zaktualizuj sekcję install. Values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Zmodyfikuj kod usługi, aby odwoływać się do tych kluczy tajnych jako zmienne środowiskowe, jak w poniższym przykładzie:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Zaktualizuj usługi działające w klastrze przy użyciu tych zmian. W wierszu polecenia Uruchom polecenie:

    ```
    azds up
    ```
 
6. Obowiązkowe W wierszu polecenia Sprawdź, czy zostały utworzone następujące wpisy tajne:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Upewnij się, że dodano _wartości. dev. YAML_ do pliku _. gitignore_ , aby uniknąć zatwierdzania wpisów tajnych w kontroli źródła.
 
 
## <a name="method-2-azdsyaml"></a>Metoda 2: azds. YAML
1.  W _azds. YAML_Ustaw wpisy tajne w sekcji YAML konfiguracje/projektowanie/Instalowanie. Chociaż w tym miejscu można wprowadzać wartości tajne, nie jest to zalecane, ponieważ _azds. YAML_ jest sprawdzany w kontroli źródła. Zamiast tego Dodaj symbole zastępcze przy użyciu składni "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  Utwórz plik _ENV_ w tym samym folderze, w którym znajduje się _azds. YAML_. Wprowadź wpisy tajne przy użyciu standardowego klucza = notacja wartości. Nie zatwierdzaj pliku _ENV_ do kontroli źródła. (Aby pominąć kontrolę źródła w systemach kontroli wersji opartych na usłudze git, należy dodać ją do pliku _GITIGNORE_ ). W poniższym przykładzie przedstawiono plik _. env_ :

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Zmodyfikuj kod źródłowy usługi, aby odwoływać się do tych wpisów tajnych w kodzie, jak w poniższym przykładzie:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Zaktualizuj usługi działające w klastrze przy użyciu tych zmian. W wierszu polecenia Uruchom polecenie:

    ```
    azds up
    ```

4.  obowiązkowe Wyświetl wpisy tajne z polecenia kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="passing-secrets-as-build-arguments"></a>Przekazywanie wpisów tajnych jako argumentów kompilacji

W poprzednich sekcjach pokazano, jak przekazać klucze tajne do użycia w czasie wykonywania kontenera. W czasie kompilacji kontenera można także przekazać wpis tajny, taki jak hasło dla prywatnego NuGet, przy użyciu `azds.yaml`.

W `azds.yaml`Ustaw klucze tajne czasu kompilacji w *konfiguracjach. opracowywać. Build. args* przy użyciu składni `<variable name>: ${secret.<secret name>.<secret key>}`. Na przykład:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

W powyższym przykładzie *mynugetsecret* jest istniejącym wpisem tajnym, a *pattoken* jest istniejącym kluczem.

>[!NOTE]
> Nazwy i klucze wpisów tajnych mogą zawierać znak `.`. Użyj `\`, aby wyjść `.` podczas przekazywania wpisów tajnych jako argumentów kompilacji. Na przykład, aby przekazać wpis tajny o nazwie *foo. bar* z kluczem *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Ponadto wpisy tajne można ocenić przy użyciu prefiksu i przyrostka tekstu. Na przykład `MYURL: eus-${secret.foo\.bar.token}-version1`. Ponadto klucze tajne dostępne w obszarze nadrzędnym i nadrzędnym mogą być przekazane jako argumenty kompilacji.

W pliku dockerfile Użyj dyrektywy *ARG* , aby użyć klucza tajnego, a następnie użyj tej samej zmiennej w dalszej części pliku dockerfile. Na przykład:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Zaktualizuj usługi działające w klastrze przy użyciu tych zmian. W wierszu polecenia Uruchom polecenie:

```
azds up
```

## <a name="next-steps"></a>Następne kroki

Za pomocą tych metod można teraz bezpiecznie łączyć się z bazą danych, usługą Azure cache for Redis lub uzyskiwać dostęp do bezpiecznych usług platformy Azure.
 
