---
title: FSLogix profilu kontenerów i plików platformy Azure w Windows pulpitu wirtualnego — Azure
description: W tym artykule opisano kontenery profilu FSLogix w plikach pulpitu wirtualnego Windows i na platformie Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497538"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontenery profili FSLogix i pliki platformy Azure

Usługa Windows wirtualnego Desktop w wersji zapoznawczej zaleca FSLogix profilu kontenery jako rozwiązanie profilu użytkownika. FSLogix jest przeznaczony do mobilny dostęp do profilów w programie środowisk, takich jak Windows pulpitu wirtualnego. Profil użytkownika pełną jest przechowywana w jednym kontenerze. Podczas logowania ten kontener jest dynamicznie dołączony do środowiska komputerowego, za pomocą natywnie obsługiwanych dysk wirtualny dysk twardy (VHD) i funkcji Hyper-V wirtualnych twardych (VHDX). Profil użytkownika jest natychmiast dostępna i jest widoczny w systemie, tak samo jak profil użytkownika natywnych.

W tym artykule opiszemy kontenery profilu FSLogix używane z usługą Azure Files. Informacje znajdują się w kontekście pulpitu wirtualnego Windows, która była [ogłoszonych na 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Profile użytkowników

Profil użytkownika zawiera elementy danych dotyczących osoby, w tym informacje o konfiguracji, takich jak ustawienia pulpitu, trwałych połączeń sieciowych i ustawień aplikacji. Domyślnie Windows tworzy profil użytkownika lokalnego, który jest ściśle zintegrowany z systemem operacyjnym.

Profil użytkownika zdalnego udostępnia partycji między dane użytkownika i systemu operacyjnego. Umożliwia systemowi operacyjnemu można zastąpić lub zmienić bez wpływu na dane użytkownika. Host sesji usług pulpitu zdalnego (RDSH) i infrastruktury pulpitu wirtualnego (VDI) system operacyjny może zostać zastąpiony w następujących sytuacjach:

- Uaktualnienie systemu operacyjnego
- Zastąpienie dla istniejących maszyn wirtualnych (VM)
- Użytkownika będącego częścią puli (nietrwałe) środowiska RDSH lub infrastruktury VDI

Produkty firmy Microsoft pracują z kilku technologii dla profilów użytkowników zdalnych, w tym tych technologii:
- Profile użytkowników mobilnych (RUP)
- Dyski profilu użytkownika (UPD)
- (ESR) roaming stanu dla przedsiębiorstw

AKTU i RUP są najczęściej używanych technologii dla profilów użytkowników w środowiskach hosta sesji usług pulpitu zdalnego (RDSH) i wirtualny dysk twardy (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Wyzwania związane z dotychczasowe używane technologie profilu użytkownika

Istniejące i starszej wersji rozwiązania firmy Microsoft dla profilów użytkowników zostało dostarczone z różnych wyzwań. Nie poprzedniego rozwiązania obsługiwane wszystkie profilu potrzeby użytkowników, które pochodzą ze środowiskiem RDSH lub infrastruktury VDI. Na przykład UPD nie może obsługiwać duże pliki OST i RUP nie utrwala ustawień modern.

#### <a name="functionality"></a>Funkcja

W poniższej tabeli przedstawiono zalety i ograniczenia dotyczące poprzedniego technologii profilu użytkownika.

| Technologia | Ustawienia nowoczesny | Ustawienia systemu Win32 | Ustawienia systemu operacyjnego | Dane użytkowników | Obsługiwane na serwerze jednostki SKU | Magazynu zaplecza na platformie Azure | Magazynu zaplecza w środowisku lokalnym | Obsługa wersji | Kolejne logowania w czasie |Uwagi|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Dyski profilu użytkownika (UPD)** | Yes | Yes | Yes | Yes | Yes | Nie | Yes | Windows 7 + | Tak | |
| **Mobilnego profilu użytkownika (RUP), trybu konserwacji** | Nie | Yes | Yes | Yes | Yes| Nie | Tak | Windows 7 + | Nie | |
| **(ESR) Roaming stanu dla przedsiębiorstw** | Tak | Nie | Yes | Nie | Zobacz uwagi | Yes | Nie | Win 10 | Nie | Funkcje na serwerze jednostki SKU, ale bez obsługi interfejsu użytkownika |
| **Wirtualizacji środowiska użytkownika (wirtualizacji środowiska użytkownika)** | Tak | Yes | Yes | Nie | Yes | Nie | Tak | Windows 7 + | Nie |  |
| **Plików w chmurze usługi OneDrive** | Nie | Nie | Nie | Yes | Zobacz uwagi | Zobacz uwagi  | Zobacz uwagi | Windows 10 RS3 | Nie | Nie przetestowane na serwerze jednostki SKU. Magazynu zaplecza na platformie Azure, zależy od klienta synchronizacji. Magazynu zaplecza środowiska lokalnego wymaga klienta synchronizacji. |

#### <a name="performance"></a>Wydajność

Wymaga UPD [bezpośrednimi miejscami do magazynowania (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) do wymagań dotyczących wydajności adresu. AKTU używa protokołu bloku komunikatów serwera (SMB). Profil, który kopiuje do maszyny Wirtualnej, w którym użytkownik jest zalogowany. AKTU z S2D była rozwiązania, zespół usług pulpitu zdalnego zalecane dla Windows pulpitu wirtualnego w trakcie okresu zapoznawczego usługi.  

#### <a name="cost"></a>Koszty

Chociaż klastry S2D uzyskać niezbędne wydajność, koszt jest kosztowne dla klientów korporacyjnych, ale szczególnie kosztowne dla małych i średnich firm (SMB). W tym rozwiązaniu firmy płacą dysków magazynu premium storage, wraz z kosztami korzystania z maszyn wirtualnych, które korzystają z dysków dla udziału.

#### <a name="administrative-overhead"></a>Koszty administracyjne

Klastry S2D wymagają systemu operacyjnego, który jest zastosowana poprawka, aktualizowanej i konserwowanej w stanie bezpiecznym. Te procesy i złożonością konfigurowania odzyskiwania po awarii S2D S2D, że jest to możliwe tylko w przypadku przedsiębiorstw mających personelu IT.

## <a name="fslogix-profile-containers"></a>Kontenery profilu FSLogix

19 listopada 2018 r. [Microsoft nabyte FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix rozwiązuje wiele wyzwań kontenera profilu. Klucz między nimi są następujące:

- **Wydajność:** [Kontenery profilu FSLogix](https://fslogix.com/products/profile-containers) to o wysokiej wydajności i rozwiąż problemy z wydajnością, które w przeszłości zablokowane trybu buforowanego programu exchange.
- **OneDrive:** Bez FSLogix profilu kontenerów usługi OneDrive dla firm jest nieobsługiwana w nietrwałe środowiskach RDSH lub infrastruktury VDI. [W usłudze OneDrive dla firm i FSLogix najlepszych rozwiązań](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) w tym artykule opisano, jak mogą wchodzić w interakcje. Aby uzyskać więcej informacji, zobacz [na pulpitów wirtualnych za pomocą klienta synchronizacji](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Dodatkowe foldery:** FSLogix zapewnia możliwość rozszerzania profilów użytkowników w celu uwzględnienia dodatkowych folderów.

Od pozyskiwania Microsoft pracę, zastępując istniejące rozwiązania profilu użytkownika, takich jak UPD, FSLogix profilu kontenerów.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integracja z platformą Azure plików za pomocą usługi Azure Active Directory

FSLogix profilu kontenery wydajność i funkcje z zalet chmury. 24 września 2018 r. plików pakietu Microsoft Azure ogłoszono publiczną wersję zapoznawczą [obsługujące uwierzytelnianie usługi Azure Active Directory w usłudze Azure Files](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Za adresowanie kosztów administracyjnych i kosztów, usługi Azure Files przy użyciu uwierzytelniania usługi Azure Active Directory to rozwiązanie premium dla profilów użytkowników w nowej usłudze Windows pulpitu wirtualnego.

## <a name="best-practices-for-windows-virtual-desktop"></a>Najlepsze rozwiązania dotyczące Windows pulpitu wirtualnego

Pulpit wirtualny Windows zapewnia pełną kontrolę nad rozmiar, typ i liczbę maszyn wirtualnych, które są używane przez klientów. Aby uzyskać więcej informacji, zobacz [co to jest Windows wirtualnego pulpitu (wersja zapoznawcza)?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Aby zapewnić Windows pulpitu wirtualnego środowiska następujące najlepsze rozwiązania:

- Konto usługi Azure storage plików musi należeć do tego samego regionu hosta sesji maszyn wirtualnych.
- Uprawnienia platformy Azure pliki powinny odpowiadać uprawnienia opisane w [wymagania — kontenery profilu](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Każda pula hosta muszą zostać skompilowane tego samego typu i rozmiaru maszyny Wirtualnej na podstawie tego samego wzorca obrazu.
- Każda pula hosta maszyny Wirtualnej musi być w tej samej grupie zasobów, aby ułatwić zarządzanie, skalowanie i aktualizowanie.
- Aby uzyskać optymalną wydajność lokalizację centrum rozwiązanie magazynowania i FSLogix kontenera profil powinien znajdować się w tych samych danych.
- Konto magazynu zawierające obrazu wzorcowego musi należeć do tego samego regionu i subskrypcji, w której maszyny wirtualne są aprowizowane.

## <a name="next-steps"></a>Kolejne kroki

Skorzystaj z poniższych instrukcji, aby skonfigurować środowisko pulpitu wirtualnego Windows.

- Aby rozpocząć zbudowaniu rozwiązania do wirtualizacji pulpitu, zobacz [utworzyć dzierżawę w pulpitu wirtualnego Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Aby utworzyć pulę hosta w ramach dzierżawy pulpitu wirtualnego Windows, zobacz [Utwórz pulę hosta za pomocą portalu Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Aby skonfigurować w pełni zarządzane udziały plików w chmurze, zobacz [skonfigurować udział plików Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Aby skonfigurować FSLogix profilu kontenerów, zobacz [skonfiguruj udział profilu użytkownika dla puli hosta](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Aby przypisać użytkowników do puli hosta, zobacz [Zarządzanie grupami aplikacji dla Windows pulpitu wirtualnego](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Dostęp do pulpitu wirtualnego Windows zasobów za pomocą przeglądarki sieci web, zobacz [nawiązywanie połączenia z pulpitem wirtualnym Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
