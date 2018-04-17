---
title: Rozwiązywanie problemów z grup zabezpieczeń sieci - Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z grup zabezpieczeń sieci w modelu wdrażania usługi Azure Resource Manager przy użyciu portalu Azure.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: b053993bec74d358dd7a0a8889fa05885b563b16
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Rozwiązywanie problemów z grup zabezpieczeń sieci przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [Program PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Jeśli skonfigurowana grup zabezpieczeń sieci (NSG) na maszynie wirtualnej (VM) i występują problemy z połączeniem maszyny Wirtualnej, ten artykuł zawiera omówienie funkcji diagnostyki dla grup NSG do dalszego rozwiązywania.

Grupy NSG umożliwiają kontrolę typów ruchu przepływające i maszyn wirtualnych (VM). Grupy NSG można zastosować do podsieci w sieci wirtualnej platformy Azure (VNet) i/lub interfejsów sieciowych (NIC). Skuteczne zasady stosowane do karty Sieciowej są agregacji reguł, które istnieją w grup NSG stosowana do karty Sieciowej i podsieci, w której jest dołączona do. Reguły w tych grup NSG można czasami konflikt ze sobą i mieć wpływ na łączność sieciową z maszyny Wirtualnej.  

Można wyświetlić wszystkie reguły efektywnym elementem systemu zabezpieczeń z grup NSG, jak stosować kart sieciowych maszyny Wirtualnej. W tym artykule pokazano, jak rozwiązywać problemy z połączeniem maszyny Wirtualnej przy użyciu tych reguł w modelu wdrażania usługi Azure Resource Manager. Jeśli nie znasz z sieci wirtualnej i NSG pojęcia, przeczytaj [sieci wirtualnej](virtual-networks-overview.md) i [sieciowej grupy zabezpieczeń](virtual-networks-nsg.md) omówienie artykułów.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Rozwiązywanie problemów z przepływem ruchu maszyny Wirtualnej za pomocą skuteczne reguły zabezpieczeń
Scenariusz, który następuje jest przykładem to powszechny problem połączenia:

Maszyna wirtualna o nazwie *VM1* jest częścią podsieci o nazwie *podsieć1* w ramach sieci wirtualnej o nazwie *WestUS VNet1*. Próba nawiązania połączenia z maszyną wirtualną za pomocą protokołu RDP za pośrednictwem portu 3389 protokołu TCP nie powiedzie się. Grupy NSG są stosowane w obu karty interfejsu Sieciowego *VM1 NIC1* i podsieć *podsieć1*. Ruch do portu 3389 protokołu TCP jest dozwolona w grupie NSG skojarzone z interfejsem sieciowym *VM1 NIC1*, ale TCP ping VM1 do portu 3389 kończy się niepowodzeniem.

Podczas tego przykładu korzysta z portu 3389 protokołu TCP, następujące czynności może służyć do określenia błędów połączenia przychodzącego i wychodzącego przez dowolnego portu.

### <a name="vm"></a>Wyświetl reguły efektywnym elementem systemu zabezpieczeń dla maszyny wirtualnej
Wykonaj poniższe kroki, aby rozwiązać grup NSG dla maszyny Wirtualnej:

Pełna lista reguł efektywnym elementem systemu zabezpieczeń można wyświetlić karty sieciowej, z samej maszyny Wirtualnej. Można także dodawanie, modyfikowanie i usuwanie reguły NSG podsieci i karty w bloku skuteczne reguły, jeśli masz uprawnienia do wykonania tych operacji.

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com przy użyciu konta platformy Azure. Konto musi mieć przypisaną *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* operacji interfejsu sieciowego. Aby dowiedzieć się, jak przypisać operacje do kont, zobacz [Tworzenie niestandardowych ról dla kontroli dostępu](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Kliknij przycisk **wszystkie usługi**, następnie kliknij przycisk **maszyn wirtualnych** na liście.
3. Wybierz maszynę Wirtualną, aby rozwiązać problemy z listy, która pojawia się i zostanie wyświetlony blok maszyny Wirtualnej, z opcjami.
4. Kliknij przycisk **Diagnozuj & rozwiązywania problemów** , a następnie wybierz powszechny problem. Na przykład **nie mogę połączyć się z maszyną Wirtualną z systemem Windows** jest zaznaczone. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Kroki są wyświetlane w obszarze ten problem, jak pokazano na poniższej ilustracji: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Kliknij przycisk *efektywnym elementem systemu zabezpieczeń, zasady grupy* na liście zalecanych kroków.
6. **Pobieranie reguł efektywnym elementem systemu zabezpieczeń** zostanie wyświetlony blok, jak pokazano na poniższej ilustracji:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Zwróć uwagę sekcje obrazu:
   
   * **Zakres:** ustawioną *VM1*, maszyny Wirtualnej wybrany w kroku 3.
   * **Interfejs sieciowy:** *VM1 NIC1* jest zaznaczone. Maszyna wirtualna może mieć wiele interfejsów sieciowych (NIC). Poszczególne karty Sieciowe mogą mieć unikatowe efektywnym elementem systemu zabezpieczeń reguły. Podczas rozwiązywania problemu, należy wyświetlić reguły efektywnym elementem systemu zabezpieczeń dla poszczególnych kart sieciowych.
   * **Grupy NSG skojarzone:** grupy NSG można zastosować do karty Sieciowej i podsieci, karta sieciowa jest połączona. Na ilustracji grupy NSG zostały zastosowane do kartę Sieciową i podsieć, z którą jest połączona. Możesz kliknąć nazwy grupy NSG bezpośrednio zmodyfikować zasady grup NSG.
   * **Karta VM1 nsg:** listę reguł w zdjęć jest grupa NSG stosowana do karty sieciowej. Kilka reguł domyślnych są tworzone przez usługę Azure zawsze, gdy grupa NSG jest tworzona. Nie można usunąć reguły domyślne, ale można je zastąpić przy użyciu reguł o wyższym priorytecie. Aby dowiedzieć się więcej na temat reguł domyślnych, przeczytaj [omówienie NSG](virtual-networks-nsg.md#default-rules) artykułu.
   * **Kolumna docelowa:** reguł zawiera tekst w kolumnie, podczas gdy inne muszą prefiksy adresów. Tekst jest nazwą znaczniki domyślne stosowane do reguły zabezpieczeń podczas jej tworzenia. Tagi są dostarczane przez system identyfikatorów, które reprezentują wiele prefiksów. Wybranie reguły za pomocą tagów, takich jak *AllowInternetOutBound*, zawiera listę prefiksów **prefiksy adresów** bloku.
   * **Pobieranie:** listę reguł może trwać długo. Plik CSV reguł dla analizy offline można pobrać po kliknięciu **Pobierz** i zapisać plik.
   * **AllowRDP** reguły dla ruchu przychodzącego: ta zasada umożliwia połączeń protokołu RDP z maszyny Wirtualnej.
7. Kliknij przycisk **NSG podsieć1** kartę, aby wyświetlić skuteczne reguły z Grupa NSG stosowana do podsieci, jak pokazano na poniższej ilustracji: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Powiadomienie *denyRDP* **przychodzący** reguły. Reguły ruchu przychodzącego w podsieci są obliczane przed zasady stosowane w interfejsie sieciowym. Ponieważ w tej podsieci jest stosowana reguła odmowy, żądanie połączenia do 3389 protokołu TCP nie powiedzie się, ponieważ nigdy nie jest obliczane regułą Zezwalaj umieszczoną karty interfejsu Sieciowego. 
   
    *DenyRDP* reguły jest powód, dlaczego połączenie RDP kończy się niepowodzeniem. Usunięcie go powinno rozwiązać problem.
   
   > [!NOTE]
   > Jeśli skojarzona z kartą Sieciową maszyny Wirtualnej nie jest w stanie uruchomionym lub grupy NSG nie zostały zastosowane do karty Sieciowej lub podsieci, są wyświetlane żadne reguły.
   > 
   > 
8. Aby edytować reguły NSG, kliknij przycisk *NSG podsieć1* w **skojarzonych grup NSG** sekcji.
   Spowoduje to otwarcie **NSG podsieć1** bloku. Reguły można bezpośrednio edytować, klikając **reguły zabezpieczeń dla ruchu przychodzącego**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Po usunięciu *denyRDP* reguły dla ruchu przychodzącego w **NSG podsieć1** i dodawanie *allowRDP* reguły obowiązującą reguł listy wygląda jak poniżej:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Upewnij się, że portu 3389 protokołu TCP jest otwarty przez otwarcie połączenia RDP do maszyny Wirtualnej lub przy użyciu narzędzia PsPing narzędzia. Użytkownik może dowiedzieć się więcej o narzędzia PsPing odczytując [stronę pobierania narzędzia PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Wyświetl reguły efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego
Jeśli do ruchu maszyny Wirtualnej jest w pełni funkcjonalne dla określonych kart interfejsu Sieciowego, można wyświetlić pełną listę skuteczne reguły do karty Sieciowej z kontekstu interfejsów sieciowych, wykonując następujące czynności:

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com.
2. Kliknij przycisk **wszystkie usługi**, następnie kliknij przycisk **interfejsy sieciowe** na liście.
3. Wybierz interfejs sieciowy. Na poniższej ilustracji, karta sieciowa o nazwie *VM1 NIC1* jest zaznaczone.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Zwróć uwagę, że **zakres** ustawiono wybrany interfejs sieciowy. Aby dowiedzieć się więcej o dodatkowe informacje wyświetlane, przeczytaj kroku 6 **Rozwiązywanie problemów z grup NSG dla maszyny Wirtualnej** sekcji tego artykułu.
   
   > [!NOTE]
   > Usunięcie grupy NSG z interfejsem sieciowym podsieci grupa NSG jest nadal skuteczne w danej karty sieciowej. W takim przypadku dane wyjściowe będą wyświetla tylko reguły NSG podsieci. Reguły są wyświetlane tylko, jeśli karta sieciowa jest podłączona do maszyny Wirtualnej.
   > 
   > 
4. Zasady można edytować bezpośrednio do grupy zabezpieczeń sieci skojarzonych z karty Sieciowej i podsieci. Aby dowiedzieć się, jak to zrobić, przeczytaj kroku 8 **wyświetlić reguły efektywnym elementem systemu zabezpieczeń dla maszyny wirtualnej** sekcji tego artykułu.

## <a name="nsg"></a>Wyświetlanie efektywnym elementem systemu zabezpieczeń reguł sieciowej grupy zabezpieczeń (NSG)
Podczas modyfikowania reguły NSG, można przejrzeć wpływu reguły dodawany na określonej maszynie Wirtualnej. Pełna lista reguł efektywnym elementem systemu zabezpieczeń można wyświetlić wszystkie karty sieciowe obsługujące danego grupa NSG jest stosowana do, bez konieczności przełączania kontekstu w danym bloku NSG. Rozwiązywać skuteczne reguły w obrębie grupy NSG, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure pod adresem https://portal.azure.com.
2. Kliknij przycisk **wszystkie usługi**, następnie kliknij przycisk **sieciowej grupy zabezpieczeń** na liście.
3. Wybierz grupy NSG. Na poniższej ilustracji grupy NSG o nazwie grupy nsg VM1 został wybrany.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Zwróć uwagę sekcje poprzedniego obrazu:
   
   * **Zakres:** ustawioną NSG wybrane.
   * **Maszyna wirtualna:** gdy grupa NSG jest stosowana do podsieci, nie jest stosowana dla wszystkich interfejsów sieciowych dołączonych do wszystkich maszyn wirtualnych podłączonych do podsieci. Ta lista zawiera ta grupa NSG jest stosowana do wszystkich maszyn wirtualnych. Żadnej maszyny Wirtualnej można wybrać z listy.
     
     > [!NOTE]
     > Jeśli grupa NSG jest stosowana tylko puste podsieci, maszyny wirtualne nie są wyświetlane. Jeśli grupa NSG jest stosowana do karty Sieciowej, która nie jest skojarzona z maszyną Wirtualną, te karty sieciowe również nie są wyświetlane. 
     > 
     > 
   * **Interfejs sieciowy:** maszyny Wirtualnej może mieć wiele interfejsów sieciowych. Możesz wybrać karty sieciowej podłączony do wybranej maszynie Wirtualnej.
   * **AssociatedNSGs:** w dowolnym momencie, karta sieciowa może mieć maksymalnie dwóch skuteczne grup NSG, jeden stosowany do karty Sieciowej, a drugą do podsieci. Mimo że zakres został wybrany jako grupa nsg VM1, jeśli karta NIC ma podsieć skuteczne NSG, dane wyjściowe zostaną wyświetlone zarówno grupy NSG.
4. Zasady można edytować bezpośrednio do grupy zabezpieczeń sieci skojarzonych z karty Sieciowej lub podsieci. Aby dowiedzieć się, jak to zrobić, przeczytaj kroku 8 **wyświetlić reguły efektywnym elementem systemu zabezpieczeń dla maszyny wirtualnej** sekcji tego artykułu.

Aby dowiedzieć się więcej o dodatkowe informacje wyświetlane, przeczytaj kroku 6 **wyświetlić reguły efektywnym elementem systemu zabezpieczeń dla maszyny wirtualnej** sekcji tego artykułu.

> [!NOTE]
> Chociaż podsieci i karty Sieciowej można każdego mieć tylko jedną grupę NSG zastosowanych do nich, grupy NSG można skojarzyć wielu kart sieciowych i wielu podsieci.
> 
> 

## <a name="considerations"></a>Zagadnienia do rozważenia
Podczas rozwiązywania problemów z łącznością, należy wziąć pod uwagę następujące kwestie:

* Reguły NSG domyślne zostanie blokowanie dostępu przychodzące z Internetu i zezwolić tylko sieci wirtualnej ruch przychodzący. Zasady należy jawnie dodać udostępnianie przychodzące z Internetu, zgodnie z wymaganiami.
* Jeśli nie żadnych reguł zabezpieczeń grupy NSG powoduje niepowodzenie łączności sieciowej maszyny Wirtualnej, problem może być z powodu:
  * Oprogramowanie zapory działających w systemie operacyjnym maszyny Wirtualnej
  * Trasy skonfigurowanych dla urządzenia wirtualnego lub ruch lokalny. Ruch internetowy mogą zostać przekierowane do lokalnego przy użyciu tunelowania wymuszonego. Połączenia protokołu RDP/SSH z Internetu do maszyny Wirtualnej może nie działać to ustawienie, w zależności od tego, jak sprzęt sieci lokalnej obsługuje ten ruch. Odczyt [Rozwiązywanie problemów z tras](virtual-network-routes-troubleshoot-powershell.md) artykuł, aby dowiedzieć się, jak diagnozować problemy trasy, które mogą utrudnić przepływu ruchu i z maszyny Wirtualnej. 
* Domyślnie ma połączyć za pomocą sieci wirtualnych, VIRTUAL_NETWORK tag zostanie automatycznie rozwiń węzeł aby objąć prefiksy połączyć za pomocą sieci wirtualnych. Możesz wyświetlić te prefiksy **ExpandedAddressPrefix** listy, aby rozwiązać problemy związane z łącznością komunikacji równorzędnej sieci wirtualnej. 
* Reguły efektywnym elementem systemu zabezpieczeń są wyświetlane tylko jeśli jest grupy NSG skojarzonej z maszyny Wirtualnej karty Sieciowej i podsieci. 
* Jeśli nie ma żadnych grup NSG skojarzona z kartą Sieciową lub w podsieci i publicznego adresu IP przypisane do maszyny Wirtualnej, wszystkie porty jest otwarte dla przychodzący i wychodzący dostęp. Jeśli maszyna wirtualna ma publiczny adres IP, zdecydowanie zalecane jest zastosowanie grup NSG do kart Sieciowych lub podsieci.

