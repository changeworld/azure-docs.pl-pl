---
title: Azure AD Domain Services — Sprawdź kondycję domeny zarządzanej | Microsoft Docs
description: Sprawdź kondycję domeny zarządzanej przy użyciu strony kondycja w Azure Portal.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 6b808126fe4366d3ca3cc19c674b489ec3055665
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234155"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Sprawdzanie kondycji domeny zarządzanej Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Przegląd strony kondycji
Korzystając ze strony kondycja na Azure Portal, możesz zapewnić aktualność informacji na temat tego, co dzieje się w domenie zarządzanej. Ten artykuł przeprowadzi Cię przez elementy strony kondycji.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Jak wyświetlić kondycję domeny zarządzanej
1. Przejdź do [strony Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w Azure Portal.
2. Kliknij domenę, dla której chcesz wyświetlić kondycję.
3. W okienku nawigacji po lewej stronie kliknij pozycję **kondycja**.

Na poniższej ilustracji przedstawiono przykładową stronę kondycji: ![Przykładowa strona kondycji](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Kondycja domeny zarządzanej jest oceniana co godzinę. Po wprowadzeniu zmian w domenie zarządzanej poczekaj na następny cykl oceny, aby wyświetlić zaktualizowaną kondycję domeny zarządzanej. Sygnatura czasowa "Ostatnia Ocena" w prawym górnym rogu pokazuje, kiedy kondycja domeny zarządzanej była ostatnio Szacowana.
>

### <a name="status-of-your-managed-domain"></a>Stan domeny zarządzanej
Stan w prawym górnym rogu strony kondycji wskazuje ogólną kondycję domeny zarządzanej. Czynniki stanu we wszystkich istniejących alertach w domenie. Stan domeny można także wyświetlić na stronie Przegląd Azure AD Domain Services.

| Stan | Ikona | Wyjaśnienie |
| --- | :----: | --- |
| Działanie | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Twoja domena zarządzana działa bezproblemowo i nie ma alertów krytycznych ani ostrzeżeń. Ta domena może mieć alerty informacyjne. |
| Wymaga uwagi (ostrzeżenie) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Brak alertów krytycznych w domenie zarządzanej, ale istnieje co najmniej jeden Alert ostrzegawczy, który należy rozwiązać. |
| Wymaga uwagi (krytyczne) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Istnieje co najmniej jeden alert krytyczny w domenie zarządzanej. Możesz również mieć alerty ostrzegawcze i/lub informacyjne. |
| Wdrażanie | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Trwa proces wdrażania domeny. |

## <a name="monitors"></a>Monitory
Monitory są aspektami zarządzanej domeny, które regularnie Azure AD Domain Services monitoruje. Najlepszym sposobem utrzymywania monitorów w prawidłowym stanie jest rozwiązanie wszystkich aktywnych alertów dla domeny zarządzanej.

Azure AD Domain Services obecnie monitoruje następujące elementy:
 - Backup
 - Synchronizacja z usługą Azure AD

### <a name="the-backup-monitor"></a>Monitor "Backup"
To monitoruje, czy są wykonywane regularne kopie zapasowe domeny zarządzanej. W poniższej tabeli opisano, czego można oczekiwać w kolumnie szczegóły monitora kopii zapasowej:

| Wartość Szczegółowa | Wyjaśnienie |
| --- | --- |
|"Nigdy nie wykonuj kopii zapasowej" | Ten stan jest normalny dla nowo utworzonej domeny zarządzanej. Zazwyczaj pierwsza kopia zapasowa jest tworzona przez 24 godziny po zainicjowaniu obsługi administracyjnej domeny zarządzanej. Jeśli zarządzana domena nie jest nowo utworzona lub widzisz ten stan przez niewłaściwy czas, [skontaktuj się z pomocą techniczną](contact-us.md). |
| Ostatnia kopia zapasowa została wykonana od 1 do 14 dni temu | Ogólnie rzecz biorąc, ta wartość jest oczekiwana dla monitora kopii zapasowych. |
| Ostatnia kopia zapasowa została wykonana ponad 14 dni temu. | Czas dłuższy niż dwa tygodnie jest zwykle dłuższy od momentu utworzenia ostatniej kopii zapasowej. Aktywne alerty krytyczne mogą uniemożliwiać regularne tworzenie kopii zapasowej domeny zarządzanej. Najpierw Rozwiąż wszystkie aktywne alerty dla domeny zarządzanej, a następnie, jeśli problem nadal występuje, [skontaktuj się z pomocą techniczną](contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitor "Synchronizacja z usługą Azure AD"
Firma Microsoft monitoruje, jak często domena zarządzana jest synchronizowana z Azure Active Directory. Liczba obiektów (użytkowników & grupy) i liczba zmian wprowadzonych w katalogu usługi Azure AD od momentu ostatniej synchronizacji może mieć wpływ na czas trwania synchronizacji. Jeśli Twoja domena zarządzana była ostatnio synchronizowana przez trzy dni temu, [skontaktuj się z pomocą techniczną](contact-us.md).

## <a name="alerts"></a>Alerty
Alerty są generowane w celu uzyskania problemów w domenie zarządzanej, które muszą zostać rozwiązane, aby Azure AD Domain Services do uruchomienia. Każdy alert wyjaśnia problem i zawiera adres URL rozwiązania, w którym opisano poszczególne kroki umożliwiające rozwiązanie problemu. Aby wyświetlić wszystkie alerty i ich rozwiązania, zapoznaj się z artykułem [Rozwiązywanie problemów](troubleshoot-alerts.md) z alertami.

### <a name="alert-severity"></a>Ważność alertu
Alerty są podzielone na trzy różne poziomy ważności: krytyczne, ostrzegawcze i informacyjne.

 * **Alerty krytyczne** to problemy, które poważnie wpływają na domenę zarządzaną. Te alerty powinny być natychmiast rozwiązywane, ponieważ firma Microsoft nie może monitorować i synchronizować domeny zarządzanej oraz zarządzać nią. 
 * **Alerty ostrzegawcze** powiadamiają o problemach, które mogą mieć wpływ na domenę zarządzaną w przyszłości. Te alerty oferują zalecenia dotyczące zabezpieczania domeny zarządzanej.
 * **Alerty informacyjne** to powiadomienia, które nie wpływają negatywnie na Twoją domenę. Alerty informacyjne zostały zaprojektowane w celu zapewnienia znajomości informacji o tym, co dzieje się w domenie i Azure AD Domain Services.

## <a name="next-steps"></a>Następne kroki
- [Rozwiązywanie alertów w domenie zarządzanej](troubleshoot-alerts.md)
- [Przeczytaj więcej na temat Azure AD Domain Services](overview.md)
- [Skontaktuj się z zespołem produktu](contact-us.md)
