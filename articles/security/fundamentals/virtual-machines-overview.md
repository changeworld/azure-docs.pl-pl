---
title: Funkcje zabezpieczeń używane z maszynami wirtualnymi platformy Azure
titleSuffix: Azure security
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z maszynami wirtualnymi platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657167"
---
# <a name="azure-virtual-machines-security-overview"></a>Omówienie zabezpieczeń maszyn wirtualnych platformy Azure
Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z maszynami wirtualnymi.

Maszyny wirtualne platformy Azure umożliwiają elastyczne wdrażanie szerokiej gamy rozwiązań obliczeniowych. Usługa obsługuje systemy Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i Usługi Azure BizTalk. Dzięki czemu można wdrożyć dowolne obciążenie i dowolny język w prawie każdym systemie operacyjnym.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działania maszyny wirtualnej. Aplikacje można tworzyć i wdrażać z zapewnieniem, że dane są chronione i bezpieczne w wysoce bezpiecznych centrach danych.

Za pomocą platformy Azure można tworzyć rozwiązania spełniające wymagania bezpieczeństwa, które:

* Chroń swoje maszyny wirtualne przed wirusami i złośliwym oprogramowaniem.
* Szyfruj poufne dane.
* Bezpieczny ruch sieciowy.
* Identyfikowanie i wykrywanie zagrożeń.
* Spełniaj wymagania dotyczące zgodności.  

## <a name="antimalware"></a>Antimalware

Dzięki platformie Azure możesz używać oprogramowania ochrony przed złośliwym oprogramowaniem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, adware i innymi zagrożeniami.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure to funkcja ochrony w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie.  Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure udostępnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w systemach platformy Azure.

Microsoft Antimalware for Azure to rozwiązanie jednoagentowe dla aplikacji i środowisk dzierżawy. Jest przeznaczony do pracy w tle bez interwencji człowieka. Ochronę można wdrożyć na podstawie potrzeb obciążeń aplikacji, przy zastosowaniu podstawowej bezpiecznej domyślnej lub zaawansowanej konfiguracji niestandardowej, w tym monitorowania ochrony przed złośliwym oprogramowaniem.

Dowiedz się więcej o [zabezpieczeniach przed złośliwym oprogramowaniem firmy Microsoft na platformę Azure](antimalware.md) i dostępnych podstawowych funkcjach.

Dowiedz się więcej o oprogramowaniu chroniącym przed złośliwym oprogramowaniem, które pomaga chronić maszyny wirtualne:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak zainstalować i skonfigurować trend Trend Micro Deep Security jako usługę na maszynie Wirtualnej systemu Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak zainstalować i skonfigurować program Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Rozwiązania zabezpieczające w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Aby uzyskać jeszcze bardziej wydajną ochronę, należy rozważyć użycie [zaawansowanej ochrony przed zagrożeniami w usłudze Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). W systemie Windows Defender ATP otrzymujesz:

* [Redukcja powierzchni ataku](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Ochrona nowej generacji](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Ochrona i odpowiedź punktów końcowych](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Zautomatyzowane dochodzenie i korygowanie](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Wskaźnik bezpieczeństwa](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [Zaawansowane polowania](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Zarządzanie i interfejsy API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Ochrona przed zagrożeniami firmy Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Więcej informacji:

* [Wprowadzenie do WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Omówienie możliwości WDATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Poprawa zabezpieczeń kluczy może zwiększyć ochronę szyfrowania i uwierzytelniania. Możesz uprościć zarządzanie i bezpieczeństwo krytycznych wpisów tajnych i kluczy, przechowując je w usłudze Azure Key Vault.

Usługa Key Vault umożliwia przechowywanie kluczy w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 Level 2. Klucze szyfrowania programu SQL Server do szyfrowania kopii zapasowych lub [przezroczystych danych](https://msdn.microsoft.com/library/bb934049.aspx) mogą być przechowywane w ucho. Uprawnienia i dostęp do tych elementów chronionych są zarządzane za pośrednictwem [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Więcej informacji:

* [Co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Blog usługi Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Szyfrowanie dysku maszyny wirtualnej

Szyfrowanie dysków platformy Azure to nowa funkcja szyfrowania dysków maszyn wirtualnych systemu Windows i Linux. Usługa Azure Disk Encryption korzysta z standardowej funkcji [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków z danymi.

Rozwiązanie jest zintegrowane z usługą Azure Key Vault, aby ułatwić kontrolowanie kluczy szyfrowania dysku i zarządzania nimi w ramach subskrypcji magazynu kluczy. Zapewnia, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane w spoczynku w usłudze Azure Storage.

Więcej informacji:

* [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Szybki start: szyfrowanie maszyny wirtualnej IaaS z systemem Windows za pomocą programu Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej

Usługa Azure Backup to skalowalne rozwiązanie, które pomaga chronić dane aplikacji przy zerowych inwestycjach kapitałowych i minimalnych kosztach operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą powodować błędy w aplikacjach. Dzięki usłudze Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

Więcej informacji:

* [Co to jest Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Często zadawane pytania dotyczące usługi azure backup](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Ważną częścią strategii BCDR organizacji jest ustalenie, jak utrzymać obciążenia firmowe i aplikacje uruchomione w przypadku planowanych i nieplanowanych awarii. Usługa Azure Site Recovery ułatwia organizowanie replikacji, pracy awaryjnej i odzyskiwania obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja podstawowa ustępuje.

Odzyskiwanie witryny:

* **Upraszcza strategię BCDR:** Usługa Site Recovery ułatwia obsługę replikacji, pracy awaryjnej i odzyskiwania wielu obciążeń biznesowych i aplikacji z jednej lokalizacji. Usługa Site Recovery organizuje replikację i przeczesywę pracy awaryjnej, ale nie przechwytuje danych aplikacji ani nie ma żadnych informacji na jej temat.
* **Zapewnia elastyczną replikację:** Za pomocą usługi Site Recovery można replikować obciążenia uruchomione na maszynach wirtualnych funkcji Hyper-V, maszynach wirtualnych VMware i serwerach fizycznych systemu Windows/Linux.
* **Obsługuje funkcję failover i odzyskiwanie:** Odzyskiwanie lokacji zapewnia testowanie pracy awaryjnej do obsługi ćwiczeń odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Możesz również uruchomić planowane tryby failover (brak utraty danych) w przypadku przewidywanych przerw w działaniu lub nieplanowane tryby failover (minimalna utrata danych, zależna od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. Po przemierniu awaryjnym można przywrócić po awarii lokacje główne. Usługa Site Recovery zapewnia plany odzyskiwania, które mogą uwzględniać skrypty i skoroszyty automatyzacji platformy Azure. Dzięki nim możesz dostosować tryb failover i odzyskiwanie dla aplikacji wielowarstwowych.
* **Eliminuje pomocnicze centra danych:** można replikować do dodatkowej lokacji lokalnej lub do platformy Azure. Korzystanie z platformy Azure jako miejsca docelowego odzyskiwania po awarii eliminuje koszty i złożoność utrzymania lokacji dodatkowej. Replikowane dane są przechowywane w usłudze Azure Storage.
* **Integruje się z istniejącymi technologiami BCDR**: Site Recovery współpracuje z funkcjami BCDR innych aplikacji. Na przykład można użyć usługi Site Recovery, aby chronić zapleczu programu SQL Server obciążeń korporacyjnych. Obejmuje to natywną obsługę programu SQL Server Always On w celu zarządzania obsługą awaryjną grup dostępności.

Więcej informacji:

* [Co to jest usługa Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [W jaki sposób działa usługa Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Jakie obciążenia są chronione przez usługę Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Sieć wirtualna

Maszyny wirtualne potrzebują łączności sieciowej. Aby obsługiwać to wymaganie, platforma Azure wymaga, aby maszyny wirtualne były połączone z siecią wirtualną platformy Azure.

Sieć wirtualna platformy Azure to logiczna konstrukcja zbudowana na podstawie fizycznej sieci szkieletowej platformy Azure. Każda logiczna sieć wirtualna platformy Azure jest odizolowana od wszystkich innych sieci wirtualnych platformy Azure. Ta izolacja pomaga zapewnić, że ruch sieciowy w wdrożeniach nie jest dostępny dla innych klientów platformy Microsoft Azure.

Więcej informacji:

* [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)
* [Omówienie sieci wirtualnej](/azure/virtual-network/virtual-networks-overview)
* [Funkcje sieci i partnerstwa dla scenariuszy dla przedsiębiorstw](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami bezpieczeństwa i raportowanie

Usługa Azure Security Center pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Usługa Security Center zapewnia lepszy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad tym. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Usługa Security Center ułatwia optymalizację i monitorowanie bezpieczeństwa maszyn wirtualnych przez:

* Dostarczanie [zaleceń dotyczących zabezpieczeń](/azure/security-center/security-center-recommendations) dla maszyn wirtualnych. Przykładowe zalecenia obejmują: stosowanie aktualizacji systemu, konfigurowanie punktów końcowych list ACL, włączanie ochrony przed złośliwym oprogramowaniem, włączanie grup zabezpieczeń sieciowych i stosowanie szyfrowania dysku.
* Monitorowanie stanu maszyn wirtualnych.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](/azure/security-center/security-center-intro)
* [Często zadawane pytania dotyczące usługi Azure Security Center](/azure/security-center/security-center-faq)
* [Planowanie i operacje usługi Azure Security Center](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Zgodność

Maszyny wirtualne platformy Azure są certyfikowane dla programów FISMA, FedRAMP, HIPAA, PCI DSS Level 1 i innych kluczowych programów zgodności. Ten certyfikat ułatwia aplikacjom platformy Azure spełnianie wymagań dotyczących zgodności, a firmie spełnianie szerokiej gamy krajowych i międzynarodowych wymogów regulacyjnych.

Więcej informacji:

* [Centrum zaufania firmy Microsoft: zgodność](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Zaufana chmura: zabezpieczenia, prywatność i zgodność z usługami firmy Microsoft Azure](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Poufne przetwarzanie danych

Podczas gdy poufne przetwarzanie nie jest technicznie częścią zabezpieczeń maszyny wirtualnej, temat zabezpieczeń maszyny wirtualnej należy do wyższego poziomu tematu zabezpieczeń "obliczeniowych". Poufne obliczenia należą do kategorii zabezpieczeń "obliczeniowych".

Poufne przetwarzanie zapewnia, że gdy dane są "w jasny", który jest wymagany do wydajnego https://en.wikipedia.org/wiki/Trusted_execution_environment przetwarzania, dane są chronione wewnątrz zaufanego środowiska wykonywania (TEE - znany również jako enklawy), czego przykład jest pokazany na rysunku poniżej.  

Tees upewnij się, że nie ma możliwości wyświetlania danych lub operacji wewnątrz z zewnątrz, nawet za pomocą debugera. Zapewniają nawet, że tylko autoryzowany kod jest dozwolony w dostępie do danych. Jeśli kod zostanie zmieniony lub zmodyfikowany, operacje są odrzucane, a środowisko wyłączone. TEE wymusza te zabezpieczenia przez cały wykonywania kodu w nim.

Więcej informacji:

* [Przedstawiamy poufne przetwarzanie na platformie Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Poufne przetwarzanie na platformie Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [najlepszych rozwiązaniach w zakresie zabezpieczeń](iaas.md) dla maszyn wirtualnych i systemów operacyjnych.
