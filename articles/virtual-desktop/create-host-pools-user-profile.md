---
title: Skonfiguruj udział profilu użytkownika dla puli hosta Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: Jak skonfigurować kontener profilu FSLogix puli hosta Windows wirtualnego Desktop w wersji zapoznawczej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: af4147de06f9fb7c856dfd93dc186f1a6e83ffff
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58628979"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Konfigurowanie udziału profilu użytkownika dla puli hostów

Usługa Windows wirtualnego pulpitu (wersja zapoznawcza) oferuje FSLogix profilu kontenery jako rozwiązanie profilu użytkownika zalecane. Nie zaleca się przy użyciu rozwiązania dysku profilu użytkownika (UPD) i zostaną wycofane w kolejnych wersjach systemu Windows pulpitu wirtualnego.

W tej sekcji opisano, jak skonfigurować udział FSLogix profilu kontener dla zestawu hostów. Ogólne dokumentację dotyczącą FSLogix, zobacz [witryny FSLogix](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Tworzenie nowej maszyny wirtualnej, który będzie pełnił rolę udziału plików

Podczas tworzenia maszyny wirtualnej, należy umieścić go w obu tej samej sieci wirtualnej jako maszyny wirtualne w puli hosta lub w sieci wirtualnej, który ma łączność do hostowania maszyn wirtualnych w puli. Można utworzyć maszynę wirtualną na wiele sposobów:

- [Utwórz maszynę wirtualną z obrazu w galerii systemu Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Utwórz maszynę wirtualną z obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po utworzeniu maszyny wirtualnej, należy je przyłączania do domeny, wykonując następujące czynności:

1. [Łączenie z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) przy użyciu poświadczeń dostarczonych podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej Uruchom **Panelu sterowania** i wybierz **systemu**.
3. Wybierz **nazwy komputera**, wybierz opcję **zmiany ustawień**, a następnie wybierz pozycję **zmiany...**
4. Wybierz **domeny** , a następnie wprowadź domeny usługi Active Directory w sieci wirtualnej.
5. Uwierzytelnianie przy użyciu konta domeny, które ma uprawnienia do przyłączania do domeny komputerów.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Przygotowywanie maszyny wirtualnej, która będzie działać jako udziału plików profilów użytkowników

Poniżej przedstawiono ogólne instrukcje o tym, jak przygotować maszynę wirtualną do działania jako udziału plików profilów użytkowników:

1. Dołącz do sesji hosta maszyn wirtualnych [grupy zabezpieczeń usługi Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Ta grupa zabezpieczeń będzie używany do uwierzytelniania maszyn wirtualnych hostów sesji z maszyną wirtualną udziału plików, właśnie utworzony.
2. [Łączenie z maszyną wirtualną w udziale plików](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na maszynie wirtualnej udziału plików, należy utworzyć folder na **dysku C** który będzie używany jako udział profilu.
4. Kliknij prawym przyciskiem myszy nowy folder, wybierz **właściwości**, wybierz opcję **udostępniania**, a następnie wybierz **Udostępnianie zaawansowane...** .
5. Wybierz **Udostępnij ten folder**, wybierz opcję **uprawnień...** , a następnie wybierz **Dodaj...** .
6. Wyszukaj grupy zabezpieczeń, do którego dodawane maszyny wirtualne hosta sesji, a następnie upewnij się, że grupa ma **Pełna kontrola**.
7. Po dodaniu do grupy zabezpieczeń, kliknij prawym przyciskiem myszy folder, wybierz **właściwości**, wybierz opcję **udostępniania**, następnie skopiować **ścieżkę sieciową** do użycia później.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [dokumentacji FSLogix](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>Konfiguruj kontener profilu FSLogix

Aby skonfigurować maszyn wirtualnych z oprogramowaniem FSLogix, wykonaj następujące czynności na każdej maszynie zarejestrowany do puli hosta:

1. [Łączenie z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) przy użyciu poświadczeń dostarczonych podczas tworzenia maszyny wirtualnej.
2. Uruchom przeglądarkę internetową i przejdź do [ten link](https://go.microsoft.com/fwlink/?linkid=2084562) do pobrania agenta FSLogix. W ramach publicznej wersji zapoznawczej Windows pulpitu wirtualnego otrzymasz klucz licencji, aby aktywować oprogramowania FSLogix. Klucz jest plik LicenseKey.txt zawarte w pliku zip FSLogix agenta.
3. Zainstaluj agenta FSLogix.
4. Przejdź do **Program Files** > **FSLogix** > **aplikacje** Aby upewnić się, jest zainstalowany agent.
5. Z start menu Uruchom **RegEdit** jako administrator. Przejdź do **komputera\\HKEY_LOCAL_MACHINE\\oprogramowania\\FSLogix\\profilów**
6. Utwórz następujące wartości:

| Name (Nazwa)                | Type               | Dane/wartość                        |
|---------------------|--------------------|-----------------------------------|
| Enabled (Włączony)             | DWORD              | 1                                 |
| VHDLocations        | Wartość ciągu wielokrotnego | "Ścieżka sieciowa dla udziału plików" |
| VolumeType          | String             | VHDX                              |
| SizeInMBs           | DWORD              | "Liczba całkowita dla rozmiaru profilu"     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
