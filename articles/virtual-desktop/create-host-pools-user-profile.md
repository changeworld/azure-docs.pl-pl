---
title: Udział kontenera profilu FSLogix dla pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak skonfigurować kontener profilu FSLogix dla puli hostów pulpitu wirtualnego systemu Windows przy użyciu udziału plików opartego na maszynie wirtualnej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250922"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Tworzenie kontenera profilu dla puli hostów za pomocą udziału plików

Usługa Pulpitu wirtualnego systemu Windows oferuje kontenery profilu FSLogix jako zalecane rozwiązanie profilu użytkownika. Nie zaleca się używania rozwiązania UPD (User Profile Disk), które zostanie przestarzałe w przyszłych wersjach pulpitu wirtualnego systemu Windows.

W tym artykule dowiesz się, jak skonfigurować udział kontenera profilu FSLogix dla puli hostów przy użyciu udziału plików opartego na maszynie wirtualnej. Więcej dokumentacji FSLogix można znaleźć na [stronie FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Jeśli szukasz materiałów porównawczych dotyczących różnych opcji magazynu kontenerów profilu FSLogix na platformie Azure, zobacz [Opcje usługi Storage dla kontenerów profilu FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Tworzenie nowej maszyny wirtualnej, która będzie działać jako udział plików

Podczas tworzenia maszyny wirtualnej należy umieścić ją w tej samej sieci wirtualnej co maszyny wirtualne puli hostów lub w sieci wirtualnej, która ma łączność z maszynami wirtualnymi puli hostów. Maszynę wirtualną można utworzyć na wiele sposobów:

- [Tworzenie maszyny wirtualnej na podstawie obrazu usługi Azure Gallery](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Tworzenie maszyny wirtualnej na podstawie obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po utworzeniu maszyny wirtualnej dołącz ją do domeny, wykonując następujące czynności:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) z poświadczeniami podanymi podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej uruchom **Panel sterowania** i wybierz **pozycję System**.
3. Wybierz **pozycję Nazwa komputera**, wybierz pozycję Zmień **ustawienia**, a następnie wybierz pozycję **Zmień...**
4. Wybierz **pozycję Domena,** a następnie wprowadź domenę usługi Active Directory w sieci wirtualnej.
5. Uwierzytelnij się przy użyciu konta domeny, które ma uprawnienia do komputerów dołączanych do domeny.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Przygotowanie maszyny wirtualnej do działania jako udział plików dla profilów użytkowników

Poniżej przedstawiono ogólne instrukcje dotyczące przygotowania maszyny wirtualnej do działania jako udział plików dla profilów użytkowników:

1. Dodawanie użytkowników usługi Active Directory pulpitu wirtualnego systemu Windows do [grupy zabezpieczeń usługi Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups/). Ta grupa zabezpieczeń będzie używana do uwierzytelniania użytkowników pulpitu wirtualnego systemu Windows na utworzonej właśnie maszynie wirtualnej udziału plików.
2. [Połącz się z maszyną wirtualną udziału plików](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na maszynie wirtualnej udziału plików utwórz folder na **dysku C,** który będzie używany jako udział profilu.
4. Kliknij prawym przyciskiem myszy nowy folder, wybierz polecenie **Właściwości**, wybierz pozycję **Udostępnianie**, a następnie wybierz pozycję **Udostępnianie zaawansowane...**.
5. Wybierz **pozycję Udostępnij ten folder**, wybierz pozycję **Uprawnienia...**, a następnie wybierz pozycję **Dodaj...**.
6. Wyszukaj grupę zabezpieczeń, do której dodano użytkowników pulpitu wirtualnego systemu Windows, a następnie upewnij się, że grupa ma **pełną kontrolę**.
7. Po dodaniu grupy zabezpieczeń kliknij prawym przyciskiem myszy folder, wybierz polecenie **Właściwości**, wybierz pozycję **Udostępnianie**, a następnie skopiuj **ścieżkę sieciową,** która będzie używana później.

Aby uzyskać więcej informacji na temat uprawnień, zobacz [dokumentację FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurowanie kontenera profilu FSLogix

Aby skonfigurować maszyny wirtualne za pomocą oprogramowania FSLogix, wykonaj następujące czynności na każdym komputerze zarejestrowanym w puli hostów:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) z poświadczeniami podanymi podczas tworzenia maszyny wirtualnej.
2. Uruchom przeglądarkę internetową i przejdź do [tego linku,](https://go.microsoft.com/fwlink/?linkid=2084562) aby pobrać agenta FSLogix.
3. Przejdź do \\ \\\\wersji Win32 \\ \\release\\lub X64 Release w pliku zip i uruchom **FSLogixAppsSetup,** aby zainstalować agenta FSLogix.  Aby dowiedzieć się więcej o instalacji FSLogix, zobacz [Pobieranie i instalowanie programu FSLogix](/fslogix/install-ht/).
4. Przejdź do **aplikacji Pliki programu** > **FSLogix,** > **Apps** aby potwierdzić zainstalowany agent.
5. Z menu Start uruchom **RegEdit** jako administrator. Przejdź do **aplikacji\\Komputer\\\\HKEY_LOCAL_MACHINE FSLogix**.
6. Utwórz klucz o nazwie **Profile**.
7. Utwórz następujące wartości dla klucza Profile:

| Nazwa                | Typ               | Dane/wartość                        |
|---------------------|--------------------|-----------------------------------|
| Enabled (Włączony)             | DWORD              | 1                                 |
| VHDLokacje        | Wartość wielostrunowa | "Ścieżka sieciowa dla udziału plików"     |

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389, aby użytkownicy mogli uzyskiwać dostęp do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu do maszyn wirtualnych just-in-time](../security-center/security-center-just-in-time.md).
