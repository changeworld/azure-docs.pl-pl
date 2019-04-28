---
title: Zresetuj dostęp do maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak zarządzać użytkownicy administracyjni i zresetuj dostęp na maszynach wirtualnych systemu Linux przy użyciu rozszerzenia VMAccess i wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 71aecc1748e70e2119b1f54c21a0f705afc5d5d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800065"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Zarządzanie użytkowników administracyjnych, SSH i sprawdzanie lub napraw dyski maszyn wirtualnych systemu Linux przy użyciu rozszerzenia VMAccess przy użyciu wiersza polecenia platformy Azure
## <a name="overview"></a>Omówienie
Dysk na maszynie Wirtualnej systemu Linux są wyświetlane błędy. Jakiś sposób resetowania hasła głównego dla maszyny Wirtualnej z systemem Linux lub przypadkowo usunięty klucz prywatny SSH. Jeśli które miały miejsce w ciągu dni centrum danych, będziesz potrzebować dysku istnieje, a następnie otwórz KVM można pobrać z konsoli serwera. Rozszerzenie Azure VMAccess należy traktować jako tego przełącznika KVM, która pozwala uzyskać dostęp do konsoli zresetować dostępu do systemu Linux lub przeprowadzenia konserwacji poziomu dysku.

W tym artykule pokazano, jak Sprawdź lub naprawy dysku za pomocą rozszerzenia Azure VMAccess, zresetuj dostęp użytkowników, zarządzanie kontami użytkowników administracyjnych lub zaktualizować konfiguracji SSH w systemie Linux, gdy są one uruchamiane jako maszyny wirtualne usługi Azure Resource Manager. Jeśli musisz zarządzać maszynami wirtualnymi Classic — można wykonać instrukcji zawartych w [klasycznego dokumentacji dotyczącej maszyny Wirtualnej](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Jeśli używasz rozszerzenie VMAccess do zresetowania hasła maszyny wirtualnej po zainstalowaniu rozszerzenia logowania usługi AAD będzie konieczne ponowne uruchomienie rozszerzenie logowania usługi AAD, aby ponownie włączyć logowania usługi AAD dla maszyny.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="operating-system"></a>System operacyjny

Rozszerzenie dostępu do maszyny Wirtualnej mogą być uruchamiane względem tych dystrybucje systemu Linux:

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS i 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 i 12 |
| OpenSuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Sposoby korzystania z rozszerzenia VMAccess
Istnieją dwa sposoby użyć rozszerzenia VMAccess na maszynach wirtualnych systemu Linux:

* Za pomocą wiersza polecenia platformy Azure i wymaganych parametrów.
* [Użyj nieprzetworzone pliki w formacie JSON, które przetwarzają rozszerzenie VMAccess](#use-json-files-and-the-vmaccess-extension) i następnie działać na nich.

W poniższych przykładach używane [użytkownika maszyny wirtualnej az](/cli/azure/vm/user) poleceń. Aby wykonać te kroki, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Zaktualizuj klucz SSH
Poniższy przykład aktualizuje klucz SSH dla użytkownika `azureuser` na maszynie Wirtualnej o nazwie `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **UWAGA:** `az vm user update` Polecenie dołącza nowy tekst klucza publicznego do `~/.ssh/authorized_keys` pliku dla administratora na maszynie Wirtualnej. To nie Zamień lub usuń jakiekolwiek klucze SSH. Nie spowoduje to usunięcie poprzednich kluczy zestawu w czasie wdrażania lub kolejnych aktualizacji za pośrednictwem rozszerzenia VMAccess.

## <a name="reset-password"></a>Resetowanie hasła
Poniższy przykład powoduje zresetowanie hasła dla użytkownika `azureuser` na maszynie Wirtualnej o nazwie `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Uruchom ponownie SSH
Poniższy przykład powoduje ponowne uruchomienie demon SSH i zresetowanie konfiguracji SSH do wartości domyślnych na maszynie Wirtualnej o nazwie `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Utwórz użytkownika administracyjnego/sudo
Poniższy przykład tworzy użytkownika o nazwie `myNewUser` z **"sudo"** uprawnienia. To konto używa klucza SSH do uwierzytelniania na maszynie Wirtualnej o nazwie `myVM`. Ta metoda jest przeznaczony do pomóc Ci odzyskania dostępu do maszyny Wirtualnej, w przypadku, gdy bieżące poświadczenia są zgubienia lub zapomnienia hasła. Najlepszym rozwiązaniem jest kont z **"sudo"** uprawnienia powinny być ograniczone.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Usuwanie użytkownika
W następującym przykładzie usunięto użytkownika o nazwie `myNewUser` na maszynie Wirtualnej o nazwie `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Pliki w formacie JSON i rozszerzenie VMAccess
W poniższych przykładach używane nieprzetworzone pliki w formacie JSON. Użyj [zestaw rozszerzeń maszyn wirtualnych az](/cli/azure/vm/extension) następnie wywołać pliki w formacie JSON. Te pliki JSON mogą być również wywoływane z szablonów platformy Azure. 

### <a name="reset-user-access"></a>Resetowanie dostępu użytkownika
W przypadku utraty dostępu do katalogu głównego na maszynie Wirtualnej systemu Linux, możesz uruchomić skrypt VMAccess do zaktualizowania użytkownika hasła lub klucza SSH.

Aby zaktualizować klucz publiczny SSH użytkownika, Utwórz plik o nazwie `update_ssh_key.json` i dodaniu ustawienia w następującym formacie. Zastąp własnymi wartościami `username` i `ssh_key` parametry:

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

Aby zresetować hasło użytkownika, Utwórz plik o nazwie `reset_user_password.json` i dodaniu ustawienia w następującym formacie. Zastąp własnymi wartościami `username` i `password` parametry:

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

### <a name="restart-ssh"></a>Uruchom ponownie SSH
Aby ponownie uruchomić demona SSH i resetowanie konfiguracji SSH do wartości domyślnych, Utwórz plik o nazwie `reset_sshd.json`. Dodaj następującą zawartość:

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

Aby utworzyć użytkownika z **"sudo"** uprawnienia, które używa klucza SSH do uwierzytelniania, Utwórz plik o nazwie `create_new_user.json` i dodaniu ustawienia w następującym formacie. Zastąp własnymi wartościami `username` i `ssh_key` parametrów. Ta metoda jest przeznaczony do pomóc Ci odzyskania dostępu do maszyny Wirtualnej, w przypadku, gdy bieżące poświadczenia są zgubienia lub zapomnienia hasła. Najlepszym rozwiązaniem jest kont z **"sudo"** uprawnienia powinny być ograniczone.

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

Aby usunąć użytkownika, Utwórz plik o nazwie `delete_user.json` i dodaj następującą zawartość. Zastąp wartość dla `remove_user` parametru:

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

### <a name="check-or-repair-the-disk"></a>Sprawdź lub naprawy dysku
Użyć rozszerzenia VMAccess można również sprawdzić i naprawić dysku, który został dodany do maszyny Wirtualnej systemu Linux.

Aby sprawdzić, a następnie napraw dysku, Utwórz plik o nazwie `disk_check_repair.json` i dodaniu ustawienia w następującym formacie. Zastąp wartość dla nazwy `repair_disk`:

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
## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
