---
title: Azure AD Domain Services — sprawdzanie kondycji domeny zarządzanej | Dokumentacja firmy Microsoft
description: Sprawdzanie kondycji domeny zarządzanej przy użyciu strony kondycji w witrynie Azure portal.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: c3ff0b7daeaef077ea0d83782aeac6cda56fc659
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246482"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Sprawdzanie kondycji domeny zarządzanej usług domenowych Azure AD

## <a name="overview-of-the-health-page"></a>Omówienie strony kondycji
Za pomocą strony kondycji w swojej witrynie Azure portal, jesteś w stanie zapewnić aktualność na tym, co dzieje się w domenie zarządzanej. W tym artykule przedstawiono elementy na stronie kondycji.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Jak wyświetlić kondycję domeny zarządzanej
1. Przejdź do [strony usług domenowych Azure AD](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) w witrynie Azure portal.
2. Kliknij domenę, aby wyświetlić kondycję elementów.
3. W okienku nawigacji po lewej stronie kliknij **kondycji**.

Poniższy obraz pokazuje przykładowa strona kondycji: ![Przykładowa strona kondycji](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Kondycji domeny zarządzanej jest obliczane co godzinę. Po wprowadzeniu zmian do domeny zarządzanej, poczekaj, aż następnym cyklu szacowania, aby wyświetlić zaktualizowaną kondycję domeny zarządzanej. Sygnatura czasowa "Datą ostatniej oceny" w prawym górnym rogu pokazuje, kiedy kondycję domeny zarządzanej datą ostatniej oceny.
>

### <a name="status-of-your-managed-domain"></a>Stan domeny zarządzanej
Stan w prawym górnym rogu kondycję swojej stronie wskazuje ogólną kondycję domeny zarządzanej. Stan uwzględnia wszystkie istniejące alerty w domenie. Możesz również wyświetlić stan domeny, na stronie Przegląd w usługach domenowych Azure AD.

| Stan | Ikona | Wyjaśnienie |
| --- | :----: | --- |
| Działanie | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15"> | Twoja domena zarządzana jest sprawne i nie ma żadnych alertów krytyczna lub poważna. Ta domena może być alerty informacyjne. |
| Wymaga uwagi (ostrzeżenie) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15"> | Brak alertów krytycznych w domenie zarządzanej, ale istnieją alerty ostrzegawcze, które powinny być usuwane. |
| Wymaga uwagi (krytyczne) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15"> | Istnieje jeden lub więcej alertów krytycznych w domenie zarządzanej. Mogą również mieć alerty informacyjne i/lub ostrzeżenia. |
| Wdrażanie | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15"> | Domeny jest w trakcie wdrażania. |

## <a name="monitors"></a>Monitory
Monitory są aspektów Twojej domeny zarządzanej, monitorującej przez usługi domenowe Azure AD w regularnych odstępach czasu. Najlepszym sposobem utrzymywania monitory w dobrej kondycji jest Rozwiąż wszystkie aktywne alerty dla domeny zarządzanej.

Usługi domenowe Azure AD obecnie monitoruje następujące czynności:
 - Backup
 - Synchronizacja z usługą Azure AD

### <a name="the-backup-monitor"></a>Monitor "Kopia zapasowa"
Monitoruje to, czy są wykonywane regularnie Twórz kopie zapasowe domeny zarządzanej. W poniższej tabeli opisano, czego można oczekiwać w kolumnie szczegóły kopii zapasowej monitora:

| Wartość szczegółu | Wyjaśnienie |
| --- | --- |
|"Nigdy nie wykonano kopii" | Ten stan jest normalne dla nowo utworzonego domeny zarządzanej. Ogólnie rzecz biorąc pierwsza kopia zapasowa jest tworzony 24 godziny po zaaprowizowaniu domeny zarządzanej. Jeśli Twoja domena zarządzana nie jest nowo utworzony lub wyświetlić ten stan nietypowe ilość czasu, [się z pomocą techniczną](contact-us.md). |
| Ostatnia kopia zapasowa została wykonana 1 do 14 dni temu | Ogólnie rzecz biorąc ta wartość oczekuje monitora kopii zapasowej. |
| Ostatnia kopia zapasowa została wykonana więcej niż 14 dni temu. | Ilekroć dłużej niż dwa tygodnie jest zbyt dużo czasu od momentu ostatniej kopii zapasowej. Aktywne alerty krytyczne może uniemożliwić tworzona kopia zapasowa na bieżąco z domeną zarządzaną. Po pierwsze, rozwiąż wszystkie aktywne alerty dla domeny zarządzanej, a następnie czy wciąż problem nadal występuje, [się z pomocą techniczną](contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitor "synchronizacji z usługą Azure AD"
Microsoft monitoruje, jak często Twoja domena zarządzana jest zsynchronizowany z usługą Azure Active Directory. Liczba obiektów (użytkowników i grup) i liczby zmian w katalogu usługi Azure AD, od momentu ostatniej synchronizacji może jednocześnie mieć wpływ na jak długo może trwać okres synchronizacji. Jeśli Twoja domena zarządzana ostatniej synchronizacji ponad trzy dni temu, [się z pomocą techniczną](contact-us.md).

## <a name="alerts"></a>Alerty
Alerty są generowane w przypadku problemów w Twojej domeny zarządzanej, które muszą zostać poprawione w kolejności dla usług domenowych Azure AD uruchomić. Każdy alert wyjaśnia problem i zawiera adres URL rozwiązania, zawierający określone kroki, aby rozwiązać ten problem. Aby wyświetlić wszystkie alerty i ich rozwiązania, odwiedź stronę [Rozwiązywanie problemów z alertami](troubleshoot-alerts.md) artykułu.

### <a name="alert-severity"></a>Ważność alertu
Alerty są podzielone na trzy różne poziomy ważności: krytyczne, ostrzegawcze i informacyjne.

 * **Alerty krytyczne** problemy, które poważnym stopniu wpływa na Twojej domeny zarządzanej. Te alerty powinny być niezwłocznie, jak firmy Microsoft nie może monitorować, zarządzanie, patch i synchronizacji z domeną zarządzaną. 
 * **Alerty ostrzegawcze** powiadamiają użytkownika o problemach, które mogą mieć wpływ na domenę zarządzaną w przyszłości. Te alerty oferuje rekomendacje dotyczące zabezpieczanie Twojej domeny zarządzanej.
 * **Alerty informacyjne** są powiadomienia, które nie są niekorzystnego wpływu na domenę. Alerty informacyjne zaprojektowano tak, aby utrzymać odpowiednią wiedzę na temat działania wykonywane w Twojej domenie i Azure AD Domain Services.

## <a name="next-steps"></a>Kolejne kroki
- [Rozwiąż alerty w domenie zarządzanej](troubleshoot-alerts.md)
- [Więcej informacji na temat usługi Azure AD Domain Services](overview.md)
- [Skontaktuj się z zespołem produktu](contact-us.md)
