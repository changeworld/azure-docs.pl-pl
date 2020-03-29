---
title: Jak aprowizować urządzenia pod kątem wielodostępności w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Jak aprowizować urządzenia do obsługi wielu urządzeń za pomocą wystąpienia usługi aprowizacji urządzeń (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e0dec0a67ed33186797ccec8066aaad89ceb8dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434751"
---
# <a name="how-to-provision-for-multitenancy"></a>Jak udostępnić wielodostępność 

Zasady alokacji zdefiniowane przez usługę inicjowania obsługi administracyjnej obsługują różne scenariusze alokacji. Dwa typowe scenariusze to:

* **Geolokalizacja / GeoLatency**: Gdy urządzenie przemieszcza się między lokalizacjami, opóźnienie sieci jest poprawiane przez udostępnianie urządzenia do centrum IoT hub najbliżej każdej lokalizacji. W tym scenariuszu grupa centrów IoT, które obejmują regiony, są wybierane do rejestracji. Dla tych rejestracji wybrano zasadę alokacji **najniższych opóźnień.** Ta zasada powoduje, że usługa inicjowania obsługi administracyjnej urządzeń do oceny opóźnienia urządzenia i określić szafy IoT hub z grupy centrów IoT. 

* **Multi-dzierżawy:** Urządzenia używane w ramach rozwiązania IoT może być konieczne przypisane do określonego centrum IoT hub lub grupy IoT hubs. Rozwiązanie może wymagać wszystkich urządzeń dla określonej dzierżawy do komunikowania się z określoną grupą centrów IoT. W niektórych przypadkach dzierżawca może posiadać centra IoT i wymagać, aby urządzenia były przypisywane do ich centrów IoT.

Jest to wspólne, aby połączyć te dwa scenariusze. Na przykład wielodostępne rozwiązanie IoT często przypisuje urządzenia dzierżawy przy użyciu grupy centrów IoT, które są rozproszone w różnych regionach. Te urządzenia dzierżawy można przypisać do centrum IoT hub w tej grupie, która ma najniższe opóźnienie na podstawie lokalizacji geograficznej.

W tym artykule użyto symulowanego przykładu urządzenia z [sdk C usługi Azure IoT,](https://github.com/Azure/azure-iot-sdk-c) aby zademonstrować sposób aprowizowania urządzeń w scenariuszu wielodostępnym w różnych regionach. W tym artykule wykonaj następujące czynności:

* Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwa regionalne centra IoT **(Zachodnie stany USA** i **wschodnie stany USA)**
* Tworzenie rejestracji wielodostępczej
* Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwie regionalne maszyny wirtualne z systemem Linux, które będą działać jako urządzenia w tych samych regionach **(zachodnie stany USA** i **wschodnie stany USA)**
* Konfigurowanie środowiska programistycznego dla zestawu SDK języka Azure IoT C na obu maszynach wirtualnych z systemem Linux
* Symuluj urządzenia, aby zobaczyć, że są one aprowied dla tej samej dzierżawy w najbliższym regionie.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania usługi inicjowania obsługi administracyjnej urządzeń w centrum IoT za pomocą przewodnika](./quick-setup-auto-provision.md) Szybki start w portalu Azure.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Tworzenie dwóch regionalnych centrów IoT

W tej sekcji użyjesz usługi Azure Cloud Shell do utworzenia dwóch nowych regionalnych centrów IoT w regionach **zachodnie stany USA** i **wschodnie stany USA** dla dzierżawy.


1. Użyj usługi Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą polecenia [tworzenia grupy az.](/cli/azure/group#az-group-create) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-us-resource-group* w regionie *eastus.* Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. Ułatwi to oczyszczanie po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Użyj usługi Azure Cloud Shell, aby utworzyć centrum IoT hub w regionie **eastus** za pomocą polecenia [tworzenia centrum az iot.](/cli/azure/iot/hub#az-iot-hub-create) Centrum IoT zostanie dodane do *contoso-us-resource-group*.

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-east-hub* w lokalizacji *eastus.* Należy użyć własnej unikatowej nazwy koncentratora zamiast **contoso-east-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    To polecenie może potrwać kilka minut.

3. Użyj usługi Azure Cloud Shell, aby utworzyć centrum IoT hub w regionie **westus** za pomocą polecenia [tworzenia centrum az iot.](/cli/azure/iot/hub#az-iot-hub-create) To centrum IoT zostanie również dodane do *contoso-us-resource-group*.

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-west-hub* w lokalizacji *westus.* Należy użyć własnej unikatowej nazwy koncentratora zamiast **contoso-west-hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    To polecenie może potrwać kilka minut.



## <a name="create-the-multitenant-enrollment"></a>Tworzenie rejestracji wielodostępczej

W tej sekcji utworzysz nową grupę rejestracji dla urządzeń dzierżawcy.  

Dla uproszczenia w tym artykule używa [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby uzyskać bezpieczniejsze rozwiązanie, należy rozważyć użycie [zaświadczenia certyfikatu X.509](concepts-security.md#x509-certificates) z łańcuchem zaufania.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz wystąpienie usługi inicjowania obsługi administracyjnej urządzeń.

2. Wybierz kartę **Zarządzanie rejestracjami,** a następnie kliknij przycisk **Dodaj grupę rejestracji** u góry strony. 

3. W **programie Dodaj grupę rejestracji**wprowadź następujące informacje i kliknij przycisk **Zapisz.**

    **Nazwa grupy**: Wprowadź **contoso-us-devices**.

    **Typ zaświadczenia**: Wybierz **klucz symetryczny**.

    **Automatyczne generowanie kluczy:** to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do koncentratorów:** Wybierz **opcję Najniższe opóźnienie**.

    ![Dodawanie wielodostępnejednarzyszy grupy rejestracji dla zaświadczania klucza symetrycznego](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. W **programie Dodawanie grupy rejestracji**kliknij pozycję **Połącz nowe centrum IoT,** aby połączyć oba centra regionalne.

    **Subskrypcja:** Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono regionalne centra IoT.

    **Centrum IoT:** wybierz jeden z utworzonych centrów regionalnych.

    **Zasady dostępu:** Wybierz **iothubowner**.

    ![Łączenie regionalnych węzłów IoT z usługą inicjowania obsługi administracyjnej](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po połączeniu obu regionalnych centrów IoT należy je wybrać dla grupy rejestracji i kliknąć przycisk **Zapisz,** aby utworzyć regionalną grupę centrum IoT dla rejestracji.

    ![Tworzenie regionalnej grupy centrum dla rejestracji](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po zapisaniu rejestracji otwórz ją ponownie i zanotuj **klucz podstawowy**. Należy najpierw zapisać rejestrację, aby wygenerowano klucze. Ten klucz będzie używany do generowania unikatowych kluczy urządzenia dla obu symulowanych urządzeń później.


## <a name="create-regional-linux-vms"></a>Tworzenie regionalnych maszyn wirtualnych z systemem Linux

W tej sekcji utworzysz dwie regionalne maszyny wirtualne systemu Linux (maszyny wirtualne). Te maszyny wirtualne uruchomi próbkę symulacji urządzenia z każdego regionu, aby zademonstrować inicjowanie obsługi administracyjnej urządzeń dzierżawy z obu regionów.

Aby ułatwić oczyszczanie, te maszyny wirtualne zostaną dodane do tej samej grupy zasobów, która zawiera utworzone centra IoT, *contoso-us-resource-group*. Jednak maszyny wirtualne będą działać w oddzielnych regionach **(Zachodnie stany USA** i **wschodnie stany USA).**

1. W usłudze Azure Cloud Shell wykonaj następujące polecenie, aby utworzyć maszynę wirtualną regionu **wschodnich stanów USA** po wyrobieniu następujących zmian parametrów w poleceniu:

    **--name**: Wprowadź unikatową nazwę maszyny wirtualnej urządzenia regionalnego **we wschodnich stanach USA.** 

    **--admin-username**: Użyj własnej nazwy użytkownika administratora.

    **--admin-password**: Użyj własnego hasła administratora.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Wykonanie tego polecenia potrwa kilka minut. Po zakończeniu polecenia zanotuj wartość **publicIpAddress** dla maszyny wirtualnej regionu Wschodnie stany USA.

1. W usłudze Azure Cloud Shell wykonaj polecenie, aby utworzyć maszynę wirtualną regionu **Zachodniego Us Us** Po wyrobieniu następujących zmian parametrów w poleceniu:

    **--name**: Wprowadź unikatową nazwę maszyny wirtualnej urządzenia regionalnego **w zachodnie stany USA.** 

    **--admin-username**: Użyj własnej nazwy użytkownika administratora.

    **--admin-password**: Użyj własnego hasła administratora.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    Wykonanie tego polecenia potrwa kilka minut. Po zakończeniu polecenia zanotuj wartość **publicIpAddress** dla maszyny wirtualnej regionu Zachodnie stany USA.

1. Otwórz dwie powłoki wiersza polecenia. Połącz się z jedną z regionalnych maszyn wirtualnych w każdej powłoce przy użyciu SSH. 

    Przekaż nazwę użytkownika administratora i publiczny adres IP, który został odnotowany dla maszyny Wirtualnej jako parametry do SSH. Po wyświetleniu monitu wprowadź hasło administratora.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego SDK usługi Azure IoT C

W tej sekcji będzie klonować zestaw SDK C usługi Azure IoT C na każdej maszynie wirtualnej. Zestaw SDK zawiera przykład, który będzie symulować inicjowanie obsługi administracyjnej urządzeń dzierżawcy z każdego regionu.

1. Dla każdej maszyny Wirtualnej zainstaluj **CMake**, **g++**, **gcc**i [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) za pomocą następujących poleceń:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

1. Znajdź nazwę tagu [dla najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK.

1. Klonuj [zestaw SDK języka Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) na obu maszynach wirtualnych.  Użyj znacznika znalezionego w poprzednim kroku `-b` jako wartości parametru:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Dla obu maszyn wirtualnych utwórz nowy folder **cmake** wewnątrz repozytorium i zmień na ten folder.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Dla obu maszyn wirtualnych uruchom następujące polecenie, które tworzy wersję SDK specyficzne dla platformy klienta dewelopera. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>Wyprowadzanie unikatowych kluczy urządzenia

Podczas korzystania z zaświadczania klucza symetrycznego z rejestracjami grupowymi nie używasz kluczy grupy rejestracji bezpośrednio. Zamiast tego należy utworzyć unikatowy klucz pochodny dla każdego urządzenia i wymienione w [rejestracje grupowe z kluczami symetrycznymi](concepts-symmetric-key-attestation.md#group-enrollments).

Aby wygenerować klucz urządzenia, użyj głównego klucza grupy, aby obliczyć [identyfikator HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji urządzenia i przekonwertować wynik na format Base64.

Nie należy dołączać klucza głównego grupy do kodu urządzenia.

Użyj przykładu powłoki Bash, aby utworzyć klucz urządzenia pochodnego dla każdego urządzenia przy użyciu **openssl**.

- Zastąp wartość **klucza** **kluczem kluczem podstawowym,** który został wcześniej odnotowany podczas rejestracji.

- Zastąp wartość **REG_ID** własnym unikatowym identyfikatorem rejestracyjnym dla każdego urządzenia. Użyj małych liter znaków alfanumerycznych i myślnikowych ('-') do zdefiniowania obu identyfikatorów.

Przykład generowania klucza urządzenia dla *contoso-simdevice-east:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Przykład generowania klucza urządzenia dla *contoso-simdevice-west:*

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Urządzenia dzierżawy będą używać ich pochodny klucz urządzenia i unikatowy identyfikator rejestracji do wykonywania zaświadczania klucza symetrycznego z grupy rejestracji podczas inicjowania obsługi administracyjnej do dzierżawy ioT hubów.




## <a name="simulate-the-devices-from-each-region"></a>Symuluj urządzenia z każdego regionu


W tej sekcji zaktualizujesz przykład inicjowania obsługi administracyjnej w zestawie SDK C usługi Azure IoT dla obu regionalnych maszyn wirtualnych. 

Przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie inicjowania obsługi administracyjnej do wystąpienia usługi inicjowania obsługi administracyjnej urządzenia. Sekwencja rozruchowa spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT hub, który jest najbliżej na podstawie opóźnienia.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otwórz **~/azure-iot-sdk-c/inicjowanie obsługi\_administracyjnej\_\_klienta/samples/prov\_\_dev client sample/prov dev\_client\_sample.c** do edycji na obu maszynach wirtualnych.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że `hsm_type` `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` zmienna jest ustawiona na jak pokazano poniżej, aby dopasować metodę zaświadczania grupy rejestracji. 

    Zapisz zmiany w plikach na obu maszynach wirtualnych.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Na obu maszynach wirtualnych `prov_dev_set_symmetric_key_info()` znajdź wywołanie w **prov\_dev\_\_klienta sample.c,** który jest komentowany.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentuj wywołania funkcji i zastąp wartości symbolu zastępczego (w tym nawiasy kątowe) unikatowymi identyfikatorami rejestracyjnymi i pochodnymi kluczami urządzeń dla każdego urządzenia. Poniższe klawisze służą wyłącznie przykładowi. Użyj kluczy wygenerowanych wcześniej.

    Wschodnie stany USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Zachodnie stany USA:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    Zapisz pliki.

1. Na obu maszynach wirtualnych przejdź do przykładowego folderu pokazanego poniżej i skompiluj przykład.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Gdy kompilacja zakończy się pomyślnie, uruchom **prov\_dev\_client\_sample.exe** na obu maszynach wirtualnych, aby symulować urządzenie dzierżawy z każdego regionu. Należy zauważyć, że każde urządzenie jest przydzielane do centrum IoT dzierżawy najbliżej regionów symulowanego urządzenia.

    Uruchom symulację:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Przykładowy wynik z maszyny Wirtualnej wschodnich stanów ZJEDNOCZONYCH:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Przykładowy wynik z maszyny Wirtualnej w stanach zachodnich:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie zamierzasz kontynuować korzystania z zasobu, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten artykuł, aby uniknąć niepotrzebnych opłat.

W tym miejscu przyjęto założenie, że wszystkie zasoby w tym artykule zostały utworzone zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-us-resource-group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polach tekstowych **Filtruj według nazwy...** wpisz nazwę grupy zasobów zawierającej zasoby, **contoso-us-resource-group**. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej Reprovisioning, zobacz [Urządzenia usługi IoT Hub reprovisioning pojęcia](concepts-device-reprovision.md) 
- Aby dowiedzieć się więcej Deprovisioning, zobacz [Jak anulować aprovision urządzeń, które wcześniej były automatycznie aprowizacji](how-to-unprovision-devices.md) 











