---
title: Uruchamianie skryptów niestandardowych na maszynach wirtualnych z systemem Linux na platformie Azure
description: Automatyzuj zadania konfiguracji maszyny Wirtualnej systemu Linux przy użyciu niestandardowego rozszerzenia skryptu w wersji 2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: b75b232c048a1ea49256b12ce1b65c4bd87a1cf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535446"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Używanie rozszerzenia niestandardowego skryptu platformy Azure w wersji 2 z maszynami wirtualnymi z systemem Linux
Rozszerzenie niestandardowego skryptu w wersji 2 pobiera i uruchamia skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innego zadania konfiguracji/zarządzania. Skrypty można pobrać z usługi Azure Storage lub innej dostępnej lokalizacji internetowej lub udostępnić je do środowiska wykonawczego rozszerzenia. 

Rozszerzenie skryptów niestandardowych integruje się z szablonami usługi Azure Resource Manager. Można go również uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub interfejsu API REST maszyn wirtualnych platformy Azure.

W tym artykule opisano, jak używać rozszerzenia niestandardowego skryptu z interfejsu wiersza polecenia platformy Azure i jak uruchomić rozszerzenie przy użyciu szablonu usługi Azure Resource Manager. W tym artykule przedstawiono również kroki rozwiązywania problemów dla systemów Linux.


Istnieją dwa rozszerzenia skryptów niestandardowych systemu Linux:
* Wersja 1 — Microsoft.OSTCExtensions.CustomScriptForLinux
* Wersja 2 — Microsoft.Azure.Extensions.CustomScript

Przełącz nowe i istniejące wdrożenia, aby zamiast tego użyć nowej wersji 2. Nowa wersja jest przeznaczona do użycia bezpośrednio w miejsce starszej. Dlatego migracja jest tak łatwa jak zmiana nazwy i wersji — nie trzeba zmieniać konfiguracji rozszerzenia.


### <a name="operating-system"></a>System operacyjny

Niestandardowe rozszerzenie skryptu dla systemu Linux będzie działać na rozszerzeń obsługiwanych, aby uzyskać więcej informacji, zobacz ten [artykuł](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenia można użyć poświadczeń magazynu obiektów Blob platformy Azure, aby uzyskać dostęp do magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacja skryptu może być dowolna, o ile maszyna wirtualna może kierować do tego punktu końcowego, takiego jak GitHub, wewnętrzny serwer plików itp.

### <a name="internet-connectivity"></a>Łączność z Internetem
Jeśli chcesz pobrać skrypt zewnętrznie, takich jak GitHub lub Usługi Azure Storage, a następnie dodatkowe porty zapory/sieciowej grupy zabezpieczeń muszą zostać otwarte. Na przykład, jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi Azure NSG dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, nadal może być konieczne otwarcie dodatkowych portów zapory/sieciowej grupy zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki
* Najwyższy współczynnik błędów dla tego rozszerzenia jest spowodowany błędami składni w skrypcie. Sprawdź, czy skrypt działa bezbłędnie, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić wyszukiwanie miejsca, w którym wystąpił błąd.
* Pisz skrypty idempotentne, aby w razie ich ponownego uruchomienia przez przypadek więcej niż jeden raz nie nastąpiły zmiany systemu.
* Upewnij się, że skrypty nie wymagają danych wejściowych użytkownika podczas ich uruchamiania.
* Jest 90 minut dozwolone dla skryptu do uruchomienia, nic dłużej spowoduje nieuwzwolenie rozszerzenia.
* Nie umieszczaj ponownie uruchamianych ponownie wewnątrz skryptu, spowoduje to problemy z innymi rozszerzeniami, które są instalowane, a po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowane po ponownym uruchomieniu. 
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie, a następnie zainstalować aplikacje i uruchomić skrypty itp. Należy zaplanować ponowne uruchomienie przy użyciu zadania Cron lub za pomocą narzędzi, takich jak DSC lub Chef, rozszerzenia lalek.
* Rozszerzenie uruchomi skrypt tylko raz, jeśli chcesz uruchomić skrypt przy każdym rozruchu, możesz użyć [obrazu init w chmurze](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) i użyć modułu [Skrypty na rozruch.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Alternatywnie można użyć skryptu do utworzenia jednostki serwisowej SystemD.
* Jeśli chcesz zaplanować, kiedy skrypt zostanie uruchomiony, należy użyć rozszerzenia do utworzenia zadania Cron. 
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli chcesz częstsze aktualizacje stanu uruchomionego skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie Skrypt niestandardowy nie obsługuje natywnie serwerów proxy, jednak można użyć narzędzia do przesyłania plików, które obsługuje serwery proxy w skrypcie, takie jak *Curl*. 
* Należy pamiętać o nie domyślnych lokalizacji katalogów, które skrypty lub polecenia mogą polegać na, mają logikę do obsługi tego.
*  Podczas wdrażania skryptu niestandardowego do produkcyjnych wystąpień VMSS zaleca się wdrożenie za pomocą szablonu json i przechowywania konta magazynu skryptu, gdzie masz kontrolę nad tokenem sygnatury dostępu Współdzielonego. 


## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja niestandardowego rozszerzenia skryptu określa takie elementy, jak lokalizacja skryptu i polecenie do uruchomienia. Tę konfigurację można przechowywać w plikach konfiguracyjnych, określić ją w wierszu polecenia lub określić w szablonie usługi Azure Resource Manager. 

Dane poufne można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywać tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło.

Te elementy powinny być traktowane jako poufne dane i określone w konfiguracji ustawień chronionych rozszerzeniami. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> ManagedIdentity Właściwość **nie może** być używana w połączeniu z storageAccountName lub storageAccountKey właściwości

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| wydawca | Microsoft.Compute.Extensions | ciąg |
| type | Kod niestandardowy | ciąg |
| typHandlerVersion | 2.1 | int |
| fileUris (np. | https://github.com/MyProject/Archive/MyPythonScript.py | tablica |
| commandToExecute (np.) | python MyPythonScript.py \<my-param1> | ciąg |
| skrypt | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | ciąg |
| skipDos2Unix (np. | false | wartość logiczna |
| sygnatura czasowa (np. | 123456789 | 32-bitowa całkowitej liczby |
| storageAccountName (np. | przykłady | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |
| zarządzaneTożerność (np. | { } lub { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } lub { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json obiekt |

### <a name="property-value-details"></a>Szczegóły wartości właściwości
* `apiVersion`: Najbardziej aktualne apiVersion można znaleźć przy użyciu [Eksploratora zasobów](https://resources.azure.com/) lub z platformy Azure CLI za pomocą następującego polecenia`az provider list -o json`
* `skipDos2Unix`: (opcjonalnie, logiczno) pomijaj konwersję dos2unix adresów URL lub skryptów opartych na skryptach.
* `timestamp`(opcjonalna 32-bitowa liczba całkowita) służy do wyzwalania ponownego uruchomienia skryptu przez zmianę wartości tego pola.  Każda wartość całkowita jest dopuszczalna; musi być tylko inna niż poprzednia wartość.
* `commandToExecute`: (**wymagane,** jeśli skrypt nie jest ustawiony, ciąg) skrypt punktu wejścia do wykonania. Tego pola należy użyć, jeśli polecenie zawiera wpisy tajne, takie jak hasła.
* `script`: (**wymagane,** jeśli commandToExecute nie ustawiono, ciąg)a base64 zakodowany (i opcjonalnie gzip'ed) skrypt wykonywany przez /bin/sh.
* `fileUris`: (opcjonalnie, tablica ciągów) adresy URL dla plików do pobrania.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. Jeśli określisz poświadczenia magazynu, wszystkie `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu
* `managedIdentity`: (opcjonalnie, obiekt json) [zarządzana tożsamość](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do pobierania plików
  * `clientId`: (opcjonalnie, ciąg) identyfikator klienta tożsamości zarządzanej
  * `objectId`: (opcjonalnie, ciąg) identyfikator obiektu tożsamości zarządzanej


Następujące wartości można ustawić w ustawieniach publicznych lub chronionych, rozszerzenie odrzuci dowolną konfigurację, w której poniższe wartości są ustawione w ustawieniach publicznych i chronionych.
* `commandToExecute`
* `script`
* `fileUris`

Za pomocą ustawień publicznych może przydatne do debugowania, ale zdecydowanie zaleca się, aby użyć ustawień chronionych.

Ustawienia publiczne są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdzie skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie Wirtualnej w miarę ich wysyłania, czyli jeśli ustawienia zostały zaszyfrowane, są zapisywane zaszyfrowane na maszynie wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (jeśli to konieczne) w czasie wykonywania.

#### <a name="property-skipdos2unix"></a>Właściwość: skipDos2Unix

Wartość domyślna to false, co oznacza, że wykonywana **jest** konwersja dos2unix.

Poprzednia wersja języka CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, automatycznie konwertuje pliki DOS na pliki UNIX, tłumacząc `\r\n` na `\n`program . To tłumaczenie nadal istnieje i jest domyślnie włączone. Ta konwersja jest stosowana do wszystkich plików pobranych z fileUris lub ustawienia skryptu na podstawie dowolnego z następujących kryteriów.

* Jeśli rozszerzenie jest `.sh`jednym `.txt` `.py`z `.pl` , , lub zostanie przekonwertowane. Ustawienie skryptu zawsze będzie zgodne z tymi kryteriami, ponieważ zakłada się, że jest to skrypt wykonywany z /bin/sh i jest zapisywany jako script.sh na maszynie wirtualnej.
* Jeśli plik zaczyna `#!`się od pliku .

Konwersję dos2unix można pominąć, ustawiając skipDos2Unix na true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Właściwość: skrypt

Kod CustomScript obsługuje wykonywanie skryptu zdefiniowanego przez użytkownika. Ustawienia skryptu, aby połączyć commandToExecute i fileUris w jednym ustawieniu. Zamiast konieczności konfigurowania pliku do pobrania z usługi Azure Storage lub GitHub, można po prostu zakodować skrypt jako ustawienie. Skrypt może służyć do zastąpienia commandToExecute i fileUris.

Skrypt **musi** być zakodowany base64.  Skrypt może być **opcjonalnie** gzip'ed. Ustawienie skryptu może być używane w ustawieniach publicznych lub chronionych. Maksymalny rozmiar danych parametru skryptu wynosi 256 KB. Jeśli skrypt przekracza ten rozmiar nie zostanie wykonany.

Na przykład, biorąc pod uwagę następujący skrypt zapisany w pliku /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Prawidłowe ustawienie skryptu Języka CustomScript zostanie skonstruowane przez odebranie danych wyjściowych następującego polecenia.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skrypt może być opcjonalnie gzip'ed do dalszego zmniejszenia rozmiaru (w większości przypadków). (CustomScript automatycznie wykrywa użycie kompresji gzip).

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript używa następującego algorytmu do wykonania skryptu.

 1. potwierdzić długość wartości skryptu nie przekracza 256 KB.
 1. base64 dekoduje wartość skryptu
 1. _próba_ gunzip base64 zdekodowany wartość
 1. napisać zdekodowaną (i opcjonalnie zdekompresowaną) wartość na dysku (/var/lib/waagent/custom-script/#/script.sh)
 1. wykonywanie skryptu przy użyciu _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Właściwość: managedIdentity

Kod CustomScript (wersja 2.1) obsługuje [tożsamość zarządzaną](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) do pobierania plików z adresów URL podanych w ustawieniu "fileUris". Umożliwia kod customscript dostęp do prywatnych obiektów blob usługi Azure Storage lub kontenerów bez konieczności przekazywania przez użytkownika wpisów tajnych, takich jak tokeny sygnatury dostępu współdzielonego lub klucze konta magazynu.

Aby korzystać z tej funkcji, użytkownik musi dodać tożsamość [przypisaną do systemu](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) lub tożsamości [przypisanej przez użytkownika](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) do maszyny Wirtualnej lub VMSS, w której ma zostać uruchomiony kod CustomScript, i [udzielić dostępu do tożsamości zarządzanej do kontenera lub obiektu blob usługi Azure Storage](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Aby użyć tożsamości przypisanej do systemu na docelowej maszynie Wirtualnej/VMSS, ustaw pole "zarządzana tożsamość" na pusty obiekt json. 

> Przykład:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Aby użyć tożsamości przypisanej przez użytkownika na docelowej maszynie Wirtualnej/VMSS, należy skonfigurować pole "zarządzana tożsamość" przy użyciu identyfikatora klienta lub identyfikatora obiektu tożsamości zarządzanej.

> Przykłady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> ManagedIdentity Właściwość **nie może** być używana w połączeniu z storageAccountName lub storageAccountKey właściwości

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Schemat JSON wyszczególniony w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do uruchamiania rozszerzenia skryptu niestandardowego podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon zawierający rozszerzenie skryptu niestandardowego można znaleźć tutaj, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>W tych nazwach właściwości rozróżniana jest wielkość liter. Aby uniknąć problemów z wdrażaniem, należy użyć nazw, jak pokazano tutaj.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas korzystania z narzędzia Azure CLI do uruchamiania rozszerzenia skryptu niestandardowego należy utworzyć plik konfiguracyjny lub pliki. Co najmniej musisz mieć "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg sformatowany json. Dzięki temu konfiguracja ma być określona podczas wykonywania i bez oddzielnego pliku konfiguracji.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-script-file"></a>Konfiguracja publiczna z plikiem skryptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Konfiguracja publiczna bez pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Publiczne i chronione pliki konfiguracyjne

Użyj publicznego pliku konfiguracji, aby określić identyfikator URI pliku skryptu. Chroniony plik konfiguracyjny służy do określania polecenia do uruchomienia.

Publiczny plik konfiguracyjny:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chroniony plik konfiguracyjny:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Po uruchomieniu rozszerzenia skryptu niestandardowego skrypt jest tworzony lub pobierany do katalogu podobnego do poniższego przykładu. Dane wyjściowe polecenia są również `stdout` zapisywane w tym katalogu i `stderr` plikach.

```bash
/var/lib/waagent/custom-script/download/0/
```

Aby rozwiązać problem, najpierw sprawdź dziennik agenta systemu Linux, upewnij się, że rozszerzenie jest uruchomiony, sprawdź:

```bash
/var/log/waagent.log 
```

Należy szukać wykonania rozszerzenia, będzie wyglądać mniej więcej tak:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Kilka punktów, na które należy zwrócić uwagę:
1. Włącz jest, gdy polecenie zaczyna działać.
2. Download odnosi się do pobierania pakietu rozszerzenia CustomScript z platformy Azure, a nie plików skryptów określonych w fileUris.


Rozszerzenie skryptu platformy Azure tworzy dziennik, który można znaleźć tutaj:

```bash
/var/log/azure/custom-script/handler.log
```

Należy szukać indywidualnego wykonania, będzie to wyglądać mniej więcej tak:

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

Tutaj możesz zobaczyć:
* Polecenie Włącz, które jest uruchamiane, to ten dziennik
* Ustawienia przekazane do rozszerzenia
* Plik pobierania rozszerzenia i wynik tego.
* Uruchamiane polecenie i wynik.

Można również pobrać stan wykonywania rozszerzenia skryptu niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco:

```output
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Następne kroki
Aby zobaczyć kod, bieżące problemy i wersje, zobacz [niestandardowe script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux).

