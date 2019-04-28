---
title: Rozwiązywanie problemów z widokami kosztów platformy Azure w przedsiębiorstwach | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać wszelkie problemy, które może być za pomocą widoków koszt organizacji w witrynie Azure portal.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615756"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Rozwiązywanie problemów z widoki kosztów przedsiębiorstwa

W ramach rejestracji enterprise istnieje kilka ustawień, które może spowodować, że użytkownicy nie zobaczyć koszty w ramach rejestracji.  Te ustawienia są zarządzane przez administratora rejestracji. Lub, jeśli rejestracja nie jest bezpośrednio przez firmę Microsoft, ustawienia będą zarządzane przez partnera.  Ten artykuł pomaga zrozumieć, co to są ustawienia oraz ich wpływ na rejestracji. Te ustawienia są niezależne od ról kontroli dostępu opartej na rolach platformy Azure.

## <a name="enabling-access-to-costs"></a>Umożliwianie dostępu do kosztów

Widzisz komunikat bez autoryzacji, lub *"widoki kosztów są wyłączone w Twojej rejestracji".* Jeśli szukasz informacji o koszcie?
![Zrzut ekranu pokazujący "Brak autoryzacji" w polu Koszt dla bieżącego dla subskrypcji.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Może to być jeden z następujących powodów:

1. Azure zostały zakupione w ramach partnera usługi enterprise, a partnera nie release jeszcze cen. Skontaktuj się z partnerem, aby zaktualizować ceny ustawienie w [witryny Enterprise portal](https://ea.azure.com).
2. Jeśli jesteś klientem z bezpośrednimi umowami EA, istnieje kilka możliwości:
    * Jesteś właścicielem konta i Administrator rejestracji wyłączone **AO Wyświetl opłaty** ustawienie.  
    * Jesteś administratorem działu i administratorem rejestracji wyłączone **DA Wyświetl opłaty** ustawienie.
    * Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp. Administrator rejestracji może też zaktualizować ustawienia w [witryny Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Zrzut ekranu pokazujący ustawienia portalu Enterprise opłaty widoku.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Zasób jest niedostępny

Jeśli pojawi się komunikat o błędzie "ten zasób jest niedostępny" podczas próby uzyskania dostępu do subskrypcji lub grupę zarządzania, możesz nie ma odpowiednią rolę, aby wyświetlić ten element.  

![Zrzut ekranu pokazujący wiadomość "zasobu jest niedostępna".](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Poproś Azure subskrypcji lub zarządzania grupy administratora dostępu. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).
