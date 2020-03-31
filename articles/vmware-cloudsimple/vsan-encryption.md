---
title: Rozwiązanie Azure VMware by CloudSimple — konfigurowanie szyfrowania vSAN dla chmury prywatnej
description: W tym artykule opisano sposób konfigurowania funkcji szyfrowania oprogramowania vSAN, aby chmura CloudSimple Private Cloud mogła pracować z serwerem zarządzania kluczami uruchomionym w sieci wirtualnej platformy Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020645"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Konfigurowanie szyfrowania vSAN dla chmury CloudSimple Private Cloud

Funkcję szyfrowania oprogramowania vSAN można skonfigurować, aby chmura CloudSimple Private Cloud mogła współpracować z serwerem zarządzania kluczami działającym w sieci wirtualnej platformy Azure.

Usługa VMware wymaga użycia zewnętrznego narzędzia kmip 1.1 zgodnego z serwerem zarządzania kluczami innych firm (KMS) podczas korzystania z szyfrowania vSAN. Możesz korzystać z obsługiwanych usług zarządzania kluczami, które są certyfikowane przez VMware i są dostępne na platformie Azure.

W tym przewodniku opisano sposób korzystania z funkcji klucza kluczy hytrust działających w sieci wirtualnej platformy Azure. Podobne podejście może być używane dla każdego innego certyfikowanego rozwiązania KMS innych firm dla vSAN.

To rozwiązanie usługi zarządzania kluczami wymaga:

* Instalowanie, konfigurowanie i zarządzanie narzędziem kms certyfikowanym przez firmę VMware innej firmy w sieci wirtualnej platformy Azure.
* Podaj własne licencje dla narzędzia usługi zarządzania kluczami.
* Konfigurowanie szyfrowania vSAN i zarządzanie nim w chmurze prywatnej przy użyciu narzędzia usługi zarządzania usługami innych firm działającego w sieci wirtualnej platformy Azure.

## <a name="kms-deployment-scenario"></a>Scenariusz wdrażania usługi zarządzania kluczami

Klaster serwerów usługi zarządzania kluczami działa w sieci wirtualnej platformy Azure i jest dostępny dla adresów IP z centrum wirtualnego private cloud za pośrednictwem skonfigurowanego połączenia Usługi Azure ExpressRoute.

![.. /media/klaster usługi zarządzania kluczami w sieci wirtualnej platformy Azure](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

Proces wdrażania ma następujące kroki:

1. [Sprawdź, czy spełnione są wymagania wstępne](#verify-prerequisites-are-met)
2. [Portal CloudSimple: uzyskiwanie informacji o komunikacji równorzędnej usługi ExpressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Portal Azure: Łączenie sieci wirtualnej z chmurą prywatną](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Portal Azure: Wdrażanie klastra hytrust keycontrol w sieci wirtualnej](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Konfigurowanie serwera KMIP](#hytrust-webui-configure-the-kmip-server)
6. [Interfejs użytkownika vCenter: konfigurowanie szyfrowania vSAN do używania klastra usługi zarządzania kluczami w sieci wirtualnej platformy Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Sprawdź, czy spełnione są wymagania wstępne

Przed wdrożeniem sprawdź następujące kwestie:

* Wybrany dostawca, narzędzie i wersja usługi zarządzania kluczami znajdują się na liście zgodności z vSAN.
* Wybrany dostawca obsługuje wersję narzędzia do uruchomienia na platformie Azure.
* Wersja narzędzia usługi zarządzania kluczami platformy Azure jest zgodna z programem KMIP 1.1.
* Usługa Azure Resource Manager i sieć wirtualna są już utworzone.
* CloudSimple Private Cloud jest już utworzony.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portal CloudSimple: uzyskiwanie informacji o komunikacji równorzędnej usługi ExpressRoute

Aby kontynuować konfigurację, potrzebujesz klucza autoryzacji i identyfikatora URI obwodu równorzędnego dla usługi ExpressRoute oraz dostępu do subskrypcji platformy Azure. Te informacje są dostępne na stronie Połączenie sieci wirtualnej w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia sieci wirtualnej z chmurą prywatną](virtual-network-connection.md). Jeśli masz jakiekolwiek problemy z uzyskaniem informacji, otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Portal Azure: Łączenie sieci wirtualnej z chmurą prywatną

1. Utwórz bramę sieci wirtualnej dla sieci wirtualnej, postępując zgodnie z instrukcjami w [temacie Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu portalu Azure.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
2. Połącz sieć wirtualną z obwódem CloudSimple ExpressRoute, postępując zgodnie z instrukcjami w [aplikacji Podłącz sieć wirtualną do obwodu usługi ExpressRoute za pomocą portalu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Użyj informacji o obwodzie CloudSimple ExpressRoute otrzymanych w powitalnej wiadomości e-mail od CloudSimple, aby połączyć swoją sieć wirtualną z obwódem CloudSimple ExpressRoute na platformie Azure.
4. Wprowadź klucz autoryzacji i identyfikator URI obwodu równorzędnego, nadaj nazwę połączeniu i kliknij przycisk **OK**.

![Podaj identyfikator URI obwodu równorzędnego CS podczas tworzenia sieci wirtualnej](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Witryna Azure portal: wdrażanie klastra HyTrust KeyControl w usłudze Azure Resource Manager w sieci wirtualnej

Aby wdrożyć klaster HyTrust KeyControl w usłudze Azure Resource Manager w sieci wirtualnej, wykonaj następujące zadania. Szczegółowe informacje można znaleźć w [dokumentacji HyTrust.](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

1. Utwórz grupę zabezpieczeń sieci platformy Azure (nsg-hytrust) z określonymi regułami przychodzącymi, postępując zgodnie z instrukcjami w dokumentacji HyTrust.
2. Generowanie pary kluczy SSH na platformie Azure.
3. Wdrażanie początkowego węzła KeyControl z obrazu w portalu Azure Marketplace.  Użyj klucza publicznego pary kluczy, która została wygenerowana, i wybierz **nsg-hytrust** jako grupę zabezpieczeń sieci dla węzła KeyControl.
4. Konwertuj prywatny adres IP KeyControl na statyczny adres IP.
5. SSH do keycontrol maszyny Wirtualnej przy użyciu jego publiczny adres IP i klucz prywatny wcześniej wymienione pary kluczy.
6. Po wyświetleniu monitu w `No` powłoce SSH wybierz, aby ustawić węzeł jako początkowy węzeł KeyControl.
7. Dodaj dodatkowe węzły KeyControl, powtarzając kroki 3-5 `Yes` tej procedury i wybierając po wyświetleniu monitu o dodanie do istniejącego klastra.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Konfigurowanie serwera KMIP

Przejdź do https://*public-ip*, gdzie *public-ip* jest publiczny adres IP węzła KeyControl VM. Wykonaj następujące kroki z [dokumentacji HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Konfigurowanie serwera KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Tworzenie pakietu certyfikatów dla szyfrowania VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>Interfejs użytkownika vCenter: konfigurowanie szyfrowania vSAN do używania klastra usługi zarządzania kluczami w sieci wirtualnej platformy Azure

Postępuj zgodnie z instrukcjami HyTrust, aby [utworzyć klaster usługi zarządzania kluczami w centrum vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Dodawanie szczegółów klastra usługi zarządzania kluczami w centrum vCenter](media/vsan-config01.png)

W obszarze vCenter przejdź do **programu Cluster > Configure** i wybierz opcję **Ogólne** dla vSAN. Włącz szyfrowanie i wybierz klaster usługi zarządzania kluczami, który został wcześniej dodany do vCenter.

![Włączanie szyfrowania vSAN i konfigurowanie klastra usługi zarządzania kluczami w centrum vCenter](media/vsan-config02.png)

## <a name="references"></a>Dokumentacja

### <a name="azure"></a>Azure

[Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu portalu Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Nawiązywanie połączenia sieci wirtualnej z obwodem usługi ExpressRoute za pomocą witryny Portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust (HyTrust)

[HyTrust DataControl i Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Konfigurowanie serwera KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Tworzenie pakietu certyfikatów dla szyfrowania VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Tworzenie klastra usługi zarządzania kluczami w vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
