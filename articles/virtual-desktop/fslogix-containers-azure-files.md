---
title: Pliki kontenerów profilu FSLogix pulpitu wirtualnego systemu Windows — Platforma Azure
description: W tym artykule opisano kontenery profilu FSLogix w plikach pulpitu wirtualnego systemu Windows i platformy Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127879"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontenery profili FSLogix i pliki platformy Azure

Usługa pulpitu wirtualnego systemu Windows zaleca kontenery profilu FSLogix jako rozwiązanie profilu użytkownika. FSLogix jest przeznaczony do poruszania się po profilach w środowiskach zdalnych komputerów, takich jak pulpit wirtualny systemu Windows. Przechowuje pełny profil użytkownika w jednym kontenerze. Podczas logowania ten kontener jest dynamicznie dołączany do środowiska komputerowego przy użyciu natywnie obsługiwanego wirtualnego dysku twardego (VHD) i wirtualnego dysku twardego funkcji Hyper-V (VHDX). Profil użytkownika jest natychmiast dostępny i pojawia się w systemie dokładnie tak samo jak natywny profil użytkownika. W tym artykule opisano, jak kontenery profilu FSLogix używane z funkcją usługi Azure Files na pulpicie wirtualnym systemu Windows.

>[!NOTE]
>Jeśli szukasz materiałów porównawczych dotyczących różnych opcji magazynu kontenerów profilu FSLogix na platformie Azure, zobacz [Opcje usługi Storage dla kontenerów profilu FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Profile użytkowników

Profil użytkownika zawiera elementy danych dotyczące poszczególnych osób, w tym informacje o konfiguracji, takie jak ustawienia pulpitu, trwałe połączenia sieciowe i ustawienia aplikacji. Domyślnie system Windows tworzy lokalny profil użytkownika, który jest ściśle zintegrowany z systemem operacyjnym.

Zdalny profil użytkownika zapewnia partycję między danymi użytkownika a systemem operacyjnym. Umożliwia zastąpienie lub zmianę systemu operacyjnego bez wpływu na dane użytkownika. W przypadku hosta sesji usług pulpitu zdalnego (RDSH) i infrastruktury pulpitu wirtualnego (VDI) system operacyjny może zostać zastąpiony z następujących powodów:

- Uaktualnienie systemu operacyjnego
- Zastąpienie istniejącej maszyny wirtualnej
- Użytkownik będący częścią połączonego (nietrwałego) środowiska RDSH lub VDI

Produkty firmy Microsoft współpracują z kilkoma technologiami dla zdalnych profili użytkowników, w tym z tymi technologiami:
- Mobilne profile użytkowników (RUP)
- Dyski profilu użytkownika (UPD)
- Roaming w stanie przedsiębiorstwa (ESR)

UPD i RUP to najczęściej stosowane technologie dla profili użytkowników w środowiskach hosta sesji pulpitu zdalnego (RDSH) i wirtualnego dysku twardego (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Wyzwania związane z poprzednimi technologiami profilu użytkownika

Istniejące i starsze rozwiązania firmy Microsoft dla profili użytkowników były stawiane różnym wyzwaniom. Żadne poprzednie rozwiązanie nie obsługiwał wszystkich potrzeb profilu użytkownika, które pochodzą ze środowiska RDSH lub VDI. Na przykład UPD nie może obsługiwać dużych plików OST, a RUP nie utrzymuje nowoczesnych ustawień.

#### <a name="functionality"></a>Funkcjonalność

W poniższej tabeli przedstawiono zalety i ograniczenia poprzednich technologii profilu użytkownika.

| Technologia | Nowoczesne ustawienia | Ustawienia win32 | Ustawienia systemu operacyjnego | Dane użytkowników | Obsługiwane na jednostce SKU serwera | Magazyn zaplecza na platformie Azure | Magazyn zaplecza lokalnie | Obsługa wersji | Kolejny czas logowania |Uwagi|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Dyski profilu użytkownika (UPD)** | Tak | Tak | Tak | Tak | Tak | Nie | Tak | Wygraj 7+ | Tak | |
| **Mobilny profil użytkownika (RUP), tryb konserwacji** | Nie | Tak | Tak | Tak | Tak| Nie | Tak | Wygraj 7+ | Nie | |
| **Roaming w stanie przedsiębiorstwa (ESR)** | Tak | Nie | Tak | Nie | Zobacz notatki | Tak | Nie | Wygraj 10 | Nie | Funkcje jednostki SKU serwera, ale brak obsługi interfejsu użytkownika |
| **Wirtualizacja środowiska użytkownika (UE-V)** | Tak | Tak | Tak | Nie | Tak | Nie | Tak | Wygraj 7+ | Nie |  |
| **Pliki w chmurze usługi OneDrive** | Nie | Nie | Nie | Tak | Zobacz notatki | Zobacz notatki  | Zobacz notatki | Wygraj 10 RS3 | Nie | Nie testowane na jednostce SKU serwera. Magazyn zaplecza na platformie Azure zależy od klienta synchronizacji. Pamięć podręczna on-prem potrzebuje klienta synchronizacji. |

#### <a name="performance"></a>Wydajność

Upd wymaga [bezpośrednie miejsca do magazynowania (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) w celu spełnienia wymagań dotyczących wydajności. Upd używa protokołu Bloku Komunikatów serwera (SMB). Kopiuje profil do maszyny Wirtualnej, w którym użytkownik jest rejestrowany. UPD z S2D to rozwiązanie, które polecamy dla pulpitu wirtualnego systemu Windows.  

#### <a name="cost"></a>Koszty

Podczas gdy klastry S2D osiągają niezbędną wydajność, koszt jest kosztowny dla klientów korporacyjnych, ale szczególnie kosztowny dla małych i średnich firm (SMB). W przypadku tego rozwiązania firmy płacą za dyski magazynu wraz z kosztem maszyn wirtualnych, które używają dysków do udziału.

#### <a name="administrative-overhead"></a>Koszty administracyjne

Klastry S2D wymagają systemu operacyjnego, który jest poprawiony, zaktualizowany i utrzymywany w bezpiecznym stanie. Te procesy i złożoność konfigurowania odzyskiwania po awarii S2D sprawiają, że S2D jest wykonalne tylko dla przedsiębiorstw z dedykowanym personelem IT.

## <a name="fslogix-profile-containers"></a>Kontenery profilów FSLogix

W dniu 19 listopada 2018 r. [Microsoft nabył FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix rozwiązuje wiele problemów z kontenerami profilu. Kluczowe z nich to:

- **Wydajność:** [Kontenery profilu FSLogix](/fslogix/configure-profile-container-tutorial/) są wysokiej wydajności i rozwiązać problemy z wydajnością, które historycznie zablokowane buforowane tryb wymiany.
- **Usługa OneDrive:** Bez kontenerów profilu FSLogix usługa OneDrive dla Firm nie jest obsługiwana w nietrwałych środowiskach RDSH lub VDI. [Najlepsze rozwiązania usługi OneDrive dla Firm i FSLogix](/fslogix/overview/) opisują sposób interakcji. Aby uzyskać więcej informacji, zobacz [Korzystanie z klienta synchronizacji na pulpitach wirtualnych](/deployoffice/rds-onedrive-business-vdi/).
- **Dodatkowe foldery:** FSLogix umożliwia rozszerzenie profili użytkowników o dodatkowe foldery.

Od momentu przejęcia firma Microsoft zaczęła zastępować istniejące rozwiązania profilu użytkownika, takie jak UPD, kontenerami profilu FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integracja z usługą Azure Files z usługą domenową Usługi domenowej Active Directory

Wydajność i funkcje kontenerów profilu FSLogix wykorzystują chmurę. 7 sierpnia 2019 r. usługa Microsoft Azure Files ogłosiła ogólną dostępność [uwierzytelniania plików platformy Azure za pomocą usługi domenowej Active Directory (AD DS)](../storage/files/storage-files-active-directory-overview.md)platformy Azure. Dzięki rozwiązaniu kosztów i nakładów administracyjnych usługa Azure Files z uwierzytelnianiem usług Azure AD DS jest rozwiązaniem klasy premium dla profilów użytkowników w usłudze pulpitu wirtualnego systemu Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Najważniejsze wskazówki dotyczące pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows oferuje pełną kontrolę nad rozmiarem, typem i liczbą maszyn wirtualnych, które są używane przez klientów. Aby uzyskać więcej informacji, zobacz [Co to jest pulpit wirtualny systemu Windows?](overview.md).

Aby upewnić się, że środowisko pulpitu wirtualnego systemu Windows jest zgodne z najlepszymi rozwiązaniami:

- Konto usługi Azure Files storage musi znajdować się w tym samym regionie co maszyny wirtualne hosta sesji.
- Uprawnienia usługi Azure Files powinny być zgodne z uprawnieniami opisanymi w [wymagania — kontenery profilów](/fslogix/fslogix-storage-config-ht).
- Każda pula hosta musi być zbudowana tego samego typu i rozmiaru maszyny Wirtualnej na podstawie tego samego obrazu wzorca.
- Każda maszyna wirtualna puli hostów musi znajdować się w tej samej grupie zasobów, aby ułatwić zarządzanie, skalowanie i aktualizowanie.
- Aby uzyskać optymalną wydajność, rozwiązanie pamięci masowej i kontener profilu FSLogix powinny znajdować się w tej samej lokalizacji centrum danych.
- Konto magazynu zawierające obraz główny musi znajdować się w tym samym regionie i subskrypcji, w którym są aprowizowane maszyny wirtualne.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować środowisko pulpitu wirtualnego systemu Windows, użyj następujących przewodników.

- Aby rozpocząć tworzenie rozwiązania do wirtualizacji pulpitu, zobacz [Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows](tenant-setup-azure-active-directory.md).
- Aby utworzyć pulę hostów w dzierżawie pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli hostów w portalu Azure Marketplace](create-host-pools-azure-marketplace.md).
- Aby skonfigurować w pełni zarządzane udziały plików w chmurze, zobacz [Konfigurowanie udziału usługi Azure Files](/azure/storage/files/storage-files-active-directory-enable/).
- Aby skonfigurować kontenery profilów FSLogix, zobacz [Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).
- Aby przypisać użytkowników do puli hostów, zobacz [Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows](manage-app-groups.md).
- Aby uzyskać dostęp do zasobów pulpitu wirtualnego systemu Windows z przeglądarki sieci Web, zobacz [Łączenie się z pulpitem wirtualnym systemu Windows](connect-web.md).
