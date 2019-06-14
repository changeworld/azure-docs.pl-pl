---
title: Omówienie pakietu cloud-init obsługę maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji pakietu cloud-init w systemie Microsoft Azure
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
ms.openlocfilehash: 377ad49ae3a5ae0f61cd0fd93b68dd817d617397
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050822"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Pakiet cloud-init obsługę maszyn wirtualnych na platformie Azure
W tym artykule opisano obsługę, która istnieje dla [pakietu cloud-init](https://cloudinit.readthedocs.io) Aby skonfigurować maszynę wirtualną (VM) lub maszyn wirtualnych zestawów skalowania (zestawu skalowania maszyn wirtualnych) na inicjowanie obsługi administracyjnej czas na platformie Azure. Skrypty te pakietu cloud-init są uruchamiane podczas pierwszego rozruchu po zasoby zostały udostępnione przez platformę Azure.  

## <a name="cloud-init-overview"></a>Omówienie pakietu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest wywoływana podczas początkowego rozruchu, dlatego są żadne dodatkowe kroki ani agenci wymagane do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat poprawnie sformatować swoje `#cloud-config` plików, zobacz [witrynie dokumentacji pakietu cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` są to pliki tekstowe zakodowane w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

 Aktywnie współpracujemy z partnerami zalecanych dystrybucji systemu Linux, aby mogła mieć pakietu cloud-init, włączone obrazów dostępnych w witrynie Azure marketplace. Te obrazy spowoduje, że wdrożenia pakietu cloud-init i konfiguracje bezproblemowej współpracy z maszyn wirtualnych i zestawów skalowania maszyn wirtualnych (VMSS). W poniższej tabeli przedstawiono bieżącej dostępności obrazów pakietu cloud-init, włączone na platformie Azure:

| Wydawca | Oferta | SKU | Wersja | gotowe pakietu cloud-init |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |najnowsza |tak | 
|Canonical |UbuntuServer |17.10 |najnowsza |tak | 
|Canonical |UbuntuServer |16.04-LTS |najnowsza |tak | 
|Canonical |UbuntuServer |14.04.5-LTS |najnowsza |tak |
|CoreOS |CoreOS |Stable |najnowsza |tak |
|OpenLogic |CentOS |7-CI |najnowsza |wersja zapoznawcza |
|RedHat |RHEL |7-RAW-CI |najnowsza |wersja zapoznawcza |

Obecnie usługi Azure Stack, nie obsługuje aprowizację RHEL 7.4 i CentOS 7.4 przy użyciu pakietu cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaka jest różnica między pakietu cloud-init i agenta systemu Linux (WALA)?
WALA jest agenta specyficzne dla platformy Azure, używany do aprowizacji i konfigurowania maszyn wirtualnych i obsługę rozszerzeń platformy Azure. Rozszerzamy zadania konfigurowania maszyn wirtualnych do korzystania z pakietu cloud-init zamiast agenta systemu Linux w celu umożliwienia istniejący klienci pakietu cloud-init, używać ich bieżącego skryptów pakietu cloud-init.  Jeśli masz istniejące inwestycje w pakiet cloud-init skrypty do konfigurowania systemów Linux, istnieją **są wymagane nie dodatkowe ustawienia** je włączyć. 

Jeśli nie dołączysz wiersza polecenia platformy Azure `--custom-data` przełącznik na czas, WALA obsługi administracyjnej ma minimalne maszyn wirtualnych, inicjowanie obsługi administracyjnej parametrów wymaganych do aprowizowania maszyny Wirtualnej i ukończyć wdrażanie przy użyciu ustawień domyślnych.  Jeśli odwołujesz się pakietu cloud-init `--custom-data` przełącznika, niezależnie od rodzaju znajduje się w danych niestandardowych (poszczególnych ustawień lub pełny skrypt) zastępuje wartości domyślne WALA. 

Konfiguracje WALA maszyn wirtualnych są ograniczone na czas pracy w maksymalny czas obsługi administracyjnej maszyny Wirtualnej.  Konfiguracje pakietu cloud-init stosowane do maszyn wirtualnych bez ograniczeń czasowych i nie spowoduje niepowodzenie, przekroczeniem limitu czasu wdrażania. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Wdrażanie pakietu cloud-init włączone maszyny wirtualnej
Wdrażanie maszyny wirtualnej pakietu cloud-init, włączone jest proste i polega odwołujące się do dystrybucji pakietu cloud-init, włączone podczas wdrażania.  Maintainers dystrybucji systemu Linux, musisz wybrać opcję Włącz i integrowanie pakietu cloud-init na ich podstawowej platformy Azure opublikowanych obrazów. Po sprawdzeniu, czy obraz, który chcesz wdrożyć jest pakietu cloud-init, włączone, można użyć wiersza polecenia platformy Azure do wdrożenia obrazu. 

Pierwszym krokiem we wdrażaniu tego obrazu jest utworzenie grupy zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Następnym krokiem jest utworzenie pliku w bieżącej powłoce o nazwie *cloud-init.txt* i Wklej poniższą konfigurację. W tym przykładzie należy utworzyć plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **nano** edytora. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Naciśnij klawisz `ctrl-X` aby wyjść z pliku, wpisz `y` można zapisać pliku i naciśnij klawisz `enter` aby potwierdzić nazwę pliku na wyjściu.

Ostatnim krokiem jest utworzenie maszyny Wirtualnej z [tworzenie az vm](/cli/azure/vm) polecenia. 

Poniższy przykład tworzy Maszynę wirtualną o nazwie *centos74* i tworzy klucze SSH, jeśli ich jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy Maszynę wirtualną o nazwie *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po utworzeniu maszyny Wirtualnej, wiersza polecenia platformy Azure zostaną wyświetlone informacje specyficzne dla danego wdrożenia. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.  Dopiero po pewnym czasie utworzenie maszyny Wirtualnej, zainstalowanie pakietów i uruchomienie aplikacji. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Możesz SSH z maszyną wirtualną i użyj kroki opisane w sekcji rozwiązywania problemów, aby wyświetlić dzienniki pakietu cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Rozwiązywanie problemów z pakietu cloud-init
Gdy maszyna wirtualna została aprowizowana, pakietu cloud-init zostanie uruchomiony za pośrednictwem wszystkich modułów i skrypt zdefiniowany w `--custom-data` w celu skonfigurowania maszyny Wirtualnej.  Jeśli trzeba rozwiązać wszelkie błędy lub zaniechań z konfiguracji, należy wyszukać nazwę modułu (`disk_setup` lub `runcmd` na przykład) w dzienniku pakietu cloud-init - siedzibą na terenie **/var/log/cloud-init.log**.

> [!NOTE]
> Nie każdy błąd modułu skutkuje krytyczny pakietu cloud-init ogólny błąd konfiguracji. Na przykład za pomocą `runcmd` modułu, jeśli skrypt zakończy się niepowodzeniem, pakietu cloud-init będzie nadal zgłaszać inicjowania obsługi administracyjnej powiodło się, ponieważ moduł runcmd wykonany.

Aby uzyskać więcej szczegółów dotyczących rejestrowania pakietu cloud-init, dotyczą [dokumentacji pakietu cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Kolejne kroki
Przykłady pakietu cloud-init zmian konfiguracji na ten temat można znaleźć w następujących dokumentach:
 
- [Dodaj dodatkowe użytkownika w systemie Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety podczas pierwszego rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwą hosta maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizować pliki konfiguracji i wstawić kluczy](tutorial-automate-vm-deployment.md)
