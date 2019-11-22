---
title: Zarządzanie wpisami tajnymi podczas pracy z obszarem dev platformy Azure
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280007"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Zarządzanie wpisami tajnymi podczas pracy z obszarem dev platformy Azure

Twoje usługi mogą wymagać pewnych haseł, parametrów połączenia i innych wpisów tajnych, takich jak bazy danych lub inne bezpieczne usługi platformy Azure. Ustawiając wartości tych kluczy tajnych w plikach konfiguracji, można udostępnić je w kodzie jako zmienne środowiskowe.  Należy zachować ostrożność, aby uniknąć naruszenia bezpieczeństwa wpisów tajnych.

Azure Dev Spaces oferuje dwie zalecane, Usprawnione opcje przechowywania wpisów tajnych na wykresach Helm generowanych przez narzędzia klienta Azure Dev Spaces: w pliku wartości. dev. YAML i bezpośrednio w azds. YAML. Nie zaleca się przechowywania wpisów tajnych w wartościach. YAML. Poza dwoma podejściami dla wykresów Helm generowanych przez narzędzia klienta zdefiniowane w tym artykule, jeśli utworzysz własny wykres Helm, możesz użyć wykresu Helm bezpośrednio do zarządzania i przechowywania wpisów tajnych.

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
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metoda 2: Wbudowana bezpośrednio w azds. YAML
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

## <a name="next-steps"></a>Następne kroki

Za pomocą tych metod można teraz bezpiecznie łączyć się z bazą danych, usługą Azure cache for Redis lub uzyskiwać dostęp do bezpiecznych usług platformy Azure.
 
