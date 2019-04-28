---
title: Wdrażanie bramy połączonej fabryki — Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć bramę na Windows lub Linux, aby umożliwić łączność z akcelerator rozwiązania połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450192"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Wdrażanie bramy dla połączonej fabryki akcelerator rozwiązań w systemie Windows lub Linux krawędzi

Potrzebujesz dwóch składników oprogramowania do wdrożenia bramy usługi edge dla *połączonej fabryki* akcelerator rozwiązań:

-  *Serwera Proxy OPC* nawiąże połączenie z połączonej fabryki. Serwer Proxy OPC, a następnie czeka na komunikaty poleceń i kontroli z zintegrowanej przeglądarki OPC, działający w portalu rozwiązania połączonej fabryki.

-  *Wydawca OPC* nawiązanie połączenia z istniejących serwerów OPC UA w środowisku lokalnym i przekazuje komunikaty telemetryczne z nich do połączonej fabryki. Możesz połączyć urządzenie klasyczne OPC za pomocą [adapter klasycznego OPC dla serwera OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Oba te składniki są typu open source i są dostępne jako źródło w witrynie GitHub i jako kontenery platformy Docker w witrynie DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Nie potrzebujesz publicznego adresu IP lub otwieranie portów dla ruchu przychodzącego w zaporze bramy albo składnika. Składniki serwera Proxy OPC i wydawca OPC używać tylko wychodzącego portu 443.

Kroki opisane w tym artykule opisano do wdrożenia bramy krawędzi za pomocą platformy Docker w systemie Windows lub Linux. Brama zapewnia łączność do akceleratora rozwiązania połączonej fabryki. Umożliwia także składniki bez połączonej fabryki.

> [!NOTE]
> Oba te składniki mogą być używane jako moduły w [usługi Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Wybierz urządzenie bramy

Jeśli jeszcze nie masz urządzenia bramy, firma Microsoft zaleca, Kup bram komercyjnych jednego z ich partnerów. Aby uzyskać listę bram urządzeń zgodnych z rozwiązania połączonej fabryki, odwiedź stronę [Azure IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/?q=opc). Postępuj zgodnie z instrukcjami, które pochodzą z urządzenia do konfigurowania bramy.

Użyj poniższych instrukcji, jeśli musisz ręcznie skonfigurować istniejące urządzenie bramy.

## <a name="install-and-configure-docker"></a>Instalowanie i konfigurowanie platformy Docker

Zainstaluj [Docker for Windows](https://www.docker.com/docker-windows) na urządzenie bramy z systemem Windows lub użyj Menedżera pakietów, aby zainstalować platformę docker na swoim urządzeniu bramy oparte na systemie Linux.

Podczas instalacji platformy Docker for Windows należy wybrać dysk na komputerze z hosta, aby udostępnić za pomocą platformy Docker. Poniższy zrzut ekranu przedstawia, udostępnianie **D** dysku w systemie Windows. Udostępnianie dysku zezwala na dostęp do dysku hosta w kontenerze platformy docker:

![Zainstalować platformę Docker for Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Można również wykonać ten krok, po zainstalowaniu platformy docker z **ustawienia** okna dialogowego. Kliknij prawym przyciskiem myszy **Docker** ikonę na pasku zadań systemu Windows i wybierz polecenie **ustawienia**. Jeśli ważne aktualizacje Windows zostały wdrożone w systemie, takie jak aktualizacji Windows Fall Creators update, anulowanie udostępniania z stacje i udostępniaj je ponownie, aby odświeżyć praw dostępu.

Jeśli używasz systemu Linux, aby umożliwić dostęp do systemu plików jest wymagana żadna konfiguracja dodatkowych.

W Windows należy utworzyć folder na dysku, na którym ją udostępniono platformy Docker, w systemie Linux, Utwórz folder na ścieżce głównego systemu plików. Ten instruktażu odwołuje się do tego folderu jako `<SharedFolder>`.

Gdy odwołasz celu `<SharedFolder>` za pomocą polecenia Docker, upewnij się, że poprawna składnia dla systemu operacyjnego. Poniżej przedstawiono dwa przykłady, jeden dla Windows i jeden dla systemu Linux:

- Jeśli jesteś za pomocą folderu `D:\shared` na Windows jako usługi `<SharedFolder>`, składnia polecenia Docker jest `d:/shared`.

- Jeśli jesteś za pomocą folderu `/shared` w systemie Linux jako swojej `<SharedFolder>`, składnia polecenia Docker jest `/shared`.

Aby uzyskać więcej informacji, zobacz [korzysta z woluminów](https://docs.docker.com/engine/admin/volumes/volumes/) dokumentacja aparatu docker.

## <a name="configure-the-opc-components"></a>Konfigurowanie składników OPC

Przed zainstalowaniem składników OPC, wykonaj następujące kroki, aby przygotować środowisko:

1. Aby ukończyć wdrożenie bramy, musisz mieć **iothubowner** parametry połączenia usługi IoT Hub w danym wdrożeniu połączonej fabryki. W [witryny Azure portal](https://portal.azure.com/), przejdź do Centrum IoT w grupie zasobów utworzone podczas wdrażania rozwiązania połączonej fabryki. Kliknij przycisk **zasady dostępu współdzielonego** dostęp do **iothubowner** ciąg połączenia:

    ![Znajdź parametry połączenia Centrum IoT Hub](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Kopiuj **parametry połączenia — klucz podstawowy** wartości.

1. Aby umożliwić komunikację między kontenerami aparatu docker, konieczne jest siecią mostka zdefiniowanych przez użytkownika. Aby utworzyć siecią mostka dla swoich kontenerów, uruchom następujące polecenia w wierszu polecenia:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Aby sprawdzić **iot_edge** sieci mostkowanej został utworzony, uruchom następujące polecenie:

    ```cmd/sh
    docker network ls
    ```

    Twoje **iot_edge** sieci mostkowanej znajduje się na liście sieci.

Aby uruchomić z wydawcy OPC, uruchom następujące polecenie w wierszu polecenia:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [GitHub wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) i [platformy docker, uruchom odwołanie](https://docs.docker.com/engine/reference/run/) zawierają więcej informacji na temat:

  - Opcje wiersza polecenia platformy docker, określone przed nazwą kontenera (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - Znaczenie wydawca OPC parametry wiersza polecenia określone po nazwie kontenera (`microsoft/iot-edge-opc-publisher:2.1.4`).

- `<IoTHubOwnerConnectionString>` Jest **iothubowner** udostępnionych parametrów połączenia zasad dostępu w witrynie Azure portal. Te parametry połączenia są kopiowane w poprzednim kroku. Potrzebujesz tylko te parametry połączenia za pierwszym uruchomieniu wydawcy OPC. W kolejnych przebiegów należy pominąć znak go ponieważ stanowi zagrożenie dla bezpieczeństwa.

- `<SharedFolder>` Korzystasz, a jego składnia jest opisane w sekcji [Instalowanie i konfigurowanie platformy Docker](#install-and-configure-docker). Wydawca OPC używa `<SharedFolder>` do:

    - Do odczytu i zapisu pliku konfiguracyjnym wydawcy OPC.
    - Zapis do pliku dziennika.
    - Udostępnić oba te pliki poza kontener.

- Wydawca OPC odczytuje konfigurację z **publishednodes.json** pliku, który jest odczytywane i zapisywane `<SharedFolder>/docker` folderu. Ten plik konfiguracyjny definiuje dane węzła OPC UA na danym serwerze OPC UA, które subskrybować wydawca OPC. Pełna składnia **publishednodes.json** pliku jest opisany w [wydawca OPC](https://github.com/Azure/iot-edge-opc-publisher) strony w witrynie GitHub. Po dodaniu bramy, należy umieścić pustą **publishednodes.json** w folderze:

    ```json
    [
    ]
    ```

- Zawsze, gdy serwer OPC UA powiadamia wydawca OPC zmian danych, nowa wartość jest wysyłana do usługi IoT Hub. W zależności od ustawień przetwarzania wsadowego najpierw wydawca OPC są gromadzone dane przed wysłaniem danych do usługi IoT Hub w zadaniu wsadowym.

- Docker nie obsługuje rozpoznawanie nazw systemu NetBIOS, tylko rozpoznawanie nazw DNS. Jeśli nie masz serwera DNS w sieci, można użyć obejścia w poprzednim przykładzie wiersza polecenia. Poprzedni przykład wiersza polecenia używa `--add-host` parametru, aby dodać wpis do pliku hosts kontenerów. Ten wpis umożliwia wyszukiwanie nazwy hosta dla danego `<OpcServerHostname>`, rozpoznawania do danego adresu IP `<IpAddressOfOpcServerHostname>`.

- OPC UA przy użyciu certyfikatów X.509 do uwierzytelniania i szyfrowania. Umieść certyfikat wydawcy OPC na serwer OPC UA, z którymi nawiązywane jest, aby upewnić się, że subskrypcja ufa wydawcy OPC. Wydawca OPC magazyn certyfikatów znajduje się w `<SharedFolder>/CertificateStores` folderu. Można znaleźć certyfikatu wydawcy OPC w `trusted/certs` folderze `CertificateStores` folderu.

  Kroki, aby skonfigurować serwer OPC UA, zależą od urządzenia, którego używasz. te kroki zwykle są udokumentowane w podręczniku użytkownika serwera OPC UA.

Aby zainstalować serwer Proxy OPC, uruchom następujące polecenie w wierszu polecenia:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Musisz tylko raz uruchomić instalację w systemie.

Użyj następującego polecenia do uruchomienia serwera Proxy OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

Parametry połączenia serwera Proxy OPC są zapisywane podczas instalacji. W kolejnych przebiegów można pominąć parametrów połączenia, ponieważ stanowi zagrożenie dla bezpieczeństwa.

## <a name="enable-your-gateway"></a>Włącz bramy

Wykonaj poniższe kroki, aby włączyć bramę akcelerator rozwiązania połączonej fabryki:

1. Gdy oba te składniki są uruchomione, przejdź do **Połącz własny serwer OPC UA** strony w portalu rozwiązania połączonej fabryki. Ta strona jest dostępna tylko dla administratorów w rozwiązaniu. Wprowadź adres URL punktu końcowego wydawcy (opc.tcp://publisher: 62222) i kliknij przycisk **Connect**.

1. Ustanawiania relacji zaufania między portalem połączonej fabryki i wydawca OPC. Gdy pojawi się ostrzeżenie o certyfikacie, kliknij przycisk **Kontynuuj**. Następnie zostanie wyświetlony błąd, że wydawca OPC nie ufa klienta sieci Web UA. Aby rozwiązać ten problem, skopiuj **klienta sieci Web UA** certyfikat od `<SharedFolder>/CertificateStores/rejected/certs` folder `<SharedFolder>/CertificateStores/trusted/certs` folderu w bramie. Nie trzeba ponownie uruchomić bramę.

Teraz można podłączyć do bramy z chmury, a wszystko jest gotowe do dodania serwerów OPC UA z rozwiązaniem.

## <a name="add-your-own-opc-ua-servers"></a>Dodawanie własnych serwerów OPC UA

Aby dodać serwery OPC UA do akcelerator rozwiązania połączonej fabryki:

1. Przejdź do **Połącz własny serwer OPC UA** strony w portalu rozwiązania połączonej fabryki.

    1. Uruchom serwer OPC UA, z którym chcesz się połączyć. Upewnij się, że Twój serwer OPC UA jest osiągalna z wydawcy OPC i serwer Proxy OPC działającej w kontenerze. Zobacz poprzednie komentarzy dotyczących rozpoznawania nazw.
    1. Wprowadź adres URL punktu końcowego serwera OPC UA (`opc.tcp://<host>:<port>`) i kliknij przycisk **Connect**.
    1. Proces instalacji połączenia ustanawia relację zaufania między portalem połączonej fabryki (OPC UA klienta) i serwer OPC UA, z którym próbujesz nawiązać połączenie. Na pulpicie nawigacyjnym połączonej fabryki otrzymasz **nie można zweryfikować certyfikatu serwera, którą chcesz połączyć** ostrzeżenie. Gdy pojawi się ostrzeżenie o certyfikacie, kliknij przycisk **Kontynuuj**.
    1. Konfiguracja certyfikatu serwera OPC UA, z którym próbujesz nawiązać połączenie z jest trudniejsze do instalacji. Dla serwerów opartych na komputerze serwera OPC UA po prostu może zostać okno dialogowe z ostrzeżeniem na pulpicie nawigacyjnym, który można potwierdzić. For embedded systems serwera OPC UA lub dokumentacji Twój serwer OPC UA w celu wyszukania jak odbywa się to zadanie. Aby wykonać to zadanie, może być konieczne certyfikat klienta OPC UA portal połączonej fabryki. Administrator można pobrać tego certyfikatu **Połącz własny serwer OPC UA** strony:

        ![Portal rozwiązania](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Przeglądać drzewo węzłów OPC UA serwera OPC UA, kliknij prawym przyciskiem myszy węzły OPC chcesz wysyłać wartości połączonej fabryki, a następnie wybierz pozycję **publikowania**.

1. Dane telemetryczne teraz wypływających z urządzeniem bramy. Możesz wyświetlić dane telemetryczne w **lokalizacje fabryk** widoku portalu połączonej fabryki, w obszarze **nowa fabryka**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o architekturze akcelerator rozwiązania połączonej fabryki, zobacz [Przewodnik po akceleratorze rozwiązań połączonej fabryki](iot-accelerators-connected-factory-sample-walkthrough.md).

Dowiedz się więcej o [implementacja referencyjna wydawcy OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).