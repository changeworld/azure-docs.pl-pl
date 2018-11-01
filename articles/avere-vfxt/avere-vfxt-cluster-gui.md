---
title: Dostęp do panelu sterowania vFXT Avere - Azure
description: Jak łączyć się z klastrem vFXT i oparte na przeglądarce Avere Panelu sterowania skonfigurować Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c48f0d8f7ad34db585f4deae566641b6453357e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634202"
---
# <a name="access-the-vfxt-cluster"></a>Dostęp do klastra vFXT

Aby zmienić ustawienia i monitorować Avere vFXT klastra, za pomocą Panelu sterowania Avere. Panel sterowania Avere jest oparty na przeglądarce interfejs graficzny do klastra.

Ponieważ klaster vFXT znajduje się w prywatnej sieci wirtualnej, należy utworzyć tunel SSH lub użyć innej metody, aby osiągnąć adres IP zarządzania klastra. Istnieją dwa podstawowe kroki: 

1. Utwórz połączenie między tą stacją roboczą a prywatna sieć wirtualna 
1. Ładowanie Panelu sterowania w przeglądarce sieci web przy użyciu adresu IP zarządzania klastra 

> [!NOTE] 
> W tym artykule założono, że zostało ustawione na publiczny adres IP na kontrolerze klastra lub w innej maszyny Wirtualnej w sieci wirtualnej klastra. Jeśli używasz sieci VPN lub usługi ExpressRoute dla dostępu do sieci wirtualnej, przejdź do [nawiązywanie połączenia z Panelu sterowania Avere](#connect-to-the-avere-control-panel-in-a-browser).

Przed połączeniem, upewnij się, że pary kluczy publiczny/prywatny SSH użytą podczas tworzenia kontrolera klastra jest zainstalowany na komputerze lokalnym. Przeczytaj dokumentację klucze SSH [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) lub [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) Jeśli potrzebujesz pomocy.  

## <a name="access-with-a-linux-host"></a>Dostęp za pomocą hoście z systemem Linux

w tym formularzu: 

SSH -L *local_port*:*cluster_mgmt_ip*: 443 *controller_username*@*controller_public_IP* 

To polecenie łączy się adres IP zarządzania klastra za pośrednictwem adresu IP kontrolera klastra.

Przykład:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Uwierzytelnianie odbywa się automatyczne, jeśli publiczny klucz SSH został użyty do utworzenia klastra i dopasowany klucz jest zainstalowany w systemie klienta.


## <a name="access-with-a-windows-host"></a>Dostęp z hostem Windows

Jeśli przy użyciu programu PuTTY, wypełnij **hostname** pole nazwy użytkownika kontrolera klastra i jego adres IP: *your_username*@*controller_public_IP*.

Przykład: ``azureuser@203.0.113.51``

W **konfiguracji** panelu:

1. Rozwiń **połączenia** > **SSH** po lewej stronie. 
1. Kliknij przycisk **tuneli**. 
1. Wprowadź port źródłowy, na przykład 8443. 
1. Dla miejsca docelowego wprowadź adres IP zarządzania klastrem vFXT i portu 443. 
   Przykład: ``203.0.113.51:443``
1. Kliknij pozycję **Add** (Dodaj).
1. Kliknij przycisk **Open** (Otwórz).

![Zrzut ekranu programu Putty aplikacji przedstawiający miejsce kliknij, aby dodać tunelu](media/avere-vfxt-ptty-numbered.png)

Uwierzytelnianie odbywa się automatyczne, jeśli publiczny klucz SSH został użyty do utworzenia klastra i dopasowany klucz jest zainstalowany w systemie klienta.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Łączenie do panelu sterowania Avere w przeglądarce

Ten krok używa przeglądarki sieci web do łączenia z uruchomionymi w klastrze vFXT narzędzie do konfiguracji.

Otwórz przeglądarkę internetową i przejdź do https://127.0.0.1:8443. 

W zależności od przeglądarki, konieczne może być kliknij **zaawansowane** i sprawdź, czy jest bezpieczne przejść do strony.

Wprowadź nazwę użytkownika `admin` i hasło podane podczas tworzenia klastra.

![Zrzut ekranu przedstawiający Avere Zaloguj się na stronie wypełniane przy użyciu nazwy użytkownika administratora i hasła](media/avere-vfxt-gui-login.png)

Kliknij przycisk **logowania** lub naciśnij klawisz enter na klawiaturze.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy uzyskujesz dostęp do klastra, należy włączyć [obsługuje](avere-vfxt-enable-support.md).
