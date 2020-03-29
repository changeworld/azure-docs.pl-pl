---
title: Poznaj udostępnione adresy IP w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Azure DevTest Labs używa udostępnionych adresów IP w celu zminimalizowania publicznych adresów IP wymaganych do uzyskania dostępu do maszyn wirtualnych w laboratorium.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65236860"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Opis udostępnionych adresów IP w laboratorium devtest azure

Usługa Azure DevTest Labs umożliwia maszynom wirtualnym laboratorium współużytkowanie tego samego publicznego adresu IP w celu zminimalizowania liczby publicznych adresów IP wymaganych do uzyskania dostępu do poszczególnych maszyn wirtualnych w laboratorium.  W tym artykule opisano, jak działają udostępnione usługi IP i powiązane z nimi opcje konfiguracji.

## <a name="shared-ip-setting"></a>Ustawienie udostępnionego adresu IP

Podczas tworzenia laboratorium jest on tworzony w podsieci sieci wirtualnej.  Domyślnie ta podsieć jest tworzona z **ustawieniem Włącz udostępniony publiczny adres IP** ustawiony na *Tak*.  Ta konfiguracja tworzy jeden publiczny adres IP dla całej podsieci.  Aby uzyskać więcej informacji na temat konfigurowania sieci wirtualnych i podsieci, zobacz [Konfigurowanie sieci wirtualnej w laboratoriach DevTest Azure](devtest-lab-configure-vnet.md).

![Nowa podsieć laboratorium](media/devtest-lab-shared-ip/lab-subnet.png)

W przypadku istniejących laboratoriów można włączyć tę opcję, wybierając **pozycję Konfiguracja i zasady > sieci wirtualnych**. Następnie wybierz sieć wirtualną z listy i wybierz **pozycję WŁĄCZ UDOSTĘPNIONY PUBLICZNY ADRES IP** dla wybranej podsieci. Można również wyłączyć tę opcję w dowolnym laboratorium, jeśli nie chcesz udostępniać publiczny adres IP w laboratorium maszyn wirtualnych.

Wszystkie maszyny wirtualne utworzone w tym laboratorium domyślnie przy użyciu udostępnionego adresu IP.  Podczas tworzenia maszyny Wirtualnej to ustawienie można zaobserwować na stronie **Ustawienia zaawansowane** w obszarze **Konfiguracja adresu IP**.

![Nowa maszyna wirtualna](media/devtest-lab-shared-ip/new-vm.png)

- **Udostępnione:** Wszystkie maszyny wirtualne utworzone jako **udostępnione** są umieszczane w jednej grupie zasobów (RG). Jeden adres IP jest przypisany do tego RG i wszystkie maszyny wirtualne w RG użyje tego adresu IP.
- **Publiczne:** Każda utworzona maszyna wirtualna ma swój własny adres IP i jest tworzona we własnej grupie zasobów.
- **Prywatne:** Każda utworzona maszyna wirtualna używa prywatnego adresu IP. Nie można połączyć się z tą maszyną wirtualną bezpośrednio z Internetu za pomocą pulpitu zdalnego.

Za każdym razem, gdy maszyna wirtualna z włączonym współużytkowanym ip jest dodawana do podsieci, DevTest Labs automatycznie dodaje maszynę wirtualną do modułu równoważenia obciążenia i przypisuje numer portu TCP na publiczny adres IP, przesyłając dalej do portu RDP na maszynie wirtualnej.  

## <a name="using-the-shared-ip"></a>Korzystanie z udostępnionego adresu IP

- **Użytkownicy Linuksa:** SSH do maszyny Wirtualnej przy użyciu adresu IP lub w pełni kwalifikowana nazwa domeny, a następnie dwukropek, a następnie port. Na przykład na poniższej ilustracji adres RDP do `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`łączenia się z maszyną wirtualną to .

  ![Przykład maszyny Wirtualnej](media/devtest-lab-shared-ip/vm-info.png)

- **Użytkownicy systemu Windows:** Wybierz przycisk **Połącz** w witrynie Azure portal, aby pobrać wstępnie skonfigurowany plik RDP i uzyskać dostęp do maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

* [Definiowanie zasad laboratoryjnych w laboratoriach devtest azure](devtest-lab-set-lab-policy.md)
* [Konfigurowanie sieci wirtualnej w laboratorium devtest platformy Azure](devtest-lab-configure-vnet.md)





