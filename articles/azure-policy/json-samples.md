---
title: Przykłady szablonów zasad usługi Azure Policy
description: Przykłady JSON dla usługi Azure Policy
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4b9096c1fb0d9ee74849e259a6e0af2486c5d29b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195128"
---
# <a name="templates-for-azure-policy"></a>Szablony dla usługi Azure Policy

Poniższa tabela zawiera linki do szablonów JSON dla usługi Azure Policy. Przykłady znajdują się w [repozytorium przykładów usługi Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Obliczanie**||
| [Approved VM images](scripts/allowed-custom-images.md) (Zatwierdzone obrazy maszyn wirtualnych) | Wymaga, aby w środowisku były wdrażane tylko zatwierdzone niestandardowe obrazy. Należy określić tablicę identyfikatorów zatwierdzonych obrazów. |
| [Audit when VM does not use Managed Disk](scripts/create-vm-managed-disk.md) (Sprawdzanie, czy maszyna wirtualna nie używa dysku zarządzanego) | Sprawdza, czy utworzona maszyna wirtualna nie korzysta z dysków zarządzanych.|
| [Audit if extension does not exist](scripts/audit-ext-not-exist.md) (Sprawdzanie, czy rozszerzenie nie istnieje) | Sprawdza, czy rozszerzenie nie zostało wdrożone z maszyną wirtualną. Należy określić typ i wydawcę rozszerzenia, aby sprawdzić, czy zostało ono wdrożone. |
| [Allow custom VM image from a Resource Group](scripts/allow-custom-vm-image.md) (Zezwalanie na korzystanie z niestandardowego obrazu maszyny wirtualnej pochodzącego z grupy zasobów) |  Wymaga, aby niestandardowe obrazy pochodziły z zatwierdzonej grupy zasobów. Należy określić nazwę zatwierdzonej grupy zasobów. |
| [Deny hybrid use benefit](scripts/deny-hybrid-use.md) (Uniemożliwianie użycia korzyści użycia hybrydowego) | Uniemożliwia użycie korzyści użycia hybrydowego platformy Azure (AHUB). Zastosuj tę zasadę, jeśli nie chcesz zezwolić na korzystanie z licencji lokalnych. |
| [Not allowed VM Extensions](scripts/not-allowed-vm-ext.md) (Niedozwolone rozszerzenia maszyny wirtualnej) | Uniemożliwia użycie określonych rozszerzeń. Należy określić tablicę zawierającą typy zabronionych rozszerzeń. |
| [Only allow a certain VM platform image](scripts/allow-certain-vm-image.md) (Zezwalanie tylko na obraz określonej platformy maszyn wirtualnych) | Wymaga użycia określonej wersji systemu UbuntuServer na maszynach wirtualnych. |
| [Create VM using Managed Disk](scripts/use-managed-disk-vm.md) (Tworzenie maszyny wirtualnej używającej dysku zarządzanego) | Wymaga używania dysków zarządzanych przez maszyny wirtualne.|
|**Monitorowanie**||
| [Audit diagnostic setting](scripts/audit-diag-setting.md) (Przeprowadzaj inspekcję ustawienia diagnostyki) | Przeprowadza inspekcję, czy ustawienia diagnostyki nie zostały włączone dla określonych typów zasobów. Należy określić tablicę typów zasobów, aby sprawdzić, czy ustawienia diagnostyki są włączone. |
|**Konwencje nazw i tekstu**||
| [Allow multiple name patterns](scripts/allow-multiple-name-patterns.md) (Zezwalaj na wiele wzorców nazw) | Zezwala na używanie jednego z wielu wzorców nazw dla zasobów. |
| [Require like pattern](scripts/enforce-like-pattern.md) (Wymagaj wzorca like) | Zapewnia, że nazwy zasobów spełniają warunek like dla wzorca. |
| [Require match pattern](scripts/enforce-match-pattern.md) (Wymagaj zgodności z wzorcem) | Zapewnia, że nazwy zasobów są zgodne ze wzorcem nazewnictwa. |
| [Require tag match pattern](scripts/enforce-tag-match-pattern.md) (Wymagaj zgodności tagu z wzorcem) | Zapewnia, że wartość tagu jest zgodna z wzorcem tekstu. |
|**Sieć**||
| [Allowed Application Gateway SKUs](scripts/allowed-app-gate-sku.md) (Dozwolone jednostki SKU bramy aplikacji) | Wymaga, aby bramy aplikacji używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Audit if Network Watcher is not enabled for region](scripts/net-watch-not-enabled.md) (Przeprowadzanie inspekcji stanu włączenia usługi Network Watcher w danym regionie) | Przeprowadza inspekcję stanu włączenia usługi Network Watcher w danym regionie. Należy określić nazwę regionu, aby sprawdzić, czy usługa Network Watcher jest włączona. |
| [NSG X on every NIC](scripts/nsg-on-nic.md) (Sieciowa grupa zabezpieczeń X na każdej karcie sieciowej) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdym wirtualnym interfejsem sieciowym. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [NSG X on every subnet](scripts/nsg-on-subnet.md) (Sieciowa grupa zabezpieczeń X w każdej podsieci) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdą podsiecią wirtualną. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Allowed Express Route bandwidth](scripts/allowed-er-band.md) (Dozwolona przepustowość usługi Express Route) | Wymaga, aby usługa Express Route używała określonego zestawu przepustowości. Należy podać tablicę jednostek SKU, które można określać dla usługi Express Route. |
| [Allowed Peering Location for Express Route](scripts/allowed-peering-er.md) (Dozwolona lokalizacja komunikacji równorzędnej dla usługi Express Route) | Wymaga, aby usługa Express Route używała określonych lokalizacji komunikacji równorzędnej. Należy określić tablicę dozwolonych lokalizacji komunikacji równorzędnej. |
| [Allowed Express Route SKUs](scripts/allowed-er-skus.md) (Dozwolone jednostki SKU usługi Express Route) | Wymaga, aby usługa Express Route używała zatwierdzonej jednostki SKU. Należy określić tablicę dozwolonych jednostek SKU. |
| [Allowed Load Balancer SKUs](scripts/allowed-lb-skus.md) (Dozwolone jednostki SKU modułu równoważenia obciążenia) | Wymaga, aby moduły równoważenia obciążenia używały zatwierdzonej jednostki SKU. Należy określić tablicę dozwolonych jednostek SKU. |
| [No network peering to ER network](scripts/no-peering-er-net.md) (Uniemożliwianie korzystania z sieci równorzędnych w sieci ER) | Uniemożliwia skojarzenie sieci równorzędnej z siecią znajdującą się w określonej grupie zasobów. Pozwalają one zapobiegać nawiązywaniu połączeń z centralnie zarządzaną infrastrukturą sieci. Należy określić nazwę grupy zasobów, aby uniemożliwić skojarzenie. |
| [No User Defined Route Table](scripts/no-user-def-route-table.md) (Uniemożliwianie używania tabeli tras zdefiniowanej przez użytkownika)  |Uniemożliwia wdrażanie sieci wirtualnych przy użyciu tabeli tras zdefiniowanej przez użytkownika. |
| [Allowed Virtual Network Gateway SKUs](scripts/allowed-vn-gate-sku.md) (Dozwolone jednostki SKU bramy sieci wirtualnej) | Wymaga, aby bramy sieci wirtualnych miały określony typ i używały zatwierdzonych jednostek SKU. Należy określić tablicę zatwierdzonych jednostek SKU i tablicę zatwierdzonych typów bram. |
| [Use approved subnet for VM network interfaces](scripts/use-approved-subnet-vm-nics.md) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](scripts/use-approved-vnet-vm-nics.md) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |
|**Tagi**||
| [Billing tags policy initiative](scripts/billing-tags-policy-init.md) (Inicjatywa zasad tagów rozliczeń) | Wymaga określonych wartości tagów centrum kosztu i nazwy produktu. Przy użyciu wbudowanych zasad określa wymagane tagi i wymusza ich stosowanie. Wymagane wartości tagów są określane przez Ciebie.  |
| [Enforce tag and its value on resource groups](scripts/enforce-tag-rg.md) (Wymuś tag i jego wartość w grupach zasobów) | Wymaga tagu i wartości w grupie zasobów. Należy określić wymaganą nazwę i wartość tagu.  |
|**SQL**||
| [Audit SQL DB Level Audit Setting](scripts/audit-sql-db-audit-setting.md) (Przeprowadzanie inspekcji ustawień inspekcji na poziomie usługi SQL Database) | Sprawdza, czy ustawienia inspekcji usługi SQL Database nie są zgodne z określonym ustawieniem. Należy określić wartość, która wskazuje, czy ustawienia inspekcji mają być włączone czy wyłączone.  |
| [Audit transparent data encryption status](scripts/audit-trans-data-enc-status.md) (Inspekcja stanu przezroczystego szyfrowania danych) | Sprawdza, czy usługa SQL Database nie ma włączonego przezroczystego szyfrowania danych.  |
| [Audit DB level threat detection setting](scripts/audit-db-threat-det-setting.md) (Przeprowadzanie inspekcji ustawień wykrywania zagrożeń na poziomie bazy danych) | Przeprowadza inspekcję zasad alertu zabezpieczeń bazy danych SQL, dla których nie ustawiono określonego stanu. Należy określić wartość, która wskazuje, czy wykrywanie zagrożeń ma być włączone czy wyłączone.  |
| [Audit SQL Server Level Audit Setting](scripts/audit-sql-ser-leve-audit-setting.md) (Przeprowadzanie inspekcji ustawień inspekcji na poziomie programu SQL Server) | Sprawdza, czy ustawienia inspekcji programu SQL Server nie są zgodne z określonym ustawieniem. Należy określić wartość, która wskazuje, czy ustawienia inspekcji mają być włączone czy wyłączone. |
| [Audit Server level threat detection setting](scripts/audit-sql-ser-threat-det-setting.md) (Przeprowadzanie inspekcji ustawień wykrywania zagrożeń na poziomie serwera) | Przeprowadza inspekcję zasad alertu zabezpieczeń bazy danych SQL, dla których nie ustawiono określonego stanu. Należy określić wartość, która wskazuje, czy wykrywanie zagrożeń ma być włączone czy wyłączone.  |
| [Audit no Azure Active Directory administrator](scripts/audit-no-aad-admin.md) (Sprawdzanie, czy nie skonfigurowano konta administratora usługi Azure Active Directory) | Sprawdzanie, czy do programu SQL Server nie przypisano administratora usługi Azure Active Directory. |
| [Allowed SQL DB SKUs](scripts/allowed-sql-db-skus.md) (Dozwolone jednostki SKU usługi SQL DB) | Wymaga, aby bazy danych SQL używały zatwierdzonych jednostek SKU. Należy określić tablicę identyfikatorów dozwolonych jednostek SKU lub tablicę nazw dozwolonych jednostek SKU. |
|**Storage**||
| [Allowed SKUs for Storage Accounts and Virtual Machines](scripts/allowed-skus-storage.md) (Dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych) | Wymaga, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu. |
| [Ensure https traffic only for storage account](scripts/ensure-https-stor-acct.md) (Zapewnienie używania tylko ruchu https dla konta magazynu) | Wymaga używania ruchu HTTPS przez konta magazynu.  |
| [Deny cool access tiering for storage accounts](scripts/deny-cool-access-tiering.md) (Uniemożliwianie użycia warstwy dostępu Chłodna dla kont magazynu) | Uniemożliwia użycie warstwy dostępu Chłodna dla kont usługi Blob Storage.  |
| [Ensure storage file encryption](scripts/ensure-store-file-enc.md) (Zapewnianie szyfrowania plików w magazynie) | Wymaga włączenia szyfrowania plików na kontach magazynu.  |
|**Zasady wbudowane**||
| [Allowed locations](scripts/allowed-locs.md) (Dozwolone lokalizacje) | Wymaga wdrożenia wszystkich zasobów w zatwierdzonych lokalizacjach. Należy określić tablicę zatwierdzonych lokalizacji.  |
| [Allowed resource types](scripts/allowed-res-types.md) (Dozwolone typy zasobów) | Zapewnia wdrażanie tylko zatwierdzonych typów zasobów. Należy określić tablicę typów zasobów, które są dozwolone.  |
| [Allowed storage account SKUs](scripts/allowed-stor-acct-skus.md) (Dozwolone jednostki SKU konta magazynu) | Wymaga, aby konta magazynu używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Apply tag and its default value](scripts/apply-tag-def-val.md) (Zastosuj tag i jego wartość domyślną) | Dołącza określoną nazwę i wartość tagu, jeśli nie podano tagu. Należy określić nazwę i wartość tagu do zastosowania.  |
| [Audit SQL Database encryption](scripts/sql-database-encryption-audit.md) (Inspekcja szyfrowania w usłudze SQL Database) | Sprawdza, czy usługa SQL Database nie ma włączonego przezroczystego szyfrowania danych. |
| [Audit SQL Server audit settings](scripts/sql-server-audit.md) (Przeprowadzanie inspekcji ustawień inspekcji programu SQL Server) | Przeprowadza inspekcję programu SQL Server w zależności od tego, czy włączono ustawienia inspekcji. |
| [Enforce Data Lake Store encryption](scripts/enforce-datalakestore-encryption.md) (Wymuszanie szyfrowania w usłudze Data Lake Store) | Uniemożliwia korzystanie z kont usługi Data Lake Store, które nie mają włączonego szyfrowania. |
| [Enforce tag and its value](scripts/enforce-tag-val.md) (Wymuś tag i jego wartość) | Wymaga określonej nazwy i wartości tagu. Należy określić nazwę i wartość tagu do wymuszenia.  |
| [Not allowed resource types](scripts/not-allowed-res-type.md) (Niedozwolone typy zasobów) | Uniemożliwia wdrażanie określonych typów zasobów. Należy określić tablicę typów zasobów do blokowania.  |
| [Require SQL Server Version 12.0](scripts/req-sql-12.md) (Wymaganie programu SQL Server w wersji 12.0) | Wymaga używania wersji 12.0 programu SQL Server.  |
| [Require storage account encryption](scripts/req-store-acct-enc.md) (Wymaganie szyfrowania konta magazynu) | Wymaga szyfrowania obiektów blob na koncie magazynu.  |
