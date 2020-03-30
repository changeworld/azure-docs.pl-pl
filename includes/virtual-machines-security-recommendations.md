---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048564"
---
Ten artykuł zawiera zalecenia dotyczące zabezpieczeń dla maszyn wirtualnych platformy Azure. Postępuj zgodnie z tymi zaleceniami, aby pomóc w wypełnieniu zobowiązań w zakresie bezpieczeństwa opisanych w naszym modelu dla wspólnej odpowiedzialności. Zalecenia pomogą również poprawić ogólne bezpieczeństwo rozwiązań aplikacji sieci web. Aby uzyskać więcej informacji na temat tego, co firma Microsoft robi, aby wypełniać obowiązki dostawcy usług, zobacz [Współużytkowane obowiązki związane z przetwarzaniem w chmurze](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Niektóre z zaleceń tego artykułu mogą być automatycznie adresowane przez usługę Azure Security Center. Usługa Azure Security Center to pierwsza linia zabezpieczeń dla zasobów na platformie Azure. Okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zaleca się, jak rozwiązać luki w zabezpieczeniach. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące zabezpieczeń w usłudze Azure Security Center](../articles/security-center/security-center-recommendations.md).

Aby uzyskać ogólne informacje o usłudze Azure Security Center, zobacz [Co to jest Usługa Azure Security Center?](../articles/security-center/security-center-intro.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Podczas tworzenia niestandardowych obrazów maszyn wirtualnych, zastosuj najnowsze aktualizacje. | Przed utworzeniem obrazów należy zainstalować najnowsze aktualizacje dla systemu operacyjnego i wszystkich aplikacji, które będą częścią obrazu.  | - |
| Utrzymuj aktualną maszynę wirtualną. | Za pomocą rozwiązania [Zarządzanie aktualizacjami](../articles/automation/automation-update-management.md) w usłudze Azure Automation można zarządzać aktualizacjami systemu operacyjnego dla komputerów z systemem Windows i Linux na platformie Azure. | [Tak](../articles/security-center/security-center-apply-system-updates.md) |
| 19.000. | [Usługa Azure Backup](../articles/backup/backup-overview.md) pomaga chronić dane aplikacji i ma minimalne koszty operacyjne. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą powodować błędy w aplikacjach. Usługa Azure Backup chroni maszyny wirtualne z systemem Windows i Linux. | - |
| Użyj wielu maszyn wirtualnych, aby zwiększyć odporność i dostępność. | Jeśli maszyna wirtualna uruchamia aplikacje, które muszą być wysoce dostępne, należy użyć wielu maszyn wirtualnych lub [zestawów dostępności](../articles/virtual-machines/windows/manage-availability.md). | - |
| Przyjęcie strategii ciągłości działania i odzyskiwania po awarii (BCDR). | Usługa Azure Site Recovery umożliwia wybór spośród różnych opcji zaprojektowanych w celu obsługi ciągłości biznesowej. Obsługuje różne scenariusze replikacji i pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [Temat odzyskiwania witryny](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Bezpieczeństwo danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Szyfruj dyski systemu operacyjnego. | [Szyfrowanie dysków platformy Azure](../articles/security/azure-security-disk-encryption-overview.md) pomaga szyfrować dyski maszyn wirtualnych systemu Windows i Linux IaaS. Bez niezbędnych kluczy zawartość zaszyfrowanych dysków jest nieczytelna. Szyfrowanie dysku chroni przechowywane dane przed nieautoryzowanym dostępem, który w przeciwnym razie byłby możliwy, gdyby dysk został skopiowany.| [Tak](../articles/security-center/security-center-apply-disk-encryption.md) |
| Szyfrowanie dysków danych. | [Szyfrowanie dysków platformy Azure](../articles/security/azure-security-disk-encryption-overview.md) pomaga szyfrować dyski maszyn wirtualnych systemu Windows i Linux IaaS. Bez niezbędnych kluczy zawartość zaszyfrowanych dysków jest nieczytelna. Szyfrowanie dysku chroni przechowywane dane przed nieautoryzowanym dostępem, który w przeciwnym razie byłby możliwy, gdyby dysk został skopiowany.| -  |
| Ogranicz zainstalowane oprogramowanie. | Ogranicz zainstalowane oprogramowanie do tego, co jest wymagane do pomyślnego zastosowania rozwiązania. Ta wytyczna pomaga zmniejszyć powierzchnię ataku rozwiązania. | - |
| Użyj oprogramowania antywirusowego lub ochrony przed złośliwym oprogramowaniem. | Na platformie Azure można używać oprogramowania ochrony przed złośliwym oprogramowaniem od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, adware i innymi zagrożeniami. Można wdrożyć oprogramowanie antywirusowe w oparciu o obciążenia aplikacji. Użyj podstawowej bezpiecznej domyślnie lub zaawansowanej konfiguracji niestandardowej. Aby uzyskać więcej informacji, zobacz [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Bezpiecznie przechowuj klucze i wpisy tajne. | Uprość zarządzanie wpisami tajnymi i kluczami, zapewniając właścicielom aplikacji bezpieczną, centralnie zarządzaną opcję. To zarządzanie zmniejsza ryzyko przypadkowego naruszenia lub wycieku. Usługa Azure Key Vault może bezpiecznie przechowywać klucze w sprzętowych modułach zabezpieczeń (HSM), które są certyfikowane zgodnie z poziomem 2 FIPS 140-2. Jeśli do przechowywania kluczy i wpisów tajnych należy używać fips 140.2 Poziomu 3, można użyć [dedykowanego modułu HSM platformy Azure.](../articles/dedicated-hsm/overview.md) | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem 

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Scentralizuj uwierzytelnianie maszyn wirtualnych. | Uwierzytelnianie maszyn wirtualnych z systemem Windows i Linux można scentralizować przy użyciu [uwierzytelniania usługi Azure Active Directory](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Monitoruj maszyny wirtualne. | Usługi [Azure Monitor dla maszyn wirtualnych](../articles/azure-monitor/insights/vminsights-overview.md) można używać do monitorowania stanu maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych. Problemy z wydajnością maszyny Wirtualnej może prowadzić do zakłóceń w usłudze, co narusza zasadę zabezpieczeń dostępności. | - |

## <a name="networking"></a>Obsługa sieci

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Ograniczanie dostępu do portów zarządzania. | Osoby atakujące skanują zakresy adresów IP w chmurze publicznej w poszukiwaniu otwartych portów zarządzania i próbują "łatwych" ataków, takich jak typowe hasła i znane niezałatane luki w zabezpieczeniach. Możesz użyć [dostępu do maszyny Wirtualnej just-in-time (JIT),](../articles/security-center/security-center-just-in-time.md) aby zablokować ruch przychodzący do maszyn wirtualnych platformy Azure, zmniejszając narażenie na ataki, zapewniając jednocześnie łatwe połączenia z maszynami wirtualnymi, gdy są potrzebne. | - |
| Ogranicz dostęp do sieci. | Sieciowe grupy zabezpieczeń umożliwiają ograniczenie dostępu do sieci i kontrolowanie liczby ujawnionych punktów końcowych. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby dowiedzieć się więcej o dodatkowych wymaganiach dotyczących zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz [dokumentacja bezpiecznego gospodarowania](../articles/security/fundamentals/abstract-develop-secure-apps.md).
