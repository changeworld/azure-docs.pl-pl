---
title: Omówienie zestawów skalowania maszyn wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się więcej o zestawach skalowania maszyn wirtualnych platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64c4b309d214b91c759ff93ad659b9c69320bea7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201159"
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Co to są zestawy skalowania maszyn wirtualnych na platformie Azure?
Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który umożliwia wdrożenie zestawu identycznych maszyn wirtualnych oraz zarządzanie nim. Wszystkie maszyny wirtualne są skonfigurowane tak samo, dzięki czemu zestawy skalowania umożliwiają prawdziwe automatyczne skalowanie i nie jest wymagana wstępna aprowizacja maszyn wirtualnych. Ułatwia to tworzenie usług w dużej skali nakierowanych na duże wystąpienia obliczeniowe, duże ilości danych i obciążenia konteneryzowane.

W przypadku aplikacji skalujących zasoby komputerowe w poziomie operacje skalowania są niejawnie równoważone w domenach błędów i aktualizacji. Aby lepiej zapoznać się z zagadnieniami dotyczącymi zestawów skalowania, zobacz [Ogłoszenia na blogu platformy Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Aby dowiedzieć się więcej o zestawach skalowania, obejrzyj te klipy wideo:

* [Mark Russinovich omawia zestawy skalowania na platformie Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Zestawy skalowania maszyn wirtualnych według Guya Bowermana](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Tworzenie zestawów skalowania i zarządzanie nimi
Zestaw skalowania można utworzyć w witrynie [Azure Portal](https://portal.azure.com), wybierając pozycję **Utwórz zasób** i wpisując na pasku wyszukiwania hasło **skalowania**. Na liście wyników wyszukiwania pojawi się pozycja **zestaw skalowania maszyn wirtualnych**. Z tego miejsca można wypełnić wymagane pola w celu dostosowania i wdrożenia zestawu skalowania. W portalu dostępne są również opcje umożliwiające konfigurację podstawowych reguł automatycznego skalowania na podstawie użycia procesora CPU. Do zarządzania zestawem skalowania możesz użyć witryny Azure Portal, [poleceń cmdlet programu Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) lub interfejsu wiersza polecenia platformy Azure 2.0.

Zestawy skalowania można wdrożyć w różnych [strefach dostępności](virtual-machine-scale-sets-use-availability-zones.md).

Zestawy skalowania można definiować i wdrażać za pomocą szablonów JSON oraz [interfejsów API REST](https://msdn.microsoft.com/library/mt589023.aspx) — podobnie jak poszczególne maszyny wirtualne w ramach usługi Azure Resource Manager. Można zatem użyć dowolnej standardowej metody wdrażania za pomocą usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Zestaw przykładowych szablonów dla zestawów skalowania maszyn wirtualnych znajduje się w [repozytorium szablonów szybkiego startu platformy Azure w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates). (Poszukaj szablonów z ciągiem **vmss** w nazwie).

W przykładach szablonów w przewodniku Szybki start przycisk „Wdróż na platformie Azure” w pliku Readme dla każdego szablonu odsyła do funkcji wdrażania w portalu. Aby wdrożyć zestaw skalowania, kliknij ten przycisk, a następnie wypełnij wymagane parametry w portalu. 


## <a name="autoscale"></a>Automatyczne skalowanie
Aby zachować spójną wydajność aplikacji, możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych w zestawie skalowania. Ta możliwość automatycznego skalowania zmniejsza nakład pracy na zarządzanie monitorowaniem i dostrajaniem Twojego zestawu skalowania w miarę, jak z czasem klient żąda zmian. Możesz zdefiniować reguły na podstawie metryki wydajności, odpowiedzi aplikacji lub ustalonego harmonogramu, a Twój zestaw skalowania automatycznie zmieni skalę odpowiednio do potrzeb.

Dla podstawowych reguł automatycznego skalowania możesz użyć metryki wydajności opartej na hoście, takiej jak użycie procesora CPU lub operacje We/Wy dysku. Te metryki oparte na hoście są dostępne automatycznie i nie trzeba instalować ani konfigurować żadnych dodatkowych agentów. Reguły automatycznego skalowania, które korzystają z metryk opartych na hoście, można utworzyć za pomocą jednego z następujących narzędzi:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Interfejs wiersza polecenia platformy Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Aby użyć bardziej szczegółowych metryk wydajności, możesz zainstalować i skonfigurować rozszerzenie diagnostyki platformy Azure na wystąpieniach maszyn wirtualnych w Twoim zestawie skalowania. Rozszerzenie diagnostyki platformy Azure umożliwia zbieranie dodatkowych metryk wydajności, takich jak zużycie pamięci, z wewnątrz każdego wystąpienia maszyny wirtualnej. Te metryki wydajności są przesyłane strumieniowo do konta magazynu platformy Azure, a użytkownik tworzy reguły automatycznego skalowania, aby wykorzystać te dane. Aby uzyskać więcej informacji, zobacz artykuły o tym, jak włączyć rozszerzenie diagnostyki platformy Azure na [maszynie wirtualnej z systemem Linux](../virtual-machines/linux/diagnostic-extension.md) lub [maszynie wirtualnej z systemem Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).

Do monitorowania samej wydajności aplikacji możesz zainstalować i skonfigurować mały pakiet instrumentacji w swojej aplikacji w celu uzyskania szczegółowych informacji o aplikacji. Następnie można przesłać strumieniowo szczegółowe metryki wydajności dotyczące czasu odpowiedzi aplikacji lub liczby sesji z powrotem ze swojej aplikacji. Możesz następnie utworzyć reguły automatycznego skalowania ze zdefiniowanymi progami dla samej wydajności na poziomie aplikacji. Aby uzyskać więcej informacji o usłudze Application Insights, zobacz [Co to jest usługa Application Insights](../application-insights/app-insights-overview.md).


## <a name="manually-scaling-a-scale-set-out-and-in"></a>Ręczne skalowanie zestawu skalowania na zewnątrz i do wewnątrz
Pojemność zestawu skalowania można ręcznie zmienić w witrynie Azure Portal, klikając sekcję **Skalowanie** w obszarze **Ustawienia**. 

Aby zmienić pojemność zestawu skalowania w wierszu polecenia, użyj polecenia **scale** w [interfejsie wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli). Aby na przykład ustawić zestaw skalowania na pojemność 10 maszyn wirtualnych, użyj polecenia:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Aby ustawić liczbę maszyn wirtualnych w zestawie skalowania za pomocą programu PowerShell, użyj polecenia **Update-AzureRmVmss**:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Aby zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w zestawie skalowania przy użyciu szablonu usługi Azure Resource Manager, zmień właściwość **capacity** i ponownie wdróż szablon. Ta prosta metoda umożliwia zintegrowanie zestawów skalowania z funkcją automatycznego skalowania na platformie Azure lub utworzenie własnej niestandardowej warstwy skalowania w celu zdefiniowania niestandardowych zdarzeń skalowania, które nie są obsługiwane przez automatyczne skalowanie na platformie Azure. 

W przypadku ponownego wdrażania szablonu usługi Azure Resource Manager w celu zmiany pojemności można zdefiniować dużo mniejszy szablon obejmujący jedynie pakiet właściwości **SKU** ze zaktualizowaną pojemnością. [Oto przykład](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).


## <a name="monitoring-your-scale-set"></a>Monitorowanie zestawu skalowania
W witrynie [Azure Portal](https://portal.azure.com) znajduje się lista zestawów skalowania oraz ich właściwości. Witryna obsługuje również operacje zarządzania. Operacje zarządzania można wykonywać zarówno na zestawach skalowania, jak i na poszczególnych maszynach wirtualnych w zestawie skalowania. Witryna zawiera także dostosowywalny wykres użycia zasobów. 

Jeśli chcesz wyświetlić lub edytować podstawową definicję JSON zasobu platformy Azure, możesz także użyć [Eksploratora zasobów Azure](https://resources.azure.com). Zestawy skalowania są zasobami dostępnymi w ramach dostawcy zasobów Microsoft.Compute platformy Azure. Z poziomu tej witryny można je wyświetlić, rozwijając następujące linki:

**Subskrypcje** > **Twoja subskrypcja** > **resourceGroups** > **dostawcy** > **Microsoft.Compute** > **virtualMachineScaleSets** > **zestaw skalowania** itd.

## <a name="scale-set-scenarios"></a>Scenariusze dotyczące zestawów skalowania
W tej sekcji przedstawiono niektóre typowe scenariusze dotyczące zestawów skalowania. Te scenariusze mają zastosowanie w przypadku niektórych usług platformy Azure wyższego poziomu (np. Batch, Service Fabric i Container Service).

* **Nawiązywanie połączenia z wystąpieniami zestawów skalowania przy użyciu protokołu RDP lub SSH** — zestaw skalowania jest tworzony w sieci wirtualnej, a poszczególnym maszynom wirtualnym w zestawie skalowania nie są domyślnie przydzielane publiczne adresy IP. Dzięki tej zasadzie unika się wydatków i nakładów pracy związanych z przydzielaniem osobnych publicznych adresów IP wszystkim węzłom w sieci obliczeniowej. Jeśli konieczne są bezpośrednie połączenia zewnętrzne do maszyn wirtualnych zestawu skalowania, można skonfigurować zestaw skalowania, aby automatycznie przypisywać publiczne adresy IP do nowych maszyn wirtualnych. Inna możliwość to nawiązywanie połączeń z maszynami wirtualnymi z innych zasobów w sieci wirtualnej, którym można przydzielić publiczne adresy IP, na przykład z modułów równoważenia obciążenia i autonomicznych maszyn wirtualnych. 
* **Nawiązywanie połączenia z maszynami wirtualnymi za pomocą reguł NAT** — można utworzyć publiczny adres IP, przypisać go do modułu równoważenia obciążenia i zdefiniować pulę NAT dla ruchu przychodzącego. Te akcje spowodują zamapowanie portów pod adresem IP na port maszyny wirtualnej w zestawie skalowania. Na przykład:
  
  | Element źródłowy | Port źródłowy | Element docelowy | Port docelowy |
  | --- | --- | --- | --- |
  |  Publiczny adres IP |Port 50000 |vmss\_0 |Port 22 |
  |  Publiczny adres IP |Port 50001 |vmss\_1 |Port 22 |
  |  Publiczny adres IP |Port 50002 |vmss\_2 |Port 22 |
  
   W [tym przykładzie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat) reguły NAT zdefiniowano w celu udostępnienia połączenia SSH każdej maszynie wirtualnej w zestawie skalowania przy użyciu jednego publicznego adresu IP.
  
   W [tym przykładzie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) wykonano te same działania przy użyciu protokołu RDP i systemu Windows.
* **Łączenie się z maszynami wirtualnymi za pomocą „rampy”** — jeśli utworzysz zestaw skalowania i autonomiczną maszynę wirtualną w tej samej sieci wirtualnej, autonomiczna maszyna wirtualna i maszyny wirtualne z zestawu skalowania mogą się łączyć ze sobą za pomocą wewnętrznych adresów IP zgodnie z definicją w sieci wirtualnej lub podsieci. W przypadku utworzenia publicznego adresu IP i przypisania go do autonomicznej maszyny wirtualnej można nawiązać połączenie RDP lub SSH z autonomiczną maszyną wirtualną. Następnie z poziomu tej maszyny możesz połączyć się z wystąpieniami danego zestawu skalowania. Można tutaj zauważyć, że prosty zestaw skalowania jest z natury bezpieczniejszy niż prosta autonomiczna maszyna wirtualna z publicznym adresem IP w konfiguracji domyślnej.
  
   Na przykład [ten szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) wdraża prosty zestaw skalowania z autonomiczną maszyną wirtualną. 
* **Równoważenie obciążenia wystąpień zestawów skalowania** — aby przekazywać zadania do klastra obliczeniowego maszyn wirtualnych w trybie okrężnym, można odpowiednio skonfigurować moduł równoważenia obciążenia platformy Azure za pomocą reguł równoważenia obciążenia w warstwie 4. Definiując sondy do weryfikowania działania aplikacji, można wysyłać polecenia ping do portów przy użyciu określonego protokołu, interwału i ścieżki żądania. Usługa [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) obsługuje również zestawy skalowania, a także warstwę 7 oraz bardziej zaawansowane scenariusze równoważenia obciążenia.
  
   Oto [przykład](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) tworzenia zestawu skalowania z uruchomionymi serwerami sieci Web Apache, który używa modułu równoważenia obciążenia do zrównoważenia obciążenia każdej maszyny wirtualnej. (Spójrz na typ zasobu Microsoft.Network/loadBalancers oraz elementy networkProfile i extensionProfile w obszarze virtualMachineScaleSet).

   W [tym przykładzie dla systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) i [tym przykładzie dla systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) użyto usługi Application Gateway.  

* **Wdrażanie zestawu skalowania jako klastra obliczeniowego w menedżerze klastra w środowisku PaaS** — zestawy skalowania są czasami określane jako rola procesu roboczego następnej generacji. Mimo iż jest to trafny opis, wiąże się z nim ryzyko pomylenia funkcji zestawu skalowania z funkcjami usług Azure Cloud Services. W pewnym sensie zestawy skalowania pełnią rolę procesu roboczego lub stanowią zasób procesu roboczego. Są ogólnym zasobem obliczeniowym, który jest niezależny od platformy/środowiska uruchomieniowego oraz możliwy do dostosowania i zintegrowania z infrastrukturą IaaS usługi Azure Resource Manager.
  
   Rola procesu roboczego usług Cloud Services ma ograniczenia w zakresie obsługi platform/środowiska uruchomieniowego (obsługuje wyłącznie obrazy platformy Windows), ale obejmuje także usługi takie jak wymiana wirtualnego adresu IP, konfigurowane ustawienia uaktualniania i określone ustawienia wdrażania środowiska uruchomieniowego/aplikacji. Te usługi nie są *jeszcze* dostępne w zestawach skalowania lub są zapewniane przez inne usługi PaaS wyższego poziomu, takie jak Azure Service Fabric. Zestawy skalowania można rozumieć jako infrastrukturę obsługującą rozwiązanie PaaS. Rozwiązania PaaS takie jak [Service Fabric](https://azure.microsoft.com/services/service-fabric/) są oparte na tej infrastrukturze.
  
   W [tym przykładzie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) takiego podejścia usługa [Azure Container Service](https://azure.microsoft.com/services/container-service/) wdraża klaster oparty na zestawach skalowania z koordynatorem kontenera.

## <a name="scale-set-performance-and-scale-guidance"></a>Wytyczne dotyczące wydajności i skalowania zestawów skalowania
* Zestaw skalowania obsługuje maksymalnie 1000 maszyn wirtualnych. W przypadku utworzenia i przekazania własnych niestandardowych obrazów maszyn wirtualnych limit wynosi 300. Uwagi dotyczące korzystania z dużych zestawów skalowania znajdziesz w temacie [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md).
* Aby używać zestawów skalowania, nie trzeba wstępnie tworzyć kont magazynu platformy Azure. Zestawy skalowania obsługują usługę Azure Managed Disks, dzięki czemu nie trzeba się martwić o wydajność związaną z liczbą dysków na konto magazynu. Aby uzyskać więcej informacji, zobacz [Zestawy skalowania maszyn wirtualnych i dyski zarządzane](virtual-machine-scale-sets-managed-disks.md).
* Aby uzyskać krótsze i bardziej przewidywalne czasy aprowizacji maszyny wirtualnej oraz lepszą wydajność operacji we/wy, rozważ użycie magazynu Azure Premium Storage zamiast magazynu Azure Storage.
* Przydział procesorów wirtualnych w regionie wdrażania ogranicza liczbę możliwych do utworzenia maszyn wirtualnych. Nawet jeśli obecnie masz wysoki limit procesorów wirtualnych na użytek usług Azure Cloud Services, może być konieczne skontaktowanie się z działem pomocy technicznej w celu zwiększenia limitu przydziału obliczeniowego. Aby wysłać zapytanie dotyczące limitu przydziału, uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure: `az vm list-usage`. Alternatywnie uruchom następujące polecenie programu PowerShell: `Get-AzureRmVMUsage`.

## <a name="frequently-asked-questions-for-scale-sets"></a>Często zadawane pytania dotyczące zestawów skalowania
**PYTANIE** Ile maszyn wirtualnych może się znajdować w zestawie skalowania?

**ODPOWIEDŹ** Zestaw skalowania może zawierać od 0 do 1000 maszyn wirtualnych opartych na obrazach platformy lub od 0 do 300 maszyn wirtualnych opartych na obrazach niestandardowych. 

**PYTANIE** Czy zestawy skalowania obsługują dyski danych?

**ODPOWIEDŹ** Tak. Zestaw skalowania może definiować konfigurację dołączonych dysków danych, która jest stosowana do wszystkich maszyn wirtualnych w zestawie. Aby uzyskać więcej informacji, zobacz [Zestawy skalowania na platformie Azure i dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md). Oto przykłady innych opcji magazynowania danych:

* Pliki platformy Azure (dyski udostępnione za pośrednictwem protokołu SMB)
* Dysk systemu operacyjnego
* Dysk tymczasowy (lokalny, bez kopii zapasowej w usłudze Azure Storage)
* Usługa danych platformy Azure (na przykład tabele platformy Azure, obiekty blob platformy Azure)
* Zewnętrzne usługi danych (na przykład zdalna baza danych)

**PYTANIE** Które regiony świadczenia usługi Azure obsługują zestawy skalowania?

**ODPOWIEDŹ** Wszystkie regiony obsługują zestawy skalowania.

**PYTANIE** Jak utworzyć zestaw skalowania za pomocą obrazu niestandardowego?

**ODPOWIEDŹ** Utwórz dysk zarządzany na podstawie wirtualnego dysku twardego z obrazem niestandardowym i odwołaj się do niego w szablonie zestawu skalowania. [Oto przykład](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**PYTANIE** Jeśli zmniejszę pojemność zestawu skalowania z 20 do 15, które maszyny wirtualne zostaną usunięte?

**ODPOWIEDŹ** W celu zapewnienia maksymalnej dostępności maszyny wirtualne są usuwane z zestawu skalowania równomiernie w domenach błędów i domenach aktualizacji. Najpierw są usuwane maszyny wirtualne o najwyższym identyfikatorze.

**PYTANIE** Co się stanie, jeśli następnie zwiększę pojemność z 15 do 18?

**ODPOWIEDŹ** Zwiększenie pojemności do 18 spowoduje utworzenie 3 nowych maszyn wirtualnych. Za każdym razem następuje zwiększenie identyfikatora wystąpienia maszyny wirtualnej względem poprzedniej najwyższej wartości (na przykład 20, 21, 22). Maszyny wirtualne są równoważone w domenach błędów i domenach aktualizacji.

**PYTANIE** Czy mogę wymusić sekwencję wykonywania w przypadku korzystania z wielu rozszerzeń w zestawie skalowania?

**ODPOWIEDŹ** Nie można tego zrobić bezpośrednio, ale w przypadku rozszerzenia customScript skrypt może poczekać na ukończenie działania innego rozszerzenia. Dodatkowe wytyczne dotyczące sekwencjonowania rozszerzeń można znaleźć we wpisie w blogu: [Extension Sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Sekwencjonowanie rozszerzeń w zestawach skalowania maszyn wirtualnych platformy Azure).

**PYTANIE** Czy zestawy skalowania współdziałają z zestawami dostępności platformy Azure?

**ODPOWIEDŹ** Tak. Zestaw skalowania to niejawny zestaw dostępności z pięcioma domenami błędów i pięcioma domenami aktualizacji. Zestawy skalowania składające się z ponad 100 maszyn wirtualnych obejmują wiele *grup umieszczania*, które są równoważne wielu zestawom dostępności. Aby uzyskać więcej informacji na temat grup umieszczania, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). Zestaw dostępności maszyn wirtualnych może znajdować się w tej samej sieci wirtualnej co zestaw skalowania maszyn wirtualnych. Typowa konfiguracja polega na umieszczeniu maszyn wirtualnych węzła kontrolnego (często wymagających unikatowej konfiguracji) w zestawie dostępności, a węzłów danych w zestawie skalowania.

Więcej odpowiedzi na pytania dotyczące zestawów skalowania można znaleźć w artykule [Zestawy skalowania maszyn wirtualnych platformy Azure — często zadawane pytania](virtual-machine-scale-sets-faq.md).
