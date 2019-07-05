---
title: Dostęp do panelu sterowania vFXT Avere - Azure
description: Jak łączyć się z klastrem vFXT i oparte na przeglądarce Avere Panelu sterowania skonfigurować Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439984"
---
# <a name="access-the-vfxt-cluster"></a>Dostęp do klastra vFXT

Aby zmienić ustawienia i monitorować Avere vFXT klastra, za pomocą Panelu sterowania Avere. Panel sterowania Avere jest oparty na przeglądarce interfejs graficzny do klastra.

Ponieważ klaster vFXT znajduje się w prywatnej sieci wirtualnej, należy utworzyć tunel SSH lub użyć innej metody, aby osiągnąć adres IP zarządzania klastra. Istnieją dwa podstawowe kroki: 

1. Utwórz połączenie między tą stacją roboczą a prywatna sieć wirtualna 
1. Ładowanie Panelu sterowania klastra w przeglądarce sieci web 

> [!NOTE] 
> W tym artykule założono, że zostało ustawione na publiczny adres IP na kontrolerze klastra lub w innej maszyny Wirtualnej w sieci wirtualnej klastra. W tym artykule opisano sposób użycia tej maszyny Wirtualnej na hoście dostęp do klastra. Jeśli używasz sieci VPN lub usługi ExpressRoute dla dostępu do sieci wirtualnej, przejdź do [nawiązywanie połączenia z Panelu sterowania Avere](#connect-to-the-avere-control-panel-in-a-browser).

Przed połączeniem, upewnij się, że pary kluczy publiczny/prywatny SSH użytą podczas tworzenia kontrolera klastra jest zainstalowany na komputerze lokalnym. Przeczytaj dokumentację klucze SSH [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Jeśli potrzebujesz pomocy. (Jeśli używasz hasła, zamiast klucza publicznego, użytkownik zostanie wyświetlony monit wprowadź go po nawiązaniu połączenia.) 

## <a name="create-an-ssh-tunnel"></a>Tworzenie tunelu SSH 

Możesz utworzyć tunel SSH w wierszu polecenia z opartej na systemie Linux lub systemie klienta systemu Windows 10. 

Użyj polecenia za pomocą tego formularza tunelowania SSH: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

To polecenie łączy się adres IP zarządzania klastra za pośrednictwem adresu IP kontrolera klastra.

Przykład:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

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
