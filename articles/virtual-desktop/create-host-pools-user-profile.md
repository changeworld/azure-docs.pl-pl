---
title: Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu udziału plików opartego na maszynach wirtualnych — Azure
description: Jak skonfigurować kontener profilu FSLogix dla puli hostów usług pulpitu wirtualnego systemu Windows przy użyciu udziału plików opartego na maszynie wirtualnej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: 0e7516fc4874e4cbc387f2f494efc6ef745d64f7
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311595"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Tworzenie kontenera profilu dla puli hostów za pomocą udziału plików

Usługa pulpitu wirtualnego systemu Windows oferuje kontenery profilu FSLogix jako zalecane rozwiązanie profilu użytkownika. Nie zalecamy korzystania z rozwiązania profilu użytkownika (UPD), które będzie przestarzałe w przyszłych wersjach pulpitu wirtualnego systemu Windows.

W tym artykule opisano sposób konfigurowania udziału kontenera FSLogix profile dla puli hostów przy użyciu udziału plików opartego na maszynach wirtualnych. Więcej dokumentacji FSLogix można znaleźć w [witrynie FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Jeśli szukasz materiału do porównania o różnych opcjach magazynu kontenera profilu FSLogix na platformie Azure, zobacz [Opcje magazynu dla kontenerów profilów FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Utwórz nową maszynę wirtualną, która będzie pełnić rolę udziału plików

Podczas tworzenia maszyny wirtualnej należy ją umieścić w tej samej sieci wirtualnej co maszyny wirtualne puli hostów lub w sieci wirtualnej, która ma łączność z maszynami wirtualnymi puli hostów. Maszynę wirtualną można utworzyć na wiele sposobów:

- [Tworzenie maszyny wirtualnej na podstawie obrazu z galerii platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Tworzenie maszyny wirtualnej na podstawie obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po utworzeniu maszyny wirtualnej Dołącz ją do domeny, wykonując następujące czynności:

1. [Połącz się z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej Uruchom **Panel sterowania** , a następnie wybierz pozycję **system**.
3. Wybierz pozycję **Nazwa komputera**, wybierz pozycję **Zmień ustawienia**, a następnie wybierz pozycję **Zmień.**
4. Wybierz pozycję **domena** , a następnie wprowadź domenę Active Directory w sieci wirtualnej.
5. Uwierzytelnianie przy użyciu konta domeny, które ma uprawnienia do komputerów przyłączania do domeny.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Przygotuj maszynę wirtualną do działania jako udział plików dla profilów użytkowników

Poniżej znajdują się ogólne instrukcje dotyczące przygotowania maszyny wirtualnej do działania jako udział plików dla profilów użytkowników:

1. Dodaj pulpit wirtualny systemu Windows Active Directory użytkowników do [Active Directory grupy zabezpieczeń](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ta grupa zabezpieczeń zostanie użyta do uwierzytelnienia użytkowników pulpitu wirtualnego systemu Windows na utworzoną maszynę wirtualną udziału plików.
2. [Nawiąż połączenie z maszyną wirtualną udziału plików](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na maszynie wirtualnej udziału plików Utwórz folder na **dysku C** , który będzie używany jako udział profilu.
4. Kliknij prawym przyciskiem myszy nowy folder, wybierz pozycję **Właściwości**, wybierz pozycję **udostępnianie**, a następnie wybierz pozycję **Udostępnianie zaawansowane...** .
5. Wybierz pozycję **Udostępnij ten folder**, wybierz pozycję **uprawnienia...** , a następnie wybierz pozycję **Dodaj.**
6. Wyszukaj grupę zabezpieczeń, do której dodano użytkowników pulpitu wirtualnego systemu Windows, a następnie upewnij się, że grupa ma **pełną kontrolę**.
7. Po dodaniu grupy zabezpieczeń kliknij prawym przyciskiem myszy folder, wybierz polecenie **Właściwości**, wybierz pozycję **udostępnianie**, a następnie skopiuj **ścieżkę sieciową** do użycia w przyszłości.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [dokumentację usługi FSLogix](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurowanie kontenera profilu FSLogix

Aby skonfigurować maszyny wirtualne przy użyciu oprogramowania FSLogix, wykonaj następujące czynności na każdym komputerze zarejestrowanym w puli hostów:

1. [Połącz się z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) przy użyciu poświadczeń podanych podczas tworzenia maszyny wirtualnej.
2. Uruchom przeglądarkę internetową i przejdź do [tego linku](https://go.microsoft.com/fwlink/?linkid=2084562) , aby pobrać agenta FSLogix.
3. Przejdź do jednej z \\ @ no__t-1Win32 @ no__t-2Release lub \\ @ no__t-4X64 @ no__t-5Release w pliku zip i uruchom **FSLogixAppsSetup** , aby zainstalować agenta FSLogix.  Aby dowiedzieć się więcej na temat sposobu instalowania programu FSLogix, zobacz [pobieranie i Instalowanie FSLogix](https://docs.microsoft.com/fslogix/install-ht).
4. Przejdź do **pliku Program Files** > **FSLogix** > **aplikacje** , aby potwierdzić, że Agent jest zainstalowany.
5. Z menu Start Uruchom polecenie **regedit** jako administrator. Przejdź do **komputera @ no__t-1HKEY_LOCAL_MACHINE @ no__t-2software @ no__t-3FSLogix**.
6. Utwórz klucz o nazwie **Profile**.
7. Utwórz następujące wartości dla klucza profile:

| Nazwa                | Typ               | Dane/wartość                        |
|---------------------|--------------------|-----------------------------------|
| Enabled (Włączony)             | Ostatnie              | 1                                 |
| VHDLocations        | Wartość ciągu wielociągowego | "Ścieżka sieciowa udziału plików"     |

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389 dla użytkowników w celu uzyskania dostępu do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu just in Time do maszyny wirtualnej](../security-center/security-center-just-in-time.md).
