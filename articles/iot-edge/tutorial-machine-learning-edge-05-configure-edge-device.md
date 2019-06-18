---
title: Konfigurowanie urządzenia usługi IoT Edge — Machine Learning na platformie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Skonfiguruj maszynę wirtualną platformy Azure z systemem Linux jako urządzenie usługi Azure IoT Edge, która pełni rolę przezroczystej bramy.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155623"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Samouczek: Konfigurowanie urządzenia usługi IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii, samouczek dotyczący przy użyciu usługi Azure Machine Learning w usłudze IoT Edge. Jeśli została wyświetlona bezpośrednio w tym artykule, firma Microsoft zachęca rozpoczynać się [najpierw artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii, aby uzyskać najlepsze wyniki.

W tym artykule możemy skonfigurować maszynę wirtualną platformy Azure z systemem Linux jako urządzenia Azure IoT Edge, która pełni rolę przezroczystej bramy. Konfiguracja przezroczystej bramy umożliwia urządzeniom na łączenie z usługą Azure IoT Hub za pośrednictwem bramy, nie wiedząc o tym znajdującą się bramy. W tym samym czasie dla użytkownika obsługującego urządzeń w usłudze IoT Hub nie rozpoznaje urządzenie bramy pośredniej. Ostatecznie używamy przezroczystej bramy można dodać analiz krawędziowych do naszego systemu, dodając moduły usługi IoT Edge do bramy.

Kroki opisane w tym artykule są najczęściej wykonywane przez dewelopera w chmurze.

## <a name="generate-certificates"></a>Generowanie certyfikatów

Dla urządzeń do działania jako brama musi mieć możliwość nawiązania bezpiecznego połączenia podrzędnego urządzeń. Usługa Azure IoT Edge umożliwia użycie infrastruktury kluczy publicznych (PKI) do skonfigurowania bezpiecznych połączeń między urządzeniami. W tym przypadku możemy zezwolenie podrzędnym urządzenia połączyć się z urządzenia usługi IoT Edge, działając jako przezroczystej bramy. Aby zachować bezpieczeństwo uzasadnione, podrzędnym urządzenia powinny potwierdzenia tożsamości urządzenia usługi IoT Edge. Aby uzyskać więcej informacji na temat wykorzystania certyfikatów przez urządzenia usługi IoT Edge, zobacz [szczegóły użycia certyfikatu usługi Azure IoT Edge](iot-edge-certs.md).

W tej sekcji utworzymy certyfikatów z podpisem własnym za pomocą obrazu platformy Docker, które możemy następnie skompilować i uruchomić. Wybraliśmy używać obrazu platformy Docker do ukończenia tego kroku, jak znacząco zmniejszyć liczbę kroków potrzebnych do tworzenia certyfikatów na komputerze deweloperskim Windows. Zobacz [generowanie certyfikatów z Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) Aby uzyskać szczegółowe informacje na temat sposobu tworzenia certyfikatów bez korzystania z kontenera. [Generowanie certyfikatów z systemem Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) ma zbiór instrukcji, które firma Microsoft zautomatyzowane przy użyciu obrazu platformy Docker.

1. Zaloguj się do tworzenia maszyny wirtualnej.

2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby utworzyć katalog na maszynie Wirtualnej.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Rozpocznij **Docker for Windows** z menu Windows Start.

4. Otwórz program Visual Studio Code.

5. Wybierz **pliku** > **Otwórz Folder...**  i wybierz polecenie **C:\\źródła\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Kliknij prawym przyciskiem myszy w pliku dockerfile, a następnie wybierz **tworzenia obrazu**.

7. W oknie dialogowym, zaakceptuj wartość domyślną dla nazwy obrazu i tagu: **createcertificates:latest**.

8. Poczekaj na zakończenie kompilacji.

    > [!NOTE]
    > Zobaczysz ostrzeżenie dotyczące o brakuje klucza publicznego. Jest ona bezpiecznie zignorować to ostrzeżenie. Podobnie zobaczysz ostrzeżenie zaleca wyboru/reset uprawnień na obrazie, który jest bezpiecznie zignorować ten obraz.

9. W oknie terminalu programu Visual Studio Code uruchomienia kontenera createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Wyświetli monit o platformy docker w celu uzyskania dostępu do **c:\\**  dysku. Wybierz **udostępnić go**.

11. Wprowadź swoje poświadczenia, po wyświetleniu monitu.

12. Raz kontenera zakończy działa, sprawdź następujące pliki w **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Przekaż certyfikaty usługi Azure Key Vault

Nasze certyfikaty Przechowuj w bezpiecznym miejscu i udostępnić je przy użyciu wielu urządzeń, firma Microsoft przekaże certyfikatów w usłudze Azure Key Vault. Jak widać na powyższej liście, mamy dwa rodzaje plików certyfikatów: Plik PFX i podaj PEM. Plik PFX będzie traktowany jako certyfikaty magazynu kluczy do przesłania do usługi Key Vault. Pliki PEM to zwykły tekst, a firma Microsoft będzie traktowana jako wpisy tajne z magazynu Key. Firma Microsoft będzie używać usługi Key Vault, skojarzone z obszarem roboczym usługi Azure Machine Learning, które zostały utworzone przez uruchomienie [notesów usługi Azure](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Z [witryny Azure portal](https://portal.azure.com), przejdź do obszaru roboczego usługi Azure Machine Learning.

2. Na stronie Przegląd obszaru roboczego usługi Azure Machine Learning, należy znaleźć nazwę **usługi Key Vault**.

    ![Skopiuj nazwę usługi key vault](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na komputerze deweloperskim należy przekazać certyfikaty do usługi Key Vault. Zastąp **\<subscriptionId\>** i **\<keyvaultname\>** przy użyciu informacji o zasobach.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Jeśli zostanie wyświetlony monit, zaloguj się do platformy Azure.

5. Skrypt będzie uruchamiany przez kilka minut przy użyciu danych wyjściowych, który zawiera listę nowych wpisów usługi Key Vault.

    ![Dane wyjściowe skryptu usługi Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Tworzenie urządzenia usługi IoT Edge

Aby połączyć urządzenie z systemem Azure IoT Edge do usługi IoT hub, najpierw utworzymy tożsamość urządzenia w Centrum. Firma Microsoft mogą parametrów połączenia z tożsamości urządzenia w chmurze i umożliwia konfigurowanie środowiska uruchomieniowego w naszym urządzeniu usługi IoT Edge. Gdy urządzenia został skonfigurowany i nawiązuje połączenie z koncentratorem, możemy wdrażać moduły oraz wysyłać wiadomości. Możemy również zmienić konfiguracji fizycznego urządzenia usługi IoT Edge, zmieniając konfigurację odpowiedniej tożsamości urządzenia w usłudze IoT hub.

W tym samouczku utworzymy nową tożsamość urządzenia przy użyciu programu Visual Studio Code. Można również wykonać te kroki przy użyciu [witryny Azure portal](how-to-register-device-portal.md), lub [wiersza polecenia platformy Azure](how-to-register-device-cli.md).

1. Na komputerze deweloperskim Otwórz program Visual Studio Code.

2. Otwórz **urządzeń usługi Azure IoT Hub** ramki w widoku Eksploratora Visual Studio Code.

3. Kliknij przycisk wielokropka i wybierz pozycję **Utwórz urządzenie brzegowe IoT**.

4. Nazwij urządzenia. Dla wygody używany **aaTurbofanEdgeDevice** tak sortuje wcześniej wszystkich urządzeń klienckich utworzony wcześniej przez kontroler urządzenia, aby wysłać dane testowe.

5. Nowe urządzenie pojawi się na liście urządzeń.

    ![AaTurbofanEdgeDevice nowego widoku w Eksploratorze programu VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Wdrażanie maszyny wirtualnej platformy Azure

Używamy [usługi Azure IoT Edge w systemie Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) obrazu z witryny Azure Marketplace do utworzenia naszej urządzenia usługi IoT Edge na potrzeby tego samouczka. Usługa Azure IoT Edge w obrazie systemu Ubuntu instaluje najnowsze środowisko uruchomieniowe usługi Azure IoT Edge i jego zależności przy uruchamianiu. Wdrożymy maszyny Wirtualnej, przy użyciu skryptu programu PowerShell `Create-EdgeVM.ps1`; szablon usługi Resource Manager `IoTEdgeVMTemplate.json`; a skrypt powłoki, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Włączyć wdrożenia programowe

Aby użyć obrazu z witryny marketplace w ramach wdrożenia inicjowane przez skrypty, musimy włączyć wdrożenia programowe obrazu.

1. Zaloguj się do Portalu Azure.

1. Wybierz pozycję **Wszystkie usługi**.

1. Na pasku wyszukiwania wprowadź i wybierz **Marketplace**.

1. Na pasku wyszukiwania wprowadź i wybierz **usługi Azure IoT Edge w systemie Ubuntu**.

1. Wybierz **mają zostać wdrożone programowo? Rozpoczynanie pracy** hiperłącze.

1. Wybierz **Włącz** przycisk następnie **Zapisz**.

    ![Włączyć wdrożenia programowe dla maszyn wirtualnych](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Zostanie wyświetlone powiadomienie sukces.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Następnie uruchom skrypt, aby utworzyć maszynę wirtualną dla urządzenia usługi IoT Edge.

1. Otwórz okno programu PowerShell i przejdź do **EdgeVM** katalogu.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Uruchom skrypt, aby utworzyć maszynę wirtualną.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Po wyświetleniu monitu podaj wartości dla każdego parametru. Dla subskrypcji grupy zasobów i lokalizacji, w której firma Microsoft zaleca się używać tego samego dostępne dla Ciebie dla wszystkich zasobów w tym samouczku.

    * **Identyfikator subskrypcji platformy Azure**: znaleziono w witrynie Azure portal
    * **Nazwa grupy zasobów**: zapamiętania nazwę do grupowania zasobów na potrzeby tego samouczka
    * **Lokalizacja**: Lokalizacja platformy Azure, w którym zostanie utworzona maszyna wirtualna. Na przykład westus2 lub northeurope. Aby uzyskać więcej informacji, zobacz wszystko [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: Nazwa konta administratora, którego użyjesz zalogować się do maszyny wirtualnej
    * **AdminPassword**: hasło, aby ustawić AdminUsername na maszynie wirtualnej

4. Dla skryptu można było skonfigurować maszynę Wirtualną należy zalogować się do platformy Azure przy użyciu poświadczeń skojarzonych z subskrypcją platformy Azure, której używasz.

5. Skrypt potwierdza informacje dotyczące tworzenia maszyny Wirtualnej. Wybierz **y** lub **Enter** aby kontynuować.

6. Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące czynności:

    * Utwórz grupę zasobów, jeśli go jeszcze nie istnieje
    * Tworzenie maszyny wirtualnej
    * Dodać wyjątki sieciowej grupy zabezpieczeń dla maszyny Wirtualnej dla portów 22 (SSH), 5671 (AMQP), 5672 (AMPQ) i 443 (SSL)
    * Zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. Skrypt generuje parametry połączenia SSH do łączenia się z maszyną Wirtualną. Skopiuj parametry połączenia do kolejnego kroku.

    ![Skopiuj parametry połączenia SSH dla maszyny Wirtualnej](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Nawiązywanie połączenia z urządzeniem usługi IoT Edge

W kolejnych sekcjach kilka Konfigurowanie maszyny wirtualnej platformy Azure, którą utworzyliśmy. Pierwszym krokiem jest nawiązanie połączenia z maszyną wirtualną.

1. Otwórz wiersz polecenia, a następnie wklej parametry połączenia SSH, który został skopiowany z danych wyjściowych skryptu. Wprowadź dane dla nazwy użytkownika, sufiks i region, zgodnie z podanych wartości do skryptu programu PowerShell w poprzedniej sekcji.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Po wyświetleniu monitu, aby zweryfikować autentyczności hosta, wpisz **tak** i wybierz **Enter**.

3. Po wyświetleniu monitu podaj hasło.

4. Ubuntu wyświetla komunikat powitalny, a następnie powinien zostać wyświetlony monit podobny `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Pobierz certyfikaty usługi Key Vault

W tym artykule certyfikaty przekazany do usługi Key Vault, aby były one dostępne dla naszej urządzenia usługi IoT Edge i naszych urządzenia liścia, to urządzenie podrzędne, które używa urządzenia usługi IoT Edge jako bramy do komunikowania się z usługą IoT Hub. Firma Microsoft zajmuje się urządzenia typu liść w dalszej części tego samouczka. W tej sekcji należy pobrać certyfikatów na urządzeniu usługi IoT Edge.

1. W sesji SSH na maszynie wirtualnej systemu Linux Zaloguj się do platformy Azure przy użyciu wiersza polecenia platformy Azure.

    ```bash
    az login
    ```

1. Zostanie wyświetlony monit Otwórz w przeglądarce <https://microsoft.com/devicelogin> i wprowadź unikatowy kod. Te kroki można wykonać na komputerze lokalnym. Zamknij okno przeglądarki, po zakończeniu uwierzytelniania.

1. Po pomyślnym uwierzytelnieniu maszyny Wirtualnej systemu Linux Zaloguj i wyświetlić listę swoich subskrypcji platformy Azure.

1. ASet subskrypcji platformy Azure, którego chcesz używać dla poleceń interfejsu wiersza polecenia platformy Azure.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Utwórz katalog na maszynie Wirtualnej certyfikatów.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Pobierz certyfikaty, które są przechowywane w usłudze key vault: nowy edge — urządzenia — pełnej chain.cert.pem, nowe device.key.pem krawędzi i azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aktualizowanie konfiguracji urządzenia usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge korzysta /etc/iotedge/config.yaml pliku do utrwalenia jego konfiguracji. Musimy zaktualizować trzy informacje w tym pliku:

* **Parametry połączenia urządzenia**: parametry połączenia z tożsamości tego urządzenia w usłudze IoT Hub
* **Certyfikaty:** certyfikaty na potrzeby połączeń z urządzeniami podrzędne
* **Nazwa hosta:** w pełni kwalifikowana nazwa domeny (FQDN) urządzenia usługi IoT Edge dla maszyny Wirtualnej.

*Usługi Azure IoT Edge w systemie Ubuntu* obrazu, które firma Microsoft użyte do utworzenia maszyny Wirtualnej IoT Edge jest dostarczany za pomocą skryptu powłoki, która aktualizuje config.yaml przy użyciu parametrów połączenia.

1. W programie Visual Studio Code kliknij prawym przyciskiem myszy na urządzeniu usługi IoT Edge, a następnie wybierz **parametry połączenia urządzenia kopii**.

    ![Skopiuj parametry połączenia z programu Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. W sesji SSH Uruchom polecenie, aby zaktualizować plik config.yaml parametrami połączenia urządzenia.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Następnie zaktualizujemy certyfikatów i nazwy hosta przez bezpośrednią edycję config.yaml.

1. Otwórz plik config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Zaktualizuj certyfikaty części config.yaml, usuwając wiodące `#` i ustawianie ścieżki, więc plik wygląda następująco:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Upewnij się, że "certyfikatów:" nie ma przypisanego poprzedzających białe znaki i każdy z certyfikatów jest poprzedzony przez dwie spacje.

    Kliknij prawym przyciskiem myszy w systemie nano zostaną Wklej zawartość Schowka do bieżącego położenia kursora. Aby zastąpić ciąg, użyj strzałek z klawiatury, aby przejść do ciągu, który chcesz zamienić, Usuń ciąg, a następnie kliknij prawym przyciskiem myszy, aby wkleić z buforu.

3. W witrynie Azure portal przejdź do maszyny wirtualnej. Skopiuj nazwę DNS (nazwę FQDN komputera) z **Przegląd** sekcji.

4. Wklej nazwę FQDN, w sekcji hostname config.yml. Upewnij się, że nazwa jest tylko małe litery.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Zapisz i zamknij plik (`Ctrl + X`, `Y`, `Enter`).

6. Ponownie uruchom demona iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Sprawdzanie stanu demona usługi IoT Edge (po wykonaniu polecenia, wpisz ": q" Aby zakończyć działanie).

    ```bash
    systemctl status iotedge
    ```

8. Jeśli widzisz błędy (kolor tekstu z prefiksem "\[błąd\]") w dziennikach demona Sprawdź stan szczegółowe informacje o błędzie.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Kolejne kroki

Właśnie został ukończony, konfigurowanie maszyny Wirtualnej platformy Azure jako usługi Azure IoT Edge przezroczystej bramy. Rozpoczęliśmy, generując Certyfikaty testowe, które możemy przekazać do usługi Azure Key Vault. Następnie użyliśmy skryptu i szablon usługi Resource Manager do wdrażania maszyn wirtualnych przy użyciu "Ubuntu Server 16.04 LTS i środowisko uruchomieniowe usługi Azure IoT Edge" obrazu z witryny Azure marketplace. Skryptu trwało dodatkowego kroku instalowania interfejsu wiersza polecenia platformy Azure ([zainstalować interfejs wiersza polecenia w usłudze Azure za pomocą narzędzia apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Za pomocą maszyny Wirtualnej w górę i działa firma Microsoft połączone za pośrednictwem protokołu SSH, zalogowali się do platformy Azure, pobierane certyfikaty z usługi Key Vault i wprowadzono kilka aktualizacji w konfiguracji środowiska uruchomieniowego usługi IoT Edge, aktualizując plik config.yaml. Aby uzyskać więcej informacji o korzystaniu z usługi IoT Edge jako bramy, zobacz [jak IoT Edge urządzenia mogą być używane jako brama](iot-edge-as-gateway.md). Aby uzyskać więcej informacji na temat konfigurowania urządzenia usługi IoT Edge rolę przezroczystej bramy, zobacz [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md).

Przejdź do następnego artykułu, aby tworzyć moduły usługi IoT Edge.

> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie niestandardowych modułów usługi IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
