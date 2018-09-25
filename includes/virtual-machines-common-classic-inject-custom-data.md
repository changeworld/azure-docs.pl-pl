


W tym artykule opisano sposób:

* Wstawianie danych do maszyny wirtualnej (VM) platformy Azure, gdy jest aprowizowana.
* Pobrać do systemów Windows i Linux.
* Użyj narzędzia specjalne, które są dostępne w niektórych systemach wykrywania i obsługi danych niestandardowych automatycznie.

> [!NOTE]
> W tym artykule opisano jak niestandardowe dane można wprowadzonym za pomocą maszyny Wirtualnej utworzonej przy użyciu klasycznego modelu wdrażania. Aby zobaczyć, jak używać interfejsu API usługi Azure Resource Management, zobacz [przykładowy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Wstawianie danych niestandardowych do maszyny wirtualnej platformy Azure

[!INCLUDE [classic-cli](contains-classic-cli-content.md)]

Ta funkcja jest obecnie obsługiwany tylko [wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli). W tym miejscu utworzymy `custom-data.txt` plik zawierający dane, następnie wstrzyknąć w do maszyny Wirtualnej podczas inicjowania obsługi. Mimo że można użyć opcji `azure vm create` polecenia w poniższym przykładzie pokazano jedno z podejść podstawowe:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Korzystanie z niestandardowych danych na maszynie wirtualnej
* Jeśli maszyna wirtualna platformy Azure jest maszyny Wirtualnej z systemem Windows, a następnie zapisany danych niestandardowych `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Mimo że pochodzi algorytmem base64 na transfer z komputera lokalnego do nowej maszyny Wirtualnej, jego jest automatycznie zdekodowany i można otworzyć lub od razu użyć.
  
  > [!NOTE]
  > Jeśli plik istnieje, zostanie zastąpiony. Zabezpieczenia w katalogu jest ustawiona na **systemu: Pełna kontrola** i **Administratorzy: Pełna kontrola**.
  > 
  > 
* Maszyna wirtualna platformy Azure w przypadku maszyn wirtualnych opartych na systemie Linux, plik danych niestandardowych znajduje się w jednym z poniżej wymienionych miejsc, w zależności od Twojej dystrybucji. Dane mogą być zakodowane w formacie base64, więc trzeba najpierw dekodowania danych:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Pakiet cloud-init w systemie Azure
W przypadku maszyny Wirtualnej platformy Azure z obrazu systemu Ubuntu lub CoreOS, można użyć funkcji CustomData wysyłać konfiguracji chmury pakietu cloud-init. Lub, jeśli plik danych niestandardowych to skrypt, następnie pakietu cloud-init można uruchomić go.

### <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Images
W przypadku większości obrazów systemu Linux platformy Azure, możesz edytować "/ etc/waagent.conf" Aby skonfigurować dysk tymczasowy zasobów i plik wymiany. Zobacz [przewodnik użytkownika agenta platformy Azure](../articles/virtual-machines/extensions/agent-linux.md) Aby uzyskać więcej informacji.

Jednak na obrazy systemu Ubuntu w chmurze, należy użyć pakietu cloud-init do skonfigurowania zasobu dysku (czyli "tymczasowych" dysk), a zamienić partycji. Zobacz następującą stronę w witrynie wiki Ubuntu, aby uzyskać więcej informacji: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Następne kroki: przy użyciu pakietu cloud-init
Aby uzyskać więcej informacji, zobacz [dokumentacji pakietu cloud-init Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Dodaj rolę — dokumentacja interfejsu API REST zarządzania usługi](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli)

