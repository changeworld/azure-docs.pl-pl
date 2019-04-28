---
title: Dostęp do panelu sterowania vFXT Avere - Azure
description: Jak łączyć się z klastrem vFXT i oparte na przeglądarce Avere Panelu sterowania skonfigurować Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f989f4d103efecf2b6e206287dd8b7b300a1796d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794299"
---
# <a name="access-the-vfxt-cluster"></a>Dostęp do klastra vFXT

Aby zmienić ustawienia i monitorować Avere vFXT klastra, za pomocą Panelu sterowania Avere. Panel sterowania Avere jest oparty na przeglądarce interfejs graficzny do klastra.

Ponieważ klaster vFXT znajduje się w prywatnej sieci wirtualnej, należy utworzyć tunel SSH lub użyć innej metody, aby osiągnąć adres IP zarządzania klastra. Istnieją dwa podstawowe kroki: 

1. Utwórz połączenie między tą stacją roboczą a prywatna sieć wirtualna 
1. Ładowanie Panelu sterowania klastra w przeglądarce sieci web 

> [!NOTE] 
> W tym artykule założono, że zostało ustawione na publiczny adres IP na kontrolerze klastra lub w innej maszyny Wirtualnej w sieci wirtualnej klastra. W tym artykule opisano sposób użycia tej maszyny Wirtualnej na hoście dostęp do klastra. Jeśli używasz sieci VPN lub usługi ExpressRoute dla dostępu do sieci wirtualnej, przejdź do [nawiązywanie połączenia z Panelu sterowania Avere](#connect-to-the-avere-control-panel-in-a-browser).

Przed połączeniem, upewnij się, że pary kluczy publiczny/prywatny SSH użytą podczas tworzenia kontrolera klastra jest zainstalowany na komputerze lokalnym. Przeczytaj dokumentację klucze SSH [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Jeśli potrzebujesz pomocy. (Jeśli używasz hasła, zamiast klucza publicznego, użytkownik zostanie wyświetlony monit wprowadź go po nawiązaniu połączenia.) 

## <a name="ssh-tunnel-with-a-linux-host"></a>Tunel SSH z hostem systemu Linux

Jeśli używasz klienta opartego na systemie Linux, należy użyć polecenia w tym formularzu tunelowania SSH: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

To polecenie łączy się adres IP zarządzania klastra za pośrednictwem adresu IP kontrolera klastra.

Przykład:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Uwierzytelnianie odbywa się automatyczne, jeśli publiczny klucz SSH został użyty do utworzenia klastra i dopasowany klucz jest zainstalowany w systemie klienta. Jeśli użyto hasła, system wyświetli monit o jego wprowadzenie.

## <a name="ssh-tunnel-with-a-windows-host"></a>Tunel SSH z hostem Windows

W tym przykładzie użyto wspólnej oparte na Windows narzędzie terminal, program PuTTY.

Wypełnij PuTTY **hostname** pole nazwy użytkownika kontrolera klastra i jego adres IP: *your_username*\@*controller_public_IP*.

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

Uwierzytelnianie odbywa się automatyczne, jeśli publiczny klucz SSH został użyty do utworzenia klastra i dopasowany klucz jest zainstalowany w systemie klienta. Jeśli użyto hasła, system wyświetli monit o jego wprowadzenie.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Łączenie do panelu sterowania Avere w przeglądarce

Ten krok używa przeglądarki sieci web do łączenia z uruchomionymi w klastrze vFXT narzędzie do konfiguracji.

* Dla połączenia tunelu SSH, otwórz przeglądarkę internetową i przejdź do `https://127.0.0.1:8443`. 

  Masz połączenie z klastrem adresu IP podczas tworzenia tunelu, więc wystarczy użyć adresu IP hosta lokalnego w przeglądarce. Jeśli używany jest port lokalny inny niż 8443, należy użyć numeru portu.

* Jeśli dotrzeć do klastra przy użyciu sieci VPN lub usługi ExpressRoute, przejdź na adres IP klastra zarządzania w przeglądarce. Przykład: ``https://203.0.113.51``

W zależności od przeglądarki, konieczne może być kliknij **zaawansowane** i sprawdź, czy jest bezpieczne przejść do strony.

Wprowadź nazwę użytkownika `admin` i hasło administracyjne podaną podczas tworzenia klastra.

![Zrzut ekranu przedstawiający Avere Zaloguj się na stronie wypełniane przy użyciu nazwy użytkownika administratora i hasła](media/avere-vfxt-gui-login.png)

Kliknij przycisk **logowania** lub naciśnij klawisz enter na klawiaturze.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy uzyskujesz dostęp do klastra, należy włączyć [obsługuje](avere-vfxt-enable-support.md).
