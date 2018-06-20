---
title: 'Usługi domenowe Azure AD: Wskazówki dotyczące sieci | Dokumentacja firmy Microsoft'
description: Zagadnienia dotyczące sieci dla usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: bb69c217c1038a66333e65629023357e5854d242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265048"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Zagadnienia dotyczące sieci dla usług domenowych Azure AD
## <a name="how-to-select-an-azure-virtual-network"></a>Jak wybrać sieć wirtualną platformy Azure
Poniższe wskazówki ułatwiają wybieranie sieci wirtualnej do korzystania z usług domenowych Azure AD.

### <a name="type-of-azure-virtual-network"></a>Typ sieci wirtualnej platformy Azure
* **Sieci wirtualne usługi Resource Manager**: można włączyć usługi domenowe Azure AD w sieciach wirtualnych utworzonych przy użyciu usługi Azure Resource Manager.
* Nie można włączyć usługi domenowe Azure AD w klasycznej sieci wirtualnej platformy Azure.
* Można połączyć innych sieci wirtualnych do sieci wirtualnej, w której włączono usługi domenowe Azure AD. Aby uzyskać więcej informacji, zobacz [połączeń sieciowych](active-directory-ds-networking.md#network-connectivity) sekcji.

### <a name="azure-region-for-the-virtual-network"></a>Region platformy Azure dla sieci wirtualnej
* Usługi domenowe Azure AD domeny zarządzanej jest wdrażana w tym samym regionie Azure, co sieć wirtualna wybierzesz opcję Włącz usługę w.
* Wybierz sieć wirtualną w regionie platformy Azure obsługiwane przez usługi domenowe Azure AD.
* Ze strony zawierającej [usługi platformy Azure uporządkowane według regionów](https://azure.microsoft.com/regions/#services/) dowiesz się, w których regionach platformy Azure jest dostępna usługa Azure AD Domain Services.

### <a name="requirements-for-the-virtual-network"></a>Wymagania dotyczące sieci wirtualnej
* **Zbliżeniowe obciążeniom Azure**: Wybierz sieć wirtualną, która obecnie hostuje/będzie hostować maszyny wirtualne, które wymagają dostępu do usług domenowych Azure AD. W przypadku wdrożenia w innej sieci wirtualnej niż domeny zarządzanej obciążeń można także połączyć sieci wirtualnych.
* **Niestandardowe/bring your own serwerów DNS**: Sprawdź, czy żadnych niestandardowych serwerów DNS, które są skonfigurowane dla sieci wirtualnej. Przykład niestandardowy serwer DNS jest wystąpieniem DNS systemu Windows Server uruchomiony na maszynę Wirtualną serwera systemu Windows, która została wdrożona w sieci wirtualnej. Usługi domenowe Azure AD nie obsługują żadnych niestandardowych serwerów DNS, które są wdrożone w ramach sieci wirtualnej.
* **Domen z tą samą nazwą domeny**: Upewnij się, że nie masz istniejącej domeny o takiej samej nazwie domeny, dostępne w tej sieci wirtualnej. Na przykład załóżmy, że masz domenę o nazwie „contoso.com” już dostępną w wybranej sieci wirtualnej. Później spróbuj włączyć domeny zarządzanej usług domenowych Azure AD z tą samą nazwą domeny (który jest "contoso.com") w tej sieci wirtualnej. Wystąpi awaria podczas próby włączenia usług domenowych Azure AD. Ten błąd jest spowodowany konfliktów nazw dla nazwy domeny w sieci wirtualnej. W takiej sytuacji musisz użyć innej nazwy podczas konfigurowania domeny zarządzanej Usług domenowych Azure AD. Możesz również anulować aprowizację istniejącej domeny i kontynuować włączanie Usług domenowych Azure AD.

> [!WARNING]
> Nie można przenieść usług domenowych do innej sieci wirtualnej, po włączeniu usługi.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Wskazówki dotyczące wybierania podsieci

![Zalecane podsieci](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Wdrażanie usług domenowych Azure AD do **oddzielnych dedykowanych podsieci** w ramach sieci wirtualnej platformy Azure.
* Nie dotyczą grup NSG podsieci dedykowane dla domeny zarządzanej. Jeśli należy zastosować grupy NSG do podsieci dedykowanych, upewnij się, możesz **nie blokować porty wymagane do usługi i zarządzać domenę**.
* Nie ograniczaj zbyt liczba adresów IP dostępne w ramach dedykowanej podsieci dla domeny zarządzanej. To ograniczenie uniemożliwia udostępnianie dwa kontrolery domeny do domeny zarządzanej usługi.
* **Usługi domenowe Azure AD w podsieci bramy nie należy włączać** z sieci wirtualnej.
* Nie należy blokować dostęp ruchu wychodzącego z podsieci, w którym włączono domeny zarządzanej.

> [!WARNING]
> Po skojarzeniu grupy NSG z podsiecią, w którym usługi domenowe Azure AD jest włączona, może zakłócić zdolność firmy Microsoft do usługi i Zarządzanie domeną. Ponadto synchronizacja dzierżawy usługi Azure AD i domeny zarządzanej jest zakłócona. **Umowa SLA nie ma zastosowania do wdrożeń, w którym grupy NSG zastosowano blokujący usług domenowych Azure AD z aktualizacji i Zarządzanie domeną.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Porty wymagane przez usługi domenowe Azure AD
Następujące porty są wymagane dla usług domenowych Azure AD do usługi i obsługa domeny zarządzanej. Upewnij się, że te porty nie są blokowane dla podsieci, w którym włączono domeny zarządzanej.

| Numer portu | Wymagana? | Przeznaczenie |
| --- | --- | --- |
| 443 | Obowiązkowy |Synchronizacja z dzierżawy usługi Azure AD |
| 5986 | Obowiązkowy | Zarządzanie domeny |
| 3389 | Obowiązkowy | Zarządzanie domeny |
| 636 | Optional (Opcjonalność) | Bezpieczny dostęp protokołu LDAP (LDAPS) do domeny zarządzanej |

**Port 443 (synchronizacji z usługą Azure AD)**
* Służy do synchronizacji katalogu usługi Azure AD z domeny zarządzanej.
* Jest to konieczne, aby umożliwić dostęp do tego portu w Twojej NSG. Bez dostępu do tego portu domeny zarządzanej nie jest zsynchronizowana z katalogiem Azure AD. Użytkownicy nie można zalogować się jako zmiany hasła nie są synchronizowane z domeny zarządzanej.
* Do tego portu do adresów IP należących do zakresu adresów IP platformy Azure, można ograniczyć dostęp dla ruchu przychodzącego. Należy pamiętać, że zakres adresów Azure IP inny zakres niż zakres programu PowerShell pokazano w poniższej reguły.

**Portu 5986 (obsługę zdalną środowiska PowerShell)**
* Służy do wykonywania zadań zarządzania w domenie zarządzanej przy użyciu komunikacji zdalnej programu PowerShell.
* Jest to konieczne, aby zezwolić na dostęp za pośrednictwem tego portu w Twojej NSG. Bez dostępu do tego portu domeny zarządzanej nie może być zaktualizowany, skonfigurowany, kopii zapasowej lub monitorowanych.
* Dla nowych domenach lub domenach z sieci wirtualnej platformy ARM, można ograniczyć dostęp dla ruchu przychodzącego dla tego portu do źródłowych adresów IP: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141, 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* W przypadku domen z klasycznej sieci wirtualnej można ograniczyć dostęp dla ruchu przychodzącego dla tego portu do źródłowych adresów IP: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* Kontrolery domeny dla domeny zarządzanej nie Nasłuchuj zwykle na tym porcie. Tylko wtedy, gdy operacja zarządzania lub konserwacji musi zostać wykonana dla domeny zarządzanej usługi zostanie otwarty ten port na zarządzanych kontrolerach domeny. Zaraz po zakończeniu operacji, Usługa zamyka tego portu, na kontrolerach domeny zarządzanej.

**Port 3389 (pulpitu zdalnego)**
* Służy do obsługi połączeń pulpitu zdalnego do kontrolerów domeny dla domeny zarządzanej.
* Możesz ograniczyć dostęp przychodzący do źródłowych adresów IP: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Również portu pozostanie wyłączony przede wszystkim na domeny zarządzanej. Ten mechanizm nie jest używany w sposób ciągły, ponieważ do zarządzania i monitorowania zadań są wykonywane przy użyciu komunikacji zdalnej programu PowerShell. Port ten jest używany tylko w rzadkich, firma Microsoft będzie potrzebowała nawiązywanie połączeń zdalnych do domeny zarządzanej do zaawansowanego rozwiązywania problemów. Port jest zamknięty, natychmiast po zakończeniu operacji rozwiązywania problemów.

**Port 636 bezpiecznego protokołu LDAP)**
* Służy do włączyć bezpieczny dostęp LDAP do domeny zarządzanej za pośrednictwem Internetu.
* Otwarcie tego portu za pośrednictwem sieci NSG jest opcjonalne. Otwórz port tylko wtedy, gdy masz bezpiecznego dostępu LDAP w Internecie włączone.
* Do tego portu źródłowych adresów IP, z których można było się spodziewać nawiązywać połączeń za pośrednictwem bezpiecznego protokołu LDAP, można ograniczyć dostęp dla ruchu przychodzącego.

**Wychodzący dostęp** usług domenowych w usłudze AAD musi wychodzący dostęp do różnych innych usług platformy Azure w celu zarządzania, kopii zapasowej i monitorować domeny zarządzanej. Nie należy blokować wychodzący dostęp z dedykowanych podsieci, w którym włączono domeny zarządzanej.


## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
A [grupy zabezpieczeń sieci (NSG)](../virtual-network/security-overview.md) zawiera listę reguł listy kontroli dostępu (ACL), które akceptować lub odrzucać ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto ruch do poszczególnych maszyn wirtualnych można ograniczyć jeszcze bardziej przez skojarzenie grupy NSG bezpośrednio z tą maszyną Wirtualną.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Przykład grupy NSG dla sieci wirtualnych z usług domenowych Azure AD
W poniższej tabeli przedstawiono przykład grupy NSG można skonfigurować sieć wirtualną z domeny zarządzanej usług domenowych Azure AD. Ta zasada umożliwia ruch przychodzący przez wymagane porty, aby upewnić się, Twoje pozostaje domeny zarządzanej poprawkami, aktualizacji i mogą być monitorowane przez firmę Microsoft. Domyślna reguła "DenyAll" dotyczy cały ruch przychodzący z Internetu.

Ponadto grupa NSG również ilustruje sposób zablokowanie bezpiecznego dostępu LDAP w Internecie. Pomiń tę regułę, jeśli nie włączono bezpiecznego dostępu LDAP do domeny zarządzanej za pośrednictwem Internetu. Grupa NSG zawiera zestaw reguł, które zezwolić na przychodzący LDAPS dostęp za pośrednictwem portu TCP 636 tylko z określonego zestawu adresów IP. Reguły NSG umożliwiają LDAPS dostęp przez internet z określonych adresów IP ma wyższy priorytet niż reguły DenyAll NSG.

![Przykład grupy NSG bezpieczny dostęp do LDAPS w Internecie](.\media\active-directory-domain-services-alerts\default-nsg.png)

**Więcej informacji** - [Utwórz grupę zabezpieczeń sieci](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Połączenie sieciowe
Domeny zarządzanej usług domenowych Azure AD można włączyć tylko w obrębie jednej sieci wirtualnych na platformie Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scenariusze dotyczące łączenia sieci platformy Azure
Połączenie sieci wirtualnych platformy Azure, aby użyć domeny zarządzanej w jednym z następujących scenariuszy wdrażania:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Użyj domeny zarządzanej w więcej niż jedną sieć wirtualną platformy Azure
Sieć wirtualna platformy Azure, w której włączono usługi domenowe Azure AD umożliwia nawiązywanie innych sieci wirtualnych platformy Azure. Połączenia sieci VPN/wirtualnej komunikacji równorzędnej umożliwia domeny zarządzanej za pomocą obciążeń wdrożonych w innych sieciach wirtualnych.

![Łączność klasycznej sieci wirtualnej](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Użyj domeny zarządzanej w sieci wirtualnej Resource Manager
Azure classic sieci wirtualnej, w której włączono usługi domenowe Azure AD umożliwia nawiązywanie sieci wirtualnych Menedżera zasobów. To połączenie umożliwia domeny zarządzanej za pomocą obciążeń wdrożonych w sieci wirtualnej na podstawie Menedżera zasobów.

![Menedżer zasobów z łącznością klasycznej sieci wirtualnej](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opcje połączeń sieciowych
* **Aby wirtualnymi połączenia wirtualnej sieci komunikacji równorzędnej**: sieci wirtualnej komunikacji równorzędnej jest mechanizm, który łączy dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci Azure sieci szkieletowej. Po połączeniu za pomocą komunikacji równorzędnej dwie sieci wirtualne są traktowane jako jedna do wszystkich celów związanych z łącznością. Są one nadal zarządzane jako oddzielne zasoby, ale maszyny wirtualne w tych sieciach wirtualnych mogą komunikować się bezpośrednio przy użyciu prywatnych adresów IP.

    ![Łączność sieci wirtualnej za pomocą komunikacji równorzędnej](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Więcej informacji - wirtualnych sieci komunikacji równorzędnej](../virtual-network/virtual-network-peering-overview.md)

* **Wirtualnymi do połączeń przy użyciu połączeń VPN lokacja lokacja**: łączenie sieci wirtualnej do innej sieci wirtualnej (aby wirtualnymi) jest podobny do łączenia sieci wirtualnej do lokalizacji lokalnej. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

    ![Łączność sieci wirtualnej przy użyciu bramy sieci VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Więcej informacji - połączyć sieci wirtualnych za pomocą bramy sieci VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Powiązana zawartość
* [Komunikacja równorzędna sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md)
* [Konfigurowanie połączenia do wirtualnymi klasycznym modelu wdrażania](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Grupy zabezpieczeń sieci platformy Azure](../virtual-network/security-overview.md)
* [Utwórz grupę zabezpieczeń sieci](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
