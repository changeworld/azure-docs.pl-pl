---
title: Omówienie chmury inicjowania obsługi maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji init chmurze na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: fbb6fc15663570d9b9470fc7d4de3c8eb30de9d9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
ms.locfileid: "29763149"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Obsługa inicjowania chmury maszyn wirtualnych na platformie Azure
W tym artykule opisano obsługę, czy istnieje dla [init chmury](https://cloudinit.readthedocs.io) do skonfigurowania maszyny wirtualnej (VM) lub maszyny wirtualnej zestawach skali (VMSS) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te init chmury są uruchamiane po pierwszym uruchomieniu komputera po zasoby zostały udostępnione przez platformę Azure.  

## <a name="cloud-init-overview"></a>Omówienie pakietu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Ponieważ init chmury jest wywoływana podczas początkowego procesu rozruchu, nie są żadne dodatkowe kroki lub agentów wymaganych do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat sposobu poprawnie sformatowana Twojej `#cloud-config` plików, zobacz [witryna dokumentacji usługi chmury init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` pliki są plikami tekstowymi zakodowane w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

 Obecnie pracujemy z partnerami potwierdzony distro systemu Linux w celu dostępnych obrazów włączone inicjowania chmury w portalu Azure marketplace. Te obrazy spowoduje, że wdrożeń chmury init i konfiguracje współpracuje z maszynami wirtualnymi i zestawy skalowania maszyny Wirtualnej (VMSS). W poniższej tabeli przedstawiono bieżącej dostępności obrazów init chmury, włączone na platformie Azure:

| Wydawca | Oferta | SKU | Wersja | gotowe init chmury
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |najnowsza |tak | 
|Canonical |UbuntuServer |14.04.5-LTS |najnowsza |tak |
|CoreOS |CoreOS |Stable |najnowsza |tak |
|OpenLogic |CentOS |7-CI |najnowsza |wersja zapoznawcza |
|RedHat |RHEL |7-RAW-CI |najnowsza |wersja zapoznawcza |

Stos Azure nie obsługuje obecnie inicjowania obsługi administracyjnej RHEL 7.4 i CentOS 7.4 przy użyciu inicjowania chmury.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaka jest różnica między init chmury i agenta systemu Linux (WALA)?
WALA jest używany do obsługi administracyjnej i skonfigurować maszyny wirtualne i obsługi rozszerzeń Azure agenta specyficzne dla platformy Azure. Firma Microsoft są udoskonalanie zadanie konfigurowania maszyn wirtualnych do korzystania z chmury init zamiast agenta systemu Linux w celu umożliwienia istniejących klientów init chmury za pomocą ich bieżący skryptów init chmury.  Jeśli dokonano już inwestycji w chmurze init skrypty do konfigurowania serwerów z systemem Linux są **są wymagane nie dodatkowe ustawienia** je włączyć. 

Jeśli nie ma interfejsu wiersza polecenia Azure `--custom-data` przełącznik na inicjowanie obsługi administracyjnej czas WALA przyjmuje minimalnego obsługi parametrów wymaganych do obsługi administracyjnej maszyny Wirtualnej i ukończyć wdrażanie przy użyciu ustawień domyślnych maszyny Wirtualnej.  Jeśli odwołanie init chmury `--custom-data` przełącznika, niezależnie od znajduje się w danych niestandardowych (poszczególnych ustawień lub skryptu pełne) zastępuje wartości domyślne WALA. 

Konfiguracje WALA maszyn wirtualnych są ograniczone na czas do pracy z maksymalną obsługi czasu maszyny Wirtualnej.  Konfiguracje init chmury stosowane do maszyn wirtualnych bez ograniczeń czasowych i nie powoduje wdrożenia niepowodzenie przez przekroczeniem limitu czasu. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Wdrażanie inicjowania chmury włączone maszyny wirtualnej
Wdrażanie włączone inicjowania chmury maszyny wirtualnej jest tak proste, jak odwołujące się do dystrybucji chmury inicjowaniem włączona podczas wdrażania.  Maintainers dystrybucji systemu Linux musiał wybrać włączyć i integrowanie init chmury podstawowej Azure obrazów opublikowanych. Po potwierdzeniu chmury inicjowaniem włączone jest obraz, który chcesz wdrożyć, można użyć wiersza polecenia platformy Azure do wdrożenia obrazu. 

Pierwszym etapem wdrożenia tego obrazu jest utworzenie grupy zasobów z [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Następnym krokiem jest utworzenie pliku w bieżącym powłoki, o nazwie *init.txt chmury* i wklej następującą konfigurację. Na przykład można utworzyć pliku w powłoce chmury nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Naciśnij klawisz `ctrl-X` aby wyjść z pliku, wpisz `y` zapisać plik i naciśnij przycisk `enter` aby potwierdzić nazwę pliku na wyjściu.

Ostatnim krokiem jest utworzenie maszyny Wirtualnej z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenia. 

Poniższy przykład tworzy Maszynę wirtualną o nazwie *centos74* i tworzy kluczy SSH, jeśli nie już istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy Maszynę wirtualną o nazwie *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po utworzeniu maszyny Wirtualnej, interfejsu wiersza polecenia Azure zawiera informacje specyficzne dla danego wdrożenia. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.  Trwa trochę czasu, można utworzyć maszyny Wirtualnej, pakietów do zainstalowania i aplikacji, aby rozpocząć. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Można SSH do maszyny Wirtualnej i sprawdź dzienniki z inicjowaniem chmury przy użyciu kroków opisanych w części Rozwiązywanie problemów. 

## <a name="troubleshooting-cloud-init"></a>Rozwiązywanie problemów z inicjowaniem chmury
Po zainicjowano maszyny Wirtualnej, init chmurze zostanie uruchomiony za pośrednictwem wszystkich modułów i skryptów są zdefiniowane w `--custom-data` w celu skonfigurowania maszyny Wirtualnej.  Należy sprawdzić poprawność błędów lub pominięć z konfiguracji, należy wyszukać nazwę modułu (`disk_setup` lub `runcmd` na przykład) w dzienniku chmury init - znajduje się w **/var/log/cloud-init.log**.

> [!NOTE]
> Nie każdy błąd modułu powoduje krytyczny init chmury ogólny błąd konfiguracji. Na przykład za pomocą `runcmd` moduł, jeśli skrypt zakończy się niepowodzeniem, init chmury nadal zgłasza inicjowania obsługi administracyjnej zakończyło się pomyślnie, ponieważ moduł runcmd wykonany.

Więcej informacji o chmurze init rejestrowania, zapoznaj się [dokumentacji init chmury](http://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Kolejne kroki
Przykłady init chmury zmian konfiguracji można znaleźć w następujących dokumentach:
 
- [Dodaj dodatkowe użytkownika w systemie Linux na maszynie Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety po pierwszym uruchomieniu komputera](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i wstrzyknąć kluczy](tutorial-automate-vm-deployment.md)
