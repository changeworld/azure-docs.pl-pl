---
title: Azure Container Instances rozwiązywania problemów
description: Dowiedz się, jak rozwiązywać problemy z Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 4b41a3862341ef39c1288985d86d86667fbc5866
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325600"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Rozwiązywanie typowych problemów w Azure Container Instances

W tym artykule pokazano, jak rozwiązywać typowe problemy związane z zarządzaniem kontenerami i ich wdrażaniem Azure Container Instances. Zobacz również [często zadawane pytania](container-instances-faq.md).

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Podczas definiowania specyfikacji kontenera niektóre parametry wymagają przestrzegania ograniczeń nazewnictwa. Poniżej znajduje się tabela z określonymi wymaganiami dotyczącymi właściwości grupy kontenerów. Aby uzyskać więcej informacji na temat konwencji nazewnictwa [][azure-name-restrictions] platformy Azure, zobacz Konwencje nazewnictwa w centrum architektury platformy Azure.

| Scope | Długość | Wielkość liter | Prawidłowe znaki | Sugerowany wzorzec | Przykład |
| --- | --- | --- | --- | --- | --- |
| Nazwa grupy kontenerów | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik wszędzie z wyjątkiem pierwszego lub ostatniego znaku |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nazwa kontenera | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik wszędzie z wyjątkiem pierwszego lub ostatniego znaku |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontenerów | Od 1 do 65535 |Liczba całkowita |Liczba całkowita z zakresu od 1 do 65535 |`<port-number>` |`443` |
| Etykieta nazwy DNS | 5-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik wszędzie z wyjątkiem pierwszego lub ostatniego znaku |`<name>` |`frontend-site1` |
| Zmienna środowiskowa | 1-63 |Bez uwzględniania wielkości liter |Znaki alfanumeryczne i znaki podkreślenia (_) wszędzie z wyjątkiem pierwszego lub ostatniego znaku |`<name>` |`MY_VARIABLE` |
| Nazwa woluminu | 5-63 |Bez uwzględniania wielkości liter |Małe litery i cyfry oraz łączniki wszędzie z wyjątkiem pierwszego lub ostatniego znaku. Nie może zawierać dwóch kolejnych łączników. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Wersja systemu operacyjnego obrazu nie jest obsługiwana

Jeśli określisz obraz, który Azure Container Instances nie obsługuje, `OsVersionNotSupported` zwracany jest błąd. Ten błąd jest podobny do poniższego, `{0}` gdzie jest nazwą obrazu, który próbujesz wdrożyć:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ten błąd jest najczęściej spotykany podczas wdrażania obrazów systemu Windows opartych na półrocze w wersji 1709 lub 1803, które nie są obsługiwane. Aby uzyskać obsługiwane obrazy systemu Windows w Azure Container Instances, zobacz [często zadawane pytania](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="unable-to-pull-image"></a>Nie można ściągnąć obrazu

Jeśli Azure Container Instances początkowo nie można ściągnąć obrazu, ponawia próbę przez pewien czas. Jeśli operacja ściągania obrazu nie powiedzie się, ACI ostatecznie kończy pracę wdrożenia i może zostać wyświetlony `Failed to pull image` błąd.

Aby rozwiązać ten problem, Usuń wystąpienie kontenera i spróbuj ponownie wykonać wdrożenie. Upewnij się, że obraz istnieje w rejestrze i że wpisano poprawną nazwę obrazu.

Jeśli nie można ściągnąć obrazu, zdarzenia takie jak następujące są wyświetlane w danych wyjściowych polecenia [AZ Container show][az-container-show]:

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

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontener ciągle opuszcza i uruchamia ponownie (bez długotrwałego procesu)

Grupy kontenerów domyślnie mają [zasady ponownego uruchamiania](container-instances-restart-policy.md) **zawsze**, dlatego kontenery w grupie kontenerów zawsze są ponownie uruchamiane po ich zakończeniu. Może być konieczne jej zmianę na **OnFailure** lub **nigdy** , jeśli zamierzasz uruchomić kontenery oparte na zadaniach. Jeśli określisz **** wartość OnFailure i nadal widzisz ciągłe ponowne uruchomienia, może wystąpić problem z aplikacją lub skryptem wykonywanym w kontenerze.

W przypadku uruchamiania grup kontenerów bez długotrwałych procesów mogą pojawić się powtórzone wyjścia i ponowne uruchomienia z obrazami, takimi jak Ubuntu lub Alpine. Łączenie za pośrednictwem programu [exec](container-instances-exec.md) nie będzie działało, ponieważ kontener nie ma żadnego procesu utrzymywania aktywności. Aby rozwiązać ten problem, należy dodać polecenie uruchomienia podobne do następującego w przypadku wdrożenia grupy kontenerów w celu zachowania uruchomionego kontenera.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Interfejs API Container Instances i Azure Portal zawiera `restartCount` właściwość. Aby sprawdzić liczbę ponownych uruchomień dla kontenera, w interfejsie wiersza polecenia platformy Azure można użyć polecenie [AZ Container show][az-container-show] . W poniższych przykładowych danych wyjściowych (które zostały obcięte dla zwięzłości) można zobaczyć `restartCount` właściwość na końcu danych wyjściowych.

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
> Większość obrazów kontenerów dla dystrybucji systemu Linux ustawia powłokę, taką jak bash, jako polecenie domyślne. Ponieważ sama powłoka nie jest długotrwałą usługą, te kontenery są natychmiast zamykane i przepadają w pętli ponownego uruchomienia po skonfigurowaniu domyślnych zasad **zawsze** uruchamiaj ponownie.

## <a name="container-takes-a-long-time-to-start"></a>Rozpoczęcie pracy kontenera trwa długo

Dwa podstawowe czynniki, które przyczyniają się do czasu uruchamiania kontenera w Azure Container Instances są następujące:

* [Rozmiar obrazu](#image-size)
* [Lokalizacja obrazu](#image-location)

Obrazy systemu Windows mają [dodatkowe uwagi](#cached-images).

### <a name="image-size"></a>Rozmiar obrazu

Jeśli uruchamianie kontenera trwa zbyt długo, ale ostatecznie zakończy się powodzeniem, Zacznij od przejrzenia rozmiaru obrazu kontenera. Ponieważ Azure Container Instances pobiera obraz kontenera na żądanie, wyświetlany czas uruchamiania jest bezpośrednio związany z jego rozmiarem.

Rozmiar obrazu kontenera można wyświetlić, korzystając `docker images` z polecenia w interfejsie CLI platformy Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Klucz służący do zachowywania rozmiaru obrazów jest zapewniany, że końcowy obraz nie zawiera żadnych elementów, które nie są wymagane w czasie wykonywania. Jednym ze sposobów wykonania tej czynności są [kompilacje][docker-multi-stage-builds]wieloetapowe. Kompilacje wieloetapowe ułatwiają zapewnienie, że obraz końcowy zawiera tylko te artefakty, które są potrzebne dla aplikacji, a nie do żadnej dodatkowej zawartości, która była wymagana w czasie kompilacji.

### <a name="image-location"></a>Lokalizacja obrazu

Innym sposobem zmniejszenia wpływu pobierania obrazu na czas uruchamiania kontenera jest hostowanie obrazu kontenera w [Azure Container Registry](/azure/container-registry/) w tym samym regionie, w którym zamierzasz wdrożyć wystąpienia kontenerów. Skraca to ścieżkę sieciową wymaganą przez obraz kontenera, co znacznie skraca czas pobierania.

### <a name="cached-images"></a>Obrazy w pamięci podręcznej

Azure Container Instances używa mechanizmu buforowania w celu skrócenia czasu uruchamiania kontenera dla obrazów opartych na typowych [obrazach podstawowych systemu Windows](container-instances-faq.md#what-windows-base-os-images-are-supported), w `servercore:1809`tym `nanoserver:1809`, `servercore:ltsc2019`i. Często używane obrazy systemu Linux, `ubuntu:1604` takie `alpine:3.6` jak i, są również buforowane. Aby uzyskać aktualną listę buforowanych obrazów i tagów, użyj interfejsu API [listy buforowanych obrazów][list-cached-images] .

> [!NOTE]
> Korzystanie z obrazów opartych na systemie Windows Server 2019 w Azure Container Instances jest w wersji zapoznawczej.

### <a name="windows-containers-slow-network-readiness"></a>Niewolne gotowość sieci w kontenerach systemu Windows

Podczas tworzenia początkowego kontenery systemu Windows mogą nie mieć łączności przychodzącej ani wychodzącej przez maksymalnie 30 sekund (lub dłużej w rzadkich przypadkach). Jeśli aplikacja kontenera wymaga połączenia z Internetem, Dodaj opóźnienie i logikę ponowień, aby zezwolić na 30 sekund na nawiązanie połączenia z Internetem. Po początkowej instalacji należy odpowiednio wznowić działanie sieci kontenera.

## <a name="resource-not-available-error"></a>Błąd niedostępnego zasobu

Ze względu na zróżnicowane obciążenie zasobów regionalnych na platformie Azure podczas próby wdrożenia wystąpienia kontenera może zostać wyświetlony następujący błąd:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, że ze względu na duże obciążenie w regionie, w którym próbujesz wdrożyć, zasoby określone dla danego kontenera nie mogą być przyłączone w tym czasie. Aby rozwiązać problem, należy użyć co najmniej jednego z następujących kroków zaradczych.

* Sprawdź, czy ustawienia wdrożenia kontenera mieszczą się w parametrach zdefiniowanych w [obszarze dostępność w regionie dla Azure Container Instances](container-instances-region-availability.md)
* Określ mniejsze ustawienia procesora i pamięci dla kontenera
* Wdrażanie w innym regionie platformy Azure
* Wdróż w późniejszym czasie

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nie można nawiązać połączenia z bazowym interfejsem API platformy Docker lub uruchamiać kontenery uprzywilejowane

Azure Container Instances nie ujawnia bezpośredniego dostępu do podstawowej infrastruktury, która hostuje grupy kontenerów. Obejmuje to dostęp do interfejsu API platformy Docker uruchomionego na hoście kontenera i uruchomionych kontenerów uprzywilejowanych. Jeśli wymagasz interakcji z platformą Docker, zapoznaj się z [dokumentacją usługi REST](https://aka.ms/aci/rest) , aby zobaczyć, co obsługuje interfejs API usługi ACI. Jeśli brakuje czegoś, Prześlij żądanie na [forach opinii ACI](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Adresy IP mogą nie być dostępne ze względu na niezgodne porty

Azure Container Instances obecnie nie obsługuje mapowania portów, takiego jak zwykła konfiguracja platformy Docker, jednak ta poprawka jest zawarta w planie. Jeśli okaże się, że adresy IP nie są dostępne, jeśli uważasz, że jest to konieczne, upewnij się, że skonfigurowano obraz kontenera do nasłuchiwania na tych samych portach, które zostały ujawnione w grupie kontenerów z `ports` właściwością.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [pobrać dzienniki kontenerów i zdarzenia](container-instances-get-logs.md) w celu ułatwienia debugowania kontenerów.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
