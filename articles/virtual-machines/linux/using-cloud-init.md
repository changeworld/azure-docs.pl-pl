---
title: Omówienie obsługi cloud-init dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Omówienie możliwości init w chmurze, aby skonfigurować maszynę wirtualną w czasie inicjowania obsługi administracyjnej na platformie Azure.
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
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465043"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>obsługa maszyn wirtualnych w chmurze na platformie Azure
W tym artykule wyjaśniono, że istnieje pomoc techniczna dla [init w chmurze,](https://cloudinit.readthedocs.io) aby skonfigurować zestawy skalowania maszyny wirtualnej (VM) lub maszyny wirtualnej w czasie inicjowania obsługi administracyjnej na platformie Azure. Te konfiguracje cloud-init są uruchamiane przy pierwszym rozruchu po udostępnieniu zasobów przez platformę Azure.  

Inicjowanie obsługi administracyjnej maszyny Wirtualnej to proces, w którym platforma Azure przekaże wartości parametrów tworzenia maszyny Wirtualnej, takie jak nazwa hosta, nazwa użytkownika, hasło itp., i udostępni je maszynie Wirtualnej podczas uruchamiania. "Agent inicjowania obsługi administracyjnej" będzie zużywać te wartości, skonfigurować maszynę wirtualną i raport z powrotem po zakończeniu. 

Platforma Azure obsługuje dwóch agentów inicjowania obsługi [administracyjnej w chmurze](https://cloudinit.readthedocs.io)i [agenta systemu Azure Linux Agent (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>przegląd cloud-init
[cloud-init](https://cloudinit.readthedocs.io) jest powszechnie stosowane podejście do dostosowywania maszyny Wirtualnej z systemem Linux, jak to uruchamia się po raz pierwszy. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Ponieważ cloud-init jest wywoływana podczas początkowego procesu rozruchu, nie ma żadnych dodatkowych kroków lub wymaganych agentów do zastosowania konfiguracji.  Aby uzyskać więcej informacji na `#cloud-config` temat prawidłowego formatowania plików lub innych danych wejściowych, zobacz [witrynę dokumentacji init w chmurze](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`pliki są plikami tekstowymi zakodowanymi w base64.

cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie współpracujemy z naszymi zatwierdzonymi partnerami dystrybucji systemu Linux, aby obrazy z obsługą chmury były dostępne w portalu Azure marketplace. Te obrazy sprawią, że wdrożenia i konfiguracje typu cloud-init będą bezproblemowo współpracować z maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych. Początkowo współpracujemy z zatwierdzonymi partnerami dystrybucji systemu Linux i nadrzędnymi partnerami, aby zapewnić funkcje cloud-init z systemem operacyjnym na platformie Azure, a następnie pakiety są aktualizowane i udostępniane publicznie w repozytoriach pakietów dystrybucji. 

Istnieją dwa etapy udostępniania systemu operacyjnego dystrybucji systemu Linux na platformie Azure, obsługi pakietów, a następnie obsługi obrazów:
* "cloud-init obsługi pakietów na platformie Azure" dokumenty, które pakiety cloud-init są obsługiwane lub w wersji zapoznawczej, dzięki czemu można używać tych pakietów z systemu operacyjnego w obrazie niestandardowym.
* "image cloud-init ready" dokumentów, jeśli obraz jest już skonfigurowany do korzystania z cloud-init.


### <a name="canonical"></a>Canonical
| Wydawca / Wersja| Oferta | SKU | Wersja | obraz cloud-init gotowy | obsługa pakietów init w chmurze na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanoniczna 18.04 |UbuntuServer |18.04-LTS |najnowsza |tak | tak |
|Kanoniczny 16.04|UbuntuServer |16.04-LTS |najnowsza |tak | tak |
|Kanoniczny 14.04|UbuntuServer |14.04.5-LTS |najnowsza |tak | tak |

### <a name="rhel"></a>RHEL
| Wydawca / Wersja | Oferta | SKU | Wersja | obraz cloud-init gotowy | obsługa pakietów init w chmurze na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |tak | tak - wsparcie z wersji pakietu: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Tak (należy pamiętać, że jest to obraz podglądu, a gdy wszystkie obrazy RHEL 7.7 obsługują cloud-init, zostanie to usunięte w połowie 2020 r., zawiadomienie zostanie podane) | tak - wsparcie z wersji pakietowej: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | Nie dotyczy| no - aktualizacje obrazów do końca kwietnia 2020 r.| tak - wsparcie z wersji pakietowej: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | Nie dotyczy| no - aktualizacje obrazu do końca kwietnia| tak - wsparcie z wersji pakietowej: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | Nie dotyczy| no - aktualizacje obrazu do końca kwietnia | tak - wsparcie z wersji pakietowej: *18.5-3.el7*|
|RedHat 7.7 |rhel-byos | rhel-lvm77 | Nie dotyczy|no - aktualizacje obrazu do końca kwietnia  | tak - wsparcie z wersji pakietowej: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Wydawca / Wersja | Oferta | SKU | Wersja | obraz cloud-init gotowy | obsługa pakietów init w chmurze na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |Tak (należy pamiętać, że jest to obraz podglądu, a gdy wszystkie obrazy CentOS 7.7 obsługują cloud-init, zostanie to usunięte w połowie 2020 r., zawiadomienie zostanie podane) | tak - wsparcie z wersji pakietowej: *18.5-3.el7.centos*|

* Obrazy CentOS 7.7, które będą włączone w chmurze, zostaną zaktualizowane tutaj w marcu 2020 r. 

### <a name="oracle"></a>Oracle

| Wydawca / Wersja | Oferta | SKU | Wersja | obraz cloud-init gotowy | obsługa pakietów init w chmurze na platformie Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| podgląd obrazu (należy pamiętać, że jest to obraz podglądu, a gdy wszystkie obrazy Oracle 7.7 obsługują cloud-init, zostanie to usunięte w połowie 2020 r., zawiadomienie zostanie podane) | nie, w wersji zapoznawczej, pakiet jest: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>& Debiana SuSE SLES
Obecnie pracujemy nad wsparciem podglądu, oczekujemy aktualizacji w lutym i marcu 2020.

Obecnie usługa Azure Stack będzie obsługiwać inicjowanie obsługi administracyjnej obrazów z włączoną funkcją cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaka jest różnica między cloud-init i Linux Agent (WALA)?
WALA to agent specyficzny dla platformy Azure używany do aprowizowania i konfigurowania maszyn wirtualnych oraz obsługi [rozszerzeń platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux) 

Zwiększamy zadanie konfigurowania maszyn wirtualnych do używania cloud-init zamiast agenta systemu Linux, aby umożliwić istniejącym klientom typu cloud-init korzystanie z ich bieżących skryptów init w chmurze lub nowym klientom korzystanie z bogatej funkcji konfiguracji cloud-init. Jeśli masz już inwestycje w skrypty cloud-init do konfigurowania systemów Linux, **nie ma żadnych dodatkowych ustawień wymaganych** do włączenia procesu cloud-init je. 

cloud-init nie może przetwarzać rozszerzeń platformy Azure, więc WALA jest nadal wymagane w obrazie do przetwarzania rozszerzeń, ale będzie musiał mieć wyłączony kod inicjowania obsługi administracyjnej, dla zatwierdzonych obrazów dystrybucji Linuksa, które są konwertowane do udostępniania przez cloud-init, będą miały WALA i poprawnie skonfigurować.

Podczas tworzenia maszyny Wirtualnej, jeśli nie zawierają `--custom-data` przełącznik interfejsu wiersza polecenia platformy Azure w czasie inicjowania obsługi administracyjnej, cloud-init lub WALA przyjmuje minimalne parametry inicjowania obsługi administracyjnej maszyny Wirtualnej wymagane do aprowizowania maszyny Wirtualnej i zakończenia wdrożenia z wartościami domyślnymi.  Jeśli odwołujesz się do konfiguracji `--custom-data` cloud-init z przełącznikiem, cokolwiek jest zawarte w danych niestandardowych będą dostępne dla cloud-init po uruchomieniu maszyny Wirtualnej.

cloud-init konfiguracje stosowane do maszyn wirtualnych nie mają ograniczeń czasowych i nie spowoduje wdrożenia zakończyć się niepowodzeniem przez limit czasu. Nie jest to prawdą dla WALA, jeśli zmienisz domyślne WALA do przetwarzania danych niestandardowych, nie może przekroczyć całkowitego limitu czasu inicjowania obsługi administracyjnej maszyny Wirtualnej 40 minut, jeśli tak, tworzenie maszyny Wirtualnej zakończy się niepowodzeniem.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Wdrażanie maszyny wirtualnej z włączoną funkcją init w chmurze
Wdrażanie maszyny wirtualnej z włączoną funkcją cloud-init jest tak proste, jak odwoływanie się do dystrybucji włączonej w chmurze podczas wdrażania.  Opiekunzy dystrybucji systemu Linux muszą wybrać, aby włączyć i zintegrować cloud-init do swoich podstawowych obrazów opublikowanych platformy Azure. Po potwierdzeniu obrazu, który chcesz wdrożyć jest włączony w chmurze, można użyć interfejsu wiersza polecenia platformy Azure, aby wdrożyć obraz. 

Pierwszym krokiem wdrażania tego obrazu jest utworzenie grupy zasobów za pomocą polecenia [tworzenie grupy az.](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Następnym krokiem jest utworzenie pliku w bieżącej powłoce o nazwie *cloud-init.txt* i wklejenie następującej konfiguracji. W tym przykładzie utwórz plik w usłudze Cloud Shell nie na komputerze lokalnym. Możesz użyć dowolnego edytora. Wprowadź `sensible-editor cloud-init.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Wybierz #1, aby użyć **edytora nano.** Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Naciśnij, `ctrl-X` aby zamknąć `y` plik, wpisz, `enter` aby zapisać plik i naciśnij, aby potwierdzić nazwę pliku przy wyjściu.

Ostatnim krokiem jest utworzenie maszyny Wirtualnej za pomocą polecenia [az vm create.](/cli/azure/vm) 

Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74* i tworzy klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku konfiguracji *cloud-init.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym. Poniższy przykład tworzy maszynę wirtualną o nazwie *centos74:*

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po utworzeniu maszyny Wirtualnej interfejsu wiersza polecenia platformy Azure zawiera informacje specyficzne dla danego wdrożenia. Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.  Trwa trochę czasu dla maszyny Wirtualnej do utworzenia, pakiety do zainstalowania i aplikacji, aby uruchomić. Pewne zadania w tle działają nadal po powrocie do wiersza polecenia w interfejsie wiersza polecenia platformy Azure. Można SSH do maszyny Wirtualnej i wykonać kroki opisane w sekcji Rozwiązywanie problemów, aby wyświetlić dzienniki init chmury. 

## <a name="troubleshooting-cloud-init"></a>Rozwiązywanie problemów z init chmury
Po zainicjowaniu obsługi administracyjnej, cloud-init będzie działać za pośrednictwem `--custom-data` wszystkich modułów i skryptów zdefiniowanych w celu skonfigurowania maszyny Wirtualnej.  Jeśli chcesz rozwiązać wszelkie błędy lub pominięcia z konfiguracji, musisz wyszukać`disk_setup` `runcmd` nazwę modułu (lub na przykład) w dzienniku cloud-init - znajdującym się w **/var/log/cloud-init.log**.

> [!NOTE]
> Nie każda awaria modułu powoduje fatalną awarię ogólnej konfiguracji w chmurze. Na przykład przy `runcmd` użyciu modułu, jeśli skrypt nie powiedzie się, cloud-init będzie nadal raport inicjowania obsługi administracyjnej powiodło się, ponieważ uruchomiony moduł.

Aby uzyskać więcej informacji na temat rejestrowania cloud-init, zapoznaj się z [dokumentacją cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać przykłady zmian konfiguracji w chmurze, zobacz następujące dokumenty:
 
- [Dodawanie dodatkowego użytkownika systemu Linux do maszyny Wirtualnej](cloudinit-add-user.md)
- [Uruchamianie menedżera pakietów w celu zaktualizowania istniejących pakietów przy pierwszym rozruchu](cloudinit-update-vm.md)
- [Zmienianie nazwy hosta lokalnego maszyny Wirtualnej](cloudinit-update-vm-hostname.md) 
- [Instalowanie pakietu aplikacji, aktualizowanie plików konfiguracyjnych i wstrzykiwanie kluczy](tutorial-automate-vm-deployment.md)
 
