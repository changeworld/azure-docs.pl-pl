---
title: Dostęp do panelu sterowania Avere vFXT — Azure
description: Jak połączyć się z klastrem vFXT i opartym na przeglądarce Panelem sterowania Avere, aby skonfigurować avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416143"
---
# <a name="access-the-vfxt-cluster"></a>Uzyskiwanie dostępu do klastra vFXT

Aby dostosować ustawienia klastra i monitorować klaster, użyj Panelu sterowania Avere. Avere Control Panel to oparty na przeglądarce interfejs graficzny do klastra.

Ponieważ klaster vFXT znajduje się wewnątrz prywatnej sieci wirtualnej, należy utworzyć tunel SSH lub użyć innej metody, aby uzyskać dostęp do zarządzania adresem IP klastra.

Istnieją dwa podstawowe kroki:

1. Tworzenie połączenia między stacją roboczą a prywatną siecią wirtualną
1. Ładowanie panelu sterowania klastra w przeglądarce internetowej

> [!NOTE]
> W tym artykule przyjęto założenie, że publiczny adres IP został ustawiony na kontrolerze klastra lub na innej maszynie wirtualnej w sieci wirtualnej klastra. W tym artykule opisano, jak używać tej maszyny Wirtualnej jako hosta, aby uzyskać dostęp do klastra. Jeśli korzystasz z sieci VPN lub usługi ExpressRoute do dostępu do sieci wirtualnej, przejdź do [opcji Połącz z Panelem sterowania Avere](#connect-to-the-avere-control-panel-in-a-browser).

Przed nawiązaniem połączenia upewnij się, że para kluczy publicznych/prywatnych SSH używana podczas tworzenia kontrolera klastra jest zainstalowana na komputerze lokalnym. Przeczytaj dokumentację kluczy SSH dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) lub [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) jeśli potrzebujesz pomocy. Jeśli użyto hasła zamiast klucza publicznego, zostanie wyświetlony monit o jego wprowadzenie podczas nawiązywania połączenia.

## <a name="create-an-ssh-tunnel"></a>Tworzenie tunelu SSH

Tunel SSH można utworzyć z wiersza polecenia systemu klienckiego opartego na systemie Linux lub Windows 10.

Użyj polecenia tunelowania SSH w tym formularzu:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

To polecenie łączy się z adresem IP zarządzania klastrem za pośrednictwem adresu IP kontrolera klastra.

Przykład:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Uwierzytelnianie jest automatyczne, jeśli do utworzenia klastra użyto klucza publicznego SSH, a pasujący klucz jest zainstalowany w systemie klienckim. Jeśli użyto hasła, system wyświetli monit o jego wprowadzenie.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Łączenie się z Panelem sterowania Avere w przeglądarce

Ten krok używa przeglądarki internetowej do łączenia się z narzędziem konfiguracyjnym w klastrze vFXT.

* Aby uzyskać połączenie tunelu SSH, otwórz `https://127.0.0.1:8443`przeglądarkę internetową i przejdź do .

  Podczas tworzenia tunelu z adresem IP klastra nawiązano połączenie, więc wystarczy użyć adresu IP hosta lokalnego w przeglądarce. Jeśli użyto portu lokalnego innego niż 8443, użyj numeru portu.

* Jeśli dotarcia do klastra jest używany vpn lub usługa ExpressRoute, przejdź do adresu IP zarządzania klastrem w przeglądarce. Przykład: ``https://203.0.113.51``

W zależności od przeglądarki może być konieczne **kliknięcie przycisku Zaawansowane** i sprawdzenie, czy przejście do strony jest bezpieczne.

Wprowadź nazwę `admin` użytkownika i hasło administracyjne podane podczas tworzenia klastra.

![Zrzut ekranu przedstawiający stronę logowania Avere wypełnioną nazwą użytkownika "admin" i hasłem](media/avere-vfxt-gui-login.png)

Kliknij **pozycję Zaloguj się** lub naciśnij klawisz Enter na klawiaturze.

## <a name="next-steps"></a>Następne kroki

Po zalogowaniu się do panelu sterowania klastra włącz [obsługę](avere-vfxt-enable-support.md).
