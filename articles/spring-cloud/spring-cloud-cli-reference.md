---
title: az wiosenna chmura
description: Zarządzanie wiosenną chmurą platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298778"
---
# <a name="az-spring-cloud"></a>az wiosna-chmura

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Zarządzanie wiosenną chmurą platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

>[!Note]
> Usługa Azure Spring Cloud jest obecnie w wersji zapoznawczej.  Te polecenia mogą zostać zmienione lub usunięte w przyszłej wersji.

| az wiosna-chmura |  |
|------|------:|
| [az wiosna-chmura tworzyć](#az-spring-cloud-create) | Utwórz wystąpienie usługi Azure Spring Cloud. |
| [az sprężyna-chmura usunąć](#az-spring-cloud-delete) | Usuń wystąpienie usługi Azure Spring Cloud. |
| [az lista sprężynowa-chmurowa](#az-spring-cloud-list) | Lista wszystkich wystąpień usługi Azure Spring Cloud w danej grupie zasobów, w przeciwnym razie lista identyfikatorów subskrypcji. |
| [az wiosenno-chmurowy pokaz](#az-spring-cloud-show) | Pokaż szczegóły dotyczące usługi Azure Spring Cloud. |

| az aplikacja wiosna-chmura | Polecenia do zarządzania aplikacjami w chmurze Azure Spring Cloud.  |
| ---- | ----: |
| [az aplikacja wiosna-chmura utworzyć](#az-spring-cloud-app-create) | Utwórz nową aplikację z domyślnym wdrożeniem w chmurze Azure Spring Cloud. |
| [az aplikacja wiosna-chmura usunąć](#az-spring-cloud-app-delete) | Usuwanie aplikacji w chmurze Azure Spring Cloud. |
| [az sprężyna chmura aplikacja wdrożyć](#az-spring-cloud-app-deploy) | Wdrażanie z kodu źródłowego lub wstępnie utworzonego pliku binarnego do aplikacji i aktualizowanie powiązanych konfiguracji. |
| [az wiosenna chmura lista aplikacji](#az-spring-cloud-app-list) | Wyświetl listę wszystkich aplikacji w chmurze Azure Spring Cloud. |
| [AZ wiosenne ponowne uruchomienie aplikacji w chmurze](#az-spring-cloud-app-restart) | Uruchom ponownie wystąpienia aplikacji przy użyciu domyślnych wdrożeń produkcyjnych. |
| [skala aplikacji wiosna-chmura az](#az-spring-cloud-app-scale) | Ręcznie skaluj aplikację lub jej wdrożenia. |
| [az wiosenne wdrażanie aplikacji w chmurze](#az-spring-cloud-app-set-deployment) | Ustaw wdrożenie produkcyjne aplikacji. |
| [az wiosenno-chmurowe app show](#az-spring-cloud-app-show) | Pokaż szczegóły aplikacji w chmurze Azure Spring Cloud. |
| [az aplikacja wiosna-chmura show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Pokaż dzienniki kompilacji dla najnowszego wdrożenia ze źródła. Domyślnie do wdrożenia produkcyjnego. |
| [AZ wiosna-chmura aplikacja start](#az-spring-cloud-app-start) | Uruchom wystąpienia aplikacji przy użyciu domyślnych wdrożeń produkcyjnych. |
| [az wiosna-chmura app stop](#az-spring-cloud-app-stop) | Zatrzymaj wystąpienia aplikacji przy użyciu domyślnych wdrożeń produkcyjnych. |
| [az wiosenna aktualizacja aplikacji w chmurze](#az-spring-cloud-app-update) | Zaktualizuj konfigurację określonej aplikacji. |

| az sprężynowa aplikacja powiązana | Polecenia do zarządzania powiązaniami za pomocą usług Azure Data Services.  Aplikacja musi zostać ponownie uruchomiona, zanim te ustawienia zajmą się efektem. |
| --- | ---: |
| [az wiosenna lista powiązań aplikacji w chmurze](#az-spring-cloud-app-binding-list) | Wyświetl listę wszystkich powiązań usługi w aplikacji. |
| [az sprężyna-chmura wiązanie usunąć](#az-spring-cloud-app-binding-remove) | Usuń powiązanie usługi z aplikacji. |
| [az wiosenno-chmurowy pokaz oprawy aplikacji](#az-spring-cloud-app-binding-show) | Pokaż szczegóły powiązania usługi. |
| [az wiosna-chmura aplikacja powiązanie kosmos dodać](#az-spring-cloud-app-binding-cosmos-add) | Powiąż usługę Azure CosmosDB z aplikacją. |
| [az wiosenna aktualizacja programu windows-cloud](#az-spring-cloud-app-binding-cosmos-update) | Zaktualizuj powiązanie usługi Azure CosmosDB. |
| [az wiosna-chmura aplikacja powiązanie mysql dodać](#az-spring-cloud-app-binding-mysql-add) | Powiąż usługę Azure Database for MySQL z aplikacją. |
| [az wiosna-chmura aplikacja powiązanie mysql aktualizacja](#az-spring-cloud-app-binding-mysql-update) | Zaktualizuj powiązanie usługi MySQL usługi Azure Database. |
| [az wiosna-chmura aplikacja powiązanie redis dodać](#az-spring-cloud-app-binding-redis-add) | Powiąż pamięć podręczną platformy Azure dla redis z aplikacją. |
| [az wiosenna aktualizacja wiązań aplikacji w chmurze](#az-spring-cloud-app-binding-redis-update) | Zaktualizuj powiązanie usługi Usługi Redis w pamięci podręcznej platformy Azure. |

| wdrożenie aplikacji az wiosna-chmura | Polecenia do zarządzania cyklem wdrażania aplikacji w usłudze Azure Spring Cloud. |
| --- | ---: |
| [tworzenie aplikacji w wiosennej chmurze az](#az-spring-cloud-app-deployment-create) | Utwórz wdrożenie przejściowe dla aplikacji. |
| [az wiosenne wdrażanie aplikacji w chmurze usunięte](#az-spring-cloud-app-deployment-delete) | Usuń wdrożenie aplikacji. |
| [lista wdrażania aplikacji w wiosennej chmurze az](#az-spring-cloud-app-deployment-list) | Wyświetl listę wszystkich wdrożeń w aplikacji. |
| [az wiosenno-chmurowy pokaz wdrażania aplikacji](#az-spring-cloud-app-deployment-show) | Pokaż szczegóły wdrożenia. |

| az wiosna-chmura config-serwer | Polecenia do zarządzania serwerem konfiguracyjnym w chmurze usługi Azure Spring Spring. |
| --- | ---: |
| [az wiosna-chmura config-serwer wyczyść](#az-spring-cloud-config-server-clear) | Wymaż wszystkie ustawienia na serwerze config. |
| [az spring-cloud config-server set az spring-cloud config-server set az spring-cloud config-server set az](#az-spring-cloud-config-server-set) | Zdefiniuj serwer konfiguracji z pliku YAML. |
| [az wiosna-chmura config-serwer pokaż](#az-spring-cloud-config-server-show) | Pokaż konfigurację serwera konfiguracji. |
| [az wiosna-chmura config serwer git zestaw](#az-spring-cloud-config-server-git-set) | Zdefiniuj właściwości git dla serwera config.  Poprzednie wartości zostaną zastąpione. |
| [az wiosna-chmura config serwer git repo dodać](#az-spring-cloud-config-server-git-repo-add) | Dodaj nową konfigurę repozytorium git do config servera. |
| [az wiosna-chmura config serwer git repo lista](#az-spring-cloud-config-server-git-repo-list) | Wymień wszystkie configy repozytorium git dla config servera. |
| [az wiosna-chmura config serwer git repo usunąć](#az-spring-cloud-config-server-git-repo-remove) | Usuń określone repozytorium git z config server. |

| az punkt końcowy testu sprężyny i chmury | Polecenia do zarządzania testowaniem punktów końcowych w usłudze Azure Spring Cloud |
| --- | ---: |
| [az punkt końcowy testu sprężyny-chmury wyłącza się](#az-spring-cloud-test-endpoint-disable) | Wyłącz punkt końcowy testu. |
| [az punkt końcowy testu sprężyny i chmury](#az-spring-cloud-test-endpoint-enable) | Włącz punkt końcowy testu. |
| [az lista punktów końcowych testu sprężyny i chmury](#az-spring-cloud-test-endpoint-list) | Lista kluczy punktu końcowego testu. |
| [az wiosna-chmura test-punkt końcowy klucz odnawiania](#az-spring-cloud-test-endpoint-renew-key) | Ponowne generowanie klucza punktu końcowego testu. |

## <a name="az-spring-cloud-create"></a>az wiosna-chmura tworzyć

Utwórz nową aplikację z domyślnym wdrożeniem w chmurze Azure Spring Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa dla tego wystąpienia usługi Azure Spring Cloud. |
| --resource-group -g | Określa grupę zasobów dla tej aplikacji.  Konfigurowanie grupy domyślnej przy użyciu`az configure --defaults group=<name>` |

| Parametry opcjonalne | |
| --- | ---: |
| --lokalizacja -l | Określa lokalizację serwera dla tej aplikacji.  Znajdowanie prawidłowych lokalizacji przy użyciu`az account list-locations` |
| --nie czekaj | Nie należy wykonywać długotrwałych operacji.

### <a name="examples"></a>Przykłady

Tworzenie nowej chmury Azure Spring Cloud w WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az sprężyna-chmura usunąć

Usuń wystąpienie usługi Azure Spring Cloud.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa wystąpienia usługi Azure Spring Cloud do usunięcia. |
| --resource-group -g | Nazwa grupy zasobów, do której należy usługa Azure Spring Cloud. |

| Parametry opcjonalne | |
| --- | ---: |
| -no-wait | Nie czekaj na zakończenie długotrwałych operacji. |

### <a name="example"></a>Przykład

Usuń wystąpienie usługi Azure Spring Cloud o nazwie "MyService" z "MyResourceGroup".

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az lista sprężynowa-chmurowa

Wyświetl listę wszystkich wystąpień usługi Azure Spring Cloud skojarzonych z daną grupą zasobów. Jeśli nie określono żadnej grupy zasobów, wyświetl identyfikatory subskrypcji.

```azurecli
az spring-cloud list --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --resource-group -g | Nazwa grupy zasobów. |

## <a name="az-spring-cloud-show"></a>az wiosenno-chmurowy pokaz

Pokaż szczegóły dla określonego wystąpienia usługi Azure Spring Cloud.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa wystąpienia usługi Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów, do której należy wystąpienie usługi Azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>az aplikacja wiosna-chmura utworzyć

Utwórz nową aplikację w chmurze Azure Spring Cloud.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --cpu | Liczba rdzeni wirtualnych na wystąpienie.  Domyślnie: 1. |
| --enable-persistent-storage | Wartość logiczna.  Jeśli true, instaluje dysk 50GB ze ścieżką domyślną. |
| --liczba wystąpień | Liczba wystąpień.  Domyślnie: 1. |
| --jest publiczny | Wartość logiczna.  Jeśli true, przypisuje domenę publiczną. |
| --memory | Liczba GB pamięci na wystąpienie.  Domyślnie: 1. |

### <a name="examples"></a>Przykłady

Utwórz aplikację z konfiguracją domyślną.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Utwórz publicznie dostępną aplikację z 3 wystąpieniami.  Każde wystąpienie ma 3 GB pamięci i 2 rdzenie procesora.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az aplikacja wiosna-chmura usunąć

Usuwa aplikację w chmurze Azure Spring Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>az sprężyna chmura aplikacja wdrożyć

Wdrażanie aplikacji w chmurze Azure Spring Cloud z kodu źródłowego lub wstępnie utworzonego pliku binarnego i aktualizowanie powiązanych konfiguracji.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --cpu | Liczba wirtualnych rdzeni CPI na wystąpienie. |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie wdrożenia produkcyjnego, jeśli nie określono. |
| --env | Zmienne środowiskowe oddzielone spacją w formacie "key[=value]". |
| --liczba wystąpień | Liczba wystąpień. |
| --jar-ścieżka | Jeśli pod warunkiem, wdrożyć jar z danej ścieżki. W przeciwnym razie wdrożyć bieżący folder jako tar. |
| --jvm-opcje | Ciąg zawierający opcje JVM.  Użyj '=' zamiast ' ' , aby uniknąć błędów analizy powłoki. `--jvm-options='-Xms1024m -Xmx2048m`Np. |
| --memory | Liczba GB pamięci na wystąpienie. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |
| --runtime-version | Wersja środowiska wykonawczego języka używanego w aplikacji.  Dozwolone wartości: `Java_11` `Java_8`, . |
| --target-module | Moduł podrzędny do wdrożenia.  Wymagane, gdy wiele pakietów jar są zbudowane z kodu źródłowego. |
| --wersja | Wersja wdrażania.  Bez zmian, jeśli nie jest ustawiona. |

### <a name="examples"></a>Przykłady

Wdrażanie kodu źródłowego w aplikacji. Spowoduje to spakowanie bieżącego katalogu, skompilowanie pliku binarnego przy użyciu usługi kompilacji kluczykowej, a następnie wdrożenie w aplikacji.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Wdrażanie wstępnie utworzonego słoika w aplikacji przy użyciu opcji JVM i zmiennych środowiskowych.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Wdrażanie kodu źródłowego do określonego wdrożenia aplikacji.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az wiosenna chmura lista aplikacji

Wyświetl listę wszystkich aplikacji w wystąpieniu usługi Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parametry wymagane | |
| --- | ---: |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ wiosenne ponowne uruchomienie aplikacji w chmurze

Uruchom ponownie wystąpienia aplikacji.  Domyślnie do wdrożenia produkcyjnego.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie wdrożenia produkcyjnego, jeśli nie określono. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |

## <a name="az-spring-cloud-app-scale"></a>skala aplikacji wiosna-chmura az

Ręcznie skaluj aplikację lub jej wdrożenia.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --cpu | Liczba wirtualnych rdzeni procesora CPU na wystąpienie aplikacji. |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie wdrożenia produkcyjnego, jeśli nie określono. |
| --liczba wystąpień | Liczba wystąpień tej aplikacji. |
| --memory | Liczba GB pamięci na wystąpienie aplikacji. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |

### <a name="examples"></a>Przykłady

Skalowanie aplikacji do 4 rdzeni procesora CPU i 8 GB pamięci na wystąpienie.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Skalowanie w poziomie wdrożenia aplikacji do 5 wystąpień.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az wiosenne wdrażanie aplikacji w chmurze

Ustaw opcje konfiguracji dla wdrożenia produkcyjnego aplikacji.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji. |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |

### <a name="examples"></a>Przykłady

Zamień przejściowe wdrożenie aplikacji na produkcyjne.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az wiosenno-chmurowe app show

Pokaż szczegóły aplikacji w chmurze Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az aplikacja wiosna-chmura show-deploy-log

Pokaż dziennik kompilacji ostatniego wdrożenia z kodu źródłowego.  Domyślnie środowisko produkcyjne.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie jest to środowisko produkcyjne. |

## <a name="az-spring-cloud-app-start"></a>AZ wiosna-chmura aplikacja start

Uruchamia wystąpienia aplikacji.  Domyślnie środowisko produkcyjne.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie jest to środowisko produkcyjne. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |

## <a name="az-spring-cloud-app-stop"></a>az wiosna-chmura app stop

Zatrzymaj wystąpienia aplikacji.  Domyślnie jest to środowisko produkcyjne.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie jest to środowisko produkcyjne. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |

## <a name="az-spring-cloud-app-update"></a>az wiosenna aktualizacja aplikacji w chmurze

Zaktualizuj zapisaną konfigurację aplikacji.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Parametry wymagane | |
| --- | ---: |
| --name -n | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie jest to środowisko produkcyjne. |
| --enable-persistent-storage | Boolean.  Jeśli true, zainstaluj dysk 50GB ze ścieżką domyślną. |
| --env | Zmienne środowiskowe oddzielone spacją w formacie "key[=value]". |
| --jest publiczny | Boolean.  Jeśli true, przypisz domenę publiczną do aplikacji. |
| --jvm-opcje | Ciąg zawierający opcje JVM.  Użyj '=' zamiast ' ' , aby uniknąć błędów analizy powłoki. `--jvm-options='-Xms1024m -Xmx2048m`Np. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |
| --runtime-version | Wersja środowiska wykonawczego języka używanego w aplikacji.  Dozwolone wartości: `Java_11` `Java_8`, . |

### <a name="example"></a>Przykład

Dodaj zmienną środowiskową dla aplikacji.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az wiosenna lista powiązań aplikacji w chmurze

Wyświetl listę wszystkich powiązań usługi w aplikacji.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>az sprężyna-chmura wiązanie usunąć

Usuń powiązanie usługi z aplikacji.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi do usunięcia. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>az wiosenno-chmurowy pokaz oprawy aplikacji

Pokaż szczegóły powiązania usługi.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az wiosna-chmura aplikacja powiązanie kosmos dodać

Powiąż usługę Azure Cosmos DB z aplikacją.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --api-type | Określ typ interfejsu API przy użyciu jednej z następujących wartości: cassandra, gremlin, mongo, sql, table. |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

|Parametry opcjonalne | |
| --- | ---: |
| --collection-name | Nazwa kolekcji.  Wymagane podczas korzystania z Gremlin. |
| --database-name | Nazwa bazy danych.  Wymagane podczas korzystania z Mongo, SQL i Gremlin. |
| --key-space | Cassandra klucz-przestrzeń.  Wymagane podczas korzystania z Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az wiosenna aktualizacja programu windows-cloud

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

|Parametry opcjonalne | |
| --- | ---: |
| --collection-name | Nazwa kolekcji.  Wymagane podczas korzystania z Gremlin. |
| --database-name | Nazwa bazy danych.  Wymagane podczas korzystania z Mongo, SQL i Gremlin. |
| --key-space | Cassandra klucz-przestrzeń.  Wymagane podczas korzystania z Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az wiosna-chmura aplikacja powiązanie mysql dodać

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --database-name | Nazwa bazy danych. |
| --klucz | klucz interfejsu API usługi. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --resource-id | Identyfikator zasobu platformy Azure usługi do powiązania z. |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |
| --username | Nazwa użytkownika dostępu do bazy danych. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az wiosna-chmura aplikacja powiązanie mysql aktualizacja

Zaktualizuj połączenie powiązania usługi aplikacji do bazy danych platformy Azure dla MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --database-name | Nazwa bazy danych. |
| --klucz | klucz interfejsu API usługi. |
| --username | Nazwa użytkownika dostępu do bazy danych. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az wiosna-chmura aplikacja powiązanie redis dodać

Powiąż pamięć podręczną platformy Azure dla redis z aplikacją.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --resource-id | Identyfikator zasobu platformy Azure usługi, z którą chcesz powiązać. |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --disable-ssl | Wyłącz TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az wiosenna aktualizacja wiązań aplikacji w chmurze

Zaktualizuj powiązanie usługi dla usługi Azure Cache for Redis.

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --disable-ssl | Wyłącz TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>tworzenie aplikacji w wiosennej chmurze az

Utwórz wdrożenie przejściowe dla aplikacji.

Aby wdrożyć kod lub zaktualizować ustawienia `az spring-cloud app deploy --deployment <staging-deployment>` do istniejącego wdrożenia, użyj lub <staging deployment>'az spring-cloud app update --deployment .

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa powiązania usługi. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --cpu | Liczba wirtualnych rdzeni procesora CPU na wystąpienie.  Domyślne: 1 |
| --env | Zmienne środowiskowe oddzielone spacją w formacie "key[=value]". |
| --liczba wystąpień | Liczba wystąpień. Domyślnie: 1. |
| --jar-ścieżka | Jeśli jest to przewidziane, wdrożyć jar.  W przeciwnym razie wdrożyć bieżący folder jako tar. |
| --jvm-opcje | Ciąg zawierający opcje JVM.  Użyj '=' zamiast ' ' , aby uniknąć błędów analizy powłoki. `--jvm-options='-Xms1024m -Xmx2048m`Np. |
| --memory | Liczba GB pamięci na wystąpienie. |
| --nie czekaj | Nie czekaj na zakończenie długotrwałych operacji. |
| --runtime-version | Wersja środowiska wykonawczego języka używanego w aplikacji.  Dozwolone wartości: `Java_11` `Java_8`, . |
| --skip-clone-settings | Utwórz wdrożenie przejściowe przez klonowanie bieżących ustawień wdrożenia produkcyjnego. |
| --target-module | Moduł podrzędny do wdrożenia.  Wymagane, gdy wiele pakietów jar są zbudowane z kodu źródłowego. |
| --wersja | Wersja wdrażania.  Bez zmian, jeśli nie jest ustawiona. |

### <a name="examples"></a>Przykłady

Wdrażanie kodu źródłowego w nowym wdrożeniu aplikacji.  Spowoduje to spakowanie bieżącego katalogu, zbudowanie pliku binarnego przy użyciu kluczowego systemu kompilacji, a następnie wdrożenie.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Wdrażanie wstępnie utworzonego słoika w aplikacji z opcjami JVM i zmiennymi środowiskowymi.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az wiosenne wdrażanie aplikacji w chmurze usunięte

Usuń wdrożenie aplikacji.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa wdrożenia. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>lista wdrażania aplikacji w wiosennej chmurze az

Wyświetl listę wszystkich wdrożeń w aplikacji.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>az wiosenno-chmurowy pokaz wdrażania aplikacji

Pokaż szczegóły wdrożenia.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --name | Nazwa wdrożenia. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --service -s | Nazwa chmury Azure Spring Cloud.  Usługę domyślną można skonfigurować za pomocą programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>az wiosna-chmura config-serwer wyczyść

Wymaż wszystkie ustawienia konfiguracji na serwerze config.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

## <a name="az-spring-cloud-config-server-set"></a>az spring-cloud config-server set az spring-cloud config-server set az spring-cloud config-server set az

Ustaw ustawienia konfiguracji na serwerze config przy użyciu pliku YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --config-file | Ścieżka pliku do manifestu YAML dla konfiguracji serwera konfiguracji. |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

| Parametry opcjonalne | |
| --- | ---: |
| --nie czekaj | Nie należy wykonywać długotrwałych operacji.

## <a name="az-spring-cloud-config-server-show"></a>az wiosna-chmura config-serwer pokaż

Pokaż ustawienia serwera konfiguracji.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

## <a name="az-spring-cloud-config-server-git-set"></a>az wiosna-chmura config-serwer git zestaw

Ustaw właściwości git dla serwera config.  Spowoduje to zastąpienie wszystkich istniejących właściwości git.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --uri | Identyfikator URI dodanego configu. |

| Parametry opcjonalne | |
| --- | ---: |
| --odroczyć | Tymczasowo przechowywać obiekt w lokalnej pamięci podręcznej zamiast wysyłania na platformę Azure.  Służy `az cache` do wyświetlania / wyczyść. |
| --host-key | Klucz hosta dla dodanej konfiguracji. |
| --host-key-algorithm | Algorytm klucza hosta dla dodanej konfiguracji. |
| --label | Etykieta dodanego configu. |
| --password | Hasło dodanego configu. |
| --klucz prywatny | Klucz prywatny dodanego configu. |
| --search-ścieżki | Szukaj ścieżek dodanego configu.  Użyj ograniczników przecinków dla wielu ścieżek. |
| --strict-host-key-checking | Umożliwia ścisłe sprawdzanie klucza hosta dodanego configu. |
| --username | Nazwa użytkownika dodanego configu. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az wiosna-chmura config-serwer git repo dodać

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --nazwa repozytorium | Identyfikator URI repozytorium. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --uri | Identyfikator URI dodanego configu. |

| Parametry opcjonalne | |
| --- | ---: |
| --odroczyć | Tymczasowo przechowywać obiekt w lokalnej pamięci podręcznej zamiast wysyłania na platformę Azure.  Służy `az cache` do wyświetlania / wyczyść. |
| --host-key | Klucz hosta dla dodanej konfiguracji. |
| --host-key-algorithm | Algorytm klucza hosta dla dodanej konfiguracji. |
| --label | Etykieta dodanego configu. |
| --password | Hasło dodanego configu. |
| --wzór | Wzorzec repozytorium.  Użyj ograniczników przecinków dla wielu ścieżek.|
| --klucz prywatny | Klucz prywatny dodanego configu. |
| --search-ścieżki | Szukaj ścieżek dodanego configu.  Użyj ograniczników przecinków dla wielu ścieżek. |
| --strict-host-key-checking | Umożliwia ścisłe sprawdzanie klucza hosta dodanego configu. |
| --username | Nazwa użytkownika dodanego configu. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az wiosna-chmura config-serwer git repo lista

Lista wszystkich repozytoriów git zdefiniowanych w config server

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

| Parametry opcjonalne | |
| --- | ---: |
| --odroczyć | Tymczasowo przechowywać obiekt w lokalnej pamięci podręcznej zamiast wysyłania na platformę Azure.  Służy `az cache` do wyświetlania / wyczyść. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az wiosna-chmura config-serwer git repo usunąć

Usuń istniejącą konfigurację repozytorium git z config server.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --nazwa repozytorium | Identyfikator URI repozytorium. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

| Parametry opcjonalne | |
| --- | ---: |
| --odroczyć | Tymczasowo przechowywać obiekt w lokalnej pamięci podręcznej zamiast wysyłania na platformę Azure.  Służy `az cache` do wyświetlania / wyczyść. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az punkt końcowy testu sprężyny-chmury wyłącza się

Wyłączanie punktu końcowego testu w chmurze usługi Azure Spring Cloud

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az punkt końcowy testu sprężyny i chmury

Włącz punkt końcowy testu dla usługi Azure Spring Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

## <a name="az-spring-cloud-test-endpoint-list"></a>az lista punktów końcowych testu sprężyny i chmury 

Wyświetl listę dostępnych kluczy punktu końcowego testu dla usługi Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |

| Parametry opcjonalne | |
| --- | ---: |
| --aplikacja | Nazwa aplikacji. |
| --wdrożenie -d | Nazwa istniejącego wdrożenia aplikacji.  Domyślnie jest to produkcja, jeśli nieokreślona. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az wiosna-chmura test-punkt końcowy klucz odnawiania

Ponowne generowanie klucza punktu końcowego testu dla usługi Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parametry wymagane | |
| --- | ---: |
| --name | Nazwa chmury Azure Spring Cloud. |
| --resource-group -g | Nazwa grupy zasobów.  Grupę domyślną można `az configure --defaults group=<name>`skonfigurować za pomocą programu . |
| --typ | Typ klucza punktu końcowego testu.  Dozwolone wartości: Podstawowy, Pomocniczy. |
