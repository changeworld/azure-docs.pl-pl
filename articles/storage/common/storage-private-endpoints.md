---
title: Używanie prywatnych punktów końcowych w usłudze Azure Storage | Microsoft Docs
description: Przegląd prywatnych punktów końcowych w celu bezpiecznego dostępu do kont magazynu z sieci wirtualnych.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949471"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Używanie prywatnych punktów końcowych usługi Azure Storage (wersja zapoznawcza)

Usługa Azure Storage umożliwia korzystanie z [prywatnych punktów końcowych](../../private-link/private-endpoint-overview.md) dla klientów w sieci wirtualnej (VNET) w celu bezpiecznego dostępu do danych na koncie magazynu za pośrednictwem [prywatnego linku](../../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi konta magazynu. Ruch sieciowy między klientami w sieci wirtualnej a kontem magazynu przechodzącym przez sieć wirtualną i prywatnym łączem w usłudze Microsoft szkielet Network, eliminując ekspozycję z publicznego Internetu.

Używanie prywatnych punktów końcowych dla konta magazynu pozwala:
- Zabezpiecz swoje konto magazynu, konfigurując zaporę magazynu do blokowania wszystkich połączeń w publicznym punkcie końcowym usługi Storage.
- Zwiększa bezpieczeństwo sieci wirtualnej (VNet), umożliwiając zablokowanie eksfiltracji danych w wirtualnej.
- Bezpiecznie łącz się z kontami magazynu z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

## <a name="conceptual-overview"></a>Omówienie pojęć
![Omówienie prywatnych punktów końcowych usługi Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Prywatny punkt końcowy jest specjalnym interfejsem sieciowym w [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNET) dla usługi platformy Azure. Zapewnia bezpieczną łączność między klientami w sieci wirtualnej i kontem magazynu. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą magazynu używa bezpiecznego linku prywatnego.

Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą magazynu za pośrednictwem prywatnego punktu końcowego, używając tych samych parametrów połączenia i mechanizmów autoryzacji, które mogą być używane w inny sposób. Prywatnych punktów końcowych można używać ze wszystkimi protokołami obsługiwanymi przez konto magazynu, w tym REST i SMB.

Po utworzeniu prywatnego punktu końcowego dla usługi magazynu w sieci wirtualnej do zatwierdzenia dla właściciela konta magazynu jest wysyłane żądanie zgody. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem konta magazynu, to żądanie zgody jest automatycznie zatwierdzane.

Właściciele konta magazynu mogą zatwierdzać lub odrzucać żądania zgody, a także wyświetlać i zarządzać prywatnymi punktami końcowymi za pomocą karty "prywatne punkty końcowe" dla konta magazynu w [Azure Portal](https://portal.azure.com).

Możesz zabezpieczyć konto magazynu tak, aby akceptowało tylko połączenia z sieci wirtualnej, [konfigurując zaporę magazynu](storage-network-security.md#change-the-default-network-access-rule) tak, aby domyślnie nie zezwala na dostęp za pośrednictwem jego publicznego punktu końcowego. Reguła zapory magazynu nie jest potrzebna, aby zezwalać na ruch z sieci wirtualnej, która ma prywatny punkt końcowy, ponieważ reguły zapory magazynu mają zastosowanie tylko do jego publicznego punktu końcowego. Prywatne punkty końcowe zamiast tego polegają na przepływie zgody na przyznanie podsieci dostępu do usługi magazynu.

### <a name="private-endpoints-for-storage-service"></a>Prywatne punkty końcowe usługi Storage

Podczas tworzenia prywatnego punktu końcowego należy określić konto magazynu i usługę magazynu, z którą nawiąże połączenie. Wymagany jest prywatny punkt końcowy dla każdej usługi magazynu w ramach konta magazynu, do którego wymagany jest dostęp, czyli [obiekty blob](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [pliki](../files/storage-files-introduction.md), [kolejki](../queues/storage-queues-introduction.md), [tabele](../tables/table-storage-overview.md)lub [statyczne witryny sieci Web](../blobs/storage-blob-static-website.md).

Aby zapewnić dostępność odczytu [geograficznie nadmiarowego konta magazynu dla dostępu do odczytu](storage-redundancy-grs.md#read-access-geo-redundant-storage), musisz oddzielić prywatne punkty końcowe dla wystąpienia podstawowego i dodatkowego usługi.

#### <a name="resources"></a>Zasoby

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia prywatnego punktu końcowego dla konta magazynu, zapoznaj się z następującymi artykułami:

- [Połącz się prywatnie z kontem magazynu z poziomu środowiska konta magazynu w Azure Portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Utwórz prywatny punkt końcowy przy użyciu prywatnego centrum linków w Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego dla usługi magazynu aktualizujemy rekord zasobu CNAME DNS dla tego punktu końcowego magazynu do aliasu w poddomenie z prefiksem "*privatelink*". Domyślnie tworzymy również [prywatną strefę DNS](../../dns/private-dns-overview.md) dołączoną do sieci wirtualnej. Ta prywatna strefa DNS odpowiada poddomeny z prefiksem "*privatelink*" i zawiera rekordy zasobów DNS a dla prywatnych punktów końcowych.

Po rozwiązaniu adresu URL punktu końcowego magazynu spoza sieci wirtualnej, w której jest tworzony prywatny punkt końcowy, nadal jest on rozpoznawany jako publiczny punkt końcowy usługi magazynu. Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, adres URL punktu końcowego magazynu jest rozpoznawany jako adres IP prywatnego punktu końcowego.

W przykładzie przedstawionym powyżej, rekordy zasobów DNS dla konta magazynu "StorageAccountA", po rozwiązaniu spoza sieci wirtualnej obsługującej prywatny punkt końcowy, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<publiczny punkt końcowy\>                                   |
| \<publiczny punkt końcowy\>                                   | A     | \<publiczny adres IP usługi Storage\>                 |

Jak wspomniano wcześniej, można odmówić dostępu przez publiczny punkt końcowy przy użyciu zapory magazynu.

Rekordy zasobów DNS dla StorageAccountA, po rozwiązaniu przez klienta w sieci wirtualnej hostującym prywatnego punktu końcowego, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | klienta 10.1.1.5                                              |

Takie podejście umożliwia dostęp do konta magazynu przy użyciu tych samych parametrów połączenia z sieci wirtualnej obsługującej prywatne punkty końcowe, a także klientów spoza sieci wirtualnej. Możesz użyć zapory magazynu, aby odmówić dostępu wszystkim klientom spoza sieci wirtualnej.

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Znane problemy

### <a name="copy-blob-failures"></a>Błędy kopiowania obiektów BLOB

Obecnie [kopie poleceń obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) wystawione dla kont magazynu, do których uzyskuje się dostęp za pomocą prywatnych punktów końcowych, zakończą się niepowodzeniem, gdy źródłowe konto magazynu

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Ograniczenia dostępu do magazynu dla klientów w sieci wirtualnych z prywatnymi punktami końcowymi

Klienci w sieci wirtualnych, którzy mają istniejące ograniczenia dotyczące prywatnego punktu końcowego magazynu podczas uzyskiwania dostępu do innych kont magazynu z prywatnymi punktami końcowymi. Na przykład załóżmy, że sieć wirtualna N1 ma prywatny punkt końcowy dla konta magazynu a1 dla, powiedz, usługa BLOB Service. Jeśli konto magazynu a2 ma prywatny punkt końcowy w sieci wirtualnej N2 dla usługi BLOB, wówczas klienci w sieci wirtualnej N1 muszą również uzyskać dostęp do usługi BLOB na koncie a2 przy użyciu prywatnego punktu końcowego. Jeśli konto magazynu a2 nie ma żadnych prywatnych punktów końcowych dla usługi BLOB, klienci w sieci wirtualnej N1 mogą uzyskać dostęp do swojej usługi BLOB bez prywatnego punktu końcowego.

To ograniczenie jest wynikiem zmian wprowadzonych w systemie DNS, gdy konto a2 tworzy prywatny punkt końcowy.

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>Reguły sieciowej grupy zabezpieczeń w podsieciach z prywatnymi punktami końcowymi

W tej chwili nie można skonfigurować reguł [grupy zabezpieczeń sieci](../../virtual-network/security-overview.md) (sieciowej grupy zabezpieczeń) dla podsieci z prywatnymi punktami końcowymi. Ograniczone obejście tego problemu polega na implementacji reguł dostępu dla prywatnych punktów końcowych w podsieciach źródłowych, chociaż takie podejście może wymagać wyższego obciążenia zarządzania.
