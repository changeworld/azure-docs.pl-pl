---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163945"
---
Ten artykuł zawiera zalecenia dotyczące zabezpieczeń dla usługi Azure Virtual Machines. Wdrożenie tych zaleceń pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności i poprawi ogólne zabezpieczenia rozwiązań aplikacji sieci Web. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, Przeczytaj [udostępnione obowiązki w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Niektóre zalecenia zawarte w tym artykule mogą być automatycznie monitorowane przez Azure Security Center. Azure Security Center to pierwszy wiersz obrony w ochronie zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich rozwiązywania.

- Aby uzyskać więcej informacji o Azure Security Center zaleceniach, zapoznaj się [z zaleceniami dotyczącymi zabezpieczeń w programie Azure Security Center](../articles/security-center/security-center-recommendations.md).
- Aby uzyskać informacje na temat Azure Security Center, zobacz [co to jest Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Zalecenia

| Category | Zalecenie | Komentarze | Centrum zabezpieczeń |
|-|-|----|--|
| Ogólne | Podczas kompilowania niestandardowych obrazów maszyn wirtualnych Użyj najnowszych aktualizacji | Przed utworzeniem obrazów upewnij się, że wszystkie najnowsze aktualizacje są zainstalowane dla systemu operacyjnego i wszystkich aplikacji, które będą częścią obrazu.  | - |
| Ogólne | Bieżące przechowywanie maszyn wirtualnych | [Update Management](../articles/automation/automation-update-management.md) rozwiązanie w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego na komputerach z systemem Windows i Linux na platformie Azure | [Tak](../articles/security-center/security-center-apply-system-updates.md) |
| Ogólne | Tworzenie kopii zapasowych maszyn wirtualnych | [Azure Backup](../articles/backup/backup-overview.md) pomaga chronić dane aplikacji przy minimalnych kosztach operacyjnych. Błędy aplikacji mogą powodować uszkodzenia danych, a błędy użytkowników — usterki aplikacji. W przypadku Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione. | - |
| Ogólne | Używanie wielu maszyn wirtualnych w celu uzyskania większej odporności i dostępności | Jeśli maszyna wirtualna uruchamia aplikacje wymagające wysokiej dostępności, należy korzystać z wielu maszyn wirtualnych lub [zestawów dostępności](../articles/virtual-machines/windows/manage-availability.md) | - |
| Ogólne | Zastosuj strategię ciągłości działania i odzyskiwania po awarii (BCDR) | Azure Site Recovery umożliwia wybór spośród różnych opcji, które są przeznaczone do obsługi ciągłości biznesowej. Obsługuje ona różne replikacje i przełącza się w tryb failover. Aby uzyskać więcej informacji, zobacz [Informacje o Site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| Zarządzanie tożsamościami i dostępem | Scentralizowanie uwierzytelniania maszyn wirtualnych | Uwierzytelnianie maszyn wirtualnych z systemami Windows i Linux można scentralizować przy użyciu [uwierzytelniania usługi Azure AD](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Bezpieczeństwo danych | Szyfrowanie dysków systemu operacyjnego | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ułatwia szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS. Szyfrowanie dysków sprawia, że zawartość nie jest czytelna bez wymaganych kluczy. Szyfrowanie dysków chroni przechowywane dane przed nieautoryzowanym dostępem, które w przeciwnym razie byłyby możliwe w przypadku skopiowania dysku| [Tak](../articles/security-center/security-center-apply-disk-encryption.md) |
| Bezpieczeństwo danych | Szyfrowanie dysków danych | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ułatwia szyfrowanie dysków maszyn wirtualnych z systemami Windows i Linux IaaS. Szyfrowanie dysków sprawia, że zawartość nie jest czytelna bez wymaganych kluczy. Szyfrowanie dysków chroni przechowywane dane przed nieautoryzowanym dostępem, które w przeciwnym razie byłyby możliwe w przypadku skopiowania dysku| -  |
| Bezpieczeństwo danych | Ogranicz zainstalowane oprogramowanie | Ograniczanie zainstalowanego oprogramowania do programu, co jest wymagane do pomyślnego zaimplementowania rozwiązania, pozwala zmniejszyć obszar ataków na rozwiązanie | - |
| Bezpieczeństwo danych | Używanie oprogramowania antywirusowego/chroniącego przed złośliwym | Na platformie Azure można korzystać z oprogramowania chroniącego przed złośliwym kodem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky pomogą. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, programami reklamujące i innymi zagrożeniami. Ochronę przed złośliwym oprogramowaniem firmy Microsoft można wdrożyć na podstawie obciążeń aplikacji przy użyciu podstawowej, bezpiecznej lub zaawansowanej konfiguracji niestandardowej. Aby uzyskać więcej informacji na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure, zobacz [Microsoft chroniący przed złośliwym kodem dla platformy azure Virtual Machines Cloud Services](../articles/security/azure-security-antimalware.md) | - |
| Bezpieczeństwo danych | Bezpieczne przechowywanie kluczy i wpisów tajnych | Uproszczenie zarządzania tajemnicami i kluczami przez udostępnienie właścicieli aplikacji przy użyciu opcji zabezpieczonej centralnie zarządzanej pozwala zmniejszyć ryzyko przypadkowego naruszenia lub wycieków. Azure Key Vault mogą bezpiecznie przechowywać klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) certyfikowane do poziomu 2 trybu FIPS 140-2. Jeśli potrzebujesz przechowywać klucze i wpisy tajne przy użyciu poziomu standardu FIPs 140,2, możesz użyć [dedykowanego modułu HSM platformy Azure](../articles/dedicated-hsm/overview.md) | - |
| Networking | Ograniczanie dostępu do portów zarządzania | Osoby atakujące skanują zakresy adresów IP chmury publicznej pod kątem otwartych portów zarządzania i podejmują próby ataków typu "łatwe", takie jak typowe hasła i znane luki w zabezpieczeniach. [Dostęp do maszyn wirtualnych w czasie just-in-Time (JIT)](../articles/security-center/security-center-just-in-time.md) może służyć do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, co pozwala ograniczyć narażenie na ataki, zapewniając łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. | - |
| Networking | Ogranicz dostęp do sieci | Sieciowe grupy zabezpieczeń pozwalają ograniczyć dostęp do sieci i kontrolować liczbę narażonych punktów końcowych. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](../articles/virtual-network/manage-network-security-group.md) | - |
| Monitorowanie | Monitorowanie maszyn wirtualnych | [Za pomocą usługi Azure monitor dla maszyn wirtualnych](../articles/azure-monitor/insights/vminsights-overview.md) można monitorować stan maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych. Problemy z wydajnością maszyny wirtualnej mogą prowadzić do przerw w działaniu usługi, co narusza zasadę zabezpieczeń. | - |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby sprawdzić, czy istnieją dodatkowe wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz temat [bezpieczna programowanie dokumentacji](../articles/security/fundamentals/abstract-develop-secure-apps.md).