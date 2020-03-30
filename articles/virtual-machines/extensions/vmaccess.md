---
title: Resetowanie dostępu do maszyny Wirtualnej systemu Azure z systemem Linux
description: Jak zarządzać użytkownikami administracyjnymi i resetować dostęp na maszynach wirtualnych z systemem Linux przy użyciu rozszerzenia VMAccess i interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250363"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Zarządzanie użytkownikami administracyjnymi, SSH i sprawdzanie lub naprawianie dysków na maszynach wirtualnych z systemem Linux przy użyciu rozszerzenia VMAccess z interfejsem wiersza polecenia platformy Azure
## <a name="overview"></a>Omówienie
Na dysku maszyny Wirtualnej z systemem Linux są wyświetlane błędy. W jakiś sposób zresetowałeś hasło główne maszyny Wirtualnej systemu Linux lub przypadkowo usunąłeś klucz prywatny SSH. Jeśli tak się stało w dniach centrum danych, trzeba będzie tam jechać, a następnie otworzyć KVM, aby uzyskać w konsoli serwera. Rozszerzenie usługi Azure VMAccess można było potraktować jako przełącznik KVM, który umożliwia dostęp do konsoli w celu zresetowania dostępu do systemu Linux lub przeprowadzenia konserwacji na poziomie dysku.

W tym artykule pokazano, jak używać rozszerzenia usługi Azure VMAccess, aby sprawdzić lub naprawić dysk, zresetować dostęp użytkownika, zarządzać kontami użytkowników administracyjnych lub zaktualizować konfigurację SSH w systemie Linux, gdy są one uruchomione jako maszyny wirtualne usługi Azure Resource Manager. Jeśli chcesz zarządzać klasycznymi maszynami wirtualnymi - możesz postępować zgodnie z instrukcjami zawartymi w [klasycznej dokumentacji maszyny wirtualnej](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Jeśli używasz rozszerzenia VMAccess, aby zresetować hasło maszyny wirtualnej po zainstalowaniu rozszerzenia logowania AAD, musisz ponownie uruchomić rozszerzenie logowania AAD, aby ponownie włączyć logowanie AAD dla komputera.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="operating-system"></a>System operacyjny

Rozszerzenie VM Access można uruchomić w następujących dystrybucjach systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS i 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 i 12 |
| Opensuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Sposoby korzystania z rozszerzenia VMAccess
Istnieją dwa sposoby, które można użyć VMAccess Rozszerzenie na maszynach wirtualnych z systemem Linux:

* Użyj interfejsu wiersza polecenia platformy Azure i wymaganych parametrów.
* [Użyj nieprzetworzonych plików JSON, które proces rozszerzenia VMAccess,](#use-json-files-and-the-vmaccess-extension) a następnie działać na.

W poniższych przykładach użyto poleceń [użytkownika az vm.](/cli/azure/vm/user) Aby wykonać te kroki, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

## <a name="update-ssh-key"></a>Update SSH key (Aktualizowanie klucza SSH)
Poniższy przykład aktualizuje klucz SSH dla `azureuser` użytkownika `myVM`na maszynie wirtualnej o nazwie:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **UWAGA:** Polecenie `az vm user update` dołącza nowy tekst klucza publicznego do `~/.ssh/authorized_keys` pliku dla użytkownika administratora na maszynie Wirtualnej. Nie zastępuje to ani nie usuwa istniejących kluczy SSH. Nie spowoduje to usunięcia wcześniejszych kluczy ustawionych w czasie wdrażania lub kolejnych aktualizacji za pośrednictwem rozszerzenia VMAccess.

## <a name="reset-password"></a>Resetowanie hasła
Poniższy przykład resetuje hasło `azureuser` dla użytkownika na `myVM`maszynie wirtualnej o nazwie:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Ponowne uruchamianie protokołu SSH
Poniższy przykład powoduje ponowne uruchomienie demona SSH i resetowanie konfiguracji SSH `myVM`do wartości domyślnych na maszynie wirtualnej o nazwie:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Tworzenie użytkownika administracyjnego/sudo
Poniższy przykład tworzy `myNewUser` użytkownika o nazwie z uprawnieniami **sudo.** Konto używa klucza SSH do uwierzytelniania `myVM`na maszynie wirtualnej o nazwie . Ta metoda ma na celu pomóc odzyskać dostęp do maszyny Wirtualnej w przypadku, gdy bieżące poświadczenia są utracone lub zapomniane. Najlepszym rozwiązaniem jest ograniczenie kont z uprawnieniami **sudo.**

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Usuwanie użytkownika
Poniższy przykład usuwa użytkownika `myNewUser` o nazwie `myVM`na maszynie wirtualnej o nazwie:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Korzystanie z plików w formacie JSON i rozszerzenia VMAccess
W poniższych przykładach użyto nieprzetworzonych plików JSON. Użyj [zestawu rozszerzeń az vm,](/cli/azure/vm/extension) aby następnie wywołać pliki JSON. Te pliki JSON można również wywołać z szablonów platformy Azure. 

### <a name="reset-user-access"></a>Resetowanie dostępu użytkownika
Jeśli utracisz dostęp do katalogu głównego na maszynie Wirtualnej systemu Linux, możesz uruchomić skrypt VMAccess, aby zaktualizować klucz lub hasło SSH użytkownika.

Aby zaktualizować klucz publiczny SSH użytkownika, utwórz plik o nazwie `update_ssh_key.json` i dodaj ustawienia w następującym formacie. Zastąp własne `username` `ssh_key` wartości i parametry:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Wykonaj skrypt VMAccess za pomocą:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Aby zresetować hasło użytkownika, `reset_user_password.json` utwórz plik o nazwie i dodaj ustawienia w następującym formacie. Zastąp własne `username` `password` wartości i parametry:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Wykonaj skrypt VMAccess za pomocą:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Ponowne uruchamianie protokołu SSH
Aby ponownie uruchomić demon SSH i zresetować konfigurację SSH `reset_sshd.json`do wartości domyślnych, należy utworzyć plik o nazwie . Dodaj następującą zawartość:

```json
{
  "reset_ssh": true
}
```

Wykonaj skrypt VMAccess za pomocą:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Zarządzanie użytkownikami administracyjnymi

Aby utworzyć użytkownika z uprawnieniami **sudo,** który używa klucza SSH do uwierzytelniania, należy utworzyć plik o nazwie `create_new_user.json` i dodać ustawienia w następującym formacie. Zastąp własne `username` `ssh_key` wartości i parametry. Ta metoda ma na celu pomóc odzyskać dostęp do maszyny Wirtualnej w przypadku, gdy bieżące poświadczenia są utracone lub zapomniane. Najlepszym rozwiązaniem jest ograniczenie kont z uprawnieniami **sudo.**

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Wykonaj skrypt VMAccess za pomocą:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Aby usunąć użytkownika, utwórz `delete_user.json` plik o nazwie i dodaj następującą zawartość. Zastąp własną `remove_user` wartość parametru:

```json
{
  "remove_user":"myNewUser"
}
```

Wykonaj skrypt VMAccess za pomocą:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Sprawdzanie lub naprawianie dysku
Za pomocą VMAccess można również sprawdzić i naprawić dysk, który został dodany do maszyny Wirtualnej systemu Linux.

Aby sprawdzić, a następnie naprawić dysk, utwórz plik o nazwie `disk_check_repair.json` i dodaj ustawienia w następującym formacie. Zastąp własną wartość `repair_disk`nazwą:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Wykonaj skrypt VMAccess za pomocą:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
