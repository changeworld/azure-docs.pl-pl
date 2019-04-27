---
title: Jak do zarządzania wpisami tajnymi, pracując z przestrzenią deweloperów platformy Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 9fe29e8717c76c353f3e95d4693011f3925c4e1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686448"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Jak do zarządzania wpisami tajnymi, pracując z przestrzenią deweloperów platformy Azure

Usługi mogą wymagać niektóre hasłach, parametry połączenia i innych wpisów tajnych, takich jak do baz danych lub innych usług platformy Azure bezpieczne. Przez ustawienie wartości tych kluczy tajnych w plikach konfiguracji, można udostępnić je w swoim kodzie jako zmienne środowiskowe.  Te muszą być obsługiwane z rozwagą, aby uniknąć zagrożenia dla bezpieczeństwa kluczy tajnych.

Usługa Azure Dev do magazynowania zapewnia zaleca się dwie opcje przechowywania wpisów tajnych: w pliku values.dev.yaml i wbudowane bezpośrednio w azds.yaml. Nie zaleca się przechowywanie wpisów tajnych w values.yaml.
 
## <a name="method-1-valuesdevyaml"></a>Metoda 1: values.dev.yaml
1. Otwórz program VS Code z projektem, który jest włączony dla usługi Azure Dev miejsca do magazynowania.
2. Dodaj plik o nazwie _values.dev.yaml_ w tym samym folderze co istniejące _values.yaml_ i zdefiniuj klucz tajny i wartości, jak w poniższym przykładzie:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Aktualizacja _azds.yaml_ mówić Azure Dev miejsca do magazynowania do użycia nowego _values.dev.yaml_ pliku. Aby to zrobić, należy dodać tę konfigurację w sekcji configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Zmodyfikuj kod usługi do odwoływania się do tych kluczy tajnych jako zmienne środowiskowe, jak w poniższym przykładzie:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Zaktualizuj usługi działające w klastrze za pomocą tych zmian. W wierszu polecenia Uruchom polecenie:

    ```
    azds up
    ```
 
6. (Opcjonalnie) W wierszu polecenia Sprawdź, czy utworzono tych kluczy tajnych:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Upewnij się, że dodano _values.dev.yaml_ do _.gitignore_ plik, aby unikać zatwierdzania wpisów tajnych w kontroli źródła.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metoda 2. Wbudowane bezpośrednio w azds.yaml
1.  W _azds.yaml_, ustaw wpisami tajnymi w ramach yaml sekcji konfiguracji/tworzenie/instalację. Chociaż możesz wprowadzać hasło wartości bezpośrednio, nie zaleca się ponieważ _azds.yaml_ jest ewidencjonowany w kontroli źródła. Zamiast tego dodać symbole zastępcze przy użyciu składni "$PLACEHOLDER".

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  Tworzenie _ENV_ pliku w tym samym folderze co _azds.yaml_. Wprowadź wpisami tajnymi za pomocą standardowych klucz = wartość notacji. Zatwierdź nie _ENV_ plików do kontroli źródła. (Aby pominąć z kontroli źródła w systemach kontroli wersji git, dodaj go do _.gitignore_ pliku.) W poniższym przykładzie przedstawiono _ENV_ pliku:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Zmodyfikuj kod źródłowy usługi, aby odwoływać się do tych kluczy tajnych w kodzie, jak w poniższym przykładzie:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Zaktualizuj usługi działające w klastrze za pomocą tych zmian. W wierszu polecenia Uruchom polecenie:

    ```
    azds up
    ```

4.  (opcjonalnie) Wpisy tajne widoku z narzędzia kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Kolejne kroki

Za pomocą tych metod można teraz bezpiecznie Połącz z bazą danych, pamięci podręcznej Redis Azure lub dostęp do bezpiecznych usług platformy Azure.
 
