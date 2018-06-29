---
title: Funkcje zabezpieczeń platformy Azure używana z maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie podstawowe funkcje zabezpieczeń platformy Azure, które mogą być używane z maszyn wirtualnych platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 5ffba386ae6b1e1e5d18f92803bc8cb74b2ff89e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061303"
---
# <a name="azure-virtual-machines-security-overview"></a>Omówienie zabezpieczeń usługi Azure maszyny wirtualne
Maszyny wirtualne Azure umożliwia wdrażanie szeroką gamę rozwiązania informatyczne w sposób agile. Usługa obsługuje Microsoft Windows, Linux, programu Microsoft SQL Server, Oracle, IBM, SAP i usługi BizTalk Services dla platformy Azure. Dlatego mogą wdrażać dowolne obciążenia i dowolnego języka w niemal dowolnym systemie operacyjnym.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działania maszyny wirtualnej. Możesz skompilować i wdrożyć aplikacje za zapewnienie, że dane są chronione i bezpieczne w bardzo bezpieczny centrach danych.

Przy użyciu platformy Azure można tworzyć rozwiązania z rozszerzonymi zabezpieczeniami, zgodne który:

* Maszyny wirtualne ochrony przed wirusami i złośliwym oprogramowaniem.
* Szyfrowania poufnych danych.
* Zabezpieczenie ruchu sieciowego.
* Identyfikowanie i wykrywać zagrożenia.
* Spełnić wymagania zgodności.

Celem tego artykułu jest zapewnienie omówienie podstawowe funkcje zabezpieczeń platformy Azure, które mogą być używane z maszyn wirtualnych. Łącza do artykułów podać szczegóły dotyczące każdej funkcji, aby dowiedzieć się więcej.  

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem
Przy użyciu platformy Azure można użyć ochrony przed złośliwym oprogramowaniem z dostawcami zabezpieczeń, takich jak Microsoft, firmy Symantec, Trend Micro i Kaspersky. To oprogramowanie chroni maszyny wirtualne z złośliwych plików, adware i innymi zagrożeniami.

Antimalware firmy Microsoft dla usług Azure Cloud Services i maszyn wirtualnych jest możliwość ochrony w czasie rzeczywistym, która pomaga w identyfikacji i usuwania wirusy, programy szpiegujące lub inne złośliwe oprogramowanie.  Antimalware Microsoft Azure udostępnia można skonfigurować alerty, gdy wiadomo, że złośliwego lub niechcianego oprogramowania próbuje się zainstalować lub uruchomić w systemie Azure.

Antimalware Microsoft Azure to rozwiązanie jednego agenta do aplikacji i środowisk dzierżawy. Został zaprojektowany tak, aby uruchomić w tle bez udziału człowieka. Można wdrożyć ochrony oparte na potrzeby obciążeń aplikacji, z albo podstawowe secure domyślnie lub Zaawansowane Konfiguracja niestandardowa, w tym monitorowania ochrony przed złośliwym oprogramowaniem.

Podczas wdrażania i włączyć Antimalware Microsoft Azure, dostępne są następujące funkcje podstawowe:

* **Ochrona w czasie rzeczywistym**: monitoruje aktywność usług w chmurze i maszyn wirtualnych do wykrywania i blokowania złośliwego oprogramowania wykonywania.
* **Zaplanowane skanowanie**: okresowo wykonuje skanowanie docelowe do wykrywania złośliwego oprogramowania, włącznie z aktywnie działającymi programy.
* **Korygowania złośliwego oprogramowania**: automatycznie pobiera wykrytego złośliwego oprogramowania, takich jak usuwanie lub poddawania złośliwych plików i czyszczenie wpisy rejestru złośliwych działań.
* **Aktualizowanie sygnatur**: automatycznie instaluje najnowsze podpisy ochrony (definicje wirusów) w celu zapewnienia aktualności na podstawie wstępnie częstotliwości ochrony.
* **Aktualizacje aparatu ochrony przed złośliwym kodem**: automatycznie aktualizuje Antimalware Microsoft Azure przez aparat.
* **Aktualizacje platformy ochrony przed złośliwym kodem**: automatycznie aktualizuje Antimalware firmy Microsoft dla platformy Azure.
* **Aktywną ochronę**: metadane telemetrii raporty do platformy Azure o wykrytych zagrożeń i podejrzane zasobów w celu zapewnienia szybkiego odpowiedzi. Umożliwia dostarczanie w czasie rzeczywistym synchroniczne podpisu za pomocą Microsoft Active Protection System (MAPS).
* **Przykłady raportowania**: zapewnia i raporty próbek do Antimalware firmy Microsoft dla usługi Azure ułatwia uściślić usługi i włączyć rozwiązywania problemów.
* **Wykluczenia**: pozwala na konfigurowanie niektórych plików procesów, aplikacji i Administratorzy usługi i dyski, aby wykluczyć je z ochrony i skanowanie w poszukiwaniu wydajności i innych powodów.
* **Zbieranie zdarzeń ochrony przed złośliwym kodem**: rejestruje kondycję usługi ochrony przed złośliwym kodem, podejrzanych działań i korygowania akcje wykonywane w dzienniku zdarzeń systemu operacyjnego i zbiera je na koncie magazynu Azure.

Dowiedz się więcej na temat ochrony przed złośliwym oprogramowaniem w celu ochrony maszyn wirtualnych:

* [Ochrony przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze Azure i maszyn wirtualnych](azure-security-antimalware.md)
* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak zainstalować i skonfigurować Trend Micro głębokie zabezpieczeń jako usługa na maszynie Wirtualnej systemu Windows](../virtual-machines/windows/classic/install-trend.md)
* [Jak zainstalować i skonfigurować Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Rozwiązań zabezpieczeń w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń
Zwiększanie klucza zabezpieczeń może zwiększyć zabezpieczenia uwierzytelniania i szyfrowania. Można uprościć zarządzanie i bezpieczeństwo kluczy i kluczy tajnych krytyczne przez zapisanie ich w usłudze Azure Key Vault. 

Usługa Key Vault oferuje możliwość przechowywania kluczy w sprzętowych modułach zabezpieczeń z certyfikatami poświadczającymi zgodność ze standardami FIPS 140-2 (poziom 2). Klucze szyfrowania programu SQL Server do utworzenia kopii zapasowej lub [przezroczystego szyfrowania danych](https://msdn.microsoft.com/library/bb934049.aspx) wszystkie można przechowywać w magazynie kluczy z kluczy ani kluczy tajnych z aplikacji. Zarządzać uprawnień i dostępu do tych elementów chronionych za pomocą [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Więcej informacji:

* [Co to jest usługa Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Blog Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Szyfrowanie dysków maszyny wirtualnej
Szyfrowanie dysków Azure to nowa funkcja szyfrowania dysków maszyny wirtualnej systemu Windows i Linux. Szyfrowanie dysków Azure korzysta ze standardu branżowego [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcji systemu Windows i [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi.

Rozwiązanie jest zintegrowany z usługą Azure Key Vault ułatwia kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w magazynie kluczy subskrypcji. Gwarantuje, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w usłudze Azure Storage.

Więcej informacji:

* [Szyfrowanie dysków Azure dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Szyfrowanie dysków Azure dla maszyn wirtualnych systemu Windows i Linux](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Szyfrowanie maszyny wirtualnej](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej
Kopia zapasowa Azure jest skalowalna, która pomaga chronić dane aplikacji z inwestycji kapitału oraz minimalne kosztów operacyjnych. Błędy aplikacji mogą powodować uszkodzenia danych, a błędy użytkowników — usterki aplikacji. Kopia zapasowa Azure maszynach wirtualnych z systemem Windows i Linux są chronione.

Więcej informacji:

* [Co to jest Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Ścieżka szkoleniowa kopii zapasowej systemu Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Często zadawane pytania usługi Azure Backup usługi](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Istotną częścią strategii BCDR organizacji jest ustaleniem, jak zapewnić firmowych obciążeń i aplikacji działających podczas planowanych i nieplanowanych przestojów wystąpić. Usługa Azure Site Recovery pomaga organizowania replikacji, trybu failover i odzyskiwania obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli spadnie do lokalizacji głównej.

Usługa Site Recovery:

* **Upraszcza strategii BCDR**: Usługa Site Recovery ułatwia obsługę replikacji, trybu failover i odzyskiwania wielu firmowych obciążeń i aplikacji z jednej lokalizacji. Usługa Site Recovery organizuje replikację i tryb failover, ale nie przechwytuje danych aplikacji lub mieć żadnych informacji ich dotyczących.
* **Zapewnia elastyczne replikacji**: przy użyciu usługi Site Recovery, można replikować obciążenia uruchomione na maszynach wirtualnych funkcji Hyper-V, maszyny wirtualne VMware i serwery fizyczne z systemem Windows lub Linux.
* **Obsługuje tryb failover i odzyskiwania**: Usługa Site Recovery zapewnia testowy tryb failover do obsługi testowanie odzyskiwania po awarii bez wywierania wpływu na środowiska produkcyjne. Możesz również uruchomić planowane tryby failover (brak utraty danych) w przypadku przewidywanych przerw w działaniu lub nieplanowane tryby failover (minimalna utrata danych, zależna od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. Po przejściu w tryb failover można w trybie do lokacji głównych. Usługa Site Recovery zapewnia plany odzyskiwania, które mogą uwzględniać skrypty i skoroszyty automatyzacji platformy Azure. Dzięki nim możesz dostosować tryb failover i odzyskiwanie dla aplikacji wielowarstwowych.
* **Eliminuje centrów danych w dodatkowej**: można replikować do lokacji dodatkowej lokalnymi lub na platformie Azure. Za pomocą platformy Azure jako miejsca docelowego dla odzyskiwania po awarii eliminuje koszty i z utrzymywaniem lokacji dodatkowej. Replikowane dane są przechowywane w usłudze Azure Storage.
* **Integruje się z istniejącymi technologiami BCDR**: Usługa Site Recovery współpracuje z funkcjami BCDR inne aplikacje. Na przykład można użyć usługi Site Recovery w celu ochrony wewnętrznej programu SQL Server obciążeń firmowych. W tym macierzystą obsługę programu SQL Server zawsze włączone zarządzanie trybem failover grup dostępności.

Więcej informacji:

* [Co to jest Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Jak działa usługi Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Jakie obciążenia są chronione przez usługę Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Sieć wirtualna
Maszyny wirtualne muszą łączność sieciową. W celu spełnienia tego wymagania, platforma Azure wymaga maszyn wirtualnych, które będą podłączone do sieci wirtualnej platformy Azure. 

Sieć wirtualna platformy Azure jest konstrukcją logiczną, rozszerzający fizycznej Azure sieci szkieletowej. Każdy logicznej sieci wirtualnej platformy Azure jest odizolowana od innych Azure sieci wirtualnej. Izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępna dla innych klientów firmy Microsoft Azure.

Więcej informacji:

* [Przegląd zabezpieczeń sieci platformy Azure](security-network-overview.md)
* [Omówienie usługi Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Funkcje sieci i powiązań dla scenariuszy dla przedsiębiorstw](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie
Centrum zabezpieczeń Azure ułatwia zapobieganie, wykrywania i reagowania na zagrożenia. Centrum zabezpieczeń zapewnia należy zwiększyć widoczność i skuteczniejszą kontrolę zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Centrum zabezpieczeń ułatwia optymalizacji i monitorowania zabezpieczeń maszyn wirtualnych przez:

* Zapewnianie [zalecenia dotyczące zabezpieczeń](../security-center/security-center-recommendations.md) dla maszyn wirtualnych. Przykład zalecenia obejmują: stosowanie aktualizacji systemu, skonfiguruj listy ACL punktów końcowych włączenia ochrony przed złośliwym kodem, Włącz grup zabezpieczeń sieci i zastosować szyfrowanie dysku.
* Monitorowanie stanu maszyn wirtualnych.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)
* [Centrum zabezpieczeń Azure — często zadawane pytania](../security-center/security-center-faq.md)
* [Planowanie Centrum zabezpieczeń Azure i operacje](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Zgodność
Maszyny wirtualne platformy Azure jest certyfikowany do FISMA, FedRAMP HIPAA, PCI DSS poziom 1 i innych programów zgodności kluczy. Certyfikat ułatwia dla aplikacji platformy Azure spełnić wymagania zgodności i dla Twojej firmy w celu rozwiązania szeroką gamę krajowych i międzynarodowych przepisów wymagania.

Więcej informacji:

* [Centrum zaufania Microsoft: zgodności](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Zaufanych chmury: Microsoft Azure zabezpieczeń, prywatności i zgodności](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
