---
title: Sprawdzanie poprawności reguł członkostwa w grupach dynamicznych (wersja zapoznawcza) — Usługa Azure AD | Dokumenty firmy Microsoft
description: Jak przetestować członków z reguły członkostwa dla grup dynamicznych w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115523"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Sprawdzanie poprawności reguły członkostwa grupy dynamicznej (w wersji zapoznawczej) w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) zapewnia teraz środki do sprawdzania poprawności reguł grupy dynamicznej (w publicznej wersji zapoznawczej). Na karcie **Sprawdzanie poprawności reguł** można sprawdzić poprawność reguły dynamicznej względem przykładowych członków grupy, aby potwierdzić, że reguła działa zgodnie z oczekiwaniami. Podczas tworzenia lub aktualizowania reguł grupy dynamicznej administratorzy chcą wiedzieć, czy użytkownik lub urządzenie będzie członkiem grupy. Pomaga to ocenić, czy użytkownik lub urządzenie spełnia kryteria reguły i pomoc w rozwiązywaniu problemów, gdy członkostwo nie jest oczekiwane.

## <a name="step-by-step-walk-through"></a>Krok po kroku

Aby rozpocząć, przejdź do **grup usługi Azure Active Directory** > **.** Wybierz istniejącą grupę dynamiczną lub utwórz nową grupę dynamiczną i kliknij dynamiczne reguły członkostwa. Następnie można wyświetlić kartę **Sprawdzanie poprawności reguł.**

![Znajdź kartę Sprawdzanie poprawności reguł i zacznij od istniejącej reguły](./media/groups-dynamic-rule-validation/validate-tab.png)

Na karcie **Sprawdzanie poprawności reguł** możesz wybrać użytkowników, którzy mają sprawdzić poprawność ich członkostwa. Jednocześnie można wybrać 20 użytkowników lub urządzeń.

![Dodawanie użytkowników w celu zweryfikowannia istniejącej reguły](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Po wybraniu użytkowników lub urządzeń z selektora i **Wybierz,** sprawdzanie poprawności zostanie automatycznie rozpocznie się i wyniki sprawdzania poprawności pojawią się.

![Wyświetlanie wyników sprawdzania poprawności reguły](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Wyniki mówią, czy użytkownik jest członkiem grupy, czy nie. Jeśli reguła jest nieprawidłowa lub występuje problem z siecią, wynik będzie pokazywany jako **Nieznany**. W przypadku **nieznanych**szczegółowy komunikat o błędzie opisze problem i potrzebne akcje.

![Wyświetlanie szczegółów wyników sprawdzania poprawności reguły](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Można zmodyfikować regułę i sprawdzanie poprawności członkostwa zostaną wyzwolone. Aby zobaczyć, dlaczego użytkownik nie jest członkiem grupy, kliknij "Wyświetl szczegóły", a szczegóły weryfikacji pokażą wynik każdego wyrażenia tworzącego regułę. Kliknij **przycisk OK,** aby wyjść.

## <a name="next-steps"></a>Następne kroki

- [Dynamiczne reguły członkostwa dla grup](groups-dynamic-membership.md)
