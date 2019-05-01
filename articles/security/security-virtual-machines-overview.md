---
title: Funkcje zabezpieczeń, używane w usłudze Azure virtual machines — zabezpieczenia platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, których można użyć z usługą Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 3467050214cba6ce5723c2747d2c13e40e86609b
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872025"
---
# <a name="azure-virtual-machines-security-overview"></a>Omówienie zabezpieczeń usługi Azure Virtual Machines
Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z maszyn wirtualnych.

Azure Virtual Machines umożliwia zwinne wdrażanie szerokiego zakresu rozwiązań obliczeniowych. Usługa obsługuje program Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i usługi Azure BizTalk Services. Dlatego możesz wdrożyć dowolne obciążenie i język w niemal dowolnym systemie operacyjnym.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działania maszyny wirtualnej. Można tworzyć i wdrażać aplikacje mając pewność, że dane są chronione i bezpieczne w zabezpieczonych centrach danych.

Dzięki systemowi Azure możesz tworzyć rozszerzone zabezpieczenia i zgodnej ze standardami rozwiązania który:

* Ochrona maszyn wirtualnych przed wirusami i złośliwym oprogramowaniem.
* Szyfrowanie poufnych danych.
* Zabezpieczanie ruchu sieciowego.
* Identyfikowanie i wykrywanie zagrożeń.
* Spełnianie wymagań dotyczących zgodności.  

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Za pomocą platformy Azure można użyć ochrony przed złośliwym oprogramowaniem z dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky. To oprogramowanie pomaga chronić Twoje maszyny wirtualne przed złośliwymi plikami, oprogramowaniem reklamowym i innymi zagrożeniami.

Microsoft Antimalware dla usług Azure Cloud Services i Virtual Machines, jest to funkcja ochrony w czasie rzeczywistym, który ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania.  Microsoft Antimalware dla platformy Azure zapewnia konfigurowalnych alertów, gdy znany prób złośliwego lub niechcianego oprogramowania do zainstalowania lub uruchomienia w systemie Azure.

Microsoft Antimalware dla platformy Azure to rozwiązanie jednego agenta dla aplikacji i środowisk dzierżawy. Zostało zaprojektowane do uruchamiania w tle bez interwencji człowieka. Można wdrożyć ochrony odpowiednio do potrzeb obciążeń aplikacji, za pomocą obu podstawowe secure domyślnie lub zaawansowanych konfiguracji niestandardowej, włącznie z monitorowaniem ochrony przed złośliwym oprogramowaniem.

W przypadku wdrażania i Włącz Microsoft Antimalware dla platformy Azure dostępne są następujące podstawowe funkcje:

* **Ochrona w czasie rzeczywistym**: Monitoruje aktywność w usługach w chmurze i na maszynach wirtualnych, aby wykrywać i blokować wykonywanie złośliwego oprogramowania.
* **Zaplanowane skanowanie**: Okresowo wykonuje skanowanie docelowych do wykrywania złośliwego oprogramowania, w tym aktywnie uruchomione programy.
* **Korygowaniem złośliwego oprogramowania**: Automatycznie pobiera wykryte złośliwe oprogramowanie, takie jak usuwanie lub poddawania kwarantannie złośliwych plików i czyszczenie wpisy rejestru złośliwe działania.
* **Sygnatury aktualizacji**: Automatycznie instaluje najnowszą podpisy ochrony (definicje wirusów), aby upewnić się, że ochrony są aktualne na ustalonej częstotliwości.
* **Aktualizacje aparatu ochrony przed złośliwym oprogramowaniem**: Automatycznie aktualizuje Microsoft Antimalware dla aparatu platformy Azure.
* **Aktualizacje platformy ochrony przed złośliwym oprogramowaniem**: Automatycznie aktualizuje Microsoft Antimalware dla platformy Azure.
* **Aktywnej ochrony**: Raporty telemetrii metadanych do platformy Azure o wykryte zagrożenia i podejrzane zasobów, aby zapewnić szybką odpowiedź. Umożliwia dostarczanie w czasie rzeczywistym podpisu synchronicznego przy użyciu Microsoft Active Protection systemu (MAPS).
* **Przykłady raportowania**: Zapewnia i raporty próbkach Microsoft Antimalware dla usług platformy Azure do pomocy usługi analizy i umożliwiają rozwiązywanie problemów.
* **Wykluczenia**: Umożliwia aplikacji i administratorów usługi, aby skonfigurować określone pliki, procesy i dyski je wykluczyć z ochrony i skanowanie pod kątem wydajności i innych powodów.
* **Zbieranie zdarzeń ochrony przed złośliwym oprogramowaniem**: Rejestruje kondycję usługi ochrony przed złośliwym oprogramowaniem, podejrzanych działań i działania korygujące w dzienniku zdarzeń systemu operacyjnego i zbiera ich na swoim koncie usługi Azure storage.

Dowiedz się więcej na temat ochrony przed złośliwym oprogramowaniem, aby pomóc w ochronie maszyn wirtualnych:

* [Microsoft Antimalware dla usług Azure Cloud Services i Virtual Machines](azure-security-antimalware.md)
* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Jak zainstalować i skonfigurować Trend Micro Deep Security as a service, na maszynie Wirtualnej Windows](../virtual-machines/windows/classic/install-trend.md)
* [Jak zainstalować i skonfigurować rozwiązanie Symantec Endpoint Protection na maszyny Wirtualnej z systemem Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Rozwiązania zabezpieczeń w witrynie Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Dla bardziej zaawansowanych ochrony, należy wziąć pod uwagę przy użyciu [zaawansowanej ochrony przed zagrożeniami programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Za pomocą usługi Windows Defender ATP zapewnia następujące korzyści:

* [Zmniejszenie obszaru ataków](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Następnej generacji ochrony](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Program Endpoint protection i odpowiedzi](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatyczne badania i rozwiązywania problemów](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Zabezpieczanie wynik](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Zaawansowane myślistwo](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Zarządzanie i interfejsów API](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Ochrona przed zagrożeniami firmy Microsoft](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Więcej informacji:

* [Rozpoczynanie pracy z usługą WDATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/get-started)  
* [Omówienie funkcji WDATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Zwiększanie bezpieczeństwa może zwiększyć zabezpieczenia szyfrowania i uwierzytelniania. Dzięki przechowywaniu ich w usłudze Azure Key Vault można uprościć zarządzanie i bezpieczeństwo, wpisami tajnymi i kluczami.

Usługa Key Vault oferuje możliwość przechowywania kluczy w sprzętowych modułach zabezpieczeń z certyfikatami poświadczającymi zgodność ze standardami FIPS 140-2 (poziom 2). Klucze szyfrowania programu SQL Server, do utworzenia kopii zapasowej lub [technologii transparent data encryption](https://msdn.microsoft.com/library/bb934049.aspx) można przechowywać w usłudze Key Vault przy użyciu dowolne klucze i wpisy tajne z aplikacji. Uprawnienia i dostęp do tych chronionych elementów są zarządzane za pośrednictwem [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Więcej informacji:

* [Co to jest usługa Azure Key Vault?](../key-vault/key-vault-overview.md)
* [Blog usługi Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Szyfrowanie dysku maszyny wirtualnej

Usługa Azure Disk Encryption jest dostępna nowa funkcja szyfrowania dysków maszyn wirtualnych Windows i Linux. Usługa Azure Disk Encryption korzysta będące standardami branżowymi [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcja systemu Windows i [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi.

Rozwiązanie jest zintegrowana z usługą Azure Key Vault ułatwia kontrolowanie oraz zarządzanie tymi wpisami tajnymi w ramach subskrypcji usługi key vault i kluczami szyfrowania dysków. Zapewnia, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure Storage.

Więcej informacji:

* [Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS](../security/azure-security-disk-encryption-overview.md)
* [Szybki start: Szyfrowanie maszyny Wirtualnej IaaS Windows przy użyciu programu Azure PowerShell](../security/quick-encrypt-vm-powershell.md)

## <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej

Usługa Azure Backup to skalowalne rozwiązanie, która pomaga chronić dane Twoich aplikacji w taki sposób, bez żadnych inwestycji kapitałowych i przy minimalnych kosztach operacyjnych. Błędy aplikacji mogą powodować uszkodzenia danych, a błędy użytkowników — usterki aplikacji. W usłudze Azure Backup są chronione, maszynach wirtualnych z systemem Windows i Linux.

Więcej informacji:

* [Co to jest Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Usługa Azure Backup usługi — często zadawane pytania](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Ważnym elementem strategii BCDR organizacji jest ustalenie, jak zapewnić obciążeń firmowych i aplikacji podczas planowanych i nieplanowanych przestojów. Usługa Azure Site Recovery ułatwia organizowanie replikacji, trybu failover i odzyskiwania obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli Twoja lokalizacja główna przestanie działać.

Usługa Site Recovery:

* **Upraszcza strategii bcdr można wykorzystać**: Usługa Site Recovery ułatwia obsługę replikacji, trybu failover i odzyskiwania wielu firmowych obciążeń i aplikacji z jednej lokalizacji. Usługa Site Recovery organizuje replikację i tryb failover, ale nie przechwytuje danych aplikacji lub mieć żadnych informacji ich dotyczących.
* **Oferuje elastyczny replikację**: Przy użyciu usługi Site Recovery, można replikować obciążenia uruchomione na maszynach wirtualnych funkcji Hyper-V, maszyn wirtualnych VMware i serwerów fizycznych z systemem Windows/Linux.
* **Obsługuje tryb failover i odzyskiwanie**: Usługa Site Recovery zapewnia testowych trybów Failover odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Możesz również uruchomić planowane tryby failover (brak utraty danych) w przypadku przewidywanych przerw w działaniu lub nieplanowane tryby failover (minimalna utrata danych, zależna od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. Po przejściu w tryb failover można powrotu po awarii do lokacji głównych. Usługa Site Recovery zapewnia plany odzyskiwania, które mogą uwzględniać skrypty i skoroszyty automatyzacji platformy Azure. Dzięki nim możesz dostosować tryb failover i odzyskiwanie dla aplikacji wielowarstwowych.
* **Tworzenie dodatkowych centrów danych eliminuje**: Można replikować do lokacji dodatkowej w środowisku lokalnym lub na platformie Azure. Korzystanie z platformy Azure jako miejsca docelowego dla odzyskiwania po awarii eliminuje koszty i złożoność obsługi dodatkowej witryny. Replikowane dane są przechowywane w usłudze Azure Storage.
* **Integruje się z istniejącymi technologiami BCDR**: Usługa Site Recovery współpracuje z funkcjami BCDR innych aplikacji. Na przykład można użyć Usługa Site Recovery w celu ochrony zaplecza programu SQL Server obciążeń firmowych. Obejmuje to Natywna obsługa programu SQL Server Always On zarządzać trybem failover grup dostępności.

Więcej informacji:

* [Co to jest usługa Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Jak działa usługa Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Jakie obciążenia są chronione przez usługę Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Sieć wirtualna

Maszyny wirtualne muszą łączność sieciową. W celu spełnienia tego wymagania, platforma Azure wymaga maszyn wirtualnych, które będą podłączone do sieci wirtualnej platformy Azure.

Usługa Azure virtual network jest konstrukcją logiczną, zbudowany na podstawie sieci szkieletowej sieci fizycznej usługi Azure. Każdy logicznej sieci wirtualnej platformy Azure jest odizolowana od innych Azure sieci wirtualnej. Izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępna dla innych klientów firmy Microsoft Azure.

Więcej informacji:

* [Omówienie zabezpieczeń sieci platformy Azure](security-network-overview.md)
* [Omówienie usługi Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Funkcje sieci i powiązań dla scenariuszy dla przedsiębiorstw](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie

Usługa Azure Security Center pomaga zapobiegać, wykrywanie ich i reagowanie na zagrożenia. Usługa Security Center zapewnia możesz zwiększyć wgląd w i skuteczniejszą kontrolę ich zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Usługa Security Center pomaga zoptymalizować oraz monitorowanie zabezpieczeń maszyn wirtualnych przez:

* Zapewnianie [zalecenia dotyczące zabezpieczeń](../security-center/security-center-recommendations.md) dla maszyn wirtualnych. Przykład zalecenia obejmują: Zastosuj aktualizacje systemu, konfigurowanie listy ACL punktów końcowych, Włącz ochrony przed złośliwym oprogramowaniem, Włącz sieciowe grupy zabezpieczeń i zastosuj szyfrowanie dysków.
* Monitoruje stan maszyn wirtualnych.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)
* [Usługa Azure Security Center — często zadawane pytania](../security-center/security-center-faq.md)
* [Usługa Azure Security Center planowania i obsługi](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Zgodność

Usługa Azure Virtual Machines jest certyfikowany dla FISMA, FedRAMP, HIPAA, PCI DSS poziom 1 i innych kluczowych programów zgodności. Ten certyfikat ułatwia dla aplikacji platformy Azure do spełniania wymagań dotyczących zgodności i dla Twojej firmy w celu szerokiego zakresu krajowych i międzynarodowych wymogów prawnych.

Więcej informacji:

* [Centrum zaufania firmy Microsoft: zgodność](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Zaufana chmura: Bezpieczeństwo platformy Microsoft Azure, ochrony prywatności i zgodności](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Obliczenia poufnego

Gdy poufne przetwarzania nie jest technicznie zabezpieczenia maszyn wirtualnych, temat zabezpieczenia maszyn wirtualnych należy do wyższego poziomu podmiotu zabezpieczeń "compute". Obliczenia poufnego należy w kategorii "compute" zabezpieczeń.

Obliczenia poufnego gwarantuje, że jeśli danych jest "Wyczyść", co jest niezbędne do wydajnego przetwarzania, danych jest chroniony w zaufanym środowisku wykonywania https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE — nazywanego także enklawy), na poniższej ilustracji przedstawiono przykład .  

Trójniki upewnij się, że nie ma możliwości z danymi widoku lub operacji wewnątrz z zewnątrz, nawet w przypadku debugera. Zapewniają one nawet tylko autoryzowanym kod będzie mógł uzyskać dostęp do danych. Jeśli kod jest zmienione lub przez nikogo, operacje są odrzucane i środowiska wyłączone. TEE wymusza te zabezpieczenia stosowane w całym realizację kodu znajdujący się w nim.

Więcej informacji:

* [Wprowadzenie do przetwarzania danych poufnych platformy Azure](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Poufne obliczeń platformy Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  
