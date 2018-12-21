---
title: Co to jest dedykowany moduł HSM? — Dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Omówienie dedykowanego modułu HSM platformy Azure udostępniającego funkcje magazynu kluczy na platformie Azure, który spełnia wymogi certyfikatu FIPS 140-2 poziom 3
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080082"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co to jest dedykowany moduł HSM platformy Azure?

Dedykowany moduł HSM platformy Azure to usługa zapewniająca magazyn kluczy kryptograficznych na platformie Azure. Dedykowany moduł HSM spełnia większość najbardziej rygorystycznych wymagań dotyczących zabezpieczeń. To idealne rozwiązanie dla klientów, którzy potrzebują urządzeń zweryfikowanych w trybie FIPS 140-2 poziom 3 oraz kompletnej i wyłącznej kontroli nad urządzeniem HSM. Urządzenia HSM są wdrażane globalnie w kilku regionach świadczenia usługi Azure i można je łatwo aprowizować jako parę urządzeń oraz skonfigurować pod kątem wysokiej dostępności. Moduły HSM można również aprowizować w kilku regionach, aby zabezpieczyć się przed pracą w trybie failover na poziomie regionu. Firma Microsoft dostarcza usługę dedykowanych modułów HSM przy użyciu urządzenia [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) od firmy Gemalto. To urządzenie oferuje najwyższe poziomy wydajności oraz opcje integracji usług kryptograficznych. Po aprowizacji moduły HSM są połączone bezpośrednio z siecią wirtualną klienta. Dostęp do nich można uzyskać także za pomocą aplikacji lokalnej i narzędzi do zarządzania, konfigurując połączenie VPN typu punkt-lokacja lub lokacja-lokacja. Klienci będą nabywać oprogramowanie i dokumentację dotyczące konfiguracji urządzeń HSM i zarządzania nimi z portalu pomocy technicznej firmy Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Dlaczego warto używać dedykowanego modułu HSM platformy Azure?

### <a name="fips-140-2-level-3-compliance"></a>Zgodność ze standardem FIPS 140-2 poziom 3

Wiele organizacji musi spełniać wymogi rygorystycznych przepisów branżowych, które żądają, aby kryptograficzny magazyn kluczy był zgodny ze standardem [FIPS 140-2 poziom 3](https://csrc.nist.gov/publications/detail/fips/140/2/final). Wielodostępna usługa Azure Key Vault firmy Microsoft obecnie zapewnia zgodność z certyfikatem FIPS 140-2 poziom 2. Dedykowany moduł HSM platformy Azure zaspokaja rzeczywistą potrzebę branży usług finansowych, instytucji rządowych i innych organizacji, które muszą spełniać wymagania certyfikatu FIPS 140-2 poziom 3.

### <a name="single-tenant-devices"></a>Urządzenia dla jednej dzierżawy

Wielu naszych klientów potrzebuje urządzenia magazynu kryptograficznego obsługującego jedną dzierżawę. Usługa dedykowanego modułu HSM platformy Azure będzie umożliwiać aprowizację urządzenia fizycznego z jednego z globalnie rozproszonych centrów danych firmy Microsoft. Po aprowizowaniu u klienta tylko ten klient będzie miał dostęp do danego urządzenia.

### <a name="full-administrative-control"></a>Pełna kontrola administracyjna

Oprócz urządzeń obsługujących jedną dzierżawę wielu klientów wymaga pełnej kontroli administracyjnej oraz wyłącznego dostępu w celach administracyjnych. Po aprowizacji tylko ten klient ma wszelki dostęp administracyjny i na poziomie aplikacji do danego urządzenia. Po uzyskaniu dostępu przez klienta po raz pierwszy, co wymaga zmiany hasła, firma Microsoft nie będzie miała żadnej kontroli administracyjnej. Od tego momentu klient jest rzeczywistym jedynym dzierżawcą z pełną kontrolą administracyjną i możliwością zarządzania aplikacją. Firma Microsoft zachowuje dostęp na poziomie monitorowania (nie jest to rola administratora) na potrzeby telemetrii za pośrednictwem połączenia portu szeregowego, obejmujący monitory sprzętu, takie jak temperatura, kondycja zasilania i kondycja wentylatora. Klient może wyłączyć tę opcję, jeśli chce, jednak w takim przypadku nie będzie otrzymywać proaktywnych alertów dotyczących kondycji od firmy Microsoft.

### <a name="high-performance"></a>Wysoka wydajność

Urządzenie firmy Gemalto zostało wybrane do tej usługi ze względu na obsługę szerokiej gamy algorytmów kryptograficznych, różnorodnych systemów operacyjnych i szerokiego zakresu interfejsów API. Określony model, który został wdrożony, oferuje doskonałą wydajność dzięki obsłudze 10 000 operacji na sekundę dla szyfrowania RSA-2048. Obsługuje on 10 partycji, których można użyć na potrzeby unikatowych wystąpień aplikacji. Jest to urządzenie o małych opóźnieniach, dużej pojemności i wysokiej przepływności.

### <a name="unique-cloud-based-offering"></a>Unikatowa oferta chmurowa

Firma Microsoft zauważyła specyficzną potrzebę wśród unikatowego zestawu klientów i jest jedynym dostawcą usług w chmurze, który oferuje nowym klientom dedykowaną usługę modułu HSM zweryfikowaną w trybie FIPS 140-2 poziom 3 i oferującą taki zakres integracji aplikacji chmurowych i lokalnych.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Czy dedykowany moduł HSM platformy Azure jest dla Ciebie odpowiedni?

Dedykowany moduł HSM platformy Azure to wyspecjalizowana usługa zaspokajająca unikatowe wymagania dużych organizacji określonego typu. Z tego względu spodziewamy się, że większość klientów platformy Azure nie będzie pasować do profilu użycia dla tej usługi. Dla wielu z nich usługa Azure Key Vault okaże się bardziej odpowiednia i ekonomiczna. Aby ułatwić dopasowanie zgodne z potrzebami, określiliśmy następujące kryteria.

### <a name="best-fit"></a>Najlepsze dopasowanie

Usługa jest najbardziej odpowiednia dla scenariuszy migracji metodą „lift-and-shift”, które wymagają bezpośredniego i wyłącznego dostępu do urządzeń HSM. Przykłady:

- Migrowanie aplikacji ze środowiska lokalnego do usługi Azure Virtual Machines
- Migrowanie aplikacji z usługi Amazon AWS EC2 do wystąpień usługi Azure Virtual Machines, które korzystają z usługi AWS Cloud HSM Classic (firma Amazon nie oferuje tej usługi dla nowych klientów)
- Uruchamianie oprogramowania w otoce w usłudze Azure Virtual Machines, takiego jak Apache/Ngnix SSL Offload, Oracle TDE i ADCS

### <a name="not-a-fit"></a>Brak dopasowania

Usługi firmy Microsoft w chmurze, które obsługują szyfrowanie za pomocą kluczy zarządzanych przez klienta (np. Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL, Klucz klienta w usłudze Office 365) nie są zintegrowane z dedykowanym modułem HSM platformy Azure.

### <a name="it-depends"></a>To zależy

Wiele scenariuszy będzie zależeć od potencjalnej złożonej mieszaniny wymagań i kompromisów, na jakie można, lub nie można, pójść. Przykładem jest wymaganie zgodności z certyfikatem FIPS 140-2 poziom 3, które często jest narzucane — dedykowany moduł HSM jest obecnie jedyną opcją, jaka je spełnia.  Jeśli te narzucone wymagania nie są istotne, często trzeba będzie wybrać między usługą Azure Key Vault i dedykowanym modułem HSM na podstawie oceny różnych wymagań. Przykłady:

- Nowy kod działający na maszynie wirtualnej platformy Azure klienta
- SQL Server TDE na maszynie wirtualnej platformy Azure
- Szyfrowanie po stronie klienta usługi Azure Storage
- SQL Server i Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Następne kroki

Biorąc pod uwagę wysoce specjalistyczną naturę tej usługi, zaleca się szczegółowe zapoznanie się z niektórymi kluczowymi pojęciami opisanymi w tym zestawie dokumentacji, pełne zrozumienie cennika, pomocy technicznej i umów dotyczących poziomu usług, a następnie zapoznanie się z samouczkiem ułatwiającym aprowizowanie modułów HSM w istniejącym środowisku sieci wirtualnej. [Przewodniki po integracji firmy Gemalto](https://safenet.gemalto.com/partners/microsoft/) oraz przewodniki z instrukcjami ułatwiające wybór architektury wdrożenia także są cennym źródłem informacji.

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
