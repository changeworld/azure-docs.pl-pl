---
title: Zrozumienie współużytkowanych adresach IP w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzysta z usługi Azure DevTest Labs współużytkowanych adresach IP zminimalizować publiczne adresy IP wymagane do dostępu do maszyn wirtualnych laboratorium.
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
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236860"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Zrozumienie współużytkowanych adresach IP w usłudze Azure DevTest Labs

Usługa Azure DevTest Labs umożliwia maszyn wirtualnych laboratorium udostępnianie tego samego publiczny adres IP, aby zminimalizować liczbę publicznych adresów IP, wymagane do dostępu do poszczególnych maszyn wirtualnych laboratorium.  W tym artykule opisano udostępnione pracy adresów IP i ich powiązane opcje konfiguracji.

## <a name="shared-ip-setting"></a>Udostępnione ustawienie adresu IP

Po utworzeniu laboratorium, zostanie utworzony w podsieci sieci wirtualnej.  Domyślnie ta podsieć jest tworzony z **Włącz udostępnionego publicznego adresu IP** równa *tak*.  Ta konfiguracja tworzy jeden publiczny adres IP w całej podsieci.  Aby uzyskać więcej informacji na temat konfigurowania sieci wirtualnych i podsieci, zobacz [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nowa podsieć laboratorium](media/devtest-lab-shared-ip/lab-subnet.png)

Istniejące labs tę opcję można włączyć, wybierając **konfiguracji i zasad > sieciami wirtualnymi**. Następnie wybierz sieć wirtualną z listy i wybierz **Włączanie UDOSTĘPNIONEGO publicznego adresu IP** dla wybranej podsieci. Można również wyłączyć tę opcję w dowolnym laboratorium, jeśli nie chcesz udostępniać publiczny adres IP w laboratorium, maszyny wirtualne.

Wszystkie maszyny wirtualne utworzone w tego ustawienia domyślnego laboratorium do korzystania z udostępnionych adresów IP.  Podczas tworzenia maszyny Wirtualnej, to ustawienie można zaobserwować w **Zaawansowane ustawienia** strony w obszarze **konfiguracji adresu IP**.

![Nowa maszyna wirtualna](media/devtest-lab-shared-ip/new-vm.png)

- **Udostępnione:** Wszystkie maszyny wirtualne utworzone jako **Shared** są umieszczane w jednej grupie zasobów (RG). Pojedynczy adres IP jest przypisywany, w tym grupą zasobów i wszystkie maszyny wirtualne w RG użyje tego adresu IP.
- **Publiczny:** Każda maszyna wirtualna, którą tworzysz ma swój własny adres IP i jest tworzona w jego własnej grupie zasobów.
- **Prywatny:** Każda maszyna wirtualna, którą tworzysz używa prywatnego adresu IP. Do tej maszyny Wirtualnej nie można połączyć bezpośrednio z Internetu przy użyciu pulpitu zdalnego.

Gdy maszyny Wirtualnej z udostępnionego IP włączony zostanie dodany do podsieci, DevTest Labs dodaje maszynę Wirtualną z modułem równoważenia obciążenia i automatycznie przypisuje numer portu TCP na publiczny adres IP przesyłania dalej z portem RDP na maszynie Wirtualnej.  

## <a name="using-the-shared-ip"></a>Używa udostępnionego adresu IP

- **Użytkownicy systemu Linux:** SSH z maszyną Wirtualną przy użyciu adresu IP lub w pełni kwalifikowaną nazwę domeny, następuje dwukropek i numer portu. Na przykład na poniższej ilustracji adresu protokołu RDP do połączenia z maszyną wirtualną jest `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`.

  ![Przykładzie dotyczącym maszyny Wirtualnej](media/devtest-lab-shared-ip/vm-info.png)

- **Użytkownicy Windows:** Wybierz **Connect** przycisku w witrynie Azure portal, aby pobrać wstępnie skonfigurowane pliku RDP i dostęp do maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* [Definiowanie zasad laboratorium Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurowanie sieci wirtualnej w usłudze Azure DevTest Labs](devtest-lab-configure-vnet.md)





