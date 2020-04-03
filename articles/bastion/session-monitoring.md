---
title: Monitorowanie sesji i zarządzanie sesjami usługi Azure Bastion | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak wybrać trwającą sesję i wymusić jej rozłączenie lub usunięcie.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619191"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitorowanie sesji i zarządzanie dla usługi Azure Bastion

Po udostępnieniu i wdrożeniu usługi Bastion w sieci wirtualnej można jej używać do bezproblemowego łączenia się z dowolną maszyną wirtualną w tej sieci wirtualnej. Gdy użytkownicy łączą się z obciążeniami, usługa Azure Bastion może służyć do monitorowania sesji zdalnych i podejmowania szybkich akcji zarządzania. Monitorowanie sesji usługi Azure Bastion umożliwia wyświetlanie, którzy użytkownicy są połączeni z maszynami wirtualnymi. Pokazuje adres IP, z którym użytkownik jest połączony, jak długo był podłączony i kiedy się łączył. Środowisko zarządzania sesjami umożliwia wybranie trwającej sesji i wymuś rozłączenie lub usunięcie sesji w celu odłączenia użytkownika od trwającej sesji.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorowanie sesji zdalnych

1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do zasobu Bastion platformy Azure i wybierz **pozycję Sesje** ze strony Bastion platformy Azure.

   ![sesje](./media/session-monitoring/sessions.png)
2. Na stronie **Sesje** możesz zobaczyć trwające sesje zdalne po prawej stronie.

   ![zobacz sesję](./media/session-monitoring/view-session.png)
3. Wybierz **pozycję Odśwież,** aby wyświetlić zaktualizowaną listę sesji zdalnych. Po wybraniu opcji Odśwież bastion platformy Azure pobierze najnowsze informacje o monitorowaniu i odświeży je w portalu.

   ![odświeżanie](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Włącz port 4443 dla ruchu przychodzącego z Menedżera bramy do monitorowania sesji do pracy.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Usuwanie lub wymuszanie rozłączania trwającej sesji zdalnej

Można wybrać zestaw sesji i wymusić ich odłączenie. W poniższych krokach pokazano, jak usunąć sesje zdalne:

1. Przejdź do zasobu Bastion platformy Azure i wybierz **sesje** ze strony Bastion platformy Azure.

   ![nawigacja](./media/session-monitoring/navigate.png)
2. Po wybraniu opcji Sesje zostanie wyświetlona lista sesji zdalnych.

   ![lista sesji](./media/session-monitoring/list.png)
3. Wybierz określoną sesję zdalną, a następnie zaznacz trzy elipsy po prawej stronie wiersza sesji, a następnie wybierz pozycję **Usuń**.

   ![delete](./media/session-monitoring/delete.png)
4. Po wybraniu opcji Usuń sesja zdalna zostanie rozłączona, a użytkownik zostanie wyświetlony komunikat "Zostałeś rozłączony" w sesji zdalnej.

   ![rozłączenie](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane pytania dotyczące [bastionu](bastion-faq.md).