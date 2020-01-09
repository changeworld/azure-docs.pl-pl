---
title: Omówienie obsługi funkcji Cloud-init dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Omówienie możliwości usługi Cloud-init w celu skonfigurowania maszyny wirtualnej w czasie aprowizacji na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/11/2019
ms.author: danis
ms.openlocfilehash: 7b3f64d0629ba5d7aaf85b854e1ee8e5a1410f94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458606"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Obsługa usługi Cloud-init dla maszyn wirtualnych w systemie Azure
W tym artykule opisano obsługę funkcji [Cloud-init](https://cloudinit.readthedocs.io) w celu skonfigurowania maszyny wirtualnej lub zestawów skalowania maszyn wirtualnych w czasie aprowizacji na platformie Azure. Te skrypty usługi Cloud-init są uruchamiane podczas pierwszego rozruchu po udostępnieniu zasobów przez platformę Azure.  

## <a name="cloud-init-overview"></a>Omówienie pakietu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest wywoływana podczas początkowego rozruchu, dlatego są żadne dodatkowe kroki ani agenci wymagane do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat poprawnie sformatować swoje `#cloud-config` plików, zobacz [witrynie dokumentacji pakietu cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` są to pliki tekstowe zakodowane w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie współpracujemy z partnerami zalecanych dystrybucji systemu Linux, aby mogła mieć pakietu cloud-init, włączone obrazów dostępnych w witrynie Azure marketplace. Te obrazy spowodują, że wdrożenia i konfiguracje usługi Cloud-init pracują bezproblemowo z maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych. W poniższej tabeli przedstawiono bieżącej dostępności obrazów pakietu cloud-init, włączone na platformie Azure:

| Publisher | Oferta | JSZ | Wersja | gotowe pakietu cloud-init |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |najnowsza |tak | 
|Canonical |UbuntuServer |16.04-LTS |najnowsza |tak | 
|Canonical |UbuntuServer |14.04.5-LTS |najnowsza |tak |
|CoreOS |CoreOS |Stable |najnowsza |tak |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |wersja zapoznawcza |
|Oracle 7,7 |Oracle-Linux |77-Ci |7.7.01|wersja zapoznawcza |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |tak |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |wersja zapoznawcza |
    
Obecnie Azure Stack nie obsługuje aprowizacji RHEL 7. x i CentOS 7. x przy użyciu funkcji Cloud-init.

* Dla RHEL 7,6, pakiet Cloud-init, obsługiwany pakiet to: *18.2-1. el7_6.2* 
* W przypadku RHEL 7,7 (wersja zapoznawcza) pakiet Cloud-init jest pakietem w wersji zapoznawczej: *18.5 -3. el7*
* W przypadku CentOS 7,7 (wersja zapoznawcza) pakiet Cloud-init jest pakietem w wersji zapoznawczej: *18.5 -3. el7. CentOS*
* W przypadku oprogramowania Oracle 7,7 (wersja zapoznawcza) pakiet Cloud-init jest pakietem w wersji zapoznawczej: *18.5-3.0.1. el7*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Czym różni się usługa Cloud-init i Agent systemu Linux (WALA)?
WALA to Agent specyficzny dla platformy Azure służący do aprowizacji i konfigurowania maszyn wirtualnych oraz obsługi rozszerzeń platformy Azure. Ulepszamy zadanie konfigurowania maszyn wirtualnych do korzystania z usługi Cloud-init zamiast agenta systemu Linux, aby umożliwić istniejącym klientom korzystającym z usługi Cloud-init korzystanie z bieżących skryptów w chmurze.  Jeśli masz istniejące inwestycje w Skrypty inicjalizacji w chmurze do konfigurowania systemów z systemem Linux, **nie są wymagane żadne dodatkowe ustawienia** , aby je włączyć. 

Jeśli nie dołączysz przełącznika `--custom-data` interfejsu wiersza polecenia platformy Azure w czasie aprowizacji, WALA przyjmuje minimalne parametry aprowizacji maszyny wirtualnej wymagane do zainicjowania obsługi maszyny wirtualnej i ukończą wdrożenie przy użyciu ustawień domyślnych.  Jeśli odwołujesz się do przełącznika Cloud-init `--custom-data`, każda z nich jest zawarta w danych niestandardowych (poszczególne ustawienia lub pełny skrypt) przesłania wartości domyślne WALA. 

Konfiguracje WALA maszyn wirtualnych są ograniczone do czasu pracy w maksymalnym czasie aprowizacji maszyny wirtualnej.  Konfiguracje inicjowania usługi Cloud-init stosowane do maszyn wirtualnych nie mają ograniczeń czasowych i nie spowodują, że wdrożenie zakończy się niepowodzeniem. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Wdrażanie maszyny wirtualnej z obsługą usługi Cloud-init
Wdrażanie maszyny wirtualnej z włączoną obsługą chmury jest proste, ponieważ odwołuje się do dystrybucji z włączoną obsługą chmury podczas wdrażania.  Obsłudze dystrybucji systemu Linux muszą włączyć i zintegrować usługę Cloud-init z podstawowymi opublikowanymi obrazami platformy Azure. Po potwierdzeniu obrazu, który chcesz wdrożyć, jest włączona funkcja Cloud-init, można użyć interfejsu wiersza polecenia platformy Azure do wdrożenia obrazu. 

Pierwszym krokiem wdrożenia tego obrazu jest utworzenie grupy zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Następnym krokiem jest utworzenie pliku w bieżącej powłoce o nazwie *Cloud-init. txt* i wklejenie poniższej konfiguracji. Na potrzeby tego przykładu Utwórz plik w Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć edytora **nano** . Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Naciśnij `ctrl-X`, aby zamknąć plik, wpisz `y`, aby zapisać plik, a następnie naciśnij klawisz `enter`, aby potwierdzić nazwę pliku przy zamykaniu.

Ostatnim krokiem jest utworzenie maszyny wirtualnej za pomocą polecenia [AZ VM Create](/cli/azure/vm) . 

Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74* i tworzy klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetli informacje specyficzne dla Twojego wdrożenia. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.  Tworzenie maszyny wirtualnej trwa trochę czasu, pakiety do zainstalowania i aplikacja do uruchomienia. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Do maszyny wirtualnej można użyć protokołu SSH i wykonać kroki opisane w sekcji Rozwiązywanie problemów w celu wyświetlenia dzienników usługi Cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Rozwiązywanie problemów z usługą Cloud-init
Po zainicjowaniu obsługi maszyny wirtualnej Usługa Cloud-init będzie działać za pomocą wszystkich modułów i skryptów zdefiniowanych w `--custom-data` w celu skonfigurowania maszyny wirtualnej.  Jeśli konieczne jest rozwiązanie błędów lub pominięć z konfiguracji, należy wyszukać nazwę modułu (na przykład`disk_setup` lub `runcmd`) w dzienniku inicjowania usługi Cloud-init w **/var/log/Cloud-init.log**.

> [!NOTE]
> Nie każdy błąd modułu powoduje krytyczny błąd konfiguracji w chmurze. Na przykład przy użyciu modułu `runcmd`, jeśli skrypt nie powiedzie się, Usługa Cloud-init nadal zgłosi zgłoszenie, ponieważ moduł runcmd został uruchomiony.

Więcej szczegółów dotyczących rejestrowania w usłudze Cloud-init można znaleźć w [dokumentacji usługi Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Następne kroki
Przykłady zmian konfiguracji w usłudze Cloud-init można znaleźć w następujących dokumentach:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny wirtualnej](cloudinit-add-user.md)
- [Uruchom Menedżera pakietów, aby zaktualizować istniejące pakiety przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmień lokalną nazwę hosta maszyny wirtualnej](cloudinit-update-vm-hostname.md) 
- [Zainstaluj pakiet aplikacji, zaktualizuj pliki konfiguracji i klucze iniekcji](tutorial-automate-vm-deployment.md)
