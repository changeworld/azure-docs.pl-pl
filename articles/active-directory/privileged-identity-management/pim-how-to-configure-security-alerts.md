---
title: Konfigurowanie alertów zabezpieczeń dla ról katalogu usługi Azure AD w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról katalogu usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/01/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e7204c223681b9a33c439b0d9fc653167422384a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011701"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Konfigurowanie alertów zabezpieczeń dla ról katalogu usługi Azure AD w usłudze PIM

Usługa Azure AD Privileged Identity Management (PIM) generuje alerty w przypadku podejrzanej lub niebezpieczne działania w środowisku. Po wyzwoleniu alertu ona wyświetlona na pulpicie nawigacyjnym usługi PIM. Wybierz alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliła alert.

![Alerty zabezpieczeń PIM — zrzut ekranu](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

W tej sekcji przedstawiono wszystkie alerty zabezpieczeń dla ról w katalogu, oraz jak naprawiać i jak zapobiec. Ważność ma mają następujące znaczenie:

* **Wysoka**: wymaga natychmiastowego działania z powodu naruszenia zasad.
* **Średnia**: nie wymagać natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
* **Niska**: nie wymagać natychmiastowego działania, ale sugeruje zmianę preferowane zasady.

### <a name="roles-are-being-assigned-outside-of-pim"></a>Role są przypisywane poza usługą PIM

| | |
| --- | --- |
| **Ważność** | Wysoka |
| **Dlaczego warto uzyskać ten alert?** | Przypisania ról uprzywilejowanych dokonane poza usługą PIM nie są prawidłowo monitorowane i może oznaczać atak active. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usunąć je z ról uprzywilejowanych przypisane poza usługą PIM. |
| **Zapobieganie** | Zbadaj, gdzie użytkownicy są przypisywane ról uprzywilejowanych poza usługą PIM i Stanów Zjednoczonych zabraniają przyszłych przydziałów, w tym miejscu. |
| **Akcja ograniczenia w portalu** | Usuwa konto z ich roli uprzywilejowanej. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potencjalne starych kont w roli uprzywilejowanej

| | |
| --- | --- |
| **Ważność** | Medium |
| **Dlaczego warto uzyskać ten alert?** | Konta, które nie uległy zmianie hasła ostatnio może być usługa lub udostępnionych kont, które nie są już uaktualniana. Te konta, w ramach ról uprzywilejowanych są narażone na ataki. |
| **Jak naprawić?** | Przegląd konta na liście. Jeśli już nie potrzebują dostępu, należy je usunąć z ich ról uprzywilejowanych. |
| **Zapobieganie** | Upewnij się, kont, które są udostępniane są obracanie silne hasła po zmiany użytkowników, którzy znać hasło. </br>Regularne przeglądy kont z ról uprzywilejowanych, korzystanie z przeglądów dostępu i usuwanie przypisań ról, które nie są już potrzebne. |
| **Akcja ograniczenia w portalu** | Usuwa konto z ich roli uprzywilejowanej. |

### <a name="users-arent-using-their-privileged-roles"></a>Użytkownicy nie są z ich ról uprzywilejowanych

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego warto uzyskać ten alert?** | Użytkownicy, które zostały przypisane do ról uprzywilejowanych, które nie potrzebują zwiększa ryzyko ataku. Jest również łatwiej pozostają niezauważona na kontach, które nie są aktywnie używane przez osoby atakujące. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i usunąć je z ról uprzywilejowanych, który nie ma potrzeby. |
| **Zapobieganie** | Role uprzywilejowane można przypisywać tylko do użytkowników, którzy mają uzasadnienie biznesowe. </br>Przeglądy dostępu regularnego harmonogramu, aby sprawdzić, czy użytkownicy nadal konieczne dostępu. |
| **Akcja ograniczenia w portalu** | Usuwa konto z ich roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik przejdzie określoną ilość czasu, bez uaktywniania roli. |
| **Liczba dni** | To ustawienie określa liczbę dni z zakresu od 0 do 100, które użytkownik może go bez uaktywniania roli.|

### <a name="there-are-too-many-global-administrators"></a>Istnieje zbyt wiele Administratorzy globalni

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego warto uzyskać ten alert?** | Administrator globalny jest najwyższym ról uprzywilejowanych. Jeśli Administrator globalny zostanie naruszony, osoba atakująca uzyska dostęp do wszystkich swoich uprawnień, które powoduje zagrożenie dla całego systemu. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście, a następnie usuń wszystkie, które nie wymagają absolutnie rolę administratora globalnego. </br>Przypisz tych użytkowników niższe ról uprzywilejowanych. |
| **Zapobieganie** | Przypisywanie użytkowników o najniższych uprawnieniach roli, które są im potrzebne. |
| **Akcja ograniczenia w portalu** | Usuwa konto z ich roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli są spełnione dwa różne kryteria, a następnie możesz skonfigurować oba z nich. Najpierw należy do osiągnięcia progu administratorów globalnych. Po drugie procent swoje przypisania roli całkowita musi być administratorów globalnych. Jeśli tylko spełniać jeden z tych pomiarów, alert nie jest widoczna. |
| **Minimalna liczba administratorów globalnych** | To ustawienie określa liczbę administratorów globalnych, od 2 do 100, należy rozważyć niebezpieczne kwoty. |
| **Procent administratorów globalnych** | To ustawienie określa minimalny procent administratorów, którzy są Administratorzy globalni od 0% do 100%, który jest niebezpieczne w danym środowisku. |

### <a name="roles-are-being-activated-too-frequently"></a>Role są aktywowane zbyt często

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego warto uzyskać ten alert?** | Wiele aktywacji do tej samej roli uprzywilejowanej przez ten sam użytkownik jest znakiem ataku. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście, a następnie upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla ich roli uprzywilejowanej jest ustawiony na tyle długo dla nich do wykonywania ich zadań. |
| **Zapobieganie** | Upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) uaktywnień ról uprzywilejowanych ustawiono wystarczająco długi, służący do wykonywania ich zadań.</br>[Wymagać uwierzytelniania Wieloskładnikowego](pim-how-to-change-default-settings.md) dla uprzywilejowanych ról, które mają konta współużytkowane przez wielu administratorów. |
| **Akcja ograniczenia w portalu** | ND |
| **Wyzwalacz** | Wyzwalane, gdy użytkownik aktywuje tej samej roli uprzywilejowanej wiele razy w danym okresie. Można skonfigurować zarówno okres czasu, jak i liczbę aktywacji. |
| **Okres odnowienia uaktywnienia** | To ustawienie określa dni, godziny, minuty, a drugi w okresie chcesz użyć do śledzenia podejrzanych odnowienia. |
| **Liczba odnowień aktywacji** | To ustawienie określa liczbę aktywacji od 2 do 100, które uważasz za Alberta alertu w przedziale czasu, który został wybrany. Możesz zmienić to ustawienie za pomocą suwaka lub wpisanie liczby w polu tekstowym. |

### <a name="roles-dont-require-mfa-for-activation"></a>Role nie wymagają usługi MFA do aktywacji

| | |
| --- | --- |
| **Ważność** | Małe |
| **Dlaczego warto uzyskać ten alert?** | Bez uwierzytelniania MFA których bezpieczeństwo zostało naruszone użytkowników można aktywować ról uprzywilejowanych. |
| **Jak naprawić?** | Przejrzyj listę ról i [wymagać uwierzytelniania Wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli. |
| **Zapobieganie** | [Wymagać uwierzytelniania Wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli.  |
| **Akcja ograniczenia w portalu** | Sprawia, że usługa MFA jest wymagany do aktywacji ról uprzywilejowanych. |

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Można dostosować niektóre alerty zabezpieczeń w usłudze PIM chcesz pracować w swoim środowisku i cele zabezpieczeń. Wykonaj następujące kroki, aby otworzyć ustawienia alertów zabezpieczeń:

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **ustawienia** i następnie **alerty**.

    ![Przejdź do ustawień alertów zabezpieczeń](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Kliknij nazwę alertu, aby skonfigurować ustawienia dla tego alertu.

    ![Ustawienia alertów zabezpieczeń](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
