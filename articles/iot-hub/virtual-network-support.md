---
title: Obsługa usługi Azure IoT Hub dla sieci wirtualnych
description: Jak korzystać ze wzorca łączności sieci wirtualnych z Usługą IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501439"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Obsługa sieci wirtualnych w centrum IoT Hub

W tym artykule przedstawiono wzorzec łączności sieci wirtualnej i opracuje sposób konfigurowania środowiska łączności prywatnej do centrum IoT za pośrednictwem sieci wirtualnej platformy Azure należącej do klienta.

> [!NOTE]
> Funkcje usługi IoT Hub opisane w tym artykule są obecnie dostępne dla centrów IoT [utworzonych z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity) w następujących regionach: Wschodnie stany USA, południowo-środkowe stany USA i zachodnie stany USA 2.


## <a name="introduction"></a>Wprowadzenie

Domyślnie nazwy hostów usługi IoT Hub są mapowane do publicznego punktu końcowego z publicznie rutowalnym adresem IP przez Internet. Jak pokazano na poniższej ilustracji, ten publiczny punkt końcowy usługi IoT Hub jest współużytkowany przez koncentratory należące do różnych klientów i jest dostępny dla urządzeń IoT zarówno za pośrednictwem sieci rozległych, jak i sieci lokalnych.

Kilka funkcji Usługi IoT Hub, w tym [routing wiadomości,](./iot-hub-devguide-messages-d2c.md) [przekazywanie plików](./iot-hub-devguide-file-upload.md)i [importowanie/eksportowanie urządzeń zbiorczych](./iot-hub-bulk-identity-mgmt.md) w podobny sposób wymagają łączności z usługi IoT Hub z zasobem platformy Azure należącym do klienta w jego publicznym punkcie końcowym. Jak pokazano poniżej, te ścieżki łączności łącznie stanowią ruch wychodzący z Usługi IoT Hub do zasobów klienta.
![Publiczny punkt końcowy Usługi IoT Hub](./media/virtual-network-support/public-endpoint.png)


Z kilku powodów klienci mogą chcieć ograniczyć łączność do swoich zasobów platformy Azure (w tym usługi IoT Hub) za pośrednictwem sieci wirtualnej, która jest ich właścicielem i działa. Powody te obejmują:

* Wprowadzenie dodatkowych warstw zabezpieczeń za pośrednictwem izolacji poziomu sieci dla centrum IoT hub, zapobiegając ekspozycji łączności z koncentratorem za pośrednictwem publicznego Internetu.

* Włączenie środowiska łączności prywatnej z lokalnych zasobów sieciowych, zapewniając, że dane i ruch są przesyłane bezpośrednio do sieci szkieletowej platformy Azure.

* Zapobieganie atakom eksfiltracji z poufnych sieci lokalnych. 

* Po ustanowieniu wzorców łączności na całej platformie Azure przy użyciu [prywatnych punktów końcowych.](../private-link/private-endpoint-overview.md)


W tym artykule opisano, jak osiągnąć te cele przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) dla łączności transferu danych przychodzących do usługi IoT Hub, jak przy użyciu usługi azure zaufanych usług pierwszej strony wyjątek dla łączności wychodzącej z Usługi IoT Hub do innych zasobów platformy Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Łączność przychodząca z Centrum IoT przy użyciu prywatnych punktów końcowych

Prywatny punkt końcowy to prywatny adres IP przydzielony wewnątrz sieci wirtualnej należącej do klienta, za pośrednictwem której zasób platformy Azure jest osiągalny. Dzięki prywatnemu punktowi końcowemu dla centrum IoT hub można zezwolić usługom działającym wewnątrz sieci wirtualnej na dotarcie do usługi IoT Hub bez konieczności wysyłania ruchu do publicznego punktu końcowego usługi IoT Hub. Podobnie urządzenia, które działają w środowisku lokalnym, mogą używać [wirtualnej sieci prywatnej (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) lub prywatnej komunikacji równorzędnej [usługi ExpressRoute](https://azure.microsoft.com/services/expressroute/) w celu uzyskania łączności z siecią wirtualną na platformie Azure, a następnie z centrum IoT Hub (za pośrednictwem jej prywatnego punktu końcowego). W rezultacie klienci, którzy chcą ograniczyć łączność do swoich publicznych punktów końcowych centrum IoT hub (lub ewentualnie całkowicie zablokować go wyłączyć) można osiągnąć ten cel przy użyciu [reguł zapory Usługi IoT Hub](./iot-hub-ip-filtering.md) przy jednoczesnym zachowaniu łączności z ich Centrum przy użyciu prywatnego punktu końcowego.

> [!NOTE]
> Głównym celem tej konfiguracji jest dla urządzeń wewnątrz sieci lokalnej. Ta konfiguracja nie jest zalecana w przypadku urządzeń wdrożonych w sieci rozległej.

![Publiczny punkt końcowy Usługi IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Przed przystąpieniem do postępowania upewnij się, że spełnione są następujące wymagania wstępne:

* Centrum IoT musi być aprowizować z [tożsamością usługi zarządzanej.](#create-an-iot-hub-with-managed-service-identity)

* Centrum IoT musi być aprowizować w jednym z [obsługiwanych regionów.](#regional-availability-private-endpoints)

* Zaaprowizowano sieć wirtualną platformy Azure za pomocą podsieci, w której zostanie utworzony prywatny punkt końcowy. Aby uzyskać więcej [informacji, zobacz tworzenie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.](../virtual-network/quick-create-cli.md)

* W przypadku urządzeń działających wewnątrz sieci lokalnych należy skonfigurować prywatną komunikację równorzędną [wirtualną siecią prywatną (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) lub prywatną usługą [ExpressRoute](https://azure.microsoft.com/services/expressroute/) w sieci wirtualnej platformy Azure.


### <a name="regional-availability-private-endpoints"></a>Dostępność regionalna (prywatne punkty końcowe)

Prywatne punkty końcowe obsługiwane w centrum IoT Hub utworzone w następujących regionach:

* Wschodnie stany USA

* Południowo-środkowe stany USA

* Zachodnie stany USA 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Konfigurowanie prywatnego punktu końcowego dla transferu ruchu przychodzącego usługi IoT Hub

Aby skonfigurować prywatny punkt końcowy, wykonaj następujące kroki:

1. Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby ponownie zarejestrować dostawcę usługi Azure IoT Hub w ramach subskrypcji:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Przejdź do karty **Prywatne połączenia punktów końcowych** w portalu Usługi IoT Hub (ta karta jest dostępna tylko w centrach IoT w [obsługiwanych regionach)](#regional-availability-private-endpoints)i kliknij znak, **+** aby dodać nowy prywatny punkt końcowy.

3. Podaj subskrypcję, grupę zasobów, nazwę i region, aby utworzyć nowy prywatny punkt końcowy w (najlepiej, prywatny punkt końcowy powinien zostać utworzony w tym samym regionie co centrum; zobacz [sekcję dostępności regionalnej,](#regional-availability-private-endpoints) aby uzyskać więcej informacji).

4. Kliknij **przycisk Dalej: Zasób**i udostępnij subskrypcję zasobu Usługi IoT Hub, a następnie wybierz **"Microsoft.Devices/IotHubs"** jako typ zasobu, nazwę Centrum IoT jako **zasób** **iotHub** jako docelowy zasób.

5. Kliknij **przycisk Dalej: Konfiguracja** i udostępnij sieć wirtualną i podsieć, aby utworzyć prywatny punkt końcowy. Wybierz opcję integracji z prywatną strefą DNS platformy Azure, jeśli to możliwe.

6. Kliknij **przycisk Dalej: Tagi**i opcjonalnie podaj wszystkie tagi dla swojego zasobu.

7. Kliknij **przycisk Przejrzyj + utwórz,** aby utworzyć prywatny zasób punktu końcowego.


### <a name="pricing-private-endpoints"></a>Ceny (prywatne punkty końcowe)

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Łączność wychodząca z usługi IoT Hub do innych zasobów platformy Azure

Usługa IoT Hub potrzebuje dostępu do magazynu obiektów blob platformy Azure, centrów zdarzeń, zasobów magistrali usług do [routingu wiadomości, przekazywania](./iot-hub-devguide-messages-d2c.md) [plików](./iot-hub-devguide-file-upload.md)i [importu/eksportu urządzeń zbiorczych,](./iot-hub-bulk-identity-mgmt.md)które zazwyczaj odbywają się za pomocą publicznego punktu końcowego zasobów. W przypadku powiązania konta magazynu, centrów zdarzeń lub zasobu magistrali usług z siecią wirtualną, zalecana konfiguracja domyślnie zablokuje łączność z zasobem. W związku z tym będzie to utrudniać funkcjonalność usługi IoT Hub, która wymaga dostępu do tych zasobów.

Aby złagodzić tę sytuację, należy włączyć łączność z zasobu usługi IoT Hub do konta magazynu, centrów zdarzeń lub zasobów magistrali usług za pośrednictwem opcji usług zaufanych usług **platformy Azure.**

Obowiązują następujące wymagania wstępne:

* Centrum IoT musi być aprowizować w jednym z [obsługiwanych regionów.](#regional-availability-trusted-microsoft-first-party-services)

* Centrum IoT Hub musi być przypisany tożsamości usługi zarządzanej w czasie inicjowania obsługi administracyjnej centrum. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia centrum z tożsamością usługi zarządzanej](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Dostępność regionalna (zaufane usługi firmy Microsoft)

Wyjątek zaufanych usług pierwszej firmy platformy Azure w celu ominięcia ograniczeń zapory w magazynie platformy Azure, centrach zdarzeń i zasobach magistrali usług jest obsługiwany tylko dla centrów IoT w następujących regionach:

* Wschodnie stany USA

* Południowo-środkowe stany USA

* Zachodnie stany USA 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Cennik (zaufane usługi firmy Microsoft)

Zaufana funkcja wyjątku usług innych firm firmy Microsoft jest bezpłatna w centrach IoT w [obsługiwanych regionach](#regional-availability-trusted-microsoft-first-party-services). Opłaty za aprowizowane konta magazynu, centra zdarzeń lub zasoby magistrali usług mają zastosowanie oddzielnie.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Tworzenie centrum IoT hub z tożsamością usługi zarządzanej

Tożsamość usługi zarządzanej można przypisać do koncentratora w czasie inicjowania obsługi administracyjnej zasobów (ta funkcja nie jest obecnie obsługiwana dla istniejących koncentratorów), co wymaga, aby centrum IoT używać protokołu TLS 1.2 jako minimalnej wersji. W tym celu należy użyć poniższego szablonu zasobu ARM:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Po zastąpieniu wartości zasobu `name` `location`, `SKU.name` `SKU.tier`i , można użyć interfejsu wiersza polecenia platformy Azure, aby wdrożyć zasób w istniejącej grupie zasobów przy użyciu:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Po utworzeniu zasobu można pobrać tożsamość usługi zarządzanej przypisaną do koncentratora przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Po zainicjowaniu obsługi administracyjnej przez centrum IoT Hub z tożsamością usługi zarządzanej należy postępować zgodnie z odpowiednią sekcją, aby skonfigurować punkty końcowe routingu na [konta magazynu,](#egress-connectivity-to-storage-account-endpoints-for-routing) [centra zdarzeń](#egress-connectivity-to-event-hubs-endpoints-for-routing)i zasoby [magistrali usług](#egress-connectivity-to-service-bus-endpoints-for-routing) lub skonfigurować [przesyłanie plików](#egress-connectivity-to-storage-accounts-for-file-upload) i [importowanie/eksportowanie urządzeń zbiorczych.](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Łączność wychodząca z punktami końcowymi konta magazynu dla routingu

Centrum IoT Hub można skonfigurować do kierowania wiadomości do konta magazynu należącego do klienta. Aby zezwolić funkcji routingu na dostęp do konta magazynu, gdy obowiązują ograniczenia zapory, centrum IoT hub musi mieć tożsamość usługi zarządzanej (zobacz jak [utworzyć koncentrator z tożsamością usługi zarządzanej).](#create-an-iot-hub-with-managed-service-identity) Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe czynności, aby nadać rbac uprawnienia do tożsamości zasobów centrum, aby uzyskać dostęp do konta magazynu.

1. W witrynie Azure Portal przejdź do karty **Kontrola dostępu (IAM)** konta magazynu i kliknij pozycję **Dodaj** w sekcji **Dodaj przypisanie roli.**

2. Wybierz **źródło informacji o danych obiektów blob magazynu** jako **rolę,** **użytkownika, grupę lub jednostkę usługi Azure AD** jako **przypisywanie dostępu do** usługi IoT Hub i wybieranie nazwy zasobu usługi IoT na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne** na koncie magazynu i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** W obszarze **Lista Wyjątki** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów usługi IoT Hub przejdź do karty **Routing wiadomości.**

5. Przejdź do sekcji **Niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz **magazyn** jako typ punktu końcowego.

6. Na stronie, która się pojawi, podaj nazwę punktu końcowego, wybierz kontener, który ma być używany w magazynie obiektów blob, podaj kodowanie i format nazwy pliku. Wybierz **opcję System przypisany** jako typ **uwierzytelniania** do punktu końcowego magazynu. Kliknij przycisk **Utwórz**.

Teraz niestandardowy punkt końcowy magazynu jest skonfigurowany do używania tożsamości przypisanej systemowi koncentratora i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory. Teraz można użyć tego punktu końcowego, aby skonfigurować regułę routingu.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Łączność wychodząca z punktami końcowymi centrów zdarzeń dla routingu

Centrum IoT hub można skonfigurować do kierowania wiadomości do obszaru nazw centrów zdarzeń należących do klienta. Aby zezwolić funkcji routingu na dostęp do zasobu centrów zdarzeń, gdy obowiązują ograniczenia zapory, centrum IoT hub musi mieć tożsamość usługi zarządzanej (zobacz jak [utworzyć koncentrator z tożsamością usługi zarządzanej).](#create-an-iot-hub-with-managed-service-identity) Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe czynności, aby nadać rbac uprawnienia do tożsamości zasobów centrum, aby uzyskać dostęp do centrów zdarzeń.

1. W witrynie Azure Portal przejdź do karty kontrola dostępu do centrów zdarzeń **(IAM)** i kliknij pozycję **Dodaj** w sekcji **Dodaj przypisanie roli.**

2. Wybierz **pozycję Nadawca danych usługi Event Hubs** jako **rolę**, **użytkownik, grupa lub podmiot usługi Azure AD** jako **przypisywanie dostępu do** usługi IoT Hub i wybieranie nazwy zasobu usługi IoT na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne** w centrach zdarzeń i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** W obszarze **Lista Wyjątki** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do centrów zdarzeń**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów usługi IoT Hub przejdź do karty **Routing wiadomości.**

5. Przejdź do sekcji **Niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz **centra zdarzeń** jako typ punktu końcowego.

6. Na stronie, która się pojawi, podaj nazwę punktu końcowego, wybierz obszar nazw centrów zdarzeń i wystąpienie, a następnie kliknij przycisk **Utwórz.**

Teraz niestandardowy punkt końcowy centrów zdarzeń jest skonfigurowany do używania tożsamości przypisanej systemowi centrum i ma uprawnienia dostępu do zasobu centrów zdarzeń pomimo ograniczeń zapory. Teraz można użyć tego punktu końcowego, aby skonfigurować regułę routingu.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Łączność wychodząca z punktami końcowymi magistrali usług dla routingu

Centrum IoT Hub można skonfigurować do kierowania wiadomości do obszaru nazw magistrali usług należących do klienta. Aby zezwolić funkcji routingu na dostęp do zasobu magistrali usług, gdy obowiązują ograniczenia zapory, centrum IoT hub musi mieć tożsamość usługi zarządzanej (zobacz jak [utworzyć koncentrator z tożsamością usługi zarządzanej).](#create-an-iot-hub-with-managed-service-identity) Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe czynności, aby nadać rbac uprawnienia do tożsamości zasobów centrum, aby uzyskać dostęp do magistrali usług.

1. W witrynie Azure Portal przejdź do karty **Kontrola dostępu (IAM)** magistrali usług i kliknij pozycję **Dodaj** w sekcji **Dodaj przypisanie roli.**

2. Wybierz **pozycję Nadawca danych usługi Service jako** **rola**, **użytkownik, grupa lub podmiot usługi Azure AD** jako **przypisywanie dostępu do** usługi IoT Hub i wybranie nazwy zasobu usługi IoT na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne** w magistrali usług i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** W obszarze **Lista Wyjątki** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tej magistrali usług**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów usługi IoT Hub przejdź do karty **Routing wiadomości.**

5. Przejdź do sekcji **Niestandardowe punkty końcowe** i kliknij przycisk **Dodaj**. Wybierz **kolejkę magistrali usług** lub **temat magistrali usług** (w stosownych przypadkach) jako typ punktu końcowego.

6. Na stronie, która się pojawi, podaj nazwę punktu końcowego, wybierz obszar nazw magistrali usług i kolejki lub tematu (w stosownych przypadkach). Kliknij przycisk **Utwórz**.

Teraz punkt końcowy niestandardowej magistrali usług jest skonfigurowany do używania tożsamości przypisanej systemowi koncentratora i ma uprawnienia dostępu do zasobu magistrali usług pomimo ograniczeń zapory. Teraz można użyć tego punktu końcowego, aby skonfigurować regułę routingu.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Łączność wychodząca z kontami magazynu w celu przekazywania plików

Funkcja przekazywania plików usługi IoT Hub umożliwia urządzeniom przekazywanie plików na konto magazynu należące do klienta. Aby umożliwić przekazywanie pliku do działania, zarówno urządzenia i Usługi IoT Hub muszą mieć łączność z kontem magazynu. Jeśli na koncie magazynu obowiązują ograniczenia zapory, urządzenia muszą użyć dowolnego mechanizmu obsługiwanego konta magazynu (w tym [prywatnych punktów końcowych,](../private-link/create-private-endpoint-storage-portal.md) [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md) lub konfiguracji [zapory bezpośredniej)](../storage/common/storage-network-security.md)w celu uzyskania łączności. Podobnie jeśli ograniczenia zapory są w miejscu na koncie magazynu, Centrum IoT musi być skonfigurowany do dostępu do zasobu magazynu za pośrednictwem wyjątku zaufanych usług firmy Microsoft. W tym celu centrum IoT Hub musi mieć tożsamość usługi zarządzanej (zobacz jak [utworzyć centrum z tożsamością usługi zarządzanej).](#create-an-iot-hub-with-managed-service-identity) Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe czynności, aby nadać rbac uprawnienia do tożsamości zasobów centrum, aby uzyskać dostęp do konta magazynu.

1. W witrynie Azure Portal przejdź do karty **Kontrola dostępu (IAM)** konta magazynu i kliknij pozycję **Dodaj** w sekcji **Dodaj przypisanie roli.**

2. Wybierz **źródło informacji o danych obiektów blob magazynu** jako **rolę,** **użytkownika, grupę lub jednostkę usługi Azure AD** jako **przypisywanie dostępu do** usługi IoT Hub i wybieranie nazwy zasobu usługi IoT na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne** na koncie magazynu i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** W obszarze **Lista Wyjątki** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

4. Na stronie zasobów usługi IoT Hub przejdź do pozycji **Karta Przekazywanie plików.**

5. Na stronie, która się pojawi, wybierz kontener, który ma być używany w magazynie obiektów blob, skonfiguruj **ustawienia powiadomień o plikach,** **czas wygaśnięcia SAS,** **domyślny czas wygaśnięcia** i **maksymalną liczbę wyświetleń** zgodnie z potrzebami. Wybierz **opcję System przypisany** jako typ **uwierzytelniania** do punktu końcowego magazynu. Kliknij przycisk **Utwórz**.

Teraz punkt końcowy magazynu dla przekazywania plików jest skonfigurowany do używania tożsamości przypisanej systemowi koncentratora i ma uprawnienia dostępu do zasobu magazynu pomimo ograniczeń zapory.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Łączność wychodząca z kontami magazynu dla importu/eksportu urządzeń masowych

Centrum IoT hub obsługuje funkcje [zbiorczego importowania/eksportowania](./iot-hub-bulk-identity-mgmt.md) informacji o urządzeniach z/do obiektu blob magazynu dostarczonego przez klienta. Aby umożliwić funkcję importu/eksportu zbiorczego do działania, zarówno urządzenia, jak i centrum IoT hub muszą mieć łączność z kontem magazynu.

Ta funkcja wymaga łączności z Usługi IoT Hub do konta magazynu. Aby uzyskać dostęp do zasobu magistrali usług, gdy obowiązują ograniczenia zapory, centrum IoT hub musi mieć tożsamość usługi zarządzanej (zobacz jak [utworzyć koncentrator z tożsamością usługi zarządzanej).](#create-an-iot-hub-with-managed-service-identity) Po zainicjowaniu obsługi administracyjnej tożsamości usługi zarządzanej wykonaj poniższe czynności, aby nadać rbac uprawnienia do tożsamości zasobów centrum, aby uzyskać dostęp do magistrali usług.

1. W witrynie Azure Portal przejdź do karty **Kontrola dostępu (IAM)** konta magazynu i kliknij pozycję **Dodaj** w sekcji **Dodaj przypisanie roli.**

2. Wybierz **źródło informacji o danych obiektów blob magazynu** jako **rolę,** **użytkownika, grupę lub jednostkę usługi Azure AD** jako **przypisywanie dostępu do** usługi IoT Hub i wybieranie nazwy zasobu usługi IoT na liście rozwijanej. Kliknij przycisk **Zapisz**.

3. Przejdź do karty **Zapory i sieci wirtualne** na koncie magazynu i włącz opcję **Zezwalaj na dostęp z wybranych sieci.** W obszarze **Lista Wyjątki** zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**. Kliknij przycisk **Zapisz**.

Teraz można użyć interfejsu API REST usługi Azure IoT do [tworzenia zadań eksportu importu,](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) aby uzyskać informacje na temat używania funkcji importu/eksportu zbiorczego. Należy pamiętać, że należy `storageAuthenticationType="identityBased"` podać w treści `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` żądania i używać oraz jako adres URL danych wejściowych i wyjściowych konta magazynu, odpowiednio.


Zestaw SDK usługi Azure IoT Hub obsługuje również tę funkcję w menedżerze rejestru klienta usługi. Poniższy fragment kodu pokazuje, jak zainicjować zadanie importowania lub wyeksportować zadanie przy użyciu SDK języka C#.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Aby użyć tej ograniczonej do regionu wersji zestawów SDK usługi Azure IoT z obsługą sieci wirtualnej dla języka C#, Java i Node.js:

1. Utwórz zmienną `EnableStorageIdentity` środowiskową o `1`nazwie i ustaw jej wartość na .

2. Pobierz sdk: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
W przypadku Pythona pobierz naszą ograniczoną wersję z GitHub.

1. Przejdź do [strony wydania usługi GitHub](https://aka.ms/vnetpythonsdk).

2. Pobierz następujący plik, który znajdziesz u dołu strony wydania pod nagłówkiem o nazwie **Zasoby**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Otwórz terminal i przejdź do folderu z pobranym plikiem.

4. Uruchom następujące polecenie, aby zainstalować pakiet SDK usługi Python z obsługą sieci wirtualnych:
    > pip zainstalować ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Następne kroki

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o funkcjach usługi IoT Hub:

* [Routing komunikatów](./iot-hub-devguide-messages-d2c.md)
* [Przekazywanie plików](./iot-hub-devguide-file-upload.md)
* [Importowanie/eksportowanie urządzeń zbiorczych](./iot-hub-bulk-identity-mgmt.md) 
