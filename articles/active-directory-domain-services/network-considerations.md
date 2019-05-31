---
title: 'Azure AD Domain Services: Wytyczne dotyczące sieci | Dokumentacja firmy Microsoft'
description: Informacje dotyczące sieci usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2010
ms.author: mstephen
ms.openlocfilehash: 1f21d71bba01eb4bec24dbb558a126ecbbd78bbf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246947"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Informacje dotyczące sieci usługi Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Jak wybrać siecią wirtualną platformy Azure
Poniższe wskazówki ułatwiają wybierz sieć wirtualną do korzystania z usług domenowych Azure AD.

### <a name="type-of-azure-virtual-network"></a>Typ sieci wirtualnej platformy Azure
* **Sieci wirtualne usługi Resource Manager**: Azure AD Domain Services, można włączyć w sieciach wirtualnych utworzonych za pomocą usługi Azure Resource Manager.
* Nie można włączyć usługi domenowe Azure AD w klasycznej sieci wirtualnej platformy Azure.
* Łączenie z innymi sieciami wirtualnymi, siecią wirtualną, w której włączono usługi domenowe Azure AD. Aby uzyskać więcej informacji, zobacz [połączenia sieciowego](network-considerations.md#network-connectivity) sekcji.

### <a name="azure-region-for-the-virtual-network"></a>Region platformy Azure dla sieci wirtualnej
* Usług domenowych Azure AD, Twoja domena zarządzana jest wdrożony w tym samym regionie platformy Azure, co sieć wirtualna, do których użytkownik chce włączyć usługę w.
* Wybierz sieć wirtualną w regionie platformy Azure obsługiwane przez usługi domenowe Azure AD.
* Ze strony zawierającej [usługi platformy Azure uporządkowane według regionów](https://azure.microsoft.com/regions/#services/) dowiesz się, w których regionach platformy Azure jest dostępna usługa Azure AD Domain Services.

### <a name="requirements-for-the-virtual-network"></a>Wymagania dotyczące sieci wirtualnej
* **Bliskość obciążeń platformy Azure**: Wybierz sieć wirtualną, która obecnie hostuje/będzie hostować maszyny wirtualne wymagające dostępu do Usług domenowych Azure AD. Obciążenia w przypadku wdrożenia w innej sieci wirtualnej niż domeny zarządzanej, możesz też połączyć sieci wirtualne.
* **Serwery DNS niestandardowe/bring your own**: Upewnij się, że istnieją żadnych niestandardowych serwerów DNS, które są skonfigurowane dla sieci wirtualnej. Przykład niestandardowego serwera DNS jest wystąpieniem DNS systemu Windows Server uruchomiony na maszynie Wirtualnej serwera Windows wdrożonej w sieci wirtualnej. Azure AD Domain Services nie obsługują dowolne niestandardowe serwery DNS, które są wdrażane w ramach sieci wirtualnej.
* **Istniejące domeny, z tą samą nazwą domeny**: Upewnij się, że nie masz istniejącej domeny z tą samą nazwą domeny, dostępne w tej sieci wirtualnej. Na przykład załóżmy, że masz domenę o nazwie „contoso.com” już dostępną w wybranej sieci wirtualnej. Później spróbuj włączyć domeny zarządzanej usług domenowych Azure AD, z tą samą nazwą domeny (czyli "contoso.com") w tej sieci wirtualnej. Wystąpi błąd podczas próby włączenia usług domenowych Azure AD. Ten błąd jest spowodowany konfliktów nazw dla nazwy domeny w tej sieci wirtualnej. W takiej sytuacji musisz użyć innej nazwy podczas konfigurowania domeny zarządzanej Usług domenowych Azure AD. Możesz również anulować aprowizację istniejącej domeny i kontynuować włączanie Usług domenowych Azure AD.

> [!WARNING]
> Nie można przenieść usług domenowych do innej sieci wirtualnej, po włączeniu usługi.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Wskazówki dotyczące wybierania podsieci

![Zalecane podsieci](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Wdrażanie usługi Azure AD Domain Services **oddzielić dedykowanej podsieci** w ramach sieci wirtualnej platformy Azure.
* Czy mają zastosowanie sieciowych grup zabezpieczeń do dedykowanej podsieci dla Twojej domeny zarządzanej. Jeśli do dedykowanej podsieci, należy zastosować sieciowe grupy zabezpieczeń, upewnij się, że **nie blokować porty wymagane do usługi i zarządzanie nimi domenę**.
* Nie ograniczaj nadmiernie liczbę adresów IP w dedykowanej podsieci dla Twojej domeny zarządzanej. To ograniczenie zapobiega udostępnia dwa kontrolery domeny dla domeny zarządzanej usługi.
* **Usług domenowych Azure AD w podsieci bramy nie należy włączać** sieci wirtualnej.

> [!WARNING]
> Po skojarzeniu sieciowej grupy zabezpieczeń z podsiecią, w którym usługi Azure AD Domain Services jest włączona, może zakłócać zdolność firmy Microsoft do usługi i Zarządzanie domeną. Ponadto synchronizacji między dzierżawą usługi Azure AD, a Twoja domena zarządzana jest zakłócona. **Umowa SLA nie ma zastosowania do wdrożeń, w którym sieciowa grupa zabezpieczeń została zastosowana które blokuje usług domenowych Azure AD z aktualizacji i Zarządzanie domeną.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Porty wymagane dla usług domenowych Azure AD
Następujące porty są wymagane dla usług domenowych Azure AD do usługi i obsługa Twojej domeny zarządzanej. Upewnij się, że te porty nie są zablokowane dla podsieci, w którym włączono Twojej domeny zarządzanej.

| Numer portu | Wymagana? | Przeznaczenie |
| --- | --- | --- |
| 443 | Obowiązkowy |Synchronizacja z dzierżawą usługi Azure AD |
| 5986 | Obowiązkowy | Zarządzanie domeny |
| 3389 | Obowiązkowy | Zarządzanie domeny |
| 636 | Opcjonalne | Bezpieczny dostęp protokołu LDAP (LDAPS) do domeny zarządzanej |

**Port 443 (synchronizacji z usługą Azure AD)**
* Służy do synchronizacji katalogu usługi Azure AD z domeny zarządzanej.
* Jest to konieczne, aby zezwolić na dostęp do tego portu w sieciowej grupie zabezpieczeń. Bez dostępu do tego portu Twoja domena zarządzana nie jest zsynchronizowany z katalogiem usługi Azure AD. Użytkownicy mogą nie mieć możliwości zarejestrowania się, jak zmiany hasła nie są zsynchronizowane z domeną zarządzaną.
* Możesz ograniczyć dostęp dla ruchu przychodzącego do tego portu do adresów IP należących do zakresu adresów IP platformy Azure. Należy pamiętać, że zakres adresów IP platformy Azure inny zakres od zakresu programu PowerShell pokazano na poniższej reguły.

**Portu 5986 (komunikacja zdalna programu PowerShell)**
* Służy do wykonywania zadań administracyjnych w domenie zarządzanej przy użyciu komunikacji zdalnej programu PowerShell.
* Jest to konieczne, aby zezwolić na dostęp za pośrednictwem tego portu w sieciowej grupie zabezpieczeń. Bez dostępu do tego portu Twoja domena zarządzana nie może być zaktualizowane, skonfigurowany, kopii zapasowej lub monitorowane.
* Dla nowej domeny lub domen z siecią wirtualną usługi Azure Resource Manager możesz ograniczyć dostęp dla ruchu przychodzącego do tego portu źródłowych adresów IP: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141, 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* Dla domen z klasyczną siecią wirtualną możesz ograniczyć dostęp dla ruchu przychodzącego do tego portu źródłowych adresów IP: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209
* Kontrolery domeny dla domeny zarządzanej nie Nasłuchuj zwykle na tym porcie. Usługa otwiera ten port na kontrolerach domeny zarządzanej, tylko wtedy, gdy operacja zarządzaniem lub konserwacją, należy wykonać dla domeny zarządzanej. Jak najszybciej po zakończeniu tej operacji usługa zamknięcie tego portu, na kontrolerach domeny zarządzanej.

**Port 3389 (pulpitu zdalnego)**
* Służy do połączenia pulpitu zdalnego z kontrolerami domeny dla domeny zarządzanej.
* Możesz ograniczyć dostęp dla ruchu przychodzącego, aby źródłowe adresy IP: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Również portu pozostaje w dużej mierze wyłączony na Twojej domeny zarządzanej. Mechanizm ten nie jest używany w sposób ciągły, ponieważ zarządzanie i monitorowanie zadania są wykonywane przy użyciu komunikacji zdalnej programu PowerShell. Ten port jest używany tylko w rzadkich, firma Microsoft musi łączyć się zdalnie z domeny zarządzanej dla zaawansowanego rozwiązywania problemów. Numer portu jest zamknięty, zaraz po zakończeniu operacji rozwiązywania problemów.

**Portu 636 (protokołu Secure LDAP)**
* Jest on używany umożliwiające dostęp do Twojej domeny zarządzanej secure LDAP przez internet.
* Otwarcie tego portu, za pomocą sieciowa grupa zabezpieczeń jest opcjonalne. Tylko wtedy, gdy masz dostęp protokołu secure LDAP przez internet, włączone, należy otworzyć port.
* Możesz ograniczyć dostęp dla ruchu przychodzącego do tego portu źródłowych adresów IP, z których oczekujesz, że połączenia za pośrednictwem protokołu secure LDAP.


## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
A [grupy zabezpieczeń sieci (NSG)](../virtual-network/virtual-networks-nsg.md) zawiera listę reguł listy kontroli dostępu (ACL), które blokują lub zezwalają na ruch sieciowy do wystąpień maszyn wirtualnych w sieci wirtualnej. Grupy NSG można kojarzyć z podsieciami lub poszczególnymi wystąpieniami maszyn wirtualnych w danej podsieci. Gdy sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, reguły listy ACL dotyczą wszystkich wystąpień maszyn wirtualnych w tej podsieci. Ponadto ruch do poszczególnych maszyn wirtualnych można ograniczyć jeszcze bardziej przez skojarzenie sieciowej grupy zabezpieczeń bezpośrednio do tej maszyny Wirtualnej.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Przykładowe sieciowej grupy zabezpieczeń dla sieci wirtualnych za pomocą usług domenowych Azure AD
W poniższej tabeli przedstawiono przykładowe sieciowej grupy zabezpieczeń można skonfigurować dla sieci wirtualnej z domeną zarządzaną usług domenowych Azure AD. Ta zasada umożliwia ruchu przychodzącego za pośrednictwem portów wymaganych, aby upewnić się, Twoja domena zarządzana pozostaje poprawek, aktualizacji i mogą być monitorowane przez firmę Microsoft. Reguła "DenyAll" domyślna jest stosowana do całego ruchu przychodzącego z Internetu.

Ponadto sieciowej grupy zabezpieczeń ilustruje jak zablokować dostęp protokołu secure LDAP przez internet. Pomiń tę regułę, jeśli nie włączono dostęp protokołu secure LDAP do domeny zarządzanej za pośrednictwem Internetu. Sieciowa grupa zabezpieczeń zawiera zestaw reguł zezwalających na dla ruchu przychodzącego protokołu LDAPS dostęp za pośrednictwem portu TCP 636 tylko z określonego zestawu adresów IP. Reguły sieciowej grupy zabezpieczeń, aby zezwolić na dostęp protokołu LDAPS za pośrednictwem Internetu z określonych adresów IP ma wyższy priorytet niż reguły sieciowej grupy zabezpieczeń DenyAll.

![Przykładowe sieciowej grupy zabezpieczeń z bezpiecznego protokołu LDAPS dostępu przez internet](./media/active-directory-domain-services-alerts/default-nsg.png)

**Więcej informacji na** - [Utwórz sieciową grupę zabezpieczeń](../virtual-network/manage-network-security-group.md).


## <a name="network-connectivity"></a>Połączenie sieciowe
Domeny zarządzanej usług domenowych Azure AD można włączyć tylko w ramach jednej sieci wirtualnej na platformie Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scenariusze łączenia sieci platformy Azure
Łączenie sieci wirtualnych platformy Azure, aby używać domeny zarządzanej, które w żadnym z następujących scenariuszy wdrażania:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Używać domeny zarządzanej w więcej niż jednej sieci wirtualnej platformy Azure
Łączenie z innymi sieciami wirtualnymi platformy Azure, siecią wirtualną platformy Azure, w której włączono usługi domenowe Azure AD. To połączenie komunikacji równorzędnej sieci VPN/sieci wirtualnej można używać domeny zarządzanej przy użyciu obciążenia wdrożone w innych sieciach wirtualnych.

![Połączenia klasycznej sieci wirtualnej](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Użyj następującej domeny zarządzane w sieci wirtualnej usługi Resource Manager
Łączenie z sieci wirtualnej usługi Resource Manager, do platformy Azure klasycznej sieci wirtualnej, w której włączono usługi domenowe Azure AD. To połączenie umożliwia domeny zarządzanej za pomocą obciążenia wdrożone w sieci wirtualnej usługi Resource Manager.

![Menedżer zasobów do łączności z klasyczną siecią wirtualną](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opcje połączeń sieciowych
* **Połączenia sieć wirtualna-sieć wirtualna, używając wirtualnej sieci równorzędnej**: Komunikacja równorzędna sieci wirtualnej to mechanizm, który łączy dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci szkieletowej platformy Azure. Po połączeniu za pomocą komunikacji równorzędnej dwie sieci wirtualne są traktowane jako jedna do wszystkich celów związanych z łącznością. Są one nadal zarządzane jako oddzielne zasoby, ale maszyny wirtualne w tych sieciach wirtualnych mogą komunikować się bezpośrednio przy użyciu prywatnych adresów IP.

    ![Połączenie sieci wirtualnej za pomocą komunikacji równorzędnej](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Więcej informacji — wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md)

* **Połączenia sieć wirtualna-sieć wirtualna za pomocą połączeń sieci VPN typu lokacja lokacja**: Łączenie sieci wirtualnej z inną siecią wirtualną (VNet między sieciami wirtualnymi) jest podobne do procesu łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

    ![Połączenie sieci wirtualnej przy użyciu bramy sieci VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Więcej informacji — łączenie sieci wirtualnych przy użyciu bramy sieci VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Powiązana zawartość
* [Komunikacja równorzędna sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md)
* [Konfigurowanie połączenia między sieciami wirtualnymi dla klasycznego modelu wdrażania](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Grupy zabezpieczeń sieci platformy Azure](../virtual-network/security-overview.md)
* [Utwórz sieciową grupę zabezpieczeń](../virtual-network/manage-network-security-group.md)
