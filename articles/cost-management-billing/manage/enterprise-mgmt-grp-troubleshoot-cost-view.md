---
title: Rozwiązywanie problemów z widokami kosztów przedsiębiorstwa na platformie Azure
description: Dowiedz się, jak rozwiązywać problemy z widokami kosztów organizacyjnych w witrynie Azure Portal.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 625774331003754c79e1f1d33c663f8928a71ecc
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411570"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Rozwiązywanie problemów z widokami kosztów przedsiębiorstwa

W ramach rejestracji przedsiębiorstwa istnieje kilka ustawień, które mogą powodować, że użytkownicy w ramach rejestracji nie będą widzieć kosztów.  Te ustawienia są zarządzane przez administratora rejestracji. Jeśli rejestracja nie zostanie zakupiona bezpośrednio od firmy Microsoft, te ustawienia są zarządzane przez partnera.  W tym artykule wyjaśniono działanie tych ustawień oraz ich wpływ na rejestrację. Te ustawienia są niezależne od ról kontroli dostępu opartej na rolach (RBAC) platformy Azure.

## <a name="enable-access-to-costs"></a>Włączanie dostępu do kosztów

Czy widzisz komunikat „Bez autoryzacji” lub *„Widoki kosztów są wyłączone w Twojej rejestracji”* , kiedy wyświetlasz informacje o kosztach?
![Zrzut ekranu przedstawiający komunikat „Bez autoryzacji” w polu Bieżący koszt subskrypcji.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Takie zachowanie może mieć jedną z następujących przyczyn:

1. Platformę Azure zakupiono za pośrednictwem partnera przedsiębiorstwa, który nie opublikował jeszcze cennika. Skontaktuj się z partnerem, aby zaktualizował ustawienie cennika w witrynie [Enterprise Portal](https://ea.azure.com).
2. Jeśli jesteś klientem z bezpośrednią umową EA, masz kilka możliwości:
    * Jesteś właścicielem konta, ale administrator rejestracji wyłączył ustawienie **AO view charges** (Wyświetlanie opłat przez właściciela konta).  
    * Jesteś administratorem działu, ale administrator rejestracji wyłączył ustawienie **DA view charges** (Wyświetlanie opłat przez administratora działu).
    * Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp. Administrator rejestracji może zaktualizować te ustawienia w witrynie [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Zrzut ekranu pokazujący ustawienia witryny Enterprise Portal sterujące wyświetlaniem opłat.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Zasób jest niedostępny

Jeśli podczas próby uzyskania dostępu do subskrypcji lub grupy zarządzania zostanie wyświetlony komunikat o błędzie **Zasób jest niedostępny**, oznacza to, że nie masz poprawnej roli umożliwiającej wyświetlenie tego elementu.  

![Zrzut ekranu przedstawiający komunikat „Zasób jest niedostępny”.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Poproś administratora subskrypcji platformy Azure lub administratora grupy zarządzania o udzielenie dostępu. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki
- Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
