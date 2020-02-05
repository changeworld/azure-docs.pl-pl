---
title: Escalate AVS private cloud privileges - Azure VMware Solution by AVS
description: Describes how to escalate privileges on your AVS private cloud for administrative functions in vCenter
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 211960af359e19f93afef58162c5b09ae1d9b23f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025320"
---
# <a name="escalate-avs-private-cloud-vcenter-privileges-from-the-avs-portal"></a>Escalate AVS Private Cloud vCenter privileges from the AVS portal

For administrative access to your AVS Private Cloud vCenter, you can temporarily escalate your AVS privileges. Using elevated privileges, you can install VMware solutions, add identity sources, and manage users.

New users can be created on the vCenter SSO domain and given access to vCenter. When you create new users, add them to the AVS builtin groups for accessing vCenter. For more information, see [AVS Private Cloud permission model of VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Don’t make any configuration changes for management components. Actions taken during the escalated privileged state can adversely impact your system or can cause your system to become unavailable.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Podwyższanie poziomu uprawnień

1. Access the [AVS portal](access-cloudsimple-portal.md).

2. Open the **Resources** page, select the AVS Private Cloud for which you want to escalate privileges.

3. Near the bottom of the Summary page under **Change vSphere privileges**, click **Escalate**.

    ![Change vSphere privilege](media/escalate-private-cloud-privilege.png)

4. Select the vSphere user type. Only `CloudOwner@cloudsimple.local` local user can be escalated.

5. Select the escalate time interval from the drop-down. Choose the shortest period that will allow you to complete the task.

6. Select the checkbox to confirm that you understand the risks.

    ![Escalate privilege dialog](media/escalate-private-cloud-privilege-dialog.png)

7. Kliknij przycisk **OK**.

8. The escalation process can take a couple of minutes. Po zakończeniu kliknij przycisk **OK**.

The privilege escalation begins and lasts until the end of the selected interval. You can sign in to your AVS private cloud vCenter to do administrative tasks.

> [!IMPORTANT]
> Tylko jeden użytkownik może mieć uprawnienia eskalacji. You must de-escalate the user's privileges before you can escalate another user's privileges.

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group  Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.

## <a name="extend-privilege-escalation"></a>Extend privilege escalation

If you require additional time to complete your tasks, you can extend the privilege escalation period. Choose the additional escalate time interval that allows you to complete the administrative tasks.

1. On the **Resources** > **AVS Private Clouds** in the AVS portal, select the AVS Private Cloud for which you want to extend privilege escalation.

2. Near the bottom of the Summary tab, click **Extend privilege escalation**.

    ![Extend privilege escalation](media/de-escalate-private-cloud-privilege.png)

3. Select an escalate time interval from the drop-down. Review the new escalation end time.

4. Click **Save** to extend the interval.

## <a name="de-escalate-privileges"></a>De-escalate privileges

Once your administrative tasks are complete, you should de-escalate your privileges. 

1. On the **Resources** > **AVS Private Clouds** in the AVS portal, select the AVS Private Cloud for which you want to de-escalate privileges.

2. Click **De-escalate**.

3. Kliknij przycisk **OK**.

> [!IMPORTANT]
> To avoid any errors, sign out of vCenter and sign in again after de-escalating privileges.

## <a name="next-steps"></a>Następne kroki

* [Set up vCenter identity sources to use Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Install backup solution to [backup workload virtual machines](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)