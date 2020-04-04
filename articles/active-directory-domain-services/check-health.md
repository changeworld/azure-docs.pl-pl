---
title: Sprawdzanie kondycji usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak sprawdzić kondycję domeny zarządzanej usług domenowych usługi Azure Active Directory (Usługi Azure AD DS) i zrozumieć komunikaty o stanie za pomocą witryny Azure portal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655636"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Sprawdzanie kondycji domeny zarządzanej usług domenowych Usługi domenowe Active Directory platformy Azure

Usługi domenowe Active Directory (Azure AD DS) uruchamiają niektóre zadania w tle, aby domena zarządzana była w dobrej kondycji i była aktualna. Zadania te obejmują wykonywanie kopii zapasowych, stosowanie aktualizacji zabezpieczeń i synchronizowanie danych z usługi Azure AD. Jeśli występują problemy z domeną zarządzana usługą Azure AD DS, te zadania mogą nie zostać pomyślnie ukończone. Aby przejrzeć i rozwiązać wszelkie problemy, można sprawdzić stan kondycji domeny zarządzanej usługi Azure AD DS przy użyciu witryny Azure portal.

W tym artykule pokazano, jak wyświetlić stan kondycji usługi Azure AD DS i zrozumieć informacje lub alerty wyświetlane.

## <a name="view-the-health-status"></a>Wyświetlanie stanu zdrowia

Stan kondycji domeny zarządzanej usług Azure AD DS jest używany przy użyciu witryny Azure portal. Informacje o ostatnim czasie tworzenia kopii zapasowej i synchronizacji z usługą Azure AD można zobaczyć, wraz z alertami, które wskazują na problem ze zdrowiem domeny zarządzanej. Aby wyświetlić stan kondycji domeny zarządzanej usług Azure AD DS, wykonaj następujące kroki:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD**.
1. Wybierz domenę zarządzana usługą Azure AD DS, taką jak *aaddscontoso.com*.
1. Po lewej stronie okna zasobu usługi Azure AD DS wybierz pozycję **Zdrowie**. Poniższy przykładowy zrzut ekranu przedstawia zdrową domenę zarządzaną usługą Azure AD DS i stan ostatniej kopii zapasowej i synchronizacji usługi Azure AD:

    ![Omówienie strony kondycji w witrynie Azure Portal z stanem Usług domenowych Usługi domenowe Active Directory azure](./media/check-health/health-page.png)

*Ostatnio oceniony* sygnatura czasowa strony kondycji pokazuje, kiedy domena zarządzana usługą Azure AD DS została ostatnio sprawdzona. Kondycja domeny zarządzanej usług Azure AD DS jest oceniana co godzinę. Jeśli wprowadziliśmy jakiekolwiek zmiany w domenie zarządzanej usług Azure AD DS, poczekaj na następny cykl oceny, aby wyświetlić zaktualizowany stan kondycji.

Stan w prawym górnym rogu wskazuje ogólną kondycję domeny zarządzanej usług Azure AD DS. Współczynniki stanu wszystkich istniejących alertów w domenie. W poniższej tabeli przedstawiono dostępne wskaźniki stanu:

| Stan | Ikona | Wyjaśnienie |
| --- | :----: | --- |
| Działanie | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Domena zarządzana usługą Azure AD DS działa poprawnie i nie ma żadnych alertów krytycznych ani ostrzegawczych. Domena może mieć alerty informacyjne. |
| Wymaga uwagi (ostrzeżenie) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Nie ma żadnych alertów krytycznych w domenie zarządzanej usług Azure AD DS, ale istnieje jeden lub więcej alertów ostrzegawczych, które należy rozwiązać. |
| Wymaga uwagi (krytyczna) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Istnieje jeden lub więcej alertów krytycznych w domenie zarządzanej usługi Azure AD DS, które muszą zostać rozwiązane. Możesz również mieć ostrzeżenia i / lub alerty informacyjne. |
| Wdrażanie | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Wdrażana jest domena usług Ad DS platformy Azure. |

## <a name="understand-monitors-and-alerts"></a>Opis monitorów i alertów

Stan kondycji domeny zarządzanej usług Azure AD DS pokazuje dwa typy informacji - *monitory*i *alerty*. Monitory pokazują czas ukończenia podstawowych zadań w tle. Alerty zawierają informacje lub sugestie w celu poprawy stabilności domeny zarządzanej.

### <a name="monitors"></a>Monitory

Monitory to obszary domeny zarządzanej usług Azure AD DS, które są regularnie sprawdzane. Jeśli istnieją aktywne alerty dla domeny zarządzanej usługi Azure AD DS, może to spowodować, że jeden z monitorów zgłosić problem. Usługi domenowe usługi Azure AD mają obecnie monitory dla następujących obszarów:

* Backup
* Synchronizacja z usługą Azure AD

#### <a name="backup-monitor"></a>Monitor kopii zapasowych

Monitor kopii zapasowej sprawdza, czy automatyczne regularne kopie zapasowe domeny zarządzanej usług Azure AD DS zostały pomyślnie uruchomione. W poniższej tabeli opisano stan dostępnego monitora kopii zapasowych:

| Wartość szczegółów | Wyjaśnienie |
| --- | --- |
| Nigdy nie wykonasz kopii zapasowej | Ten stan jest normalny dla nowych domen zarządzanych usług Azure AD DS. Pierwsza kopia zapasowa powinna zostać utworzona 24 godziny po wdrożeniu domeny zarządzanej usług Azure AD DS. Jeśli ten stan będzie się powtarzał, [otwórz żądanie pomocy technicznej platformy Azure][azure-support]. |
| Ostatnia kopia zapasowa została podjęta 1 do 14 dni temu | Ten zakres czasu jest oczekiwanym stanem monitora kopii zapasowej. Automatyczne regularne kopie zapasowe powinny wystąpić w tym okresie. |
| Ostatnia kopia zapasowa została podjęta ponad 14 dni temu. | Czas dłuższy niż dwa tygodnie wskazuje, że występuje problem ze zautomatyzowanymi zwykłymi kopiami zapasowymi. Aktywne alerty krytyczne mogą uniemożliwić utworzenie kopii zapasowej domeny usług Ad DS. Rozwiązywanie wszelkich aktywnych alertów dotyczących domeny zarządzanej usług Azure AD DS. Jeśli monitor kopii zapasowej nie zaktualizuj stanu, aby zgłosić ostatnią kopię [zapasową, otwórz żądanie pomocy technicznej platformy Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronizacja z monitorem usługi Azure AD

Domena zarządzana usługą Azure AD DS regularnie synchronizuje się z usługą Azure Active Directory. Liczba użytkowników i obiektów grupy oraz liczba zmian wprowadzonych w katalogu usługi Azure AD od czasu ostatniej synchronizacji mają wpływ na czas synchronizacji. Jeśli domena zarządzana usługą Azure AD DS została ostatnio zsynchronizowana ponad trzy dni temu, sprawdź i rozwiąż wszystkie aktywne alerty. Jeśli monitor synchronizacji nie zaktualizuje stanu, aby wyświetlić ostatnią synchronizację po adresowaniu aktywnych alertów, [otwórz żądanie pomocy technicznej platformy Azure][azure-support].

### <a name="alerts"></a>Alerty

Alerty są generowane w przypadku problemów w domenie zarządzanej usług Azure AD DS, które należy rozwiązać, aby usługa działała poprawnie. Każdy alert wyjaśnia problem i podaje adres URL, który określa określone kroki, aby rozwiązać problem. Aby uzyskać więcej informacji na temat możliwych alertów i ich rozwiązań, zobacz [Rozwiązywanie problemów z alertami](troubleshoot-alerts.md).

Alerty o stanie kondycji są podzielone na następujące poziomy ważności:

 * **Alerty krytyczne** to problemy, które poważnie wpływają na domenę zarządza zarządzana usługą Azure AD DS. Te alerty należy natychmiast rozwiązać. Platforma Azure nie może monitorować, zarządzać, łatać i synchronizować domeny zarządzanej, dopóki problemy nie zostaną rozwiązane.
 * Alerty ostrzegawcze powiadamiają o **problemach,** które mogą mieć wpływ na operacje domeny zarządzanej usługi Azure AD DS, jeśli problem będzie się powtarzał. Te alerty oferują również zalecenia dotyczące zabezpieczania domeny zarządzanej.
 * **Alerty informacyjne** to powiadomienia, które nie mają negatywnego wpływu na domenę zarządzana usługą Azure AD DS. Alerty informacyjne zapewniają pewien wgląd w to, co dzieje się w domenie zarządzanej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat alertów wyświetlanych na stronie stanu kondycji, zobacz [Rozwiązywanie alertów w domenie zarządzanej][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
