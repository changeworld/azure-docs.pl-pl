---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8f7eae06947a40117f3952a0a5624c22df750b34
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165958"
---
* W oknie **Dodawanie serwera vCenter** podaj przyjazną nazwę dla hosta vSphere lub serwera vCenter, a następnie podaj adres IP lub nazwę FQDN serwera. Pozostaw port ustawiony na 443, chyba że Twoje serwery VMware są skonfigurowane do nasłuchiwania żądań na innym porcie. Wybierz konto, które ma się łączyć z serwerem VMware vCenter lub vSphere ESXi. Kliknij przycisk **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > W przypadku dodawania serwera VMware vCenter lub hosta VMware vSphere za pomocą konta bez uprawnień administracyjnych na serwerze vCenter lub serwerze hosta upewnij się, że konto ma włączone następujące uprawnienia: Centrum danych, Magazyn danych, Folder, Host, Sieć, Zasób, Maszyna wirtualna i Rozproszony przełącznik vSphere. Ponadto serwer VMware vCenter musi mieć włączone uprawnienie Widoki magazynu.
