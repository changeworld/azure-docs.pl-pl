---
title: 'Szybki start: tworzenie urządzenia usługi Azure IoT Edge w systemie Windows | Microsoft Docs'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć urządzenie usługi IoT Edge, a następnie zdalnie wdrożyć wstępnie skompilowany kod z poziomu witryny Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 54efe7b5c392ad2b4cc3a0de414e04951b268508
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674247"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Szybki start: wdrażanie pierwszego modułu usługi IoT Edge na wirtualnym urządzeniu z systemem Windows

Wypróbuj usługę Azure IoT Edge, wdrażając konteneryzowany kod na wirtualnym urządzeniu usługi IoT Edge. Usługa IoT Edge umożliwia zdalne zarządzanie kodem na urządzeniach, dzięki czemu można wysyłać więcej obciążeń do krawędzi. W przypadku tego przewodnika Szybki start zalecamy używanie maszyny wirtualnej platformy Azure dla urządzenia usługi IoT Edge. Za pomocą maszyny wirtualnej pozwala szybko utworzyć maszynę testową, zainstalować wymagania wstępne, a następnie usunąć go po zakończeniu.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

1. Utwórz centrum IoT.
2. Rejestrowanie urządzenia usługi IoT Edge w centrum IoT Hub.
3. Zainstaluj i uruchom środowisko uruchomieniowe usługi IoT Edge na urządzeniu wirtualnym.
4. Zdalne wdrażanie modułu do urządzenia usługi IoT Edge i wysyłanie telemetrii do usługi IoT Hub.

![Diagram — architektura przewodnika Szybki start dla urządzenia i chmury](./media/quickstart/install-edge-full.png)

Ten przewodnik Szybki start przeprowadzi Cię przez tworzenie maszyny wirtualnej systemu Windows i konfigurowanie jej jako urządzenia usługi IoT Edge. Następnie wdrożysz modułu z witryny Azure Portal na swoim urządzeniu. Moduł wdrażany podczas pracy z tym przewodnikiem Szybki start to symulowany czujnik generujący dane dotyczące temperatury, wilgotności i ciśnienia. Z wykonanej tutaj pracy będziesz korzystać w pozostałych samouczkach usługi Azure IoT Edge, wdrażając moduły do analizy symulowanych danych na potrzeby biznesowe.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Za pomocą interfejsu wiersza polecenia platformy Azure, aby wykonać wiele kroków w tym przewodniku Szybki start. Usługa Azure IoT ma rozszerzenie, aby włączyć dodatkowe funkcje.

Dodaj rozszerzenie usługi Azure IoT do wystąpienia usługi Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zasoby w chmurze:

* Grupa zasobów do zarządzania wszystkimi zasobami używanymi w tym przewodniku Szybki start.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Urządzenie usługi IoT Edge:

* Maszyna wirtualna systemu Windows, która działa jako urządzenie usługi IoT Edge. Tę maszynę wirtualną można utworzyć `{password}` za pomocą następującego polecenia, zastępując bezpiecznym hasłem:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Utworzenie i uruchomienie nowej maszyny wirtualnej może zająć kilka minut. Następnie można pobrać plik RDP do użycia podczas łączenia się z maszyną wirtualną:

  1. Przejdź do nowej maszyny wirtualnej systemu Windows w witrynie Azure portal.
  1. Wybierz przycisk **Połącz**.
  1. Na karcie **RDP** wybierz pozycję **Pobierz plik RDP**.

  Otwórz ten plik za pomocą usługi Podłączanie pulpitu zdalnego, aby `az vm create` połączyć się z maszyną wirtualną systemu Windows przy użyciu nazwy administratora i hasła określonego za pomocą polecenia.

> [!NOTE]
> Ten przewodnik Szybki start umożliwia uproszczenia korzystania z maszyny wirtualnej dla pulpitu systemu Windows. Aby uzyskać informacje o tym, które systemy operacyjne Windows są ogólnie dostępne w scenariuszach produkcyjnych, zobacz [Systemy obsługiwane przez usługę Azure IoT Edge](support.md).
>
> Jeśli chcesz skonfigurować własne urządzenie z systemem Windows dla usługi IoT Edge, w tym urządzenia z systemem IoT Core, wykonaj kroki opisane w [temacie Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

Rozpocznij pracę z przewodnikiem Szybki start od utworzenia centrum IoT za pomocą interfejsu wiersza polecenia platformy Azure.

![Diagram — tworzenie centrum IoT Hub w chmurze](./media/quickstart/create-iot-hub.png)

W tym przewodniku Szybki start wystarcza warstwa bezpłatna usługi IoT Hub. Jeśli w przeszłości używano usługi IoT Hub i masz już utworzone bezpłatne centrum, możesz używać tego centrum IoT Hub. Każda subskrypcja może zawierać tylko jedno bezpłatne centrum IoT Hub.

Poniższy kod tworzy wolne centrum **F1** w grupie `IoTEdgeResources`zasobów . Zamień `{hub_name}` unikatową nazwę centrum IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Jeśli wystąpi błąd, ponieważ w subskrypcji jest już jedno bezpłatne centrum, zmień jednostkę SKU na **S1**. Jeśli wystąpi błąd polegający na niedostępności nazwy centrum IoT Hub, oznacza to, że ktoś inny ma już centrum o takiej nazwie. Wypróbuj nową nazwę.

## <a name="register-an-iot-edge-device"></a>Rejestrowanie urządzenia usługi IoT Edge

Zarejestruj urządzenie usługi IoT Edge, korzystając z nowo utworzonego centrum IoT.
![Diagram — rejestrowanie urządzenia przy użyciu tożsamości usługi IoT Hub](./media/quickstart/register-device.png)

Utwórz tożsamość urządzenia symulowanego, aby umożliwić mu komunikowanie się z centrum IoT Hub. Tożsamość urządzenia jest przechowywana w chmurze. Aby skojarzyć urządzenie fizyczne z tożsamością urządzenia, używane są unikatowe parametry połączenia.

Ponieważ urządzenia usługi IoT Edge zachowują się inaczej niż typowe urządzenia IoT, a także mogą być inaczej zarządzane, zadeklaruj tę tożsamość jako należącą do urządzenia usługi IoT Edge za pomocą flagi `--edge-enabled`.

1. W powłoce chmury platformy Azure wprowadź poniższe polecenie, aby utworzyć urządzenie o nazwie **myEdgeDevice** w swoim centrum.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Jeśli pojawia się błąd dotyczący kluczy zasad iothubowner, upewnij się, że powłoka w chmurze jest uruchomiona najnowszą wersję rozszerzenia azure-iot.

2. Pobierz parametry połączenia danego urządzenia, które łączy urządzenie fizyczne z tożsamością w usłudze IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Skopiuj wartość klucza `connectionString` z danych wyjściowych JSON i zapisz ją. Ta wartość to parametry połączenia urządzenia. Za pomocą tych parametrów połączenia skonfigurujesz środowisko uruchomieniowe usługi IoT Edge w następnej sekcji.

   ![Pobieranie parametrów połączenia z danych wyjściowych interfejsu wiersza polecenia](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalowanie i uruchamianie środowiska uruchomieniowego usługi IoT Edge

Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu usługi IoT Edge i skonfiguruj je przy użyciu parametrów połączenia urządzenia.
![Diagram — uruchamianie środowiska uruchomieniowego na urządzeniu](./media/quickstart/start-runtime.png)

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Składa się ono z trzech składników. **Demon zabezpieczeń usługi IoT Edge** jest uruchamiany przy każdym uruchomieniu urządzenia IoT Edge przez rozpoczęcie działania agenta usługi IoT Edge. **Agent usługi IoT Edge** ułatwia wdrażanie i monitorowanie modułów na urządzeniu usługi IoT Edge, w tym centrum usługi IoT Edge. **Centrum usługi IoT Edge** obsługuje komunikację między modułami na urządzeniu usługi IoT Edge oraz między urządzeniem a usługą IoT Hub.

Skrypt instalacji zawiera także aparat kontenera o nazwie Moby, który zarządza obrazami kontenerów na urządzeniu usługi IoT Edge.

Podczas instalowania środowiska uruchomieniowego pojawi się prośba o podanie parametrów połączenia urządzenia. Użyj parametrów pobranych za pomocą wiersza polecenia platformy Azure. Za pomocą tych parametrów urządzenie fizyczne jest kojarzone z tożsamością urządzenia usługi IoT Edge na platformie Azure.

### <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

Kroki opisane w tej sekcji mają miejsce na urządzeniu usługi IoT Edge, więc chcesz teraz połączyć się z tą maszyną wirtualną za pośrednictwem pulpitu zdalnego.

### <a name="install-and-configure-the-iot-edge-service"></a>Instalowanie i konfigurowanie usługi IoT Edge

Pobierz i zainstaluj środowisko uruchomieniowe usługi IoT Edge za pomocą programu PowerShell. Do skonfigurowania urządzenia użyj parametrów połączenia urządzenia pobranych z usługi IoT Hub.

1. Jeśli jeszcze tego nie zrobiłeś, wykonaj kroki opisane w [rejestrze nowego urządzenia usługi Azure IoT Edge,](how-to-register-device.md) aby zarejestrować urządzenie i pobrać parametry połączenia urządzenia.

2. Na maszynie wirtualnej uruchom program PowerShell jako administrator.

   >[!NOTE]
   >Użyj sesji programu PowerShell dla systemu AMD64, aby zainstalować usługę IoT Edge, a nie program PowerShell (x86). Jeśli nie masz pewności, którego typu sesji używasz, uruchom następujące polecenie:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, pobiera środowisko uruchomieniowe Moby, a następnie pobiera środowisko wykonawcze usługi IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Urządzenie może zostać automatycznie uruchomione ponownie. Jeśli polecenie Deploy-IoTEdge zostanie wyświetlone z monitem o ponowne uruchomienie komputera, zrób to teraz.

5. Ponownie uruchom program PowerShell jako administrator.

6. Polecenie **Initialize-IoTEdge** konfiguruje środowisko wykonawcze IoT Edge na komputerze. Polecenie domyślnie ręczne inicjowanie obsługi administracyjnej za pomocą kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Po wyświetleniu prośby o podanie wartości **DeviceConnectionString**, wpisz parametry skopiowane w poprzedniej sekcji. Nie dołączaj znaków cudzysłowów otaczających parametry połączenia.

### <a name="view-the-iot-edge-runtime-status"></a>Wyświetlanie stanu środowiska uruchomieniowego usługi IoT Edge

Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane.

1. Sprawdź stan usługi IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Ponieważ usługa została właśnie uruchomiona po raz pierwszy, tylko moduł **edgeAgent** powinien być widoczny jako uruchomiony. Moduł edgeAgent działa domyślnie i pomaga zainstalować i uruchomić wszelkie dodatkowe moduły, które można wdrożyć na urządzeniu.

    ```powershell
    iotedge list
    ```

   ![Wyświetlanie jednego modułu na urządzeniu](./media/quickstart/iotedge-list-1.png)

Może upłynąć kilka minut, aby zakończyć instalację i uruchomić moduł agenta IoT Edge.

Urządzenie usługi IoT Edge jest teraz skonfigurowane. Jest ono gotowe do uruchamiania modułów wdrożonych w chmurze.

## <a name="deploy-a-module"></a>Wdrażanie modułu

Zarządzając urządzeniem usługi Azure IoT Edge z chmury, wdróż moduł przesyłający dane telemetryczne do centrum IoT Hub.
![Diagram — wdrażanie modułu z chmury do urządzenia](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

W tym przewodniku Szybki start zarejestrowano nowe urządzenie usługi IoT Edge i zainstalowano na nim środowisko uruchomieniowe usługi IoT Edge. Następnie użyto witryny Azure Portal do wdrożenia modułu usługi IoT Edge w celu uruchomienia go na urządzeniu bez konieczności wprowadzenia zmian na samym urządzeniu.

W tym przypadku wypchnięty moduł tworzy dane przykładowe, których można użyć na potrzeby testowania. Moduł symulowanego czujnika temperatury generuje dane środowiskowe, których można użyć do testowania później. Symulowany czujnik monitoruje maszynę i środowisko wokół maszyny. Na przykład ten czujnik może być umieszczony w serwerowni, w hali fabrycznej lub na turbinie wiatrowej. Komunikat zawiera temperaturę i wilgotność otoczenia, temperaturę maszyny, ciśnienie oraz znacznik czasu. W samouczkach usługi IoT Edge dane utworzone przez ten moduł są używane jako dane testowe do analizy.

Upewnij się, że moduł wdrożony z chmury jest uruchomiony na urządzeniu usługi IoT Edge.

```powershell
iotedge list
```

   ![Wyświetlanie trzech modułów na urządzeniu](./media/quickstart/iotedge-list-2.png)

Wyświetl komunikaty wysyłane z modułu czujnika temperatury do chmury.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Przy odwoływaniu się do nazw modułów w poleceniach usługi IoT Edge jest rozróżniana wielkość liter.

   ![Wyświetlanie danych z modułu](./media/quickstart/iotedge-logs.png)

Można również obserwować wiadomości przychodzące do centrum IoT hub przy użyciu [rozszerzenia Usługi Azure IoT Hub dla programu Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz przejść do samouczków dotyczących usługi IoT Edge, możesz użyć urządzenia, które zostało zarejestrowane i skonfigurowane w ramach tego przewodnika Szybki start. W przeciwnym razie można usunąć zasoby platformy Azure, które zostały utworzone w celu uniknięcia opłat.

Jeśli maszyna wirtualna i centrum IoT Hub zostały utworzone w nowej grupie zasobów, możesz usunąć tę grupę i wszystkie powiązane zasoby. Sprawdź dokładnie zawartość grupy zasobów, aby się upewnić, że nie ma w niej żadnych elementów, które chcesz zachować. Jeśli nie chcesz usuwać całej grupy, możesz usunąć poszczególne zasoby.

Usuń grupę **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono urządzenie usługi IoT Edge i wdrożono na nim kod przy użyciu interfejsu usługi Azure IoT Edge w chmurze. Masz teraz urządzenie testowe generujące dane pierwotne dotyczące jego otoczenia.

Następnym krokiem jest skonfigurowanie lokalnego środowiska programistycznego, dzięki czemu można rozpocząć tworzenie modułów usługi IoT Edge, które uruchamiają logikę biznesową.

> [!div class="nextstepaction"]
> [Rozpoczynanie tworzenia modułów usługi IoT Edge dla urządzeń z systemem Windows](tutorial-develop-for-windows.md)
