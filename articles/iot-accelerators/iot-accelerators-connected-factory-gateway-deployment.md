---
title: Wdrażanie bramy połączone fabryki - Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć bramę Windows lub Linux, aby umożliwić łączność z fabryki połączone akcelerator rozwiązań.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: c2805ddf7627ad520f6cc6585baedc7f5194aad6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626908"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Wdrażanie brama brzegowa dla fabryki połączone akcelerator rozwiązań w systemie Windows lub Linux

Należy dwa składniki oprogramowania, aby wdrożyć bramę brzegową dla *połączone fabryki* akcelerator rozwiązań:

- *OPC Proxy* nawiąże połączenie z fabryki połączony. Serwer Proxy OPC następnie czeka na polecenia i kontroli wiadomości z zintegrowane przeglądarki OPC, która jest uruchamiana w portalu rozwiązania połączone fabryki.

- *Wydawcy OPC* łączy się z istniejącymi lokalnymi serwerami OPC UA i przekazuje dane telemetryczne wiadomości z nich do fabryki połączony. Możesz połączyć urządzenie klasyczne OPC przy użyciu [OPC klasycznego karta OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Oba te składniki są open source i są dostępne jako źródło w serwisie GitHub i jak kontenery Docker na DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Nie trzeba publicznych adresów IP lub otwartych przychodzących portów w zaporze bramy albo składnika. Składniki serwera Proxy OPC i wydawcy OPC należy używać tylko port wychodzący 443.

Kroki opisane w tym artykule opisano sposób wdrażania bramy krawędzi, przy użyciu rozwiązania Docker w systemie Windows lub Linux. Brama umożliwia łączność akcelerator rozwiązań połączone fabryki. Umożliwia także składniki bez połączenia fabryki.

> [!NOTE]
> Oba te składniki mogą być używane jako modułów w [Azure IoT krawędzi](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Wybierz urządzenie bramy

Jeśli nie masz jeszcze urządzenie bramy, firma Microsoft zaleca się, że kupić komercyjnych bramy z jednego z ich partnerów. Listę bramy urządzeń zgodnych z rozwiązania fabryki połączone, odwiedź stronę [katalogu urządzenia Azure IoT](https://catalog.azureiotsuite.com/?q=opc). Postępuj zgodnie z instrukcjami, które pochodzą z urządzeniem, aby skonfigurować bramę.

Alternatywnie użyj poniższych instrukcji, aby ręcznie skonfigurować istniejące urządzenie bramy.

## <a name="install-and-configure-docker"></a>Instalowanie i konfigurowanie Docker

Zainstaluj [Docker dla systemu Windows](https://www.docker.com/docker-windows) na urządzeniu bramy systemu Windows lub użyj Menedżera pakietów, aby zainstalować docker na urządzenia bramy oparte na systemie Linux.

Podczas instalacji Docker dla systemu Windows należy wybrać dysk na komputerze hosta, aby udostępnić Docker. Poniższy zrzut ekranu przedstawia udostępnianie **D** dysku w systemie Windows, aby zezwolić na dostęp do dysku hosta z wewnątrz kontenera docker:

![Zainstaluj Docker dla systemu Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Ten krok można również wykonać po zainstalowaniu docker z **ustawienia** okna dialogowego. Kliknij prawym przyciskiem myszy **Docker** ikonę na pasku zadań systemu Windows i wybierz polecenie **ustawienia**. Jeśli wprowadzono znaczące aktualizacje systemu Windows zostały wdrożone w systemie, takie jak Windows twórców spadku zaktualizować, wyłącz udostępnianie dysków i udostępnij je ponownie, aby odświeżyć prawa dostępu.

Jeśli używasz systemu Linux, aby umożliwić dostęp do systemu plików jest wymagana żadna konfiguracja dodatkowych.

W systemie Windows utwórz folder na dysku, który został udostępniony Docker, w systemie Linux należy utworzyć folder w obszarze katalogu głównego systemu plików. Ten przewodnik odwołuje się do tego folderu jako `<SharedFolder>`.

Odwołań do `<SharedFolder>` w poleceniu Docker, należy użyć prawidłowa składnia dla systemu operacyjnego. Poniżej przedstawiono dwa przykłady, jeden dla systemu Windows i jedno dla systemu Linux:

- Jeśli jesteś za pomocą folderu `D:\shared` w systemie Windows jako sieci `<SharedFolder>`, przedstawiono składnię polecenia Docker `d:/shared`.

- Jeśli jesteś za pomocą folderu `/shared` w systemie Linux jako sieci `<SharedFolder>`, przedstawiono składnię polecenia Docker `/shared`.

Aby uzyskać więcej informacji, zobacz [korzysta z woluminów](https://docs.docker.com/engine/admin/volumes/volumes/) odwołanie do aparatu docker.

## <a name="configure-the-opc-components"></a>Konfigurowanie składników OPC

Przed zainstalowaniem składników OPC, wykonaj następujące kroki, aby przygotować swoje środowisko:

1. Aby zakończyć wdrożenia bramy, należy **iothubowner** parametry połączenia Centrum IoT w danym wdrożeniu fabryka połączenia. W [portalu Azure](http://portal.azure.com/), przejdź do Centrum IoT w grupie zasobów utworzone podczas wdrażania rozwiązania fabryka połączenia. Kliknij przycisk **zasady dostępu współużytkowanego** dostępu **iothubowner** ciąg połączenia:

    ![Znajdź parametry połączenia Centrum IoT](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Kopiuj **klucz podstawowy ciąg połączenia** wartość.

1. Aby umożliwić komunikację między docker kontenerów, należy sieci Mostek zdefiniowane przez użytkownika. Aby utworzyć sieć Mostek kontenerów, uruchom następujące polecenia w wierszu polecenia:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Aby sprawdzić **iot_edge** mostka sieciowego został utworzony, uruchom następujące polecenie:

    ```cmd/sh
    docker network ls
    ```

    Twoje **iot_edge** mostka sieciowego znajduje się na liście sieci.

Aby uruchomić narzędzie wydawca OPC, uruchom następujące polecenie w wierszu polecenia:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [GitHub wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) i [docker Uruchom odwołanie](https://docs.docker.com/engine/reference/run/) udostępniają więcej informacji na temat:

  - Opcje wiersza polecenia docker określone przed nazwa kontenera (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - Znaczenie wydawcy OPC parametru wiersza polecenia po nazwie kontenera (`microsoft/iot-edge-opc-publisher:2.1.3`).

- `<IoTHubOwnerConnectionString>` Jest **iothubowner** udostępnione parametry połączenia zasady dostępu w portalu Azure. Ten ciąg połączenia został skopiowany w poprzednim kroku. Wystarczy tylko te parametry połączenia dla przy pierwszym uruchomieniu wydawcy OPC. W kolejnych uruchomieniach należy ją pominąć ponieważ stanowi zagrożenie bezpieczeństwa.

- `<SharedFolder>` Używasz i jego składni jest opisany w sekcji [zainstalować i skonfigurować Docker](#install-and-configure-docker). OPC Wydawca używa `<SharedFolder>` do odczytu i zapisu do pliku konfiguracji OPC wydawcy, zapisywania do pliku dziennika i Udostępnij oba te pliki poza kontenera.

- Wydawca OPC odczytuje konfigurację z **publishednodes.json** pliku, który jest odczytywane i zapisywane w `<SharedFolder>/docker` folderu. Ten plik konfiguracyjny definiuje OPC UA dane węzła na danym serwerze OPC UA, który subskrybować OPC wydawcy. Pełna składnia **publishednodes.json** pliku jest opisany w [wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) strony w witrynie GitHub. Po dodaniu bramy, umieść pustą **publishednodes.json** w folderze:

    ```json
    [
    ]
    ```

- Zawsze, gdy serwer OPC UA powiadamia wydawcy OPC zmian danych, nowa wartość jest wysyłane do Centrum IoT. W zależności od ustawienia przetwarzanie wsadowe najpierw wydawcy OPC gromadzone dane przed wysłaniem danych do Centrum IoT w partii.

- Docker nie obsługuje rozpoznawania nazw systemu NetBIOS, rozpoznawania nazw DNS tylko. Jeśli nie masz serwera DNS w sieci, można użyć obejście pokazano w poprzednim przykładzie wiersza polecenia. W poprzednim przykładzie wierszu polecenia użyto `--add-host` parametr, aby dodać wpis do pliku hosts kontenerów. Ten wpis umożliwia wyszukiwanie nazwy hosta dla danej `<OpcServerHostname>`, rozpoznawanie do danego adresu IP `<IpAddressOfOpcServerHostname>`.

- OPC UA używa certyfikatów X.509 do uwierzytelniania i szyfrowania. Trzeba umieścić certyfikat wydawcy OPC na serwerze OPC UA, z którym nawiązujesz połączenie, aby upewnić się, że subskrypcja ufa wydawcy OPC. Magazyn certyfikatów wydawcy OPC znajduje się w `<SharedFolder>/CertificateStores` folderu. Można znaleźć certyfikatu wydawcy OPC w `trusted/certs` folderu w `CertificateStores` folderu.

  Kroki, aby skonfigurować serwer OPC UA zależą od urządzenia, którego używasz. te kroki zwykle są udokumentowane w podręczniku użytkownika serwera OPC UA.

Aby zainstalować serwer Proxy OPC, uruchom następujące polecenie w wierszu polecenia:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Wystarczy raz uruchomić instalację w systemie.

Aby uruchomić OPC Proxy, użyj następującego polecenia:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

Serwer Proxy OPC zapisuje ciągu połączenia podczas instalacji. W kolejnych uruchomieniach można pominąć parametry połączenia, ponieważ stanowi zagrożenie bezpieczeństwa.

## <a name="enable-your-gateway"></a>Włącz funkcję bramy

Wykonaj poniższe kroki, aby włączyć bramy w połączony fabryki akcelerator rozwiązań:

1. Gdy oba te składniki są uruchomione, przejdź do **połączyć własny serwer UA OPC** w portalu rozwiązania połączone fabryki. Ta strona jest dostępna tylko dla administratorów w rozwiązaniu. Wprowadź adres URL punktu końcowego wydawcy (opc.tcp://publisher: 62222) i kliknij przycisk **Connect**.

1. Ustanawiania relacji zaufania między połączone fabryki portalu i OPC wydawcy. Gdy pojawi się ostrzeżenie o certyfikacie, kliknij przycisk **Kontynuuj**. Następnie zostanie wyświetlony błąd, że wydawca OPC nie ufa klienta Agent użytkownika sieci Web. Aby rozwiązać ten problem, skopiuj **klienta sieci Web UA** certyfikat od `<SharedFolder>/CertificateStores/rejected/certs` folder `<SharedFolder>/CertificateStores/trusted/certs` folderu w bramie. Nie trzeba ponownie bramę.

Teraz można podłączyć do bramy z chmury, a wszystko będzie gotowe dodać serwery OPC UA do rozwiązania.

## <a name="add-your-own-opc-ua-servers"></a>Dodaj serwery OPC UA

Aby dodać serwery OPC UA do fabryki połączone akcelerator rozwiązań:

1. Przejdź do **połączyć z serwerem OPC UA** w portalu rozwiązania fabryka połączenia.

    1. Uruchom serwer OPC UA, z którym chcesz nawiązać połączenie. Upewnij się, że serwer OPC UA jest osiągalna z OPC wydawcy i Proxy OPC uruchomione w kontenerze (patrz poprzednie komentarze dotyczące rozpoznawania nazw).
    1. Wprowadź adres URL punktu końcowego serwera OPC UA (`opc.tcp://<host>:<port>`) i kliknij przycisk **Connect**.
    1. Podczas konfigurowania połączenia relacji zaufania między portalem fabryki połączony (OPC Agent użytkownika klienta) i serwer OPC UA, z którym próbujesz nawiązać połączenie zostanie nawiązane. Na pulpicie nawigacyjnym połączone fabryki otrzymasz **nie można zweryfikować certyfikatu serwera, o którym chcesz się połączyć** ostrzeżenie. Gdy pojawi się ostrzeżenie o certyfikacie, kliknij przycisk **Kontynuuj**.
    1. Konfiguracja certyfikatu serwera OPC UA, z którym próbujesz nawiązać połączenie jest trudniejsze do instalacji. Dla komputera opartych na serwerach OPC UA, okno dialogowe ostrzeżenia mogą wystąpić tylko na pulpicie nawigacyjnym, który można potwierdzić. For embedded systems OPC UA serwera w dokumentacji serwera OPC UA do odszukania jak odbywa się to zadanie. Aby wykonać to zadanie, może być konieczne certyfikat klienta OPC UA portal połączone fabryki. Administrator może pobrać tego certyfikatu na **połączyć z serwerem OPC UA** strony:

        ![Portal rozwiązania](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Przeglądanie drzewa OPC UA węzłów serwera OPC UA, kliknij prawym przyciskiem myszy węzły OPC chcesz wysłać wartości do fabryki połączone, a następnie wybierz **publikowania**.

1. Dane telemetryczne teraz wypływających z urządzeniem bramy. Można wyświetlić dane telemetryczne w **lokalizacje fabryki** widok portalu fabryki połączone w obszarze **nowa fabryka**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o architekturze połączone fabryki akcelerator rozwiązań, zobacz [wskazówki akceleratora rozwiązań połączone fabryki](iot-accelerators-connected-factory-sample-walkthrough.md).

Dowiedz się więcej o [wydawcy OPC implementacji](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).