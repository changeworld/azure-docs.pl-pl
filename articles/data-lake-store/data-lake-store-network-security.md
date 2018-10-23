---
title: Zabezpieczenia sieci w usłudze Azure Data Lake Storage Gen1 | Microsoft Docs
description: Opis sposobu działania zapory IP i integracji z siecią wirtualną w usłudze Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168190"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Integracja z siecią wirtualną w usłudze Azure Data Lake Storage Gen1 — wersja zapoznawcza

Przedstawiamy integrację z siecią wirtualną w usłudze Azure Data Lake Storage Gen1 (w wersji zapoznawczej). Integracja z siecią wirtualną zapobiega nieautoryzowanemu dostępowi do kont usługi Azure Data Lake Storage Gen1 przez ograniczenie dostępu do tych kont tylko do określonych sieci wirtualnych i podsieci. Można teraz skonfigurować konto usługi ADLS Gen1 tak, aby zezwalać tylko na ruch ze wskazanych sieci wirtualnych i podsieci, a blokować ruch z wszystkich pozostałych. Pomaga to w ochronie konta usługi ADLS przed zagrożeniami zewnętrznymi.

Integracja z siecią wirtualną w usłudze ADLS Gen1 wykorzystuje zabezpieczenia punktu końcowego usługi sieci wirtualnej pomiędzy siecią wirtualną a usługą Azure Active Directory do wygenerowania dodatkowych oświadczeń zabezpieczeń w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie ADLS Gen1 i uzyskania dostępu.

> [!NOTE]
> Jest to technologia w wersji zapoznawczej i korzystanie z niej w środowiskach produkcyjnych nie jest zalecane.
>
> Korzystanie z tych funkcji nie wiąże się z dodatkowymi opłatami. Opłaty na koncie będą naliczane według standardowej stawki za usługę ADLS Gen1 ([cennik](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) i wszystkie używane usługi platformy Azure ([cennik](https://azure.microsoft.com/pricing/#product-picker)).

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>Scenariusze dotyczące integracji z siecią wirtualną w usłudze ADLS Gen1

Integracja z siecią wirtualną w usłudze ADLS Gen1 pozwala ograniczyć dostęp do Twojego konta usługi ADLS Gen1 tylko do określonych sieci wirtualnych i podsieci.  Po ograniczeniu konta do określonej podsieci wirtualnej inne sieci wirtualne i maszyny wirtualne na platformie Azure nie będą miały do niego dostępu.  Pod względem funkcjonalnym integracja z siecią wirtualną w usłudze ADLS Gen1 umożliwia realizację takiego samego scenariusza, jak w przypadku [punktów końcowych usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  Istnieje jednak kilka kluczowych różnic, opisanych szczegółowo w poniższych sekcjach. 

![Diagram scenariusza integracji z siecią wirtualną w usłudze ADLS Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Oprócz reguł sieci wirtualnej można stosować istniejące reguły zapory IP w celu zezwalania na dostęp również z sieci lokalnych. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Optymalny routing podczas korzystania z integracji z siecią wirtualną w usłudze ADLS Gen1

Kluczową korzyścią ze stosowania punktów końcowych usługi sieci wirtualnej jest [optymalny routing](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) z sieci wirtualnej.  Aby tak samo zoptymalizować trasy do kont usługi ADLS Gen1, należy zastosować następujące [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) z sieci wirtualnej do konta usługi ADLS Gen1:

- **Publiczny adres IP usługi ADLS** — użyj publicznego adresu IP docelowych kont usługi ADLS Gen1.  Adresy IP kont usługi ADLS Gen1 można zidentyfikować przez [rozpoznawanie nazw DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) tych kont.  Utwórz oddzielny wpis dla każdego adresu.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Wyciek danych z sieci wirtualnej klienta

Oprócz zabezpieczania dostępu do kont usługi ADLS z poziomu sieci wirtualnej można również zabezpieczyć usługę przed wyciekiem danych na nieautoryzowane konto.

Zalecamy zastosowanie rozwiązania zapory w sieci wirtualnej do filtrowania ruchu wychodzącego na podstawie adresu URL konta docelowego i zezwalania tylko na ruch do autoryzowanych kont usługi ADLS Gen1.

Dostępne opcje to między innymi:
- [Usługa Azure Firewall](https://docs.microsoft.com/azure/firewall/overview): możesz [wdrożyć i skonfigurować zaporę w usłudze Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) dla sieci wirtualnej, aby zabezpieczyć ruch wychodzący z usługi ADLS i ograniczyć go do adresów URL znanych i autoryzowanych kont.
- Zapora [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/): jeśli administrator zezwala wyłącznie na korzystanie z zapór określonych dostawców komercyjnych, możesz do realizacji tych samych funkcji zastosować rozwiązanie zapory wirtualnego urządzenia sieciowego, dostępne w witrynie Azure Marketplace.

> [!NOTE]
> Użycie zapory w ramach ścieżki danych wprowadza dodatkowy przeskok na tej ścieżce i może mieć wpływ na wydajność sieci w całym procesie wymiany danych, w tym na dostępną przepływność oraz opóźnienie połączenia. 

## <a name="limitations"></a>Ograniczenia
1.  Po dodaniu do wersji zapoznawczej należy ponownie utworzyć klastry usługi HDInsight.  Klastry utworzone przed udostępnieniem integracji z siecią wirtualną w usłudze ADLS Gen1 należy utworzyć ponownie, aby umożliwić obsługę tej nowej funkcji. 
2.  Podczas tworzenia nowego klastra usługi HDInsight wybranie konta usługi ADLS Gen1 z włączoną integracją z siecią wirtualną spowoduje niepowodzenie procesu. Należy najpierw wyłączyć regułę sieci wirtualnej lub wybrać opcję **Zezwalaj na dostęp ze wszystkich sieci i usług** w bloku **Zapory i sieci wirtualne** na koncie usługi ADLS.  Zobacz sekcję [Wyjątki](##Exceptions), aby uzyskać więcej informacji.
3.  Wersja zapoznawcza integracji z siecią wirtualną w usłudze ADLS Gen1 nie działa z [tożsamościami zarządzanymi zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  Dane plików/folderów na koncie usługi ADLS Gen1 z włączoną integracją z siecią wirtualną nie są dostępne z poziomu portalu.  Dotyczy to również dostępu z poziomu maszyny wirtualnej znajdującej się w tej sieci wirtualnej oraz czynności takich jak korzystanie z Eksploratora danych.  Czynności związane z zarządzaniem kontem będą nadal działać.  Dane plików/folderów na koncie usługi ADLS Gen1 z włączoną integracją z siecią wirtualną są dostępne z poziomu wszystkich zasobów poza portalem — możliwy jest dostęp przy użyciu zestawu SDK, skryptów programu PowerShell, innych usług platformy Azure (jeśli źródłem nie jest portal) itp. 

## <a name="configuration"></a>Konfigurowanie

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>Krok 1. Konfigurowanie sieci wirtualnej w celu korzystania z punktu końcowego usługi AAD
1.  Otwórz witrynę Azure Portal i zaloguj się na koncie. 
2.  [Utwórz nową sieć wirtualną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) w ramach subskrypcji lub przejdź do istniejącej sieci wirtualnej.  Obecnie sieć wirtualna musi znajdować się w tym samym regionie, w którym znajduje się konto usługi ADLS Gen1. 
3.  W bloku Sieć wirtualna wybierz pozycję **Punkty końcowe usługi**. 
4.  Kliknij przycisk **Dodaj**, aby dodać nowy punkt końcowy usługi.
![Dodawanie punktu końcowego usługi sieci wirtualnej](media/data-lake-store-network-security/config-vnet-1.png)
5.  Jako usługę dla punktu końcowego wybierz **Microsoft.AzureActiveDirectory**.
![Wybieranie punktu końcowego usługi Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)
6.  Wybierz podsieci, w przypadku których chcesz zezwolić na łączność, i kliknij przycisk **Dodaj**.
![Wybieranie podsieci](media/data-lake-store-network-security/config-vnet-3.png)
7.  Dodanie punktu końcowego usługi może potrwać do 15 minut. Dodany punkt końcowy zostanie wyświetlony na liście. Sprawdź, czy znajduje się on na liście, i czy wszystkie szczegóły są zgodne z konfiguracją. 
![Pomyślne dodanie punktu końcowego usługi](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>Krok 2. Konfigurowanie dozwolonej sieci wirtualnej/podsieci dla konta usługi ADLS Gen1
1.  Po skonfigurowaniu sieci wirtualnej [utwórz nowe konto usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) w ramach subskrypcji lub przejdź do istniejącego konta usługi ADLS Gen1. Obecnie konto usługi ADLS Gen1 musi znajdować się w tym samym regionie, w którym znajduje się sieć wirtualna. 
2.  Wybierz pozycję **Zapory i sieci wirtualne**.

  > [!NOTE]
  > Jeśli nie widzisz pozycji **Zapory i sieci wirtualne** w ustawieniach, wyloguj się z portalu. Zamknij okno przeglądarki. Wyczyść pamięć podręczną przeglądarki. Uruchom ponownie maszynę i spróbuj ponownie.

  ![Dodawanie reguły sieci wirtualnej do konta usługi ADLS](media/data-lake-store-network-security/config-adls-1.png)
3.  Wybierz pozycję **Wybrane sieci**. 
4.  Kliknij pozycję **Dodaj istniejącą sieć wirtualną**.
  ![Dodawanie istniejącej sieci wirtualnej](media/data-lake-store-network-security/config-adls-2.png)
5.  Wybierz sieci i podsieci, w przypadku których chcesz zezwolić na łączność, a następnie kliknij pozycję **Dodaj**.
  ![Wybieranie sieci wirtualnej i podsieci](media/data-lake-store-network-security/config-adls-3.png)
6.  Upewnij się, że wybrane sieci wirtualne i podsieci są wyświetlane prawidłowo na liście, i kliknij pozycję **Zapisz**.
  ![Zapisywanie nowej reguły](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > Po zapisaniu ustawień zaczną one obowiązywać po upływie maksymalnie 5 minut.

7.  [Opcjonalnie] Oprócz sieci wirtualnych i podsieci możesz zezwolić na łączność z określonych adresów IP — w tym celu skorzystaj z sekcji **Zapora** na tej samej stronie. 

## <a name="exceptions"></a>Wyjątki
W obszarze Wyjątki w bloku **Zapory i sieci wirtualne** dostępne są dwa pola wyboru, które umożliwiają łączność z zestawem usług i maszyn wirtualnych na platformie Azure.
![Wyjątki w bloku Zapory i sieci wirtualne](media/data-lake-store-network-security/firewall-exceptions.png)
- Ustawienie **Zezwalaj wszystkim usługom platformy Azure na dostęp do tego konta usługi Data Lake Storage Gen1** umożliwia wszystkim usługom platformy Azure, w tym usługom Azure Data Factory, Event Hubs, maszynom wirtualnym platformy Azure itd. komunikowanie się z kontem usługi ADLS.

- Ustawienie **Zezwalaj usłudze Azure Data Lake Analytics na dostęp do tego konta usługi Data Lake Storage Gen1** umożliwia łączność między usługą Azure Data Lake Analytics a kontem usługi ADLS. 

Zalecamy wyłączenie tych wyjątków i włączanie ich tylko w przypadkach, w których jest wymagana łączność z tych innych usług spoza Twojej sieci wirtualnej.
