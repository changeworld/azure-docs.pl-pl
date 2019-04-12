---
title: Jak aprowizować urządzenia dla wielodostępności w usłudze Azure IoT Hub Device Provisioning Service | Dokumentacja firmy Microsoft
description: Jak aprowizować urządzenia dla wielodostępności urządzeń z systemem aprowizacji wystąpienia usługi
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 84e1f57175d772ad281c18b67fa1be484c0cac69
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500740"
---
# <a name="how-to-provision-for-multitenancy"></a>Jak wykonać aprowizację dla wielodostępności 

Zasady alokacji, zdefiniowane przez usługę aprowizacji obsługi różnych scenariuszy alokacji. Dwie typowe scenariusze są następujące:

* **Używanie funkcji Geolokalizacji / GeoLatency**: Przemieszcza się w urządzeniu między lokalizacjami, opóźnienie sieci poprawia posiadanie urządzenia aprowizowane do Centrum IoT hub najbardziej zbliżony do każdej lokalizacji. W tym scenariuszu grupę centra IoT Hub, zajmujące się między różnymi regionami, są zaznaczone dla rejestracji. **Najmniejszego opóźnienia** wybrano zasady alokacji dla tych rejestracji. Ta zasada powoduje, że usługi Device Provisioning ocenić opóźnienia urządzenie i określić dystrybucji Centrum IoT hub z grupy centra IoT Hub. 

* **Wielodostępność**: Urządzenia używane w ramach rozwiązania IoT, może być konieczne można przypisać do określonej usługi IoT hub lub grupy centra IoT Hub. Rozwiązanie może wymagać od wszystkich urządzeń do konkretnej dzierżawy do komunikowania się z określonej grupy centra IoT Hub. W niektórych przypadkach dzierżawy może własne centra IoT i wymaga urządzenia, które ma być przypisane do ich centra IoT Hub.

Są często do łączenia tych dwóch scenariuszy. Na przykład wielodostępne rozwiązania IoT często spowoduje przypisanie urządzeń dzierżawy przy użyciu grupy centra IoT, które są rozproszone między regionami. Te urządzenia dzierżawy można przypisać do usługi IoT hub w tej grupie, która ma najniższe opóźnienie, na podstawie lokalizacji geograficznej.

W tym artykule wykorzystano przykładowe symulowane urządzenie, z [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) do pokazują, jak aprowizować urządzenia w scenariuszu wielodostępna między regionami. W tym artykule należy wykonać następujące czynności:

* Użyj wiersza polecenia platformy Azure, aby utworzyć dwóch regionalnych centrów IoT Hub (**zachodnie stany USA** i **wschodnie stany USA**)
* Tworzenie wielodostępnych rejestracji
* Użyj wiersza polecenia platformy Azure, aby utworzyć dwóch regionalnych maszyn wirtualnych systemu Linux do działania jako urządzenia w tych samych regionach (**zachodnie stany USA** i **wschodnie stany USA**)
* Konfigurowanie środowiska deweloperskiego dla zestawu SDK C usługi IoT platformy Azure na obu maszynach wirtualnych systemu Linux
* Symulowanie urządzenia, które chcesz zobaczyć, że są one aprowizacji dla tej samej dzierżawy w regionie najbliższym.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [skonfigurować IoT Hub Device Provisioning Service przy użyciu witryny Azure portal](./quick-setup-auto-provision.md) Szybki Start.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>Tworzenie dwóch regionalnych centrów IoT Hub

W tej sekcji użyjesz usługi Azure Cloud Shell, aby utworzyć dwie nowe regionalnych centrów IoT, w **zachodnie stany USA** i **wschodnie stany USA** regionów dla dzierżawy.


1. Użyj usługi Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-us-resource-group* w *eastus* regionu. Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. To ułatwi oczyszczania po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Umożliwia tworzenie Centrum IoT w usłudze Azure Cloud Shell **eastus** regionie [az iot hub, utworzyć](/cli/azure/iot/hub#az-iot-hub-create) polecenia. Usługa IoT hub zostanie dodany do *contoso-us-resource-group*.

    Poniższy przykład obejmuje tworzenie Centrum IoT o nazwie *contoso wschód hub* w *eastus* lokalizacji. Należy korzystać z własnej nazwy Centrum unikatowe, zamiast **contoso wschód hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    To polecenie może potrwać kilka minut.

3. Umożliwia tworzenie Centrum IoT w usłudze Azure Cloud Shell **westus** regionie [az iot hub, utworzyć](/cli/azure/iot/hub#az-iot-hub-create) polecenia. To IoT hub zostanie także dodana do *contoso-us-resource-group*.

    Poniższy przykład obejmuje tworzenie Centrum IoT o nazwie *contoso zachód hub* w *westus* lokalizacji. Należy korzystać z własnej nazwy Centrum unikatowe, zamiast **contoso zachód hub**.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    To polecenie może potrwać kilka minut.



## <a name="create-the-multitenant-enrollment"></a>Tworzenie wielodostępnych rejestracji

W tej sekcji utworzysz nową grupę rejestracji dla urządzeń z dzierżawy.  

Dla uproszczenia w tym artykule użyto [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) w przypadku rejestracji. Bardziej bezpieczne rozwiązanie, należy wziąć pod uwagę przy użyciu [zaświadczenia certyfikat X.509](concepts-security.md#x509-certificates) przy użyciu łańcucha zaufania.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz swoje wystąpienie usługi Device Provisioning.

2. Wybierz **Zarządzanie rejestracjami** kartę, a następnie kliknij przycisk **Dodaj grupę rejestracji** znajdujący się u góry strony. 

3. Na **Dodaj grupę rejestracji**, wprowadź następujące informacje i kliknij przycisk **Zapisz** przycisku.

    **Nazwa grupy**: Wprowadź **contoso-us urządzeń**.

    **Typ zaświadczeń**: Wybierz **klucz symetryczny**.

    **Automatycznie generuj klucze**: Już to pole wyboru powinno być zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów**: Wybierz **najmniejszego opóźnienia**.

    ![Dodaj grupę wielodostępne rejestracji dla zaświadczenia klucza symetrycznego](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. Na **Dodaj grupę rejestracji**, kliknij przycisk **Link nowego centrum IoT hub** połączyć oba swoje centra regionalne.

    **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono regionalnych centrów IoT.

    **Usługa IoT hub**: Wybierz jeden z centra regionalne, który został utworzony.

    **Zasady dostępu**: Wybierz **iothubowner**.

    ![Link regionalne centra IoT Hub z usługą aprowizacji](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. Po obu regionalnych centrów IoT Hub zostały połączone, należy wybierać grupy rejestracji i kliknij przycisk **Zapisz** na utworzenie regionalnych grupy Centrum IoT rejestracji.

    ![Utwórz grupę regionalnym centrum dla rejestracji](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. Po zapisaniu rejestrację, otwórz go ponownie i zanotuj **klucz podstawowy**. Musisz zapisać rejestracji, aby najpierw mają klucze generowane. Ten klucz będzie używany do generowania kluczy unikatowych urządzeń dla obu symulowanych urządzeń później.


## <a name="create-regional-linux-vms"></a>Tworzenie maszyn wirtualnych systemu Linux regionalne

W tej sekcji utworzysz dwie regionalne maszyny wirtualne systemu Linux (VM). Te maszyny wirtualne uruchomią przykładu symulacji urządzenia z każdego regionu, aby zademonstrować, inicjowanie obsługi administracyjnej urządzeń dla urządzeń z dzierżawy z obu regionów.

Zapewnienie oczyszczania było prostsze, te maszyny wirtualne zostaną dodane do tej samej grupie zasobów, która zawiera centra IoT, które zostały utworzone, *contoso-us-resource-group*. Jednak maszyn wirtualnych jest uruchamiana w oddzielnych regionach (**zachodnie stany USA** i **wschodnie stany USA**).

1. W usłudze Azure Cloud Shell, wykonaj następujące polecenie, aby utworzyć **wschodnie stany USA** regionu maszyn wirtualnych po wprowadzeniu następujących zmian parametr w poleceniu:

    **— Nazwa**: Wprowadź unikatową nazwę dla Twojej **wschodnie stany USA** regionalnych urządzenia maszyny Wirtualnej. 

    **--admin-username**: Korzystać z własnej nazwy użytkownika administratora.

    **hasło — administratora**: Użyj hasła administratora.

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

    To polecenie potrwa kilka minut. Po zakończeniu polecenia Zanotuj **publicznego adresu IP** wartość dla regionu wschodnie stany USA maszyny Wirtualnej.

1. W usłudze Azure Cloud Shell, wykonaj polecenie, aby utworzyć **zachodnie stany USA** regionu maszyn wirtualnych po wprowadzeniu następujących zmian parametr w poleceniu:

    **— Nazwa**: Wprowadź unikatową nazwę dla Twojej **zachodnie stany USA** regionalnych urządzenia maszyny Wirtualnej. 

    **--admin-username**: Korzystać z własnej nazwy użytkownika administratora.

    **hasło — administratora**: Użyj hasła administratora.

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

    To polecenie potrwa kilka minut. Po zakończeniu polecenia Zanotuj **publicznego adresu IP** wartość dla regionu zachodnie stany USA maszyny Wirtualnej.

1. Otwórz dwóch powłoki wiersza polecenia. Połącz się z jednym regionalnych maszyn wirtualnych w każdym powłoki przy użyciu protokołu SSH. 

    Przekaż swoją nazwę użytkownika administratora i publiczny adres IP, zanotowaną dla maszyny Wirtualnej jako parametry do protokołu SSH. Wprowadź hasło administratora, po wyświetleniu monitu.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego zestawu SDK C usługi Azure IoT

W tej sekcji zostanie sklonować zestawu SDK C usługi IoT platformy Azure na każdej maszynie Wirtualnej. Zestaw SDK zawiera próbki, który będzie symulowanie urządzenia dzierżawy, inicjowanie obsługi administracyjnej z każdego regionu.


1. Dla każdej maszyny Wirtualnej, należy zainstalować **Cmake**, **g ++**, **gcc**, i [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) przy użyciu następujących poleceń:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. Klonuj [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) na obu maszynach wirtualnych.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

1. Dla obu maszyn wirtualnych, Utwórz nową **cmake** folderu w repozytorium i zmiany do tego folderu.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. Dla obu maszyn wirtualnych, uruchom następujące polecenie tworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. 

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


## <a name="derive-unique-device-keys"></a>Tworzenia kluczy unikatowych urządzeń

Korzystając z zaświadczenie klucza symetrycznego za pomocą rejestracji grup, nie używaj kluczy grupy rejestracji bezpośrednio. Zamiast tego można utworzyć unikatowe pochodne klucza dla każdego urządzenia i już wspomniano w [rejestracji grup przy użyciu kluczy symetrycznych](concepts-symmetric-key-attestation.md#group-enrollments).

Aby wygenerować klucz urządzenia, należy użyć klucza głównego grupy do obliczenia [HMAC SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji dla urządzenia i przekonwertować wynik w formacie Base64.

Nie dołączaj klucza głównego usługi grupy w kodzie urządzenia.

Skorzystaj z przykładu powłoki Bash do utworzenia klucza pochodnego urządzenia dla każdego urządzenia przy użyciu **openssl**.

- Zastąp wartość **klucz** z **klucz podstawowy** zanotowanej wcześniej dla Twojej rejestracji.

- Zastąp wartość **REG_ID** przy użyciu własnego Identyfikatora rejestracji unikatowy dla każdego urządzenia. Małe znaki alfanumeryczne i kreski ("-") znaków do definiowania obu identyfikatorów.

Przykład generowania kluczy urządzenia dla *contoso simdevice wschód*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Przykład generowania kluczy urządzenia dla *contoso simdevice zachodni*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


Urządzenia dzierżawy każdego użyje ich klucza pochodnego urządzenia i identyfikator rejestracji unikatowy przeprowadzić symetrycznego zaświadczenie klucza przy użyciu grup rejestracji podczas inicjowania obsługi dzierżawy IoT hubs.




## <a name="simulate-the-devices-from-each-region"></a>Symulowanie urządzenia z każdym z regionów


W tej sekcji zaktualizuje przykładu aprowizacji w języku C zestawu SDK usługi Azure IoT dla obu regionalnych maszyn wirtualnych. 

Przykładowy kod symuluje sekwencji rozruchu urządzenia, która spowoduje wysłanie żądania aprowizacji wystąpienia usługi Device Provisioning Service. Sekwencji rozruchu spowoduje, że urządzenie zostało rozpoznane i przypisane do Centrum IoT, które znajdujący się najbliżej zależy od opóźnienia.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. Otwórz **~/azure-iot-sdk-c/provisioning\_klienta/samples/prov\_dev\_klienta\_przykładowe/prov\_dev\_klienta\_sample.c**do edycji na obu maszynach wirtualnych.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że `hsm_type` zmienna jest ustawiona na `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` jak pokazano poniżej, aby dopasować metody zaświadczania grupy rejestracji. 

    Zapisz zmiany w plikach na obu maszynach wirtualnych.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. Na obu maszynach wirtualnych, Znajdź wywołanie `prov_dev_set_symmetric_key_info()` w **prov\_dev\_klienta\_sample.c** który jest ujęty w komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń znaczniki komentarza wywołania funkcji i Zastąp wartości symboli zastępczych (włącznie z nawiasami) rejestracji unikatowe identyfikatory i klucze urządzeń pochodnych dla każdego urządzenia. Klucze poniżej są na przykład tylko do celów. Użyj klawiszy wygenerowania wcześniej.

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

1. Na obu maszynach wirtualnych przejdź do folderu przykładu poniżej, a następnie skompilować przykład.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. Gdy kompilacja zakończy się powodzeniem, uruchom **prov\_dev\_klienta\_sample.exe** na obu maszynach wirtualnych, aby symulować urządzenie dzierżawcy w każdym regionie. Zwróć uwagę, każde urządzenie zaalokowano dzierżawy najbliżej regionów symulowane urządzenie usługi IoT hub.

    Uruchamianie symulacji:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Przykład danych wyjściowych z wschodnie stany USA maszyny Wirtualnej:

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

    Przykład danych wyjściowych z zachodnie stany USA maszyny Wirtualnej:
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

Jeśli planujesz kontynuować pracę z zasobów utworzonych w tym artykule, możesz pozostawić je. Jeśli nie planujesz kontynuować korzystanie z zasobów, użyj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W tym miejscu założono w nim utworzyć wszystkie zasoby w tym artykule, zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-us-resource-group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów o nazwie:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W **Filtruj według nazwy...**  polu tekstowym wpisz nazwę zasobu, grupy, zawierający zasoby, **contoso-us-resource-group**. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej Reprovisioning, zobacz [reprovisioning pojęcia dotyczące urządzeń usługi IoT Hub](concepts-device-reprovision.md) 
- Aby dowiedzieć się więcej anulowania zastrzeżenia, zobacz [jak anulować aprowizację urządzeń, które wcześniej zostały udostępnione do automatycznego](how-to-unprovision-devices.md) 











