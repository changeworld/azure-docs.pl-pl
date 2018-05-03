


W tym temacie opisano sposób:

* Wstaw danych do maszyny wirtualnej platformy Azure (VM), gdy jest inicjowana.
* Pobrać do systemów Windows i Linux.
* Użyj narzędzia specjalne, które są dostępne w niektórych systemach do wykrywania i obsługi danych niestandardowych automatycznie.

> [!NOTE]
> W tym artykule opisano sposób niestandardowe dane mogą zostać dodane przy użyciu maszyny Wirtualnej utworzonej z interfejs API zarządzania usługami Azure. Aby zobaczyć sposób użycia interfejsu API zarządzania zasobów Azure, zobacz [szablonu przykład](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Wstrzykiwania niestandardowe dane do maszyny wirtualnej platformy Azure
Ta funkcja jest obecnie obsługiwany tylko w [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli). W tym miejscu utworzymy `custom-data.txt` pliku, który zawiera danych, następnie wstrzyknąć w do maszyny Wirtualnej podczas inicjowania obsługi. Mimo że można użyć opcji `azure vm create` poleceń, jednym z podejść bardzo podstawowe przedstawiono następujące:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Na maszynie wirtualnej za pomocą danych niestandardowych
* Jeśli maszyna wirtualna Azure jest Maszynę wirtualną z systemem Windows, a następnie są zapisywane w pliku danych niestandardowych `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Mimo że był algorytmem base64 na przesyłanie z komputera lokalnego do nowej maszyny Wirtualnej, jest automatycznie dekodowane i można otworzyć lub od razu użyć.
  
  > [!NOTE]
  > Jeśli plik istnieje, zostanie zastąpiony. Ustawiono zabezpieczeń w katalogu **systemu: Pełna kontrola** i **Administratorzy: Pełna kontrola**.
  > 
  > 
* Maszyna wirtualna platformy Azure w przypadku maszyny Wirtualnej opartych na systemie Linux, pliku danych niestandardowych zostanie umieszczony w jednym z następujących miejscach, w zależności od Twojego distro. Dane mogą być zakodowane w formacie base64, dlatego trzeba najpierw dekodowania danych:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Inicjowania chmurze na platformie Azure
W przypadku maszyny Wirtualnej Azure z obrazu Ubuntu lub CoreOS, można użyć CustomData do wysyłania konfiguracji chmury do inicjowania chmury. Lub jeśli plik danych niestandardowych jest skryptem, następnie init chmury można po prostu wykonywania.

### <a name="ubuntu-cloud-images"></a>Obrazy Ubuntu chmury
W większości obrazów systemu Linux platformy Azure, możesz edytować "/ etc/waagent.conf" do konfigurowania zasobów dysku i plik wymiany. Zobacz [Podręcznik użytkownika agenta systemu Linux Azure](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać więcej informacji.

Jednak na Ubuntu obrazów chmury, należy użyć init chmury można skonfigurować dysku zasobów (to znaczy "tymczasowych" dysk), a zamienić partycji. Przejrzyj następującą stronę w witrynie typu wiki Ubuntu więcej szczegółów: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Następne kroki: przy użyciu inicjowania chmury
Aby uzyskać więcej informacji, zobacz [init chmury dokumentacji Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Dodaj dokumentacja interfejsu API REST zarządzania usługi roli](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli)

