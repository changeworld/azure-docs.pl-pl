---
title: Eskalowania uprawnień chmury prywatnej — Azure VMware Solution by CloudSimple
description: Opisuje sposób podwyższania poziomu uprawnień w chmurze prywatnej funkcji administracyjnych w programie vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333072"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalowania uprawnień vCenter chmurę prywatną z poziomu portalu CloudSimple 

Aby uzyskać dostęp administracyjny do serwera vCenter chmury prywatnej można tymczasowo eskalacji uprawnień usługi CloudSimple.  Korzystając z podwyższonym poziomem uprawnień, można zainstalować rozwiązań VMware, Dodaj źródła tożsamości i zarządzanie użytkownikami.

Nowi użytkownicy można tworzyć w domenie logowania jednokrotnego vCenter i udostępniają vCenter.  Podczas tworzenia nowych użytkowników, dodać je do grupy wbudowane CloudSimple do uzyskiwania dostępu do vCenter.  Aby uzyskać więcej informacji, zobacz [Chmura prywatna CloudSimple modelu uprawnień programu VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Nie wprowadzaj żadnych zmian konfiguracji składników zarządzania. Akcjach podjętych podczas eskalowanych stanu uprzywilejowanych może niekorzystnie wpłynąć na system lub może spowodować systemu stają się niedostępne.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Podwyższanie poziomu uprawnień

1. Dostęp do [CloudSimple portal](access-cloudsimple-portal.md).

2. Otwórz **zasobów** wybierz chmurę prywatną, dla którego chcesz podwyższania poziomu uprawnień.

3. W pobliżu dolnej części strony podsumowania w obszarze **zmienić uprawnienia vSphere**, kliknij przycisk **Escalate**.

    ![Zmienianie uprawnień vSphere](media/escalate-private-cloud-privilege.png)

4. Wybierz typ użytkownika vSphere.  Tylko **CloudOwner@cloudsimple.local** użytkownika lokalnego, może zostać przekazany.

5. Wybierz przedział czasu escalate z listy rozwijanej. Wybierz okres najkrótszej, który pozwoli na wykonanie zadania.

6. Zaznacz pole wyboru, aby upewnić się, że rozumiesz ryzyka.

    ![Eskalowania uprawnień w oknie dialogowym](media/escalate-private-cloud-privilege-dialog.png)

7. Kliknij przycisk **OK**.

8. Proces eskalacji może potrwać kilka minut. Po zakończeniu kliknij przycisk **OK**.

Podwyższenie poziomu rozpoczyna się i trwa aż do zakończenia wybranym zakresie.  Możesz zalogować się do serwera vCenter chmury prywatnej do wykonywania zadań administracyjnych.

> [!IMPORTANT]
> Tylko jeden użytkownik może mieć eskalowało uprawnienia.  Zanim można Eskalowanie uprawnień przez innego użytkownika należy cofnąć podwyższania poziomu uprawnień użytkownika.

## <a name="extend-privilege-escalation"></a>Rozszerzanie podwyższenie poziomu uprawnień

Jeśli potrzebujesz dodatkowego czasu na ukończenie zadań, można rozszerzyć okresu podwyższenie poziomu uprawnień.  Wybierz dodatkowe eskalować przedział czasu, który umożliwia wykonywanie zadań administracyjnych.

1. Na **zasobów** > **Chmurami prywatnymi** w portalu CloudSimple wybierz chmurę prywatną, dla którego chcesz rozszerzyć podwyższenie poziomu uprawnień.

2. W dolnej części karty Podsumowanie kliknij **rozszerzyć podwyższenie poziomu uprawnień**.

    ![Rozszerzanie podwyższenie poziomu uprawnień](media/de-escalate-private-cloud-privilege.png)

3. Wybierz przedział czasu escalate z listy rozwijanej. Przejrzyj nową wartość czasu zakończenia eskalacji.

4. Kliknij przycisk **Zapisz** można wydłużyć okres.

## <a name="de-escalate-privileges"></a>Usuń zaznaczenie pola podwyższania poziomu uprawnień

Po zakończeniu zadań administracyjnych, należy cofnąć eskalować Twoje uprawnienia.  

1. Na **zasobów** > **Chmurami prywatnymi** w portalu CloudSimple wybierz chmurę prywatną, dla którego chcesz cofnąć podwyższania poziomu uprawnień.

2. Kliknij przycisk **cofnąć zostały Eskalowanie**.

3. Kliknij przycisk **OK**.

> [!IMPORTANT]
> Aby uniknąć błędów, wyloguj się z vCenter, a następnie zaloguj się ponownie po deserializować zamocowaniem uprawnień.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie źródła tożsamości vCenter korzystania z usługi Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalowanie rozwiązania tworzenia kopii zapasowych do [kopie zapasowe obciążeń maszyn wirtualnych](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)