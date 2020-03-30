---
title: Korzystanie z prywatnych punktów końcowych
titleSuffix: Azure Storage
description: Omówienie prywatnych punktów końcowych dla bezpiecznego dostępu do kont magazynu z sieci wirtualnych.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299060"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Używanie prywatnych punktów końcowych dla usługi Azure Storage

Za pomocą [prywatnych punktów końcowych](../../private-link/private-endpoint-overview.md) dla kont usługi Azure Storage można zezwolić klientom w sieci wirtualnej na bezpieczny dostęp do danych za pośrednictwem łącza [prywatnego.](../../private-link/private-link-overview.md) Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi konta magazynu. Ruch sieciowy między klientami w sieci wirtualnej i konta magazynu przechodzi przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu.

Korzystanie z prywatnych punktów końcowych dla konta magazynu umożliwia:

- Zabezpiecz konto magazynu, konfigurując zaporę magazynu, aby zablokować wszystkie połączenia w publicznym punkcie końcowym dla usługi magazynu.
- Zwiększ bezpieczeństwo sieci wirtualnej , umożliwiając blokowanie eksfiltracji danych z sieci wirtualnej.
- Bezpiecznie połącz się z kontami magazynu z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoutes](../../expressroute/expressroute-locations.md) za pomocą prywatnej komunikacji równorzędnej.

## <a name="conceptual-overview"></a>Omówienie pojęć

![Omówienie prywatnych punktów końcowych dla usługi Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Prywatny punkt końcowy to specjalny interfejs sieciowy dla usługi platformy Azure w [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) (VNet). Podczas tworzenia prywatnego punktu końcowego dla konta magazynu zapewnia bezpieczną łączność między klientami w sieci wirtualnej i magazynu. Do prywatnego punktu końcowego jest przypisywany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą magazynu używa bezpiecznego łącza prywatnego.

Aplikacje w sieci wirtualnej można połączyć się z usługą magazynu za pośrednictwem prywatnego punktu końcowego bezproblemowo, **przy użyciu tych samych ciągów połączeń i mechanizmów autoryzacji, które będą używać w przeciwnym razie**. Prywatne punkty końcowe mogą być używane ze wszystkimi protokołami obsługiwanymi przez konto magazynu, w tym REST i SMB.

Prywatne punkty końcowe można tworzyć w podsieciach korzystających z [punktów końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md). Klienci w podsieci mogą zatem łączyć się z jednym kontem magazynu przy użyciu prywatnego punktu końcowego, podczas korzystania z punktów końcowych usługi w celu uzyskania dostępu do innych.

Podczas tworzenia prywatnego punktu końcowego dla usługi magazynu w sieci wirtualnej, żądanie zgody jest wysyłane do zatwierdzenia przez właściciela konta magazynu. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem konta magazynu, to żądanie zgody zostanie automatycznie zatwierdzone.

Właściciele kont magazynu mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą karty '*Prywatne punkty końcowe*' dla konta magazynu w [witrynie Azure portal](https://portal.azure.com).

> [!TIP]
> Jeśli chcesz ograniczyć dostęp do konta magazynu tylko za pośrednictwem prywatnego punktu końcowego, skonfiguruj zaporę magazynu, aby odmówić lub kontrolować dostęp za pośrednictwem publicznego punktu końcowego.

Konto magazynu można zabezpieczyć, aby akceptować tylko połączenia z sieci wirtualnej, [konfigurując zaporę magazynu,](storage-network-security.md#change-the-default-network-access-rule) aby domyślnie odmawiać dostępu za pośrednictwem publicznego punktu końcowego. Nie potrzebujesz reguły zapory, aby zezwolić na ruch z sieci wirtualnej, która ma prywatny punkt końcowy, ponieważ zapora magazynu kontroluje tylko dostęp za pośrednictwem publicznego punktu końcowego. Prywatne punkty końcowe zamiast polegać na przepływ zgody do udzielania dostępu do podsieci do usługi magazynu.

### <a name="private-endpoints-for-azure-storage"></a>Prywatne punkty końcowe usługi Azure Storage

Podczas tworzenia prywatnego punktu końcowego należy określić konto magazynu i usługę magazynu, z którą się łączy. Dla każdej usługi magazynowania na koncie magazynu, do którego chcesz uzyskać dostęp, potrzebny jest oddzielny prywatny punkt końcowy, a mianowicie [obiekty Blobs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [Pliki,](../files/storage-files-introduction.md) [Kolejki,](../queues/storage-queues-introduction.md) [Tabele](../tables/table-storage-overview.md)lub [Statyczne witryny sieci Web.](../blobs/storage-blob-static-website.md)

> [!TIP]
> Utwórz oddzielny prywatny punkt końcowy dla pomocniczego wystąpienia usługi magazynu, aby uzyskać lepszą wydajność odczytu na kontach RA-GRS.

Aby uzyskać dostęp do odczytu do regionu pomocniczego z kontem magazynu skonfigurowanym do magazynu geograficznie nadmiarowego, potrzebujesz oddzielnych prywatnych punktów końcowych dla wystąpienia podstawowego i pomocniczego usługi. Nie trzeba tworzyć prywatnego punktu końcowego dla wystąpienia pomocniczego dla **pracy awaryjnej.** Prywatny punkt końcowy automatycznie połączy się z nowym wystąpieniem podstawowym po przełączeniu w tryb failover. Aby uzyskać więcej informacji na temat opcji nadmiarowości magazynu, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia prywatnego punktu końcowego dla konta magazynu, zobacz następujące artykuły:

- [Łączenie się prywatnie z kontem magazynu z obsługi konta magazynu w witrynie Azure portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Centrum łączy prywatnych w witrynie Azure portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu programu Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Łączenie się z prywatnymi punktami końcowymi

Klienci w sieci wirtualnej przy użyciu prywatnego punktu końcowego należy użyć tego samego ciągu połączenia dla konta magazynu, jako klienci łączący się z publicznym punktem końcowym. Polegamy na rozpoznawaniu DNS, aby automatycznie kierować połączenia z sieci wirtualnej do konta magazynu za pośrednictwem łącza prywatnego.

> [!IMPORTANT]
> Użyj tego samego ciągu połączenia, aby połączyć się z kontem magazynu przy użyciu prywatnych punktów końcowych, jak można użyć w przeciwnym razie. Nie należy łączyć się z kontem magazynu przy użyciu adresu URL poddomeny *'privatelink'.*

Tworzymy [prywatną strefę DNS](../../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z niezbędnymi aktualizacjami dla prywatnych punktów końcowych, domyślnie. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS. W poniższej sekcji [dotyczącej zmian dns](#dns-changes-for-private-endpoints) opisano aktualizacje wymagane dla prywatnych punktów końcowych.

## <a name="dns-changes-for-private-endpoints"></a>Zmiany DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla konta magazynu jest aktualizowany do aliasu w poddomenie z prefiksem '*privatelink*'. Domyślnie tworzymy również [prywatną strefę DNS](../../dns/private-dns-overview.md), odpowiadającą poddomenie '*privatelink'* z rekordami zasobów DNS A dla prywatnych punktów końcowych.

Po rozpoznaniu adres URL punktu końcowego magazynu spoza sieci wirtualnej z prywatnego punktu końcowego, jest rozpoznawany do publicznego punktu końcowego usługi magazynu. Po rozpoznaniu z sieci wirtualnej obsługującej prywatny punkt końcowy adres URL punktu końcowego magazynu jest rozpoznawany na adres IP prywatnego punktu końcowego.

W powyższym przykładzie rekordy zasobów DNS dla konta magazynu "StorageAccountA", po usunięciu spoza sieci wirtualnej obsługującej prywatny punkt końcowy, będą następujące:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<publiczny punkt końcowy usług magazynowania\>                   |
| \<publiczny punkt końcowy usług magazynowania\>                   | A     | \<publiczny adres IP usługi przechowywania\>                 |

Jak wcześniej wspomniano, można odmówić lub kontrolować dostęp dla klientów spoza sieci wirtualnej za pośrednictwem publicznego punktu końcowego przy użyciu zapory magazynu.

Rekordy zasobów DNS dla StorageAccountA, po rozwiązaniu przez klienta w sieci wirtualnej obsługującej prywatny punkt końcowy, będą następujące:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Takie podejście umożliwia dostęp do konta magazynu **przy użyciu tego samego ciągu połączenia** dla klientów w sieci wirtualnej obsługujących prywatne punkty końcowe, a także klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą mieć możliwość rozpoznania nazwy FQDN dla punktu końcowego konta magazynu na prywatny adres IP punktu końcowego. Serwer DNS powinien skonfigurować adres delegowania poddomeny łącza prywatnego do prywatnej strefy DNS sieci wirtualnej lub skonfigurować rekordy A dla '*StorageAccountA.privatelink.blob.core.windows.net*' z adresem IP prywatnego punktu końcowego.

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować serwer DNS w celu rozpoznania nazwy konta magazynu w poddomenie "privatelink" do adresu IP prywatnego punktu końcowego. Można to zrobić, delegując poddomenę "privatelink" do prywatnej strefy DNS sieci wirtualnej lub konfigurując strefę DNS na serwerze DNS i dodając rekordy DNS A.

Zalecane nazwy stref DNS dla prywatnych punktów końcowych dla usług magazynu to:

| Usługa magazynowania        | Nazwa strefy                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Usługa Data Lake Storage 2. generacji | `privatelink.dfs.core.windows.net`   |
| Usługa plików           | `privatelink.file.core.windows.net`  |
| Usługa kolejki          | `privatelink.queue.core.windows.net` |
| Usługa tabel          | `privatelink.table.core.windows.net` |
| Statyczne strony internetowe        | `privatelink.web.core.windows.net`   |

Aby uzyskać więcej informacji na temat konfigurowania własnego serwera DNS do obsługi prywatnych punktów końcowych, zobacz następujące artykuły:

- [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfiguracja DNS dla prywatnych punktów końcowych](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Znane problemy

Należy pamiętać o następujących znanych problemów dotyczących prywatnych punktów końcowych dla usługi Azure Storage.

### <a name="copy-blob-support"></a>Pomoc techniczna pomocy technicznej funkcji Copy Blob

Jeśli konto magazynu jest chroniony przez zaporę i konto jest dostępne za pośrednictwem prywatnych punktów końcowych, to konto nie może służyć jako źródło operacji [kopiowania obiektu blob.](/rest/api/storageservices/copy-blob)

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Ograniczenia dostępu do magazynu dla klientów w sieciach wirtualnych z prywatnymi punktami końcowymi

Klienci w sieciach wirtualnych z istniejącymi prywatnymi punktami końcowymi napotykają ograniczenia podczas uzyskiwania dostępu do innych kont magazynu, które mają prywatne punkty końcowe. Załóżmy na przykład, że sieć wirtualna N1 ma prywatny punkt końcowy dla konta magazynu A1 dla magazynu obiektów Blob. Jeśli konto magazynu A2 ma prywatny punkt końcowy w sieci wirtualnej N2 dla magazynu obiektów Blob, klienci w sieci wirtualnej N1 muszą również uzyskiwać dostęp do magazynu obiektów Blob na koncie A2 przy użyciu prywatnego punktu końcowego. Jeśli konto magazynu A2 nie ma żadnych prywatnych punktów końcowych dla magazynu obiektów Blob, klienci w sieci wirtualnej N1 mogą uzyskiwać dostęp do magazynu obiektów Blob na tym koncie bez prywatnego punktu końcowego.

To ograniczenie jest wynikiem zmian DNS wprowadzonych podczas tworzenia prywatnego punktu końcowego konta A2.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Reguły sieciowej grupy zabezpieczeń dla podsieci z prywatnymi punktami końcowymi

Obecnie nie można skonfigurować reguł [sieciowej grupy zabezpieczeń (NSG)](../../virtual-network/security-overview.md) i tras zdefiniowanych przez użytkownika dla prywatnych punktów końcowych. Reguły sieciowej sieciowej sieciowej stosowane do podsieci obsługującej prywatny punkt końcowy są stosowane do prywatnego punktu końcowego. Ograniczone obejście tego problemu jest zaimplementowanie reguł dostępu dla prywatnych punktów końcowych w podsieciach źródłowych, chociaż takie podejście może wymagać wyższego obciążenia zarządzania.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](storage-network-security.md)
- [Zalecenia dotyczące zabezpieczeń dla magazynu obiektów Blob](../blobs/security-recommendations.md)
