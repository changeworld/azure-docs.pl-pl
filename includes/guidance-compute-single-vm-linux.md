---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/17/2018
ms.author: iainfou
ms.custom: include file
ms.openlocfilehash: 08b0cd35294a6cd4086fd5640b09d944ed361c41
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
W tym artykule przedstawiono zestaw sprawdzonych rozwiązań dotyczących uruchamiania maszyny wirtualnej z systemem Linux na platformie Azure z uwzględnieniem zagadnień dotyczących skalowalności, dostępności, możliwości zarządzania i zabezpieczeń. Platforma Azure obsługuje różne popularne dystrybucje systemu Linux, w tym CentOS, Debian, Red Hat Enterprise, Ubuntu i FreeBSD. Aby uzyskać więcej informacji, zobacz [Azure and Linux][azure-linux] (Platforma Azure i system Linux).

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania: [model wdrażania przy użyciu usługi Resource Manager][resource-manager-overview] i model klasyczny. W tym artykule jest używana usługa Resource Manager, którą firma Microsoft zaleca w przypadku nowych wdrożeń.
> 
> 

Nie zalecamy używania pojedynczej maszyny wirtualnej dla obciążeń o znaczeniu krytycznym, ponieważ powoduje to powstanie pojedynczego punktu awarii. Aby uzyskać wyższą dostępność, należy wdrożyć wiele maszyn wirtualnych w postaci [zestawu dostępności][availability-set]. Aby uzyskać więcej informacji, zobacz [Running multiple VMs on Azure][multi-vm] (Uruchamianie wielu maszyn wirtualnych na platformie Azure). 

## <a name="architecture-diagram"></a>Diagram architektury
Aprowizowanie maszyny wirtualnej na platformie Azure obejmuje więcej elementów niż tylko samą maszynę wirtualną. Konieczne jest wzięcie pod uwagę elementów obliczeniowych, sieciowych i magazynowych.

> Dokument programu Visio, który zawiera ten diagram architektury, jest dostępny do pobrania w [Centrum pobierania Microsoft][visio-download]. Ten diagram znajduje się na stronie „Compute - single VM” (Obliczenia — pojedyncza maszyna wirtualna).
> 
> 

![[0]][0]

* **Grupa zasobów.** [*Grupa zasobów*][resource-manager-overview] to kontener, który zawiera powiązane zasoby. Utwórz grupę zasobów na potrzeby przechowywania zasobów tej maszyny wirtualnej.
* **Maszyna wirtualna**. Możesz aprowizować maszynę wirtualną za pomocą listy opublikowanych obrazów lub pliku wirtualnego dysku twardego (VHD), który został przekazany do usługi Azure Blob Storage.
* **Dysk systemu operacyjnego.** Dysk systemu operacyjnego to plik VHD przechowywany w usłudze [Azure Storage][azure-storage]. Oznacza to, że zostanie zachowany, nawet jeśli maszyna hosta zostanie wyłączona. Dysk systemu operacyjnego to `/dev/sda1`.
* **Dysk tymczasowy.** Maszyna wirtualna jest tworzona razem z dyskiem tymczasowym. Ten dysk jest przechowywany na dysku fizycznym maszyny hosta. *Nie* jest on zapisany w usłudze Azure Storage i może zostać usunięty podczas ponownego uruchamiania i innych zdarzeń cyklu życia maszyny wirtualnej. Używaj tego dysku tylko dla danych tymczasowych, takich jak plik stronicowania lub plik wymiany. Dysk tymczasowy to dysk `/dev/sdb1` zainstalowany jako `/mnt/resource` lub `/mnt`.
* **Dyski danych.** [Dysk danych][data-disk] to trwały plik VHD używany na potrzeby danych aplikacji. Dyski danych są przechowywane w usłudze Azure Storage tak jak dysk systemu operacyjnego.
* **Sieć wirtualna i podsieci.** Każda maszyna wirtualna na platformie Azure jest wdrażana w sieci wirtualnej, która jest podzielona na podsieci.
* **Publiczny adres IP.** Publiczny adres IP umożliwia komunikację z maszyną wirtualną &mdash; na przykład za pomocą protokołu SSH.
* **Interfejs sieciowy (karta sieciowa)**. Karta sieciowa umożliwia maszynie wirtualnej komunikację z siecią wirtualną.
* **Sieciowa grupa zabezpieczeń**. [Sieciowa grupa zabezpieczeń][nsg] jest używana do określenia, który ruch sieciowy skierowany do podsieci jest dozwolony. Możesz skojarzyć sieciową grupę zabezpieczeń z konkretną kartą sieciową lub z podsiecią. W przypadku skojarzenia z podsiecią reguły sieciowej grupy zabezpieczeń mają zastosowanie do wszystkich maszyn wirtualnych w tej podsieci.
* **Diagnostyka.** Rejestrowanie informacji diagnostycznych ma kluczowe znaczenie dla zarządzania maszyną wirtualną i rozwiązywania dotyczących jej problemów.

## <a name="recommendations"></a>Zalecenia

Poniższe zalecenia dotyczą większości scenariuszy. Należy się do nich stosować, jeśli nie ma konkretnych wymagań, które byłyby z nimi sprzeczne. 

### <a name="vm-recommendations"></a>Zalecenia dotyczące maszyny wirtualnej

Platforma Azure oferuje wiele różnych rozmiarów maszyny wirtualnej, lecz zalecamy użycie serii GS i DS, ponieważ maszyny tych rozmiarów obsługują usługę [Premium Storage][premium-storage]. Wybierz jeden z tych rozmiarów maszyny, chyba że obciążenie ma specyficzne wymagania, tak jak w przypadku obliczeń o wysokiej wydajności. Aby uzyskać szczegółowe informacje, zobacz [Virtual machine sizes][virtual-machine-sizes] (Rozmiary maszyny wirtualnej).

Jeśli przenosisz istniejące obciążenie na platformę Azure, rozpocznij od rozmiaru maszyny wirtualnej, który najlepiej pasuje do Twoich serwerów lokalnych. Następnie zmierz wydajność rzeczywistego obciążenia pod kątem procesora CPU, pamięci i operacji wejścia/wyjścia na sekundę (IOPS) dysku oraz dostosuj rozmiar w razie potrzeby. Jeśli maszyna wirtualna wymaga wielu kart sieciowych, pamiętaj, że maksymalna liczba kart sieciowych zależy od [rozmiaru maszyny wirtualnej][vm-size-tables].

Podczas aprowizowania maszyny wirtualnej i innych zasobów należy określić region. Ogólnie najlepiej wybierać region znajdujący się najbliżej użytkowników wewnętrznych lub klientów. Jednak nie wszystkie rozmiary maszyny wirtualnej mogą być dostępne we wszystkich regionach. Aby uzyskać szczegółowe informacje, zobacz [Usługi według regionów][services-by-region]. Aby wyświetlić listę dostępnych rozmiarów maszyny wirtualnej w danym regionie, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure:

```
azure vm sizes --location <location>
```

Aby uzyskać informacje o wybieraniu opublikowanego obrazu maszyny wirtualnej, zobacz [Wybieranie obrazów maszyn wirtualnych z systemem Linux przy użyciu interfejsu wiersza polecenia][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Zalecenia dotyczące dysku i magazynu

Aby uzyskać najwyższą wydajność we/wy dysku, zalecamy użycie usługi [Premium Storage][premium-storage], która przechowuje dane na dyskach półprzewodnikowych (SSD, solid-state drive). Koszt jest oparty na rozmiarze aprowizowanego dysku. Wartość IOPS i przepływność (to znaczy szybkość transferu danych) także zależą od rozmiaru dysku, dlatego podczas aprowizowania dysku należy uwzględnić wszystkie trzy czynniki (pojemność, operacje wejścia/wyjścia na sekundę i przepływność). 

Utwórz oddzielne konta usługi Azure Storage dla każdej maszyny wirtualnej na potrzeby przechowywania wirtualnych dysków twardych (VHD), aby uniknąć osiągnięcia limitów liczby operacji wejścia/wyjścia na sekundę dla kont usługi Storage. 

Dodaj co najmniej jeden dysk danych. Po utworzeniu wirtualnego dysku twardego jest on niesformatowany. Zaloguj się do maszyny wirtualnej, aby sformatować dysk. W powłoce systemu Linux dyski danych są wyświetlane jako `/dev/sdc`, `/dev/sdd` i tak dalej. Możesz uruchomić polecenie `lsblk`, aby wyświetlić urządzenia blokowe, w tym dyski. Aby użyć dysku danych, utwórz partycję i system plików oraz zainstaluj dysk. Na przykład:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

W przypadku dużej liczby dysków danych należy pamiętać o całkowitym limicie operacji we/wy konta usługi Storage. Aby uzyskać więcej informacji, zobacz [Virtual machine disk limits][vm-disk-limits] (Limity dysku maszyny wirtualnej).

W przypadku dodania dysku danych zostanie do niego przypisany identyfikator numeru jednostki logicznej (LUN, logical unit number). Opcjonalnie można określić identyfikator LUN &mdash; na przykład przy wymianie dysku, aby zachować ten sam identyfikator LUN, lub jeśli używana aplikacja szuka konkretnego identyfikatora LUN. Jednak należy pamiętać, że identyfikator LUN musi być unikatowy dla każdego dysku.

Można zmienić harmonogram we/wy, aby zoptymalizować wydajność dysków SSD, ponieważ dyski maszyn wirtualnych kont usługi Premium Storage to dyski SSD. Ogólne zalecenie to użycie harmonogramu NOOP dla dysków SSD, ale należy użyć narzędzia, takiego jak [iostat], do monitorowania wydajności we/wy dysku dla określonego obciążenia.

Aby uzyskać najwyższą wydajność, należy utworzyć oddzielne konto usługi Storage na potrzeby przechowywania dzienników diagnostycznych. Standardowe konto magazynu lokalnie nadmiarowego (LRS, locally redundant storage) jest wystarczające dla dzienników diagnostycznych.

### <a name="network-recommendations"></a>Zalecenia dotyczące sieci

Publiczny adres IP może być dynamiczny lub statyczny. Domyślnie jest dynamiczny.

* Zarezerwuj [statyczny adres IP][ static-ip], jeśli jest potrzebny niezmienny adres IP, który nie ulegnie zmianie &mdash; na przykład aby utworzyć rekord A w usłudze DNS lub dodać adres IP do listy bezpiecznych adresów.
* Można również utworzyć w pełni kwalifikowaną nazwę domeny (FQDN, fully qualified domain name) dla adresu IP. Następnie można zarejestrować [rekord CNAME][ cname-record], który wskazuje nazwę FQDN, w usłudze DNS. Aby uzyskać więcej informacji, zobacz [Create a fully qualified domain name in the Azure portal][fqdn] (Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure Portal).

Wszystkie sieciowe grupy zabezpieczeń zawierają zestaw [domyślnych reguł][nsg-default-rules], tym regułę blokującą cały ruch przychodzący z Internetu. Nie można usunąć reguł domyślnych, ale inne reguły mogą je przesłonić. Aby włączyć ruch internetowy, utwórz reguły zezwalające na ruch przychodzący do określonych portów &mdash; na przykład portu 80 dla protokołu HTTP.  

Aby włączyć protokół SSH, dodaj do sieciowej grupy zabezpieczeń regułę, która zezwala na ruch przychodzący do portu TCP 22.

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Aby skalować w górę lub w dół, [zmień rozmiar maszyny wirtualnej][vm-resize]. 

Aby skalować w poziomie, umieść co najmniej dwie maszyny wirtualne w zestawie dostępności za modułem równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Running multiple VMs on Azure][multi-vm] (Uruchamianie wielu maszyn wirtualnych na platformie Azure).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Aby uzyskać wyższą dostępność, wdróż wiele maszyn wirtualnych w postaci zestawu dostępności. Ten sposób zapewnia także uzyskanie lepszej [umowy dotyczącej poziomu usług] [ vm-sla] (SLA, service level agreement). 

Na maszynę wirtualną może mieć wpływ [zaplanowana konserwacja][planned-maintenance] lub [nieplanowana konserwacja][manage-vm-availability]. Można użyć [dzienników ponownego uruchamiania maszyny wirtualnej][ reboot-logs] do określenia, czy ponowne uruchomienie maszyny wirtualnej zostało spowodowane przez zaplanowaną konserwację.

Pliki VHD są przechowywane w usłudze [Azure Storage][azure-storage], która jest replikowana w celu zapewnienia trwałości i dostępności.

Aby chronić przed przypadkową utratą danych podczas normalnego działania (na przykład z powodu błędu użytkownika), należy także zaimplementować tworzenie kopii zapasowych dla punktu w czasie za pomocą [migawek obiektu blob][blob-snapshot] lub innego narzędzia.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

**Grupy zasobów.** Ściśle powiązane zasoby objęte takim samym cyklem życia należy umieścić w tej samej [grupie zasobów][resource-manager-overview]. Grupy zasobów umożliwiają wdrażanie i monitorowanie zasobów jako grupy oraz rozliczanie kosztów według grupy zasobów. Można również usunąć zasoby jako zestaw, co jest bardzo przydatne w przypadku wdrożeń testowych. Zasobom należy nadawać opisowe nazwy. Ułatwia to zlokalizowanie danego zasobu i określenie jego roli. Zobacz [Zalecane konwencje nazewnictwa dla zasobów platformy Azure][naming conventions].

**Protokół SSH**. Przed utworzeniem maszyny wirtualnej z systemem Linux wygeneruj parę 2048-bitowych kluczy publiczny-prywatny, korzystając z algorytmu RSA. Użyj pliku klucza publicznego podczas tworzenia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [How to Use SSH with Linux and Mac on Azure][ssh-linux] (Jak używać protokołu SSH w systemach Linux i Mac na platformie Azure).

**Diagnostyka maszyny wirtualnej.** Włącz monitorowanie i diagnostykę, w tym podstawowe metryki kondycji, dzienniki infrastruktury diagnostyki i [diagnostykę rozruchu][boot-diagnostics]. Diagnostyka rozruchu może ułatwić diagnozowanie błędów rozruchu, jeśli nie będzie można uruchomić maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Włączanie monitorowania i diagnostyki][enable-monitoring].  

Następujące polecenia interfejsu wiersza polecenia umożliwiają włączenie diagnostyki:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Zatrzymywanie maszyny wirtualnej.** Platforma Azure rozróżnia między stanami „zatrzymana” i „cofnięty przydział”. Opłaty są naliczane, gdy maszyna wirtualna jest zatrzymana, lecz nie wtedy, gdy jest cofnięty przydział.

Następujące polecenie interfejsu wiersza polecenia umożliwia cofnięcie przydziału maszyny wirtualnej:

```
azure vm deallocate <resource-group> <vm-name>
```

W witrynie Azure Portal przycisk **Zatrzymaj** powoduje cofnięcie przydziału maszyny wirtualnej. Jednak jeśli maszyna wirtualna zostanie zamknięta za pomocą systemu operacyjnego przez zalogowanego użytkownika, to zostanie zatrzymana, lecz przydział *nie zostanie* cofnięty, więc w dalszym ciągu będą naliczane opłaty.

**Usuwanie maszyny wirtualnej.** Jeśli usuniesz maszynę wirtualną, jej dyski VHD nie zostaną usunięte. Oznacza to, że możesz bezpiecznie usunąć maszyną wirtualną bez utraty danych. Jednak opłaty za magazyn będą w dalszym ciągu naliczane. Aby usunąć dysk VHD, usuń plik z usługi [Blob Storage][blob-storage].

Aby zapobiec przypadkowemu usunięciu, użyj [blokady zasobu][ resource-lock] do zablokowania całej grupy zasobów lub poszczególnych zasobów, takich jak maszyna wirtualna. 

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Aktualizowanie systemu operacyjnego można zautomatyzować za pomocą rozszerzenia maszyny wirtualnej [OSPatching]. Zainstaluj to rozszerzenie podczas aprowizowania maszyny wirtualnej. Możesz określić, jak często będą instalowane poprawki i czy po instalacji poprawki zostanie wykonane ponowne uruchomienie.

Użyj [kontroli dostępu na podstawie ról] [ rbac] (RBAC, role-based access control) do kontrolowania dostępu do wdrożonych zasobów platformy Azure. Kontrola RBAC umożliwia przypisywanie ról autoryzacji do członków zespołu DevOps. Na przykład rola Czytelnik może umożliwiać wyświetlanie zasobów platformy Azure, ale nie ich tworzenie, usuwanie ani zarządzanie nimi. Niektóre role są specyficzne dla konkretnych typów zasobu platformy Azure. Na przykład rola Współautor maszyny wirtualnej umożliwia ponowne uruchomienie lub cofnięcie przydziału maszyny wirtualnej, zresetowanie hasła administratora, utworzenie maszyny wirtualnej itd. Inne [wbudowane role kontroli RBAC][rbac-roles] potencjalnie przydatne w przypadku tej architektury referencyjnej to [Użytkownik usługi DevTest Labs][rbac-devtest] i [Współautor sieci][rbac-network]. 

Użytkownika można przypisać do wielu ról oraz można utworzyć niestandardowe role w celu stosowania jeszcze bardziej szczegółowych uprawnień.

d> [!NOTE]
> Kontrola RBAC nie ogranicza akcji, które może wykonać użytkownik zalogowany do maszyny wirtualnej. Te uprawnienia są określane przez typ konta w systemie operacyjnym gościa.   
> 
> 

Użyj [dzienników inspekcji][audit-logs], aby wyświetlić akcje aprowizowania i inne zdarzenia maszyny wirtualnej.

Rozważ użycie usługi [Azure Disk Encryption][disk-encryption], jeśli jest potrzebne szyfrowanie dysków systemu operacyjnego i danych. 

## <a name="solution-deployment"></a>Wdrażanie rozwiązania
Wdrożenie tej architektury referencyjnej jest dostępne w usłudze [GitHub][github-folder]. Obejmuje ono sieć wirtualną, sieciową grupę zabezpieczeń i pojedynczą maszynę wirtualną. Aby wdrożyć architekturę, wykonaj następujące kroki: 

1. Kliknij prawym przyciskiem myszy przycisk poniżej i wybierz polecenie „Otwórz link w nowej karcie” lub „Otwórz link w nowym oknie”.
   [![Wdrażanie na platformie Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Po otwarciu linku w witrynie Azure Portal musisz podać wartości niektórych ustawień: 
   
   * Nazwa **grupy zasobów** jest już zdefiniowana w pliku parametrów, dlatego wybierz polecenie **Utwórz nową** i wpisz `ra-single-vm-rg` w polu tekstowym.
   * Wybierz region w polu listy rozwijanej **Lokalizacja**.
   * Nie zmieniaj wartości pól tekstowych **Identyfikator URI elementu głównego szablonu** ani **Identyfikator URI elementu głównego parametru**.
.   * Wybierz **linux** w **typ systemu operacyjnego** listy rozwijanej.
   * Zapoznaj się z warunkami i postanowieniami, a następnie kliknij pole wyboru **Wyrażam zgodę na powyższe warunki i postanowienia**.
   * Kliknij przycisk **Kup**.
3. Zaczekaj na zakończenie wdrożenia.
4. Pliki parametrów zawierają ustaloną nazwę i hasło administratora. Zaleca się natychmiastową zmianę tych poświadczeń. Kliknij maszynę wirtualną o nazwie `ra-single-vm0 ` w witrynie Azure Portal. Następnie kliknij polecenie **Resetuj hasło** w sekcji **Pomoc techniczna i rozwiązywanie problemów**. Wybierz polecenie **Resetuj hasło** w polu listy rozwijanej **Tryb**, a następnie wybierz nowe wartości w polach **Nazwa użytkownika** i **Hasło**. Kliknij przycisk **Aktualizuj**, aby zachować nową nazwę użytkownika i hasło.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać wyższą dostępność, należy wdrożyć co najmniej dwie maszyny wirtualne za modułem równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Running multiple VMs on Azure][multi-vm] (Uruchamianie wielu maszyn wirtualnych na platformie Azure).

<!-- links -->

[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]:../articles/storage/common/storage-introduction.md
[blob-snapshot]:../articles/storage/blobs/storage-blob-snapshots.md
[blob-storage]:../articles/storage/common/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]:../articles/virtual-machines/linux/about-disks-and-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/security-overview.md
[nsg-default-rules]: ../articles/virtual-network/security-overview.md#default-security-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]:../articles/virtual-machines/windows/premium-storage.md
[rbac]: ../articles/role-based-access-control/overview.md
[rbac-roles]:../articles/role-based-access-control/built-in-roles.md
[rbac-devtest]:../articles/role-based-access-control/built-in-roles.md#devtest-labs-user
[rbac-network]:../articles/role-based-access-control/built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architektura pojedynczej maszyny wirtualnej z systemem Linux na platformie Azure"

