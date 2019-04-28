---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ee5faedd4f59aa791424a1f178f0462922f21d28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485227"
---
Każdy punkt końcowy ma *port publiczny* i *port prywatny*:

* Port publiczny jest używany przez moduł równoważenia obciążenia platformy Azure do nasłuchiwania ruchu przychodzącego do maszyny wirtualnej z Internetu.
* Port prywatny jest używany przez maszynę wirtualną do nasłuchiwania ruchu przychodzącego, zwykle jest kierowany do aplikacji lub usługa jest uruchomiona na maszynie wirtualnej.

Wartości domyślne dla protokołu IP i portów TCP lub UDP dobrze znanych sieci, protokoły są udostępniane, kiedy tworzysz punktów końcowych przy użyciu witryny Azure portal. Dla niestandardowych punktów końcowych określ poprawny protokołu IP (TCP lub UDP) i portów publicznych i prywatnych. Aby losowo Dystrybuuj ruch przychodzący między wieloma maszynami wirtualnymi, należy utworzyć — zestawu o zrównoważonym obciążeniu składający się z wieloma punktami końcowymi.

Po utworzeniu punktu końcowego, można użyć listy kontroli dostępu (ACL), można zdefiniować reguły akceptowanie lub odrzucanie ruch przychodzący do portu publicznego punktu końcowego, w oparciu o jego źródłowy adres IP. Jednak jeśli maszyna wirtualna znajduje się w sieci wirtualnej platformy Azure, użyj sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [temat sieciowych grup zabezpieczeń](../articles/virtual-network/security-overview.md).

> [!NOTE]
> Konfiguracja zapory dla maszyn wirtualnych platformy Azure odbywa się automatycznie dla portów skojarzonych z punktami końcowymi połączenia zdalnego, które Azure konfiguruje się automatycznie. Porty określone dla innych punktów końcowych konfiguracja nie są wykonywane automatycznie do zapory maszyny wirtualnej. Po utworzeniu punktu końcowego maszyny wirtualnej, upewnij się, że Zapora maszyny wirtualnej również zezwala na ruch protokołu i portu prywatnego odpowiadający konfiguracji punktu końcowego. Aby skonfigurować zaporę, skorzystaj z dokumentacji lub Pomoc online dotyczącą systemu operacyjnego uruchomionego na maszynie wirtualnej.
>
>

## <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **maszyn wirtualnych**, a następnie wybierz maszynę wirtualną, którą chcesz skonfigurować.

3. Wybierz **punktów końcowych** w **ustawienia** grupy. **Punktów końcowych** zostanie wyświetlona strona, która wyświetla wszystkie bieżące punkty końcowe dla maszyny wirtualnej. (W tym przykładzie jest przeznaczone dla maszyny Wirtualnej z systemem Windows. Linux VM zostanie domyślnie wyświetla punkt końcowy dla protokołu SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Punkty końcowe](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. Na pasku poleceń powyżej wpisy punktów końcowych, wybierz **Dodaj**. **Dodaj punkt końcowy** zostanie wyświetlona strona.

5. Aby uzyskać **nazwa**, wprowadź nazwę punktu końcowego.

6. Aby uzyskać **protokołu**, wybierają **TCP** lub **UDP**.

7. Aby uzyskać **port publiczny**, wprowadź numer portu dla ruchu przychodzącego z Internetu. 

8. Aby uzyskać **port prywatny**, wprowadź numer portu, na którym nasłuchuje maszyny wirtualnej. Numery portów publicznych i prywatnych mogą być różne. Upewnij się, czy skonfigurowano zapory na maszynie wirtualnej, aby zezwolić na ruch odpowiadający protokół i port prywatny.

9. Kliknij przycisk **OK**.

Nowy punkt końcowy znajduje się na **punktów końcowych** strony.

![Pomyślne utworzenie punktu końcowego](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Zarządzanie listy ACL punktu końcowego
Aby zdefiniować zestaw komputerów, które mogą przesyłać dane, listy ACL punktu końcowego można ograniczyć ruchu na podstawie źródłowego adresu IP. Wykonaj następujące kroki, aby dodać, zmodyfikować lub usunąć listę ACL punktu końcowego.

> [!NOTE]
> Jeśli punkt końcowy jest częścią zestawu z równoważeniem obciążenia, wszelkie zmiany wprowadzone do listy ACL punktu końcowego są stosowane do wszystkich punktów końcowych w zestawie.
>
>

Jeśli maszyna wirtualna znajduje się w sieci wirtualnej platformy Azure, użyj sieciowych grup zabezpieczeń, zamiast listy kontroli dostępu. Aby uzyskać więcej informacji, zobacz [temat sieciowych grup zabezpieczeń](../articles/virtual-network/security-overview.md).

1. Zaloguj się do Portalu Azure.

2. Wybierz **maszyn wirtualnych**, a następnie wybierz nazwę maszyny wirtualnej, który chcesz skonfigurować.

3. Wybierz **punktów końcowych**. Z listy punktów końcowych wybierz odpowiedni punkt końcowy. Lista ACL znajduje się w dolnej części strony.

   ![Określ szczegóły listy ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Użycia wierszy na liście, aby dodać, usunąć, lub Edytuj reguły listy ACL i zmienić ich kolejność. **PODSIECI zdalnej** wartość zakresu adresów IP dla ruchu przychodzącego z Internetu, która używa modułu równoważenia obciążenia platformy Azure, aby akceptowanie lub odrzucanie ruchu, w oparciu o jego źródłowy adres IP. Pamiętaj określić zakres adresów IP format classless Inter-Domain routingu (CIDR), znany także jako format prefiksu adresu. Na przykład `10.1.0.0/8`.

   ![Nowy wpis listy ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Aby zezwolić na ruch tylko z określonych komputerów, odpowiadające na komputerach w Internecie lub zezwalają na ruch z zakresów określonych, znanych adresów, można użyć reguły.

Reguły są obliczane w kolejności, począwszy od pierwszej reguły, a kończąc na ostatni reguły. W związku z tym zasady powinny być uporządkowane od najmniej restrykcyjna, do najbardziej restrykcyjne. Aby uzyskać więcej informacji, zobacz [co to jest lista kontroli dostępu sieciowego](../articles/virtual-network/virtual-networks-acl.md).
