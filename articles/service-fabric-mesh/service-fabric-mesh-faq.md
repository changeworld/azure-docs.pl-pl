---
title: Często zadawane pytania dotyczące usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o najczęściej zadawanych pytań i odpowiedzi dla usługi Azure Service Fabric siatki.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: b32af29a123ce4d070e1bb68b5a43ba6d0d2c5e1
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218478"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Często zadawane pytania usługi Service Fabric siatki
Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. W tym artykule znajdują się odpowiedzi na często zadawane pytania.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak zgłosić problem lub Zadaj pytanie?

Zadawaj pytania, uzyskuj odpowiedzi od inżynierów firmy Microsoft i zgłaszania problemów w [repozytorium GitHub service-fabric siatki preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Limit przydziału i kosztów

**Jaki jest koszt uczestniczenia w wersji zapoznawczej?**

Obecnie nie są żadne opłaty za wdrażanie aplikacji lub kontenerów do siatki w wersji zapoznawczej. Jednak firma Microsoft zachęca do usuwania zasobów, wdrażania i nie pozostawić je systemem aktywnie podczas testów.

**Czy istnieje limit przydziału liczby rdzeni i pamięci RAM?**

Tak, limity przydziału dla każdej subskrypcji są ustawione w następujący sposób:

- Liczba aplikacji — 5 
- Liczba rdzeni na aplikacji – 12 
- Całkowita ilość pamięci RAM na aplikację — 48 GB 
- Punkty końcowe sieci i transferu danych przychodzących — 5  
- Woluminy systemu Azure, które można dołączyć — 10 
- Liczba replik usługi — 3 
- Największy kontenera, które można wdrożyć jest ograniczony do 4 rdzeni, 16 GB pamięci RAM.
- Częściowe rdzeni można przydzielić do kontenerów w przyrostach o rozmiarze 0,5 rdzenia maksymalnie 6 rdzeni.

**Jak długo można pozostawić Moja aplikacja wdrożona?**

Firma Microsoft ma obecnie ograniczony okres istnienia aplikacji do dwóch dni. Jest to w celu optymalnego wykorzystania wolnych rdzeni przydzielonych do wersji zapoznawczej. W rezultacie tylko mogła działać danego wdrożenia stale w ciągu 48 godzin, po tym czasie zostaną usunięte przez system. Jeśli tak się dzieje się tak zdarzyć, można sprawdzić, czy system zamknij go, uruchamiając `az mesh app show` polecenia wiersza polecenia platformy Azure i sprawdzanie zwraca `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Na przykład: 

```cli
chackdan@Azure:~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

## <a name="supported-container-os-images"></a>Obrazy obsługiwane kontenerów systemu operacyjnego
Następujące obrazy systemu operacyjnego kontener może służyć w przypadku wdrażania usług.

- Windows — windowsservercore i nanoserver
    - Windows Server 2016
    - Windows Server w wersji 1709
- Linux
    - Nie znane ograniczenia

## <a name="features-gaps-and-known-issues"></a>Funkcje luki i znane problemy

**Po wdrożeniu aplikacji, zasobów sieciowych skojarzonych z nim nie wydaje się adres IP**

Istnieje znany problem już dziś przy użyciu adresu IP, pojawi się z opóźnieniem. Sprawdź stan zasobów sieciowych w ciągu kilku minut, aby wyświetlić skojarzony adres IP.

**Moja aplikacja nie powiodło się uzyskać dostępu do zasobu odpowiednie sieci/woluminu**

W modelu aplikacji należy użyć pełny identyfikator zasobu sieci i woluminów, aby można było uzyskać dostęp do skojarzonego zasobu. Poniżej przedstawiono, jak to wygląda w przykładzie Szybki Start:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Nie widzę bieżący model aplikacji obsługuje możliwość szyfrowania Moje wpisy tajne**

Tak, szyfrowanie kluczy tajnych nie jest obsługiwana w prywatnej wersji zapoznawczej. 

**DNS nie działa tak samo w Mój klaster projektowy usługi Service Fabric i siatka**

Jest to znany problem, gdzie trzeba będzie odwoływać się do usług, inaczej w przypadku lokalnego klastra projektowego i Azure siatki. W przypadku lokalnego klastra projektowego za pomocą {usługi serviceName}. {applicationName}. W Azure Service Fabric siatki za pomocą {usługi servicename}. Azure siatki aktualnie nie obsługuje rozpoznawanie nazw dns w aplikacjach.

Dla innych znanych problemach DNS z systemem klaster projektowy usługi Service Fabric w systemie Windows 10, zobacz tutaj: [kontenery Windows debugowania](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Pobierz ten błąd, podczas korzystania z modułu interfejsu wiersza polecenia ImportError: nie można zaimportować sdk_no_wait"name"**

Jeśli używasz starszej wersji interfejsu wiersza polecenia niż 2.0.30 może wystąpić ten błąd-

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**Błąd niezgodności dystrybucji nazwy podczas instalowania pakietu rozszerzenia interfejsu wiersza polecenia**

Oznacza to, czy nie zainstalowano rozszerzenia. Nadal można używać poleceń interfejsu wiersza polecenia bez problemu.

**Czy mogę skalować, widzę czy wszystkie moje kontenery dotyczą, tym Moje uruchomione**

Jest to błąd i powinny być ustalone w następnym odświeżeniu środowiska uruchomieniowego.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Przegląd](service-fabric-mesh-overview.md).
