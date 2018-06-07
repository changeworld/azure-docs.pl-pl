---
title: Usługi domenowe Azure AD - Sprawdź kondycję domeny zarządzanej | Dokumentacja firmy Microsoft
description: Sprawdź kondycję domeny zarządzanej za pomocą strony kondycji w portalu Azure.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: 5be6a2c36dfec8c8543f6dcfe252ce01b4c28e50
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587242"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Sprawdź kondycję domeny zarządzanej usług domenowych Azure AD

## <a name="overview-of-the-health-page"></a>Omówienie strony kondycji
Na stronie kondycji portalu Azure, jest możliwe zapewnić aktualność na tym, co dzieje się w domenie zarządzanej. W tym artykule przedstawiono elementy strony kondycji.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Jak wyświetlić informacje o kondycji domeny zarządzanej
1. Przejdź do [strony usług domenowych Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w portalu Azure.
2. Polecenie domeny, który chcesz wyświetlić kondycję.
3. W okienku nawigacji po lewej stronie kliknij **kondycji**.

Poniżej przedstawiono przykładową stronę kondycji: ![przykładową stronę kondycji](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> Domeny zarządzanej kondycji jest oceniane co godzinę. Po wprowadzeniu zmian do domeny zarządzanej, poczekaj na następny cykl oceny wyświetlić kondycję zaktualizowane domeny zarządzanej. Sygnatura czasowa "Ostatniej oceny" w prawym górnym rogu pokazuje podczas ostatniej oceny kondycji domeny zarządzanej.
>

### <a name="status-of-your-managed-domain"></a>Stan domeny zarządzanej
Stan w górnym rogu kondycji Twojej strony wskazuje ogólną kondycję domeny zarządzanej. Stan uwzględnia wszystkie istniejące alerty w domenie. Można również wyświetlić stan domenę na stronie przeglądu usług domenowych Azure AD.

| Stan | Ikona | Wyjaśnienie |
| --- | :----: | --- |
| Działanie | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | Domeny zarządzanej sprawnego działania i nie ma żadnych alertów krytyczna lub ostrzeżenie. Ta domena może być alerty informacyjne. |
| Należy zwrócić uwagę (ostrzeżenie) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Nie ma żadnych alertów krytycznych na domeny zarządzanej, ale istnieją alerty ostrzegawcze, które należy rozważyć. |
| Należy zwrócić uwagę (krytyczne) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Brak jednego lub wielu alertów krytycznych na domeny zarządzanej. Ponadto klientowi mogą przysługiwać alerty informacyjne i/lub ostrzeżenia. |
| Wdrażanie | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | Domeny jest w trakcie wdrażania. |

## <a name="monitors"></a>Monitory
Monitory są aspektów domeny zarządzanej monitorująca regularnie usługi domenowe Azure AD. Najlepszy sposób, aby zachować monitory w dobrej kondycji jest Rozwiąż wszystkie aktywne alerty dla domeny zarządzanej.

Usługi domenowe Azure AD obecnie monitoruje następujące czynności:
 - Backup
 - Synchronizacja z usługą Azure AD

### <a name="the-backup-monitor"></a>Monitor "kopii zapasowej"
Monitoruje to, czy są wykonywane regularne tworzenie kopii zapasowych domeny zarządzanej. W poniższej tabeli opisano, czego można oczekiwać w kolumnie szczegóły kopii zapasowej monitora:

| Wartość szczegółów | Wyjaśnienie |
| --- | --- |
|"Nigdy nie wykonano kopii" | Ten stan jest nowo utworzony domeny zarządzanej. Ogólnie rzecz biorąc pierwsza kopia zapasowa jest tworzony 24 godzin po udostępnieniu domeny zarządzanej. Jeśli domeny zarządzanej nie jest nowo utworzony lub ten stan jest widoczny dla nietypowe ilość czasu, [się z pomocą techniczną](active-directory-ds-contact-us.md). |
| Ostatnia kopia zapasowa została wykonana 1 do 14 dni temu | Ogólnie rzecz biorąc Oczekiwano wartości tego monitora kopii zapasowej. |
| Ostatnia kopia zapasowa została wykonana więcej niż 14 dni temu. | Wszelkie czas dłuższy niż dwa tygodnie jest zbyt długi czas od ostatniej kopii zapasowej. Aktywne alerty krytyczne może uniemożliwiać domeny zarządzanej, z których powstaje kopia zapasowa na bieżąco. Po pierwsze, rozwiąż wszystkie aktywne alerty dla domeny zarządzanej, a następnie Jeśli problem nadal pozostanie, [się z pomocą techniczną](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitor "synchronizacji z usługą Azure AD"
Microsoft monitoruje, jak często domeny zarządzanej jest zsynchronizowany z usługą Azure Active Directory. Liczba obiektów (użytkowników i grup) i liczby zmian w katalogu usługi Azure AD, od momentu ostatniej synchronizacji zarówno wpływają na jak długo może trwać okres synchronizacji. Jeśli Twoje domeny zarządzanej ostatniej synchronizacji ponad trzy dni temu, [się z pomocą techniczną](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Alerty
Alerty są generowane problemów na Twojej domeny zarządzanej, które należy rozważyć w kolejności dla usług domenowych Azure AD uruchomić. Każdy alert problem i zapewnia rozpoznawania adresów URL, które opisano wykonania określonych kroków w celu rozwiązania problemu. Aby wyświetlić wszystkie alerty i ich rozwiązania, odwiedź stronę [rozwiązywać problemy dotyczące alertów](active-directory-ds-troubleshoot-alerts.md) artykułu.

### <a name="alert-severity"></a>Ważność alertu
Alerty są podzielone na trzy różne poziomy ważności: krytyczne, ostrzeżenie i informacyjne.

 * **Alerty krytyczne** problemy, które poważnie obniżyć domeny zarządzanej. Te alerty powinny być kierowane natychmiast, jak Microsoft nie można monitorować, zarządzanie poprawki i synchronizacji domeny zarządzanej. 
 * **Alerty ostrzegawcze** powiadomienie, problemów, które mogą mieć wpływ na domeny zarządzanej w przyszłości. Te alerty oferują zalecenia dotyczące bezpiecznego domeny zarządzanej.
 * **Alerty informacyjne** są powiadomienia, które nie są niekorzystnego wpływu na domenie. Alerty informacyjne są przeznaczone do pozwalają na odpowiednią wiedzę na temat działania wykonywane w domenie i domenowych Azure AD usług.

## <a name="next-steps"></a>Kolejne kroki
- [Rozwiązywanie alertów dotyczących domeny zarządzanej](active-directory-ds-troubleshoot-alerts.md)
- [Więcej informacji na temat usług domenowych Azure AD](active-directory-ds-overview.md)
- [Skontaktuj się z zespołem produktu](active-directory-ds-contact-us.md)
