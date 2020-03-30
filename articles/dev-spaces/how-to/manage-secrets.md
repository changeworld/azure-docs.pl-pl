---
title: Jak zarządzać wpisami tajnymi podczas pracy z przestrzenią deweloperów platformy Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Dowiedz się, jak używać wpisów tajnych usługi Kubernetes w czasie uruchamiania lub tworzenia podczas tworzenia aplikacji za pomocą usługi Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Usługa kontenera azure, kontenery
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438463"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Jak zarządzać wpisami tajnymi podczas pracy z przestrzenią deweloperów platformy Azure

Usługi mogą wymagać pewnych haseł, parametry połączenia i innych wpisów tajnych, takich jak bazy danych lub innych bezpiecznych usług platformy Azure. Ustawiając wartości tych wpisów tajnych w plikach konfiguracyjnych, można udostępnić je w kodzie jako zmienne środowiskowe.  Te pliki konfiguracyjne muszą być traktowane z ostrożnością, aby uniknąć naruszenia bezpieczeństwa wpisów tajnych.

## <a name="storing-and-using-runtime-secrets"></a>Przechowywanie i używanie wpisów tajnych środowiska uruchomieniowego

Usługa Azure Dev Spaces udostępnia dwie zalecane, usprawnione opcje przechowywania wpisów tajnych na `values.dev.yaml` wykresach Helm generowanych `azds.yaml`przez narzędzia klienta usługi Azure Dev Spaces: w pliku i w inline bezpośrednio w pliku . Nie zaleca się przechowywania tajemnic `values.yaml`w .

> [!NOTE]
> Poniższe podejścia pokazują, jak przechowywać i używać wpisów tajnych dla wykresów Helm generowane przez narzędzia klienta. Jeśli tworzysz własny wykres Helm, można użyć wykresu Helm bezpośrednio do zarządzania i przechowywania wpisów tajnych.

### <a name="using-valuesdevyaml"></a>Korzystanie z pliku values.dev.yaml

W projekcie, który został już przygotowany za `values.dev.yaml` pomocą usługi Azure `azds.yaml` Dev Spaces, utwórz plik w tym samym folderze, aby zdefiniować tajne klucze i wartości. Przykład:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Sprawdź `azds.yaml` odwołania do `values.dev.yaml` plików jako `?`opcjonalne za pomocą pliku . Przykład:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Jeśli masz dodatkowe tajne pliki, możesz dodać je również tutaj.

Aktualizowanie lub weryfikowanie usługi odwołuje się do wpisów tajnych jako zmienne środowiskowe. Przykład:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Uruchom zaktualizowane `azds up`usługi przy użyciu programu .

```console
azds up
```
 
Służy `kubectl` do weryfikacji wpisów tajnych zostały utworzone.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Nie zaleca się przechowywania wpisów tajnych w kontroli źródła. Jeśli używasz Git, dodaj `values.dev.yaml` do `.gitignore` pliku, aby uniknąć podawania wpisów tajnych w kontroli źródła.

### <a name="using-azdsyaml"></a>Korzystanie z pliku azds.yaml

W projekcie, który został już przygotowany za pomocą usługi Azure Dev Spaces, dodaj tajne klucze i `azds.yaml`wartość przy użyciu *$PLACEHOLDER* składni w obszarze *configurations.develop.install.set* in . Przykład:

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

> [!NOTE]
> Wartości tajne można wprowadzać bezpośrednio bez użycia *$PLACEHOLDER* `azds.yaml`składni w pliku . Jednak to podejście nie `azds.yaml` jest zalecane, ponieważ jest przechowywany w kontroli źródła.
     
Utwórz `.env` plik w tym `azds.yaml` samym folderze, aby zdefiniować *wartości $PLACEHOLDER.* Przykład:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Nie zaleca się przechowywania wpisów tajnych w kontroli źródła. Jeśli używasz Git, dodaj `.env` do `.gitignore` pliku, aby uniknąć podawania wpisów tajnych w kontroli źródła.

Aktualizowanie lub weryfikowanie usługi odwołuje się do wpisów tajnych jako zmienne środowiskowe. Przykład:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Uruchom zaktualizowane `azds up`usługi przy użyciu programu .

```console
azds up
```
 
Służy `kubectl` do weryfikacji wpisów tajnych zostały utworzone.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Używanie wpisów tajnych jako argumentów kompilacji

W poprzedniej sekcji pokazano, jak przechowywać i używać wpisów tajnych do użycia w czasie wykonywania kontenera. Można również użyć dowolnego klucza tajnego w czasie kompilacji kontenera, takiego jak hasło dla prywatnego NuGet, używając `azds.yaml`.

W `azds.yaml`programie ustaw wpisy tajne czasu kompilacji w *configurations.develop.build.args* przy użyciu `<variable name>: ${secret.<secret name>.<secret key>}` składni. Przykład:

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

W powyższym przykładzie *mynugetsecret* jest istniejącym kluczem tajnym, a *pattoken* jest istniejącym kluczem.

>[!NOTE]
> Tajne nazwy i klucze mogą zawierać `.` znak. Służy `\` do `.` ucieczki podczas przekazywania wpisów tajnych jako argumentów kompilacji. Na przykład, aby przekazać klucz tajny o nazwie *foo.bar* z kluczem *tokenu:* `MYTOKEN: ${secret.foo\.bar.token}`. Ponadto wpisy tajne mogą być oceniane za pomocą prefiksu i tekstu postfix. Na przykład `MYURL: eus-${secret.foo\.bar.token}-version1`. Ponadto wpisy tajne dostępne w przestrzeniach nadrzędnych i dziadków mogą być przekazywane jako argumenty kompilacji.

W pliku Dockerfile użyj *arg* dyrektywy do korzystania z klucza tajnego, a następnie użyj tej samej zmiennej później w Dockerfile. Przykład:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Zaktualizuj usługi uruchomione w klastrze za pomocą tych zmian. W wierszu polecenia uruchom polecenie:

```
azds up
```

## <a name="next-steps"></a>Następne kroki

Dzięki tym metodom można teraz bezpiecznie połączyć się z bazą danych, pamięcią podręczną Azure Cache for Redis lub uzyskać dostęp do bezpiecznych usług platformy Azure.
 
