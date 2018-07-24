---
title: Przegląd dostępu wszystkich użytkowników w usłudze Azure AD przeglądów dostępu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić przegląd dostępu za pomocą przeglądów dostępu w usłudze Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 2e0a522f6125a9e34a37c50f547e725072ec65b4
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205007"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Przegląd dostępu wszystkich użytkowników z usługą Azure AD przeglądów dostępu

Azure Active Directory (Azure AD) upraszcza przedsiębiorstw zarządzanie dostępem do aplikacji i członkami grup w usłudze Azure AD i przeglądy innych Microsoft Online Services, przy użyciu funkcji o nazwie dostęp. Być może otrzymasz wiadomość e-mail od firmy Microsoft, która prosi o przegląd dostępu wszystkich użytkowników dla członków grupy lub użytkownicy z dostępem do aplikacji. 

## <a name="open-an-access-review"></a>Otwórz przegląd dostępu

Aby wyświetlić przeglądów dostępu oczekujące, kliknij link dostępu do przeglądu w wiadomości e-mail. Począwszy od sierpnia 2018 powiadomienia e-mail dla ról usługi Azure AD ma zaktualizowanego projektu. Poniżej przedstawiono przykładową wiadomością e-mail jest wysyłana, gdy użytkownik otrzymał zaproszenie jako osoba dokonująca przeglądu. 

![Przejrzyj dostęp do poczty e-mail](./media/active-directory-azure-ad-controls-perform-access-review/new-ar-email.png)

Jeśli nie masz wiadomości e-mail, możesz znaleźć przeglądów dostępu, wykonaj następujące czynności:

1. Zaloguj się na [panelu dostępu usługi Azure AD](https://myapps.microsoft.com).

2. Wybierz symbol użytkownika w prawym górnym rogu strony, który wyświetla nazwę i domyślny organizacji. Jeśli więcej niż jednej z organizacji jest wymieniony, wybierz organizacji, który zażądał przeglądu dostępu.

3. Jeśli Kafelek oznaczone jako **przeglądów dostępu** jest w prawej części strony wybierz opcję. Jeśli Kafelek nie jest widoczna, nie Brak sprawdzeń dostępu do przeprowadzenia tej organizacji i w tej chwili jest wymagana żadna akcja.

## <a name="fill-out-an-access-review"></a>Wypełnij przeglądu dostępu

Po wybraniu przeglądu dostępu z listy zobaczysz nazwy użytkowników, którzy muszą być analizowane. Widoczne mogą być tylko jedną nazwę — własne — Jeśli żądanie zostało do przeglądu własnego dostępu.

Dla każdego wiersza na liście możesz zdecydować, czy zatwierdzić lub odrzucić dany użytkownik ma dostęp. Zaznacz wiersz i określ, czy chcesz zatwierdzić lub odrzucić. (Jeśli użytkownik nie jest znany, można wskazać, że zbyt.)

Recenzent może wymagać, że możesz podać uzasadnienie zatwierdzenie przedłużenia dostępu oraz członkostwa w grupie.

## <a name="next-steps"></a>Kolejne kroki

Odmowa dostępu użytkownika nie jest usuwana natychmiast. Można je usunąć, po zakończeniu przeglądu lub administrator zatrzymania przeglądu. Jeśli chcesz zmienić odpowiedzi, a następnie zatwierdzić wcześniej zabronionych użytkownika lub Odmów użytkownikowi zatwierdzonego wcześniej, wybierz wiersz, zresetuj odpowiedzi, a następnie wybierz nową odpowiedź. Aby wykonać ten krok, dopiero po zakończeniu przeglądu dostępu.



