---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać typowe problemy podczas wdrażania, uruchamiania lub zarządzania wystąpieniami kontenerów platformy Azure
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533396"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Rozwiązywanie typowych problemów w usłudze Azure Container Instances

W tym artykule pokazano, jak rozwiązywać typowe problemy związane z zarządzaniem kontenerami lub wdrażaniem kontenerów w wystąpieniach kontenerów platformy Azure. Zobacz też [Często zadawane pytania](container-instances-faq.md).

Jeśli potrzebujesz dodatkowej pomocy technicznej, zobacz dostępne opcje **pomocy i pomocy technicznej** w [witrynie Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemy podczas wdrażania w grupie kontenerów
### <a name="naming-conventions"></a>Konwencje nazewnictwa

Podczas definiowania specyfikacji kontenera niektóre parametry wymagają przestrzegania ograniczeń nazewnictwa. Poniżej znajduje się tabela z określonymi wymaganiami dotyczącymi właściwości grupy kontenerów. Aby uzyskać więcej informacji na temat konwencji nazewnictwa platformy Azure, zobacz [konwencje nazewnictwa][azure-name-restrictions] w Centrum architektury platformy Azure.

| Zakres | Długość | Wielkość liter | Prawidłowe znaki | Sugerowany wzorzec | Przykład |
| --- | --- | --- | --- | --- | --- |
| Nazwa grupy kontenerów | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i myślnik w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nazwa kontenera | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i myślnik w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontenerowe | Między 1 a 65535 |Liczba całkowita |Całkowita od 1 do 65535 |`<port-number>` |`443` |
| Etykieta nazwy DNS | 5-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i myślnik w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>` |`frontend-site1` |
| Zmienna środowiskowa | 1-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i podkreślenia (_) w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>` |`MY_VARIABLE` |
| Nazwa woluminu | 5-63 |Bez uwzględniania wielkości liter |Małe litery i cyfry oraz łączniki w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku. Nie może zawierać dwóch kolejnych łączników. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>Wersja obrazu systemu operacyjnego nie jest obsługiwana

Jeśli określisz obraz, który nie obsługuje wystąpień `OsVersionNotSupported` kontenera platformy Azure, zwracany jest błąd. Błąd jest podobny do `{0}` następującego, gdzie jest nazwa obrazu, który próbowano wdrożyć:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ten błąd jest najczęściej spotykany podczas wdrażania obrazów systemu Windows, które są oparte na wersji kanału półrocznego 1709 lub 1803, które nie są obsługiwane. Aby uzyskać obsługiwane obrazy systemu Windows w wystąpieniach kontenerów platformy Azure, zobacz [Często zadawane pytania](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Nie można wyciągnąć obrazu

Jeśli wystąpienia kontenera platformy Azure początkowo nie można wyciągnąć obraz, ponawia próby na pewien czas. Jeśli operacja ściągania obrazu nadal zakończy się niepowodzeniem, `Failed to pull image` ACI ostatecznie nie powiedzie się wdrożenie i może pojawić się błąd.

Aby rozwiązać ten problem, usuń wystąpienie kontenera i ponów próbę wdrożenia. Upewnij się, że obraz istnieje w rejestrze i czy nazwa obrazu została wpisana poprawnie.

Jeśli nie można wyciągnąć obrazu, zdarzenia takie jak następujące są wyświetlane na wyjściu [az container show:][az-container-show]

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Błąd niedostępny zasób

Ze względu na różne obciążenie zasobów regionalnych na platformie Azure może pojawić się następujący błąd podczas próby wdrożenia wystąpienia kontenera:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, że z powodu dużego obciążenia w regionie, w którym próbujesz wdrożyć, zasoby określone dla kontenera nie mogą być przydzielane w tym czasie. Użyj co najmniej jednego z następujących kroków ograniczających zagrożenie, aby rozwiązać problem.

* Sprawdź, czy ustawienia wdrażania kontenera mieszczą się w parametrach zdefiniowanych w [obszarze Dostępność kontenerów dla wystąpień kontenerów platformy Azure](container-instances-region-availability.md)
* Określanie niższych ustawień procesora i pamięci kontenera
* Wdrażanie w innym regionie platformy Azure
* Wdrażanie w późniejszym czasie

## <a name="issues-during-container-group-runtime"></a>Problemy podczas wykonywania grupy kontenerów
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontener stale wychodzi i uruchamia ponownie (nie długotrwały proces)

Grupy kontenerów domyślnie [zasady ponownego uruchamiania](container-instances-restart-policy.md) **Zawsze**, więc kontenery w grupie kontenerów zawsze ponownie po uruchomieniu do zakończenia. Może być konieczne zmienić to **na OnFailure** lub **Nigdy,** jeśli zamierzasz uruchomić kontenery oparte na zadaniach. Jeśli określisz **OnFailure** i nadal widzisz ciągłe ponowne uruchamianie, może to być problem z aplikacją lub skryptem wykonywanym w kontenerze.

Podczas uruchamiania grup kontenerów bez długotrwałych procesów mogą pojawić się powtarzające się wyjścia i ponowne uruchomienie z obrazami, takimi jak Ubuntu lub Alpine. Połączenie za pośrednictwem [EXEC](container-instances-exec.md) nie będzie działać, ponieważ kontener nie ma procesu utrzymującego go przy życiu. Aby rozwiązać ten problem, należy dołączyć polecenie start, takie jak następujące z wdrożenia grupy kontenerów, aby zachować kontener uruchomiony.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Interfejs API wystąpień kontenerów `restartCount` i witryny Azure portal zawiera właściwość. Aby sprawdzić liczbę ponownych uruchomień kontenera, można użyć polecenia [az container show][az-container-show] w wierszu polecenia platformy Azure CLI. W poniższym przykładzie danych wyjściowych (który został obcięty dla zwięzłości), można zobaczyć `restartCount` właściwość na końcu danych wyjściowych.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Większość obrazów kontenerów dla dystrybucji systemu Linux ustawić powłoki, takich jak bash, jako polecenie domyślne. Ponieważ powłoka sama w sobie nie jest długotrwałą usługą, te kontenery natychmiast wyjdą i wpadną w pętlę ponownego uruchomienia po skonfigurowaniu z domyślną zasadą **Zawsze** restartuj.

### <a name="container-takes-a-long-time-to-start"></a>Uruchomienie kontenera zajmuje dużo czasu

Trzy podstawowe czynniki, które przyczyniają się do czasu uruchamiania kontenera w wystąpieniach kontenera platformy Azure są:

* [Rozmiar obrazu](#image-size)
* [Lokalizacja obrazu](#image-location)
* [Obrazy w pamięci podręcznej](#cached-images)

Obrazy systemu Windows mają [dodatkowe zagadnienia](#cached-images).

#### <a name="image-size"></a>Rozmiar obrazu

Jeśli kontener zajmuje dużo czasu, aby rozpocząć, ale ostatecznie powiedzie się, zacznij od patrząc na rozmiar obrazu kontenera. Ponieważ wystąpienia kontenera platformy Azure ściąga obraz kontenera na żądanie, czas uruchamiania widać jest bezpośrednio związane z jego rozmiarem.

Rozmiar obrazu kontenera można wyświetlić `docker images` za pomocą polecenia w wierszu polecenia platformy Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Kluczem do utrzymania małych rozmiarów obrazu jest zapewnienie, że ostateczny obraz nie zawiera niczego, co nie jest wymagane w czasie wykonywania. Jednym ze sposobów, aby to zrobić, jest [kompilacje wieloetapowe.][docker-multi-stage-builds] Kompilacje wieloetapowe ułatwiają upewnienie się, że ostateczny obraz zawiera tylko artefakty potrzebne dla aplikacji, a nie żadnej dodatkowej zawartości, która była wymagana w czasie kompilacji.

#### <a name="image-location"></a>Lokalizacja obrazu

Innym sposobem zmniejszenia wpływu ściągania obrazu na czas uruchamiania kontenera jest hostowania obrazu kontenera w [rejestrze kontenerów platformy Azure](/azure/container-registry/) w tym samym regionie, w którym zamierzasz wdrożyć wystąpienia kontenera. Skraca to ścieżkę sieciową, którą musi podróżować obraz kontenera, znacznie skracając czas pobierania.

#### <a name="cached-images"></a>Obrazy w pamięci podręcznej

Wystąpienia kontenera platformy Azure używają mechanizmu buforowania, aby przyspieszyć czas uruchamiania `nanoserver:1809`kontenera `servercore:1809`dla obrazów utworzonych na typowych [obrazach podstawowych systemu Windows,](container-instances-faq.md#what-windows-base-os-images-are-supported)w tym `servercore:ltsc2019`i . Powszechnie używane obrazy Linuksa, takie jak `ubuntu:1604` i `alpine:3.6` są również buforowane. Aby uzyskać aktualną listę buforowanych obrazów i tagów, użyj interfejsu API [Lista obrazów buforowanych.][list-cached-images]

> [!NOTE]
> Użycie obrazów opartych na systemie Windows Server 2019 w wystąpieniach kontenerów platformy Azure jest w wersji zapoznawczej.

#### <a name="windows-containers-slow-network-readiness"></a>Kontenery systemu Windows spowalniają gotowość sieci

Podczas początkowego tworzenia kontenery systemu Windows mogą nie mieć łączności przychodzącej lub wychodzącej przez maksymalnie 30 sekund (lub dłużej, w rzadkich przypadkach). Jeśli aplikacja kontenera wymaga połączenia z Internetem, dodaj opóźnienie i ponów logikę, aby umożliwić 30 sekund na nawiązanie połączenia z Internetem. Po wstępnej konfiguracji sieć kontenerów powinna zostać odpowiednio wznowiona.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nie można połączyć się z podstawowym interfejsem API platformy Docker ani uruchomić kontenerów uprzywilejowanych

Wystąpienia kontenera platformy Azure nie uwidacznia bezpośredniego dostępu do podstawowej infrastruktury, która obsługuje grupy kontenerów. Obejmuje to dostęp do interfejsu API platformy Docker uruchomionego na hoście kontenera i uruchomione kontenery uprzywilejowane. Jeśli potrzebujesz interakcji platformy Docker, sprawdź [dokumentację odwołania REST,](https://aka.ms/aci/rest) aby zobaczyć, co obsługuje interfejs API ACI. Jeśli czegoś brakuje, prześlij prośbę na [forach opinii ACI.](https://aka.ms/aci/feedback)

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Adres IP grupy kontenerów może być niedostępny z powodu niedopasowanych portów

Wystąpienia kontenera platformy Azure nie obsługuje jeszcze mapowania portów, jak w przypadku zwykłej konfiguracji platformy docker. Jeśli okaże się, że adres IP grupy kontenerów nie jest dostępny, gdy uważasz, że powinien być, upewnij się, `ports` że skonfigurowano obraz kontenera, aby nasłuchiwać tych samych portów, które udostępniasz w grupie kontenerów z właściwością.

Jeśli chcesz potwierdzić, że wystąpienia kontenera platformy Azure można nasłuchiwać na `aci-helloworld` porcie skonfigurowanym w obrazie kontenera, przetestuj wdrożenie obrazu, który udostępnia port. Uruchom również `aci-helloworld` aplikację, aby nasłuchiwała na porcie. `aci-helloworld`akceptuje opcjonalną zmienną `PORT` środowiskową, aby zastąpić domyślny port 80, na który nasłuchuje. Na przykład, aby przetestować port 9000, ustaw [zmienną środowiskową](container-instances-environment-variables.md) podczas tworzenia grupy kontenerów:

1. Skonfiguruj grupę kontenerów, aby udostępnić port 9000 i przekazać numer portu jako wartość zmiennej środowiskowej. Przykład jest sformatowany dla powłoki Bash. Jeśli wolisz inną powłokę, taką jak PowerShell lub wiersz polecenia, musisz odpowiednio dostosować przypisanie zmiennych.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Znajdź adres IP grupy kontenerów w `az container create`danych wyjściowych polecenia . Poszukaj wartości **ip**. 
1. Po pomyślnym zainicjowaniu obsługi administracyjnej kontenera przejdź do adresu IP i portu `192.0.2.0:9000`aplikacji kontenera w przeglądarce, na przykład: . 

    Powinien zostać wyświetlony "Witamy w instancjach kontenerów platformy Azure!" komunikat wyświetlany przez aplikację internetową.
1. Po zakończeniu pracy z kontenerem usuń `az container delete` go za pomocą polecenia:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [pobrać dzienniki kontenerów i zdarzenia,](container-instances-get-logs.md) aby pomóc debugować kontenery.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
