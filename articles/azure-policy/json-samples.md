---
title: Przykłady szablonów zasad
description: Przykłady JSON dla zasad usługi Azure
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 458ce4fb77f501d5feba4cf732fe3ce9a49f9c21
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="templates-for-azure-policy"></a>Szablony zasad Azure

Poniższa tabela zawiera linki do szablony json dla zasad usługi Azure. Te przykłady znajdują się w [repozytorium Przykłady zasad Azure](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Obliczanie**||
| [Zatwierdzone obrazów maszyn wirtualnych](scripts/allowed-custom-images.md) | Wymaga tylko zatwierdzone niestandardowe obrazy wdrożenia w danym środowisku. Należy określić tablicę zatwierdzony obraz identyfikatorów. |
| [Inspekcji, gdy maszyna wirtualna nie używa dysku zarządzanego](scripts/create-vm-managed-disk.md) | Inspekcja po utworzeniu maszyny wirtualnej, który nie korzysta z dysków zarządzanych.|
| [Inspekcji, jeśli rozszerzenie nie istnieje.](scripts/audit-ext-not-exist.md) | Inspekcje, jeśli rozszerzenie nie została wdrożona z maszyną wirtualną. Należy określić wydawcy rozszerzenia i typu, aby sprawdzić, czy została ona wdrożona. |
| [Zezwalaj na niestandardowe obrazu maszyny Wirtualnej z grupy zasobów](scripts/allow-custom-vm-image.md) |  Wymaga się, że niestandardowe obrazy pochodzą z grupy zasobów zatwierdzone. Należy określić nazwę grupy zasobów zatwierdzone. |
| [Odmów korzyści Użyj hybrydowego](scripts/deny-hybrid-use.md) | Zabrania używania programu Azure hybrydowego Użyj korzyści (AHUB). Użyj, jeśli nie chcesz zezwolić na korzystanie z lokalnych licencji. |
| [Niedozwolone rozszerzeń maszyny Wirtualnej](scripts/not-allowed-vm-ext.md) | Zabrania używania określonych rozszerzeń. Należy określić tablicę zawierającą typy rozszerzeń zabronione. |
| [Zezwalaj tylko określonego obrazu platformy maszyny Wirtualnej](scripts/allow-certain-vm-image.md) | Wymaga określonej wersji UbuntuServer użycia maszyn wirtualnych. |
| [Tworzenie maszyny Wirtualnej za pomocą zarządzania dyskami](scripts/use-managed-disk-vm.md) | Wymaga użycia dysków zarządzanych maszyn wirtualnych.|
|**Monitorowanie**||
| [Ustawienie diagnostyczne inspekcji](scripts/audit-diag-setting.md) | Inspekcje, jeśli ustawienia diagnostyki nie jest włączone dla określonych typów zasobów. Należy określić tablicę typów zasobów, aby sprawdzić, czy są włączone ustawienia diagnostyki. |
|**Konwencje nazw i tekst**||
| [Zezwalaj na wiele wzorce nazw](scripts/allow-multiple-name-patterns.md) | Zezwalaj na jednym z wielu Nazwa do użycia dla zasobów. |
| [Wymagaj like wzór](scripts/enforce-like-pattern.md) | Upewnij się, że nazwy zasobów spełniają warunek like dla wzorca. |
| [Wymagaj dopasowania wzorca](scripts/enforce-match-pattern.md) | Upewnij się, że nazwy zasobu jest zgodna z wzorcem nazw. |
| [Wymaganie tag dopasowania wzorca](scripts/enforce-tag-match-pattern.md) | Upewnij się, że wartość tagu jest zgodna z wzorcem tekstu. |
|**Sieć**||
| [Dozwolone jednostki SKU bramy aplikacji](scripts/allowed-app-gate-sku.md) | Wymaga użycia zatwierdzone jednostki SKU bramy aplikacji. Należy określić tablicę zatwierdzone jednostki SKU. |
| [Inspekcji, jeśli nie włączono obserwatora sieciowego dla regionu](scripts/net-watch-not-enabled.md) | Inspekcje, jeśli nie włączono obserwatora sieciowego dla określonego regionu. Należy określić nazwę regionu, aby sprawdzić, czy włączono obserwatora sieciowego. |
| [X NSG na każdej karcie interfejsu Sieciowego](scripts/nsg-on-nic.md) | Wymaga określonej sieciowej grupy zabezpieczeń jest z każdego interfejsu sieci wirtualnej. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Grupa NSG X w każdej podsieci](scripts/nsg-on-subnet.md) | Wymaga określonej sieciowej grupy zabezpieczeń jest z wszystkich podsieci wirtualnych. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Dozwolone przepustowości Express Route](scripts/allowed-er-band.md) | Wymaga użycia określonego pasma express trasy. Należy określić tablicę jednostki magazynowe, które można określić dla Express Route. |
| [Dozwolone lokalizacja komunikacji równorzędnej Expressroute](scripts/allowed-peering-er.md) | Wymaga użycia Express trasy lokalizacje komunikacji równorzędnej. Należy określić tablicę dozwolonych lokalizacji komunikacji równorzędnej. |
| [Dozwolone Express Route jednostki SKU](scripts/allowed-er-skus.md) | Wymaga użycia trasy Express SKU zatwierdzone. Należy określić tablicę dozwolonych jednostki SKU. |
| [Dozwolone jednostki SKU usługi równoważenia obciążenia](scripts/allowed-lb-skus.md) | Wymaga użycia zatwierdzone jednostki SKU usługi równoważenia obciążenia. Należy określić tablicę dozwolonych jednostki SKU. |
| [Brak sieci komunikację równorzędną, aby ER sieci](scripts/no-peering-er-net.md) | Zabrania używania sieć równorzędna z przypisaniem go do sieci w grupie określonego zasobu. Użyj, aby uniemożliwić połączenia z infrastrukturą sieci zarządzanej centralnej. Należy określić nazwę grupy zasobów, aby zapobiec skojarzenia. |
| [Żaden użytkownik nie zdefiniowano tabeli tras](scripts/no-user-def-route-table.md)  |Zabrania sieci wirtualne wdrażane z tabelą tras zdefiniowanych przez użytkownika. |
| [Dozwolone jednostki SKU bramy sieci wirtualnej](scripts/allowed-vn-gate-sku.md) | Wymaga użycia zatwierdzone jednostki SKU i bramy typu bram sieci wirtualnej. Należy określić tablicę zatwierdzone jednostki SKU i tablicę typów zatwierdzonych bramy. |
| [Użyj zatwierdzonych podsieci maszyny Wirtualnej w interfejsach sieciowych](scripts/use-approved-subnet-vm-nics.md) | Wymaga użycia zatwierdzonych podsieci interfejsów sieciowych. Należy określić identyfikator zatwierdzonych podsieci. |
| [Użyj zatwierdzonych sieci wirtualnej maszyny Wirtualnej w interfejsach sieciowych](scripts/use-approved-vnet-vm-nics.md) | Wymaga użycia interfejsów sieciowych zatwierdzonych sieci wirtualnej. Należy określić identyfikator zatwierdzonych sieci wirtualnej. |
|**Tagi**||
| [Inicjatywy zasad tagi rozliczeń](scripts/billing-tags-policy-init.md) | Wymaga wartości określonego tagu dla Centrum kosztów, nazwa produktu. Przy użyciu wbudowanych zasad określa wymagane tagi i wymusza ich stosowanie. Wymagane wartości tagów są określane przez Ciebie.  |
| [Wymuszanie tagu i jej wartość na temat grup zasobów](scripts/enforce-tag-rg.md) | Wymaga tagu i wartość dla grupy zasobów. Należy określić nazwa wymaganego tagu i wartość.  |
|**SQL**||
| [Inspekcja poziom bazy danych SQL Inspekcja](scripts/audit-sql-db-audit-setting.md) | Inspekcja ustawienia inspekcji bazy danych SQL, jeśli te ustawienia są niezgodne z określonej wartości. Można określić wartość, która wskazuje, czy ustawienia inspekcji powinny być włączone lub wyłączone.  |
| [Stan szyfrowania przezroczysty danych inspekcji](scripts/audit-trans-data-enc-status.md) | Inspekcja SQL bazy danych przezroczystego szyfrowania danych, jeśli nie jest włączona.  |
| [Ustawienie wykrywania zagrożeń poziomu bazy danych inspekcji](scripts/audit-db-threat-det-setting.md) | Inspekcja zasady alertów zabezpieczeń bazy danych SQL, jeśli te zasady nie są skonfigurowane do określonego stanu. Można określić wartość, która wskazuje, czy wykrywanie zagrożeń jest włączone.  |
| [Poziom programu SQL Server audit Inspekcja](scripts/audit-sql-ser-leve-audit-setting.md) | Inspekcja ustawienia inspekcji serwera SQL, jeśli te ustawienia są niezgodne z określonej wartości. Można określić wartość, która wskazuje, czy ustawienia inspekcji powinny być włączone lub wyłączone. |
| [Inspekcja wykrywania zagrożeń poziomu serwera](scripts/audit-sql-ser-threat-det-setting.md) | Inspekcja zasady alertów zabezpieczeń bazy danych SQL, jeśli te zasady nie są skonfigurowane do określonego stanu. Można określić wartość, która wskazuje, czy wykrywanie zagrożeń jest włączone.  |
| [Inspekcja żaden administrator usługi Azure Active Directory](scripts/audit-no-aad-admin.md) | Inspekcja po żaden administrator usługi Azure Active Directory przypisane do programu SQL server. |
| [Dozwolone jednostki SKU bazy danych SQL](scripts/allowed-sql-db-skus.md) | Wymaga wersji zatwierdzonych używać bazy danych SQL. Należy określić tablicę dozwolonych identyfikatorów jednostki SKU lub tablicę dozwolonych nazw jednostki SKU. |
|**Storage**||
| [Dozwolone jednostki SKU dla konta magazynu i maszyny wirtualne](scripts/allowed-skus-storage.md) | Wymaga użycia zatwierdzone jednostki SKU konta magazynu i maszyn wirtualnych. Używa wbudowanych zasad w celu zapewnienia zatwierdzone jednostki SKU. Należy określić tablicę zatwierdzonych maszyn wirtualnych jednostki SKU i tablicę konta magazynu zatwierdzone jednostki SKU. |
| [Upewnij się, ruch https tylko dla konta magazynu](scripts/ensure-https-stor-acct.md) | Wymaga konta magazynu do użycia ruchu HTTPS.  |
| [Odmowa dostępu chłodnych Obsługa poziomów w przypadku kont magazynu](scripts/deny-cool-access-tiering.md) | Zabrania używania chłodnych dostępu, Obsługa poziomów w przypadku kont magazynu obiektów blob.  |
| [Upewnij się, szyfrowanie plików magazynu](scripts/ensure-store-file-enc.md) | Wymaga szyfrowania plików, że jest włączony dla konta magazynu.  |
|**Wbudowane zasady**||
| [Dozwolonych lokalizacji](scripts/allowed-locs.md) | Wymaga wdrożenia wszystkich zasobów do lokalizacji zatwierdzone. Należy określić tablicę lokalizacje zatwierdzone.  |
| [Typy zasobów dozwolonych](scripts/allowed-res-types.md) | Zapewnia tylko typy zasobów zatwierdzonych są wdrażane. Należy określić tablicę typów zasobów, które są dozwolone.  |
| [Dozwolone konto magazynu jednostki SKU](scripts/allowed-stor-acct-skus.md) | Wymaga użycia wersji zatwierdzonych kont magazynu. Należy określić tablicę zatwierdzone jednostki SKU. |
| [Stosuje tag i wartość domyślną](scripts/apply-tag-def-val.md) | Dołącza określonego tagu nazwy i wartości, jeśli nie podano tagu. Można określić nazwy tagu i wartości do zastosowania.  |
| [Inspekcja szyfrowania bazy danych SQL](scripts/sql-database-encryption-audit.md) | Inspekcje, jeśli baza danych SQL nie ma przezroczystego szyfrowania danych włączone. |
| [SQL Server audit ustawień inspekcji](scripts/sql-server-audit.md) | Inspekcji oparte na czy są włączone ustawienia inspekcji serwera SQL. |
| [Wymuszanie szyfrowania usługi Data Lake Store](scripts/enforce-datalakestore-encryption.md) | Odrzuca wszystkie konta usługi Data Lake Store, które nie mają włączone szyfrowanie. |
| [Wymuszanie tagu i jego wartość](scripts/enforce-tag-val.md) | Wymagana jest nazwa określonego tagu i wartość. Określ jest nazwa tagu i wartości do wymuszania.  |
| [Niedozwolone typy zasobów](scripts/not-allowed-res-type.md) | Zabrania używania wdrożenia typów określonego zasobu. Należy określić tablicę typów zasobów, aby zablokować.  |
| [Wymaga programu SQL Server w wersji 12.0](scripts/req-sql-12.md) | Wymaga serwerów SQL w wersji 12.0.  |
| [Wymagaj szyfrowania konta magazynu](scripts/req-store-acct-enc.md) | Wymaga szyfrowania blob Użyj konta magazynu.  |
