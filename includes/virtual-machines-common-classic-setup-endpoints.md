---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cfe675ca269a69c7c2bfa67638acd0afbcd1c8ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371290"
---
Każdy punkt końcowy ma *port publiczny* i *port prywatny*:

* Port publiczny jest używany przez moduł równoważenia obciążenia Azure do nasłuchiwania ruch przychodzący do maszyny wirtualnej z Internetu.
* Port prywatny jest używane przez maszynę wirtualną do nasłuchiwania dla ruchu przychodzącego, zwykle kierowanych do aplikacji lub usługi działające na maszynie wirtualnej.

Wartości domyślne dla protokołu IP i portów TCP lub UDP sieci dobrze znanych protokołów są dostępne podczas tworzenia punktów końcowych z portalu Azure. Niestandardowe punkty końcowe należy określić poprawną protokołu IP (TCP lub UDP) i porty publiczne i prywatne. Aby losowo dystrybucji przychodzącego ruchu między wieloma maszynami wirtualnymi, należy utworzyć zestaw z równoważeniem obciążenia, składające się z wielu punktów końcowych.

Po utworzeniu punktu końcowego, można użyć listy kontroli dostępu (ACL) można zdefiniować reguły akceptowanie lub odrzucanie ruch przychodzący do portu publicznego punktu końcowego na podstawie jego adresu IP źródłowego. Jednak jeśli maszyna wirtualna znajduje się w sieci wirtualnej platformy Azure, należy zamiast tego użyć grup zabezpieczeń sieci. Aby uzyskać więcej informacji, zobacz [dotyczące grup zabezpieczeń sieci](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Konfiguracja zapory dla maszyn wirtualnych platformy Azure odbywa się automatycznie dla portów skojarzonych z punktami końcowymi połączenia zdalnego, które Azure konfiguruje się automatycznie. Porty określone dla innych punktów końcowych konfiguracja nie odbywa się automatycznie w zaporze maszyny wirtualnej. Po utworzeniu punktu końcowego maszyny wirtualnej należy upewnij się, że zapory maszyny wirtualnej również zezwala na ruch protokołu i port prywatny odpowiadający konfiguracji punktu końcowego. Aby skonfigurować zaporę, skorzystaj z dokumentacji lub pomocy online dla systemu operacyjnego działającego na maszynie wirtualnej.
>
>

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **maszyn wirtualnych**, a następnie kliknij nazwę maszyny wirtualnej, który chcesz skonfigurować.
3. Kliknij przycisk **punkty końcowe** w **ustawienia** grupy. **Punkty końcowe** stronie są wyświetlane wszystkie bieżące punkty końcowe dla maszyny wirtualnej. (W tym przykładzie jest Maszynę wirtualną systemu Windows. Linux VM domyślnie wyświetli punkt końcowy SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Punkty końcowe](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. Na pasku poleceń powyżej wpisów punktu końcowego kliknij **Dodaj**.
5. Na **dodać punkt końcowy** wpisz nazwę punktu końcowego w **nazwa**.
6. W **protokołu**, albo wybierz **TCP** lub **UDP**.
7. W **Port publiczny**, wpisz numer portu dla ruchu przychodzącego z Internetu. W **Port prywatny**, wpisz numer portu, na którym nasłuchuje maszyny wirtualnej. Numery portów mogą być różne. Upewnij się, że zapora na maszynie wirtualnej została skonfigurowana zezwalająca na ruch odpowiadającego port prywatny i protokół (w kroku 6).
10. Kliknij przycisk **OK**.

Nowy punkt końcowy zostanie wymieniony na **punkty końcowe** strony.

![Pomyślne utworzenie punktu końcowego](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Zarządzanie listy ACL punktu końcowego
Aby określić zestaw komputerów, które mogą przesyłać, listy ACL punktu końcowego można ograniczyć ruch ustalane na podstawie źródłowego adresu IP. Wykonaj następujące kroki, aby dodać, zmodyfikować lub usunąć listy ACL punktu końcowego.

> [!NOTE]
> Jeśli punkt końcowy jest częścią zestawu o zrównoważonym obciążeniu, wszystkie zmiany wprowadzone do listy ACL punktu końcowego są stosowane do wszystkich punktów końcowych w zestawie.
>
>

Jeśli maszyna wirtualna znajduje się w sieci wirtualnej platformy Azure, firma Microsoft zaleca sieciowe grupy zabezpieczeń zamiast listy kontroli dostępu. Aby uzyskać więcej informacji, zobacz [dotyczące grup zabezpieczeń sieci](../articles/virtual-network/security-overview.md).

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do portalu Azure.
2. Kliknij przycisk **maszyn wirtualnych**, a następnie kliknij nazwę maszyny wirtualnej, który chcesz skonfigurować.
3. Kliknij przycisk **Punkty końcowe**. Z listy wybierz odpowiednie punktu końcowego. Lista ACL jest w dolnej części strony.

   ![Określ szczegóły list kontroli dostępu](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Aby dodać, usunąć, lub edytowanie reguły listy ACL i ich kolejność, należy użyć wierszy na liście. **Podsieci zdalnej** wartość jest zakres adresów IP dla ruchu przychodzącego z Internetu, korzystającą z usługi równoważenia obciążenia Azure celu akceptowanie lub odrzucanie ruchu na podstawie jego adresu IP źródłowego. Pamiętaj określić zakres adresów IP w formacie CIDR, znanej także jako formacie prefiksu adresu. Może to być na przykład `10.1.0.0/8`.

 ![Nowy wpis listy kontroli dostępu](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Zasady można użyć zezwalająca na ruch tylko z określonych komputerów odpowiadający komputerów w Internecie lub odrzucanie ruchu z zakresów określonych, znanych adresów.

Zasady są oceniane w kolejności, zaczynając od pierwszej reguły, a kończąc ostatnia reguła. Oznacza to, reguły powinna być wyświetlana z najmniej restrykcyjny do najbardziej restrykcyjne. Aby uzyskać więcej informacji, zobacz [co to jest lista kontroli dostępu do sieci](../articles/virtual-network/virtual-networks-acl.md).
