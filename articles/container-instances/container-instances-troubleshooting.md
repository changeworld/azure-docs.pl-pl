---
title: Rozwiązywanie problemów w usłudze Azure Container Instances
description: Dowiedz się, jak rozwiązywać problemy związane z usługą Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9dc3e19f9429a6055a799f3f013c732538fa370d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070864"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Rozwiązywanie typowych problemów w usłudze Azure Container Instances

W tym artykule pokazano, jak rozwiązywać typowe problemy dotyczące zarządzania lub wdrażanie kontenerów w usłudze Azure Container Instances. Zobacz też [— często zadawane pytania](container-instances-faq.md).

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Podczas definiowania specyfikacji usługi kontenera, niektóre parametry wymagają gotowość do ograniczenia nazewnictwa. Poniżej przedstawiono tabelę z określonych wymagań dotyczących kontenera właściwości grupy. Aby uzyskać więcej informacji na temat konwencji nazewnictwa platformy Azure, zobacz [konwencje nazewnictwa] [ azure-name-restrictions] w Centrum architektury platformy Azure.

| Zakres | Długość | Wielkość liter | Prawidłowe znaki | Sugerowany wzorzec | Przykład |
| --- | --- | --- | --- | --- | --- |
| Nazwa grupy kontenerów | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nazwa kontenera | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontenera | Od 1 do 65535 |Liczba całkowita |Liczba całkowita od 1 do 65535 |`<port-number>` |`443` |
| Etykieta nazwy DNS | 5-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>` |`frontend-site1` |
| Zmienna środowiskowa | 1-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i podkreślenia (_) w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>` |`MY_VARIABLE` |
| Nazwa woluminu | 5-63 |Bez uwzględniania wielkości liter |Małe litery i cyfry i łączniki w dowolnym miejscu poza pierwszym ani ostatnim znakiem. Nie może zawierać dwóch łączników pod rząd. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Wersja systemu operacyjnego obrazu nie jest obsługiwane

Jeśli określisz obraz, który nie obsługuje usługi Azure Container Instances, `OsVersionNotSupported` zostanie zwrócony błąd. Błąd jest podobne do następujących, gdzie `{0}` nazywa się obraz próbujesz wdrożyć:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ten błąd występuje najczęściej w przypadku wdrażania obrazów Windows, które są oparte na półroczny kanał wersji 1709 lub 1803, które nie są obsługiwane. Dla obsługiwanych obrazów Windows w usłudze Azure Container Instances, zobacz [— często zadawane pytania](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="unable-to-pull-image"></a>Nie można do ściągania obrazu

W przypadku początkowo nie można ściągnąć obrazu usługi Azure Container Instances ponawia próbę w okresie czasu. Jeśli operacja ściągnięcia obrazu zakończy się niepowodzeniem, ACI ostatecznie zakończy się niepomyślnie wdrożenia i może zostać wyświetlony `Failed to pull image` błędu.

Aby rozwiązać ten problem, usuń wystąpienia kontenera i ponów próbę wdrożenia. Upewnij się, że obraz, który istnieje w rejestrze i że nazwa obrazu została wpisana poprawnie.

Jeśli nie można ściągnąć obrazu, zdarzenia, podobnie do poniższego są wyświetlane w danych wyjściowych [az container show][az-container-show]:

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

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontener stale kończy działanie i uruchamia ponownie (żaden proces długotrwałych)

Domyślnie grupy kontenerów [zasady ponownego uruchamiania](container-instances-restart-policy.md) z **zawsze**, więc kontenerów w grupie kontenerów zawsze po ponowne uruchomienie zostało ukończone. Konieczne może być to można zmienić **OnFailure** lub **nigdy** Jeśli zamierzasz uruchamiać kontenery opartego na zadaniach. Jeśli określisz **OnFailure** i nadal znaleźć ciągłe ponowne uruchomienie, może to być problem z aplikacją lub skrypt wykonywany w kontenerze.

Podczas uruchamiania grupy kontenerów bez długotrwałe procesy mogą pojawić się powtarzanych umożliwia zamknięcie i ponowne uruchomienie komputera przy użyciu obrazów, takie jak Ubuntu lub Alpine. Łączenie za pośrednictwem [EXEC](container-instances-exec.md) nie będzie działać w kontenerze ma żaden proces, utrzymywanie jej aktywności. Aby rozwiązać ten problem, obejmują uruchamiania polecenia podobnego do poniższego, przy użyciu wdrożenia grupy kontenerów, który zapewnienie działanie kontenera.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Interfejs API wystąpień kontenera i Azure portal zawiera `restartCount` właściwości. Aby sprawdzić liczbę ponownych uruchomień dla kontenera, można użyć [az container show] [ az-container-show] polecenia w interfejsie wiersza polecenia platformy Azure. W poniższym przykładzie danych wyjściowych (który został obcięty dla zwięzłości), możesz zobaczyć `restartCount` właściwości na końcu danych wyjściowych.

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
> Większość obrazów kontenerów dystrybucje systemu Linux ustawić powłoki, takie jak bash, jako domyślnego polecenia. Ponieważ powłoki samodzielnie nie jest usługą długotrwałych, te kontenery natychmiast zakończyć i wpadają w pętli ponownego uruchamiania w przypadku skonfigurowania przy użyciu domyślnego **zawsze** zasady ponownego uruchamiania.

## <a name="container-takes-a-long-time-to-start"></a>Kontener zajmuje dużo czasu do uruchomienia

Są dwa podstawowe czynniki, które przyczyniają się do czasu uruchamiania kontenera w usłudze Azure Container Instances:

* [Rozmiar obrazu](#image-size)
* [Lokalizacja obrazu](#image-location)

Windows obrazy mają [dodatkowych kwestii dotyczących](#cached-images).

### <a name="image-size"></a>Rozmiar obrazu

Jeśli kontener zajmuje dużo czasu, aby rozpocząć, ale ostatecznie zakończy się powodzeniem, zacznij od przejrzenia rozmiar obrazu kontenera. Ponieważ usługi Azure Container Instances ściąga obraz kontenera na żądanie, czas uruchamiania, który zostanie wyświetlony jest bezpośrednio związane z jego rozmiaru.

Rozmiar obrazu kontenera można wyświetlić za pomocą `docker images` polecenia w interfejsie wiersza polecenia platformy Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Klucz zachowanie małe rozmiary obrazów jest zapewnienie, że końcowy obraz nie zawiera wszystko, co nie jest wymagane w czasie wykonywania. Jednym ze sposobów, aby zrobić to za pomocą [kompilacje wieloetapowych][docker-multi-stage-builds]. Wieloetapowe opiera się upewnij, które ułatwiają upewnić się, że finalnego obrazu zawiera artefakty potrzebne dla aplikacji, a nie jakiekolwiek nadmiarowe zawartością, która była wymagana w czasie kompilacji.

### <a name="image-location"></a>Lokalizacja obrazu

Innym sposobem, aby zmniejszyć wpływ ściągania obrazów na czas uruchamiania przez kontener jest hostowanie obrazu kontenera w [usługi Azure Container Registry](/azure/container-registry/) w tym samym regionie, w którym mają zostać wdrożone wystąpienia kontenera. Skraca ścieżkę sieciową wymagającym podróż, obraz kontenera, znacznie skrócić czas pobierania.

### <a name="cached-images"></a>Obrazy w pamięci podręcznej

Usługa Azure Container Instances używa mechanizm buforowania, aby ułatwić szybkość czas uruchamiania kontenera obrazów utworzonych na wspólnym [Windows podstawowa obrazów](container-instances-faq.md#what-windows-base-os-images-are-supported), w tym `nanoserver:1809`, `servercore:ltsc2019`, i `servercore:1809`. Najczęściej używane obrazów systemu Linux, takie jak `ubuntu:1604` i `alpine:3.6` są także buforowane. Aktualną listę pamięci podręcznej obrazów i tagów, można użyć [listy pamięci podręcznej obrazów] [ list-cached-images] interfejsu API.

> [!NOTE]
> Korzystanie z obrazów w sieci systemu Windows Server w 2019 r w usłudze Azure Container Instances jest dostępna w wersji zapoznawczej.

### <a name="windows-containers-slow-network-readiness"></a>Gotowość wolną sieć kontenery Windows

Po utworzeniu początkowego kontenery Windows może być brak łączności dla ruchu przychodzącego lub wychodzącego przez maksymalnie 30 sekund (lub dłużej, w rzadkich przypadkach). Jeśli aplikacja kontenera wymaga połączenia internetowego, dodawać opóźnienie i ponów próbę wykonania logiki, aby zezwolić na 30 sekund, aby nawiązać połączenie z Internetem. Po przeprowadzeniu instalacji początkowej kontenerowa powinna zostać wznowiona odpowiednio.

## <a name="resource-not-available-error"></a>Zasób błąd nie jest dostępna

Z powodu różnych regionalnych zasobów obciążenia na platformie Azure, możesz otrzymać następujący błąd podczas próby wdrożenia wystąpienia kontenera:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, że z powodu dużego obciążenia w regionie, w którym próbujesz wdrożyć, nie można przydzielić zasoby określone dla kontenera usługi w tym czasie. Aby pomóc rozwiązać problem, należy użyć co najmniej jeden z następujących czynności zaradcze.

* Sprawdź ustawienia wdrożenia kontenera mieszczą się w parametrów zdefiniowanych w [dostępność regionów dla usługi Azure Container Instances](container-instances-region-availability.md)
* Określ niższe ustawienia Procesora i pamięci dla kontenera
* Wdrażanie w innym regionie platformy Azure
* Wdrażanie w późniejszym czasie

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nie można nawiązać połączenie z podstawowego interfejsu API Docker lub uruchamiaj kontenery uprzywilejowanych

Usługa Azure Container Instances nie ujawnia bezpośredni dostęp do podstawowej infrastruktury, który jest hostem grupy kontenerów. Obejmuje to dostęp do interfejsu API platformy Docker zainstalowany na hoście kontenera, a uprzywilejowanych kontenerów. Jeśli potrzebujesz interakcji platformy Docker, sprawdź [Dokumentacja referencyjna REST](https://aka.ms/aci/rest) obsługuje interfejs API ACI. Jeśli jest coś, co jest Brak, Prześlij żądanie na [fora z opiniami ACI](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Adresy IP mogą być niedostępne z powodu niezgodnej portów

Usługa Azure Container Instances nie obsługuje obecnie portu mapowania takich jak za pomocą konfiguracji regularnych platformy docker, jednak ta poprawka znajduje się w planie. Jeśli okaże się adresy IP nie są dostępne, gdy uznasz, że powinno być, upewnij się, został skonfigurowany obraz kontenera do nasłuchiwania tych samych portów, należy udostępnić w grupie kontenerów za pomocą `ports` właściwości.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [pobrać dzienniki kontenera i zdarzenia](container-instances-get-logs.md) debugować swoje kontenery.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
