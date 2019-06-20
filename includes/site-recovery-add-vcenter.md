---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183760"
---
W oknie **Dodawanie serwera vCenter** podaj przyjazną nazwę dla hosta vSphere lub serwera vCenter, a następnie podaj adres IP lub nazwę FQDN serwera. Pozostaw port ustawiony na 443, chyba że Twoje serwery VMware są skonfigurowane do nasłuchiwania żądań na innym porcie. Wybierz konto, które ma się łączyć z serwerem VMware vCenter lub vSphere ESXi. Kliknij przycisk **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > W przypadku dodawania serwera VMware vCenter lub hosta vSphere firmy VMware przy użyciu konta, które nie mają uprawnień administratora na serwerze vCenter lub hosta, upewnij się, że konto ma włączone następujące uprawnienia: Centrum danych, Magazyn danych, Folder, hosta, sieci, zasobów, maszyna wirtualna i Rozproszony przełącznik vSphere. Ponadto serwer VMware vCenter musi mieć włączone uprawnienie Widoki magazynu.
