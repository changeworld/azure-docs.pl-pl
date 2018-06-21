---
title: Uruchamianie niestandardowych skryptów na maszynach wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyny Wirtualnej systemu Linux przy użyciu v2 niestandardowe rozszerzenie skryptu
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: e614c78ca4e8ed7afbce0d3f2423ce137c5225b5
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294979"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Użyj Azure niestandardowy skrypt rozszerzenie wersji 2 z maszyn wirtualnych systemu Linux
Rozszerzenia niestandardowego skryptu w wersji 2 pobiera i uruchamia skrypty na maszynach wirtualnych Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacja oprogramowania lub innych zadań konfiguracji i zarządzania nimi. Skrypty można pobrać z magazynu Azure lub innej dostępnej lokalizacji internet lub zapewniają je do środowiska wykonawczego rozszerzenia. 

Niestandardowe rozszerzenie skryptu jest zintegrowany z szablonów usługi Azure Resource Manager. Można również uruchomić przy użyciu wiersza polecenia platformy Azure, programu PowerShell, portalu Azure lub interfejsu API REST dla maszyn wirtualnych Azure.

Ten artykuł zawiera szczegóły dotyczące używania niestandardowe rozszerzenie skryptu z wiersza polecenia platformy Azure i sposobu uruchamiania rozszerzenia za pomocą szablonu usługi Azure Resource Manager. Ten artykuł zawiera również kroki rozwiązywania problemów dla systemu Linux.


Istnieją dwa rozszerzenia skryptów niestandardowych Linux:
* Wersja 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* W wersji 2 - Microsoft.Azure.Extensions.CustomScript

Przełącz nowych i istniejących wdrożeń, aby zamiast niej używać nowej wersji 2. Nowa wersja ma być zastępczy dzięki wsuwanej konstrukcji. W związku z tym migracji jest tak proste, jak zmiana nazwy i wersji, nie musisz zmienić konfigurację rozszerzenia.


### <a name="operating-system"></a>System operacyjny

Niestandardowe rozszerzenie skryptu dla systemu Linux będzie uruchamiany na rozszerzenia rozszerzenie obsługiwane systemem operacyjnym, aby uzyskać więcej informacji, zobacz ten [artykułu](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Lokalizacja skryptu

Można użyć rozszerzenia do użycia poświadczeń magazynu obiektów Blob platformy Azure na dostęp do magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacji skryptu można dowolnego miejsca, tak długo, jak długo maszyny Wirtualnej można kierować do tego punktu końcowego, takie jak GitHub, serwer wewnętrzny plików itp.

### <a name="internet-connectivity"></a>Połączenie z Internetem
Jeśli potrzebujesz można pobrać skryptu zewnętrznie, takich jak usługi GitHub lub usługi Azure Storage, następnie dodatkowe zapory i sieci grupa zabezpieczeń porty muszą być otwarte. Na przykład jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp przy użyciu tagów usługi NSG Azure dla [magazynu](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, a następnie nadal potrzebujesz dodatkowych zapory i sieci zabezpieczeń grupy porty muszą być otwarte.

### <a name="tips-and-tricks"></a>Porady i wskazówki
* Najwyższy współczynnik awaryjności dla tego rozszerzenia jest z powodu błędów składni w skrypcie testu, którego skrypt jest uruchamiany bez błędów, i również umieścić w dodatkowe opcje rejestrowania do skryptu, aby ułatwić znajdowanie, których nie powiodło się.
* Napisać skrypty, które są idempotentności, więc jeśli uzyskać Uruchom ponownie więcej niż raz przypadkowo, nie spowoduje zmiany w systemie.
* Upewnij się, że skrypty nie wymagają danych wejściowych użytkownika podczas uruchamiania.
* Występuje w ciągu 90 minut dozwolone w przypadku skryptu uruchamianego, niczego dłużej spowoduje postanowienia rozszerzenia nie powiodło się.
* Nie należy umieszczać wewnątrz skrypt jest uruchamiany ponownie, powoduje to problemy z innymi rozszerzeniami, które są instalowane i post ponowny rozruch, rozszerzenia nie będzie kontynuowana po ponownym uruchomieniu. 
* Jeśli skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i uruchamiać skrypty itp. Należy zaplanować ponowny rozruch przy użyciu zadania Cron lub za pomocą narzędzi takich jak DSC lub Chef, Puppet rozszerzenia.
* Rozszerzenie zostanie uruchomiony tylko wtedy skrypt raz, jeśli chcesz uruchomić skrypt w każdym rozruchu, a następnie można użyć [obrazu init chmury](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) i użyj [skryptów na rozruch](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modułu. Alternatywnie można użyć skryptu, aby utworzyć jednostkę usługi Systemd.
* Jeśli chcesz zaplanować skrypt będzie uruchamiany, należy użyć rozszerzenia można utworzyć zadania usługi Cron. 
* Podczas wykonywania skryptu, będą widoczne są tylko "Przechodzenie" stan rozszerzenia z portalu Azure lub interfejsu wiersza polecenia. Chcąc częstsze aktualizacje stanu uruchamianie skryptu, należy utworzyć własne rozwiązania.
* Niestandardowe rozszerzenie skryptu nie obsługuje serwerów proxy, jednak można korzystać narzędzie transferu plików, które obsługuje serwery proxy w skrypcie, takich jak *Curl*. 
* Należy pamiętać o z systemem innym niż domyślny katalog lokalizacje, które mogą polegać skryptu lub polecenia, mieć logiki do obsługi to.



## <a name="extension-schema"></a>Schemat rozszerzenia

Niestandardowe rozszerzenie skryptu konfiguracji określa lokalizację i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub określ go w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfigurację, która zostaje zaszyfrowany i odszyfrowane tylko na maszynie wirtualnej. Chronione konfiguracja jest użyteczna, jeśli wykonanie polecenia obejmuje tajemnice, takie jak hasła.

Te elementy powinny traktowane jako poufne dane i określony w konfiguracji chronionych ustawień rozszerzenia. Dane Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest szyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

```json
{
  "name": "config-app",
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":"123456789",          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.Compute.Extensions | ciąg |
| type | CustomScript | ciąg |
| typeHandlerVersion | 2.0 | int |
| fileUris (np.) | https://github.com/MyProject/Archive/MyPythonScript.py | tablica |
| commandToExecute (np.) | Python MyPythonScript.py < Moje param1 > | ciąg |
| Skrypt | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | ciąg |
| skipDos2Unix (np.) | false | wartość logiczna |
| Znacznik czasu (np.) | 123456789 | 32-bitowa liczba całkowita |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | ciąg |

### <a name="property-value-details"></a>Szczegóły wartości właściwości
* `skipDos2Unix`: (opcjonalne, wartość logiczna) Pomiń konwersji dos2unix skryptu lub pliku skryptu na podstawie adresów URL.
* `timestamp` Użyj (opcjonalne, 32-bitowa liczba całkowita), w tym polu tylko do wyzwolenia ponownie uruchomić skrypt, zmieniając wartość tego pola.  Dowolną liczbą całkowitą jest dopuszczalne; tylko musi być inna niż poprzednia wartość.
 * `commandToExecute`: (**wymagane** Jeśli skrypt nie został ustawiony, ciąg) wykonywania skryptu punktu wejścia. Zamiast tego użyj tego pola, jeśli polecenie zawiera klucze tajne, takie jak hasła.
* `script`: (**wymagane** Jeśli commandToExecute nie został ustawiony, ciąg) kodowanie base64 (i opcjonalnie gzip'ed) skrypt wykonywany przez element/bin/ud.
* `fileUris`: (opcjonalne, tablicy ciągów) w adresach URL dla pliki do pobrania.
* `storageAccountName`: (opcjonalne, ciąg) nazwa konta magazynu. Jeśli określisz poświadczeń magazynu wszystkich `fileUris` muszą być adresami URL dla obiektów blob Azure.
* `storageAccountKey`: (opcjonalne, ciąg) klucz dostępu konta magazynu


Można ustawić następujące wartości w ustawieniach publiczne lub chronione, rozszerzenie spowoduje odrzucenie żadnej konfiguracji, w którym poniższe wartości są ustawiane w ustawieniach zarówno publiczne i chronione.
* `commandToExecute`
* `script`
* `fileUris`

Przy użyciu ustawienia publicznego, może być przydatne dla debugowania, ale zdecydowanie zalecane jest użycie chronionych ustawień.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, w którym skrypt zostanie wykonany.  Ustawienia chronione są szyfrowane za pomocą klucza znane tylko do platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, tj. Jeśli zostały zaszyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości są przechowywane na maszynie Wirtualnej i używany do odszyfrowywania ustawienia (Jeśli to konieczne) w czasie wykonywania.

#### <a name="property-skipdos2unix"></a>Właściwości: skipDos2Unix

Wartość domyślna to false, co oznacza, że konwersji dos2unix **jest** wykonywane.

Poprzedniej wersji CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, może automatycznie przekonwertować DOS plików do plików systemu UNIX przez tłumaczenie `\r\n` do `\n`. Tłumaczenie nadal istnieje i jest domyślnie włączone. Ta konwersja jest stosowany do wszystkich plików pobranych z fileUris lub ustawienia skryptu według dowolnej z następujących kryteriów.

* Jeśli rozszerzenie jest jednym z `.sh`, `.txt`, `.py`, lub `.pl` zostanie przekonwertowany. Ustawienia skryptu zawsze będzie odpowiadać te kryteria, ponieważ zakłada, że skrypt wykonywany z /bin/sh i jest zapisywany jako script.sh na maszynie Wirtualnej.
* Jeśli plik, który rozpoczyna się od `#!`.

Konwersja dos2unix można pominięte przez ustawienie skipDos2Unix na wartość true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Właściwości: skryptu

CustomScript obsługuje wykonywanie skryptu zdefiniowane przez użytkownika. Ustawienia skryptu, aby połączyć commandToExecute i fileUris w jednym ustawienie. Nie trzeba skonfigurować plik do pobrania z magazynu Azure lub GitHub gist, może po prostu zakodować skrypt jako ustawienie. Skrypt może służyć do zastąpionego commandToExecute i fileUris.

Skrypt **musi** można algorytmem base64.  Skrypt może **opcjonalnie** można gzip'ed. Ustawienie skryptu może być używane w ustawieniach publiczne lub chronione. Maksymalny rozmiar danych parametru skryptu to 256 KB. Jeśli skrypt przekroczy ten rozmiar nie można wykonać.

Na przykład poniższy skrypt zapisane w pliku /script.sh/ podane.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Czy można konstruować prawidłowe ustawienie skryptu CustomScript wykonując dane wyjściowe polecenia następujące.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skrypt można opcjonalnie gzip'ed, aby jeszcze bardziej ograniczyć rozmiar (w większości przypadków). (CustomScript auto wykrywa używanie kompresji gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript używa następującego algorytmu do uruchomienia skryptu.

 1. potwierdzenia, że długość wartości skryptu nie przekracza 256 KB.
 1. wartość skryptu dekodowania Base64
 1. _Próba_ do gunzip base64 zdekodować wartości
 1. zapisać wartości dekodowane (i opcjonalnie zdekompresowanych) na dysku (/ var/lib/waagent/custom-script/#/script.sh)
 1. wykonanie skryptu, za pomocą /var/lib/waagent/custom-script/#/script.sh _ / bin/sh - c.


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Schematu JSON szczegółowo opisane w poprzedniej sekcji można w szablonie usługi Azure Resource Manager niestandardowe rozszerzenie skryptu są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który zawiera niestandardowe rozszerzenie skryptu można znaleźć w tym miejscu [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/MyPythonScript.py"
      ]  
    }
  }
}
```

>[!NOTE]
>Te nazwy właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas korzystania z wiersza polecenia platformy Azure do uruchomienia niestandardowe rozszerzenie skryptu, Utwórz plik konfiguracji lub plików. Co najmniej musi mieć "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg w formacie JSON. Dzięki temu konfiguracji należy określić podczas wykonywania i bez pliku osobną konfiguracją.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-script-file"></a>Publiczna Konfiguracja z pliku skryptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Publiczna Konfiguracja z żadnego pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Pliki konfiguracji publiczne i chronione

Aby określić plik skryptu identyfikatora URI jest używany plik publicznej konfiguracji. Aby określić polecenie do uruchomienia jest używany plik konfiguracji chronionych.

Plik konfiguracji publicznego:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Plik chroniony konfiguracji:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Polecenia interfejsu wiersza polecenia platformy Azure:

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
Po uruchomieniu niestandardowe rozszerzenie skryptu skrypt jest tworzony lub pobrany do katalogu, który jest podobny do poniższego przykładu. Dane wyjściowe polecenia jest także zapisane w tym katalogu w `stdout` i `stderr` plików.

```bash
/var/lib/waagent/custom-script/download/0/
```

Rozwiązywanie problemów, najpierw, sprawdź dziennik agenta systemu Linux, upewnij się, rozszerzenie zostanie wykonane, sprawdź:

```bash
/var/log/waagent.log 
```

Należy szukać wykonywania rozszerzenia, wygląda mniej więcej tak:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Niektóre informacje, które należy zwrócić uwagę:
1. Włącz to, gdy polecenie zacznie działać.
2. Pobieranie odnosi się do pobierania pakietu rozszerzenia CustomScript z platformy Azure, nie plików skryptów określony w fileUris.


Rozszerzenie skryptu Azure tworzy dziennika, który można znaleźć tutaj:

```bash
/var/log/azure/custom-script/handler.log
```

Należy szukać wykonywania induvidual, wygląda mniej więcej tak:
```text
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
W tym miejscu można wyświetlić:
* Uruchamianie polecenia Enable jest ten dziennik
* Ustawienia przekazane do rozszerzenia
* Rozszerzenie pobierania pliku i jego wynikach.
* Uruchamianego polecenia i wynik.

Można również pobrać stanu wykonywania niestandardowe rozszerzenie skryptu przy użyciu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco następujący tekst:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Kolejne kroki
Aby wyświetlić kod, bieżących problemach i wersji, zobacz [repozytorium niestandardowego skryptu — rozszerzenie linux](https://github.com/Azure/custom-script-extension-linux).

