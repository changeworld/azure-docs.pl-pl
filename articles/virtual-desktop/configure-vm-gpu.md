---
title: Konfigurowanie procesora GPU dla Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: Jak włączyć accelerated procesora GPU renderowanie kodowania i w wersji zapoznawczej pulpitu wirtualnego Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: b6a4811f685803ecdc079a690d550618c071c4a6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620197"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Konfigurowanie GPU jednostkę GPU przyspieszanie Windows wirtualnego pulpitu (wersja zapoznawcza)

Windows wirtualnego pulpitu (wersja zapoznawcza) obsługuje przyspieszoną procesora GPU renderowanie i kodowania dla aplikacji została poprawiona wydajność i skalowalność. Przyspieszanie procesora GPU to szczególnie istotne dla aplikacji intensywnie korzystających z grafiki.

Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć maszynę wirtualną platformy Azure zoptymalizowane pod kątem procesora GPU, dodaj go do puli hosta i skonfigurować go do użycia procesora GPU przyspieszenie kodowanie i renderowanie. W tym artykule przyjęto założenie, że masz już dzierżawę Windows pulpitu wirtualnego skonfigurowane.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Wybierz rozmiar maszyny wirtualnej platformy Azure zoptymalizowane pod kątem procesora GPU

Platforma Azure oferuje szereg [rozmiarów maszyn wirtualnych GPU zoptymalizowanych pod kątem](/azure/virtual-machines/windows/sizes-gpu). Dobrym wyborem dla puli hosta zależy od wielu czynników, takich jak Twoje obciążenia danej aplikacji, odpowiednią jakość środowiska użytkownika i koszty. Ogólnie rzecz biorąc większych i bardziej możliwością procesory GPU oferują lepsze środowisko użytkownika o gęstości danego użytkownika.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Utwórz pulę hosta, aprowizowanie maszyny wirtualnej i skonfigurować grupę aplikacji

Tworzenie nowej puli hosta przy użyciu wybranego rozmiaru maszyny Wirtualnej. Aby uzyskać instrukcje, zobacz [samouczka: Utwórz pulę hosta za pomocą portalu Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows wirtualnego pulpitu (wersja zapoznawcza) obsługuje przyspieszoną procesora GPU renderowanie i kodowanie w następujących systemach operacyjnych:

* Windows 10 w wersji 1511 lub nowszej
* Windows Server 2016 lub nowszy

Musisz również skonfigurować grupę aplikacji lub użyć domyślnej aplikacji klasycznej grupy (o nazwie "Grupa aplikacji pulpitu") tworzony automatycznie podczas tworzenia nowej puli hosta. Aby uzyskać instrukcje, zobacz [samouczka: Zarządzanie grupami aplikacji w wersji zapoznawczej pulpitu wirtualnego Windows](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows wirtualnego pulpitu (wersja zapoznawcza) obsługuje tylko typ grupy aplikacji "Desktop" dla hosta z włączonymi procesorami GPU pul. Grupy aplikacji typu "RemoteApp" nie są obsługiwane w przypadku pul hosta z włączonymi procesorami GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Zainstaluj sterowniki grafiki obsługiwanych na maszynie wirtualnej

Aby móc korzystać z możliwości procesora GPU maszyny wirtualne serii N platformy Azure w wersji zapoznawczej pulpitu wirtualnego Windows, należy zainstalować sterowniki grafiki firmy NVIDIA. Postępuj zgodnie z instrukcjami w artykule [sterowniki zainstalować procesor GPU NVIDIA na maszynach wirtualnych serii N z systemem Windows](/azure/virtual-machines/windows/n-series-driver-setup) sterowniki, albo ręcznie zainstalować lub za pomocą [rozszerzenia sterowników procesora GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Pamiętaj, że tylko [sterowniki NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) dystrybuowane przez platformę Azure są obsługiwane w przypadku Windows wirtualnego Desktop w wersji zapoznawczej.

Po zainstalowaniu sterownika wymagane jest ponowne uruchomienie maszyny Wirtualnej. Wykonaj kroki weryfikacji w instrukcje powyżej, aby upewnić się, czy zostały pomyślnie zainstalowane sterowniki grafiki.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurowanie renderowanie przyspieszane sprzętowo GPU aplikacji

Domyślnie aplikacje działające w konfiguracji sesji wielu pulpitów są renderowane przy użyciu procesora CPU i nie korzystać z dostępnych procesorów GPU w przypadku renderowania. Skonfiguruj zasady grupy dla hosta sesji umożliwić renderowanie przyspieszane sprzętowo procesora GPU:

1. Podłącz do pulpitu maszyny Wirtualnej przy użyciu konta z uprawnieniami administratora lokalnego.
2. Otwórz Start menu i wpisz "gpedit.msc" Aby otworzyć Edytor zasad grupy.
3. Przejdź w drzewie aby **konfiguracji komputera** > **Szablony administracyjne** > **składników Windows**  >   **Usługi pulpitu zdalnego** > **hosta sesji usług pulpitu zdalnego** > **sesji zdalnej środowiska**.
4. Wybierz zasady **używania karty grafiki domyślnej sprzętu dla wszystkich sesji usług pulpitu zdalnego** i ustawić te zasady na **włączone** umożliwiające renderowanie procesora GPU w sesji zdalnej.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Skonfiguruj kodowanie ramki accelerated procesora GPU

Pulpit zdalny koduje wszystkie grafiki, renderowane przy użyciu aplikacji i komputerów stacjonarnych (czy jest renderowany przy użyciu procesora GPU lub CPU) dla transmisji do klientów usług pulpitu zdalnego. Domyślnie pulpitu zdalnego nie korzystanie dostępne procesory GPU na potrzeby tego kodowania. Skonfiguruj zasady grupy dla hosta sesji włączyć kodowanie ramki accelerated procesora GPU. Kontynuowanie powyższych kroków:

1. Wybierz zasady **tryb priorytety koder H.264/AVC 444 graficznych dla połączeń usług pulpitu zdalnego** i ustawić te zasady na **włączone** wymusić koder H.264/AVC 444 w sesji zdalnej.
2. Wybierz zasady **skonfigurować koder H.264/AVC sprzętu kodowania dla połączeń usług pulpitu zdalnego** i ustawić te zasady na **włączone** umożliwiające sprzętu kodowanie AVC/H.264 w sesji zdalnej.

    >[!NOTE]
    >W systemie Windows Server 2016, należy ustawić opcję **Preferuj kodowanie sprzętu AVC** do **zawsze próbuj**.

3. Teraz, gdy edycji zasad grupy należy wymusić zaktualizowanie zasad grupy. Otwórz wiersz polecenia i wpisz:

    ```batch
    gpupdate.exe /force
    ```

4. Wyloguj się z sesji pulpitu zdalnego.

## <a name="verify-gpu-accelerated-app-rendering"></a>Sprawdź renderowanie przyspieszane sprzętowo GPU aplikacji

Aby zweryfikować, że aplikacje będą używać procesora GPU renderowanie, spróbuj wykonać następujące czynności:

* Użyj `nvidia-smi` narzędzie zgodnie z opisem w [zweryfikować instalację sterownika](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) pod kątem użycia procesora GPU podczas uruchamiania aplikacji.
* Na obsługiwanych wersji systemów operacyjnych można użyć Menedżera zadań pod kątem użycia procesora GPU. Wybierz procesor GPU na karcie "Performance", aby zobaczyć, czy aplikacje są przy użyciu procesora GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Sprawdź kodowanie ramki accelerated procesora GPU

Aby sprawdzić, czy pulpit zdalny używa kodowania accelerated procesora GPU:

1. Podłącz do pulpitu maszyny Wirtualnej przy użyciu klienta pulpitu wirtualnego Windows.
2. Uruchom Podgląd zdarzeń i przejdź do następującego węzła: **Dzienniki aplikacji i usług** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Operacyjne**
3. Aby ustalić, czy jest używane kodowanie accelerated procesora GPU, poszukaj zdarzeń 170 identyfikator. Jeśli widzisz "kodera sprzętu AVC włączone: 1", a następnie kodowanie procesorów GPU jest używany.
4. Aby określić, jeśli używany jest tryb AVC 444, poszukaj zdarzeń 162 identyfikator. Jeśli widzisz "AVC dostępne: Profile początkową 1: 2048", a następnie AVC 444 jest używany.

## <a name="next-steps"></a>Kolejne kroki

Te instrukcje należy dołączasz pracę przyspieszenie procesora GPU na jednej sesji w hoście maszyny Wirtualnej. Kilka dodatkowych kwestii dotyczących włączania przyspieszenie procesora GPU na większą pulę hosta:

* Należy rozważyć użycie [rozszerzenia sterowników procesora GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) można uproszczenie instalacji sterowników i aktualizacje w liczbie maszyn wirtualnych.
* Należy wziąć pod uwagę, aby uprościć konfigurację zasad grupy w liczbie maszyn wirtualnych przy użyciu zasad grupy usługi Active Directory. Aby uzyskać informacji na temat wdrażania zasad grupy w domenie usługi Active Directory, zobacz [Praca z obiektami zasad grupy](https://go.microsoft.com/fwlink/p/?LinkId=620889).
