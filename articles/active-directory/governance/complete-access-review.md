---
title: Kończenie przeglądu dostępu grup lub aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zakończyć Przegląd dostępu członków grupy lub dostęp do aplikacji w przeglądów dostępu w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4265a7e08eab079e55ce91b27142ec3e55b3f3e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246470"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Kończenie przeglądu dostępu grup lub przeglądów dostępu dla aplikacji w usłudze Azure AD

Administratorzy mogą używać usługi Azure Active Directory (Azure AD) do [tworzenia przeglądów dostępu](create-access-review.md) dla członków grupy lub użytkowników przypisanych do aplikacji. Usługa Azure AD automatycznie wysyła wiadomość e-mail z monitami, aby przeprowadzić przegląd dostępu recenzentów. Jeśli użytkownik nie otrzymasz wiadomość e-mail, możesz wysłać im instrukcje [Przegląd dostępu wszystkich użytkowników do grup ani aplikacji](perform-access-review.md). (Należy zauważyć, że goście, którzy są przypisywane recenzentów, ale nie zaakceptowali zaproszenia nie otrzyma wiadomość e-mail z przeglądów dostępu, zgodnie z ich musisz najpierw zaakceptować zaproszenie przed przeglądania.) Po zakończeniu okresu przeglądu dostępu lub administrator zatrzymuje przeglądu dostępu, wykonaj kroki opisane w tym artykule, aby zobaczyć i zastosować wyniki.

## <a name="view-an-access-review-in-the-azure-portal"></a>Wyświetl przeglądu dostępu w witrynie Azure portal

1. Przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), wybierz opcję **programy**i wybierz program, który zawiera formant Przegląd dostępu.

2. Wybierz **Zarządzaj**i wybierz formant Przegląd dostępu. Jeśli program zawiera wiele kontroli, możesz filtrować kontrole pod kątem określonego typu i sortować je według stanu. Istnieje również możliwość wyszukiwania według nazwy kontroli przeglądu dostępu lub nazwy wyświetlanej jej twórcy. 

## <a name="stop-a-review-that-hasnt-finished"></a>Zatrzymaj Przegląd, który nie zakończono

W przypadku przeglądu nie osiągnięcia planowanej dacie zakończenia, administrator może wybrać opcję **zatrzymać** do końca wcześnie przeglądu. Po zatrzymaniu przeglądu użytkowników nie będzie można wyświetlić. Nie można ponownie uruchomić przeglądu, po jej zatrzymaniu.

## <a name="apply-the-changes"></a>Zastosuj zmiany 

Po zakończeniu przeglądu dostępu albo ponieważ osiągnęła ona Data zakończenia lub ręcznie zatrzymana przez administratora i automatycznie Zastosuj nie został skonfigurowany do przeglądu, możesz wybrać **Zastosuj** ręcznie zastosować zmiany. Wyniku przeglądu jest implementowany przez zaktualizowanie grupę lub aplikację. Jeśli w przeglądzie, nastąpiła odmowa dostępu użytkownika, gdy administrator może wybrać tę opcję, usługi Azure AD spowoduje usunięcie ich przypisania członkostwa lub aplikacji. 

Po zakończeniu przeglądu dostępu i automatycznie Zastosuj została skonfigurowana, stan przeglądu ulegnie zmianie z ukończono za pośrednictwem pośrednich stanów, a na koniec zostanie zmieniony na stan zastosowano. Należy się spodziewać się zablokowani użytkownicy, jeśli dowolny usuwana z zasobu grupy przypisania aplikacji lub członkostwa w ciągu kilku minut.

Automatycznie skonfigurowany, zastosowanie przeglądu lub wybierając **Zastosuj** nie ma wpływu na grupy, który pochodzi z katalogu lokalnego lub grupy dynamicznej. Jeśli chcesz zmienić grupę, która pochodzi w środowisku lokalnym, Pobierz wyniki i stosowanie tych zmian na reprezentację w postaci grupy, w tym katalogu.

## <a name="download-the-results-of-the-review"></a>Pobieranie wyników przeglądu

Aby pobrać wyników przeglądu, wybierz **zatwierdzenia** , a następnie wybierz **Pobierz**. Wynikowy plik CSV można wyświetlać w programie Excel lub w innych programach, które otwierają UTF-8 zakodowane pliki CSV.

## <a name="optional-delete-a-review"></a>Opcjonalnie: Usuwanie przeglądu
Jeśli interesuje Cię już przeglądu, możesz go usunąć. Wybierz **Usuń** usuwanie przeglądu usługi Azure AD.

> [!IMPORTANT]
> Nie ma żadnych ostrzeżenie, zanim nastąpi usunięcie upewnij się, że chcesz usunąć przeglądu.
> 
> 

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](manage-user-access-with-access-reviews.md)
- [Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD](manage-guest-access-with-access-reviews.md)
- [Zarządzanie programami i kontrolami obejmującymi przeglądy dostępu w usłudze Azure AD](manage-programs-controls.md)
- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
