---
title: Tworzenie sieci/podsieci VLAN — rozwiązanie Azure VMware według cloudsimple
description: Rozwiązania VMware platformy Azure firmy CloudSimple — opisuje sposób tworzenia sieci/podsieci/podsieci sieci VLAN dla chmur prywatnych i zarządzania nimi, a następnie stosowania reguł zapory.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565999"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Tworzenie sieci/podsieci sieci VLAN i zarządzanie nimi dla chmur prywatnych

Otwórz kartę Sieci/podsieci na stronie Sieć, aby utworzyć sieci/podsieci sieci VPN i zarządzać nimi. Po utworzeniu sieci VLAN/podsieci można zastosować reguły zapory.

## <a name="create-a-vlansubnet"></a>Tworzenie sieci VLAN/podsieci

1. [Przejdź do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz polecenie **Sieć** w menu bocznym.
2. Wybierz **sieci/podsieci VLAN**.
3. Kliknij **pozycję Utwórz sieć VLAN/Podsieć**.

    ![Strona sieci VLAN/podsieci](media/vlan-subnet-page.png)

4. Wybierz chmurę prywatną dla nowej sieci VLAN/podsieci.
5. Wprowadź identyfikator sieci VLAN.
6. Wprowadź nazwę podsieci.
7. Aby włączyć routing w sieci VLAN (podsieć), należy określić zakres CIDR podsieci. Upewnij się, że zakres CIDR nie pokrywa się z żadnym z podsieci lokalnych, podsieci platformy Azure lub podsieci bramy.
8. Kliknij **przycisk Prześlij**.

    ![Tworzenie sieci VLAN/podsieci](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Istnieje przydział 30 sieci VLAN na chmurę prywatną. Limity te można zwiększyć, [kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Konfigurowanie rozproszonej grupy portów w sferze vSphere za pomocą informacji sieci VLAN

Aby utworzyć grupę portów rozproszonych w vSphere, postępuj zgodnie z instrukcjami w temacie VMware "Dodawanie rozproszonej grupy portów" w <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">przewodniku sieci vSphere</a>. Podczas konfigurowania rozproszonej grupy portów podaj informacje vlan z konfiguracji CloudSimple.

![Rozproszona grupa portów](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Wybieranie tabeli zapory

Tabele zapory i skojarzone reguły są definiowane na stronie **Tabele zapory sieciowej >.** Aby wybrać tabelę zapory do zastosowania do sieci VLAN/podsieci dla chmury prywatnej, wybierz pozycję VLAN/podsieć kliknij **pozycję Załącznik tabeli zapory** na stronie **Sieci/podsieci.** Zobacz [Tabele zapory, aby](firewall.md) uzyskać instrukcje dotyczące konfigurowania tabel zapory i definiowania reguł.

![Łącze do tabeli zapory](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Podsieć może być skojarzona z jedną tabelą zapory. Tabela zapory może być skojarzona z wieloma podsieciami.

## <a name="edit-a-vlansubnet"></a>Edytowanie sieci VLAN/podsieci

Aby edytować ustawienia sieci VLAN/Podsieci, zaznacz ją na stronie **Sieci/podsieci,** a następnie kliknij ikonę **Edytuj.** Wprowadzanie zmian i klikanie **przycisku Podmet**.

## <a name="delete-a-vlansubnet"></a>Usuwanie sieci VLAN/podsieci

Aby usunąć sieć VLAN/Podsieć, zaznacz ją na stronie **Sieci/podsieci,** a następnie kliknij ikonę **Usuń.** Kliknij **przycisk Usuń,** aby potwierdzić.
