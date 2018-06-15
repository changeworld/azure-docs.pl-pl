---
title: Zrozumienie udostępnionego adresów IP w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Azure DevTest Labs używana udostępnionego adresów IP, aby zminimalizować publiczne adresy IP wymagane do uzyskania dostępu laboratorium maszyn wirtualnych.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c62f8808565022371484b936f5a2bdaba1f8900e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787381"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Zrozumienie udostępnionego adresów IP w usłudze Azure DevTest Labs

Azure DevTest Labs umożliwia maszyn wirtualnych laboratorium udostępnianie tego samego publiczny adres IP, aby zminimalizować liczbę publicznych adresów IP wymagane do uzyskania dostępu laboratorium poszczególnych maszyn wirtualnych.  W tym artykule opisano, jak udostępnione pracy adresów IP oraz ich powiązane opcje konfiguracji.

## <a name="shared-ip-setting"></a>Udostępnione ustawienie adresu IP

Po utworzeniu laboratorium znajduje się w podsieci sieci wirtualnej.  Domyślnie ta podsieć jest tworzony z **Włącz udostępnione publicznego adresu IP** ustawioną *tak*.  Ta konfiguracja tworzy jeden publiczny adres IP w całej podsieci.  Aby uzyskać więcej informacji na temat konfigurowania sieci wirtualnych i podsieci, zobacz [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nowa podsieć laboratorium](media/devtest-lab-shared-ip/lab-subnet.png)

Istniejące labs tę opcję można włączyć, wybierając **konfiguracji i zasadach > sieci wirtualnych**. Następnie wybierz sieć wirtualną z listy i wybierz **Włącz UDOSTĘPNIONE publicznego adresu IP** dla wybranej podsieci. Można również wyłączyć tę opcję w dowolnym laboratorium, jeśli nie chcesz udostępniać publicznego adresu IP w laboratorium maszyn wirtualnych.

Wszystkie maszyny wirtualne utworzone z tego laboratorium domyślną przy użyciu udostępnionych protokołu IP.  Podczas tworzenia maszyny Wirtualnej, to ustawienie można zaobserwować w **Zaawansowane ustawienia** bloku w obszarze **konfiguracji adresu IP**.

![Nowej maszyny Wirtualnej](media/devtest-lab-shared-ip/new-vm.png)

- **Shared:** wszystkie maszyny wirtualne utworzone jako **Shared** są umieszczane w jednej grupy zasobów (zarządcy zasobów). Pojedynczy adres IP jest przypisany do tego zarządcy zasobów i wszystkich maszyn wirtualnych w grupie replikacji użyje tego adresu IP.
- **Publicznego:** każdej maszyny Wirtualnej tworzonej ma swój adres IP i jest tworzona w jego własnej grupy zasobów.
- **Prywatne:** każdej maszyny Wirtualnej tworzonej używa prywatnego adresu IP. Nie można nawiązać połączenia z tej maszyny Wirtualnej bezpośrednio z Internetu przy użyciu pulpitu zdalnego.

Zawsze, gdy maszyny Wirtualnej z udostępnionego IP włączony jest dodawany do podsieci, DevTest Labs automatycznie dodaje maszynę Wirtualną z modułem równoważenia obciążenia i przypisuje numer portu TCP na publiczny adres IP, przesyłania dalej z portem RDP na maszynie Wirtualnej.  

## <a name="using-the-shared-ip"></a>Za pomocą udostępnionego IP

- **Użytkownicy systemu Linux:** SSH z maszyną Wirtualną przy użyciu adresu IP lub nazwy FQDN, wprowadź dwukropek, a następnie port. Na przykład na poniższej ilustracji, adres protokołu RDP do nawiązania połączenia z maszyną Wirtualną jest `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Przykład maszyny Wirtualnej](media/devtest-lab-shared-ip/vm-info.png)

- **Użytkownicy systemu Windows:** wybierz **Connect** przycisk w portalu Azure, aby pobrać plik RDP wstępnie skonfigurowane i dostęp do maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* [Definiowanie zasad laboratorium w usłudze Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md)





