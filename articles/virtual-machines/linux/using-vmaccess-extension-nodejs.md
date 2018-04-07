---
title: Zresetuj dostęp na maszynach wirtualnych systemu Linux platformy Azure przy użyciu rozszerzenia VMAccess | Dokumentacja firmy Microsoft
description: Zresetuj dostęp na maszynach wirtualnych systemu Linux platformy Azure przy użyciu rozszerzenia VMAccess.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 5fb130fc2e448f3cbc648991ea6bebd5795bc78b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Zarządzanie użytkownikami, SSH i wyboru lub napraw dyski na maszynach wirtualnych systemu Linux platformy Azure przy użyciu rozszerzenia VMAccess z interfejsu wiersza polecenia platformy Azure w wersji 1.0
W tym artykule przedstawiono sposób rozszerzenia VMAcesss Azure umożliwia sprawdzanie lub naprawiania dysku, zresetuj dostęp użytkownika, zarządzanie kontami użytkowników, zresetuj konfigurację SSHD w systemie Linux. Wykonanie czynności opisanych w tym artykule wymaga:

* konta platformy Azure ([skorzystaj z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/));
* dostępu do [interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md) (po zalogowaniu przy użyciu `azure login`).
* Interfejs wiersza polecenia platformy Azure *musi działać w* trybie usługi Azure Resource Manager`azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands)— nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="quick-commands"></a>Szybkie polecenia
Istnieją dwa sposoby użyć rozszerzenia VMAccess na maszyny wirtualne systemu Linux:

* Przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure i wymaganych parametrów.
* Przy użyciu nieprzetworzone pliki w formacie JSON, które rozszerzenia VMAccess przetwarza i działa na.

Dla sekcji szybkich poleceń zamierzamy używać 1.0 interfejsu wiersza polecenia Azure `azure vm reset-access` metody. W poniższych przykładach poleceń Zastąp wartości, które zawierają "example" z wartościami z własnego środowiska.

## <a name="create-a-resource-group-and-linux-vm"></a>Tworzenie grupy zasobów i maszyny Wirtualnej systemu Linux
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Tworzenie Debian maszyny Wirtualnej
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Resetowanie hasła głównego
Aby zresetować hasło główne:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Resetowanie klucza SSH
Aby zresetować użytkownika innego niż główny klucz SSH:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Utwórz użytkownika
Aby utworzyć użytkownika:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Usuwanie użytkownika
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Resetuj SSHD
Aby zresetować konfigurację SSHD:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik
### <a name="vmaccess-defined"></a>Rozszerzenia VMAccess zdefiniowane:
Dysk na maszynie Wirtualnej systemu Linux są pokazywane błędy. Jakiś sposób resetowania hasła głównego dla maszyny Wirtualnej systemu Linux lub przypadkowo usunięty klucz prywatny SSH. Jeśli które miały miejsce w ciągu dni centrum danych, konieczne będzie dysk istnieje, a następnie otwórz KVM można uzyskać za pomocą konsoli serwera. Rozszerzenia Azure VMAccess można traktować jako przełącznika KVM, które umożliwia dostęp do konsoli zresetować dostępu do systemu Linux lub przeprowadź obsługę poziomu dysku.

Aby uzyskać szczegółowe wskazówki zamierzamy długi formularz VMAccess, który używa nieprzetworzone pliki w formacie JSON.  Te pliki JSON rozszerzenia VMAccess również może być wywołana z szablonów platformy Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Aby sprawdzić lub naprawy dysku maszyny wirtualnej systemu Linux przy użyciu rozszerzenia VMAccess
Za pomocą rozszerzenia VMAccess można wykonać fsck Uruchom na dysku w obszarze maszyny Wirtualnej systemu Linux.  Można także zrobić wyboru dysku i naprawy dysku przy użyciu rozszerzenia VMAccess.

Sprawdź i napraw dysk, użyj tego skryptu rozszerzenia VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Wykonanie skryptu VMAccess:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Użyć rozszerzenia VMAccess do zresetowania dostępu użytkownika do systemu Linux
W przypadku utraty dostępu do katalogu głównego na maszynie Wirtualnej systemu Linux, możesz uruchomić skrypt rozszerzenia VMAccess do zresetowania hasła głównego.

Aby zresetować hasła głównego, należy użyć tego skryptu VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Wykonanie skryptu VMAccess:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Aby zresetować użytkownika innego niż główny klucz SSH, należy użyć tego rozszerzenia VMAccess skryptu:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Wykonanie skryptu VMAccess:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Użyć rozszerzenia VMAccess do zarządzania kontami użytkowników w systemie Linux
Rozszerzenia VMAccess jest skrypt w języku Python, który może służyć do zarządzania użytkownikami w Twojej maszyny Wirtualnej systemu Linux bez logowania i przy użyciu sudo lub konta głównego.

Aby utworzyć użytkownika, użyj tego skryptu VMAccess:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Wykonanie skryptu VMAccess:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Aby usunąć użytkownika, należy użyć tego rozszerzenia VMAccess skryptu:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Wykonanie skryptu VMAccess:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Użyć rozszerzenia VMAccess do zresetowania konfiguracji SSHD
Jeśli wprowadzania zmian w konfiguracji SSHD maszyn wirtualnych systemu Linux i zamknąć połączenie SSH przed zweryfikowaniem zmiany, użytkownik może uniemożliwić SSH'ing ponownie.  Rozszerzenia VMAccess może służyć do zresetowania konfiguracji SSHD do znanej dobrej konfiguracji, bez konieczności logowania się za pomocą protokołu SSH.

Aby zresetować konfigurację SSHD użyć tego skryptu VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Wykonanie skryptu VMAccess:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Kolejne kroki
Aktualizowanie systemu Linux przy użyciu rozszerzenia VMAccess Azure jest jedną metodę, aby wprowadzić zmiany w uruchomionej maszyny Wirtualnej systemu Linux.  Narzędzia, takie jak chmury init i szablony usługi Azure umożliwia również zmodyfikować maszyny Wirtualnej systemu Linux na rozruchu.

[Temat funkcji i rozszerzeń maszyny wirtualnej](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Tworzenie szablonów usługi Azure Resource Manager z rozszerzeniami maszyny Wirtualnej systemu Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Dostosowywanie maszyny Wirtualnej systemu Linux podczas tworzenia za pomocą init chmury](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

