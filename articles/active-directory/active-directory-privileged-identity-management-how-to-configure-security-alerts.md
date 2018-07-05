---
title: Jak skonfigurować alerty zabezpieczeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla rozszerzenia Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d054ca015fd97b3ec45f18b2d3a795eccf606993
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447561"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Jak skonfigurować alerty zabezpieczeń w usłudze Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Alerty zabezpieczeń
Azure Privileged Identity Management (PIM) generuje alerty w przypadku działania związane z niebezpieczne lub podejrzane w danym środowisku. Po wyzwoleniu alertu ona wyświetlona na pulpicie nawigacyjnym usługi PIM. Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliła alert.

![Alerty zabezpieczeń pulpitu nawigacyjnego usługi PIM — zrzut ekranu][1]

| Alerty | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Role są przypisywane poza usługą PIM** |Wysoka |Użytkownikowi przypisano trwale ról uprzywilejowanych, poza interfejsu usługi PIM. |Przejrzyj użytkowników na liście i Cofnij przypisanie, ich z uprzywilejowane role przypisane poza usługą PIM. |
| **Role są aktywowane zbyt często** |Medium |Wystąpiło zbyt wiele reaktywacji o tej samej roli w ustalonym terminie w ustawieniach. |Skontaktuj się z użytkownika, aby zobaczyć, dlaczego one uaktywniono rolę tak wiele razy. Może być limitu czasu jest zbyt mała w przypadku ich do wykonywania swoich zadań lub może ich za pomocą skryptów automatycznej aktywacji roli. Upewnij się, że czas trwania aktywacji w ramach swojej roli ustawiono wystarczająco długo, aby je do wykonywania ich zadań. |
| **Role nie wymagają uwierzytelniania wieloskładnikowego w celu aktywacji** |Medium |Dostępne są role, bez włączony w ustawieniach usługi MFA. |Firma Microsoft wymaga uwierzytelniania Wieloskładnikowego dla najbardziej wysoce uprzywilejowanych ról, ale zdecydowanie zachęcamy włączyć usługę MFA do aktywacji wszystkich ról. |
| **Użytkownicy nie są z ich ról uprzywilejowanych** |Małe |Ma kwalifikujących się Administratorzy, którzy nie został ostatnio aktywowany ich ról. |Uruchamianie przeglądu dostępu w celu określenia użytkowników, którzy nie muszą już mieć dostęp. |
| **Istnieje zbyt wiele Administratorzy globalni** |Małe |Istnieją administratorzy charakter bardziej globalny niż zalecane. |Jeśli masz dużą liczbę administratorów globalnych, jest prawdopodobne, że użytkownicy otrzymują więcej uprawnień niż jest to wymagane. Przenieść użytkowników do mniej uprzywilejowanych ról lub upewnij niektóre z nich kwalifikuje się do roli zamiast trwale przypisane. |

### <a name="severity"></a>Ważność
* **Wysoka**: wymaga natychmiastowego działania z powodu naruszenia zasad. 
* **Średnia**: nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: nie wymagać natychmiastowego działania, ale sugeruje zmianę preferrable zasady.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń
Można dostosować niektóre alerty zabezpieczeń w usłudze PIM chcesz pracować w swoim środowisku i cele zabezpieczeń. Wykonaj następujące kroki, aby dotrzeć do bloku ustawienia:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz **usługi Azure AD Privileged Identity Management** kafelka na pulpicie nawigacyjnym.
2. Wybierz **zarządzane ról uprzywilejowanych** > **ustawienia** > **ustawienia alertów**.
   
    ![Przejdź do ustawień alertów zabezpieczeń][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alert "Role są aktywowane zbyt często"
Ten alert jest wyzwalana, gdy użytkownik aktywuje tej samej roli uprzywilejowanej wiele razy w danym okresie. Można skonfigurować zarówno okres czasu, jak i liczbę aktywacji.

* **Okres odnowienia uaktywnienia**: Określ w dni, godziny, minuty i sekundy okres czasu, którego chcesz użyć do śledzenia podejrzanych odnowienia.
* **Liczba odnowień aktywacji**: Określ liczbę aktywacji od 2 do 100, które uważasz za Alberta alertu w przedziale czasu została wybrana opcja. Możesz zmienić to ustawienie za pomocą suwaka lub wpisanie liczby w polu tekstowym.

### <a name="there-are-too-many-global-administrators-alert"></a>Alert "Istnieją zbyt wielu administratorów globalnych"
PIM wyzwala alert, jeśli są spełnione dwa różne kryteria, a następnie możesz skonfigurować oba z nich. Najpierw należy do osiągnięcia progu administratorów globalnych. Po drugie procent swoje przypisania roli całkowita musi być administratorów globalnych. Jeśli tylko spełniać jeden z tych pomiarów, alert nie jest widoczna.  

* **Minimalna liczba administratorów globalnych**: liczba administratorów globalnych, od 2 do 100, należy rozważyć niebezpieczne kwoty.
* **Procent administratorów globalnych**: Określ wartość procentową administratorów, którzy Administratorzy globalni, od 0% do 100%, to niebezpieczne w danym środowisku.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Alert "Administratorzy nie są za pomocą ich ról uprzywilejowanych"
Ten alert wyzwalacze, jeśli użytkownik przejdzie określoną ilość czasu, bez uaktywniania roli.

* **Liczba dni**: Określ liczbę dni z zakresu od 0 do 100, które użytkownik może go bez uaktywniania roli.

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
