---
title: Jak zarządzać kluczy tajnych podczas pracy z przestrzenią deweloperów platformy Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Szybkie opracowywanie Kubernetes z kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: douge
ms.openlocfilehash: b77d862f578ddc374dbb58117b4ea58eb973e5fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198317"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Jak zarządzać kluczy tajnych podczas pracy z przestrzenią deweloperów platformy Azure

Usługi mogą wymagać niektórych haseł, parametry połączenia i innych informacji poufnych, takich jak do baz danych lub innymi usługami Azure bezpiecznego. Przez ustawienie wartości tych kluczy tajnych w plikach konfiguracji, można udostępnić je w kodzie jako zmienne środowiskowe.  Te musi obsługiwać z ostrożnością, aby uniknąć zagrożenia dla bezpieczeństwa kluczy tajnych.

Spacje deweloperów platformy Azure udostępnia dwa zalecane opcje do przechowywania kluczy tajnych: w pliku values.dev.yaml i wbudowane bezpośrednio w azds.yaml. Przechowywanie kluczy tajnych w values.yaml nie jest zalecane.
 
## <a name="method-1-valuesdevyaml"></a>Metoda 1: values.dev.yaml
1. Otwórz kodzie VS nad projektem, którym włączono obsługę spacje deweloperów platformy Azure.
2. Dodaj plik o nazwie _values.dev.yaml_ w tym samym folderze co istniejące _values.yaml_ i zdefiniuj tajnego klucza i wartości, jak w poniższym przykładzie:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Aktualizacja _azds.yaml_ mówić spacje deweloperów Azure do użycia nowego _values.dev.yaml_ pliku. Aby to zrobić, Dodaj tę konfigurację w sekcji configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Modyfikowanie kodu usługi do odwoływania się do tych kluczy tajnych jako zmienne środowiskowe, jak w poniższym przykładzie:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Aktualizacja usługi działające w klastrze z tych zmian. W wierszu polecenia Uruchom polecenie:

    ```
    azds up
    ```
 
6. (Opcjonalnie) Z wiersza polecenia Sprawdź, czy utworzono tych kluczy tajnych:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Upewnij się, że możesz dodać _values.dev.yaml_ do _.gitignore_ plik, aby uniknąć zatwierdzania kluczy tajnych w kontroli źródła.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metoda 2: Wbudowane bezpośrednio w azds.yaml
1.  W _azds.yaml_, ustaw kluczy tajnych w obszarze yaml programu sekcji konfiguracji/opracowanie/instalacji. Chociaż możesz wprowadzać klucz tajny wartości bezpośrednio, nie jest zalecane ponieważ _azds.yaml_ jest wyewidencjonowany do kontroli źródła. Zamiast tego dodać symbole zastępcze przy użyciu składni "$PLACEHOLDER".

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
     
2.  Utwórz _.env_ pliku w tym samym folderze co _azds.yaml_. Wprowadź hasła przy użyciu standardowych klucza = wartość notacji. Zatwierdź nie _.env_ plików do kontroli źródła. (Aby pominąć z kontroli źródła w systemów kontroli wersji git, dodaj go do _.gitignore_ pliku.) W poniższym przykładzie przedstawiono _.env_ pliku:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Zmień kod źródłowy usługi do odwołania tych kluczy tajnych w kodzie, jak w poniższym przykładzie:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Aktualizacja usługi działające w klastrze z tych zmian. W wierszu polecenia Uruchom polecenie:

    ```
    azds up
    ```

4.  (opcjonalnie) Wyświetl klucze tajne z kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Kolejne kroki

Z tych metod można teraz bezpiecznie połączenie z bazy danych, pamięć podręczna Redis lub usług Azure bezpiecznego dostępu.
 